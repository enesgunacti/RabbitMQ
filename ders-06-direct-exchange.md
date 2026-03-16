# Ders 6 - Direct Exchange

## İçindekiler

* [Direct Exchange](#direct-exchange)
* [Publisher](#publisher)
* [Consumer](#consumer)

---

# Direct Exchange

* Birçok kuyruk olabilir, farkı routing-key üzerinden anlaşılır.

---

# Publisher

```csharp
using RabbitMQ.Client;
using System.Text;

ConnectionFactory factory = new();

factory.Uri = new("amqp://guest:guest@localhost:5672");

using IConnection connection = await factory.CreateConnectionAsync();

using IChannel channel = await connection.CreateChannelAsync();

await channel.ExchangeDeclareAsync(exchange: "direct-exchange-example", type: ExchangeType.Direct);

while (true)
{
    Console.Write("Enter message: ");
    string message = Console.ReadLine();
    byte[] byteMessage = Encoding.UTF8.GetBytes(message);

    await channel.BasicPublishAsync(
        exchange: "direct-exchange-example",
        routingKey: "direct-queue-example",
        body: byteMessage);
}

Console.Read();
```

---

# Consumer

```csharp
// Bağlantı Oluşturma
using RabbitMQ.Client;
using RabbitMQ.Client.Events;
using System.Text;

ConnectionFactory factory = new();

factory.Uri = new("amqp://guest:guest@localhost:5672");

// Bağlantıyı Aktif Etme
using IConnection connection = await factory.CreateConnectionAsync();

using IChannel channel = await connection.CreateChannelAsync();

// 1. Adım
await channel.ExchangeDeclareAsync(exchange: "direct-exchange-example", type: ExchangeType.Direct);

// 2. Adım
var queueResult = await channel.QueueDeclareAsync();

string queueName = queueResult.QueueName;

// ya da tek satırda:
// string queueName = (await channel.QueueDeclareAsync()).QueueName;

// 3. Adım
await channel.QueueBindAsync(
    queue: queueName,
    exchange: "direct-exchange-example",
    routingKey: "direct-queue-example");

AsyncEventingBasicConsumer consumer = new(channel);

await channel.BasicConsumeAsync(
    queue: queueName,
    autoAck: true,
    consumer: consumer);

consumer.ReceivedAsync += (sender, args) =>
{
    string message = Encoding.UTF8.GetString(args.Body.Span);
    Console.WriteLine($"Gelen Mesaj : {message}");
    return Task.CompletedTask;
};

Console.Read();

// 1. Adım : Publisher'daki exchange ile birebir aynı isim ve type'a sahip bir exchange tanımlanmalıdır!

// 2. Adım : Publisher tarafından routing key'de bulunan değerdeki kuyruğa gönderilen mesajları,
// kendi oluşturduğumuz kuyruğa yönlendirerek tüketmemiz gerekmektedir.
// Bunun için öncelikle bir kuyruk oluşturulmalıdır.

// 3. Adım :
```

---

# Örnek Çıktımız
![Round Robin Example](images/direct-exhange-example.PNG)
