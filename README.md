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
    # Simpliest dialogflow response
    return {'fulfillmentText': 'This is a response from webhook'}

# Intent Map for handle intents, this can be inside your request function (webhook)
# or here, in this way you only setup once
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

##### Google Assistant

Google Assistant Works in a similar way, you have to test responses in Gogle Assistant Test Sandbox, because inside dialogflow test tool responses aren't showed, we are working on this issue, but works fine in production.

###### Simple Response

Add simple responses to Google Assistant as follows:

```Python
import flask
from flask import request, jsonify
# Import GoogleResponse
from pylogflow import IntentMap, GoogleResponse, Agent

app = flask.Flask(__name__)

@app.route('/', methods=['POST'])
def home():
    result = intentMap.execute_intent(request.json)
    return jsonify(result)
    
# Intent Multiple simple response
def intentSimpleResponse(req):
    agent = Agent()
    # Instantiate a GoogleResponse Object, you can optionaly 
    # send a boolean if google must expect response, by default
    # is "True"
    gResponse = GoogleResponse()
    # Add simple response, Maximum 2 simple responses
    gResponse.add_simple_response("This is a message")
    gResponse.add_simple_response("This is another message")
    # Add google message to your agent
    agent.add_custom_payload(gResponse.get_response())
    # Return agent in same way
    return agent.get_response()

# Set up intent map
intentMap = IntentMap()
intentMap.add("Simple Response", intentSimpleResponse)

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

###### Card

###### Sugestion Chips

```Python
import flask
from flask import request, jsonify
# Import GoogleResponse
from pylogflow import IntentMap, GoogleResponse, Agent

app = flask.Flask(__name__)

@app.route('/', methods=['POST'])
def home():
    result = intentMap.execute_intent(request.json)
    return jsonify(result)
    
# Intent Multiple simple response
def intentSuggestionChips(req):
    agent = Agent()
    # Instantiate a GoogleResponse Object, you can optionaly 
    # send a boolean if google must expect response, by default
    # is "True"
    gResponse = GoogleResponse()
    # Add suggestion chips, max 5
    gResponse.add_suggestion_chips("flask", "algo")
    # Add google message to your agent
    agent.add_custom_payload(gResponse.get_response())
    return agent.get_response()

# Set up intent map
intentMap = IntentMap()
intentMap.add("Suggestion Chips", intentSuggestionChips)

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```
###### List

```Python
import flask
from flask import request, jsonify
# Import GoogleResponse
from pylogflow import IntentMap, GoogleResponse, Agent

app = flask.Flask(__name__)

@app.route('/', methods=['POST'])
def home():
    result = intentMap.execute_intent(request.json)
    return jsonify(result)
    
# Intent Multiple simple response
def intentList(req):
    agent = Agent()
    gResponse = GoogleResponse()
    gResponse.add_list(
        gResponse.generate_list_item(
            "Elemento 1",
            "ELEMENTO_1",
            ["cosa 1", "objeto 1"],
            "descripcion corta",
            "https://files.realpython.com/media/Python_Exceptions_Watermark.47f814fbeced.jpg"
        ),
        gResponse.generate_list_item(
            "Elemento 2",
            "ELEMENTO_2",
            ["cosa 2", "objeto 2"],
            "descripcion corta",
            "https://files.realpython.com/media/Python_Exceptions_Watermark.47f814fbeced.jpg"
        ),
        gResponse.generate_list_item(
            "Elemento 3",
            "ELEMENTO_#",
            ["cosa 3", "objeto 3"],
            "descripcion corta",
            "https://files.realpython.com/media/Python_Exceptions_Watermark.47f814fbeced.jpg"
        ),
        title="Titulo de la lista"
    )
    agent.add_custom_payload(gResponse.get_response())
    return agent.get_response()

# Set up intent map
intentMap = IntentMap()
intentMap.add("List", intentList)

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

###### Carrousel

```Python

import flask
from flask import request, jsonify
# Import GoogleResponse
from pylogflow import IntentMap, GoogleResponse, Agent

app = flask.Flask(__name__)

@app.route('/', methods=['POST'])
def home():
    result = intentMap.execute_intent(request.json)
    return jsonify(result)

def intentCarrousel(req):
    agent = Agent()
    gResponse = GoogleResponse()
    gResponse.add_carrousel(
        gResponse.generate_carrousel_item(
            "Elemento 1",
            "ELEMENTO_1",
            ["cosa 1", "objeto 1"],
            "descripcion corta",
            "https://files.realpython.com/media/Python_Exceptions_Watermark.47f814fbeced.jpg"
        ),
        gResponse.generate_carrousel_item(
            "Elemento 2",
            "ELEMENTO_2",
            ["cosa 2", "objeto 2"],
            "descripcion corta",
            "https://files.realpython.com/media/Python_Exceptions_Watermark.47f814fbeced.jpg"
        ),
        gResponse.generate_carrousel_item(
            "Elemento 3",
            "ELEMENTO_#",
            ["cosa 3", "objeto 3"],
            "descripcion corta",
            "https://files.realpython.com/media/Python_Exceptions_Watermark.47f814fbeced.jpg"
        )
    )
    agent.add_custom_payload(gResponse.get_response())
    return agent.get_response()

# Set up intent map
intentMap = IntentMap()
intentMap.add("Carrousel", intentCarrousel)

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

###### Handle List and Carrousel options

List and Carrousel use options in Google Assistant, we can handle this options easy by importing GoogleRequest, generaly this options prompts to another intent, this is an example that can handle any option.


```Python
# Intent function
def intentOption(req):
    agent = Agent()
    print(req)
    gResponse = GoogleResponse()
    # Instantiate a GoogleRequest object sending request received in this intent function
    gRequest = GoogleRequest(req)
    # This is the option selected obtained from request
    selection = gRequest.get_option_arguments()
    # It comes as a list, generally is only 1 element
    for option in selection:
        gResponse.add_simple_response("selected : " + option)
    agent.add_custom_payload(gResponse.get_response())
    return agent.get_response()

```

###### Browse Carrousel

```Python

def intentBrowseCarrousel(req):
    # Set agent 
    agent = Agent()
    gResponse = GoogleResponse()
    # You can make a list of items
    items = []
    # Append every item to list
    items.append(gResponse.generate_browse_carrousel_item(
            "titulo1",
            "descripcion 1", 
            "piesillo 1", 
            "https://files.realpython.com/media/Python_Exceptions_Watermark.47f814fbeced.jpg", 
            "hola", 
            "https://files.realpython.com/media/Python_Exceptions_Watermark.47f814fbeced.jpg"))
    items.append(gResponse.generate_browse_carrousel_item(
            "titulo2",
            "descripcion 2", 
            "piesillo 2", 
            "https://files.realpython.com/media/Python_Exceptions_Watermark.47f814fbeced.jpg", 
            "hola", 
            "https://files.realpython.com/media/Python_Exceptions_Watermark.47f814fbeced.jpg"))
    # Send list as arguments with * operator
    gResponse.add_browse_carrousel(*items)
    agent.add_custom_payload(gResponse.get_response())
    return agent.get_response()
```
###### Table

```Python

def intentTable(req):
    agent = Agent()
    gResponse = GoogleResponse()
    gResponse.add_table(
        # First parameter is Header Row
        gResponse.generate_table_header_row(
            # Necesary Table headers, you can add as list with * if you want
            gResponse.generate_table_header("Titulo 1"),
            gResponse.generate_table_header("Titulo 2"),
            gResponse.generate_table_header("Titulo 3")
        ),
        # Then add rows as arguments, or a list of table rows with * 
        gResponse.generate_table_row(
            "texto1",
            "texto2",
            "texto3"
        ),
        gResponse.generate_table_row(
            "texto1",
            "texto2",
            "texto3"
        ),
        # KWargs
        title="Titulo Prueba",
        subtitle="prueba subtitulo", # this needs a title to be placed
        imageUrl="https://files.realpython.com/media/Python_Exceptions_Watermark.47f814fbeced.jpg",
        buttonText="Hola soy un boton",
        buttonUrl="https://files.realpython.com/media/Python_Exceptions_Watermark.47f814fbeced.jpg",
    )
    agent.add_custom_payload(gResponse.get_response())
    return agent.get_response()
```

###### Media Response

This is an audio response.

```Python
    def intentMedia(req):
    # Set agent 
    agent = Agent()
    # agent.add_Message("ayuda respondiendo")
    gResponse = GoogleResponse()
    gResponse.add_media_response(
        "nombre",
        "descripcion",
        "https://storage.googleapis.com/automotive-media/album_art.jpg",
        "Album cover of an ccean view",
        "https://storage.googleapis.com/automotive-media/Jazz_In_Paris.mp3"
    )
    agent.add_custom_payload(gResponse.get_response())
    # this return a dictionary format
    return agent.get_response()

```


```Python
import flask
from flask import request, jsonify
from pylogflow import IntentMap, GoogleResponse, GoogleRequest, Agent

app = flask.Flask(__name__)

@app.route('/', methods=['POST'])
def home():
    # Send request as a json, result must be a dictionary
    result = intentMap.execute_intent(request.json)
    # Parse result into json format
    return jsonify(result)
    
# Intent Multiple simple response
def intentSimpleResponse(req):
    agent = Agent()
    gResponse = GoogleResponse()
    gResponse.add_simple_response("Hey")
    gResponse.add_simple_response("You")
    agent.add_custom_payload(gResponse.get_response())
    return agent.get_response()


# Intent ayuda
def intentAyuda(req):
    # Set agent 
    agent = Agent()
    # agent.add_Message("ayuda respondiendo")
    gResponse = GoogleResponse()
    gResponse.add_media_response(
        "nombre",
        "descripcion",
        "https://storage.googleapis.com/automotive-media/album_art.jpg",
        "Album cover of an ccean view",
        "https://storage.googleapis.com/automotive-media/Jazz_In_Paris.mp3"
    )
    gResponse.add_simple_response("Hola, esto es una prueba")
    gResponse.add_suggestion_chips("flask", "algo")
    agent.add_custom_payload(gResponse.get_response())
    # this return a dictionary format
    return agent.get_response()
   
    
# Intent request
def intentRequest(req):
    # Set agent 
    agent = Agent()
    gResponse = GoogleResponse()
    gResponse.add_browse_carrousel(
        gResponse.generate_browse_carrousel_item(
            "titulo1",
            "descripcion 1", 
            "piesillo 1", 
            "https://files.realpython.com/media/Python_Exceptions_Watermark.47f814fbeced.jpg", 
            "hola", 
            "https://files.realpython.com/media/Python_Exceptions_Watermark.47f814fbeced.jpg"),
        gResponse.generate_browse_carrousel_item(
            "titulo2",
            "descripcion 2", 
            "piesillo 2", 
            "https://files.realpython.com/media/Python_Exceptions_Watermark.47f814fbeced.jpg", 
            "hola", 
            "https://files.realpython.com/media/Python_Exceptions_Watermark.47f814fbeced.jpg")
    )
    agent.add_custom_payload(gResponse.get_response())
    return agent.get_response()

# Intent algo
def intentAlgo(req):
    agent = Agent()
    gResponse = GoogleResponse()
    gResponse.add_table(
        gResponse.generate_table_header_row(
            gResponse.generate_table_header("Titulo 1"),
            gResponse.generate_table_header("Titulo 2"),
            gResponse.generate_table_header("Titulo 3")
        ),
        gResponse.generate_table_row(
            "texto1",
            "texto2",
            "texto3"
        ),
        gResponse.generate_table_row(
            "texto1",
            "texto2",
            "texto3"
        ),
        title="Titulo Prueba",
        subtitle="prueba subtitulo",
        imageUrl="https://files.realpython.com/media/Python_Exceptions_Watermark.47f814fbeced.jpg",
        buttonText="Hola soy un boton",
        buttonUrl="https://files.realpython.com/media/Python_Exceptions_Watermark.47f814fbeced.jpg",
    )
    agent.add_custom_payload(gResponse.get_response())
    return agent.get_response()

# intent lista
def intentLista(req):
    agent = Agent()
    gResponse = GoogleResponse()
    gResponse.add_list(
        gResponse.generate_list_item(
            "Elemento 1",
            "ELEMENTO_1",
            ["cosa 1", "objeto 1"],
            "descripcion corta",
            "https://files.realpython.com/media/Python_Exceptions_Watermark.47f814fbeced.jpg"
        ),
        gResponse.generate_list_item(
            "Elemento 2",
            "ELEMENTO_2",
            ["cosa 2", "objeto 2"],
            "descripcion corta",
            "https://files.realpython.com/media/Python_Exceptions_Watermark.47f814fbeced.jpg"
        ),
        gResponse.generate_list_item(
            "Elemento 3",
            "ELEMENTO_#",
            ["cosa 3", "objeto 3"],
            "descripcion corta",
            "https://files.realpython.com/media/Python_Exceptions_Watermark.47f814fbeced.jpg"
        ),
        title="Titulo de la lista"
    )
    agent.add_custom_payload(gResponse.get_response())
    return agent.get_response()

#Intent opcion lista
def intentOpcionLista(req):
    agent = Agent()
    print(req)
    gResponse = GoogleResponse()
    gRequest = GoogleRequest(req)
    selection = gRequest.get_option_arguments()
    for option in selection:
        gResponse.add_simple_response("selected : " + option)
    capabilities = gRequest.get_capabilities()
    msg = ""
    for cap in capabilities:
        msg += cap["name"]
    gResponse.add_simple_response(msg)
    agent.add_custom_payload(gResponse.get_response())
    return agent.get_response()

# Intent carrousel
def intentCarrousel(req):
    agent = Agent()
    gResponse = GoogleResponse()
    gResponse.add_carrousel(
        gResponse.generate_carrousel_item(
            "Elemento 1",
            "ELEMENTO_1",
            ["cosa 1", "objeto 1"],
            "descripcion corta",
            "https://files.realpython.com/media/Python_Exceptions_Watermark.47f814fbeced.jpg"
        ),
        gResponse.generate_carrousel_item(
            "Elemento 2",
            "ELEMENTO_2",
            ["cosa 2", "objeto 2"],
            "descripcion corta",
            "https://files.realpython.com/media/Python_Exceptions_Watermark.47f814fbeced.jpg"
        ),
        gResponse.generate_carrousel_item(
            "Elemento 3",
            "ELEMENTO_#",
            ["cosa 3", "objeto 3"],
            "descripcion corta",
            "https://files.realpython.com/media/Python_Exceptions_Watermark.47f814fbeced.jpg"
        )
    )
    agent.add_custom_payload(gResponse.get_response())
    return agent.get_response()


# Default fallback intent
def fallback(req):
    agent = Agent()
    agent.add_message("default fallback")
    print(jsonify(req))
    return agent.get_response()

# Set up intent map
intentMap = IntentMap()
intentMap.add("request", intentRequest)
intentMap.add("Simple Response", intentSimpleResponse)
intentMap.add("ayuda", intentAyuda)
intentMap.add("algo", intentAlgo)
intentMap.add("lista", intentLista)
intentMap.add("Default Fallback Intent", fallback)
intentMap.add("opcion lista", intentOpcionLista)
intentMap.add("carrousel", intentCarrousel)

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)

```

#### Advice

My english is not good at all, if i got an error correct me please.

