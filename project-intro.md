## Project Intro

In this lab you will create a simple mobile application (code name [Gramola](https://translate.google.com/?hl=es#es/en/gramola)) using [Google Flutter](https://flutter.io) that at the beginning will let a user list musical events for a given city. Later, as in the real life, a new feature will be needed like showing/adding a timeline of messages related to a given event.

We're going to create this mobile from the scratch but having some help to fastforward the process.

As we progress in the creation process we'll move from a story board to a real mobile app consuming services defined according to the needs of our story board and some sample business data.

#### What is Flutter?
As they define themselves, Flutter is:

> Flutter is Google’s mobile app SDK for crafting high-quality native interfaces on iOS and Android in record time. Flutter works with existing code, is used by developers and organizations around the world, and is free and open source.

#### Let's have a look to the story board
So, as we said before, we want to show a list of events and eventually a timeline of comments associated with a given user.

The storyboard is very simple, the first thing our potential user wants to do is login so that she can see her own timeline entries for an event, as in the next image.
![Login]({% image_path gramola-login.png %}){:width="300px"}

After a successful login we want to show all upcoming events for any location.
![List of events]({% image_path gramola-list-of-events.png %}){:width="300px"}

We want the user to choose a different city from a list of cities our company currently has footprint.
![List of events]({% image_path gramola-choose-city.png %}){:width="300px"}

Finally once a user selects and events it should be possible see the timeline of her comments related to the event.
![List of events]({% image_path gramola-timeline.png %}){:width="300px"}

#### Discovering the APIs we need
So, after analyzing the flow of our application we have found the following APIs we need to define before actually coding our app. They don't have to be perfect or complete (they never are) but at least they should define how requests and responses are and the method to be used (GET, POST, etc.).



#### Business data regarding the events API
As you can see our event objects are simple, name, location, artist, etc.

~~~json
[
    {
        "id": 1,
        "name": "Not in the Lifetime Tour",
        "address": "Cmo. de Perales, 23, 28041 Madrid",
        "city": "MADRID",
        "province": "MADRID",
        "country": "SPAIN",
        "date": "2018-08-05",
        "startTime": "18:00",
        "endTime": "23:00",
        "location": "Caja Magica",
        "artist": "Guns n Roses",
        "description": "The revived Guns N’ Roses and ...",
        "image": "guns-P1080795.jpg"
    },
    {
        "id": 2,
        "name": "CONCRETE AND GOLD TOUR 2018",
        "address": "8 Boulevard de Bercy, Paris",
        "city": "PARIS",
        "province": "PARIS",
        "country": "FRANCE",
        "date": "2018-09-15",
        "startTime": "18:00",
        "endTime": "23:00",
        "location": "AccorHotels Arena",
        "artist": "Foo Fighters",
        "description": "Concrete and Gold Tour is ...",
        "image": "foo-P1000628.jpg"
    }
]
~~~

Well done! You are ready to move on to the next lab.
