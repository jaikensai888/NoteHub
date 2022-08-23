# RabbitMq

## 1、简介

### 1.1、默认端口

> 15672:RabbitMq管理工具端口  (guest/guest)

```
启用管理工具命令
rabbitmq-plugins.bat enable rabbitmq_management
```

> 5672:RabbitMq服务端口

## 2、安装

* docker 环境
  
  ```shell
  # 下载启用管理补丁的镜像
  docker pull rabbitmq:management  
  # 运行启动 启用后直接访问localhost:15672
  docker run -dit -p 15672:15672 -p 5672:5672 --name rabbittest rabbitmq:management 
  ```

    * windows环境

## 3、命令使用

```shell
# 查看RabbitMQ当前状态
rabbitmqctl status  
```

## 4、RabbitMq模式介绍

### 4.1、helloWord模式

![(P) -> [|||] -> (C)](https://www.rabbitmq.com/img/tutorials/python-one.png)

> P:为生产者--发送消息的程序是生产者
> 
> C:为消费者--消费与接收具有相似的含义。消费者是等待接收消息的程序
> 
> HelloWord模式:生产者发送消息到队列 ，而消费者等待队列消息进行消费

* 生产者代码代码
  
  ```c#
  class Send
  {
      public static void Main()
      {
          var factory = new ConnectionFactory() { HostName = "localhost" };
          using(var connection = factory.CreateConnection())
          using(var channel = connection.CreateModel())
          {
              channel.QueueDeclare(queue: "hello",
                                   durable: false,
                                   exclusive: false,
                                   autoDelete: false,
                                   arguments: null);
  
              string message = "Hello World!";
              var body = Encoding.UTF8.GetBytes(message);
  
              channel.BasicPublish(exchange: "",
                                   routingKey: "hello",
                                   basicProperties: null,
                                   body: body);
              Console.WriteLine(" [x] Sent {0}", message);
          }
  
          Console.WriteLine(" Press [enter] to exit.");
          Console.ReadLine();
      }
  }
  ```

* 消费者代码
  
  ```c#
  class Receive
  {
      public static void Main()
      {
          var factory = new ConnectionFactory() { HostName = "localhost" };
          using(var connection = factory.CreateConnection())
          using(var channel = connection.CreateModel())
          {
              channel.QueueDeclare(queue: "hello",
                                   durable: false,
                                   exclusive: false,
                                   autoDelete: false,
                                   arguments: null);
  
              var consumer = new EventingBasicConsumer(channel);
              consumer.Received += (model, ea) =>
              {
                  var body = ea.Body.ToArray();
                  var message = Encoding.UTF8.GetString(body);
                  Console.WriteLine(" [x] Received {0}", message);
              };
              channel.BasicConsume(queue: "hello",
                                   autoAck: true,
                                   consumer: consumer);
  
              Console.WriteLine(" Press [enter] to exit.");
              Console.ReadLine();
          }
      }
  }
  ```
