# Ders 7 - Fanout Exchange

## İçindekiler

* [Fanout Exchange](#fanout-exchange)
* [Publisher](#publisher)
* [Consumer](#consumer)

---

# Fanout Exchange

* Kuyruk ismi dikkate alınmaz ve mesajlar tüm kuyruklara iletilir.
* Farklı türden bir exchange’i olan kuyruğumuz bu mesajları almayacaktır.

---

# Publisher

```csharp id="p9x2kl"
using RabbitMQ.Client;
using System.Text;

ConnectionFactory factory = new();

factory.Uri = new("amqp://guest:guest@localhost:5672");

using IConnection connection = await factory.CreateConnectionAsync();

using IChannel channel = await connection.CreateChannelAsync();

await channel.ExchangeDeclareAsync(
    "fanout-exchange-example",
    type: ExchangeType.Fanout); // ismi değil type'ı fanout olmalı 

for (int i = 0; i < 100; i++)
{
    await Task.Delay(200);

    byte[] message = Encoding.UTF8.GetBytes($"Message {i}");

    await channel.BasicPublishAsync(
        exchange: "fanout-exchange-example",
        routingKey: string.Empty, // boş geçiyoruz çünkü fanout exchange kullanıyoruz
        body: message);
}

Console.Read();
```

---

# Consumer

```csharp id="k3t8zn"
using RabbitMQ.Client;
using RabbitMQ.Client.Events;

// Bağlantı Oluşturma

ConnectionFactory factory = new();

factory.Uri = new("amqp://guest:guest@localhost:5672");

// Bağlantıyı Aktif Etme
using IConnection connection = await factory.CreateConnectionAsync();

using IChannel channel = await connection.CreateChannelAsync();

await channel.ExchangeDeclareAsync(
    "fanout-exchange-example",
    type: ExchangeType.Fanout);

Console.WriteLine("Kuyruk adını giriniz");

string queueName = Console.ReadLine();

await channel.QueueDeclareAsync(
    queue: queueName,
    exclusive: false
);

await channel.QueueBindAsync(
    queue: queueName,
    exchange: "fanout-exchange-example",
    routingKey: string.Empty
);

AsyncEventingBasicConsumer consumer = new(channel);

await channel.BasicConsumeAsync(
    queue: queueName,
    autoAck: true,
    consumer: consumer
);

consumer.ReceivedAsync += (sender, args) =>
{
    string message = System.Text.Encoding.UTF8.GetString(args.Body.Span);
    Console.WriteLine($"Gelen mesaj: {message}");
    return Task.CompletedTask;
};

Console.Read();
```

---

# İlgili Çıktı Görseli

![Fanout Exchange Output](images/fanout-exhange.PNG)
