---
title: 'Typical Workflow'
breadcrumbs: false
description: 'Learn the standard workflow for setting up and using Inventra'
weight: 1
---

1. **Define assets**: Create `UItemDataAsset` for each item type, assign `ItemTag`.
    
2. **Attach inventory**: Add `UInventoryComponent` to characters, chests, etc.
    
3. **Spawning items**: On server, call `AddItem` → automatically replicated.
    
4. **Moving items**:
    
    - Client calls `TryMoveItemPredictive`.
        
    - Component validates placement (optionally overridden in BP).
        
    - Server RPC applies authoritative state.
        
5. **Searching assets**:
    
    - In BP, call `FindItemAssetsByTag` (sync) or `FindItemAssetsAsync` (async).
        
    - Assets cached in `AInventraGameState`.
        

---

## Example (Blueprint)

```blueprint
Event OnChestOpened
→ Get Component (InventoryComponent)
→ MoveItemInFreeSpace(Player.Inventory, ItemID, Chest.Inventory, {})
```

This attempts to place the item into the chest’s first available slot or container.

---
