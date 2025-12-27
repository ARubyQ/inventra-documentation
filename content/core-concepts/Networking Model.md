---
title: 'Networking Model'
breadcrumbs: false
description: 'Understand the replication system and client-server architecture'
weight: 4
---

- All item data (`FInventoryItemArray`) replicates using **Fast Array Replication**.
    
- Server is authoritative for:
    
    - Item IDs (`AInventraGameState::GetNextItemID`).
        
    - Validation (`ServerMoveItem`).
        
- Clients:
    
    - Can **predictively** move items (smooth UX).
        
    - Receive authoritative correction via replication.
        
