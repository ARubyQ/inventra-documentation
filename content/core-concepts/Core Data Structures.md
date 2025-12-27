---
title: 'Core Data Structures'
breadcrumbs: false
description: 'Learn about FInventoryItem, FInventoryItemArray, and other core data types'
weight: 1
---
### `FInventoryItem`

Represents a single item instance inside an inventory. Inherits from `FFastArraySerializerItem` for efficient replication.

**Fields (Blueprint accessible):**

- `ItemID` — Unique runtime ID, assigned by server. `-1` = invalid.
    
- `ContainingItemID` — ID of the parent item (if stored inside a container).
    
- `ItemTag` — GameplayTag identifying the item type (links to `UItemDataAsset`).
    
- `Count` — Stack count (default `1`).
    
- `SizeX`, `SizeY` — Item dimensions in grid cells.
    
- `SlotX`, `SlotY` — Position inside container grid.
    
- `Rotation` — Grid rotation (0–3).
    
- `ContainerIndex` — Index of the container (root or item-specific).
    

Implements `operator==` (compares IDs) and `GetTypeHash`.

---

### `FInventoryItemArray`

Container struct for all items in a component. Inherits from `FFastArraySerializer`.

- `TArray<FInventoryItem> Items` — replicated array.
    
- `UInventoryComponent* OwnerComponent` — back-reference.
    

Implements:

- `AddItem`, `RemoveItem` (mark dirty for replication).
    
- Replication hooks: `PostReplicatedAdd`, `PostReplicatedChange`, `PreReplicatedRemove`.
    
