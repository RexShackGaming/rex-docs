# rex-npcdoctor

A comprehensive NPC doctor system for RedM using RSG-Core framework. Features multiple doctor locations, healing/revive services, medical supplies shop, and Discord webhook logging.

## Features

- 🏥 **Multiple Doctor Locations** - Pre-configured doctors in all major towns
- 💊 **Heal & Revive Services** - Configurable pricing for medical services
- 🛍️ **Medical Supplies Shop** - Purchase bandages and other medical items
- 📊 **Discord Webhook Integration** - Log all medical transactions
- 🌍 **Localization Support** - Easy translation via locale files
- 🗺️ **Blips & Markers** - Doctors marked on the map
- 🎮 **ox_lib Integration** - Modern UI with context menus and progress bars

## Dependencies

Required:
- [rsg-core](https://github.com/Rexshack-RedM/rsg-core)
- [ox_lib](https://github.com/overextended/ox_lib)

Optional:
- rsg-medic (for advanced revive integration)

## Installation

1. Download the latest release
2. Extract `rex-npcdoctor` to your resources folder
3. Add `ensure rex-npcdoctor` to your `server.cfg`
4. Configure settings in `shared/config.lua`
5. Restart your server

## Configuration

### Basic Settings

Edit `shared/config.lua` to customize:

```lua
Config.InteractKey = 0xF3830D8E -- Default: [J]
Config.PromptText = "Press [J] to talk to the Doctor"
Config.HealPrice = 5        -- Cost to heal (0 = free)
Config.RevivePrice = 10     -- Cost to revive (0 = free)
Config.ChargeOnServer = true -- Server-side payment validation
Config.MoneyAccount = 'cash'  -- Payment type: 'cash' or 'bank'
```

### Doctor Locations

Pre-configured locations:
- Valentine Doctor Clinic
- Strawberry Doctor Clinic
- Rhodes Doctor Clinic
- Saint Denis Doctor Clinic
- Blackwater Doctor Clinic
- Armadillo Doctor Clinic
- Guarma Doctor Clinic
- Annesburg Doctor Clinic

Add or modify locations in `Config.Doctors`:

```lua
Config.Doctors = {
    {
        model = 'cs_sddoctor_01',
        coords = vec4(x, y, z, heading),
    },
}
```

### Medical Supplies Shop

Configure items in `Config.MedicalShop`:

```lua
Config.MedicalShop = {
    {
        item = 'bandage',
        label = 'Bandage',
        price = 5,
        amount = 1,
        info = {},
        type = 'item',
        icon = 'fa-solid fa-bandage'
    },
}
```

### Discord Webhook Setup

Enable transaction logging to Discord:

1. Create a webhook in your Discord server:
   - Server Settings → Integrations → Webhooks
   - Create Webhook → Copy URL

2. Configure in `shared/config.lua`:

```lua
Config.EnableDiscordLogs = true
Config.DiscordWebhook = 'https://discord.com/api/webhooks/YOUR_WEBHOOK_URL'
Config.DiscordTitle = 'NPC Doctor Log'
Config.DiscordColor = 3447003 -- Blue
Config.DiscordFooter = 'RSG NPC Doctor System'
Config.DiscordAvatar = '' -- Optional: custom avatar URL
```

#### Discord Log Types

**Medical Service Used** (Green)
- Character name and ID
- Service type (Heal/Revive)
- Amount paid
- Payment method

**Medical Item Purchased** (Blue)
- Character name and ID
- Item name and quantity
- Total price
- Payment method

See [DISCORD_SETUP.md](DISCORD_SETUP.md) for detailed instructions.

## Localization

All text is stored in `locales/en.json` for easy translation.

### Available Locale Keys

**Menu & UI:**
- `doctor_menu_title`, `menu_heal_title`, `menu_revive_title`
- `menu_shop_title`, `menu_shop_desc`, `shop_title`
- `healing`, `reviving`, `healed`, `revived`
- `cost`, `free`, `menu_back`

**Notifications:**
- `paid`, `not_enough_money`
- `you_are_dead`, `not_dead`
- `purchased`, `purchase_failed`

**Discord Logs:**
- `discord_medical_service_title`, `discord_medical_service_desc`
- `discord_item_purchase_title`, `discord_item_purchase_desc`
- `discord_field_character`, `discord_field_citizenid`
- `discord_field_amount_paid`, `discord_field_service_type`
- `discord_field_payment_method`, `discord_field_item`
- `discord_field_quantity`, `discord_field_price`

### Adding New Languages

1. Copy `locales/en.json` to `locales/[language].json`
2. Translate all values
3. Players will use the language set in their ox_lib configuration

## Usage

### For Players

1. Approach any doctor NPC (marked with 🏥 on map)
2. Press `[J]` (or configured key) when prompted
3. Select from menu:
   - **Get treated** - Restore health (if alive)
   - **Get revived** - Revive from death
   - **Medical Supplies** - Purchase items

### For Developers

#### Custom Integration Events

Override default heal/revive behavior:

```lua
-- In your resource, listen to these events:
AddEventHandler('rex-npcdoctor:client:heal', function()
    -- Your custom heal logic
end)

AddEventHandler('rex-npcdoctor:client:revive', function()
    -- Your custom revive logic
end)
```

Change event names in config:

```lua
Config.Events = {
    Heal = 'your-resource:heal',
    Revive = 'your-resource:revive',
}
```

#### Server Events

**rex-npcdoctor:server:charge**
- Parameters: `amount` (number), `actionType` (string)
- Charges player for medical services

**rex-npcdoctor:server:purchaseItem**
- Parameters: `item` (table)
- Handles item purchase from medical shop

## Troubleshooting

### Doctor NPCs not spawning
- Ensure ox_lib is loaded before this resource
- Check console for model loading errors
- Verify coordinates are correct for your map

### Payment not working
- Confirm `Config.ChargeOnServer = true`
- Check player has sufficient funds
- Verify `Config.MoneyAccount` matches your economy setup

### Discord logs not appearing
- Verify webhook URL is correct and not deleted
- Ensure `Config.EnableDiscordLogs = true`
- Check webhook channel still exists
- Test webhook with a Discord webhook tester

### Blips not showing
- Ensure resource is started after rsg-core
- Check that blip sprites are valid for RedM

## Support

For issues, suggestions, or contributions:
- Open an issue on GitHub
- Join the RSG Discord community

## Credits

- **Framework:** RSG-Core Team
- **UI Library:** ox_lib by Overextended
- **Script:** rex-npcdoctor

## License

This project is licensed under the GPL-3.0 License.

## Changelog

### Version 2.0.0
- Added Discord webhook integration
- Added medical supplies shop
- Implemented ox_lib menus and progress bars
- Added localization support
- Multiple doctor locations
- Server-side payment validation
- Configurable pricing and events

---

**Note:** This script is designed for the RSG-Core framework on RedM. Ensure all dependencies are up to date for best compatibility.
