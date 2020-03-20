# Pylogflow

Python library for Dialogflow webhook. / Biblioteca de python para webhooks en Dialogflow 

## Content Table / Tabla de contenido 

- Language / Idioma
  - English
    - Description
    - Requirements
    - Installation
    - Usage
    - Support me
  - Español  
    - Descripcion
    - Requisitos
    - Instalación
    - Uso
    - Apoyar

## English documentation

### Description

The purpouse of pylogflow is allow people to do chatbots easier without knowing how dialogflow works, and let proggrammer just focus on the process that they want to do (I think that their javascript library is complex in further level and i love python).

This library can manage responses(messages) and intents in this version, in next versions i'll add context management and easy parameters management (just i make it for Actions on Google, i have to do for generic responses).

### Requirements

- Python 3
- Python pip

### Installation

`pip install pylogflow`

### Usage

#### Before start

To start using pylogflow you must know how to setup a web server with python, read this if you dont know how to start

In examples below i'll use flask that is a web framework on python, it's really easy to use, you can learn more about it here.

#### Intent Handling

If you have some intents in your Dialogflow agent that points to webhook you have to manage them, so pylogflow includes intent handling that makes it easy.

Suppose that you have an intent named "Test" and you want to handle in webhook, you will do something like this.

```Python
from pylogflow import IntentMap
from flask import request, jsonify

app = flask.Flask(__name__)

# We will receive POST request when webhook is called
@app.route('/', methods=['POST'])
def webhook():
    # Send request as a json dictionary, result must be a dictionary
    result = intentMap.execute_intent(request.json)
    # Parse result into json response format
    return jsonify(result)

# Intent 'Test' function
# They must receive a request in dictionary format.
def intentTest(req):
    # Simpliest response to dialogflow
    return {'fulfillmentText': 'This is a response from webhook'}

# Intent Map for handle intents
intentMap = IntentMap()
# add method appends new intent with their function
# first parameter must be the Dialogflow intent name, second is the function name
intentMap.add('Test', intentTest)

# flask run configuration
if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)

```

The code above it's the simpliest way to handle an intent in webhook, i just send request in dictionary format to intentMap with 'execute_intent' function, and then get response with 'get_response' function and return as json with jsonify feature from flask, the rest of the code is to make a function that build response and then return it, below of all must create intentMap object and then add respective intent with their respective name(must be same as we set in Dialogflow).

#### Responses (messages)

There are some responses that we can send to dilogflow, this version can make Generic responses and Google Assistant responses.

##### Agent Responses

Agent class can handle generic text and card responses( i don't recomend card responses for now, i'm searching why Google Assistant doesn't handle that cards and get error, if you want to add cards for Google, use GoogleResponse instead).

Below i'll use same example to explain agent usage, we have to focus on 'intentTest' function, and remember to import Agent.

```Python
from pylogflow import IntentMap, Agent
from flask import request, jsonify

app = flask.Flask(__name__)

# Main Webhook route
@app.route('/', methods=['POST'])
def webhook():
    result = intentMap.execute_intent(request.json)
    return jsonify(result)

def intentTest(req):
    # We create an Agent object
    agent = Agent()
    # We can add messages
    agent.add_message("Hi")
    agent.add_message("How are you?")
    # Or cards (just one recommended)
    agent.add_card("title","subtitle","imageURL","buttonName", "buttonURL")
    # Then, we return the response in dictionary format
    return agent.get_response()

# Intent Map for handle intents
intentMap = IntentMap()
intentMap.add('Test', intentTest)

# flask run configuration
if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)

```

In this case, in 'intentTest' function we have to create agent, after that, we can add messages, cards and then send response back, it's really easy.

##### Google Asisstant



#### Advice

My english is not good at all, if i got an error correct me please.

