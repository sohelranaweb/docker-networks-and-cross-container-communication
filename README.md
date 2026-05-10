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
### host.docker.internal instead of localhost
- This allows the container to access the host machine database.
- Now run the container again and successfully connected local or host machine database