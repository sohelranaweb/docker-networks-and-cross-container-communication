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

```shell
mongodb+srv://<db_username>:<db_password>@cluster0.tc6xhdw.mongodb.net/?appName=Cluster0
```  

- build and run the docker

```shell
docker build -t ts-docker:v4 .

docker run -p 5000:5000 --name ts-container --rm -w //app -v ts-docker-logs://app/logs -v "//$(pwd)"://app -v //app/node_modules --env-file .env ts-docker:v4
```

- Now container is running and query data from browser like this

![alt text](image.png)