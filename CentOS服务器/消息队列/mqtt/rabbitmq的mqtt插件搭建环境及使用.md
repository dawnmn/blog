**docker 安装**
```
docker pull rabbitmq:3.9-management

docker run -p 15672:15672 -p 5672:5672 -p 1883:1883 -d --hostname my-rabbit --name some-rabbit rabbitmq:3.9-management

docker exec -it 容器id bin/bash
rabbitmq-plugins enable rabbitmq_mqtt

docker restart 容器id
```
访问`http://192.168.126.129:15672`
overview->Ports and contexts->有了`1883`端口
exchanges->创建一个名为`MYMQTT`的`topic`类型交换机
**go客户端**
使用`github.com/eclipse/paho.mqtt.golang`包
```
package main

import (
   "fmt"
   mqtt "github.com/eclipse/paho.mqtt.golang"
   "time"
)

var messagePubHandler mqtt.MessageHandler = func(client mqtt.Client, msg mqtt.Message) {
   fmt.Printf("Received message: %s from topic: %s\n", msg.Payload(), msg.Topic())
}

var connectHandler mqtt.OnConnectHandler = func(client mqtt.Client) {
   fmt.Println("Connected")
}

var connectLostHandler mqtt.ConnectionLostHandler = func(client mqtt.Client, err error) {
   fmt.Printf("Connect lost: %v", err)
}

func main() {
   var broker = "192.168.126.129"
   var port = 1883
   opts := mqtt.NewClientOptions()
   opts.AddBroker(fmt.Sprintf("tcp://%s:%d", broker, port))
   opts.SetClientID("go_mqtt_client")
   opts.SetUsername("guest")
   opts.SetPassword("guest")
   opts.SetDefaultPublishHandler(messagePubHandler)
   opts.OnConnect = connectHandler
   opts.OnConnectionLost = connectLostHandler
   client := mqtt.NewClient(opts)
   if token := client.Connect(); token.Wait() && token.Error() != nil {
      panic(token.Error())
   }

   sub(client)
   publish(client)

   client.Disconnect(250)
}

func publish(client mqtt.Client) {
   num := 10
   for i := 0; i < num; i++ {
      text := fmt.Sprintf("Message %d", i)
      token := client.Publish("topic/MYMQTT", 0, false, text)
      token.Wait()
      time.Sleep(time.Second)
   }
}

func sub(client mqtt.Client) {
   topic := "topic/MYMQTT"
   token := client.Subscribe(topic, 1, nil)
   token.Wait()
   fmt.Printf("Subscribed to topic: %s", topic)
}
```