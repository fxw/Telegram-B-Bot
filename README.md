# Telegram Kraken Bot
Python 3 bot to trade on Kraken via Telegram messanger

<p align="center">
  <img src="demo.gif" alt="Demo GIF of bot">
</p>

## Overview
This Python script is a polling (not [webhook](https://github.com/python-telegram-bot/python-telegram-bot/wiki/Webhooks)) based Telegram bot. It can trade crypto-currencies on the [Kraken](http://kraken.com) marketplace and has a user friendly interface (custom keyboards with buttons).

### Features
- Bound to a specific Telegram user - only that user can use the bot
- No need to login to Kraken - start trading immediately, always
- Integrated update mechanism - to latest version on GitHub
- Notifies you once order is closed and trade successfully executed
- Fully usable with buttons - no need to enter commands manually
- Supports all currencies available on Kraken (configurable)
- Change bot settings via bot
- Following Kraken functionality is implemented
    - Create a buy / sell order (type _limit_)
    - Lookup last trade price for currencies
    - Show all your assets
    - Current market value of assets (one or all)
    - Show / close open orders
    - Sell all assets for current market price
    - Deposit & withdraw
    - Show real-time charts
    - List history of closed orders

## Configuration
Before starting up the bot you have to take care of some settings. You need to edit two files:

### config.json
This file holds the configuration for your bot. You have to at least edit the values for __user_id__ and __bot_token__.

- __user_id__: Your Telegram user ID. The bot will only reply to messages from this user. If you don't know your user ID, send a message to Telegram bot `userinfobot` and he will reply your ID
- __bot_token__: The token that identifies your bot. You will get this from Telegram bot `BotFather` when you create your bot. If you don't know how to register your bot, follow [these instructions](https://core.telegram.org/bots#3-how-do-i-create-a-bot)
- __trade\_to\_currency__: The base currency you are using (for example `EUR` or `USD`)
- __check_trade__: If `true` then every order (already existing or newly created) will be monitored by a job and if the status changes to `closed` (which means that the trade was successfully executed) you will be notified by a message
- __check\_trade\_time__: Time in seconds to check for order status change (see setting `check_trade`)
- __update_url__: URL to the latest GitHub version of the script. This is needed for the update functionality. Per default this points to my repository and if you don't have your own repo with some changes then you can use the default value
- __update_hash__: Hash of the latest version of the script. __Please don't change this__. Will be set automatically after updating
- __update_check__: (_currently not used_) If `true`, then periodic update-checks (see also option `update_time` for timespan) are performed. If there is a bot-update available then you will be notified by a message
- __update_time__: (_currently not used_) Time in seconds to check for bot-updates. `update_check` has to be enabled
- __send_error__: If `true`, then all errors that happen will trigger a message to the user. If `false`, only the important errors will be send and timeout errors of background jobs will not be send
- __show\_access\_denied__: If `true`, the owner and the user who tries to access the bot will both be notified. If `false`, no one will be notified. Set to `false` if you get spammed with `Access denied` messages
- __used_coins__: List of currencies to use in the bot. You can choose from all available currencies at Kraken: `XBT`, `BCH`, `DASH`, `EOS`, `ETC`, `ETH`, `GNO`, `ICN`, `LTC`, `MLN`, `REP`, `USDT`, `XDG`, `XLM`, `XMR`, `XRP`, `ZEC`
- __coin_charts__: Dictionary of all available currencies with their corresponding chart URLs. If you want to add new ones, get the plain URL of the chart, save it with [tinyurl.com](http://tinyurl.com) and add the new URL to the config file
- __log\_to\_file__: If `true`, debug-output to console will be saved in file `debug.log`

### kraken.key
This file holds two keys that are necessary in order to communicate with Kraken. Both keys have to be considered __secret__ and you should be the only one that knows them.

<a name="api-keys"></a>
If you don't know where to get / how to generate the keys:

1. Login to Kraken
2. Click on `Settings`
3. Click on `API`
4. Click on `Generate New Key`
5. Enter `Telegram-Kraken-Bot` in `Key Description`
6. Enter `4` in `Nonce Window` (or just use the default value)
7. Select all available permissions at `Key Permissions`
8. Click on `Generate Key`

When you have your Kraken API keys, open the file `kraken.key` and replace `some_api_key` (first line) with the value of `API Key` and `some_private_key` (second line) with the value of `Private Key`.

## Installation
In order to run the bot you need to execute the script `telegram_kraken_bot.py`. If you don't have any idea where to host it, take a look at [Where to host Telegram Bots](https://github.com/python-telegram-bot/python-telegram-bot/wiki/Where-to-host-Telegram-Bots). __Since you have to provide sensitive data (Kraken API keys) to use the bot, i would only host this script on a server that you own__. You can also run the script locally on your computer for testing purposes.

### Prerequisites
##### Python version
You have to use __Python 3.6__ to execute the script (because of enum method `auto()`). If you would like to use Python 3.4 or 3.5, you have to remove `auto` from imports and set the values in `WorkflowEnum` and `KeyboardEnum` yourself. Python 2.x is __not__ supported.

##### Installing needed modules from `requirements.txt`
Install a set of module-versions that is known to work together for sure (__highly recommended__):
```shell
pip3.6 install -r requirements.txt
```

##### Install newest versions of needed modules
If you want to install the newest version of the needed modules, do the following:
```shell
pip3.6 install python-telegram-bot -U
pip3.6 install krakenex -U
pip3.6 install requests -U
```

### Starting up
To start the script, execute
```shell
python3.6 telegram_kraken_bot.py &
```

## Usage
If you configured the bot correctly and execute the script, you should get a welcome message from the bot along with the information if you are using the latest version. There should also be a custom keyboard that shows you all the available commands. Click on a button to execute the command or type the command in manually.

### Available commands
##### Related to Kraken
- `/trade`: Start a workflow that leads to the creation of a new order of type _limit_ (buy or sell)
- `/orders`: Show all open orders (buy and sell) and close a specific one or all if desired
- `/balance`: Show all assets and the volume available to trade if open orders exist already
- `/price`: Return last trade price for the selected crypto-currency
- `/value`: Show current market value of chosen currency or all assets
- `/chart`: Show a trading chart for the chosen currency
- `/history`: Show history of closed trades
- `/funding`: Deposit or withdraw (only to wallet, not SEPA) funds

##### Related to bot
The following commands are available as sub-commands for command `/bot`

- `/update`: Update the bot to the latest version on GitHub
- `/restart`: Restart the bot
- `/shutdown`: Shutdown the bot
- `/settings`: Show and change bot settings

## Development
I know that it is unusual to have the whole source code in just one file. At some point i should have been switching to object orientation and multiple files but i kind of like the idea to have it all in just one file and object orientation would only blow up the code. This also makes the `/update` command much simpler :)

### Todo
##### Priority 1
- [x] Add command `/history` that shows executed trades
- [x] Add command `/chart` to show TradingView Chart Widget website
- [x] Add command `/funding` to deposit / withdraw funds
- [ ] Add command `/alert` to be notified once a specified price is reached
- [x] Add possibility to sell __all__ assets immediately to current market value
- [x] Enable to trade every currency that Kraken supports
- [x] Add possibility to change settings via bot

##### Priority 2
- [ ] Optimize code to call Kraken API less often
- [ ] Automatically check for updates (configurable timespan & changelog)
- [ ] Create webhook-version of this bot

##### Priority 3
- [ ] Add command `/stats` that shows statistics
- [ ] Closed order notifications: Show gain / loss if association between orders possible

## Troubleshooting
In case you experience any issues, please take a look at this section to check if it is described here. If not, create an [issue on GitHub](https://github.com/Endogen/Telegram-Kraken-Bot/issues/new)

- __Error `Invalid nonce`__: It might happen pretty often that Kraken replies with this error. If you want to understand what a nonce is, [read the Wikipedia article](https://en.wikipedia.org/wiki/Cryptographic_nonce). This error happens mostly if you use different Telegram clients. Maybe you issued some commands on your laptop and then switched to your smartphone? That would be a typical scenario where this might happen. Or you didn't use the bot for a long time. To resolve it, just execute the command again. It should work the second time - meaning you press the keyboard button again. Unfortunately there is not much i can do. The correct behavior would be to have one Kraken API key-pair for one device (one for your smartphone and one for your laptop). Unfortunately there is no way to identify the client. You can play around with the nonce value in your Kraken account (take a look at the [settings for the generated key-pair](#api-keys)). If you are really annoyed by this then here is what you could try: Create some key-pairs (5 might do it) and then, before you call the Kraken API, randomly choose one of the keys and use it till the next Kraken API call is made.
- __Error `Service unavailable`__: If you get this error then because Kraken fucked up again. That happens regularly. It means that their API servers are not available or the performance is degraded because the load on the servers is too high. Nothing you can do here - try again later. If you want to have details on the API server performance, go to [Kraken Status](https://status.kraken.com).

## Disclaimer
I use this bot personally to trade on Kraken so i guess it's kind of stable but __if you use it, then you are doing this on your own responsibility__ !!! I can not be made responsible for lost coins or other stuff that might happen due to some fuckup within the code. Use at your own risk!

## Donating
If you find __Telegram-Kraken-Bot__ suitable for your needs or maybe even made some money because of it, please consider donating whatever amount you like to this Bitcoin wallet:
```
1A8eQ7hA1xUH7ymoXvgRbGzRpPekSxR3DV
```
