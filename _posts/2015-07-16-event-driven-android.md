---
layout: post
title: Event Driven Android
author: Oyewale Oyediran
feature-image: event-driven-architecture.png
tags:
- android
- otto
- event-driven
- architecture
- dev
---

Communication between various components in the Android framework has been one of the major issues facing Android application development. A lot of work-arounds (more like hacks) have been used to solve some of this issues, many of which increases code complexity as the project expands and also leads to grossly inefficient use of the framework.

The event-driven architecture utilizes the [Publisher-Subscriber pattern] to solving issues with inter-component communication by allowing components asynchronously send, receive and process events. The publishers emits (produces) events, while the consumers subscribes for events. At the center of this architecture is the event-bus which receives events and sends them asynchronously to subscribers of these events.

![Event Driven Architecture](/images/event-driven-architecture.png)

###About Otto
There are a couple of libraries that help with implementing the event bus on Android, some of which includes [EventBus], [Otto]. I have used Otto in some projects i have worked on and I will be explaining the fundamentals.  Otto is an event bus designed to decouple different parts of your application while still allowing them to communicate efficiently. It is quite simple to use and highly optimized for Android.

####Create the Bus
You simply create a Bus object, which is the event bus implementation.

```Bus bus = new Bus();```

Most use-cases will require the creation of a singleton instance of Otto Bus. This can be defined in the Application sub-class

```java
public class MainApplication extends Application {
   private static Bus bus;

   public static Bus getBusInstance(){
       if (bus == null){
           bus = new Bus();
       }
       return bus;
   }
}
```

####Publishing Events
Publishing an event is by calling the ``` .post(event)``` method of the Bus class.

```java
bus.post("Hello"); // posting a simple string

//sample class
public class Weather{
    int temperature 
}

// posting an event
Weather event = new Weather();
event.temperature = 30;
bus.post(event);
```


####Registering and Unregistering for Events

Subscribing your consuming component to the event bus is achieved by registering the component class on the Bus, bus.register(this); and annotating a method with ``` @Subscribe``` which will receive a single parameter with the type representing the event you wish to subscribe to. For example, subscribing an activity class;

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);        
        .....
    }

    @Override
    protected void onResume() {
        super.onResume();

        // Register the Subscribing class on the Bus
        MainApplication.getBusInstance().register(this);
    }

    @Override
    protected void onPause() {
        super.onPause();

        //unregister when a class should no longer be on the bus
        MainApplication.getBusInstance().unregister(this);
    }

    @Subscribe
    public void weatherAvailable(WeatherModel weatherModel) {
        // React to the event somehow!
        
    }
}
```

The event-driven paradigm allows for high decoupling between various application components. Example includes Activity-Fragment, Service-Activity communication. The example code implements a simple Service-Activity communication. 

The full source code of the example is available on [Github]. If you have got questions, contributions, corrections, please leave a comment below.



[Publisher-Subscriber pattern]:https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern
[EventBus]:http://greenrobot.github.io/EventBus
[Otto]:http://square.github.io/otto
[Github]:https://github.com/waleoyediran/ottoexample