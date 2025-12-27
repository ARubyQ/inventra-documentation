---
title: 'Game State Integration'
breadcrumbs: false
description: 'Learn how Inventra integrates with Game State for asset caching'
weight: 3
---
### `AInventraGameState`

Custom `AGameStateBase` subclass.  
Responsibilities:

- **ID generation**: `GetNextItemID()` — increments `CurrentItemID` on server; clients return `-1`.
    
- **Asset caching**: `GetCachedItemAsset(Tag)` — returns cached or loads via `FItemAssetFinder`.
    
- **Events**: `OnItemAssetLoaded(Tag, Asset)` (BlueprintImplementableEvent).
    

---
