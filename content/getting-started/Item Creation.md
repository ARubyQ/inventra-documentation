---
title: 'Item Creation'
breadcrumbs: false
description: 'Step-by-step guide to creating items and configuring item data assets'
weight: 2
---
Select a folder for your items. This folder must be assigned in **Project Settings** > **Game** > **Asset Manager**
![Project Settings - Asset Manager](/images/Pasted-image-20251209175821.png)
You must also add your items folder to the **Additional Asset Directories to Cook** list. Navigate to **Project Settings** > **Project** > **Packaging**, find the **Additional Asset Directories to Cook** array, and add your folder there. This ensures the items are included in the final packaged build.
![Additional Asset Directories to Cook](/images/Pasted-image-20251209180703.png)

**To create a new item:**

1. Right-click in the desired folder.
    
2. Navigate to **Miscellaneous** > **Data Asset**.
    
3. Search for and select the `ColdItemInfo` class.
![ColdItemInfo class selection](/images/Pasted-image-20251209175914.png)

You can edit the `ColdItemInfo` class to add your own custom parameters.
### **1. General Item Settings**

_Settings related to the item's identity and UI appearance._

- **Item Name:** The display name of the item as seen by the player (e.g., "AmmoV1").
    
- **Inventory Widget:** (Optional) A specific Widget Class used to represent this item in the inventory grid. Usually left as `None` if you are using a standard universal tile.
    
- **Item Icon:** The 2D Texture/Image displayed in the inventory grid.
    
- **Background Color (R,G,B,A):** The background color of the inventory tile behind the icon.
    
- **Border Color (R,G,B,A):** The color of the outline around the item in the inventory.
    
- **Use Rarity Color:** If enabled (set to 1), the system will override the manual colors above and use a global color associated with the item's rarity tier.
    
- **Item Long Name:** A detailed or formal name, often used in tooltips or inspection windows.
    
- **Item Actor:** The specific Actor Class (Blueprint) that is spawned into the world when this item is dropped from the inventory.
    
- **Context Options:** A list of actions available when right-clicking the item (e.g., "Drop", "Inspect", "Split").
    
    - _Example:_ `UI_DropContext` handles the logic for removing the item from the grid and spawning the actor.
        
- **Description:** Fluff text or lore describing the item to the player.
    
- **Mass:** The weight of the individual item (used for inventory weight limit calculations).
    
- **Hotbar Context Option:** The specific action triggered when this item is placed in a Hotbar slot and the corresponding key is pressed (e.g., "Use" or "Consume").
    
- **Buffs Duration:** (Map) Defines the duration of specific status effects applied by this item.
    
- **Ammo:** Boolean flag. Check this if the item functions as ammunition.
    

---

### **2. Mesh & World Representation**

_Settings regarding how the item looks in the 3D world._

- **Magazine:** Boolean flag. Check this if the item is a weapon magazine attachment.
    
- **Dropped Item Scale:** The scale multiplier applied to the actor when it is dropped in the world (1.0 is default size).
    
- **Item Static Mesh:** The 3D model used for the item if it does not require animation.
    
- **Item Skeletal Mesh:** The 3D model used if the item requires animation.
    
- **Mesh Transform (Location/Rotation/Scale):** Offset settings to adjust the mesh position relative to the actor's pivot point. useful for centering the mesh without opening 3D modeling software.
    

---

### **3. Stats (Armor, Health, Survival)**

_Gameplay values for consumables and equipment._

- **Armor Class:** The defense value provided if this item is worn.
    
- **Healing Effectivity:** Amount of health restored when consumed.
    
- **Hydration Effectivity:** Amount of thirst/hydration restored when consumed.
    
- **Energy Effectivity:** Amount of stamina/energy restored when consumed.
    

---

### **4. Procedural Icons**

_Settings for generating icons at runtime (if not using static textures)._

- **Procedural Icon Texture:** A base texture used for rendering the item in 3D capture mode.
    
- **Icon Width/Height:** The resolution of the generated icon (e.g., 256x256).
    
- **Force Use Procedural Icon:** If checked, ignores the static "Item Icon" and generates one based on the mesh.
    

---

### **5. Equipment & Audio**

_Logic for equipping items and sound feedback._

- **Is Actor Item:** Check this if the item is "Equipment" (like a Rifle or Backpack) that physically attaches to the character, rather than just being a consumable or resource.
    
- **Transfer Cue:** The Sound Cue played when moving this item between inventory slots.
    
- **Equip Cue:** The Sound Cue played when equipping this item.
    

---

### **6. Logic & Stacking**

_Core inventory mechanics._

- **Item Tag:** A **Gameplay Tag** (e.g., `items.ammo.ak.v1`). This is crucial for compatibility logic. For example, a weapon will check this tag to see if this specific ammo fits inside it.
    
- **Containers:** Defines grid space _inside_ this item. Used if the item is a backpack, chest, or vest that can hold other items.
    
- **Max Stack Size:** The maximum number of these items that can be piled into a single inventory slot (e.g., 60 bullets per slot).
    
- **Max Durability:** The health of the item.
    
    - _Note:_ A value of `-1` usually indicates the item is **indestructible** or does not use durability logic.
        

## **Setting up a Custom Inventory Widget:**

If your item requires a specific **Inventory Widget** (e.g., for backpacks or vests):

1. Create a new **User Widget Blueprint** inheriting from `WB_Item_Inventory`.
    
2. Open the widget and add the required number of `WB_Container` widgets to the hierarchy.
    
3. Select each container and configure the **Container Index** and **Parent Item Tag** properties in the Details panel.
    
4. Go to the Graph view and **override** the `GetContainers` function.
    
5. Inside the function, add a reference to all your `WB_Container` widgets to the return array.

