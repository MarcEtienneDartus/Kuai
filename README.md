# Loa 🧙‍♀️

A small AI-based conversational agent capable of providing accurate informations about U.S located restaurants, and integrated with a content-based recommendation engine.

## Demo

![](https://media.giphy.com/media/gfwGJdXuj08X4YcRQu/source.gif)

## **Table of contents**:

  - [👩‍💻 Usage](#usage)
    - [Chatbot capabilities](#chatbot-capabilities)
      - [Greeting](#greeting)
      - [Search restaurants](#show-restaurants)
      - [Find the best restaurant](#best-restaurant)
      - [Number of restaurants](#number-restaurants)
    - [Recommendation Engine](#recommendation-engine)  
  - [🏃‍♀️ Testing](#testing)
  - [🎯 Objectives](#objectives)
    - [Stack and implementation](#stack)
      - [Front-End](#front-end)
      - [Back-end](#back-end)
        - [Chatbot API](#chatbot-api)
        - [Recommendation Engine](#recommendation-engine-stack)
    - [To do](#todo)

<a name="usage"/>

## 👩‍💻 Usage

<a name="chatbot-capabilities"/>

### Chatbot Capabilities


<a name="greeting"/>

### **1 - Greeting**
Type of Question

* **Hello**, how are you ?
* **Good morning**! 
* **Hi**, what's up ?

Node API :

`Endpoint :` POST /message

*Request :*

```json
{
    "message": "Hi, what's up ?"
}
```

*Response :*

```json
{
    "intent": "Greeting",
    "response": "Hi, I'm great, and you ?" (randomly picked)
}
```


Intent :

> **Hi**, what's up ?

| Variable |  Value   |
| :------: | :------: |
| `Intent` | Greeting |

<a name="show-restaurants"/>

### **2 - Search restaurants**

Type of Question :

* Can you **show** me the **restaurants** **around me** ?
* Can you **show** me the **restaurants** in **Los Angeles** ?
* **Map** of **restaurants** in **Paris**.
* What are the **most rated** **restaurants** in **Los Angeles** ?
* Which **restaurants** are open **around me** ?

#### Example

*1 - Request :*

Requesting the Node API :  
`Endpoint :` POST /message

We call our Node API we this structure :

```json
{
    "message": "Can you show me the restaurants in Los Angeles ?",
    "user": {
        "coordinates": {
                "latitude": 34.052234,
                "longitude": -118.243685
            }
    }
}
```

*2 - Wit.ai Intent extraction :*

Wit.ai will exctract intent and entities from the user question.

The question :

> Can you **show** me the **restaurants** in **Los Angeles** ?

Will return :

|  Variable  |    Value    |
| :--------: | :---------: |
|  `Intent`  |    Show     |
|  `Object`  | Restaurants |
| `Location` | Los Angeles |

*3 - Response :*

With the information, we call the `Yelp API` and use the result to build our API response :

`Yelp API` result :

```json
{
    "results": [
        {
            "name": "Howlin' Ray's",
            "id": "7O1ORGY36A-2aIENyaJWPg",
            "url": "https://www.yelp.com/biz/howlin-rays-los-angeles-3?adjust_creative=94DePyCeUwdjASSwoI0YbA&utm_campaign=yelp_api_v3&utm_medium=api_v3_graphql&utm_source=94DePyCeUwdjASSwoI0YbA",
            "display_phone": "(213) 935-8399",
            "rating": 4.5,
            "price": "$$",
            "location": {
                "address1": "727 N Broadway",
                "city": "Los Angeles",
                "postal_code": "90012"
            },
            "coordinates": {
                "latitude": 34.061517,
                "longitude": -118.239716
            },
            "photos": [
                "https://s3-media2.fl.yelpcdn.com/bphoto/9hGoyECcrewigEKYEnrYTw/o.jpg"
            ]
        }
    ]
}
```


*4 - User reponse generation*

After getting all the information in order to answer the user demand, **we use Natural Language Generation to display a response**.

additionally, **we display the concenred result(s) on the right side of our web application**.

In our example we want to display a map of the reslut and tell it to the user.

*5 - Final Node API response*

Gathering all of these steps, our API will return :

```json
{
    "intent": "Show",
    "object": "Restaurants",
    "location": {
        "name": "Los Angeles",
        "coordinates": {
                "latitude": 34.052234,
                "longitude": -118.243685
            }
    },
    "eesponse": "You will find a map of restaurants in Los Angeles.",
    "results": [
        {
            "name": "Howlin' Ray's",
            "id": "7O1ORGY36A-2aIENyaJWPg",
            "url": "https://www.yelp.com/biz/howlin-rays-los-angeles-3?adjust_creative=94DePyCeUwdjASSwoI0YbA&utm_campaign=yelp_api_v3&utm_medium=api_v3_graphql&utm_source=94DePyCeUwdjASSwoI0YbA",
            "display_phone": "(213) 935-8399",
            "rating": 4.5,
            "price": "$$",
            "location": {
                "address1": "727 N Broadway",
                "city": "Los Angeles",
                "postal_code": "90012"
            },
            "coordinates": {
                "latitude": 34.061517,
                "longitude": -118.239716
            },
            "photos": [
                "https://s3-media2.fl.yelpcdn.com/bphoto/9hGoyECcrewigEKYEnrYTw/o.jpg"
            ]
        },
        ...
    ]
}
```


GraphQL request :
```
{
  search(term: "restaurant", location:"Los Angeles") {
    business {
      name
      id
      url
      display_phone
      rating
      price
      location {
        address1
        city
        postal_code
      }
      coordinates {
        latitude
        longitude
      }
      photos
    }
  }
}
```

<a name="best-restaurant"/>

### **3 - Find the best restaurant**
Type of Question

* What is the **best** **restaurant** in **New York** ?
* What is the **best** **restaurant** around me ?
* Find the **best** **restaurant** in **Paris** ?
* Find the **best** **restaurant** **near me** ?
* Find the **best** **restaurant** near **111 8th Ave New York** ?

Node API :

`Endpoint :` POST /message

*Request :*

```json
{
    "message": "What is the best restaurant in New York ?",
    "user": {
        "coordinates": {
                "latitude": 34.052234,
                "longitude": -118.243685
            }
    }
}
```

*Response :*

```json
{
    "Intent": "Best",
    "Object": "Restaurants",
    "Location": {
        "name": "New York",
        "coordinates": {
                "latitude": 40.7122775,
                "longitude": -74.005973
            }
    },
    "Response": "The best restaurant in New York is LoveMama.",
    "Results": [
        {
            "name": "LoveMama",
            "id": "jjJc_CrkB2HodEinB6cWww",
            "url": "https://www.yelp.com/biz/lovemama-new-york?adjust_creative=94DePyCeUwdjASSwoI0YbA&utm_campaign=yelp_api_v3&utm_medium=api_v3_graphql&utm_source=94DePyCeUwdjASSwoI0YbA",
            "display_phone": "(212) 254-5370",
            "review_count": 4988,
            "rating": 4.5,
            "price": "$$",
            "location": {
                "address1": "174 2nd Ave",
                "city": "New York",
                "postal_code": "10003"
            },
            "coordinates": {
                "latitude": 40.7303859,
                "longitude": -73.9860613
            },
            "photos": [
                "https://s3-media1.fl.yelpcdn.com/bphoto/bLlFKTlVuLfmF-lIDGIjZA/o.jpg"
            ]
        }
    ]
}
```


Intent :

> What is the **best** **restaurant** in **New York** ?

|  Variable  |   Value    |
| :--------: | :--------: |
|  `Intent`  |    Best    |
|  `Object`  | Restaurant |
| `Location` |  New York  |

Process System : `Yelp API`

GraphQL Request :
```
{
  search(term: "restaurant", location:"New York") {
    business {
      name
      id
      url
      display_phone
      review_count
      rating
      price
      location {
        address1
        city
        postal_code
      }
      coordinates {
        latitude
        longitude
      }
      photos
    }
  }
}
```

<a name="number-restaurants" />

### **4 - How many restaurants**
Type of Question

* How **many** **restaurants** are in **New York** ?
* What is the number of **restaurants** around me ?
* What is the **number** of **restaurants** in **Colorado** ?

Node API :

`Endpoint :` POST /message

*Request :*

```json
{
    "message": "What is the number of restaurants in Colorado?"
}
```

*Response :*

```json
{
    "intent": "Number",
    "object": "Restaurants",
    "location": {
        "name": "Colorado",
        "coordinates": {
                "latitude": 40.7122775,
                "longitude": -74.005973
            }
    },
    "response": "There are 128 restaurants in Colorado"
}
```


Intent :

> What is the **number** of **restaurants** in **Colorado** ?

|  Variable  |    Value    |
| :--------: | :---------: |
|  `Intent`  |   Number    |
|  `Object`  | Restaurants |
| `Location` |  Colorado   |

Process System : `Yelp API`

GraphQL Request :
```

```

<a name="recommendation-engine"/>

### Recommendation Engine

<a name="testing"/>

## 🏃‍♀️ Testing

<a name="objectives"/>

## 🎯 Objectives

<a name="stack"/>

### Tech-Stack and Implementation

We use [wit.ai](https://wit.ai) to convert text to intent using NLP. We have trained a model in Python to recommend restaurant with the user preference. Otherwise if they don't need recommendation, we use the yelp API to provide some information. Node.js merge all this processing into one service wich is going to be easier to use in the front end.

![stack schema](https://user-images.githubusercontent.com/45569127/76697698-96401400-669a-11ea-90af-80bbfb78d9ca.JPG)

<a name="front-end"/>

#### Front-end

<a name="back-end"/>

#### Back-end

<a name="chatbot-api"/>

##### Chatbot API

<a name="recommendation-engine-stack"/>

##### Recommendation Engine

<a name="todo"/>

### To do

- [x] Desing & Prototype on Figma
- [ ] Define constraints and objectives 
- [ ] Implement basic front-end interface
- [ ] Train wit.ai agent on our intents
- [ ] Design and Connect Node API to wit.ai
- [ ] Gather data and design Recommendation Engine
- [ ] Serve our APIS as services and host them
- [ ] Finish front-end interface
- [ ] Tests along the way 





