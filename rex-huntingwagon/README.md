# 🎯 Rex Hunting Wagon

**A comprehensive hunting wagon system for RedM RSG Framework**

[![Discord](https://img.shields.io/badge/Discord-Join%20Us-7289DA?style=flat&logo=discord)](https://discord.gg/YUV7ebzkqs)
[![YouTube](https://img.shields.io/badge/YouTube-Subscribe-red?style=flat&logo=youtube)](https://www.youtube.com/@rexshack/videos)
[![Tebex](https://img.shields.io/badge/Tebex-Support-00B8A9?style=flat)](https://rexshackgaming.tebex.io/)

---

## 📋 Features

### 🚚 Hunting Wagon System
- **Purchase & Own Multiple Wagons** - Buy and manage multiple hunting wagons across different locations
- **Persistent Storage** - Wagons remain stored at hunting camps even after server restarts
- **Location-Based Management** - Store and retrieve wagons from any of the 6 hunting locations
- **Wagon Tracking** - GPS blip displays your active wagon's location on the map
- **Damage System** - Wagons can be damaged and require repairs at wagon store
- **Sell Wagons** - Sell unwanted wagons for a percentage of the original price

### 🦌 Animal Storage
- **Store Up to 10 Animals** - Transport multiple carcasses in your hunting wagon
- **200+ Supported Animals** - Complete animal database with proper identification
- **Visual Tarp Animation** - Dynamic tarp that adjusts based on cargo load
- **Animal Count Display** - Real-time tracking of stored animals
- **Easy Retrieval** - Drop animals from your wagon whenever needed
- **Proximity Detection** - Smart targeting system for nearby animal carcasses

### 📦 Inventory System
- **Built-in Storage** - 20 inventory slots with 400kg weight capacity
- **Standard Inventory UI** - Familiar interface for item management
- **Persistent Items** - All items remain in wagon storage between sessions

### 🏪 Hunting Shop
- **Weapon & Supplies** - Purchase hunting rifles, ammunition, and equipment
- **Multiple Locations** - 6 hunting stores across the map:
  - Valentine Hunting Store
  - Annesburg Hunting Store
  - Saint Denis Hunting Store
  - Blackwater Hunting Store
  - MacFarlane Hunting Store
  - Tumbleweed Hunting Store
- **Dynamic Stock System** - Optional persistent stock tracking across server restarts

### 🎨 User Experience
- **ox_lib Integration** - Modern UI with context menus and progress bars
- **ox_target Support** - Intuitive interaction system
- **Localization Ready** - Multi-language support (English included)
- **Blip Customization** - Configurable map markers for hunting stores
- **NPC Vendors** - Immersive hunting camp traders at each location

### 📊 Advanced Features
- **Discord Logging** - Optional webhook notifications for all transactions:
  - Wagon purchases/sales
  - Animal storage/retrieval
  - Wagon repairs
  - Wagon spawn/store events
- **Version Checker** - Automatic update notifications
- **Extensive Configuration** - Customize prices, storage limits, and behavior
- **Debug Mode** - Built-in debugging tools for development

---

## 📦 Dependencies

Ensure these resources are installed and started **before** rex-huntingwagon:

- [rsg-core](https://github.com/Rexshack-RedM/rsg-core) - RedM framework
- [ox_lib](https://github.com/Rexshack-RedM/ox_lib) - UI and utility library
- [ox_target](https://github.com/Rexshack-RedM/ox_target) - Targeting system
- [oxmysql](https://github.com/CommunityOx/oxmysql/releases/latest/download/oxmysql.zip) - MySQL wrapper

---

## 🔧 Installation

### Step 1: Download & Extract
1. Download the latest release from your purchased source
2. Extract the `rex-huntingwagon` folder
3. Place it in your server's `resources` directory

### Step 2: Database Setup
1. Locate the `rex-huntingwagon.sql` file in the resource folder
2. Import the SQL file into your database using:
   - **HeidiSQL**: Tools → Import SQL file
   - **phpMyAdmin**: Import tab → Choose file
   - **MySQL Command Line**: `mysql -u username -p database_name < rex-huntingwagon.sql`

### Step 3: Configuration
1. Navigate to `rex-huntingwagon/shared/config.lua`
2. Customize settings to fit your server:

```lua
-- Basic Settings
Config.WagonPrice = 1000              -- Purchase price
Config.WagonSellRate = 0.75            -- Sell for 75% of purchase price
Config.TotalAnimalsStored = 10         -- Maximum animals
Config.StorageMaxWeight = 400000       -- Inventory weight limit
Config.StorageMaxSlots = 20            -- Inventory slots

-- Optional: Discord Logging
Config.EnableDiscordLogs = true
Config.DiscordWebhook = 'YOUR_WEBHOOK_URL_HERE'
```

### Step 4: Server Configuration
1. Open your `server.cfg` file
2. Add the following line **after** your dependencies:

```cfg
# Dependencies
ensure rsg-core
ensure ox_lib
ensure ox_target
ensure oxmysql

# Rex Hunting Wagon
ensure rex-huntingwagon
```

### Step 5: Start Your Server
1. Start or restart your RedM server
2. Check console for any errors
3. Visit any hunting store location to test

---

## 🎮 Usage

### Buying a Wagon
1. Visit any hunting store (marked with blip on map)
2. Interact with the NPC vendor
3. Select "Buy Hunting Wagon" from the menu
4. Confirm purchase (costs $1000 by default)

### Spawning Your Wagon
1. Return to any hunting store where your wagon is stored
2. Select "Get Wagon" from the menu
3. Choose your wagon from the list
4. Wagon spawns at the designated location

### Storing Animals
1. Hunt and kill an animal and pickup
2. Approach your hunting wagon
3. Target the wagon and select "Store Animal"
4. Animal carcass will be stored

### Accessing Wagon Inventory
1. Target your hunting wagon
2. Select "Open Inventory" from the menu
3. Store/retrieve items as needed

### Storing Your Wagon
1. Target your hunting wagon
2. Select "Store Wagon" from the menu
3. Wagon is stored at the nearest hunting camp

---

## ⚙️ Configuration Options

| Setting | Default | Description |
|---------|---------|-------------|
| `WagonPrice` | 1000 | Cost to purchase a wagon |
| `WagonSellRate` | 0.75 | Percentage refunded when selling |
| `TotalAnimalsStored` | 10 | Maximum animals in wagon |
| `StorageMaxWeight` | 400000 | Maximum inventory weight |
| `StorageMaxSlots` | 20 | Number of inventory slots |
| `WagonFixRate` | 0.10 | Repair cost multiplier |
| `TargetDistance` | 5.0 | Interaction range |
| `AllowMultipleWagons` | true | Allow players to own multiple wagons |
| `ShowAnimalCount` | true | Display animal count in notifications |
| `EnableDiscordLogs` | true | Enable webhook logging |

---

## 🗺️ Hunting Store Locations

1. **Valentine** - (-263.86, 631.66, 113.45)
2. **Annesburg** - (2908.91, 1451.71, 57.60)
3. **Saint Denis** - (2509.98, -1465.73, 46.33)
4. **Blackwater** - (-855.75, -1363.25, 43.63)
5. **MacFarlane Ranch** - (-2493.83, -2418.77, 60.58)
6. **Tumbleweed** - (-5516.57, -3061.30, -2.19)

---

## 🐛 Troubleshooting

### Wagon Not Spawning
- Ensure all dependencies are started
- Check server console for errors
- Verify database table was created
- Confirm wagon is stored at current location

### Animals Not Storing
- Make sure you're within range of the wagon
- Check that wagon isn't at max capacity (10 animals)
- Verify the animal is on the supported list

### Database Errors
- Ensure SQL file was imported correctly
- Check database credentials in server.cfg
- Verify oxmysql is properly configured

---

## 🤝 Support

- **Discord**: [Join our community](https://discord.gg/YUV7ebzkqs)
- **YouTube**: [Video tutorials](https://www.youtube.com/@rexshack/videos)
- **Tebex**: [Premium resources](https://rexshackgaming.tebex.io/)

---

## 📄 Version

**Current Version**: 2.1.0

---

## 📝 License

© RexShackGaming - All Rights Reserved

This resource is protected and may not be redistributed without permission.
