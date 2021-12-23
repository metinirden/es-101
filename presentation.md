---
marp: true
theme: cs
---

<!--color primary #36264b-->
<!--color secondary #0feda1-->

![bg contain](assets/cover.png)

----------

<!-- _class: intro -->
![bg contain](assets/intro.png)


# Elasticsearch-101

![](assets/metin-irden.jpeg) Metin İRDEN


[LinkedIn](https://www.linkedin.com/in/metin-irden/) &nbsp;&nbsp;
[Github](https://github.com/metinirden/) &nbsp;&nbsp;
[Medium](https://metin-irden.medium.com/)

----------


<!-- _class: left -->
## **Giriş**

İnsanlar "Elasticsearch nedir?" sorusunu sorduğunda karşılığında, "**index**", "**search engine**", "**analytics db**", "**big data solution**" ya da "**Google gibi**" cevaplarını alabilir. 

Bu, teknoloji ile aşinalığınza göre bu cevap sizde bir ampülün yanmasına ya da kafanızın daha çok karışmasına yol açabilir. Ama gerçek şudur ki, bu cevapların hepsi doğrudur. Elasticsearch'ü sektörde çekici kılan şeylerin başında bunlar gelir. 

<!-- Londra'da yaşayan Shay Banon kendine bir iş bakarken eşi ise Cordon Bleu'da bir aşçılık kursuna devam etmektedir. Shay boş vakitlerinde, eşinin gittikçe büyüyen tarif listesi için bir search engine yapmaya başlıyor. İlk iterasyonu Compass adı ile başlayıp, ikinci iterasyonda Elasticsearch (Apache Lucene tabanlı) adını alıyor. -->

![bg contain](assets/bg.png)

----------

<!-- _class: left -->
## **Nedir Bu Elasticsearch?**

> Temelde Elasticsearch'ü JSON request alıp işleyen ve size JSON response üreten bir app olarak düşünebilirsiniz.

Elasticsearch, Apache Lucene tabanlı ve Java ile geliştirilmiş distributed, open-source bir search ve analytics engine olarak tanımlanabilir.

İlk versiyonları Lucene'in scale edilebilir hali olarak başlayıp, sonradan Lucene index'lerini kullanarak horizontally scalable olabilmesi sağlanmıştır.

----------

<!-- _class: left -->
Elasticsearch near-realtime olarak petabyte'larca veriyi işleyip/saklayıp üzerinden arama işlemlerini milisaniyeler içinde yapabilmektedir. Bu kadar hızlı cevap üretebilmesini en önemli sebeplerinden biri, direk olarak text üzerinde arama yapmak yerine [inverted-index](https://lucene.apache.org/core/3_0_3/fileformats.html#Inverted%20Indexing) üzerinde arama yapmasıdır.

Tablolar ve şemalar yerine dökümanları kullanır ve geniş çaplı bir REST API endpoint ailesi ile hizmet verir.

![bg contain](assets/bg.png)

----------

<!-- _class: left -->
## **Nasıl Çalışır?**

Elasticsearch'ün nasıl çalıştığını anlayabilmek için bazı mantıksal konseptlerini ve backend komponentlerini incelemek ile başlayabiliriz.

![bg contain](assets/bg.png)

----------

## **Mantıksal Konseptler**

![bg contain](assets/bg.png)

----------

<!-- _class: left -->
## **Documents**

Elasticseach'ün *"Document"* yönelimli olduğundan bahsetmiştik, yani index'leyebileceğiniz ya da search edebileceğiniz en küçük birim budur.

<!-- A document has a few important properties in Elasticsearch -->

- **Self-Contained** bir yapıdadır, yani field isimlerini ve değerlerini bir arada barındırır.
- **Hierarchical** olabilir, bir döküman içinde başka bir dökümanı içerebilir.
- **Flexible Structure**'a sahiptir.

<!-- A value of a
field can be simple, like the value of the location field can be a string. It can also
contain other fields and values. For example, the location field might contain
both a city and a street address within it. -->

<!-- For example, not all events need description values, so that field can be omitted
altogether. But it might require new fields, such as the latitude and longitude of
the location. -->

<!-- A document is normally a JSON representation of your data. As we discussed in chapter 1, JSON over HTTP is the most widely used way to communicate with Elasticsearch -->

<!-- Documents in Elasticsearch are said to be schema-free, in the sense that not all your documents need to have the same fields, so they’re not bound to the same schema. -->

<!-- Although you can add or omit fields at will, the type of each field matters: some are
strings, some are integers, and so on. Because of that, Elasticsearch keeps a mapping
of all your fields and their types and other settings. This mapping is specific to every
type of every index. -->

![bg contain](assets/bg.png)

----------

<!-- _class: left -->
## **Indices**
Index, benzer karakteristiklere sahip bir document kolleksiyonu olarak adlandırılabilir. Bir index, query ile etkileşime geçebileceğiniz en büyük birimdir.

Aynı zamanda mapping type'lar için bir konteynır olarak düşünülebilir. Mapping'den gelen bütün field'ları diskte barındırır. Kendi settings'lerine sahiptir.

Örnek olarak refresh_time verilebilir ki, bu settings yeni eklenen dökümanların ne aralıkla search edilmeye mümkün olacağının ayarıdır. *Near-Realtime* olabilmesinide buna borçludur.

<!-- You saw in chapter 1
that an index can be made up of one or more chunks called shards. This is good for
scalability: you can run Elasticsearch on multiple servers and have shards of the
same index live on all of them. Next, we’ll take a closer look at how sharding works
in Elasticsearch. -->

![bg contain](assets/bg.png)

----------

<!-- _class: left -->
## **Inverted Index**

Elasticsearch'de index ismi ile bahsedilen yapı aslında bir inverted index'dir ki bütün search engine'lerin temelinde var olan bir yapıdır.

Çalışma mantığı ise, içeriğe karşılık gelen kelimeleri hasmap tarzı bir listede tutmasıdır.

![bg contain](assets/bg.png)

----------

| Term      | doc_1 | doc_2 | doc_3 |
| --------- | ----- | ----- | ----- |
| **brown** | x     | x     |       |
| **dog**   |       | x     |       |
| **dogs**  | x     |       |       |
| **jump**  |       | x     | x     |
| **foxes** |       |       | x     |
| **fox**   | x     |       | x     |

![bg contain](assets/bg.png)

----------

<!-- _class: left -->
Inverted index string içerikleri bütün olarak barındırmaz bunun yerine, içeriği tekil search term'lere (**token**) ayırarak store eder.

Örnek olarak, yukardaki tabloda "fox" kelimesi 2 dökümanda geçmektedir. Bu hızlı bir lookup olarak hizmet verir. Elasticsearch distributed inverted index'ler sayesinde full-text search yapısını büyük data setlerinde bile sağlayabilmektedir.

![bg contain](assets/bg.png)

----------

## **Backend Konseptleri**

![bg contain](assets/bg.png)

----------

<!-- _class: left -->
## **Cluster**

Bir Elasticsearch cluster'ı bir veya birden fazla oluşan birbirine bağlı bir node setidir. Elasticsearch'ün temel güçlerinden biri olan search/index tasklarının bütün nodelar üzerine task olarak dağıtılabilmesidir.

![bg contain](assets/bg.png)

----------

<!-- _class: left -->
## **Node**

Node bir cluster'ın tek server'ı olarak adlandırılabilir ve cluster'ın search ve indexing operasyonlarına katkı sağlar. Çeşitli node tipleri;

- **Master Node** Cluster-wide operasyonları yani index oluşturma, node ekleme/çıkarma ve settings gibi olayların yönetilmesinden sorumludur.
- **Data Node** Dataları barındırır ve data ilişkisel operasyonları yönetir.
- **Client Node** Master ve data node'larına ilgili operasyona göre request yönlendirmelerini yapar.

![bg contain](assets/bg.png)

----------

<!-- _class: left -->
## **Shards**

Elasticsearch bir index'i shard adı verilen birden çok parçaya bölebilmeyi desteklemektedir. Her bir shard kendi başına fully-functional ve bağımsız bir index olarak nodelarda tutulur.

Bir index'de bulunan dökümanları shard'lara, shardlarıda cluster üzerindeki node'lara dağıtarak redundancy garanti eder ki buda hem hardware-fail hem de, horizontal scalability olarak karşımıza çıkmaktadır.

![bg contain](assets/bg.png)

----------

<!-- _class: left -->
## **Replicas**

Elasticsearch "replica shards" ya da sadece "replica" adı verilen shard'ların kopyalarının oluşturulmasına izin vermektedir. Basit olarak primary shard'ların kopyalarıdır. Primary shard'ların kaybolması durumunda replica yerine primary konumuna geçebilir.
 
![bg contain](assets/bg.png)

----------

## **Analyzing**

![bg contain](assets/bg.png)

----------

<!-- _class: left -->
Analiz elasticsearch'ün document body'sine inverted index'e eklemeden önce yaptığı operasyondur. Çeşitli adımlardan oluşan bir pipeline'dır.

![bg contain](assets/bg.png)

----------

<!-- _class: left -->
- **Character Filtering**: Karakter bazında transforming.
- **Tokenizing**: Text'i belirlenen metoda göre bir yada birden fazla token'e çevirme.
- **Token Filtering**: Token bazında transforming.
- **Token Indexing**: Token'ların index'de store edilmesi.

![bg contain](assets/bg.png)

----------

## **Relevancy**

![bg contain](assets/bg.png)

----------

<!-- _class: left -->

Free-text dünyasında bir document'ı query'e match edebilme konusu farklı storage ve search engineler tarafından ele alınmıştır. Elasticsearch'ü diğerlerinden önemli kılan, match edilen document'lara score atayabilmesidir. Bu sayede orjinal score'a göre gelen document'ların alaka seviyesini anlayabilisiniz.

![bg contain](assets/bg.png)

----------

<!-- _class: left -->
## **Term Frequency**

Aranan kelimenin document başına içinde ne kadar geçtiği ile ilgili istatistikdir.

![bg contain](assets/bg.png)

----------

<!-- _class: left -->
## **Inverse Term Frequency**

Aranan kelimenin diğer document'larda geçip geçmediğinin istatistiğidir, burada diğer documentlarda bir yada birden fazla geçmesi önemli değildir.

![bg contain](assets/bg.png)

----------

## **Performance**

![bg contain](assets/bg.png)

----------

<!-- _class: left -->

- Bulk ve multisearch APIs
- Refresh, Flush ve Merge
- Filter Caches
- Query Warmers
- Balancing JVM Heap Size and OS Caches


<!-- - Once it’s defined, the warmer will make Elasticsearch
run the query with every refresh operation. This will slow down the refresh, but the
user queries will always run on “warm” caches. -->

<!-- If Elasticsearch doesn’t have enough heap to finish an operation, it throws an out-ofmemory exception that effectively makes the node crash and fall out of the cluster -->
<!-- Caching, A heap size of more than 32 GB will automatically make
pointers uncompressed and waste memory.  -->
![bg contain](assets/bg.png)

----------

## **Cluster Yönetimi**

![bg contain](assets/bg.png)

----------

<!-- _class: left -->
- Monitoring
- Backup ve Restore Kullanımı
- Cross-cluster Replication

![bg contain](assets/bg.png)

----------

<!-- _class: left -->
# **Devam Edilebilecek Kaynaklar**

[Elastic Docs](https://www.elastic.co/guide/index.html)
[Awesome Elasticsearch](https://github.com/dzharii/awesome-elasticsearch)

![bg contain](assets/bg.png)

----------

#### Soru & Cevap

<!-- _class: custom-img-30 -->
![](assets/faq.png)


![bg contain](assets/bg.png)


----------

### Teşekkürler :fireworks:


![bg contain](assets/bg.png)