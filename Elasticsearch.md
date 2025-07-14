https://youtu.be/vxE1aGTEnbE?si=ISSsU3KcUGreUziv

# Elasticsearch
https://habr.com/ru/articles/766674/
https://suchkov.tech/elasticsearch-quick-start/

Elasticsearch — это хранилище документов, c возможностью создавать полнотекстовые индексы для последующего поиска, чаще всего используемое в качестве поискового движка. ElasticSearch добавляет к возможностям библиотеки Apache Lucene, на котором основан, такие функции, как шардирование, репликацию, удобный JSON API и множество других улучшений. Это делает ElasticSearch одним из самых популярных решений для полнотекстового поиска.

### Подключение 
Пример для Maven:
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
</dependency>
```

docker-compose:
```
  version: '3.3'
  services:
    db:
      image: postgres:12.16
      container_name: postgres
      ports:
        - "5433:5432"
      environment:
        - POSTGRES_USER=postgres
        - POSTGRES_PASSWORD=postgres
        - POSTGRES_DB=demo
        - config.support_escapes=true
      volumes:
        - ./pg_data:/var/lib/postgresql/data
        - ./dump:/docker-entrypoint-initdb.d
    odfe-node:
      image: elasticsearch:7.10.1
      logging:
        driver: "json-file"
        options:
          max-size: "1000m"
          max-file: "10"
      container_name: odfe-node
      environment:
        - discovery.type=single-node
        - node.name=odfe-node
        - discovery.seed_hosts=odfe-node
        - bootstrap.memory_lock=true
        - xpack.security.enabled=false
        - "ES_JAVA_OPTS=-Xms4096m -Xmx4096m"
      ulimits:
        memlock:
          soft: -1
          hard: -1
        nofile:
          soft: 65536
          hard: 65536
      volumes:
        - ./elasticsearch_data:/usr/share/elasticsearch/data
      ports:
        - "9200:9200"
        - "9600:9600"
      networks:
        - odfe-net
    kibana:
        image: kibana:7.10.1
        logging:
          driver: "json-file"
          options:
            max-size: "100m"
            max-file: "3"
        container_name: odfe-kibana
        ports:
          - "5601:5601"
        expose:
          - "5601"
        environment:
          ELASTICSEARCH_URL: http://odfe-node:9200
          ELASTICSEARCH_HOSTS: http://odfe-node:9200
        networks:
          - odfe-net
    logstash:
      user: root
      image: docker.elastic.co/logstash/logstash-oss:7.9.1
      logging:
        driver: "json-file"
        options:
          max-size: "100m"
          max-file: "3"
      ports:
        - "5044:5044"
      depends_on:
        - db
        - odfe-node
      environment:
        - PIPELINE_WORKERS=1
        - PIPELINE_BATCH_SIZE=125
        - PIPELINE_BATCH_DELAY=50
      volumes:
        - ./conf/logstash.conf:/usr/share/logstash/pipeline/logstash.conf
        - ./logstash_data:/usr/share/logstash/data
        - ./conf/postgresql-42.6.0.jar:/usr/share/logstash/postgresql-42.6.0.jar
      networks:
        - odfe-net
  networks:
    odfe-net:
  volumes:
    odfe-data:
    db:
    logstash:
```

- image: elasticsearch:7.10.1 - используемый образ для контейнера Elasticsearch
- container_name: odfe-node - имя контейнера
- logging - настройки логгирования контейнера
- environment - переменные окружения, необходимые для настройки контейнера
- ulimits - ограничения ресурсов для контейнера, настройка системных ограничений на контейнер Elasticsearch
- volumes - подключенные директории на хостовой машине
- /elasticsearch_data - директория для хранения данных Elasticsearch
- ports - прокси-порт, который привязывает порт на хостовой машине к порту внутри контейнера
- networks - подключенные сети

Properties
```
spring.elasticsearch.uris=https://search.example.com:9200
spring.elasticsearch.socket-timeout=10s
spring.elasticsearch.username=user
spring.elasticsearch.password=secret
```

## Документы 
В то время как в базе данных SQL строки данных хранятся в таблицах, в Elasticsearch данные хранятся в виде нескольких документов внутри индекса.

## Репозитории Spring Data Elasticsearch
https://javarush.com/quests/lectures/questspringboot.level03.lecture07

Отключить поддержку репозитория можно через 
``
spring.data.elasticsearch.repositories.enabled=false
``

Класс Elasticsearch помечаетеся аннотацией `@Document`
