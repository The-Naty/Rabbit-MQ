# rabbit-mq
send email using RabbitMQ and Node.js with docker and docker-compose.
```
version: "3.2"
services:
  rabbitmq:
    image: rabbitmq:3.8-management-alpine
    container_name: 'rabbitmq'
    ports:
        - 5673:5672
        - 15673:15672
    volumes:
        - ~/.docker-conf/rabbitmq/data/:/var/lib/rabbitmq/
        - ~/.docker-conf/rabbitmq/log/:/var/log/rabbitmq
    networks:
        - rabbitmq_nodejs
  consumer:
    build:
      context: ./
      target: dev
    volumes:
      - .:/src
    depends_on:
      - "rabbitmq"
    command: sh -c '/bin/wait-for-it.sh rabbitmq:5672 --timeout=30 -- node consumer.js'
    environment:
      NODE_ENV: production
      AMQP_URL: amqp://guest:guest@rabbitmq:5672
    networks:
      - rabbitmq_nodejs
networks:
  rabbitmq_nodejs:
    driver: bridge
```
First, we specify a service called rabbitmq that uses an [image from Dockerhub](https://registry.hub.docker.com/_/rabbitmq/).
 Next, we name the container `rabbitmq`.
 
 After that, we expose local port 5673 to container port 5672 and local port 15673 to container port 15672 respectively.
 RabbitMQ runs on port 5672 and the management console web UI runs on port number 15672 of the container.
 Consequently, we map volumes so that our durable queues and logs are not lost on container restart.
 
 Subsequently, we created a bridge network called `rabbitmq_nodejs` that we are going to use later when we publish and consume the message with some Node.js code.
 
 To run the RabbitMQ instance locally with management console enabled, we will run:
 ```
 docker-compose up
 ```
 using the default configuration used by the official RabbitMQ docker image.
 means it will use `guest:guest` for username and password including other default settings. To check if our RabbitMQ is running fine,
 it is best to hit `http://localhost:156763` on the browser.
 
 If you provide the username: `guest` with password: `guest` and hit login, you will get into the RabbitMQ management interface.
 
 using the AMQP library from NPM. To set up a Node.js project and install the AMQP library:-
 ```
 npm init -y
 npm i --save amqplib
```

[Geshan Manandhar Reference](https://geshan.com.np/blog/2021/07/rabbitmq-docker-nodejs/) 
