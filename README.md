# About PicoEvent

PicoEvent is a type safe event bus for Angular 4+ apps.

# Installation

PicoEvent is stilled with NPM:

```
npm install picoevent
```

# When to use PicoEvent

RxJS is a powerful API for dealing with observable streams asynchronouly.

PicoEvent simplies certain use cases while adding type safety. Since PicoEvent is just 
a library using RxJS, you can still use RxJS as usual in your Angular apps.

# Basic Usage

Import PicoEventModule:

```
  import { PicoEventModule } from 'picoevent';

  @NgModule({
  imports: [
    PicoEventModule,
  ],
})
export class MyModule { }
```

Create one or more message types. Pico allows you to create a typesafe **channel** for sending
messages of this type.

```
class SimpleTextMessage {
    constructor(public value: string) { }
}
```


Inject PicoEvent into your components and services. In a real-world application, several
components and services will be listening and/or sending to the same channel.

```
import { PicoEvent } from "picoevent";

@Component({...})
export class MyComponent {
    private channelSubscription: Subscription;

    constructor(private pico: PicoEvent) { }

    // Subscribe to SimpleTextMessage events
    ngOnInit() {

        this.channelSubscription 
            = this.pico.listen(SimpleTextMessage, msg => {
            
            // Do something interesting here
            console.log("Got: " + msg.value);
        });
    }

    // Make sure to unsubscribe when the component is destroyed
    ngOnDestroy() {
        this.channelSubscription.unsubscribe();
    }

    // On button click, sends message to everyone listening on the 
    // SimpleTextMessage channel
    onSendMessage() {
        this.pico.publish(new SimpleTextMessage(this.message));
    }
}

```

# Using event targets

PicoEvent#listen can also take an array of *target* names you wish to listen to:

```
    this.channelSubscription = this.pico.listen(
    {
          type: SimpleTextMessage,
          targets: ['chat-target']
    }, 
    msg => { console.log('Got a new message: ' + msg.value)  });
``` 

PicoEvent#publish takes the same optional second argument, which is the array of target names. Only channel
listeners with one or more matching target names will receive the event.

```
    // Make sure only the chat and log targets receive the message
    this.pico.publish(new SimpleTextMessage('A new chat message'), ['chat-target', 'log-target']);
```

Targets give you fine grained control over who receives particular events.
