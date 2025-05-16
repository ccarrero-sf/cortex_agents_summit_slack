# Building Cortex Agents Hands-On Lab Slack Integration

This is the continuation of the [Building Cortex Agents Hands-On Lab](https://github.com/ccarrero-sf/cortex_agents_summit/tree/main)

Build that lab first in order to have all the tools needed for the Cortex Agent to work. This guide will show how to use that Cortex Agent via Slack

## 1. Create your own Slack Channel

We recommend for testing you create your own private slack channel:

[Use this link to Create it](https://slack.com/get-started#create)

![image](img/11_slack_create.png)

Get you email handy as you will receive a confirmation code.

Follow the steps to:

- Enter the name of your team (My Bikes & Ski)
- Add your name
- No need to invite anybody for now (we are just testing, click on Skip this step)
- Enter what team is working on and this will create a new channel (sales research)
- Start with the Limited Free Version

With this you should be able to have your first Slack Channel!

![image](img/12_slack_channel.png)

## 2. Create a Slack App.

This is the App that will be calling Cortex Agents API.

Click on [Create Apps](https://api.slack.com/apps?new_app=1)

Click on Create New App -> Select From scratch

Give the app a name and select the workspace you just created:

![image](img/13_create_app.png)

You will get in the the slack api configuration of your app with something like this:

![image](img/14_slack_api.png)

Navigate to the **OAuth & Permissions** on the left sidebar and scroll down to the **Bot Token Scopes** section. Click **Add an OAuth Scope**.

![image](img/15_bot_token.png)

For now, we'll just add one scope: **chat:write**. This grants your app the permission to post messages in channels it's a member of.

Scroll up to the top of the **OAuth & Permissions** page and click **Install App** to Workspace. You'll be led through Slack's OAuth UI, where you should allow your app to be installed to your development workspace.

![image](img/16_oauth_tokens.png)

Once you authorize the installation, you'll land on the OAuth & Permissions page and see a Bot User OAuth Access Token.

Keep this token as you will use it later in the .env file for SLACK_BOT_TOKEN

![image](img/17_authorized.png)

Then head over to Basic Information and scroll down under the App Token section and click Generate Token and Scopes to generate an app-level token. Add the connections:write scope to this token and save the generated xapp token, we'll use both these tokens in just a moment.

![image](img/18_generate_token.png)

Give it a name and generate

![image](img/19_app_token.png)

Copy the token generated as we will be using it later under SLACK_APP_TOKEN in your env environment.

![image](img/20_copy_token.png)

Navigate to Socket Mode on the left side menu and toggle to enable.

![image](img/21_socket_mode.png)

Enable Event Subcriptions:

Go to **Event Subcriptions** and toggle the switch labeled **Enabled events**

![image](img/21_enable_events.png)

Navigate to Event Subscriptions on the left sidebar and toggle to enable. Under Subscribe to Bot Events, you can add events for your bot to respond to. There are four events related to messages:

- message.channels listens for messages in public channels that your app is added to
- message.groups listens for messages in 🔒 private channels that your app is added to
- message.im listens for messages in your app's DMs with users
- message.mpim listens for messages in multi-person DMs that your app is added to

If you want your bot to listen to messages from everywhere it is added to, choose all four message events. After you’ve selected the events you want your bot to listen to, click the green Save Changes button.


## Setup your Slack App
### Clone this repository

Open a Terminal and clone this repository:

git clone https://github.com/ccarrero-sf/cortex_agents_summit_slack

### Setup a Virtual Environment to run your Slack App

```code
python3 -m venv .venv  
source .venv/bin/activate  
pip install -r requirements.txt
```
### Configure Key-pair Authentication 

Configure [key-pair authentication](https://docs.snowflake.com/user-guide/key-pair-auth?_fsi=ntnJOu8E&_fsi=ntnJOu8E&_fsi=ntnJOu8E#configuring-key-pair-authentication)

As a summary:

- Generate a Private Key

```code
openssl genrsa 2048 | openssl pkcs8 -topk8 -inform PEM -out rsa_key.p8 -nocrypt
```

This will generate a rsa_key.p8 file with content like:

```code
-----BEGIN PRIVATE KEY-----
MIIEvAIBADANBgkqhkiG9w0BAQEFAASCBKYwggSiAgEAAoIBAQCmX9DxUPoNUbTl
....
XvaIzWzwExamSwO/GbWtzA==
-----END PRIVATE KEY-----
```

- Generate a Public Key

```code
openssl rsa -in rsa_key.p8 -pubout -out rsa_key.pub
```

This generate file rsa_key.pub with a content like:

```code
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEApl/Q8VD6DVG05Rny1Pk6
...
MwIDAQAB
-----END PUBLIC KEY-----
```

- Assign the public key to a Snowflake user¶

Login into your Snowflake account used for this hands-on lab. Open a worksheet and run the following ALTER USER SQL after you copy/paste your PUBLIC KEY:

ALTER USER example_user SET RSA_PUBLIC_KEY='MIIBIjANBgkqh...';

### Define your App Variables

In your folder, create a file called ".env", copy/paste the following content and add your user, account info and slack tokens that you have generated before. Note that you have generated SLACK_APP_TOKEN and SLACK_BOT_TOKEN above.

Your account details are available when you select your account (bottom left) and click on account details:

![image](img/22_account_details.png)


```code
DEMO_DATABASE='CC_CORTEX_AGENTS_SUMMIT'
DEMO_SCHEMA='PUBLIC'
WAREHOUSE='COMPUTE_WH'
DEMO_USER='<your-user-name>'
DEMO_USER_ROLE='ACCOUNTADMIN'
SEMANTIC_MODEL = "@CC_CORTEX_AGENTS_SUMMIT.PUBLIC.SEMANTIC_FILES/semantic.yaml"
CORTEX_SEARCH_BIKES = "CC_CORTEX_AGENTS_SUMMIT.PUBLIC.BIKES_RAG_TOOL"
CORTEX_SEARCH_SKI = "CC_CORTEX_AGENTS_SUMMIT.PUBLIC.SKI_RAG_TOOL"
ACCOUNT='<your-account-identifier>'
HOST='<your-account-identifier>.snowflakecomputing.com'
AGENT_ENDPOINT='https://<your-org>-<your-account>.snowflakecomputing.com/api/v2/cortex/agent:run'
SLACK_APP_TOKEN='<your-slack-app-token>'
SLACK_BOT_TOKEN='<your-slack-bot-token>'
      
 You may NOT edit below values  
RSA_PRIVATE_KEY_PATH='rsa_key.p8'
MODEL = 'claude-3-5-sonnet'
```

## Run your Slack App

In your terminal run 

```code
./slack_bot.sh
```

Start asking questions in your slack channel



