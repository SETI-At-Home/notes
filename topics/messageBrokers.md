Message brokers are also called queues, busses.

Apache Kafka
RabbitMQ
MQTT (MQ Telemetry Transport)
Apache ActiveMQ
Amazon Simple Queue Service (SQS)
Google Cloud Pub/Sub
Microsoft Azure Service Bus
IBM MQ (formerly known as IBM WebSphere MQ)
Beanstalkd
NATS Streaming

Example of how you might use Azure Service Bus to send and receive messages in C#:

Sending a message:

```
string connectionString = "your_connection_string";
string queueName = "your_queue_name";

QueueClient client = new QueueClient(connectionString, queueName);

string messageBody = "Hello, Service Bus!";

var message = new Message(Encoding.UTF8.GetBytes(messageBody));

client.SendAsync(message).Wait();

client.CloseAsync().Wait();
```

Receiving a message:

```
string connectionString = "your_connection_string";
string queueName = "your_queue_name";

QueueClient client = new QueueClient(connectionString, queueName);

client.RegisterMessageHandler(
    async (message, cancellationToken) =>
    {
        string messageBody = Encoding.UTF8.GetString(message.Body);

        Console.WriteLine(messageBody);

        await client.CompleteAsync(message.SystemProperties.LockToken);
    },
    new MessageHandlerOptions(ExceptionReceivedHandler) { AutoComplete = false });

Console.ReadKey();

client.CloseAsync().Wait();
```

 Example of how you might use RabbitMQ to send and receive messages in C#:

Sending a message:

```
string hostname = "your_hostname";
string queueName = "your_queue_name";

var factory = new ConnectionFactory() { HostName = hostname };
using (var connection = factory.CreateConnection())
using (var channel = connection.CreateModel())
{
    channel.QueueDeclare(queue: queueName, durable: false, exclusive: false, autoDelete: false, arguments: null);

    string message = "Hello, RabbitMQ!";
    var body = Encoding.UTF8.GetBytes(message);

    channel.BasicPublish(exchange: "", routingKey: queueName, basicProperties: null, body: body);
}
```

Receiving a message:

```
string hostname = "your_hostname";
string queueName = "your_queue_name";

var factory = new ConnectionFactory() { HostName = hostname };
using (var connection = factory.CreateConnection())
using (var channel = connection.CreateModel())
{
    channel.QueueDeclare(queue: queueName, durable: false, exclusive: false, autoDelete: false, arguments: null);

    var consumer = new EventingBasicConsumer(channel);
    consumer.Received += (model, ea) =>
    {
        var body = ea.Body;
        var message = Encoding.UTF8.GetString(body);
        Console.WriteLine("Received {0}", message);
    };
    channel.BasicConsume(queue: queueName, autoAck: true, consumer: consumer);

    Console.WriteLine("Press any key to exit.");
    Console.ReadKey();
}
```

Keep in mind that you'll need to replace the placeholder values with your own Azure Service Bus connection string and queue name, or your own RabbitMQ hostname and queue name.
Also, in the examples above I've used some features for simplicity, but in a production scenario, you may want to consider more robust solution

https://www.youtube.com/watch?v=aj9CDZm0Glc&ab_channel=IBMTechnology
https://www.youtube.com/watch?v=Ch5VhJzaoaI&ab_channel=JamesCutajar
https://www.youtube.com/watch?v=W4_aGb_MOls&ab_channel=HusseinNasser
https://www.youtube.com/watch?v=PzPXRmVHMxI&ab_channel=StephaneMaarek
https://www.youtube.com/watch?v=7rkeORD4jSw&ab_channel=IBMTechnology
