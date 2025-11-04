# REX-FARMING - Complete Documentation

## Table of Contents
1. [Overview](#overview)
2. [Features](#features)
3. [Dependencies](#dependencies)
4. [Installation](#installation)
5. [Configuration](#configuration)
6. [Plant System](#plant-system)
7. [Items & Seeds](#items--seeds)
8. [Usage Guide](#usage-guide)
9. [Server Events](#server-events)
10. [Client Events](#client-events)
11. [Database Structure](#database-structure)
12. [Troubleshooting](#troubleshooting)

---

## Overview

**rex-farming** is a comprehensive farming system for RedM servers built on the RSG framework. It allows players to plant, grow, water, fertilize, and harvest various crops with a realistic growth and quality management system.

**Version:** 2.1.5  
**Framework:** RSG-Core  
**Game:** RedM (Red Dead Redemption 2)

---

## Features

### Core Features
- **8 Different Crops**: Corn, Sugarcane, Cotton, Carrot, Potato, Tomato, Wheat, and Weed
- **3-Stage Growth System**: Seedling → Growing → Fully Grown
- **Plant Health Management**:
  - Thirst system with water bucket
  - Hunger system with fertilizer
  - Quality degradation if not maintained
- **Weather Integration**: Plants gain thirst during rain/shower/drizzle
- **Farming Zones**: Configurable designated farming areas with blips
- **Plant Anywhere Mode**: Optional unrestricted planting
- **Town Restriction**: Prevents planting in town zones
- **Owner-Only Harvesting**: Optional restriction for plant ownership
- **Auto-Cleanup**: Dead plants are automatically removed after 3 days
- **Anti-Spam Protection**: Cooldown systems for harvesting, watering, and feeding
- **Water Bucket System**: Metadata-based usage tracking (5 uses per fill)
- **Fertilizer Collection**: Collect from animal droppings in the world
- **Law Enforcement Features**: Police can destroy illegal weed plants
- **Vegetation Modifier System**: Clear vegetation in farming zones

### Quality & Reward System
- **Quality Tiers**: Poor (0-25%), Fair (25-50%), Good (50-75%), Excellent (75-100%)
- **Dynamic Rewards**: Harvest amounts based on plant quality
- **Growth Boost**: Fertilizer accelerates plant growth

---

## Dependencies

Required dependencies (install before using rex-farming):

```lua
dependencies {
    'rsg-core',      -- RSG Core Framework
    'ox_lib',        -- Ox Library for UI and utilities
    'PolyZone',      -- Zone management
    'oxmysql',       -- MySQL database connector (server-side)
    'weathersync'    -- Weather synchronization (for thirst bonuses)
}
```

---

## Installation

### Step 1: Database Setup
Execute the SQL file to create the required table:

```sql
CREATE TABLE `rex_farming` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `citizenid` varchar(50) DEFAULT NULL,
  `properties` text NOT NULL,
  `plantid` varchar(50) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

### Step 2: Add Items to Shared Items
Add the following items to your `rsg-core/shared/items.lua`:

```lua
-- Seeds
cornseed      = { name = 'cornseed',      label = 'Corn Seed',          weight = 100, type = 'item',  image = 'cornseed.png',      unique = false, useable = true,  shouldClose = true, description = 'Seeds ready for planting' },
sugarcaneseed = { name = 'sugarcaneseed', label = 'Sugarcane Seed',     weight = 100, type = 'item',  image = 'sugarcaneseed.png', unique = false, useable = true,  shouldClose = true, description = 'Seeds ready for planting' },
cottonseed    = { name = 'cottonseed',    label = 'Cotton Seed',        weight = 100, type = 'item',  image = 'cottonseed.png',    unique = false, useable = true,  shouldClose = true, description = 'Seeds ready for planting' },
carrotseed    = { name = 'carrotseed',    label = 'Carrot Seed',        weight = 100, type = 'item',  image = 'carrotseed.png',    unique = false, useable = true,  shouldClose = true, description = 'Seeds ready for planting' },
potatoseed    = { name = 'potatoseed',    label = 'Potato Seed',        weight = 100, type = 'item',  image = 'potatoseed.png',    unique = false, useable = true,  shouldClose = true, description = 'Seeds ready for planting' },
tomatoseed    = { name = 'tomatoseed',    label = 'Tomato Seed',        weight = 100, type = 'item',  image = 'tomatoseed.png',    unique = false, useable = true,  shouldClose = true, description = 'Seeds ready for planting' },
wheatseed     = { name = 'wheatseed',     label = 'Wheat Seed',         weight = 100, type = 'item',  image = 'wheatseed.png',     unique = false, useable = true,  shouldClose = true, description = 'Seeds ready for planting' },
weedseed      = { name = 'weedseed',      label = 'Weed Seed',          weight = 100, type = 'item',  image = 'weedseed.png',      unique = false, useable = true,  shouldClose = true, description = 'Seeds ready for planting' },

-- Crops
corn          = { name = 'corn',          label = 'Corn',               weight = 100, type = 'item',  image = 'corn.png',          unique = false, useable = false, shouldClose = true, description = 'Product from farming' },
sugarcane     = { name = 'sugarcane',     label = 'Sugarcane',          weight = 100, type = 'item',  image = 'sugarcane.png',     unique = false, useable = false, shouldClose = true, description = 'Product from farming' },
cotton        = { name = 'cotton',        label = 'Cotton',             weight = 100, type = 'item',  image = 'cotton.png',        unique = false, useable = false, shouldClose = true, description = 'Product from farming' },
carrot        = { name = 'carrot',        label = 'Carrot',             weight = 100, type = 'item',  image = 'carrot.png',        unique = false, useable = false, shouldClose = true, description = 'Product from farming' },
potato        = { name = 'potato',        label = 'Potato',             weight = 100, type = 'item',  image = 'potato.png',        unique = false, useable = false, shouldClose = true, description = 'Product from farming' },
tomato        = { name = 'tomato',        label = 'Tomato',             weight = 100, type = 'item',  image = 'tomato.png',        unique = false, useable = false, shouldClose = true, description = 'Product from farming' },
wheat         = { name = 'wheat',         label = 'Wheat',              weight = 100, type = 'item',  image = 'wheat.png',         unique = false, useable = false, shouldClose = true, description = 'Product from farming' },
weed          = { name = 'weed',          label = 'Weed',               weight = 100, type = 'item',  image = 'weed.png',          unique = false, useable = false, shouldClose = true, description = 'Product from farming' },

-- Tools
water_bucket  = { name = 'water_bucket',  label = 'Water Bucket',       weight = 1000, type = 'item', image = 'water_bucket.png',  unique = true,  useable = true,  shouldClose = true, description = 'water for your animals' },
fertilizer    = { name = 'fertilizer',    label = 'Fertilizer',         weight = 100, type = 'item',  image = 'fertilizer.png',    unique = false, useable = false, shouldClose = true, description = 'Helps things grow' },
```

### Step 3: Copy Images
Copy all images from `installation/images/` to your inventory images folder (typically `rsg-inventory/html/images/`).

### Step 4: Install Resource
1. Place `rex-farming` folder in your server's resources directory
2. Add to your `server.cfg`:
```
ensure rex-farming
```
3. Restart your server

---

## Configuration

All configuration is done in `shared/config.lua`.

### Plant Seed Placement
```lua
Config.ForwardDistance   = 2.0  -- Distance in front of player to place plant
Config.PromptGroupName   = 'Plant Seedling'
Config.PromptCancelName  = 'Cancel'
Config.PromptPlaceName   = 'Plant'
Config.PromptRotateLeft  = 'Rotate Left'
Config.PromptRotateRight = 'Rotate Right'
```

### General Farming Settings
```lua
Config.GrowAnywhere      = true   -- Allow planting anywhere (false = only in designated zones)
Config.RestrictTownPlanting = true  -- Prevent planting in town zones
Config.DeadPlantTime     = 60 * 60 * 72  -- Time until plant dies (3 days in seconds)
Config.MaxPlantCount     = 100    -- Maximum plants per player
Config.OwnerHarvestOnly  = true   -- Only plant owner can harvest
Config.EnableServerNotify = false -- Enable server console notifications for debugging
```

### Plant Systems
```lua
-- Thirst System
Config.StartingThirst    = 30   -- Starting thirst percentage (0-100)
Config.ThirstIncrease    = 100  -- Thirst gained when watered
Config.ThirstDecrease    = 1    -- Thirst lost per growth cycle
Config.StartDegrade      = 25   -- Thirst level where quality starts degrading

-- Hunger System
Config.StartingHunger    = 30   -- Starting hunger percentage (0-100)
Config.HungerIncrease    = 100  -- Hunger gained when fertilized
Config.HungerDecrease    = 1    -- Hunger lost per growth cycle

-- Growth System
Config.GrowthIncrease    = 1    -- Base growth per cycle
Config.GrowthBoostMin    = 1    -- Min bonus growth with fertilizer
Config.GrowthBoostMax    = 3    -- Max bonus growth with fertilizer

-- Quality System
Config.QualityDegrade    = 5    -- Quality lost per cycle when thirst is low
```

### Weather Integration
```lua
Config.ThirstRaining = 5 -- Amount thirst increases if raining per cycle
Config.ThirstShower  = 3 -- Amount thirst increases if showering per cycle
Config.ThirstDrizzle = 2 -- Amount thirst increases if drizzling per cycle
```

### Cron Job Settings
```lua
Config.FarmingCronJob = '*/1 * * * *'  -- Growth cycle runs every 1 minute
```

### Farm Zones
Define custom farming zones with polygons:

```lua
Config.FarmingZone = { 
    [1] = {
        zones = { -- Polygon points
            vector2(2703.1979980469, -792.98846435547),
            vector2(2648.6962890625, -818.03649902344),
            -- Add more points...
        },
        name = "stdenis1",
        minZ = 42.205417633057,
        maxZ = 42.399078369141,
        fieldcoords = vector3(2669.50, -776.80, 42.40), -- Blip location
        showblip = true
    },
}
```

### Reward Configuration
Customize harvest rewards for each crop:

```lua
Config.FarmItems = {
    {
        planttype = 'corn',
        item = 'corn',
        label = 'Corn',
        poorRewardMin = 1,      -- Min harvest for poor quality
        poorRewardMax = 2,      -- Max harvest for poor quality
        goodRewardMin = 3,      -- Min harvest for good quality
        goodRewardMax = 4,      -- Max harvest for good quality
        exellentRewardMin = 5,  -- Min harvest for excellent quality
        exellentRewardMax = 6,  -- Max harvest for excellent quality
    },
    -- Configure other plants...
}
```

### Water Props
Define which props can be used to refill water buckets:

```lua
Config.WaterProps = {
    `p_wellpumpnbx01x`,
    `p_watertrough01x`,
    `p_watertroughsml01x`,
    -- Add more water props...
}
```

### Fertilizer Props
Define which props can be collected for fertilizer:

```lua
Config.FertilizerProps = {
    `p_horsepoop02x`,
    `p_horsepoop03x`,
    `p_poop01x`,
    `p_sheeppoop01`,
    -- Add more fertilizer props...
}
```

---

## Plant System

### Growth Stages
Plants progress through 3 visual stages:

1. **Stage 1 (0-24% growth)**: Seedling
2. **Stage 2 (25-99% growth)**: Growing
3. **Stage 3 (100% growth)**: Fully Grown (harvestable)

### Health Management

#### Thirst System
- Plants start with 30% thirst
- Thirst decreases by 1% per growth cycle (every minute)
- Water plants to restore 100% thirst
- When thirst drops below 25%, quality begins degrading
- Rain weather provides automatic thirst recovery

#### Hunger System
- Plants start with 30% hunger
- Hunger decreases by 1% per growth cycle
- Fertilize plants to restore 100% hunger
- Fertilized plants receive bonus growth (1-3% extra per cycle)

#### Quality System
- Plants start at 100% quality
- Quality degrades by 5% per cycle when thirst < 25%
- Quality affects harvest rewards:
  - **Excellent (75-100%)**: 5-6 items
  - **Good (50-74%)**: 3-4 items
  - **Fair (25-49%)**: 3-4 items
  - **Poor (0-24%)**: 1-2 items

### Death & Cleanup
Plants die and are automatically removed if:
- Quality reaches 0%
- Plant age exceeds 72 hours (configurable)

---

## Items & Seeds

### Seeds
All seeds are useable items that initiate the planting process:

| Seed | Plant | Growth Model 1 | Growth Model 2 | Growth Model 3 |
|------|-------|----------------|----------------|----------------|
| cornseed | corn | CRP_CORNSTALKS_CB_SIM | CRP_CORNSTALKS_CA_SIM | CRP_CORNSTALKS_AB_SIM |
| sugarcaneseed | sugarcane | CRP_SUGARCANE_AA_SIM | CRP_SUGARCANE_AB_SIM | CRP_SUGARCANE_AC_SIM |
| cottonseed | cotton | CRP_COTTON_BC_SIM | CRP_COTTON_BA_SIM | CRP_COTTON_BB_SIM |
| carrotseed | carrot | CRP_CARROTS_SAP_BA_SIM | CRP_CARROTS_SAP_BA_SIM | CRP_CARROTS_BA_SIM |
| potatoseed | potato | crp_potato_aa_sim | crp_potato_aa_sim | crp_potato_sap_aa_sim |
| tomatoseed | tomato | crp_tomatoes_aa_sim | crp_tomatoes_har_aa_sim | crp_tomatoes_sap_aa_sim |
| wheatseed | wheat | crp_wheat_dry_aa_sim | crp_wheat_sap_long_ab_sim | crp_wheat_stk_ab_sim |
| weedseed | weed | prop_weed_02 | prop_weed_02 | prop_weed_01 |

### Tools

#### Water Bucket
- **Item**: `water_bucket`
- **Weight**: 1000
- **Unique**: Yes (metadata tracking)
- **Uses**: 5 uses per bucket
- **Refill**: At water props (wells, troughs)
- **Function**: Water plants to restore thirst

#### Fertilizer
- **Item**: `fertilizer`
- **Weight**: 100
- **Collection**: From animal droppings in the world
- **Function**: Feed plants to boost growth and restore hunger

---

## Usage Guide

### For Players

#### Planting Seeds
1. Obtain seeds (purchase from shop, crafting, etc.)
2. Use the seed item from inventory
3. Position the plant preview using rotation controls
4. Confirm placement to plant the seed
5. Seed is consumed and plant appears in the world

#### Watering Plants
1. Obtain a water bucket
2. Fill bucket at a water source (well, trough)
3. Approach your plant and press **J** to open plant menu
4. Select "Water Plant" option
5. Bucket uses are consumed (5 uses per bucket)

#### Fertilizing Plants
1. Collect fertilizer from animal droppings in the world
2. Approach your plant and press **J**
3. Select "Feed Plant" option
4. Fertilizer is consumed, plant receives growth boost

#### Harvesting Plants
1. Wait until plant reaches 100% growth (Stage 3)
2. Approach plant and press **J**
3. Select "Harvest Plant" option
4. Receive crops based on plant quality
5. Plant is removed after harvest

#### Collecting Fertilizer
1. Find animal droppings around the map (horse poop, sheep poop, etc.)
2. Approach and interact with prompt
3. Wait for collection animation
4. Receive fertilizer item

#### Refilling Water Bucket
1. Have an empty water bucket (0 uses remaining)
2. Approach a water source prop
3. Interact to fill bucket (restores 5 uses)

### For Law Enforcement (LEO)
- LEO players can destroy illegal weed plants
- Approach weed plant and press **J**
- Select "Destroy Plant" option
- Plant is permanently removed

**Note**: LEO players cannot plant weed seeds (server-side restriction).

---

## Server Events

### Planting & Management
```lua
-- Plant a new seed
TriggerServerEvent('rex-farming:server:plantnewseed', outputitem, inputitem, prophash1, prophash2, prophash3, propcoords, propheading)

-- Save plant to database
TriggerEvent('rex-farming:server:savePlant', data, plantId, citizenid)

-- Update plant data
TriggerServerEvent('rex-farming:server:updateFarmPlants', id, data)
```

### Plant Actions
```lua
-- Water a plant
TriggerServerEvent('rex-farming:server:waterPlant', plantid)

-- Feed a plant
TriggerServerEvent('rex-farming:server:feedPlant', plantid)

-- Harvest a plant
TriggerServerEvent('rex-farming:server:harvestPlant', plantId)

-- Destroy plant (LEO)
TriggerServerEvent('rex-farming:server:destroyplant', data)

-- Destroy dead plant (automatic)
TriggerEvent('rex-farming:server:destroydeadplant', deadid)
```

### Items
```lua
-- Remove item from player
TriggerServerEvent('rex-farming:server:removeitem', item, amount)

-- Give item to player
TriggerServerEvent('rex-farming:server:giveitem', item, amount)
```

### Water & Fertilizer
```lua
-- Refresh water bucket
TriggerServerEvent('rex-farming:server:refreshwaterbucket')

-- Collected fertilizer
TriggerServerEvent('rex-farming:server:collectedfertilizer', coords)
```

### Data Sync
```lua
-- Get plants from database
TriggerEvent('rex-farming:server:getPlants')

-- Update plants to clients
TriggerEvent('rex-farming:server:updatePlants')

-- Remove plant
TriggerEvent('rex-farming:server:PlantRemoved', plantId)
```

---

## Client Events

### Plant Interactions
```lua
-- Open plant menu
TriggerEvent('rex-farming:client:plantmenu', plantid)

-- Water plant action
TriggerEvent('rex-farming:client:waterplant', data)

-- Feed plant action
TriggerEvent('rex-farming:client:feedplant', data)

-- Harvest plant action
TriggerEvent('rex-farming:client:harvestplant', data)

-- Destroy plant action
TriggerEvent('rex-farming:client:destroyplant', data)
```

### Planting System
```lua
-- Pre-plant seed (from item usage)
TriggerClientEvent('rex-farming:client:preplantseed', src, planttype, hash1, hash2, hash3, seeditem)

-- Plant new seed
TriggerEvent('rex-farming:client:plantnewseed', outputitem, inputitem, prophash1, prophash2, prophash3, propcoords, propheading)
```

### Data Updates
```lua
-- Update plant data (server sync)
TriggerClientEvent('rex-farming:client:updatePlantData', -1, Config.FarmPlants)

-- Remove plant object from world
TriggerClientEvent('rex-farming:client:removePlantObject', -1, plantId)
```

---

## Database Structure

### rex_farming Table
```sql
CREATE TABLE `rex_farming` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `citizenid` varchar(50) DEFAULT NULL,
  `properties` text NOT NULL,
  `plantid` varchar(50) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

### Properties JSON Structure
The `properties` field stores a JSON object with the following structure:

```json
{
    "id": "123456-1699999999-1234",
    "planttype": "corn",
    "x": 2669.50,
    "y": -776.80,
    "z": 42.40,
    "h": 180.0,
    "thirst": 30,
    "hunger": 30,
    "growth": 0.0,
    "quality": 100.0,
    "grace": true,
    "stage": 1,
    "hash1": "CRP_CORNSTALKS_CB_SIM",
    "hash2": "CRP_CORNSTALKS_CA_SIM",
    "hash3": "CRP_CORNSTALKS_AB_SIM",
    "planter": "ABC12345",
    "planttime": 1699999999
}
```

---

## Callbacks

### Server Callbacks
```lua
-- Get plant data by ID
RSGCore.Functions.CreateCallback('rex-farming:server:getplantdata', function(source, cb, plantid)
    -- Returns plant data or nil
end)

-- Check if fertilizer already collected
RSGCore.Functions.CreateCallback('rex-farming:server:checkcollectedfertilizer', function(source, cb, coords)
    -- Returns true/false
end)

-- Check if water bucket is empty
lib.callback.register('rex-farming:server:checkemptybucket', function(source)
    -- Returns true if bucket has 0 uses
end)

-- Check if water bucket has uses
lib.callback.register('rex-farming:server:checkbuckethasuses', function(source)
    -- Returns true if bucket has > 0 uses
end)
```

---

## Troubleshooting

### Plants Not Spawning
1. Check server console for errors on resource start
2. Verify database table exists and is accessible
3. Ensure `PolyZone` is installed and working
4. Check if `Config.FarmPlants` is being populated from database

### Growth Not Working
1. Verify cron job is running (check Config.FarmingCronJob)
2. Check if `weathersync` resource is running
3. Enable `Config.EnableServerNotify = true` to see growth cycle logs
4. Verify MySQL queries are executing without errors

### Cannot Plant Seeds
1. Check if `Config.GrowAnywhere = true` or player is in farming zone
2. Verify `Config.RestrictTownPlanting` setting if in town
3. Ensure player hasn't exceeded `Config.MaxPlantCount`
4. Check if trying to plant too close to another plant (< 1.3 units)

### Water Bucket Not Working
1. Ensure bucket has uses remaining (check metadata)
2. Verify player has `water_bucket` item
3. Check if on cooldown (2 second cooldown between waters)
4. Make sure plant exists in database

### Fertilizer Collection Issues
1. Check if prop is in `Config.FertilizerProps` list
2. Verify fertilizer hasn't already been collected
3. Ensure player is close enough to prop
4. Check if player is already collecting (anti-spam)

### Harvest Not Working
1. Verify plant is at 100% growth (Stage 3)
2. Check if `Config.OwnerHarvestOnly = true` and player owns the plant
3. Ensure player is not on cooldown (3 second cooldown)
4. Verify plant data exists in database

### Performance Issues
1. Reduce `Config.MaxPlantCount` if too many plants
2. Increase spawn distance check from 50.0 to lower value
3. Disable `Config.EnableServerNotify` in production
4. Consider increasing cron job interval for larger servers

---

## Advanced Configuration

### Adding Custom Crops
To add a new crop:

1. Add seed useable item in `server/server.lua`:
```lua
RSGCore.Functions.CreateUseableItem('myseed', function(source)
    local src = source
    TriggerClientEvent('rex-farming:client:preplantseed', src, 'mycrop', 'model_stage1', 'model_stage2', 'model_stage3', 'myseed')
end)
```

2. Add to `Config.FarmItems` in `shared/config.lua`:
```lua
{
    planttype = 'mycrop',
    item = 'mycrop',
    label = 'My Crop',
    poorRewardMin = 1,
    poorRewardMax = 2,
    goodRewardMin = 3,
    goodRewardMax = 4,
    exellentRewardMin = 5,
    exellentRewardMax = 6,
},
```

3. Add items to shared items list
4. Add images to inventory

### Adjusting Growth Speed
To make plants grow faster/slower:

```lua
-- In shared/config.lua
Config.GrowthIncrease = 2  -- Base growth doubled (was 1)
Config.FarmingCronJob = '*/30 * * * *'  -- Run every 30 minutes instead of 1 minute
```

### Changing Plant Lifespans
```lua
Config.DeadPlantTime = 60 * 60 * 24  -- 1 day instead of 3 days
```

---

## Support & Credits

**Script**: rex-farming  
**Version**: 2.1.5  
**Framework**: RSG-Core for RedM  

For support, issues, or feature requests, please contact the script developer or check the resource documentation.

---

## License

Please refer to `LICENSE.md` for licensing information.
