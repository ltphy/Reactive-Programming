# REACTIVE PROGRAMMING

Learn RXJS through course RXJS in Practice

[Course Link](https://www.udemy.com/course/rxjs-course/learn/lecture/10897630?start=0#overview)

# STREAM  
# What are streams of value (s)?
Stream refers to a sequence of ongoing events ordered in time (flow of events).
Essentially the flow of data or events over time, managed by observables and can be manipulated
using various operators.

Multi-value srtream
Stream can be an infinite continous events like receiving IOT messages sending from the server.
EX: setInterval, handling user click event, receiving calling message from robot.

Stream can also only emit 1 value and end it services.
EX: setTimeout

For example:

If we want to set up a stream where after receiving a click event. We trigger a timeout waiting after 3 seconds to count
the up value infinitely

There can be multiple types of streams. Stream causes by user interaction, backend response, setTimeout, setInterval. All of them 
are asynchronous event that we want to combine them together to obtain the final value.

This can cause nested code or NESTED HELL.
```
document.getElementById("myButton").addEventListener("click", function() {
    console.log("Button clicked!");

    // Set a timeout to start the interval after 2 seconds
    setTimeout(function() {
        console.log("Starting interval...");

        // Set an interval that runs every 1 second
        let count = 0
        const intervalId = setInterval(function() {
            console.log("Interval event!");
            count = count + 1;
            console.log("count", count);

        }, 1000); // Interval runs every 1000ms (1 second)

    }, 2000); // Timeout before starting interval is 2000ms (2 seconds)
});

```


=> We should use RXJS to handle stream of event for flexibility and scalibility to handle complex streams of values

# INTERVAL
public static interval (period: number, scheduler: Scheduler): Observable

Create an observable that emits sequential numbers every specified interval of time.


```
const intervals$ = interval (1000);
```

intervals$ use a $ mark to differentiate with other values. This value is an observable object.
This is not a stream of values, just a definition of stream (an observables)

You need to subscribe to the observables to get the stream of values.

```
interval$.subscribe(val => console.log("val", val + 1 ));
interval$.subscribe(val => console.log("val", val + 1 ));

```
# DEFINE TIMER STREAM

wait xS before emitting the first value and then continously send the value based on the interval.

```
const interval$ = timer(3000,1000);
interval$.subscribe(val => console.log("stream 1" + val);

```
# DEFINE A CLICK STREAM

This only return the definition a stream. We have not yet instantiate yet the stream.
It is the blue sprint of stream
```
const click$ = fromEvent(document, 'click');
click$.subscribe(val => console.log("stream 1" + val);

```

An application that poll the value from server every 1 minutes.

```
click$.subscribe (
    (evt) => console.log(evt),
    (err) => console.log(err),
    () => console.log("completed);
)
```
 The stream can be error out or can also be completed with its last value.
if the stream error out, we can not longer emit value using this observalbe.

# Observable contract
+ Whenever the stream finishes emiting its values and its either an error or a complete process.
It will never emit value further again.
+ Error and completion are exclusive, it either one or another.
+ The observable might still continue to emit values without erroring out or completing incase of
timer(3000,1000). But it will still must stop to emit after completing or erroring out.
In summary, once an observable completes or encounters an error, it stops emitting values, and completion and error are mutually exclusive events.

# Subscriptions and unsubscribe
An observable that emit a lot of values overtime. We might at certain point in time, after 60 minutes stop listening
SO that we no longer handle the value the stream is emiting.

```
const sub = interval$.subscribe(val => console.log(val);
setTimeout(()=> sub.unsbscribe(), 5000):
```
# Create your own observables
a Promise is executed immediately after you define it, while observable does not. It only create a definition. You need to create 
a subscription to make observables work
Observables like timer, or intervals or the observable that call Observable.create()
```
Observable.create((observer => {
    fetch('api/course').then(response => {
        return response.json()
    })
    .then(body => {
        observer.next(body);
        observer.complete();
        # do not add another the next here
        # will violate the observerable contract 
    })
    .catch(err => {
        observer.error(err);
    })
});
```
## Difference between observable, observers and subscriptions

Think of the observable as a water pipe, the observer as a water meter, and the subscription as the valve that connects the meter to the pipe. The meter (observer) measures (reacts to) the water (values), while the valve (subscription) can start or stop the flow.
In summary:

+ The observable generates the data.
+ The observer processes the data.
+ The subscription connects the two and allows you to control the stream's execution.

## Should we use observable with async await pattern vs .then approach
In reactive programming doesnot invovle using await or .then in traditional sense

Streams
+ Emit multiple values over time.
+ Can emit zero, one, or many values and can either complete or error out.
+ Are more naturally handled with operators like subscribe, map, filter, merge, etc., which allow you to work with the sequence of events.

Promises:
+ Emit a single value or an error.
+ Are handled with await or .then() for chaining actions after the promise resolves.

Using promises is generally not recommended if you are dealing with streams because it breaks
reactive pattern and forces into the promise based approach, which does not handle multiple values over time as well.

Summary: 
+ The observable (observable$.pipe(...)) is the source of data.
+ The observer is the object passed to subscribe, which defines how to handle the data, errors, and completion.
+ The subscription is created when you call subscribe, linking the observer to the observable and allowing you to later unsubscribe if needed.
