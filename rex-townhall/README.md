# 🏛️ Rex Townhall

[![Discord](https://img.shields.io/discord/1176609103410376714?color=7289da&logo=discord&logoColor=white)](https://discord.gg/YUV7ebzkqs)
[![Version](https://img.shields.io/badge/version-2.1.2-blue.svg)](https://github.com/yourusername/rex-townhall)
[![Game](https://img.shields.io/badge/game-RedM-red.svg)](https://redm.net/)

A comprehensive job management system for RedM servers using the RSG Framework. This resource provides a user-friendly town hall interface where players can apply for various jobs, manage their employment status, and track their career progression.

## 📋 Table of Contents

- [Features](#-features)
- [Dependencies](#-dependencies)
- [Installation](#-installation)
- [Configuration](#-configuration)
- [Usage](#-usage)
- [Localization](#-localization)
- [Discord Integration](#-discord-integration)
- [Support](#-support)
- [Credits](#-credits)

## ✨ Features

### Core Functionality
- **Multiple Job Locations**: Set up town halls in various towns (St. Denis, Valentine, Rhodes, etc.)
- **Interactive NPCs**: Configurable NPCs at each town hall location with customizable models
- **Job Application System**: Players can apply for jobs with customizable costs and starting items
- **Multi-Job Support**: Integrated with rsg-multijob for players to hold multiple positions
- **Multi-Job Limit Enforcement**: Server and client-side validation ensures players respect configured job limits
- **Job Resignation**: Players can voluntarily leave their current positions
- **Confirmation Dialogs**: Optional confirmation prompts before job changes
- **Cooldown System**: Configurable cooldown period between job changes (default: 5 minutes)
- **Starting Items**: Automatically give players job-specific items upon employment
- **Item Validation**: Validates all job items exist in RSG framework before giving them to players

### User Interface
- **Modern UI**: Built with ox_lib context menus for a clean, intuitive interface
- **Current Job Display**: Shows player's current job and grade in the menu
- **Visual Feedback**: Progress bars, notifications, and animations
- **Icon Support**: FontAwesome icons for better visual representation
- **Responsive Design**: Adapts to player actions with real-time updates

### Administrative Features
- **Discord Webhook Integration**: Log job applications and resignations to Discord
- **Version Checker**: Automatic version checking to keep your resource up to date
- **Console Logging**: Comprehensive server-side logging for tracking and debugging
- **Error Handling**: Graceful handling of invalid items and configuration errors
- **Configurable Blips**: Show/hide town hall locations on the map
- **NPC Distance Spawning**: NPCs spawn only when players are nearby for performance
- **Security**: Server-side validation prevents exploits and bypassing of job limits

## 📦 Dependencies

Ensure these resources are installed and started before rex-townhall:

- **[rsg-core](https://github.com/Rexshack-RedM/rsg-core)** - Core framework (Required)
- **[rsg-multijob](https://github.com/Rexshack-RedM/rsg-multijob)** - Multi-job system (Required)
- **[ox_lib](https://github.com/overextended/ox_lib)** - UI library (Required)

## 🚀 Installation

### Step 1: Download
Download the latest release from the [releases page](https://github.com/yourusername/rex-townhall/releases) or clone this repository.

### Step 2: Extract
Extract the `rex-townhall` folder to your server's `resources` directory.

```
server/
└── resources/
    └── rex-townhall/
        ├── client/
        ├── server/
        ├── locales/
        ├── config.lua
        ├── fxmanifest.lua
        └── README.md
```

### Step 3: Configure Jobs
Edit `config.lua` to add your server's specific jobs. Make sure these jobs exist in your `rsg-core/shared/jobs.lua`:

```lua
Config.Jobs = {
    { 
        title = 'Job Title',
        description = 'Job description',
        icon = 'fa-solid fa-icon-name',
        job = 'jobname',  -- Must match rsg-core job name
        grade = 1,
        jobcost = 5,
        jobitems = {  -- Optional: Items given when taking the job
            { item = 'item_name', amount = 1 },  -- Item must exist in rsg-core/shared/items.lua
        },
        arrow = true
    },
}
```

**Important Notes:**
- Job names must match exactly with jobs defined in `rsg-core/shared/jobs.lua`
- Item names in `jobitems` must exist in `rsg-core/shared/items.lua` or they will be skipped
- Invalid items will be logged to console but won't break the job application

### Step 4: Add to server.cfg
Add the following line to your `server.cfg`:

```cfg
ensure rex-townhall
```

**Important:** Make sure dependencies are loaded first:
```cfg
ensure rsg-core
ensure rsg-multijob
ensure ox_lib
ensure rex-townhall
```

### Step 5: Restart Server
Restart your server or use the command:
```
refresh
start rex-townhall
```

## ⚙️ Configuration

### NPC Settings
```lua
Config.DistanceSpawn = 20.0          -- Distance for NPC spawning
Config.FadeIn = true                  -- Enable fade-in effect for NPCs
Config.InteractionDistance = 2.5      -- Distance to interact with NPCs
```

### Gameplay Settings
```lua
Config.JobChangeCooldown = 300000     -- Cooldown in milliseconds (5 minutes)
Config.RequireConfirmation = true     -- Require confirmation before job change
Config.ShowCurrentJob = true          -- Display current job in menu
Config.PlaySounds = true              -- Enable sound effects
```

### Discord Webhook Settings
```lua
Config.UseDiscordWebhook = false      -- Enable/disable Discord logging
Config.DiscordWebhook = ''            -- Your webhook URL
Config.WebhookName = 'Town Hall Jobs' -- Webhook display name
Config.WebhookAvatar = 'URL'          -- Webhook avatar URL
Config.WebhookColor = 3447003         -- Embed color (decimal)
```

### Town Hall Locations
```lua
Config.TownHallLocations = {
    { 
        name = 'Location Name',
        prompt = 'prompt-id',
        coords = vector3(x, y, z),
        npcmodel = 'model_name',
        npccoords = vector4(x, y, z, heading),
        location = 'location_identifier',
        blipname = 'Blip Name',
        blipsprite = 'blip_sprite_name',
        blipscale = 0.2,
        showblip = true
    },
}
```

## 📖 Usage

### For Players

1. **Finding Town Halls**
   - Look for town hall blips on your map (if enabled)
   - Visit any configured town hall location

2. **Applying for Jobs**
   - Approach the NPC at the town hall
   - Press the interaction key when prompted
   - Browse available jobs in the menu
   - Select a job and confirm your application
   - Pay the administration fee in cash
   - Receive your starting items (if applicable)

3. **Leaving Jobs**
   - Open the town hall menu
   - Select "Leave Job"
   - Confirm your resignation

4. **Job Cooldown**
   - After changing jobs, you must wait 5 minutes (configurable) before changing again
   - The remaining cooldown time will be displayed if you try to change too soon

### For Administrators

1. **Adding New Jobs**
   - Add job definition in `config.lua`
   - Ensure the job exists in `rsg-core/shared/jobs.lua`
   - Restart the resource

2. **Adding New Town Halls**
   - Add a new entry in `Config.TownHallLocations`
   - Configure coordinates, NPC model, and blip settings
   - Restart the resource

3. **Monitoring Job Changes**
   - Check server console for job application logs
   - Enable Discord webhooks for remote monitoring

## 🌍 Localization

The resource supports multiple languages through JSON locale files in the `locales/` directory.

### Available Languages
- English (`en.json`)

### Adding New Languages

1. Copy `locales/en.json` to a new file (e.g., `locales/es.json`)
2. Translate all values while keeping the keys unchanged
3. Players will automatically use the language based on their game settings

### Locale Keys
```json
{
    "cl_lang_1": "Jobs",
    "cl_lang_2": "Administration Cost $",
    "sv_lang_1": "Job Applied!",
    // ... more keys
}
```

## 📢 Discord Integration

### Setting Up Discord Webhooks

1. **Create a Webhook**
   - Go to your Discord server settings
   - Navigate to Integrations → Webhooks
   - Create a new webhook
   - Copy the webhook URL

2. **Configure rex-townhall**
   ```lua
   Config.UseDiscordWebhook = true
   Config.DiscordWebhook = 'your-webhook-url-here'
   Config.WebhookName = 'Town Hall Jobs'
   Config.WebhookAvatar = 'https://i.imgur.com/your-avatar.png'
   Config.WebhookColor = 3447003  -- Blue color
   ```

3. **Logged Events**
   - Job applications (player name, job, cost)
   - Job resignations (player name, previous job)

## 🆘 Support

### Getting Help
- **Discord**: [Join our Discord](https://discord.gg/YUV7ebzkqs)
- **YouTube**: [RexShack Gaming](https://www.youtube.com/@rexshack/videos)
- **Issues**: [GitHub Issues](https://github.com/yourusername/rex-townhall/issues)

### Common Issues

**Problem**: "You have too many jobs already!"
- **Solution**: This means you've reached the multi-job limit configured in rsg-multijob. Resign from a job first or ask an administrator to increase your job limit.

**Problem**: NPCs not spawning
- **Solution**: Check that NPC models are valid for RedM and coordinates are correct.

**Problem**: Jobs not appearing in menu
- **Solution**: Verify that job names in config.lua match those in rsg-core/shared/jobs.lua.

**Problem**: Cooldown not working
- **Solution**: The cooldown is client-side and resets on resource restart or player reconnect.

**Problem**: Players not receiving starting items
- **Solution**: Check the server console for errors. Items must exist in `rsg-core/shared/items.lua`. The script will skip invalid items and log an error.

**Problem**: Players bypassing job limits
- **Solution**: This should not be possible as of v2.1.1. Both client and server validate against rsg-multijob limits. If this occurs, report it as a bug.

## 💝 Support the Developer

### Store
- **Tebex Store**: [RexShack Gaming Store](https://rexshackgaming.tebex.io/)

### Free Resources
Check out more free resources on our GitHub and Discord!

## 👏 Credits

**Author**: RexShackGaming  
**Version**: 2.1.2  
**Framework**: RSG-Core  
**Game**: RedM (Red Dead Redemption 2)

### Special Thanks
- RSG Framework team
- ox_lib developers
- RedM community

## 📄 License

This project is provided as-is for use with RedM servers. Please respect the author's work and provide credit when using or modifying this resource.

---

**Made with ❤️ by RexShackGaming**  
*For the RedM community*
