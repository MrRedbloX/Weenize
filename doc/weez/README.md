# Weez
## Description
A weez is a JSON object composed by:
- A query for the search topic.
- A payload representing the various transformations.
- Some optional arguments.
## Structure
```
{
    "query": "<YOUR QUERY>",
    "payload": [
        {
            "input": "<KEY INPUT>", 
            "inputs": ["<KEY INPUT 1>", "<KEY INPUT 2>"],
            "transformer": "<YOUR TRANSFORMER>", // Mandatory
            "args": { // Optionnal
                "<MY ARG KEY>": "<MY ARG VALUE>"
            },
            "connector": "<CONNECTOR NAME>" // Optionnal
            "connector: { // Optionnal
                "name": "<CONNECTOR NAME>", // Mandatory
                "args": { // Optional
                    "<CONNECTOR ARG KEY>": "<CONNECTOR ARG VALUE>"
                },
                "type": "<CONNECTOR TYPE>" // Optional, can be "pre" (default) to apply the connector before the transformation, or "post" to apply after the transformation
            }
            "connectors": [ // Optionnal
                "<CONNECTOR NAME>",
                {
                    "name": "<CONNECTOR NAME>",
                    "args": {
                        "<CONNECTOR ARG KEY>": "<CONNECTOR ARG VALUE>"
                    }
                }
            ],
            "output": "<KEY OUTPUT>"
        }
    ],
    "args": { // Optional
        "<ARG KEY>": "<ARG VALUE>"
    }
}
```
#### Example
```
{
    "query": "machine learning",
    "payload": [
        {
            "input": "__query__",
            "transformer": "TwitterSearch",
            "args": {
                "creds": "__args__.twitter_api_creds"
            },
            "output": "twitter_search"
        },
        {
            "input": "twitter_search",
            "connector": {
                "name": "get_dict_value",
                "args": {
                    "key": "images"
                }
            },
            "transformer": "GoogleVision",
            "args": {
                "creds": "__args__.default_service_account",
                "detection_type": "OBJECT_LOCALIZATION",
                "limit": 3
            },
            "output": "__response__"
        }
    ]
}
```
See more examples in folder 'examples'.
## Global variables
- \_\_query__: references the value of the query field.
- \_\_args__: references the single layer dictionary of the field *args*. It can be credentials for other APIs. You can access the values with \_\_args__.\<MY ARG>.
- \_\_response__: indicates the API which transformation contains the result returned.
## Transformers
A transformer performs "heavy" operation on the given input(s) and returns the result.
### GoogleVision
- Description: runs a search on Google Vision anotate image API.
- Inputs: 
    - images: a list of urls corresponding to images.
- Mandatory args:
    - creds: credentials for the service account.
    - detection_type: see all detection types at https://cloud.google.com/vision/docs/reference/rest/v1/Feature
- Optional args:
    - limit: integer to limit the number of images.
- Output: 
```
{
    "<IMAGE URL>": {
        <VISION API DATA>: see more at https://cloud.google.com/vision/docs/reference/rest/v1/AnnotateImageResponse
    }
}
```
### GoogleSearch
- Description: runs a google search.
- Inputs: 
    - query: a string.
- Optional args:
    - limit: integer to limit the number of searches.
- Output: 
```
{
    "<URL>": "<HTML>"
}
```
### ImageFromHtml
- Description: extracts all image urls in HTML text.
- Inputs: 
    - url_html: see output of GoogleSearch.
- Output: 
```
{
    "<URL>": ["<IMAGE URL>"]
}
```
### TextFromHtml
- Description: extracts all image urls in HTML text.
- Inputs: 
    - url_html: see output of GoogleSearch.
- Output: 
```
{
    "<URL>": "<PLAIN TEXT>"
}
```
### TwitterSearch
- Description: fetches tweets from a given query.
- Inputs: 
    - query: a string.
- Mandatory args:
    - creds: credentials for the twitter developer account. See more at https://developer.twitter.com/en/docs/twitter-api/getting-started/getting-access-to-the-twitter-api
- Optional args:
    - Any additional args of the API: https://developer.twitter.com/en/docs/twitter-api/tweets/search/api-reference/get-tweets-search-all
- Output: 
```
{
    "texts": ["<TWEET>"],
    "images": ["<IMAGE URL>"]
}
```
### InstagramSearch
- Description: fetches instagram posts from a given query (corresponding to a hashtag).
- Inputs: 
    - query: a string.
- Mandatory args:
    - creds: credentials for an instagram account (username and password).
- Optional args:
    - limit: integer the limit the number of posts.
- Output: 
```
{
    "texts": ["<POST TEXT>"],
    "images": ["<IMAGE URL>"]
}
```
### GoogleCNL
- Description: calls the Google Cloud Natural Language API to perform a Sentiment Analysis (https://cloud.google.com/python/docs/reference/language/latest).
- Inputs: 
    - text: a string which can be plain text or HTML.
- Mandatory args:
    - creds: credentials for GCP service account.
    - action: the function to execute (example: analyze_sentiment).
    - text_type: value of language_v1.Document.Type (PLAIN_TEXT or HTML).
- Optional args:
    - encoding_type: value of language_v1.EncodingType (example: UTF8).
    - language: the language in the text (example: en, fr).
- Output: 
```
{
    "magnitude": 0.0, // Always positive
    "score": 0.0 // Between -1 and 1.
}
```
### Webhook
- Description: calls any other endpoint with the data as body.
- Inputs:
    - data: any data.
- Mandatory args:
    - reqs:
        - Mandatory:
            - http_method: can be any HTTP method (eg. GET, PUT, POST, ...).
            - url: the url endpoint.
        - Optional:
            - headers: arguments to pass in the request headers.
            - params: arguments to pass in the request query path.
## Connectors
A connector performs "light" operation on the given input(s) and returns the result. Connectors can be used to map the ouput of one transformer to the input of another transformer. They be run on the input with type 'pre' or on the output with type 'post'.
### dict_values
- Description: returns a list of dictionary values.
- Input: a dictionary.
- Output: a list of dict values.
### flatten
- Description: returns a flatten list.
- Input: a list of lists.
- Output: a list.
### get_dict_value
- Description: returns the value of a dictionary corresponding to the given value.
- Inputs: a dictionary.
- Optional args:
    - keys: can be a key or a list of keys in the dictionary. If null the first key will be used.
- Output: the value of dct\[key]
### concat_list
- Description: returns a string of a list joined by a separator.
- Inputs: a list.
- Optional args:
    - sep: the join separator.
- Output: a string.