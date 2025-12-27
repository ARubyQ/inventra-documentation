---
title: 'Inventra'
description: a modular Unreal Engine inventory framework designed for extensibility, multiplayer, and Blueprint accessibility. 
breadcrumbs: false
cascade:
  type: docs
---

Inventra is a modular Unreal Engine inventory framework designed for extensibility, multiplayer, and Blueprint accessibility.

## Features

- Network-replicated inventory data using **Fast Array Serializer**.
- Support for **containers/grids**, **equip slots**, and **nested items**.
- **Stacking and durability** mechanics.
- **Client-side prediction** for drag & drop.
- **Synchronous & asynchronous asset discovery** of `UItemDataAsset` via GameplayTags.
- **Blueprint libraries** for common queries.

## Documentation Sections

### [Getting Started](/getting-started/)

Start here if you're new to Inventra. This section covers:
- **Typical Workflow** - Learn the standard workflow for setting up and using Inventra in your project
- **Item Creation** - Step-by-step guide to creating your first items and configuring item data assets

### [Core Concepts](/core-concepts/)

Understand the fundamental architecture of Inventra:
- **Core Data Structures** - Learn about `FInventoryItem`, `FInventoryItemArray`, and other core data types
- **Inventory Component** - Understand how `UInventoryComponent` works and its events
- **Game State Integration** - Learn how Inventra integrates with Game State for asset caching
- **Networking Model** - Understand the replication system and client-server architecture

### [Blueprint Integration](/blueprint-integration/)

Learn how to use Inventra in Blueprint visual scripting:
- **Blueprint Integration Guide** - Comprehensive guide to wiring up Inventra in Blueprints, including UI grids, drag & drop, and equipment systems
- **Blueprint Utilities** - Reference for Blueprint-accessible functions and libraries

### [Assets](/assets/)

Learn about item data assets:
- **Assets** - Complete reference for `UItemDataAsset` configuration, including all available properties and settings

