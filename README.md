# Backstage
Backstage is an open platform for building developer portals. Powered by a centralized software catalog, Backstage restores order to your microservices and infrastructure and enables your product teams to ship high-quality code quickly without compromising autonomy. Backstage unifies all your infrastructure tooling, services, and documentation to create a streamlined development environment from end to end.

## Backstage + APIs.json
This an exploration of what is possible when you marry Backstage and the APIs.json format. Backstage is driven by entity files defined as YAML published to GitHub repositories. This repository intends to pull and APIs.json files listed in the local APIs.json index within this repository, and translate into Backstage entities. 

## APIs.json Index
There is an APIs.json index published in this repository which will act as the index for all of the APis.json API indexes that will be translated into Backstage entities, allowing new APIs to be added or removed as needed for inclusion in the API catalog.

```
{
    "name": "Backstage and APIs.json Exploration",
    "description": "This is an exploration of what is possible when you marry APIs.json with Backstage.",
    "image": "https://kinlane-productions2.s3.amazonaws.com/apis-json/apis-json-logo.jpg",
    "url": "https://raw.githubusercontent.com/apis-json/backstage/main/apis.json",
    "created": "2023/12/10",
    "modified": "2023/12/10",
    "specificationVersion": "0.16",
    "tags": [
      "APIs",
      "Discovery",
      "Backstage",
      "Portal",
      "Catalog"
    ],
    "apis": [],
    "maintainers": [
      {
        "FN": "API Evangelist",
        "email": "info@apievangelist.com"
      }
    ],
    "includes": [
        {
        "name": "Twilio",
        "url": "https://raw.githubusercontent.com/apis-json/artisanal/main/apis/twilio.json"
        }
      ]    
  }
```
## CI/CD JavaScript
This repository will have a single JavaScript file that will load the APIs.json index and pull any APIs.json available as part of the include property, updating local YAML to ensure all APIs in the index are represented.
```
// Just sample code for now.
const options = {
method: 'GET'
};  

fetch('[apis.json file]',options)
.then(function(response) {
    if (!response.ok) {
        console.log('Error with Status Code: ' + response.status);
        callback( null, response.status );
        connection.end();
    }
    response.json().then(function(data) {   

    callback( null, data );
    connection.end();

    });
})
.catch(function(err) {
    console.log('Error: ' + err);
});
```
This file will run anytime a commit is executed against this repository, or can be triggered using some other event or on a schedule, ensuring that each index is pulled regularly or when a new one is added.

## Backstage Entities
Each APIs.json that is pulled will be translated into Backstage entities, taking the APIs.json index and producing the following Backstage system "kind" to represent the APIs.json index.
```
apiVersion: backstage.io/v1alpha1
kind: Component
metadata:
  name: twilio-signup
  title: Twilio Signup
  description: The sign up page for Twilio
  tags:
    - signup
    - login
  links:
    - url: https://www.twilio.com/try-twilio
      title: Sign Up
      icon: dashboard    
spec:
  type: sign-up
  lifecycle: published
  owner: apis-io
  system: twilio 
```
Then each API that is indexed will be pulled and a Backage API "kind" will generated to represent each API that is included in the APIs.json.
```
apiVersion: backstage.io/v1alpha1
kind: API
metadata:
  name: twilio-messaging-api
  title: Twilio Messaging API
  description: Send and receive messages via SMS, MMS, WhatsApp, Facebook Messenger, and more through our Messaging and Conversations APIs.
  tags:
    - messaging
  links:
    - url: https://www.twilio.com/en-us/ahoy  
      title: Human URL
      type: human-url
      icon: dashboard    
    - url: https:/api.twilio.com
      title: Human URL
      type: base-url
      icon: dashboard             
spec:
  type: OpenAPI
  lifecycle: published
  owner: apis-io
  system: twilio
  definition: |[]
```
Then each property of an API or the common properties of the APIs.json will have individual Backstage Component "Kinds" generated and associated with the parent System or API entity.
```
apiVersion: backstage.io/v1alpha1
kind: Component
metadata:
  name: twilio-identity-verification-api-documentation
  title: Twilio Identity and Verification API Documentation
  description: The documentation for the Twilio Identity and Verification API.
  tags:
    - documentation
  links:
    - url: https://www.twilio.com/en-us/trusted-activation/verify
      title: Human URL
      icon: dashboard    
spec:
  type: documentation
  lifecycle: published
  owner: apis-io
  system: twilio  
```
All System, API, and Component entities are then saved to the underlying repository as YAML, producing Backstage entities that represent everything included in the APIs.json index.

## Backstage YAML Index
Once generated, the Backstage entities generated from the APIs.json and published to this repository can be included in the configuration for any Backstage implementation, pulling the entities into the API catalog.
```
     - type: url
       target: https://github.com/backstage/backstage/blob/master/packages/catalog-model/examples/acme-corp.yaml
       rules:
         - allow: [User, Group]
```
