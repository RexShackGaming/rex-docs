# Rex Hunting Wagon - Documentation

## Overview

**rex-huntingwagon** is a comprehensive hunting wagon system for RedM servers using the RSG Framework. This script allows players to purchase, manage, and use hunting wagons to store and transport hunted animals across the map.

**Version:** 2.1.5  
**Author:** RexShackGaming  
**Framework:** RSG-Core  
**Discord:** https://discord.gg/YUV7ebzkqs

---

## Features

### Core Features
- **Multiple Hunting Locations:** 6 pre-configured hunting stores across the map (Valentine, Annesburg, Saint Denis, Blackwater, MacFarlane, Tumbleweed)
- **Multi-Wagon Support:** Players can own multiple hunting wagons (configurable)
- **Animal Storage System:** Store up to 10 animals per wagon with visual tarp indication
- **Animal Freshness System:** Animals rot after 2 days (48 hours) with visual indicators
- **Wagon Inventory:** Each wagon has its own storage inventory (400,000 max weight, 20 slots)
- **Damage System:** Wagons can be damaged and require repairs
- **Auto-Save System:** Wagons automatically save location on disconnect
- **Discord Logging:** Track all wagon activities via Discord webhooks

### Advanced Features
- **Location-Based Storage:** Wagons are stored at specific hunting camps
- **Spawn Blocking:** Prevents wagon spawn if area is occupied
- **Animal Tracking:** Distinguishes between skinned and unskinned animals
- **Tarp Visualization:** Visual indicator of wagon fullness
- **Hunting Shop:** Built-in shop system for hunting supplies
- **Target Integration:** Uses ox_target for all interactions
- **Blip System:** Shows wagon location on map when spawned
- **Cleanup Systems:** Automatic cleanup of wagons on disconnect/resource stop

---

## Dependencies

### Required
- **rsg-core** - Core framework
- **ox_lib** - UI library and utilities
- **oxmysql** - Database operations
- **ox_target** - Targeting system
- **rsg-inventory** - Inventory management

---

## Installation

### 1. Database Setup
Run the SQL file to create required tables:

```sql
-- Creates rex_huntingwagon table (stores wagon ownership and status)
-- Creates rex_huntingwagon_inventory table (stores animals in wagons)
```

Execute: `installation/rex-huntingwagon.sql`

### 2. Resource Installation
1. Place `rex-huntingwagon` folder in your server's resources directory
2. Add to your `server.cfg`:
```cfg
ensure rex-huntingwagon
```

### 3. Configuration
Edit `shared/config.lua` to customize settings (see Configuration section below)

---

## Configuration

### Debug Settings
```lua
Config.Debug = false  -- Enable/disable debug mode
```

### Shop Settings
```lua
Config.HuntingShopItems = {
    { name = 'weapon_sniperrifle_rollingblock', amount = 10, price = 411 },
    { name = 'ammo_box_rifle', amount = 10, price = 10 },
    { name = 'weapon_melee_knife', amount = 10, price = 5 },
}
Config.PersistStock = true  -- Persist shop stock across restarts
```

### Wagon Settings
```lua
Config.WagonPrice = 1000              -- Purchase price
Config.WagonSellRate = 0.75           -- Sell rate (75% of purchase price)
Config.TotalAnimalsStored = 10        -- Max animals per wagon
Config.StorageMaxWeight = 400000      -- Max inventory weight
Config.StorageMaxSlots = 20           -- Inventory slots
Config.WagonFixPrice = 50             -- Repair cost
Config.TargetDistance = 5.0           -- Interaction distance
Config.StoreTime = 2000               -- Animal store duration (ms)
Config.WagonStoreTime = 2000          -- Wagon store duration (ms)
Config.AllowMultipleWagons = true     -- Allow multiple wagon ownership
Config.RequireProximityToStore = true -- Must be near wagon to store it
```

### NPC Settings
```lua
Config.DistanceSpawn = 20.0  -- NPC spawn distance
Config.FadeIn = true         -- Enable NPC fade-in effect
```

### Discord Webhook Settings
```lua
Config.EnableDiscordLogs = true
Config.DiscordWebhook = ''  -- Your webhook URL
Config.DiscordBotName = 'Hunting Wagon System'
Config.DiscordBotAvatar = 'https://i.imgur.com/YourImageHere.png'
Config.DiscordColor = 3447003  -- Embed color (blue)

Config.LogEvents = {
    WagonPurchase = true,
    WagonSale = true,
    WagonStore = true,
    WagonSpawn = true,
    AnimalStore = true,
    AnimalRetrieve = true,
    WagonRepair = true,
}
```

### Map Locations
Six hunting stores are pre-configured. Each location has:
- **name**: Display name
- **location**: Unique identifier
- **coords**: NPC/interaction coordinates
- **npcmodel**: NPC model hash
- **npccoords**: NPC spawn position (x, y, z, heading)
- **wagonspawn**: Wagon spawn coordinates (x, y, z, heading)
- **showblip**: Show on map (true/false)

Example:
```lua
{
    name = 'Valentine Hunting Store',
    location = 'valhuntingstore',
    coords = vec3(-263.86, 631.66, 113.45),
    npcmodel = `casp_hunting02_males_01`,
    npccoords = vec4(-263.86, 631.66, 113.45, 148.29),
    wagonspawn = vec4(-264.89, 624.28, 113.08, 160.35),
    showblip = true
}
```

---

## Animal System

### Supported Animals
The script supports **120+ animal types** including:
- Large game: Bears, Bison, Elk, Moose, Alligators
- Medium game: Deer, Boar, Cougars, Wolves, Panthers
- Small game: Rabbits, Squirrels, Raccoons
- Birds: Eagles, Hawks, Turkeys, Ducks
- Legendary animals: Bull Gator, Legendary Fox, Tatanka Bison

Each animal is tracked by:
- **modelhash**: Game model identifier
- **modelname**: Internal model name
- **modellabel**: Display name

### Animal Freshness System
- Animals stored for **less than 24 hours**: Fresh (green indicator)
- Animals stored for **24-42 hours**: Warning (yellow indicator)
- Animals stored for **42-48 hours**: Critical (red indicator)
- Animals stored for **over 48 hours**: Rotten (auto-deleted)

The cleanup system runs:
- Every hour (checks all wagons)
- On resource start (initial cleanup)

---

## Database Schema

### rex_huntingwagon
Stores wagon ownership and status:
```sql
id          INT(11)      AUTO_INCREMENT PRIMARY KEY
citizenid   VARCHAR(50)  Player identifier
plate       VARCHAR(255) Unique wagon plate
huntingcamp VARCHAR(50)  Current storage location
damaged     TINYINT(4)   Damage status (0=good, 1=damaged)
active      TINYINT(4)   Active status
```

### rex_huntingwagon_inventory
Stores animals in wagons:
```sql
id           INT(11)      AUTO_INCREMENT PRIMARY KEY
animalhash   INT(25)      Animal model hash
animallabel  VARCHAR(50)  Animal name
animallooted INT(11)      Skinned status (0=no, 1=yes)
citizenid    VARCHAR(50)  Player identifier
plate        VARCHAR(255) Wagon plate
stored_at    TIMESTAMP    Storage timestamp (for rot calculation)
```

---

## Client Events

### rex-huntingwagon:client:openhuntermenu
Opens main hunting store menu
```lua
TriggerEvent('rex-huntingwagon:client:openhuntermenu', location, wagonspawn)
```

### rex-huntingwagon:client:spawnwagon
Opens wagon selection menu
```lua
TriggerEvent('rex-huntingwagon:client:spawnwagon', { 
    huntingcamp = location, 
    spawncoords = wagonspawn 
})
```

### rex-huntingwagon:client:spawnSelectedWagon
Spawns selected wagon
```lua
TriggerEvent('rex-huntingwagon:client:spawnSelectedWagon', {
    wagon = wagonData,
    spawncoords = coords,
    huntingcamp = location
})
```

### rex-huntingwagon:client:storewagon
Stores currently spawned wagon
```lua
TriggerEvent('rex-huntingwagon:client:storewagon', {})
```

### rex-huntingwagon:client:openmenu
Opens wagon interaction menu
```lua
TriggerEvent('rex-huntingwagon:client:openmenu', wagonplate)
```

### rex-huntingwagon:client:addanimal
Stores animal player is holding
```lua
TriggerEvent('rex-huntingwagon:client:addanimal', { plate = wagonplate })
```

### rex-huntingwagon:client:getHuntingWagonStore
Opens stored animals menu
```lua
TriggerEvent('rex-huntingwagon:client:getHuntingWagonStore', { plate = wagonplate })
```

### rex-huntingwagon:client:getHuntingWagonInventory
Opens wagon inventory
```lua
TriggerEvent('rex-huntingwagon:client:getHuntingWagonInventory', { plate = wagonplate })
```

### rex-huntingwagon:client:sellwagoncheck
Confirmation dialog for selling wagon
```lua
TriggerEvent('rex-huntingwagon:client:sellwagoncheck', { plate = wagonplate })
```

### rex-huntingwagon:client:repairwagon
Repairs damaged wagon
```lua
TriggerEvent('rex-huntingwagon:client:repairwagon', { wagon = wagonData })
```

### rex-huntingwagon:client:takeoutanimal
Spawns animal on ground
```lua
TriggerEvent('rex-huntingwagon:client:takeoutanimal', animalhash, animallooted)
```

### rex-huntingwagon:client:cleanupDisconnectedWagon
Cleans up wagon when owner disconnects
```lua
TriggerEvent('rex-huntingwagon:client:cleanupDisconnectedWagon', playerId)
```

---

## Server Events

### rex-huntingwagon:server:buyhuntingcart
Purchase new hunting wagon
```lua
TriggerServerEvent('rex-huntingwagon:server:buyhuntingcart', { huntingcamp = location })
```

### rex-huntingwagon:server:sellhuntingcart
Sell hunting wagon
```lua
TriggerServerEvent('rex-huntingwagon:server:sellhuntingcart', plate)
```

### rex-huntingwagon:server:updatewagonstore
Update wagon storage location (good condition)
```lua
TriggerServerEvent('rex-huntingwagon:server:updatewagonstore', location, plate)
```

### rex-huntingwagon:server:damagedwagon
Store damaged wagon
```lua
TriggerServerEvent('rex-huntingwagon:server:damagedwagon', location, plate)
```

### rex-huntingwagon:server:fixhuntingwagon
Repair damaged wagon
```lua
TriggerServerEvent('rex-huntingwagon:server:fixhuntingwagon', plate, price)
```

### rex-huntingwagon:server:addanimal
Add animal to wagon storage
```lua
TriggerServerEvent('rex-huntingwagon:server:addanimal', animalhash, animallabel, animallooted, plate)
```

### rex-huntingwagon:server:removeanimal
Remove animal from wagon storage
```lua
TriggerServerEvent('rex-huntingwagon:server:removeanimal', {
    id = animalId,
    plate = wagonPlate,
    animallooted = status,
    animalhash = hash
})
```

### rex-huntingwagon:server:wagonstorage
Open wagon inventory
```lua
TriggerServerEvent('rex-huntingwagon:server:wagonstorage', plate)
```

### rex-huntingwagon:server:openShop
Open hunting shop
```lua
TriggerServerEvent('rex-huntingwagon:server:openShop')
```

### rex-huntingwagon:server:wagonSpawned
Track wagon spawn
```lua
TriggerServerEvent('rex-huntingwagon:server:wagonSpawned', plate)
```

### rex-huntingwagon:server:wagonDespawned
Track wagon despawn
```lua
TriggerServerEvent('rex-huntingwagon:server:wagonDespawned')
```

---

## Server Callbacks

### rex-huntingwagon:server:getwagons
Get all wagons owned by player
```lua
RSGCore.Functions.TriggerCallback('rex-huntingwagon:server:getwagons', function(wagons)
    -- Returns array of wagon data or nil
end)
```

### rex-huntingwagon:server:getwagonstore
Get animals stored in wagon
```lua
RSGCore.Functions.TriggerCallback('rex-huntingwagon:server:getwagonstore', function(animals)
    -- Returns array of animal data
end, plate)
```

### rex-huntingwagon:server:gettarpinfo
Get count of animals in wagon (for tarp visualization)
```lua
RSGCore.Functions.TriggerCallback('rex-huntingwagon:server:gettarpinfo', function(count)
    -- Returns integer count
end, plate)
```

---

## Key Functions

### Client-Side

#### DeleteThis(holding, modellabel)
Deletes animal entity player is holding
```lua
local success = DeleteThis(entityHandle, "White-Tail Deer")
-- Returns: true if deleted successfully, false otherwise
```

#### SetClosestStoreLocation()
Finds nearest hunting store to player
```lua
SetClosestStoreLocation()
-- Sets: closestWagonStore variable
```

#### NearPed(npcmodel, npccoords, location, wagonspawn)
Spawns NPC at hunting location
```lua
local ped = NearPed(modelHash, coords, 'valhuntingstore', spawnCoords)
-- Returns: spawned ped handle
```

### Server-Side

#### GeneratePlate()
Generates unique wagon plate
```lua
local plate = GeneratePlate()
-- Returns: unique 6-character plate (e.g., "ABC123")
```

#### CleanupRottedAnimals()
Removes animals stored over 2 days
```lua
CleanupRottedAnimals()
-- Deletes expired animals from database
```

---

## Localization

All text is localized via `locales/en.json`. Currently supports English with 92 locale strings.

### Adding New Languages
1. Copy `locales/en.json`
2. Rename to your language code (e.g., `fr.json`)
3. Translate all string values
4. Update `fxmanifest.lua` to include new locale file

---

## Discord Logging

When `Config.EnableDiscordLogs = true`, the following events are logged:

### Wagon Purchase
- Player name and ID
- Wagon plate
- Location of purchase
- Price paid

### Wagon Sale
- Player name and ID
- Wagon plate
- Sale price

### Wagon Store/Spawn
- Player name and ID
- Wagon plate
- Storage location
- Damage status

### Animal Store/Retrieve
- Player name and ID
- Wagon plate
- Animal name
- Current count / Max capacity

### Wagon Repair
- Player name and ID
- Wagon plate
- Repair cost

Configure webhooks in `server/discord.lua`

---

## Permissions & Security

### Ownership Verification
All server events verify wagon ownership before allowing actions:
- Purchase checks
- Sell checks
- Animal storage verification
- Repair authorization

### Anti-Exploit Measures
- Money removed before database updates
- Refund system if database operations fail
- Plate uniqueness enforcement
- Spawn area collision detection
- Network ownership tracking

---

## Performance Optimization

### Client-Side
- Distance-based NPC spawning/despawning
- Efficient wagon state monitoring (1-second intervals)
- Model cleanup after spawning
- Entity pooling for nearby checks

### Server-Side
- Prepared SQL statements for repeated queries
- Indexed database columns
- Async database operations
- Hourly cleanup instead of continuous

---

## Troubleshooting

### Wagon Won't Spawn
1. Check if spawn area is clear of vehicles/peds/objects
2. Verify wagon isn't already spawned
3. Check wagon isn't damaged (repair first)
4. Ensure you're at the correct storage location

### Animals Not Storing
1. Verify you're holding the animal (target interaction required)
2. Check wagon capacity (max 10 animals)
3. Ensure wagon exists and you own it
4. Confirm animal model is in Config.Animals table

### Wagon Disappeared
1. Check if it was damaged (auto-stored on destruction)
2. Verify you didn't disconnect (auto-stores on disconnect)
3. Check database for wagon location
4. Look at nearest hunting store

### Database Issues
1. Verify tables exist (run SQL installation)
2. Check oxmysql is running
3. Confirm database credentials in server.cfg
4. Review server console for MySQL errors

---

## Known Limitations

1. **Wagon Model:** Only uses `huntercart01` model
2. **Animal Limit:** 120 pre-configured animal types
3. **Storage Locations:** 6 fixed hunting stores
4. **Rot Timer:** 48-hour fixed duration (not configurable per-animal)
5. **Single Wagon Active:** Only one wagon can be spawned per player at a time

---

## Future Enhancement Ideas

- Custom wagon models
- Variable rot times per animal quality
- Dynamic pricing based on server economy
- Wagon upgrades (capacity, speed, durability)
- Animal trading system
- Mobile hunting camps
- Wagon convoys/groups
- Seasonal hunting events
- Achievement system

---

## Support

For issues, suggestions, or support:
- Discord: https://discord.gg/YUV7ebzkqs
- GitHub: [Check resource page]

---

## Credits

**Script Author:** RexShackGaming  
**Framework:** RSG-Core Team  
**Libraries:** ox_lib, oxmysql, ox_target  

---

## License

See `LICENSE.md` in the resource folder for licensing information.

---

## Changelog

### Version 2.1.5
- Current stable release
- Multiple wagon support
- Animal freshness system
- Auto-cleanup on disconnect
- Discord logging
- Spawn area collision detection
- Location-based wagon storage
