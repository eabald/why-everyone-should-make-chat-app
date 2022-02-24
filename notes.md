# Why everyone should make chat app?

## 1. Introduce myself

Let's start with brief introduction. I'm Maciej Krawczyk and I am an Node.js developer at STX Next. Currently I am living in Warsaw.
Last month for the short period of time, between the projects I was working on kind of demo chat app. It left me with some afterthoughts about side projects. Everyone loves site projects, am I right? Of course everyone does. But the question is what makes side project good or bad, valuable or not?

And after some consideration I believe that chat app is really good example of valuable side project. The kind which everyone should consider doing.

## 2. Why?

Ok, but why? What makes chat app so special?

### A. Because everyone does?

It's because everyone is making chat apps? Everyone made one?

### B. Because it's easy?

Or maybe it's because it's easy to do, easy to implement?

### C. Because it's complicated?

Or on the other hand maybe it's because it's complicated, hard to do and challenging?

### D. No! Because it's easy enough to be doable and complicated enough to present reasonable challenge. No matter of your skill level

I believe the truth is somewhere in between. It's easy enough that most of us may have own idea how it should look and at the other hand presents reasonable challenge to be a fun to do. We can make it as simple or as complicated as needed. It's only determined by our goal. It mostly depends on what we want to show.

## 3. What good chat app should have?

Ok, but what this kind of app should have? What is complete must and what is optional?

### A. Real time communication

First of all, real time communication. It's something what makes chat app. Without it I don't think we can even call it chat app. No matter if we use HTTP long pooling or Web Sockets it's the most challenging part of whole chat app.

### B. Rooms/user-to-user messaging

Another maybe not necessary but also important part are rooms/direct messages. It elevates user engagement, and also levels up project difficulty.

### C. Emojis ✨🐢🚀✨

Let's state that once for all. Every chat app needs Emoji. You even can't call it chat without them. Period.

### D. Images upload

Next nice to have thing is image upload. This task has it's own challenges and in most cases requires different solutions than other parts of the app.

### E. Chat-bots

If we like to crank up your app to 11, we should add chat bot, or better two. This kind of feature often require some external integrations which are something nice to have. It shows the ability to use external sources and to integrate them smoothly.

## 4. Is chat app perfect showcase app?

Considering what I said before, is chat app really perfect showcase app? It mostly depends on what we want to show off.
If our goal is to show:

### A. Versatility

### B. Good knowledge of different technologies

### C. Ability to architect multipart systems

### D. Your own look for known problem

### E. Design skills

### F. Solid (SOLID) foundations

### G. In general, good craftsmanship

If thats what you want, great. It will work just fine.

## 3. But, on the other hand, if we want to show off

### A. How innovative we are

### B. How disruptive we are

### C. How ours ideas gonna change the world

### D. How our products outmatch competitors

I think it's not the right way. With special care maybe we can show some of this, but it will be hard. In general if we want to impress tech crowd with our skills, it's great. But if one wants to impress VC investors, I don't think that's the solution. Unless you are trying to build next Slack.

## 4. Our try

Ok, enough of that. Now let's talk about how we did it.

### A. Devhouse Chat Rabbitmq Nest Next Lambda - Devhouse Chat

Name is a bit lengthy so we've shorten it to Devhouse Chat, but the idea behind it was to reflect main technologies involved.

### B. Architecture && stack

In case of architecture our take is something in between Event Driven and Domain Driven with layered flavour. Backend elements are encapsulated in domain driven way but most of the communication between them is done by events. So a little of controlled chaos.

1. Frontend part of the system contains Next.js app with help of Material ui, communication with backend happens via web socket (socket.io) and http requests with axios. Application state is handled by Redux Toolkit.
2. Main element of app backend is Typescript and Nest.js app. It provides basic structure and nice layer of abstraction to hide some unimportant implementation details (of course unimportant from our point of view).
    * Nest.js gives us unique opportunity to abstract web sockets implementation into gateways which nicely encapsulates whole logic and allows us to focus on important elements. Also this gateways pushes events into messaging queue.
    * All the events are handled by message broker, RabbitMQ in that case. This solution allows to dispatch events properly and without additional overhead. Also adding RabbitMQ is a nice case of integrating different technologies into the system.
    * Some functionalities also needed rest endpoints to work properly, for example images upload. 
    * Next important element is data persistance. In order to achieve that SQLite database with Prisma orm was used. Maybe sql database is not optimal in chat app but tiny footprint of SQLite is fair tradeoff in that case. Additionally usage of Prisma gives us nice, type safe way to interact with database.
    * Images upload leaves us with a need for some kind of storage solution. At the beginning we've tried to use Zenko Cloudserver to mimic AWS S3 storage, but after a lot of problems with integration we have pivot to use MinIO Object Storage. After that everything went smoothly and there was no more problems with file storage.
    * In case of outside communication from Nest.js to other services via http protocol Nest.js http service was used (with axios underneath). That was straightforward solution and didn't require any additional work, so here decision was easy.

3. Another important part of the whole system are Chat bots. In our case there were two of them, one returning weather info based on supplied location and another one serving random memes from reddit. Here approach was slightly different than in other parts of the system. Serverless framework was used to create AWS lambda functions. Each one was separate endpoint encapsulating whole logic for each bot.
4. Last part of the system ties everything up. Services like RabbitMQ and MinIO was closed in docker containers, other parts of the system was also encapsulated in one docker container what allows to use it as development container in vscode. To orchestrate all npm packages Lerna was used. Solution composed like this elevates whole idea of development environment encapsulation to new level.

## 5. Demo

1. Here we can create user, which is persisted in database. Also current user is stored in redux store and local storage. Currently there's no any other authentication. But username have to be unique. Login process happens via web socket.
2. By default there are three rooms created. Hello, nodejs and typescript. Users can freely switch between them.
3. Also users can create new rooms. This process also happens via web socket, and new rooms are stored in database. And of course rooms names have to be unique.
4. Additionally users can message images. They are sent via http, stored in MinIO Object Storage and new message with them is created and send back via websocket.
5. As I said before, Emojis are the crucial element of each chat app. It can't work without them. I our case they can be added in two ways. First of all there's a picker, component taken from emoji-mart library. Another way is using emoji short codes which are filtered by emojify method from node-emoji library. 
6. By typing special commands instead of message user is able to invoke bot response. Two commands are available, first one /Weather followed by place name returns information about temperature in requested place as a response. Another one, /Meme returns random meme form reddit.
7. All the messages and images are stored and persistent but to avoid issues with storage of both of them they are persisted only for one hour. On the backend there are couple of scheduled tasks running every minute and deleting messages and images older than 1 hour. It uses Nest.js schedule module.
8. Also users can communicate using direct messages. This kind of messages works like private rooms with only two users. All the functionalities available in public rooms are also available in direct messages - messages persistence, images, emoji.

## 6. Is it the best?

Ok, now we have to answer important question. Is our take on chat app the best?

### A. Yes

Of course it is. It has all the necessary functionalities, real time communication, rooms/direct messages, emoji, chat bots. Also has nice encapsulated dev environment. Ten out of ten, and I relay don't know why our company still prefers Slack?

### B. But not relay

Ok, lets get back to reality. Our app also has its flaws. But on the other hand it was nice opportunity to learn and practice new stuff. Also it was possibility to get other perspective, to look on apps design and implementation in different way. So definitely it was worth the time spent.

## 7. Again why you should have your own chat app?

I believe that benefits of having a chat app in our portfolio are clear now. It's nice challenge allowing learn new things while doing fun stuff. Also can create different problems and challenges depending on our skills and goals we've set. It's perfect way to show off good craftsmanship and versatility.

## 8. Thank You

Thank you for attention. Presentation is also available on github pages. Is there any question?
