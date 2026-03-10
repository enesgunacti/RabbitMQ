# Ders 1 - Message Queue Temelleri

## İçindekiler

* [Message Queue Nedir?](#message-queue-nedir)
* [Amacı](#amacı)
* [Senkron – Asenkron iletişim](#senkron--asenkron-iletişim)
* [Message Broker](#message-broker)
* [RabbitMQ](#rabbitmq)
* [Niye Kullanmalıyız?](#niye-kullanmalıyız)

---

## Message Queue Nedir?

* Birbirinden bağımsız sistemlerin arasında veri alış-verişi yapmak için kullanılan teknolojidir.
* Kuyruk sistemi kullanıcıyı beklemekten kurtarmak için kullanılır.
* Gönderilen verilere data değil message diyoruz.
* Message Queue yapısı mimaride asenkron bir davranış sergilenmesini sağlar.
* E-Ticaret sitelerinde ki Ödeme sonrası yapılacak işlemler için (Fatura oluşturma, Email Gönderme, Kullanıcının Siparişlerini Güncelleme) hem kullancıyı bekletmemek hemde sistemi rahatlatmak için kullanılır.
* Messagelar sırasıyla işlenir klasik FIFO (First in First out) yapısı

## Amacı

* Soru sordum cevabını aldım (senkron) mantığı her senaryo için doğru bir yaklaşım değil. Asenkron yapının gerekliliği de bunun için önemli. Örnek olarak Ödeme yaptıktan sonra fatura oluşturmak için beklemek ve bunu kullanıcıya yansıtmak doğru bir yaklaşım değil.
* Ben siparişi aldım. Kullanıcıyla işim artık bitti. Geri kalan işlemleri arkada asenkron olarak tamamlayacağım. İşlemler bittikçe kullanıcıyı bilgilendirmem gerekirse bilgilendireceğim. (SMS , Mail vs.)
* Başka servislerin işlerini kullanıcıya yansıtmak doğru değil

## Senkron – Asenkron iletişim

* Haberleşmede response bekleniyorsa Senkron beklenmiyorsa Asenkron iletişim modeli diyoruz.
* İş Arama sürecinde başvuruları yapmak Asenkron iletişimdir. Cevap gelene kadar Bilgisayar başında Haftalarca beklemeyiz

## Message Broker

* Publisher (message’ı gönderen) Consumer (message’ı alan) arasındaki iletişimi sağlayan genel sistemin adıdır. Birden fazla Message Queue içerebilir. Hangi Consumer hani queue ile işlem yapacaksa ona gelen message sonrası işlemlerine devam eder

* Örnek: RabbitMQ, Kafka, Redis vs.

## RabbitMQ

* Open Source, Cross Platform Desteği, Kaliteli Dokümantasyon, Cloud hizmeti mevcuttur.

## Niye Kullanmalıyız?

* Ölçeklendirilebilir ortam -> İşleyişi ölçeklendiriliyoruz. Fatura sonrası işlemleri ölçeklendirdik aradaki iletişimi sağlamak için de bu sistemi kullanıyoruz. Ölçeklendirme sonucu kullanıcıyı da bekletmemiş oluyoruz.
* İdeal bir uygulama kullanıcı’nın zamanını sömürmez. Bu davranışı sergileyebilmek için de Message Quening yapısına ihtiyacım var. Bu ihtiyacı giderebilmek içinde RabbitMQ kullanmam gerekiyor.
