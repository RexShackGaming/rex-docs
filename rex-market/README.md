# 🛒 Rex Market - RedM Player-Owned Markets

**Rex Market** is a comprehensive RedM script that allows players to create, manage, and trade at their own portable market stalls. Built with performance and stability in mind, this script provides a complete marketplace experience for your RedM server.

## ✨ Key Features

### 🏪 **Player-Owned Markets**
- Place portable market stalls anywhere on the map
- Customizable placement restrictions (towns, distances)
- Multiple market stalls per player (configurable limit)
- Persistent markets that survive server restarts

### 💰 **Complete Trading System**
- Buy and sell items with other players
- Dynamic pricing set by market owners
- Automatic stock management
- Real-time inventory synchronization
- Multiple currency support (cash/bloodmoney)

### 🔧 **Market Management**
- **Stock Management**: Add/remove items, set prices and quantities
- **Quality System**: Markets degrade over time and require maintenance
- **Repair System**: Fix damaged markets with customizable costs
- **Money Management**: Withdraw earnings from market sales
- **Pack & Move**: Relocate your market when needed
- **Item Blacklist**: Configurable system to restrict certain items from being sold
- **Quality Control**: Only allow items in perfect condition (100% quality) to be sold

### 🎨 **User Experience**
- **Intuitive Menus**: Clean ox_lib context menus
- **Visual Feedback**: Blips, notifications, and progress bars
- **Prop Placement**: Interactive placement with rotation controls
- **Target Integration**: ox_target for easy interaction
- **Multilingual Support**: Localization system included

### ⚡ **Performance & Stability**
- **Optimized Spawning**: Distance-based culling and smart loading
- **Memory Management**: Proper cleanup and leak prevention
- **Database Integrity**: Foreign key constraints and data validation
- **Error Handling**: Comprehensive error checking and recovery
- **Resource Monitoring**: Built-in performance optimizations
- **Security Features**: Server-side validation and blacklist enforcement

## 📋 Requirements

### Dependencies
- **[rsg-core](https://github.com/Rexshack-RedM/rsg-core)** - Core framework
- **[ox_lib](https://github.com/overextended/ox_lib)** - UI and utility library
- **[oxmysql](https://github.com/overextended/oxmysql)** - Database connector

### Optional Dependencies
- **[ox_target](https://github.com/overextended/ox_target)** - Entity targeting
- **[rsg-inventory](https://github.com/Rexshack-RedM/rsg-inventory)** - Inventory system

## 🚀 Installation

### 1. Database Setup
Execute the SQL file to create the required tables:
```sql
-- Run this file in your MySQL database
source resources/rex-market/installation/rex-market.sql
```

**Alternative**: Use the fixed schema for better performance:
```sql
source resources/rex-market/installation/rex-market_fixed.sql
```

### 2. Add Items to Shared Items
Add the market stall item to your `rsg-core/shared/items.lua`:
```lua
-- In your items.lua file
marketstall = { 
    name = 'marketstall', 
    label = 'Market Stall', 
    weight = 1000, 
    type = 'item', 
    image = 'marketstall.png', 
    unique = true, 
    useable = true, 
    shouldClose = true, 
    description = 'A portable market stall for trading goods' 
},
```

### 3. Add Item Image
Copy the market stall image to your inventory images folder:
```
rsg-inventory/html/images/marketstall.png
```

### 4. Configure the Script
Edit `config.lua` to customize settings:
```lua
Config.MaxMarkets = 1              -- Max markets per player
Config.PlaceMinDistance = 4        -- Minimum distance between markets
Config.RestrictTowns = true        -- Prevent placement in towns
Config.Money = 'cash'              -- Currency type ('cash' or 'bloodmoney')
Config.RepairCost = 1              -- Cost per quality point to repair
Config.EnableBlacklist = true      -- Enable item restriction system
Config.EnableQualityCheck = true   -- Only allow perfect condition items
Config.MinimumQuality = 100        -- Minimum quality required (0-100)
```

### 5. Start the Resource
Add to your `server.cfg`:
```cfg
ensure rex-market
```

## 🎮 How to Use

### For Players

#### **Setting Up Your Market**
1. **Obtain a Market Stall**: Get the `marketstall` item from an admin or shop
2. **Find a Location**: Choose a spot away from towns (if restricted)
3. **Place Your Stall**: Use the item to enter placement mode
4. **Position & Rotate**: Use arrow keys to rotate, hold E to place, X to cancel

#### **Managing Your Market**
1. **Approach Your Stall**: Walk up to your placed market stall
2. **Open Owner Menu**: Click to access the management interface
3. **Add Stock**: Select items from your inventory to sell
4. **Set Prices**: Configure individual item prices
5. **Monitor Sales**: Check earnings and withdraw money
6. **Maintain Quality**: Repair your stall to prevent decay

#### **Shopping at Markets**
1. **Find Player Markets**: Look for market blips on the map
2. **Browse Items**: Interact with any market stall to see stock
3. **Purchase Items**: Buy items directly to your inventory
4. **Support Players**: Help the local economy by shopping!

### For Server Owners

#### **Admin Commands** (if applicable)
```lua
-- Give market stall to player
/giveitem [playerid] marketstall 1

-- Check market database
SELECT * FROM rex_market;
SELECT * FROM rex_market_stock;
```

#### **Configuration Options**
```lua
-- Market Settings
Config.MaxMarkets = 3              -- Allow more markets per player
Config.EnableVegModifier = true    -- Clear vegetation around markets
Config.PackupTime = 10000          -- Time to pack up market (ms)
Config.EnableBlacklist = true      -- Enable item restrictions
Config.EnableQualityCheck = true   -- Enable quality restrictions

-- Blip Customization
Config.Blip = {
    blipName = 'Player Market',
    blipSprite = 'blip_shop_market_stall',
    blipScale = 0.2,
    blipColour = 'BLIP_MODIFIER_MP_COLOR_6'
}

-- Cronjob Settings (automatic maintenance)
Config.UpkeepCronJob = '0 * * * *'    -- Every hour
Config.StockCronJob = '*/5 * * * *'   -- Every 5 minutes
```

## 🔧 Advanced Configuration

### Market Placement Controls
```lua
-- Placement settings
Config.PlaceDistance = 5.0         -- How far from player to place
Config.PlaceMinDistance = 4        -- Minimum distance between markets
Config.RestrictTowns = true        -- Block placement in populated areas
```

### Quality & Maintenance System
```lua
-- Markets lose 1 quality per hour by default
-- At 0 quality, markets are automatically removed
Config.RepairCost = 1              -- Cost per quality point to repair
```

### Blacklist System Configuration
```lua
-- Enable/disable blacklist system
Config.EnableBlacklist = true

-- Items that cannot be sold in markets
Config.BlacklistItems = {
    -- Weapons and ammunition (commonly restricted)
    'weapon_revolver_cattleman',
    'weapon_revolver_cattleman_mexican',
    'weapon_revolver_doubleaction',
    'weapon_revolver_doubleaction_gambler',
    'weapon_revolver_schofield',
    'weapon_revolver_lemat',
    'weapon_revolver_navy',
    'weapon_revolver_navy_crossover',
    'weapon_pistol_volcanic',
    'weapon_pistol_m1899',
    'weapon_pistol_mauser',
    'weapon_pistol_semiauto',
    'weapon_repeater_carbine',
    'weapon_repeater_winchester',
    'weapon_repeater_henry',
    'weapon_repeater_evans',
    'weapon_rifle_varmint',
    'weapon_rifle_springfield',
    'weapon_rifle_boltaction',
    'weapon_rifle_elephant',
    'weapon_sniperrifle_rollingblock',
    'weapon_sniperrifle_rollingblock_exotic',
    'weapon_sniperrifle_carcano',
    'weapon_shotgun_doublebarrel',
    'weapon_shotgun_doublebarrel_exotic',
    'weapon_shotgun_sawedoff',
    'weapon_shotgun_semiauto',
    'weapon_shotgun_pump',
    'weapon_shotgun_repeating',
    'weapon_bow',
    'weapon_bow_improved',

    -- Market stall itself (prevents selling the stall)
    'marketstall',
    
    -- Add more items as needed
    -- 'item_name_here',
}
```

### Quality Control Configuration
```lua
-- Enable/disable quality restrictions
Config.EnableQualityCheck = true

-- Set minimum quality requirement (0-100)
Config.MinimumQuality = 100        -- Only perfect condition
Config.MinimumQuality = 80         -- Allow good condition  
Config.MinimumQuality = 50         -- Allow average condition
Config.MinimumQuality = 0          -- Allow any quality (disabled)

-- Quality checking works with RSG-Core inventory system
-- Items must have quality data in their info field:
-- item.info.quality = 100 (perfect condition)
-- item.info.quality = 0 (completely damaged)
```

### Performance Tuning
```lua
-- Client-side optimization
local checkInterval = 1000         -- Prop spawn check frequency (ms)
local maxRenderDistance = 100.0    -- Maximum render distance
local nearbyDistance = 50.0        -- Distance to check for spawning
```

## 📊 Database Schema

### Tables Created
- **`rex_market`** - Main market data and properties
- **`rex_market_stock`** - Individual item stock and pricing

### Key Fields
```sql
-- rex_market table
marketid VARCHAR(50)    -- Unique market identifier
citizenid VARCHAR(50)   -- Owner's citizen ID
quality INT(3)          -- Market condition (0-100)
money DECIMAL(10,2)     -- Market earnings

-- rex_market_stock table
item VARCHAR(50)        -- Item name
stock INT(11)           -- Quantity available
price DECIMAL(10,2)     -- Price per item
```

## 🐛 Troubleshooting

### Common Issues

**Market won't place:**
- Check if you're too close to another market
- Ensure you're not in a restricted town area
- Verify you have the required permissions

**Items not showing:**
- Confirm items exist in `rsg-core/shared/items.lua`
- Check that item images are in the correct folder
- Verify database connection

**Performance issues:**
- Reduce `Config.MaxMarkets` if too many markets exist
- Increase spawn check intervals in config
- Monitor server resources

**Blacklist issues:**
- Items showing "cannot be sold" - Check `Config.BlacklistItems` in config.lua
- Blacklist not working - Verify `Config.EnableBlacklist = true`
- Need to allow restricted item - Remove from `Config.BlacklistItems` array

**Quality control issues:**
- "Only items in perfect condition can be sold" - Item quality below minimum requirement
- Quality check not working - Verify `Config.EnableQualityCheck = true` and `Config.MinimumQuality` setting
- Want to allow damaged items - Lower `Config.MinimumQuality` or set to 0 to disable

### Error Messages
- `"Reached Maximum Allowed!"` - Player has too many markets
- `"Can't place that here!"` - Invalid placement location
- `"You are currently busy!"` - Player already placing something
- `"Need to repair first"` - Market quality too low to pack
- `"This item cannot be sold in market stalls"` - Item is blacklisted
- `"Only items in perfect condition (100% quality) can be sold"` - Item quality too low

## 🤝 Support & Community

- **Discord**: [Join our community](https://discord.gg/YUV7ebzkqs)
- **YouTube**: [RexShack Gaming](https://www.youtube.com/@rexshack/videos)
- **Tebex**: [Support the development](https://rexshackgaming.tebex.io/)

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 📋 Changelog

### Version 2.1.1
- **NEW**: Configurable blacklist system for restricting items
- **NEW**: Quality control system - only perfect condition items can be sold
- **NEW**: Server-side validation prevents selling blacklisted/low-quality items
- **NEW**: Smart inventory scanning with quality-aware item removal
- **NEW**: Localized error messages for blacklist and quality violations
- **NEW**: Runtime checking APIs for both blacklist and quality systems
- **IMPROVED**: Enhanced security with comprehensive item restriction enforcement
- **IMPROVED**: Transaction rollback mechanisms for failed quality checks
- **ADDED**: Comprehensive documentation and configuration examples

### Version 2.1.0
- Performance optimizations and bug fixes
- Enhanced error handling and validation
- Improved database integrity with foreign keys
- Memory leak prevention and cleanup improvements

## 🙏 Credits

- **RexShack Gaming** - Original development
- **RSG-Core Team** - Framework support
- **Overextended** - ox_lib and oxmysql libraries
- **Community Contributors** - Bug reports and testing

---

**Made with ❤️ for the RexShack Gaming**

*Transform your server's economy with player-driven markets!*
