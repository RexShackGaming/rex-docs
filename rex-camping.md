# REX Camping System
A comprehensive camping system for RedM servers that allows players to set up campsites with various features including cooking, crafting, storage, and more.

**Game:** RedM (RDR3)  
**Framework:** RSG Framework (ONLY)

<iframe width="560" height="315" src="https://www.youtube.com/embed/-EQriAihm7E" frameborder="0" allowfullscreen></iframe>

## 🌐 Links
- **Discord:** https://discord.gg/YUV7ebzkqs
- **YouTube:** https://www.youtube.com/@rexshack/videos
- **Tebex Store:** https://rexshackgaming.tebex.io/

## ✨ Features
- **Campsite Creation:** Place and manage personal campsites
- **Guest System:** Add other players to your campsite to share access
- **Cooking System:** Cook raw meat and fish at campfires
- **Crafting System:** Craft tools and items at camp crafting tables
- **Storage System:** Store items in your campsite with configurable weight/slot limits
- **Props Management:** Place tents, fires, hitching posts, torches, and craft tables
- **Blip System:** Visual markers for active campsites on the map
- **Automatic Cleanup:** Configurable cron job for campsite maintenance
- **Multi-language Support:** Localization system with English included

## 📋 Dependencies
Ensure these resources are installed and started **before** rex-camping:

- **rsg-core** (Required)
- **ox_lib** (Required)
- **ox_target** (Required)
- **oxmysql** (Required)

## 🔧 Installation

### 1. Download and Extract
1. Download the rex-camping resource
2. Extract to your server's `resources` folder

### 2. Database Setup

#### New Installation
Execute the SQL file in your database:
```sql
-- Run this query in your MySQL database
source path/to/rex-camping/installation/rex-camping.sql
```
Or manually run:
```sql
CREATE TABLE IF NOT EXISTS `rex_camping` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `campsiteid` varchar(50) DEFAULT NULL,
  `propid` varchar(50) DEFAULT NULL,
  `citizenid` varchar(50) DEFAULT NULL,
  `item` varchar(50) DEFAULT NULL,
  `propdata` text NOT NULL,
  `allowed_players` text DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

### 3. Add Items to RSG-Core
Add the following items to your `rsg-core/shared/items.lua` file:
```lua
-- Rex Camping Items
campflag    = { name = 'campflag',    label = 'Campsite Flag', weight = 5000, type = 'item', image = 'campflag.png',    unique = true,  useable = true,  shouldClose = true, description = 'Use to set up a campsite'},
lockpick    = { name = 'lockpick',    label = 'Lockpick',      weight = 100,  type = 'item', image = 'lockpick.png',    unique = true,  useable = true,  shouldClose = true, description = 'Tool for picking locks'},
raw_meat    = { name = 'raw_meat',    label = 'Raw Meat',      weight = 50,   type = 'item', image = 'raw_meat.png',    unique = false, useable = false, shouldClose = true, description = 'Fresh raw meat for cooking'},
raw_fish    = { name = 'raw_fish',    label = 'Raw Fish',      weight = 50,   type = 'item', image = 'raw_fish.png',    unique = false, useable = false, shouldClose = true, description = 'Fresh raw fish for cooking'},
cooked_meat = { name = 'cooked_meat', label = 'Cooked Meat',   weight = 50,   type = 'item', image = 'cooked_meat.png', unique = false, useable = true,  shouldClose = true, description = 'Delicious cooked meat'},
cooked_fish = { name = 'cooked_fish', label = 'Cooked Fish',   weight = 50,   type = 'item', image = 'cooked_fish.png', unique = false, useable = true,  shouldClose = true, description = 'Tasty cooked fish'},
coal        = { name = 'coal',        label = 'Coal',          weight = 100,  type = 'item', image = 'coal.png',        unique = false, useable = false, shouldClose = true, description = 'Fuel for fires and forges'},
steel_bar   = { name = 'steel_bar',   label = 'Steel Bar',     weight = 1000, type = 'item', image = 'steel_bar.png',   unique = false, useable = false, shouldClose = true, description = 'Strong steel for crafting'},
wood        = { name = 'wood',        label = 'Wood',          weight = 100,  type = 'item', image = 'wood.png',        unique = false, useable = false, shouldClose = true, description = 'Basic crafting material'},
pickaxe     = { name = 'pickaxe',     label = 'Pickaxe',       weight = 100,  type = 'item', image = 'pickaxe.png',     unique = false, useable = false, shouldClose = true, description = 'Tool for mining'},
```

### 4. Add Item Images
Copy the required item images to your `rsg-inventory/html/images/` folder:
- campflag.png
- lockpick.png
- raw_meat.png
- raw_fish.png
- cooked_meat.png
- cooked_fish.png
- coal.png
- steel_bar.png
- wood.png
- pickaxe.png

### 5. Server Configuration
Add to your `server.cfg` file:
```cfg
# Rex Camping Resource
ensure rex-camping
```

## 🎮 Commands & Usage

### Setting Up a Campsite
1. **Obtain a Campsite Flag:** Get a `campflag` item from an admin or shop
2. **Use the Flag:** Use the campflag item from your inventory
3. **Place the Campsite:** 
   - Use directional controls to position the campsite
   - Rotate with designated keys (Left/Right)
   - Confirm placement when satisfied
   - Cancel if needed

### Campsite Props
Once your campsite is established, you can place various props:

- **Tent** (Max: 1) - Provides shelter and storage access
- **Campfire** (Max: 1) - Used for cooking food
- **Crafting Table** (Max: 1) - Used for crafting items
- **Hitching Posts** (Max: 2) - For securing horses
- **Torches** (Max: 6) - Provides lighting around camp

### Cooking System
At the campfire, you can cook:
- **Raw Fish** → **Cooked Fish** (10 seconds)
- **Raw Meat** → **Cooked Meat** (10 seconds)

### Crafting System
At the crafting table, you can craft:
- **Pickaxe:** Requires 1x Coal + 1x Steel Bar + 2x Wood (30 seconds)

### Storage System
- Access through your tent
- Default: 2,000,000 weight limit, 20 slots
- Configurable public/private access
- Owners can grant access to specific players

### Guest System
Campsite owners can add other players as guests:
1. **Adding Guests:**
   - Interact with your campsite flag
   - Select "Manage Guests"
   - Choose "Add Player" and enter their server ID
   - The player receives a notification of access

2. **Guest Permissions:**
   - Can use campsite storage (if private mode enabled)
   - Can use cooking and crafting facilities
   - **Cannot** pack up the campsite (owner-only)

3. **Removing Guests:**
   - Return to "Manage Guests" menu
   - Click on the guest to remove
   - Confirm the removal

## ⚙️ Configuration

Edit `shared/config.lua` to customize:

### Basic Settings
```lua
Config.CampingZoneSize   = 50.0          -- Campsite zone radius
Config.StorageMaxWeight  = 2000000       -- Storage weight limit
Config.StorageMaxSlots   = 20            -- Storage slot limit
Config.CampingCronJob    = '0 * * * *'   -- Cleanup schedule (hourly)
```

### Usage Permissions
```lua
Config.StoragePublicUse  = false -- true = anyone can use | false = owner + guests only
Config.CraftingPublicUse = false -- true = anyone can use | false = owner + guests only
Config.CookingPublicUse  = false -- true = anyone can use | false = owner + guests only
```

**Note:** When set to `false`, only the campsite owner and approved guests can access these features.

### Prop Limits
```lua
Config.MaxCampsites  = 1  -- Max campsites per player
Config.MaxFire       = 1  -- Max campfires per campsite
Config.MaxTent       = 1  -- Max tents per campsite
Config.MaxHitchPost  = 2  -- Max hitching posts per campsite
Config.MaxTorch      = 6  -- Max torches per campsite
Config.MaxCraftTable = 1  -- Max craft tables per campsite
```

## 🔄 Automatic Cleanup
The system includes automatic campsite cleanup:
- Runs on configurable cron schedule (default: hourly)
- Removes abandoned or old campsites
- Configurable notifications for cleanup events

## 🌍 Localization
The resource supports multiple languages:
- Default: English (`locales/en.json`)
- Add additional language files as needed
- Configure in the fxmanifest.lua

## 🐛 Troubleshooting

### Common Issues
1. **Campsite won't place:** Check if dependencies are running and items are properly configured
2. **Props not appearing:** Verify prop names in config match your server's prop list
3. **Database errors:** Ensure SQL file was executed correctly
4. **Items not working:** Confirm items were added to rsg-core items.lua
5. **Guest system not working:** 
   - Verify the `allowed_players` column exists in your database
   - Run the migration SQL if upgrading from an older version
   - Check that `Config.StoragePublicUse = false` is set to enable guest-only access

### Support
- Join our Discord: https://discord.gg/YUV7ebzkqs
- Check our YouTube for tutorials: https://www.youtube.com/@rexshack/videos

## 💝 Support the Developer
**Purchase Premium Resources:** https://rexshackgaming.tebex.io/

Your support helps fund continued development and new features!

---

**Developed by RexShackGaming**  
*Making RedM servers better, one script at a time* 🤠
