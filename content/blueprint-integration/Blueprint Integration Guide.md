---
title: 'Blueprint Integration Guide'
breadcrumbs: false
description: 'Comprehensive guide to wiring up Inventra in Blueprints, including UI grids, drag & drop, and equipment systems'
weight: 1
---
This guide shows how to wire up Inventra in Blueprints: UI grids, drag & drop with client prediction, async asset discovery, equipment, containers, and common patterns.

---

## 1) Quick Start

### Prerequisites

- **AInventraGameState** set in your GameMode (handles item IDs + asset cache).
    
- Actors (characters, chests, etc.) have an **InventoryComponent**.
    
- Each item type has a **UItemDataAsset** with a unique `ItemTag`.
    

### Level check (BeginPlay)

```
Event BeginPlay
→ Get Game State
→ Cast to AInventraGameState
→ (Optional) Store as variable InventraGS
→ If Cast Failed → Print String "InventraGameState is missing!"
```

---

## 2) Subscribing to Inventory Events

In any Blueprint with a reference to an `InventoryComponent`:

```
On BeginPlay
→ Get InventoryComponent
   → Bind Event to OnItemAdded    (Custom Event: BP_OnItemAdded(FInventoryItem))
   → Bind Event to OnItemChanged  (Custom Event: BP_OnItemChanged(FInventoryItem))
   → Bind Event to OnItemRemoved  (Custom Event: BP_OnItemRemoved(FInventoryItem))
```

Use these events to refresh UI (rebuild grid, counts, durability bars, etc.).

---

## 3) Building a Grid UI (UMG)

### Suggested widgets

- `WBP_Inventory` (root panel)
    
    - `UniformGridPanel GridItems`
        
    - Optional: `Overlay` for drag preview
        
- `WBP_ItemTile` (one visual per item head cell)
    
    - Displays icon, count, durability, hover highlights
        

### Populate grid

```
WBP_Inventory::Rebuild
→ InventoryComponent.GetAllItems(OutItems)
→ ForEach (Item in OutItems)
    → UInventoryBlueprintLibrary::GetRotatedItemSize(Item) → SizeX, SizeY
    → CreateWidget WBP_ItemTile → SetData(Item)
    → GridItems.AddChildToUniformGrid
         Row = Item.SlotY
         Column = Item.SlotX
    → (Optional) Span across SizeX/SizeY via slot settings or Canvas scaling
```

> Tips
> 
> - Render **one** widget per item (at its top-left cell) and visually stretch it; do not spawn duplicates for occupied cells.
>     
> - Maintain a simple “occupied map” in UI for quick visual previews while dragging.
>     

---

## 4) Drag & Drop with Client Prediction

### Start drag

In `WBP_ItemTile`:

```
OnMouseButtonDown → Detect left button
→ DetectDragIfPressed
```

```
OnDragDetected
→ CreateDragDropOperation
→ Set Payload = (FromInventoryComp, ItemID)
→ Set DefaultDragVisual = ghost widget of WBP_ItemTile
```

### Hover target & placement preview

In grid slot widget / inventory root:

```
OnDragOver(Operation)
→ Resolve target inventory (this widget’s InventoryComponent)
→ Compute candidate Slot (mouse → grid coords)
→ Call InventoryComponent.BP_CanItemBeMoved(
       ItemID = Operation.ItemID,
       ToInv  = TargetInventory,
       ToItemID = -1 (root) or hovered container item ID,
       ToContainerIdx = container index (0 = root),
       Slot = candidate slot,
       Rotation = current rotation)
→ If allowed → show green preview; if stacking predicted → show stack indicator
→ Return true
```

### Drop (predictive move)

```
OnDrop(Operation)
→ TargetInventory.TryMoveItemPredictive(
       From = Operation.FromInventoryComp,
       ItemID = Operation.ItemID,
       To   = TargetInventory,
       ToItemID = container owner item ID or -1,
       ToContainerIdx = idx,
       Slot = final slot,
       Rotation = rotation)
→ Return true
```

The client updates immediately; the server validates via RPC and authoritative state replicates back.

### Rotate during drag

Bind a key (e.g., `R`) in the inventory UI:

```
OnKeyDown(R)
→ Update local rotation value
→ Re-run preview with BP_CanItemBeMoved
```

---

## 5) Moving Items via Buttons (no DnD)

Examples:

- **Move to first free slot (same inventory)**
    

```
OnClick "Auto Place"
→ InventoryComponent.MoveItemInFreeSpace(
     From = InventoryComponent,
     ItemID = SelectedItemID,
     To = InventoryComponent,
     CandidateIDs = {})
```

- **Move to container item**
    

```
OnClick "Put Into Backpack"
→ InventoryComponent.TryMoveItemInItemPredictive(
     From = Player.Inventory,
     ItemID = SelectedItemID,
     To = Player.Inventory,
     ParentID = BackpackItemID)
```

---

## 6) Validation Hooks (Blueprint-overrideable)

Use these to enforce game rules without touching C++:

- `BP_CanItemBePlaced(Item, NewContainingID, NewContainerIdx) → bool`  
    Gatekeeping when dropping into a container.
    
- `BP_CanItemBeMoved(ItemID, ToInv, ToItemID, ToContainerIdx, Slot, Rotation) → bool`  
    Final positioning check (equip restrictions, blocked slots, size checks, tag filters).
    

**Typical implementation:**

```
BP_CanItemBeMoved(...)
→ Get Item by ID (From or To inv)
→ Fetch its UItemDataAsset via GetCachedItemAsset(Item.ItemTag)
→ Check tag filters of target container/equip slot
→ Check blocked slots, secure flags, rotation compatibility
→ Return true/false
```

---

## 7) Asset Discovery (Sync & Async) in BP

### Synchronous (quick lookup)

```
UItemAssetBlueprintLibrary::FindItemAssetsByTag(Tag)
→ Returns Array<UItemDataAsset>
```

Use for editor tooling or small one-off loads (avoid spamming at runtime).

### Asynchronous (non-blocking)

```
UAsync_FindItemAssetsByTag::FindItemAssetsAsync(Tag)
→ Bind to Completed(Array<UItemDataAsset>)
→ On Completed: cache icons/names locally
```

### Cached access via GameState

```
Get Game State → Cast AInventraGameState
→ GetCachedItemAsset(Tag) → UItemDataAsset
```

---

## 8) Equipment & Root Slots

### Root Slots

`InventoryComponent.RootSlots` defines top-level slot definitions:

- **Equip** (named slots with filters, secure flag)
    
- **Container** (grid size + filters)
    

Render them in UI as tabs/sections:

- **Equipment panel**: show each equip slot (empty or holding an item)
    
- **Containers panel**: each container index gets its own grid
    

### Equipping flow (example)

```
OnDrop onto EquipSlotWidget(SlotIndex)
→ InventoryComponent.BP_CanItemBeMoved(
     ItemID,
     ToInv = OwnerInventory,
     ToItemID = -1,
     ToContainerIdx = SlotIndex,
     Slot = (0,0), Rotation=0)
→ If true → TryMoveItemPredictive(...)
```

---

## 9) Containers Inside Items (Nested Items)

Items can own container indices (defined in their `UItemDataAsset`):

- Show a **“Open”** button if the asset has container entries.
    
- When opened, rebuild a grid for that item’s container:
    

```
UInventoryBlueprintLibrary::GetAllItemsInItemContainerIndex(
    Items = InventoryComponent.Items,
    ContainingID = Item.ItemID,
    ContainerIdx = X,
    OutItems)
```

---

