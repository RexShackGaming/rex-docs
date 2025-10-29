# rex-farming

A comprehensive farming system for RedM that allows players to plant, grow, and harvest crops with a dynamic growth system including thirst, hunger, and quality mechanics.

## Features

### 🌱 Plant Growing System
- **8 Plant Types**: Corn, Sugarcane, Cotton, Carrot, Potato, Tomato, Wheat, and Weed
- **Dynamic Growth**: Plants grow over time with configurable growth cycles
- **Quality System**: Harvest yields vary based on plant quality (Poor, Good, Excellent)
- **Thirst & Hunger Mechanics**: Plants require water and fertilizer to thrive
- **Weather Effects**: Rain, showers, and drizzle naturally water your plants
- **Plant Ownership**: Optional owner-only harvesting

### 💧 Water Collection System
- Collect water from wells and water troughs
- Fill empty buckets to water your plants
- Multiple compatible water prop types

### 💩 Fertilizer System
- Collect fertilizer from animal droppings
- Feed plants to boost growth rate
- Increases growth by 1-3 points per cycle

### 📍 Flexible Planting
- **Plant Anywhere Mode**: Grow crops anywhere on the map
- **Designated Farm Zones**: Optional farming areas with vegetation clearing
- **Town Restrictions**: Prevent planting in town zones
- **Custom Placement**: Rotate and position plants before planting
- **Max Plant Limits**: Configurable per-player plant cap (default: 100)

### 🗺️ Custom Farming Zones
- Pre-configured Saint Denis field
- Automatic vegetation removal in farm zones
- Visual blip markers on map
- PolyZone integration for precise boundaries

### ⚙️ Highly Configurable
- Adjustable growth rates and cycles
- Customizable reward quantities per quality tier
- Configurable thirst/hunger systems
- Dead plant auto-removal (default: 3 days)
- Debug mode for development

## Installation

### Prerequisites
- **RedM Server** with RDR3 support
- **Required Dependencies**:
  - [rsg-core](https://github.com/Rexshack-RedM/rsg-core)
  - [ox_lib](https://github.com/Rexshack-RedM/ox_lib)
  - [PolyZone](https://github.com/mkafrin/PolyZone)
  - [oxmysql](https://github.com/CommunityOx/oxmysql/releases/latest/download/oxmysql.zip)

### Installation Steps

1. **Download the Resource**
   ```bash
   cd resources
   git clone https://github.com/yourusername/rex-farming.git
   ```

2. **Database Setup**
   - Import the SQL file into your database:
   ```bash
   mysql -u username -p database_name < rex-farming/installation/rex-farming.sql
   ```
   - If migrating from an older version, also run:
   ```bash
   mysql -u username -p database_name < rex-farming/installation/migration_plantid_varchar.sql
   ```

3. **Add Items to Your Items Database**
   - Import the items from `installation/shared_items.lua` into your item configuration
   - Items include: plant seeds, water buckets, fertilizer, and harvested crops

4. **Add Images to Inventory**
   - Copy images from the resource to `rsg-inventory/html/images`

5. **Configure Server**
   - Add to your `server.cfg`:
   ```cfg
   ensure ox_lib
   ensure PolyZone
   ensure oxmysql
   ensure rsg-core
   ensure rex-farming
   ```

6. **Configure Settings**
   - Edit `config.lua` to customize:
     - Plant types and rewards
     - Growth mechanics
     - Farming zones
     - Water/fertilizer locations
     - Planting restrictions

7. **Restart Server**
   ```bash
   restart rex-farming
   ```

## Configuration

### Key Settings in `config.lua`:

- `Config.GrowAnywhere` - Allow planting anywhere (true) or only in zones (false)
- `Config.RestrictTownPlanting` - Prevent planting in towns
- `Config.MaxPlantCount` - Maximum plants per player (default: 100)
- `Config.OwnerHarvestOnly` - Only plant owner can harvest (default: true)
- `Config.DeadPlantTime` - Time until dead plants are removed (default: 3 days)
- `Config.FarmingCronJob` - Growth cycle interval (default: every 1 minute)

### Customizing Plants:
Each plant in `Config.FarmItems` can have custom rewards:
- `poorRewardMin/Max` - Low quality harvest
- `goodRewardMin/Max` - Medium quality harvest
- `exellentRewardMin/Max` - High quality harvest

## Usage

1. **Obtain Seeds**: Get plant seeds from shops or other players
2. **Find Water Source**: Locate a well or water trough to fill buckets
3. **Optional - Get Fertilizer**: Collect from animal droppings for faster growth
4. **Plant Seeds**: Use seed item and place it in the world
5. **Maintain Plants**: Water and fertilize regularly
6. **Harvest**: Wait for full growth and harvest for rewards

## Support

- **Discord**: https://discord.gg/YUV7ebzkqs
- **YouTube**: https://www.youtube.com/@rexshack/videos
- **Tebex**: https://rexshackgaming.tebex.io/

## License

This resource is provided as-is for RedM servers.
