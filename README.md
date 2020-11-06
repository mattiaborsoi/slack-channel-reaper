# Autoarchive Unused Slack Channels

## Requirements

- python3
- Install requirements.txt ( `pip3 install -r requirements.txt` )
- An [OAuth token](https://api.slack.com/docs/oauth) from a [Slack app](https://api.slack.com/slack-apps) on your workspace that has the following permission scopes:

#### Bot Token Scopes

- `channels:read`
- `channels:manage`
- `channels:history`
- `channels:join`
- `chat:write`
- `groups:read`

#### User Token Scopes

- `channels:history`

## Example Usages

`BOT_SLACK_TOKEN` must be exposed as environment variables before running your script. By default, the script will do a `DRY_RUN`. To perform a non-dry run, specify `DRY_RUN=false` as an environment variable as well. See sample usages below.
```
# Run the script in dry run archive mode...This will output a list of channels that will be archived.
BOT_SLACK_TOKEN=<BOT_SLACK_TOKEN> python slack_autoarchive.py

# Run the script in active archive mode...THIS WILL ARCHIVE CHANNELS!
DRY_RUN=false BOT_SLACK_TOKEN=<BOT_SLACK_TOKEN> python slack_autoarchive.py
```

As an alternative to passing in environment variables through the command line, add a `.env` file to your project root:

```
BOT_SLACK_TOKEN=xoxb-
ADMIN_CHANNEL=
```

However, it may be best to always pass the `DRY_RUN` variable through the command line to avoid accidental archives.

NOTE: On the first run with `DRY_RUN=false` it will only add the bot to every channel - only on a subsequent run will channels be archived.

## How can I exempt my channel from being archived?

You can add the string 'noarchive' to your channel purpose or topic. (There is also a whitelist file or env variable if you prefer.)

## What Channels Will Be Archived

A channel will be archived by this script is it doesn't meet any of the following criteria:

- Has non-bot messages in the past 60 days.
- Is whitelisted. A channel is considered to be whitelisted if the channel name contains keywords in the WHITELIST_KEYWORDS environment variable. Multiple keywords can be provided, separated by comma.

## What Happens When A Channel Is Archived By This Script

- *Don't panic! It can be unarchived by following [these instructions](https://slack.com/intl/en-ca/help/articles/201563847#unarchive-a-channel) However all previous members would be kicked out of the channel and not be automatically invited back.
- A message will be dropped into the channel saying the channel is being auto archived because of low activity
- You can always whitelist a channel if it indeed needs to be kept despite meeting the auto-archive criteria.

## Custom Archive Messages

Just before a channel is archived, a message will be sent with information about the archive process. The default message is:

  This channel has had no activity for %s days. It is being auto-archived. If you feel this is a mistake you can <https://get.slack.help/hc/en-us/articles/201563847-Archive-a-channel#unarchive-a-channel|unarchive this channel> to bring it back at any point.'

To provide a custom message, simply edit `templates.json`.

## Known Issues

- Since slack doesn't have a batch API, we have to hit the api a couple times for each channel. This makes the performance of this script slow. If you have thousands of channels (which some people do), get some coffee and be patient.

## Docker

- First build the docker image (in the root of the project)

`docker build --tag autoarchive .`
- run the container (dryrun is set to true by default)

`docker run autoarchive`
- if your ready to archive run

`docker run -e DRY_RUN=false autoarchive`
