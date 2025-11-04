# REX Storage - RedM Storage System

## Overview
REX Storage is a comprehensive storage system for RedM servers using the RSG framework. It allows players to place portable storage boxes in the world that persist between sessions, with guest access management and admin controls.

## Version
**2.0.0**

## Features
- 🎯 **Placeable Storage Boxes** - Players can place storage boxes anywhere in the world
- 👥 **Guest System** - Share storage access with other players
- 🔒 **Ownership Validation** - Secure ownership verification for all operations
- 🎨 **Interactive Placement** - Rotate and position storage boxes before placing
- 📦 **Configurable Limits** - Set max storage boxes per player, weight, and slots
- 🌿 **Vegetation Clearing** - Optional vegetation removal around placed storage
- 🗺️ **Dynamic Blips** - Map markers for player storage locations
- 🛡️ **Admin Tools** - Admin commands to manage storage boxes
- 📊 **Discord Logging** - Comprehensive webhook logging for all storage actions
- 🚫 **Town Restrictions** - Optional restriction from placing storage in towns
- 🔄 **Progress Bars** - Visual feedback for placement and destruction

## Dependencies
- **Required:**
  - [rsg-core](https://github.com/Rexshack-RedM/rsg-core)
  - [ox_lib](https://github.com/overextended/ox_lib)
  - [oxmysql](https://github.com/overextended/oxmysql)
  - [ox_target](https://github.com/overextended/ox_target)
  - [rsg-inventory](https://github.com/Rexshack-RedM/rsg-inventory)

## Installation

### 1. Database Setup
Execute the SQL file to create the necessary database tables:
```sql
-- Located in: installation/rex-storage.sql
```

This creates two tables:
- `rex_storage` - Stores storage box data
- `rex_storage_guests` - Manages guest access permissions

### 2. Add Storage Box Item
Add the storage box item to your shared items configuration:

**Location:** `installation/shared_items.lua`

```lua
storage_box = {
    name = 'storage_box',
    label = 'Storage Box',
    weight = 5000,
    type = 'item',
    image = 'storage_box.png',
    unique = false,
    useable = true,
    shouldClose = true,
    combinable = nil,
    description = 'A portable storage container'
}
```

### 3. Add Item Image
Place the `storage_box.png` image from `installation/images/` into your inventory images folder:
```
rsg-inventory/html/images/storage_box.png
```

### 4. Install Resource
1. Place `rex-storage` folder in your resources directory
2. Add to your `server.cfg`:
```cfg
ensure rex-storage
```

## Configuration

### Basic Settings
Located in `shared/config.lua`:

```lua
Config.StorageMaxWeight  = 4000000  -- Maximum weight capacity (in grams)
Config.StorageMaxSlots   = 100      -- Number of inventory slots
Config.MaxStorageBoxes   = 4        -- Max storage boxes per player
Config.DestroyTime       = 10000    -- Time to destroy storage (milliseconds)
Config.StorageProp       = 's_re_rcboatbox01x' -- 3D model hash
Config.TownsNotAlowed    = false    -- Restrict placement in towns
Config.EnableVegModifier = true     -- Clear vegetation around storage
```

### Placement Controls
```lua
Config.ForwardDistance = 1.5 -- Distance in front of player
Config.PromptGroupName = 'Place Storage'
Config.PromptCancelName = 'Cancel'
Config.PromptPlaceName = 'Place'
Config.PromptRotateLeft = 'Rotate Left'
Config.PromptRotateRight = 'Rotate Right'
```

### Blip Configuration
```lua
Config.Blip = {
    blipName = 'Storage Box',
    blipSprite = 'blip_chest',
    blipScale = 0.2,
    blipColour = 'BLIP_MODIFIER_MP_COLOR_6'
}
```

### Discord Webhook Settings
```lua
Config.Discord = {
    enabled = true, -- Enable/disable webhooks
    webhook = '', -- Your Discord webhook URL
    botName = 'REX Storage Logs',
    botAvatar = 'https://i.imgur.com/your-image.png',
    
    -- Toggle specific log events
    logEvents = {
        storageCreate = true,   -- Log storage creation
        storageDestroy = true,  -- Log storage destruction
        storageAccess = true,   -- Log storage access
        guestAdd = true,        -- Log guest additions
        guestRemove = true,     -- Log guest removals
        adminDelete = true      -- Log admin deletions
    },
    
    -- Embed colors
    colors = {
        create = 3066993,    -- Green
        destroy = 15158332,  -- Red
        access = 3447003,    -- Blue
        guest = 10181046,    -- Purple
        admin = 15105570     -- Orange
    }
}
```

## Usage

### For Players

#### Placing a Storage Box
1. Obtain a `storage_box` item
2. Use the item from your inventory
3. Position the storage box using:
   - **Rotate Left** - Rotate counterclockwise
   - **Rotate Right** - Rotate clockwise
   - **Place** - Hold to confirm placement
   - **Cancel** - Hold to cancel
4. Wait for the placement progress bar to complete

#### Opening Storage
1. Approach your storage box
2. Target the storage box
3. Select "Open Storage" from the menu
4. Access your stored items

#### Managing Guests
1. Target your storage box
2. Select "Manage Guests"
3. Options:
   - **Add Guest** - Enter player ID to grant access
   - **Remove Guest** - Select from guest list to revoke access
   - **View Guest List** - See all players with access

#### Destroying Storage
⚠️ **Warning:** This permanently deletes all contents!

1. Target your storage box
2. Select "Destroy Storage"
3. Confirm destruction
4. Wait for dismantling progress bar
5. Storage box item will be returned to inventory

### For Administrators

#### Admin Delete Mode
Delete any storage box on the server:
```
/admindeletestorage
```
1. Enter admin delete mode
2. Target any storage box
3. Select "[ADMIN] Delete Storage"
4. Confirm deletion

**Actions logged to Discord:**
- Admin name and ID
- Storage owner citizen ID
- Storage ID
- Timestamp

### Legacy Commands
Kept for backwards compatibility:

#### Add Guest (Legacy)
```
/addstoraguest [player_id]
```
Stand near your storage and add a guest by player ID.

#### Remove Guest (Legacy)
```
/removestorguest [player_id]
```
Stand near your storage and remove a guest by player ID.

## Technical Details

### Database Structure

#### rex_storage Table
| Column | Type | Description |
|--------|------|-------------|
| id | int(11) | Auto-increment primary key |
| citizenid | varchar(50) | Owner's citizen ID |
| properties | text | JSON-encoded storage data |
| propid | varchar(100) | Unique storage identifier |
| proptype | varchar(50) | Storage type (playerstorage) |

#### rex_storage_guests Table
| Column | Type | Description |
|--------|------|-------------|
| id | int(11) | Auto-increment primary key |
| propid | varchar(255) | Associated storage ID |
| owner_citizenid | varchar(50) | Storage owner's citizen ID |
| guest_citizenid | varchar(50) | Guest player's citizen ID |
| created_at | timestamp | When access was granted |

### Storage ID Generation
Storage IDs are generated using a secure algorithm:
- 16 random alphanumeric characters
- Unix timestamp
- Random 5-digit number
- Format: `[random16]-[timestamp]-[random5]`

### Prop Spawning System
- **Load Distance:** 50 units
- **Update Interval:** 5 seconds
- **Nearby Prop Range:** 100 units
- **Spawn Optimization:** Only loads props within range
- **Vegetation Clearing:** 3.0 unit radius (if enabled)

### Access Control
The system validates access for all operations:

1. **Owner Access:**
   - Place storage
   - Destroy storage
   - Manage guests
   - Open storage

2. **Guest Access:**
   - Open storage (read/write)

3. **Admin Access:**
   - Delete any storage
   - Requires 'admin' permission

### Security Features
- Ownership validation on all destructive operations
- Secure unique ID generation
- Database-backed permission checks
- Guest access tracking
- Citizen ID-based authentication
- Admin permission verification

## Events

### Client Events
```lua
-- Place new storage prop
TriggerEvent('rex-storage:client:placeNewProp', proptype, propHash, position, heading)

-- Open storage menu
TriggerEvent('rex-storage:client:openStorageMenu', storageId, storageObj)

-- Manage guests
TriggerEvent('rex-storage:client:manageGuests', storageId, guestList)

-- Destroy storage
TriggerEvent('rex-storage:client:destroystorage', storageEntity, storageId)

-- Remove prop object
TriggerEvent('rex-storage:client:removePropObject', propId)

-- Update prop data
TriggerEvent('rex-storage:client:updatePropData', propData)

-- Admin delete mode
TriggerEvent('rex-storage:client:adminDeleteMode')
```

### Server Events
```lua
-- Add guest
TriggerServerEvent('rex-storage:server:addGuest', storageId, targetId)

-- Remove guest
TriggerServerEvent('rex-storage:server:removeGuest', storageId, guestCitizenId)

-- Open storage
TriggerServerEvent('rex-storage:server:openstorage', storageId)

-- Create new prop
TriggerServerEvent('rex-storage:server:newProp', proptype, location, heading, hash)

-- Destroy prop
TriggerServerEvent('rex-storage:server:destroyProp', propId)

-- Admin delete storage
TriggerServerEvent('rex-storage:server:adminDeleteStorage', storageId)
```

### Callbacks
```lua
-- Check if player is storage owner
RSGCore.Functions.TriggerCallback('rex-storage:server:isStorageOwner', function(isOwner, guestList)
    -- Returns: isOwner (boolean), guestList (table)
end, storageId)

-- Count player's storage props
RSGCore.Functions.TriggerCallback('rex-storage:server:countprop', function(count)
    -- Returns: count (number)
end, proptype)

-- Get all prop data
RSGCore.Functions.TriggerCallback('rex-storage:server:getallpropdata', function(result)
    -- Returns: result (table or nil)
end, propId)
```

## Discord Logging

All major actions are logged to Discord webhooks:

### Storage Created
- Player name and citizen ID
- Steam ID
- Storage ID
- Coordinates (X, Y, Z)

### Storage Destroyed
- Player name and citizen ID
- Steam ID
- Storage ID
- Coordinates (X, Y, Z)

### Storage Accessed
- Player name and citizen ID
- Steam ID
- Storage ID
- Access type (Owner/Guest)
- Storage owner (if guest access)

### Guest Added
- Owner name and citizen ID
- Guest name and citizen ID
- Storage ID

### Guest Removed
- Owner name and citizen ID
- Guest name and citizen ID
- Storage ID

### Admin Delete
- Admin name, citizen ID, and Steam ID
- Storage owner citizen ID
- Storage ID

## Localization

The script supports full localization. Language files are located in:
```
locales/en.json
```

### Creating New Translations
1. Copy `locales/en.json`
2. Rename to your language code (e.g., `es.json`, `fr.json`)
3. Translate all values (keep keys unchanged)
4. Add to `fxmanifest.lua`:
```lua
files {
    'locales/*.json'
}
```

## Performance Optimization

### Prop Loading
- Only spawns props within 50 units of players
- Updates every 5 seconds (configurable)
- Despawns props when players move away
- Nearby prop system reduces network traffic

### Database Queries
- Indexed on `citizenid` and `propid`
- Prepared statements for all queries
- Optimized guest list retrieval
- Async query execution

### Client-Side
- Distance checks before spawning
- Model streaming optimization
- Vegetation modifier caching
- Entity cleanup on resource stop

## Troubleshooting

### Storage boxes not appearing
1. Check database connection
2. Verify `rex_storage` table has entries
3. Check console for spawn errors
4. Ensure prop model exists: `s_re_rcboatbox01x`

### Cannot place storage
1. Verify you have `storage_box` item
2. Check you haven't reached `MaxStorageBoxes` limit
3. Ensure not too close to another storage (1.3 unit minimum)
4. Check `TownsNotAlowed` setting if in town

### Guest access not working
1. Verify `rex_storage_guests` table exists
2. Check guest is added in database
3. Confirm storage ID matches
4. Verify both players are online

### Discord logs not appearing
1. Check `Config.Discord.enabled = true`
2. Verify webhook URL is valid
3. Check specific `logEvents` settings
4. Test webhook with curl/Postman

## Support

For issues, suggestions, or contributions:
- Check existing issues on the repository
- Provide console logs and reproduction steps
- Include server framework version (RSG Core)
- Mention any modified configuration

## Credits

- **Framework:** RSG Core
- **Libraries:** ox_lib, oxmysql, ox_target
- **Developer:** Rexshack Studios

## License

Please check the repository for license information.

---

**Note:** This documentation is for REX Storage v2.0.0. Features and configurations may vary in different versions.
