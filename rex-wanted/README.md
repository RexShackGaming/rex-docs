# rex-wanted

A dynamic wanted/outlaw system for RedM that spawns bounty hunters to pursue players with high outlaw status.

## Features
- **Dynamic Outlaw System**: Players accumulate outlaw status through criminal activities
- **Bounty Hunter NPCs**: Automatically spawn bounty hunters when outlaw threshold is reached
- **Multiple Spawn Locations**: Bounty hunters spawn at various locations across the map
- **Discord Webhook Integration**: Log outlaw activities and status changes to Discord
- **Performance Optimized**: Configurable spawn limits and check intervals
- **Death Penalty**: Optional clearing of wanted status on player death
- **Extensive Exports**: Client and server exports for integration with other resources
- **Configurable Settings**: Customize trigger amounts, spawn distances, NPC behavior, and more

## Dependencies
- [rsg-core](https://github.com/Rexshack-RedM/rsg-core)
- [ox_lib](https://github.com/Rexshack-RedM/ox_lib)
- [oxmysql](https://github.com/CommunityOx/oxmysql/releases/latest/download/oxmysql.zip)

## Installation

### Step 1: Install Dependencies
Ensure all required dependencies are installed and started:
```cfg
ensure oxmysql
ensure ox_lib
ensure rsg-core
```

### Step 2: Add Resource
1. Download or clone this repository
2. Place the `rex-wanted` folder in your server's `resources` directory

### Step 3: Configure
1. Open `shared/config.lua` and customize settings:
   - Set `Config.OutlawTriggerAmount` (default: 20)
   - Configure Discord webhook URL if desired
   - Adjust NPC spawn settings and locations
2. Review `installation/DISCORD_WEBHOOK_README.md` for webhook setup

### Step 4: Start Resource
Add to your `server.cfg`:
```cfg
ensure rex-wanted
```

### Step 5: Verify Installation
Restart your server and check console for any errors. The resource should load successfully.

## Exports

This resource provides extensive exports for integration with other scripts. See `installation/EXPORTS.md` for complete documentation.

### Quick Export Examples

**Client-Side:**
```lua
-- Get player's outlaw status
local status = exports['rex-wanted']:GetOutlawStatus()

-- Check if bounty hunters are hostile
local hostile = exports['rex-wanted']:IsHostile()
```

**Server-Side:**
```lua
-- Add outlaw status to player
exports['rex-wanted']:AddPlayerOutlawStatus(source, 10, 'Robbery')

-- Clear player's outlaw status
exports['rex-wanted']:ClearPlayerOutlawStatus(source)
```

For detailed export documentation and integration examples, see `installation/EXPORTS.md`.

## Configuration

Key configuration options in `shared/config.lua`:
- `Config.WantedSystemActive`: Enable/disable the wanted system
- `Config.OutlawTriggerAmount`: Outlaw status threshold to trigger bounty hunters (default: 20)
- `Config.ClearWantedOnDeath`: Clear outlaw status when player dies (default: true)
- `Config.MaxSpawnedPedsPerPlayer`: Maximum bounty hunters per player (default: 10)
- `Config.Discord.Enabled`: Enable Discord webhook logging
- `Config.BanditLocations`: Configure spawn locations for bounty hunters

## Support & Community
- **Discord**: https://discord.gg/YUV7ebzkqs
- **YouTube**: https://www.youtube.com/@rexshack/videos
- **Tebex**: https://rexshackgaming.tebex.io/

## Version
Current Version: **2.1.0**

## License
This resource includes escrow-protected files. See `fxmanifest.lua` for escrow ignore list.
