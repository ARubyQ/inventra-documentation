---
title: 'Assets'
breadcrumbs: false
description: 'Complete reference for UItemDataAsset configuration, including all available properties and settings'
weight: 1
---
### `UItemDataAsset`

Primary asset defining static item properties.

**Fields:**

- `ItemTag` — Unique identifier tag (also exported to AssetRegistry).
    
- `Containers` — Map of container index → `FContainerSettings` (grid dimensions, tag filters).
    
- `EquipSlots` — Map of slot index → `FEquipSlotSettings` (name, filters, secure flag).
    
- `BlockedSlotsOnEquip` — Prevents equipping in certain slots.
    
- `MaxStackSize` — Default `1`.
    
- `MaxDurability` — Default `-1` (disabled).
    

Overrides:

- `GetPrimaryAssetId()` — returns `("Item", ItemTag)`.
    
- `GetAssetRegistryTags()` — registers `ItemTag` for discovery.
    

**Supporting structs:**

- `EInventorySlotType` — `Equip` / `Container`.
    
- `FContainerSettings` — size + tag filter.
    
- `FEquipSlotSettings` — name + tag filter + secure flag.
    
- `FInventorySlotDefinition` — slot definition (Equip or Container).
    
### Asset Discovery

#### `FItemAssetFinder`

Static C++ class providing search:

- `FindByTagSync(Tag, OutAssets)` — synchronous (blocking).
    
- `FindByTagAsync(Tag, Callback)` — async load via `UAssetManager`.
    

#### `UItemAssetBlueprintLibrary`

BP-callable:

- `FindItemAssetsByTag(Tag)` — sync search, returns `UItemDataAsset*[]`.
    

#### `UAsync_FindItemAssetsByTag`

BP async action node:

- `FindItemAssetsAsync(Tag)` — begins async search.
    
- `Completed` delegate returns loaded `UItemDataAsset*[]`.
    