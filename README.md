# Cognitive Car Dashboard Application powered by Conversation and NLU

This application demonstrates how to integrate [Natural Language Understanding](https://www.ibm.com/watson/developercloud/natural-language-understanding.html) (NLU) service with [Watson Conversation](https://www.ibm.com/watson/developercloud/conversation.html) service to extract generic entities and pass those to the Conversation service.
This application is an extension of the [Conversation Simple](https://github.com/watson-developer-cloud/conversation-simple) application where it adds integration to another Watson service, namely NLU as well as a third party API, namely [Weather Underground API](https://www.wunderground.com/weather/api/).

For more information about Conversation, see the [detailed documentation](https://www.ibm.com/watson/developercloud/doc/conversation/index.html).

For more information about NLU, see the [detailed documentation](https://www.ibm.com/watson/developercloud/doc/natural-language-understanding/index.html).


<b>To explore this application, you need to have a [Bluemix](https://bluemix.net) account to provision the Watson services.</b>

## How the app works
The app interface is designed and trained for chatting with a cognitive car. The chat interface is on the left, and the JSON that the JavaScript code receives from the server is on the right. Your questions and commands are run against a small set of sample data trained with intents like these:

    turn_on
    weather
    capabilities

These intents help the system to understand variations of questions and commands that you might submit.

Example commands that can be executed by the Conversation service are:

    turn on windshield wipers
    play music

If you say *"Wipers on"* or *"I want to turn on the windshield wipers"*, the system
understands that in both cases your intent is the same and responds accordingly.

# Getting Started

## Before you begin

1. Ensure that you have a [Bluemix account](https://console.ng.bluemix.net/registration/). While you can do part of this deployment locally, you must still use Bluemix to provision Watson services.

2. Download and install [Cloud Foundry CLI](https://github.com/cloudfoundry/cli#downloads)

3. Download and install [Node.js](http://nodejs.org/) and [npm](https://www.npmjs.com/).

## Running locally
Open a terminal on your machine and execute the following commands:
```sh
1.  mkdir convapp
2.  cd convapp
3.  git clone https://github.com/joe4k/conversation-nlu.git
4.  cd conversation-nlu
5.  npm Install  ==> installs node packages defined in package.json
6.  cp .env.example .env  ==> we define service credentials in .env file
7.  edit .env file and copy/paste the credentials for NLU, Conversation and Weather services (you will create these next).
```

To create Tone Analyzer (TA), Natural Language Understanding (NLU) and Conversation service credentials, on your terminal window, execute the following:
```
1.  cf login   ==>  connects you to your bluemix account
  - API endpoint: https://api.ng.bluemix.net
  - username: your_bluemix_username
  - password:   your_bluemix_password

2.  cf create-service conversation free convapp-conv-service
 ==> create conversation using free plan and call it convapp-conv-service

3.  cf create-service-key convapp-conv-service svcKey

4.  cf service-key convapp-conv-service svcKey
 ==> returns username and password credentials for conversation service

5.  Copy the Conversation username and password to the .env file
CONVERSATION_USERNAME=username
CONVERSATION_PASSWORD=password

6.  cf create-service natural-language-understanding free convapp-nlu-service
 ==> create NLU service using free plan and call it convapp-nlu-service

7.  cf create-service-key convapp-nlu-service svcKey

8.  cf service-key convapp-nlu-service svcKey
 ==> returns username and password for NLU service

9.  Copy NLU username and password to the .env file
NATURAL_LANGUAGE_UNDERSTANDING_USERNAME=username
NATURAL_LANGUAGE_UNDERSTANDING_PASSWORD =password

10.  cf create-service tone_analyzer lite convapp-ta-service
 ==> create Tone Analyzer service using lite plan and call it convapp-ta-service

11.  cf create-service-key convapp-ta-service svcKey

12.  cf service-key convapp-ta-service svcKey
 ==> returns username and password for Tone Analyzer service

13.  Copy Tone Analyzer username and password to the .env file
TONE_ANALYZER_USERNAME=username
TONE_ANALYZER_PASSWORD =password

14.  cf create-service personality_insights lite convapp-pi-service
 ==> create Personality Insights service using lite plan and call it convapp-pi-service

15.  cf create-service-key convapp-pi-service svcKey

16.  cf service-key convapp-pi-service svcKey
 ==> returns username and password for Personality Insights service

17.  Copy Personality Insights username and password to the .env file
PERSONALITY_INSIGHTS_USERNAME=username
PERSONALITY_INSIGHTS_PASSWORD=password

```

To computer personality profile and consumption preferances for a user, we need as input text written 
by that user. A common approach to collecting such text is to leverage Twitter to obtain tweets by the 
user. To do so, you need to have a Twitter developer account and you need to create an app which would provide
you with the credentials needed to execute Twitter APIs:
* Create Twitter user account if you don't have one (https://twitter.com/signup)
* Go to https://apps.twitter.com/ and sign in with your user account information.
* Once logged in, create a new app by clicking on "Create New App" button.
* Fill out required information, agree to the terms and click "Create your Twitter application".
* Click on "Keys and Access Tokens" tab and copy the required Twitter credentials. You will need four value, 
Consumer Key (API key), Consumer Secret (API Secret), Access Token, and Access Token Secret.
* Copy these values to the .env file
TWITTER_CONSUMER_KEY=YOUR_TWITTER_CONSUMER_KEY
TWITTER_CONSUMER_SECRET=YOUR_TWITTER_CONSUMER_SECRET
TWITTER_ACCESS_TOKEN_KEY=YOUR_TWITTER_ACCESS_TOKEN
TWITTER_ACCESS_TOKEN_SECRET=YOUR_TWITTER_ACCESS_TOKEN_SECRET

To get the weather, we will rely on the [Weather Underground API](https://www.wunderground.com/weather/api/). To use the weather underground api, you need to [sign up for an apikey](https://www.wunderground.com/weather/api/). Once you get the key, edit .env file and copy the weather api key to .env file.

WEATHER_API_KEY=weatherapikey

The last piece of information we need is the WORKSPACE_ID. 
To get this, we need to create a workspace in our conversation service and build a conversation which involves defining intents, entities and building a dialog to orchestrate the interaction with the user. 
To do so:
  * Point your browser to http://bluemix.net
  * Login with your Bluemix credentials
  * Find your conversation service with the name convapp-conv-service. Click to open the page for that service.
  * Find the Launch button and click it to launch the tooling for the conversation service.
  * Click Import to import a json file which defines the conversation workspace ==> Choose file convapp/conversation-nlu/training/car_workspace_nlu_ta_pi.json
  * This imports intents, entities, and the dialog  for this conversation into a workspace called NLU_Car_Dashboard.
  * Click the Actions menu (3 vertical dots in top right of workspace tile) to View details.
  * Copy Workspace ID, edit .env file and add workspace id
     WORKSPACE_ID=workspaceID

Now you’re ready to run the application. On the terminal command line, execute this command.
``` sh
node server.js
Point your browser to http://localhost:3000
Experiment with conversation application
   Note the conversation starts out asking how you're feeling and based on your response, it understand the emotional tone and responds accordingly.
   Ask things like: “What is the weather in Austin, TX”
```

To push your application to Bluemix:
``` sh
  edit manifest.yml and change name to a unique name (for example, convapp-conv-ta-pi-jk)
  cf push
  point your browser to http://convapp-conv-nlu-ta-pi-jk.mybluemix.net
  Experiment with conversation application
    Note that depending on your response for "How are you feeling" results in a variation in how the app
    responds back. This is becaue your response goes through Tone Analyzer which extracts the tone and
    based on that different responses are offered.
    To see how NLU and the Weather APIs are used, ask things like: “What is the weather in Austin, TX”
```

# License

  This sample code is licensed under Apache 2.0.
  Full license text is available in [LICENSE](LICENSE).
