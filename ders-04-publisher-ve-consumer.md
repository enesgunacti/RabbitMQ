# Ders 4 - Publisher & Consumer

## İçindekiler

* [Genel Mantık](#genel-mantık)
* [Publisher İşlem Sıralaması](#publisher-işlem-sıralaması)
* [Publisher Kod Örneği](#publisher-kod-örneği)
* [Consumer İşlem Sıralaması](#consumer-işlem-sıralaması)
* [Consumer Kod Örneği](#consumer-kod-örneği)

---

# Genel Mantık

* RabbitMQ’daki temel mantık Publisher ile bir mesaj iletmek ve farklı bir servis ile de bu iletilen mesajı Consumer aracılığıyla tüketmektir.
* C# .NET teknolojileriyle çalışacağız. (2 adet console projesi açmak gerekiyor: Publisher ve Consumer)
* **RabbitMQ.Client** kütüphanesini yükleyin.

Package Manager Console:

```bash
NuGet\Install-Package RabbitMQ.Client -Version 7.2.1
```

Versiyon değişiklik gösterebilir:
https://www.nuget.org/packages/rabbitmq.client/

---

# Publisher İşlem Sıralaması

* Bağlantı oluşturma
* Bağlantıyı aktifleştirme ve kanal açma
* Queue oluşturma
* Queue’ya mesaj gönderme

RabbitMQ 7.X kütüphanesi sonrası bağlantı ve channel açmada ufak değişiklikler vardır.

---

# Publisher Kod Örneği

factory.Uri ben localde çalıştığım için bu şekilde.

```csharp
ConnectionFactory factory = new();

factory.Uri = new("amqp://guest:guest@localhost:5672");

using IConnection connection = await factory.CreateConnectionAsync();

IChannel channel = await connection.CreateChannelAsync();

// Queue Oluşturma
await channel.QueueDeclareAsync(queue: "example-queue", exclusive: false);

// Queue'ya mesaj gönderme
byte[] message = Encoding.UTF8.GetBytes("Merhaba");

await channel.BasicPublishAsync(exchange: "", routingKey: "example-queue", body: message);

Console.Read();
```

Sistemi çalıştırınca gerekli adresten RabbitMQ portalına bağlanıp örnek Queue’muzu görebiliriz.
Aşağıdaki resimde olduğu gibi.

![RabbitMQ Queue Example](images/eaxmple-queue.PNG)

---

# Consumer İşlem Sıralaması

* Bağlantı oluşturma
* Bağlantıyı aktifleştirme ve kanal açma
* Queue oluşturma
* Queue’dan mesaj okuma

Consumer’da neden queue oluşturuyoruz?
Consumer’da Queue oluşturmak Publisher’daki Queue’yu Consumer’da tanımlamışız anlamına gelir.

---

# Consumer Kod Örneği

```csharp
ConnectionFactory factory = new();

factory.Uri = new("amqp://guest:guest@localhost:5672");

// Bağlantı Aktif Etme
using IConnection connection = await factory.CreateConnectionAsync();

using IChannel channel = await connection.CreateChannelAsync();

// Queue Oluşturma
await channel.QueueDeclareAsync(queue: "example-queue", exclusive: false);
// Consumer’daki kuyruk yapılanması Publisher’dakiyle birebir aynı yapılandırmayla olmalıdır.

// Queue'dan Mesaj Okuma
AsyncEventingBasicConsumer consumer = new(channel);

await channel.BasicConsumeAsync(queue: "example-queue", false, consumer: consumer);

consumer.ReceivedAsync += async (sender, e) =>
{
    // Kuyruğa gelen mesajın işlendiği yerdir.

    // e.Body : Kuyruktaki mesajın verisini bütünsel olarak getirecektir.

    // e.Body.Span veya e.Body.ToArray() : Kuyruktaki mesajın verisini byte[] olarak getirecektir.

    Console.WriteLine(Encoding.UTF8.GetString(e.Body.Span));
};

Console.Read();
```

Her şeyi doğru yaptığımızda console’da aşağıdaki resimdeki gibi bir çıktı görmemiz gerekiyor.

![RabbitMQ Console Output](images/example-consumer.PNG)
