---
title: 'Inventory Component'
breadcrumbs: false
description: 'Understand how UInventoryComponent works and its events'
weight: 2
---
### `UInventoryComponent`

Attachable actor component, owns `FInventoryItemArray` replicated inventory.

#### Events

- `OnItemAdded(const FInventoryItem&)`
    
- `OnItemChanged(const FInventoryItem&)`
    
- `OnItemRemoved(const FInventoryItem&)`
    

Triggered by fast-array replication or local modifications.

#### Core API

- `AddItem(NewItem)` → int32 `ItemID`.
    
- `RemoveItemByID(ItemID, OutRemovedItems)`.
    
- `ChangeItemByID(ItemID, NewData)`.
    
- `GetItemByIndex(Index)`, `GetItemByID(ID)`.
    
- `GetAllItems(OutItems)`.
    

#### Asset Queries

- `GetCachedItemAsset(Tag)` — delegates to `AInventraGameState`.
    

#### Placement Validation

- `BP_CanItemBePlaced(Item, NewContainingID, NewContainerIdx)` — override in BP.
    
- `BP_CanItemBeMoved(ItemID, ToInv, ToItemID, ToContainerIdx, Slot, Rotation)` — override in BP.
    

#### Predictive Client API

- `TryMoveItemPredictive(From, ItemID, To, ToItemID, ToContainerIdx, Slot, Rotation)` — executes local move, then RPC.
    

#### RPC

- `ServerMoveItem(...)` — authoritative server execution of move.
    

#### Movement / Search

- `MoveItem(...)` — core movement.
    
- `FindFirstContainerForItem(Item, Candidates, OutPos, bOutWouldStack)`.
    
- `CanItemFitsIn(Item, ContainingID, ContainerIdx, bOutWouldStack)`.
    
- `CanItemFitsInFreeSpace(Item, Candidates, OutPos, bOutWouldStack)`.
    
- `MoveItemInFreeSpace(From, ItemID, To, CandidateIDs)` — helper.
    
- `TryMoveItemInItemPredictive(From, ItemID, To, ParentID)` — specialized helper.
    

#### Root Slots

- `RootSlots` — editable array of slot definitions (equip or container).
    
- `EquipSlots` — legacy array (deprecated).
    

---
