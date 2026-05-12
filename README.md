# Docker Networks and cross Contianer Communication

## Docker Network Key Points

<aside>
💡

Remote DB in WWW

```jsx
DB_URL=mongodb+srv://toufgsdfgbb:tour-admindefca@cluster0.v6dfbfz.mongodb.net/cloudinary-multer?retryWrites=true&w=majority
```

</aside>






## 4 - 6 Creating A Container And Communicating To Web (WWW)

- At first add a mongodb database link like:

```jsx
mongodb+srv://<db_username>:<db_password>@cluster0.tc6xhdw.mongodb.net/?appName=Cluster0
```  

- build and run the docker

```shell
docker build -t ts-docker:v4 .

docker run -p 5000:5000 --name ts-container --rm -w //app -v ts-docker-logs://app/logs -v "//$(pwd)"://app -v //app/node_modules --env-file .env ts-docker:v4
```

- Now container is running and query data from browser like this

![alt text](image.png)


## 4 - 7 Making Container To Host Container Communication

At first add a mongodb localhost database link in .env file like this:

```jsx
DB_URL=mongodb://localhost:27017/ts-docker-db
``` 
- Now running the container with this commnad: 

```shell
docker run -p 5000:5000 --name ts-container --rm -w //app -v ts-docker-logs://app/logs -v "//$(pwd)"://app -v //app/node_modules --env-file .env ts-docker:v4
```

### After running the container, I can see an Error. 

![alt text](image-1.png)

- Here, we encountered an error.
The container kept trying to connect to the database for a long time, but it failed to establish the connection.

### So, what is the actual reason behind this problem?

The reason is that we used localhost in the database URL. Our MongoDB database is installed and running on the local machine, not inside the Docker container.

As I mentioned before, the container environment is completely separate from the local machine environment. Whatever happens inside the container is isolated from the host machine.

Now, MongoDB is installed on my local machine, but there is no MongoDB installation inside the container. When the application inside the container tries to connect using localhost, it actually searches for MongoDB inside the container itself.

But since MongoDB is not installed there, the container cannot find any database service to connect to. That is why the connection fails and an error is thrown.

The container does not automatically know that MongoDB is running on the host machine. From the container’s perspective, localhost refers only to the container itself, not the host computer.

### So, how can we solve this problem?

✔ Solution: Connect to Host Machine Database

If MongoDB is running on the host machine, use:

```jsx 
DB_URL=mongodb://host.docker.internal:27017/ts-docker-db
```
### Replace localhost with host.docker.internal
- This allows the container to access the host machine database.
- Now run the container again and successfully connected local or host machine database


## 4 - 8 A basic solution for container to container communication

- Let’s apply our third and final case. That means we will try to see how two containers can communicate with each other. In this case, since we already have our backend, we will create a database container and then try to establish communication between the backend container and the database container.

In the next module, we will also create a frontend container and connect it with the backend container.

So, what have we done so far?

First, we tried to connect our backend with the remote database URL that we got from MongoDB Atlas. The container connected very easily because Docker or containers can establish connections with the World Wide Web by default.

Then, we saw that when we tried to connect our local MongoDB database from the host machine to the container, it did not work. The reason is that inside a container, localhost refers to the container itself, not the host machine.

In that case, we had to use host.docker.internal instead of localhost. By using this address, the container was able to open a gateway through which it could establish a connection with the IP address of the host machine.

Now we will see that if we keep the database inside another container, then how the backend container can communicate with that database container.

For that, first we need to create a MongoDB container. To do this, we will use the official MongoDB Docker image.

1. Go to the offical website: https://hub.docker.com/_/mongo
2. run this commant for pulling mongo image from official imag
```shell
docker pull mongo
```
3. Run the mongo image

```shell
docker run --name mongodb --rm mongo 
```
- Why didn’t we publish any port?

When we install MongoDB on our local machine, it always runs by default on port 27017. Now, since we are creating a MongoDB container, MongoDB inside that container will also automatically run on the same port, 27017.

So, we do not need to publish the port separately. In other words, we do not need to do this extra step like -p 5000:5000.


### How can I connect to mongo image to the backend container:
- But should we use host.docker.internal here, or should we use localhost? This might be a question in your mind.

First, we can try using localhost. The question is: what will this localhost try to connect to? Actually, localhost will try to connect to the local host or IP address of its own container.

So, what do we mean by localhost? localhost refers to the IP address of the machine or environment where the application or container is currently running.

Now, when we run a project directly on our local computer, our computer itself becomes that environment. So, in that case, localhost refers to our own computer or our computer’s IP address.

But here, if I use localhost inside my backend container, the backend container will think that localhost refers to its own internal environment or IP address. However, there is no MongoDB installed inside the backend container itself. So, it is clear that localhost will not work here.

DB url:
```jsx
DB_URL=mongodb://localhost:27017/ts-docker-db
```
Run the container:
```shell
docker run -p 5000:5000 --name ts-container --rm -w //app -v ts-docker-logs://app/logs -v "//$(pwd)"://app -v //app/node_modules --env-file .env ts-docker:v4
```
- I can see this error:

![alt text](image-2.png)

- Let’s try it practically. But before that, I will do one thing: I will stop the MongoDB service running on my local machine.

To do that, I can go to the Services option in Windows. I will search for “Services” in Windows, open it, scroll down, find the MongoDB Server service, and stop it.

After stopping it, I will first try running the container using host.docker.internal to see whether the container can connect or not.

Now it is trying to run, but you can see that it is stuck. Since I stopped the MongoDB service on my local machine, it is taking some time, and after a while it will throw an error.

In the meantime, I will stop the container because I already know that it is not going to work.

DB url:
```jsx
DB_URL=mongodb://host.docker.internal:27017/ts-docker-db
```
Run the container:
```shell
docker run -p 5000:5000 --name ts-container --rm -w //app -v ts-docker-logs://app/logs -v "//$(pwd)"://app -v //app/node_modules --env-file .env ts-docker:v4
```
- I can see this error:

![alt text](image-3.png)

- Before stopping completely, you can already see the error message. That means the host.docker.internal address could not connect to the database. So now I am sure that there is no MongoDB running on my local machine.

Now if I use localhost, many of us may think that maybe it will try to communicate with the MongoDB container that is already running. Because inside that MongoDB container, MongoDB is also running on localhost.

If we check again using docker ps, we can see that the MongoDB container is running. So theoretically it may seem like using localhost here should work. Let’s try it again.

But once again, you can see that it is stuck. You already know the reason: this localhost is trying to resolve the IP address inside its own backend container. But there is no MongoDB inside the backend container. That is why it cannot connect.

Naturally, it will throw an error, so I stop the container again.

Now a question remains:

host.docker.internal is not working
localhost is not working either

So how can we communicate with another running container?

Whenever we run a container, that container behaves like a separate computer. And every container has its own IP address.

So, if we can somehow find the IP address of that MongoDB container, then instead of localhost, we can use that IP address. In that case, the backend container will be able to communicate with the MongoDB container successfully.

### To do this, we can use a command called:

So we run:
```shell
docker container inspect mongodb
```
- After running this command, Docker will return a very large amount of information. We do not need all of it. Near the bottom, we will find something called IPAddress.

![alt text](image-4.png)

From there, I can copy the IP address. In your case, the number may be different from mine.

After copying the IP address, I replace localhost with that IP address in my database URL.

```jsx
DB_URL=mongodb://172.17.0.2:27017/ts-docker-db
```

Now, when I run the backend container again, we can see that the database is connected successfully.

That means the IP address we found using docker inspect belongs to the running MongoDB container. By using that IP address, our backend container was able to establish communication with the MongoDB container successfully.

So this is one way containers can communicate with each other. We find the IP address of one container and use it instead of localhost. Then one container can easily connect to another container.

However, this process is a bit inconvenient because every time we need to inspect the container, find the IP address, and manually use it.

So in the next video, we will see how to make this process much easier.

## 4 - 9 Introduction to docker networks 

![alt text](image-5.png)

- Suppose we were working with just a single container. But in the last video, for the first time, we worked with two containers and saw how two containers can communicate with each other.

In that case, we needed the IP addresses of those containers. And we were able to find them using the docker container inspect command.

However, this whole process is a bit inconvenient. Also, the IP addresses of containers can change over time. So there is no fixed system or guarantee about what the IP address will look like.

Because of this, we can use another approach, which is Docker Networking.

Now think about it: suppose you have multiple containers, such as a frontend container, a backend container, and a database container. How would you connect all these containers together?

Would you search for one container’s IP address and manually use it inside another container instead of localhost? That would be difficult to manage.

But Docker gives us a feature called Docker Network. Using this feature, we can create a custom network or a custom boundary. If we place all three containers inside that same network, then they will be able to communicate with each other very easily without dealing with IP addresses manually.

For that, we need to use a command. While running a container with Docker, we can additionally use the --network option and specify the custom network name that we created.

```shell
docker run --network my_network
```

Then, without any IP address hassle, the containers will be able to communicate with each other simply by using their container names.

That means we no longer need to manually inspect containers and copy IP addresses like we were doing earlier.

So first, to do this, we need to create a network.

Now let’s move to our code.

1. Let's learn new commmands 

```shell
docker network --help
```
![alt text](image-6.png)

2. Let's create network by this command:
```shell
docker network create ts-docker-network
``` 
3. Let's check network list 
```shell
docker network ls
``` 
- We can see all networks by own created and by default docker created network as well
![alt text](image-7.png) 


- Earlier, we started with this technique using:
```shell
docker run --name mongodb --rm mongo
``` 
- Now, while running this container, we need to place it inside the custom network that we created. That means we want this container to run within our own custom network boundary.

So after docker run, we will use the --network option and provide the name of our custom network. Then the MongoDB container will run inside that specific network boundary.

```shell
docker run --name mongodb --rm --network ts-docker-network mongo
``` 
- Now our MongoDB container is running.

- Next, let’s go to the .env file.

Earlier we run databse like this:
1. mongdb local ip address
```jsx
DB_URL=mongodb://localhost:27017/ts-docker-db
```
2. host.docker.internal
```jsx
DB_URL=mongodb://host.docker.internal:27017/ts-docker-db
```
3. network ip address like: 172.17.0.2
```jsx
DB_URL=mongodb://172.17.0.2:27017/ts-docker-db
```
- Now it will be just container name: 

```jsx
DB_URL=mongodb://mongodb:27017/ts-docker-db
```
So instead of using the IP address in the database URL, we can simply write mongodb.
That means earlier, where we were replacing localhost with an IP address, now we can just use the container name. Docker will automatically resolve that container name into the correct IP address internally.

As a result, even if the container IP address changes dynamically, we do not need to worry about it anymore.

Now, since we replaced the host with mongodb, let’s run the backend container again.

- Since we already placed MongoDB inside a network, we also want our backend container to run inside that same network.

Earlier, we ran the backend container using a Docker command. Now we will also add the --network option there and provide the same network name.

So now both containers are inside the same network.

When two containers stay inside the same network or boundary, we no longer need to manually provide the IP address. Instead, we can simply use the container name.

- Run this command to start backend container with same network:
```shell
docker run -p 5000:5000 --name ts-container --rm -w //app -v ts-docker-logs://app/logs -v "//$(pwd)"://app -v //app/node_modules --env-file .env --network ts-docker-network ts-docker:v4
```

- The server connects successfully.

So this is how we can place multiple containers inside the same Docker network and make them communicate with each other simply by using container names.

Now think about the frontend-backend example we imagined earlier. Suppose we containerized the frontend too. If the frontend and backend containers are inside the same network

So from the next module onward, we will keep the frontend, backend, and database containers inside the same network boundary and see how they communicate with each other.

In the next video, we will try to understand how Docker or the environment actually resolves these names internally. For example:

- how localhost becomes 127.0.0.1
- how container names are resolved into IP addresses
- and how Docker networking handles all of this behind the scenes.

See you in the next video.


## 4 - 10 Docker Network Management and How Docker Resolves IP Address

![alt text](image-8.png)

- We have now seen how multiple containers can work together through networking.

The main things we understood here are:

- If we want a container to communicate with the outside world, that is completely fine.
- If we want a container to communicate with our local machine, that is also possible. In that case, we just need to use the host.docker.internal address.
- But if we want one container to communicate with another container, that is not possible by default. For that, we need to place both containers inside the same Docker network. Only then can those containers communicate with each other easily.

Now, another important thing is that even without creating a network, we could still make containers communicate by using the IP address of a container.

However, that approach is inconvenient because container IP addresses can change dynamically.

That is why Docker provides the Docker Network feature. Using this feature, we can place multiple containers inside the same network and allow them to communicate with each other easily.

In that case, we no longer need IP addresses. Instead, we can directly use the container name in place of localhost.

So rather than writing an IP address, we simply use the container name as the address, and Docker automatically resolves it internally.

This is how we can easily establish communication between containers.

Now, while working with Docker networking, we also saw some important concepts. One of them is how to create a Docker network.

We already know that we can create a network using a command:

```shell
docker network create ts-docker-network
```
- How to delete existing network like this command:
- single network delete
```shell
docker network rm (Network Id)
```
- multiple delete 
```shell
docker network prune
```

![alt text](image-9.png)