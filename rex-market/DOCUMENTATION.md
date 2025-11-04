# Rex-Market Documentation

## Overview
**rex-market** is a RedM script that allows players to place and operate portable market stalls in the game world. Players can sell items to other players through these stalls, manage inventory, collect money, and maintain their markets. Built on the RSG-Core framework.

**Version:** 2.1.3  
**Framework:** RSG-Core (RedM)  
**Dependencies:** rsg-core, ox_lib, oxmysql

---

## Table of Contents
1. [Features](#features)
2. [Installation](#installation)
3. [Configuration](#configuration)
4. [Database Schema](#database-schema)
5. [Usage Guide](#usage-guide)
6. [System Features](#system-features)
7. [Client-Side Functions](#client-side-functions)
8. [Server-Side Functions](#server-side-functions)
9. [Events](#events)
10. [Callbacks](#callbacks)
11. [Localization](#localization)
12. [Troubleshooting](#troubleshooting)

---

## Features

### Core Features
- **Portable Market Stalls**: Place markets anywhere in the world (with restrictions)
- **Player-to-Player Trading**: Sell items to other players asynchronously
- **Stock Management**: Add, update, and remove items from your market
- **Money System**: Collect profits from sales and withdraw funds
- **Quality System**: Markets degrade over time and require maintenance
- **Item Blacklist**: Prevent specific items from being sold (e.g., weapons)
- **Quality Restrictions**: Only sell items meeting minimum quality standards
- **Automatic Cleanup**: Cron jobs handle stock cleanup and market upkeep
- **Vegetation Clearing**: Optional vegetation removal around market stalls
- **Town Restrictions**: Prevent market placement in towns (optional)
- **Blip System**: Markets appear on the map for customers

### Advanced Features
- **Dynamic Prop Spawning**: Markets spawn/despawn based on player proximity
- **Interactive Placement**: Rotate and position markets with visual feedback
- **Transaction Safety**: Rollback mechanisms for failed operations
- **Performance Optimization**: Efficient rendering and database queries
- **Multi-Market Support**: Players can own multiple market stalls

---

## Installation

### 1. Database Setup
Run the SQL file to create required tables:

```sql path=null start=null
-- Creates rex_market and rex_market_stock tables
-- Located at: installation/rex-market.sql
```

Execute:
```bash path=null start=null
mysql -u yourusername -p yourdatabase < installation/rex-market.sql
```

### 2. Add Item to Shared Items
Add the market stall item to your `rsg-core/shared/items.lua`:

```lua path=null start=null
marketstall = {
    name = 'marketstall',
    label = 'Market Stall',
    weight = 1000,
    type = 'item',
    image = 'marketstall.png',
    unique = true,
    useable = true,
    shouldClose = true,
    description = 'A portable market stall for selling goods'
}
```

### 3. Add Image
Place `marketstall.png` from `installation/images/` into your inventory images folder:
- Default: `rsg-inventory/html/images/`

### 4. Resource Configuration
Add to your `server.cfg`:

```bash path=null start=null
ensure rex-market
```

### 5. Verify Dependencies
Ensure these resources start before rex-market:
- `rsg-core`
- `ox_lib`
- `oxmysql`

---

## Configuration

Configuration is located in `shared/config.lua`:

### Basic Settings

```lua path=null start=null
Config.EnableVegModifier = true      -- Clear vegetation around markets
Config.MaxMarkets = 1                -- Max markets per player
Config.MarketProp = `mp005_s_posse_tent_trader07x`  -- Market prop model
Config.PackupTime = 10000            -- Time to pack up (ms)
Config.PlaceMinDistance = 4          -- Min distance between markets
Config.RestrictTowns = true          -- Block placement in towns
Config.Money = 'cash'                -- Currency type: 'cash' or 'bloodmoney'
Config.RepairCost = 1                -- Cost per quality point to repair
Config.EnableServerNotify = false    -- Server console notifications
```

### Cron Job Settings

```lua path=null start=null
Config.UpkeepCronJob = '0 * * * *'      -- Every hour (quality degradation)
Config.StockCronJob = '*/5 * * * *'     -- Every 5 minutes (stock cleanup)
```

**Cron Format:** `minute hour day month dayofweek`
- Quality degrades by 1 point per upkeep cycle
- Markets at 0% quality are automatically removed
- Empty stock items are automatically deleted

### Blacklist System

```lua path=null start=null
Config.EnableBlacklist = true
Config.BlacklistItems = {
    'weapon_revolver_cattleman',
    'weapon_rifle_springfield',
    'marketstall',  -- Prevent selling the stall itself
    -- Add more items here
}
```

### Quality Restrictions

```lua path=null start=null
Config.EnableQualityCheck = true   -- Enforce quality checks
Config.MinimumQuality = 100        -- Only accept perfect items (0-100)
```

### Blip Configuration

```lua path=null start=null
Config.Blip = {
    blipName = 'Market Stall',
    blipSprite = 'blip_shop_market_stall',
    blipScale = 0.2,
    blipColour = 'BLIP_MODIFIER_MP_COLOR_6'
}
```

### Placement Prompts

```lua path=null start=null
Config.PromptGroupName = 'Place Market'
Config.PromptCancelName = 'Cancel'
Config.PromptPlaceName = 'Set'
Config.PromptRotateLeft = 'Rotate Left'
Config.PromptRotateRight = 'Rotate Right'
Config.PlaceDistance = 5.0
```

---

## Database Schema

### `rex_market` Table
Stores market stall information:

| Column | Type | Description |
|--------|------|-------------|
| `id` | int(11) | Auto-increment primary key |
| `marketid` | varchar(50) | Unique market identifier |
| `citizenid` | varchar(50) | Owner's citizen ID |
| `owner` | varchar(100) | Owner's full name |
| `properties` | text | JSON-encoded market data |
| `item` | varchar(50) | Item type (marketstall) |
| `quality` | int(3) | Market condition (0-100) |
| `money` | double(11,2) | Accumulated sales money |

**Indexes:**
- `unique_marketid` - Ensures unique market IDs
- `idx_citizenid` - Fast owner lookups
- `idx_item` - Item type queries
- `idx_quality` - Quality-based queries

### `rex_market_stock` Table
Stores items for sale in markets:

| Column | Type | Description |
|--------|------|-------------|
| `id` | int(11) | Auto-increment primary key |
| `marketid` | varchar(50) | Foreign key to rex_market |
| `item` | varchar(50) | Item name |
| `stock` | int(11) | Available quantity |
| `price` | double(11,2) | Price per item |

**Indexes:**
- `idx_marketid` - Fast market lookups
- `idx_item` - Item searches
- `idx_marketid_item` - Combined queries

**Foreign Key:** Cascade delete when market is removed

---

## Usage Guide

### For Market Owners

#### 1. Placing a Market
1. Obtain a `marketstall` item
2. Use the item from your inventory
3. Position the market with camera view
4. Rotate using Left/Right arrow keys
5. Hold `Set` to confirm placement
6. Hold `Cancel` to abort

**Restrictions:**
- Cannot place in towns (if enabled)
- Must maintain minimum distance from other markets
- Limited to `MaxMarkets` per player

#### 2. Owner Menu Options
Access by interacting with your own market:

**View Market Items**
- See all items currently for sale
- View stock levels and prices

**Add/Update Stock Item**
- Select item from inventory
- Set quantity to sell
- Set price per item
- Items must meet quality requirements
- Cannot sell blacklisted items

**Remove Stock Item**
- Returns all stock to your inventory
- Removes item from market

**Withdraw Money**
- Collect profits from sales
- Enter amount to withdraw (up to available balance)

**Market Maintenance**
- View current condition (%)
- Repair market to 100% quality
- Repair cost = (100 - quality) × RepairCost

**Packup Market Stall**
- Returns market item to inventory
- Must be at 100% quality to pack up
- All stock and money must be removed first

#### 3. Market Maintenance
Markets degrade over time based on `UpkeepCronJob`:
- Quality decreases by 1% per upkeep cycle
- At 0% quality, market is automatically destroyed
- Repair regularly to prevent loss

**Condition Indicators:**
- 🟢 Green: 51-100% (Good condition)
- 🟡 Yellow: 11-50% (Needs maintenance)
- 🔴 Red: 0-10% (Critical - repair immediately!)

### For Customers

#### Shopping at Markets
1. Locate market stalls via map blips
2. Approach and interact with the market
3. Browse available items
4. Select item and enter purchase quantity
5. Confirm transaction
6. Money is deducted, item added to inventory

**Customer View:**
- See item name, price, and stock
- Cannot see owner options
- Cannot access market money

---

## System Features

### Dynamic Prop Management

**Spawning System:**
- Markets spawn when players are within 50m
- Markets despawn when players move beyond 100m
- Reduces entity load for performance
- Handles network ownership properly

**Vegetation Clearing:**
```lua path=null start=null
if Config.EnableVegModifier then
    -- Creates 5m radius vegetation modifier sphere
    -- Clears grass, bushes, and other foliage
    -- Cleanup on market removal
end
```

### Transaction Safety

The script includes rollback mechanisms:

**Stock Addition:**
1. Verify player has items
2. Check blacklist and quality
3. Add to database
4. Remove from player inventory
5. If removal fails → rollback database

**Item Purchase:**
1. Verify stock availability
2. Check player funds
3. Update stock in database
4. Deduct player money
5. If deduction fails → rollback stock
6. Add item to inventory
7. Credit market owner

**Market Repair:**
1. Check player funds
2. Deduct repair cost
3. Update market quality
4. If database fails → refund player

### Automatic Cleanup

**Stock Cleanup** (`StockCronJob`):
- Runs every 5 minutes by default
- Deletes items with 0 stock
- Bulk deletion for performance

**Market Upkeep** (`UpkeepCronJob`):
- Runs hourly by default
- Decreases all market quality by 1
- Removes markets at 0% quality
- Logs destroyed markets

---

## Client-Side Functions

### Core Events

#### `rex-market:client:createprop`
Initiates prop placement mode.

```lua path=null start=null
TriggerEvent('rex-market:client:createprop', propmodel, item)
```
- **propmodel**: Hash/name of prop model
- **item**: Item name (marketstall)

#### `rex-market:client:openmarket`
Opens appropriate menu based on ownership.

```lua path=null start=null
TriggerEvent('rex-market:client:openmarket', marketid, owner_citizenid, entity)
```
- **marketid**: Unique market identifier
- **owner_citizenid**: Owner's citizen ID
- **entity**: Market entity handle

#### `rex-market:client:placenewprop`
Finalizes prop placement after confirmation.

```lua path=null start=null
TriggerEvent('rex-market:client:placenewprop', propmodel, item, coords, heading)
```
- **propmodel**: Prop model
- **item**: Item name
- **coords**: Vector3 coordinates
- **heading**: Rotation angle

#### `rex-market:client:updatePropData`
Updates client prop configuration.

```lua path=null start=null
TriggerEvent('rex-market:client:updatePropData', data)
```
- **data**: Table of all market props

#### `rex-market:client:removePropObject`
Removes specific prop from client.

```lua path=null start=null
TriggerEvent('rex-market:client:removePropObject', marketid)
```

### Menu Events

#### Owner Menu Events
- `rex-market:client:openownermenu` - Main owner menu
- `rex-market:client:ownerviewshopitems` - View stock (owner)
- `rex-market:client:newstockitem` - Add/update stock
- `rex-market:client:removestockitem` - Remove stock
- `rex-market:client:checkmoney` - Withdraw funds
- `rex-market:client:maintenance` - View/repair condition
- `rex-market:client:packupmarket` - Pack up market
- `rex-market:client:repairmarketstall` - Repair market

#### Customer Menu Events
- `rex-market:client:customerviewshopitems` - Browse market
- `rex-market:client:buyshopitem` - Purchase item

### Helper Functions

#### `CanPlacePropHere(pos)`
Validates placement location.

```lua path=null start=null
local canPlace = CanPlacePropHere(vector3(x, y, z))
```
**Checks:**
- Town restriction (if enabled)
- Minimum distance from other markets

#### `CreateStockMenuItems(result, isOwner)`
Generates menu options from stock data.

```lua path=null start=null
local options = CreateStockMenuItems(stockData, true)
```

---

## Server-Side Functions

### Callbacks

#### `rex-market:server:countprop`
Counts player's markets.

```lua path=null start=null
RSGCore.Functions.CreateCallback('rex-market:server:countprop', function(source, cb, item)
    -- Returns count of markets owned by player
end)
```

#### `rex-market:server:checkstock`
Retrieves market stock.

```lua path=null start=null
RSGCore.Functions.CreateCallback('rex-market:server:checkstock', function(source, cb, marketid)
    -- Returns array of stock items or nil
end)
```

#### `rex-market:server:getallmarketdata`
Gets complete market data.

```lua path=null start=null
RSGCore.Functions.CreateCallback('rex-market:server:getallmarketdata', function(source, cb, marketid)
    -- Returns market data or nil
end)
```

#### `rex-market:server:getmarketstalldata`
Gets specific market stall data.

```lua path=null start=null
RSGCore.Functions.CreateCallback('rex-market:server:getmarketstalldata', function(source, cb, marketid)
    -- Returns market data including quality
end)
```

#### `rex-market:server:getmoney`
Gets market funds.

```lua path=null start=null
RSGCore.Functions.CreateCallback('rex-market:server:getmoney', function(source, cb, marketid)
    -- Returns { marketid, money }
end)
```

#### `rex-market:server:cashcallback`
Gets player's cash.

```lua path=null start=null
RSGCore.Functions.CreateCallback('rex-market:server:cashcallback', function(source, cb)
    -- Returns player cash amount
end)
```

#### `rex-market:server:checkblacklist`
Checks if item is blacklisted.

```lua path=null start=null
RSGCore.Functions.CreateCallback('rex-market:server:checkblacklist', function(source, cb, item)
    -- Returns true if blacklisted
end)
```

#### `rex-market:server:checkquality`
Validates item quality.

```lua path=null start=null
RSGCore.Functions.CreateCallback('rex-market:server:checkquality', function(source, cb, item, amount)
    -- Returns true if meets quality requirement
end)
```

### Server Events

#### `rex-market:server:newProp`
Creates new market in database.

```lua path=null start=null
TriggerServerEvent('rex-market:server:newProp', propmodel, item, coords, heading)
```

#### `rex-market:server:saveProp`
Saves prop data to database.

```lua path=null start=null
TriggerEvent('rex-market:server:saveProp', data, marketid, citizenid, owner, item)
```

#### `rex-market:server:updateProps`
Broadcasts prop updates to clients.

```lua path=null start=null
TriggerEvent('rex-market:server:updateProps')
```

#### `rex-market:server:destroyProp`
Removes market and returns item.

```lua path=null start=null
TriggerServerEvent('rex-market:server:destroyProp', marketid, item)
```

#### `rex-market:server:PropRemoved`
Deletes market from database.

```lua path=null start=null
TriggerEvent('rex-market:server:PropRemoved', marketid)
```

#### `rex-market:server:buyitemamount`
Processes item purchase.

```lua path=null start=null
TriggerServerEvent('rex-market:server:buyitemamount', amount, item, newstock, price, label, marketid)
```

#### `rex-market:server:newstockitem`
Adds or updates stock.

```lua path=null start=null
TriggerServerEvent('rex-market:server:newstockitem', marketid, item, amount, price)
```

#### `rex-market:server:removestockitem`
Removes stock item.

```lua path=null start=null
TriggerServerEvent('rex-market:server:removestockitem', data)
```

#### `rex-market:server:withdrawfunds`
Withdraws money from market.

```lua path=null start=null
TriggerServerEvent('rex-market:server:withdrawfunds', amount, marketid)
```

#### `rex-market:server:repairmarketstall`
Repairs market condition.

```lua path=null start=null
TriggerServerEvent('rex-market:server:repairmarketstall', marketid, repaircost)
```

### Helper Functions

#### `CreateMarketId()`
Generates unique market identifier.

```lua path=null start=null
local marketid = CreateMarketId()
-- Returns: 'market_12345678_1234567890' or nil
```

#### `IsItemBlacklisted(item)`
Checks blacklist status.

```lua path=null start=null
if IsItemBlacklisted('weapon_revolver_cattleman') then
    -- Item is blacklisted
end
```

#### `DoesItemMeetQuality(src, item, amount)`
Validates item quality in inventory.

```lua path=null start=null
if DoesItemMeetQuality(source, 'meat', 5) then
    -- Player has 5 items meeting quality requirement
end
```

#### `RemoveItemsWithQuality(src, item, amount)`
Removes items meeting quality standards.

```lua path=null start=null
local success = RemoveItemsWithQuality(source, 'meat', 5)
```

---

## Events

### Client Events

| Event | Parameters | Description |
|-------|------------|-------------|
| `rex-market:client:createprop` | propmodel, item | Start placement mode |
| `rex-market:client:openmarket` | marketid, owner_citizenid, entity | Open market menu |
| `rex-market:client:openownermenu` | marketid, entity | Owner menu |
| `rex-market:client:ownerviewshopitems` | data{marketid} | View owner stock |
| `rex-market:client:customerviewshopitems` | marketid | View customer stock |
| `rex-market:client:newstockitem` | data{marketid} | Add stock dialog |
| `rex-market:client:removestockitem` | data{marketid} | Remove stock menu |
| `rex-market:client:buyshopitem` | data{label, stock, price, item, marketid} | Purchase dialog |
| `rex-market:client:checkmoney` | data{marketid} | Withdraw dialog |
| `rex-market:client:maintenance` | data{marketid} | Maintenance menu |
| `rex-market:client:repairmarketstall` | data{marketid, repaircost} | Repair confirmation |
| `rex-market:client:packupmarket` | data{marketid, entity} | Packup confirmation |
| `rex-market:client:placenewprop` | propmodel, item, coords, heading | Finalize placement |
| `rex-market:client:updatePropData` | data | Update prop config |
| `rex-market:client:removePropObject` | marketid | Remove client prop |

### Server Events

| Event | Parameters | Description |
|-------|------------|-------------|
| `rex-market:server:newProp` | propmodel, item, coords, heading | Create market |
| `rex-market:server:saveProp` | data, marketid, citizenid, owner, item | Save to DB |
| `rex-market:server:updateProps` | - | Broadcast updates |
| `rex-market:server:getProps` | - | Load from DB |
| `rex-market:server:destroyProp` | marketid, item | Remove market |
| `rex-market:server:PropRemoved` | marketid | Delete from DB |
| `rex-market:server:buyitemamount` | amount, item, newstock, price, label, marketid | Process purchase |
| `rex-market:server:newstockitem` | marketid, item, amount, price | Add stock |
| `rex-market:server:removestockitem` | data{marketid, item} | Remove stock |
| `rex-market:server:withdrawfunds` | amount, marketid | Withdraw money |
| `rex-market:server:repairmarketstall` | marketid, repaircost | Repair market |

---

## Callbacks

### Available Callbacks

| Callback | Parameters | Returns | Description |
|----------|------------|---------|-------------|
| `rex-market:server:countprop` | item | count | Player's market count |
| `rex-market:server:checkstock` | marketid | result[] or nil | Market stock items |
| `rex-market:server:getallmarketdata` | marketid | result[] or nil | Complete market data |
| `rex-market:server:getmarketstalldata` | marketid | result[] or nil | Market stall data |
| `rex-market:server:getmoney` | marketid | {marketid, money} or nil | Market funds |
| `rex-market:server:cashcallback` | - | amount | Player cash |
| `rex-market:server:checkblacklist` | item | boolean | Is blacklisted |
| `rex-market:server:checkquality` | item, amount | boolean | Meets quality |

---

## Localization

Locales are stored in `locales/en.json`. Create additional language files as needed.

### Available Locale Keys

**Client:**
- `cl_lang_1` through `cl_lang_49` - UI messages and prompts

**Server:**
- `sv_lang_1` through `sv_lang_12` - Server notifications and logs

### Adding New Languages

1. Copy `locales/en.json`
2. Rename to your language code (e.g., `es.json`, `fr.json`)
3. Translate all values
4. Load with `lib.locale()` (automatic)

### Using Locales

```lua path=null start=null
-- Client/Server
local message = locale('cl_lang_1')  -- Returns: "Reached Maximum Allowed!"
```

---

## Troubleshooting

### Common Issues

#### Markets Not Spawning
**Symptoms:** Props don't appear in world
**Solutions:**
1. Check console for model load errors
2. Verify database tables exist
3. Ensure `rex-market:server:getProps` completed
4. Check if player is within 50m range

#### Can't Place Market
**Symptoms:** Placement restricted
**Solutions:**
1. Check if player is in a town (`Config.RestrictTowns`)
2. Verify minimum distance from other markets
3. Confirm player hasn't reached `Config.MaxMarkets`
4. Check item is in player inventory

#### Stock Not Adding
**Symptoms:** Items won't add to market
**Solutions:**
1. Verify item isn't blacklisted
2. Check item quality meets `Config.MinimumQuality`
3. Confirm player has required amount
4. Check for database errors in console

#### Quality System Not Working
**Symptoms:** Markets don't degrade
**Solutions:**
1. Verify `lib.cron` is working (ox_lib)
2. Check `Config.UpkeepCronJob` format
3. Look for cron errors in console
4. Test with shorter intervals

#### Transactions Failing
**Symptoms:** Purchases don't complete
**Solutions:**
1. Check player has sufficient funds
2. Verify stock availability
3. Look for database connection errors
4. Check for rollback messages in console

### Performance Issues

**High Entity Load:**
- Reduce `Config.MaxMarkets`
- Increase despawn distance in client code
- Disable `Config.EnableVegModifier` if not needed

**Database Lag:**
- Add database indexes (already included)
- Increase `StockCronJob` interval
- Optimize oxmysql configuration

**Client FPS Drops:**
- Increase `checkInterval` in spawn thread
- Reduce `updateInterval` in placement mode
- Disable visual effects (DrawPropAxes)

### Debug Mode

Enable debug output:

```lua path=null start=null
Config.EnableServerNotify = true  -- Server console logs
```

Check console for:
- Market creation IDs
- Upkeep cycle results
- Stock cleanup reports
- Model load timeouts
- Database errors

### Error Messages

**"Player data not available"**
- RSGCore not loaded properly
- Player not fully spawned

**"Invalid market ID"**
- Market doesn't exist in database
- Synchronization issue between client/server

**"Failed to generate unique market ID"**
- Database connection issue
- Extremely rare collision (100+ attempts)

**"This item cannot be sold in market stalls"**
- Item is in `Config.BlacklistItems`

**"Only items in perfect condition (100% quality) can be sold"**
- Item quality below `Config.MinimumQuality`

---

## API Examples

### Creating a Market Programmatically

```lua path=null start=null
-- Server-side
TriggerEvent('rex-market:server:newProp',
    Config.MarketProp,
    'marketstall',
    vector3(x, y, z),
    heading
)
```

### Checking Market Count

```lua path=null start=null
-- Client-side
RSGCore.Functions.TriggerCallback('rex-market:server:countprop', function(count)
    print('Player has ' .. count .. ' markets')
end, 'marketstall')
```

### Forcing Market Removal

```lua path=null start=null
-- Server-side
TriggerEvent('rex-market:server:PropRemoved', marketid)
TriggerClientEvent('rex-market:client:removePropObject', -1, marketid)
TriggerEvent('rex-market:server:updateProps')
```

### Custom Blacklist Check

```lua path=null start=null
-- Client-side
RSGCore.Functions.TriggerCallback('rex-market:server:checkblacklist', function(isBlacklisted)
    if isBlacklisted then
        -- Handle blacklisted item
    end
end, itemName)
```

---

## Credits

**Script:** Rex-Market  
**Framework:** RSG-Core (RedM)  
**Version:** 2.1.3  
**Dependencies:** rsg-core, ox_lib, oxmysql

---

## Support

For issues, suggestions, or contributions:
- Check console for error messages
- Review configuration settings
- Verify database structure
- Test with default configuration
- Check framework compatibility

---

## Changelog

### Version 2.1.3
- Enhanced error handling
- Transaction rollback mechanisms
- Quality restriction system
- Item blacklist system
- Performance optimizations
- Improved prop spawning
- Better database indexing
- Cron job cleanup systems

---

## License

See LICENSE.md for licensing information.
