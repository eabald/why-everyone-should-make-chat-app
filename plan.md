# Why everyone should make chat app?

## 1. Introduce myself

Let's start with brief introduction. I'm Maciej Krawczyk and I am an Node.js developer at STX Next. Currently I am living in Warsaw.
Last month for the short period of time, between the projects I was working on kind of demo chat app. It left me with some afterthoughts about side projects. Everyone loves site projects, am I right? Of course everyone does. But the question is what makes side project good or bad, valuable or not?

And after some consideration I believe that chat app is the one really good and valuable example of side project. The kind which everyone should consider doing.

## 2. Why?

Ok, but why? What makes chat app so special?

### A. Because everyone does?

It's because everyone does chat apps? Everyone made one?

### B. Because it's easy?

Or maybe it's because it's easy to do, easy to implement?

### C. Because it's complicated?

Or on the other hand maybe it's because it's complicated, hard to do and challenging?

### D. No! Because it's easy enough to be doable and complicated enough to present reasonable challenge. No matter of your skill level

I believe the truth is somewhere in between. It's easy enough that most of us may have own idea how it should look and at the other hand presents reasonable challenge to fuel our curiosity. We can make it as simple or as complicated as needed. It's only determined by our goal. It mostly depends on what we want to show.

## 3. What good chat app should have?

Ok, but what this kind of app should have? What is complete must and what is optional?

### A. Real time communication

First of all, real time communication. It's something what makes chat app. Without it I don't think we can even call it chat app. No matter if we use HTTP long pooling or Web Sockets it's the more challenging part of whole chat app.

### B. Rooms/user-to-user messaging

Another maybe not necessary but also important part are rooms/direct messages. It elevates user engagement, and also levels up project difficulty.

### C. Emojis ✨🐢🚀✨

Let's state that once for all. Every chat app needs Emoji. You even can't call it chat without them. Period.

### D. Images upload

Next nice to have thing is image upload. Let's be honest, how to use chat app when one can't send mems to their friends? This task has it's own challenges and in most cases requires different solutions than other parts of the app.

### E. Chat-bots

If You like to crank up your app to 11, add chat bot, or better two. This kind of feature often require some external integrations which are always good. It shows the ability to use external sources and to integrate them smoothly.

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

If thats what you want, great. It will work.

## 3. But, on the other hand, if we want to show off

### A. How innovative we are

### B. How disruptive we are

### C. How ours ideas gonna change the world

### D. How our products outmatch competitors

I think it's not the right way. With special care maybe we can show some of this, but it will be hard. In general if we want to impress tech crowd with our skills, it's great. But if one wants to impress VC investors, I don't think that's the solution. Unless you are trying to build next Slack.

## 4. Is it for everyone?

I truly believe the answer is: YES. Of course it depends on our skill level and poses different challenges. But also it can evolve with our skills, what gives us unique opportunities.

### A. Architectural challenges

First of all, Chat app may be more complicated than it looks at the first glance. We have to have in mind all mowing parts, all connections between them. But on the other hand, if our goal is to create working system, so as long as it works and we are not planning to show off our architectural knowledge, we shouldn't care too much.

### B. Scalable OOTB?

Next important question should our chat app be scalable OOTB? Like before it mainly depends on our goal, but in general I think not. It may add unnecessary complications into the system and also may cover main advantages of our design and implementation.

### C. Is my stack the right stack?

Yes. If it works, your stack is right. Even if we see that someone is doing it different, don't get caught in thinking that just because you do something other way you do it wrong. The main idea of this whole app is to show our take on that. So our solutions can be totally different than someone other and it's perfectly right.

## 5. Our try

Ok, enough of that. Now let's talk about how we did it.

### A. Devhouse Chat Rabbitmq Nest Next Lambda - Devhouse Chat

Name is a bit lengthy so we shorten it to Devhouse Chat, but the idea behind it was to reflect main technologies involved.

### B. Architecture && stack

In case of architecture our take is something in between Event Driven and Domain Driven with layered flavour. Backend elements are encapsulated in domain driven way but most of the communication between them is done by events.

1. Client - Frontend part of the system contains Next.js app with help of Material ui, communication with backend happens via web socket (socket.io) and rest api with axios. Application state is handled by Redux Toolkit.
2. Main - nest js - Main element of apps backend is Typescript and Nest.js app. It provides basic structure and nice layer of abstraction to hide some unimportant implementation details (of course unimportant from our point of view).
    * gateways - Nest.js gives us unique opportunity to abstract web sockets implementation into gateways which nicely encapsulates whole logic and allows us to focus on let's call it business logic. In some cases it may not be desired, but here we are perfectly ok with that. Also this gateways pushes events into messaging queue.
    * rabbitMQ - All the events are handled by message broker, RabbitMQ in that case. This solution allows to dispatch events properly and without additional overhead. Also adding RabbitMQ is a nice case of integrating different technologies into the system.
    * rest endpoints - some functionalities also needed rest endpoints to work properly, for example images upload. Of course it's possible to send files via web socket but we've decided against that. It's easier and more transparent to do it in that way.
    * prisma & persistence - Next important element is data persistance. In order to achieve that SQLite database with Prisma orm was used. Maybe sql database is not optimal in chat app but tiny footprint of SQLite is fair tradeoff in that case. Also there are hardly any sophisticated relationships in our database so I don't thing this path we choose will be an issue. Additionally usage of Prisma gives us nice, type safe way to interact with database.
    * file storage - Images upload leaves us with a need for some kind of storage solution. At the beginning we've tried to use Zenko Cloudserver to mimic AWS S3 storage, but after a lot of problems with integration we have pivot to use MinIO Object Storage. After that everything went smoothly and there was no more problems with file storage.
    * axios - In case of ongoing outside communication from Nest.js to other services via http protocol Nest.js http service was used (with axios underneath). That was easy solution and didn't require any additional work, so here decision was easy.

3. Commands & lambdas - Another important part of the whole system are Chat bots. In our case there were two of them, one returning weather info based on supplied location and another one serving random memes from reddit. Here approach was slightly different than in other parts of the system. Serverless framework was used to create AWS lambda functions. Each one was separate endpoint encapsulating whole logic for each bot.
4. Docker, lerna & devcontainer - Last part of the system ties everything up. Services like RabbitMQ and MinIO was closed in docker containers, other parts of the system was also encapsulated in one docker container what allows to use it as development container in vscode. To orchestrate all npm packages Lerna was used. Solution composed like this elevates whole idea of development environment encapsulation to new level.

## 6. Demo

1. Login page - Here we can create user, which is persisted in database. Also current user is stored in redux store and local storage. Currently there's no any other authentication. But username have to be unique. Login process happens via web socket.
2. Rooms messaging - By default there are three rooms created. Hello, nodejs and typescript. Users can freely switch between them.
3. Adding rooms - Also users can create new rooms. This process also happens via web socket, and new rooms are stored in database. And of course rooms names have to be unique.
4. Images upload - Additionally users can message images. They are sent via http, stored in MinIO Object Storage and new message with them is created and send back via websocket.
5. Emoji - As I said before, Emojis are the crucial element of each chat app. It can't work without them. I our case they can be added in two ways. First of all there's a picker, component taken from emoji-mart library. Another way is using emoji short codes which are filtered by emojify method from node-emoji library. They are stored in database like the rest of messages.
6. Chat bots - By typing special commands instead of message user is able to invoke bot response. Two commands are available, first one /Weather followed by place name returns information about temperature in requested place as a response. Another one, /Meme returns random meme form reddit.
7. History persistance - All the messages and images are stored and persistent but to avoid issues with storage of both of them they are persisted only for one hour. On the backend there are couple of scheduled tasks running every minute and deleting messages and images older than 1 hour. It uses Nest.js schedule module.
8. User-to-user communication - Also users can communicate using direct messages. This kind of messages works like private rooms with only two users.
9. 4-6 in user-to-user communication - All the functionalities available in public rooms are also available in direct messages - messages persistence, images, emoji.
10. Known issues -Are there any known issues? Of course there are. First of all there's problem with refreshing history when using dev container on Ubuntu. It works perfectly outside container but there are some problems when started inside. Probably there are some problems with docker networking. (For time being someone other fixed it)

## 7. Is it the best?

Ok, now we have to answer important question. Is our take on chat app the best?

### A. Yes

Of course it is. It has all the necessary functionalities, real time communication, rooms/direct messages, emoji, chat bots. Also has nice encapsulated dev environment. Ten out of ten, and I relay don't know why our company still prefers Slack?

### B. But not relay

Ok, lets get back to reality. Our app also has its flaws. But on the other hand it was nice opportunity to learn and practice new stuff. Also it was possibility to get other perspective, to look on apps design and implementation in different way. So definitely it was worth the time spent.

## 8. Again why you should have your own chat app?

I believe that benefits of having a chat app in our portfolio are clear now. It's nice challenge allowing learn new things while doing fun stuff. Also can create different problems and challenges depending on our skills and goals we've set. It's perfect way to show off good craftsmanship and versatility. And keep on learning what's for me essential in our jobs.

## 9. Thank You

Thank you for attention. Presentation is also available on github pages. Is there any question?
