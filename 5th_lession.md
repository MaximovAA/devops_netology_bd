it## Задача 1

В этом задании вы потренируетесь в:

- установке Elasticsearch,
- первоначальном конфигурировании Elasticsearch,
- запуске Elasticsearch в Docker.

Используя Docker-образ [centos:7](https://hub.docker.com/_/centos) как базовый и 
[документацию по установке и запуску Elastcisearch](https://www.elastic.co/guide/en/elasticsearch/reference/current/targz.html):

- составьте Dockerfile-манифест для Elasticsearch,
- соберите Docker-образ и сделайте `push` в ваш docker.io-репозиторий,
- запустите контейнер из получившегося образа и выполните запрос пути `/` c хост-машины.

Требования к `elasticsearch.yml`:

- данные `path` должны сохраняться в `/var/lib`,
- имя ноды должно быть `netology_test`.

В ответе приведите:

- текст Dockerfile-манифеста,
- ссылку на образ в репозитории dockerhub,
- ответ `Elasticsearch` на запрос пути `/` в json-виде.

Подсказки:

- возможно, вам понадобится установка пакета perl-Digest-SHA для корректной работы пакета shasum,
- при сетевых проблемах внимательно изучите кластерные и сетевые настройки в elasticsearch.yml,
- при некоторых проблемах вам поможет Docker-директива ulimit,
- Elasticsearch в логах обычно описывает проблему и пути её решения.

Далее мы будем работать с этим экземпляром Elasticsearch.

```
В связи с недоступностью  прямого скачивания использовал проброс файлов эластика с хостовой машины.
При необходимости можно в Dockerfile использовать echo "node.name: netology_test" >> /elasticsearch-8.8.2/config/elasticsearch.yml

Основные параметры в конфиге:

node.name: netology_test
network.host: 0.0.0.0
http.port: 9200
xpack.security.enabled: false
cluster.initial_master_nodes: ["netology_test"]

Dockerfile:

FROM centos:7
RUN useradd -U elastic
RUN mkdir /elasticsearch-8.8.2
RUN chown -R elastic:elastic /elasticsearch-8.8.2
RUN mkdir /var/lib/nodes
RUN chown -R elastic:elastic /var/lib/nodes

docker-compose:

services:
  netology_test:
    build:
      dockerfile: Dockerfile
    volumes:
      - /home/amaksimov/elasticsearch-8.8.2:/elasticsearch-8.8.2
    tty: true
    environment:
      ELASTIC_USERNAME: elasticsearch
      ELASTIC_PASSWORD: 12345
    ports:
      - "9200:9200"
      - "9300:9300"
    volumes:
      elastic:

http://192.168.0.24:9200/

name	"netology_test"
cluster_name	"elasticsearch"
cluster_uuid	"Exo3nqJkT6KzKPAP7_kQlQ"
version	
number	"8.8.2"
build_flavor	"default"
build_type	"tar"
build_hash	"98e1271edf932a480e4262a471281f1ee295ce6b"
build_date	"2023-06-26T05:16:16.196344851Z"
build_snapshot	false
lucene_version	"9.6.0"
minimum_wire_compatibility_version	"7.17.0"
minimum_index_compatibility_version	"7.0.0"
tagline	"You Know, for Search"


```
https://hub.docker.com/layers/maximofaa/netologyvirt/netology_test/images/sha256-0dbe82f20fafcc89f18cfb23b4ddfe9a468cee2e87001602be6f75dfcd43cb6f?context=explore

## Задача 2

В этом задании вы научитесь:

- создавать и удалять индексы,
- изучать состояние кластера,
- обосновывать причину деградации доступности данных.

Ознакомьтесь с [документацией](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-create-index.html) 
и добавьте в `Elasticsearch` 3 индекса в соответствии с таблицей:

| Имя | Количество реплик | Количество шард |
|-----|-------------------|-----------------|
| ind-1| 0 | 1 |
| ind-2 | 1 | 2 |
| ind-3 | 2 | 4 |

Получите список индексов и их статусов, используя API, и **приведите в ответе** на задание.

Получите состояние кластера `Elasticsearch`, используя API.

Как вы думаете, почему часть индексов и кластер находятся в состоянии yellow?

Удалите все индексы.

**Важно**

При проектировании кластера Elasticsearch нужно корректно рассчитывать количество реплик и шард,
иначе возможна потеря данных индексов, вплоть до полной, при деградации системы.

```

```



## Задача 3

В этом задании вы научитесь:

- создавать бэкапы данных,
- восстанавливать индексы из бэкапов.

Создайте директорию `{путь до корневой директории с Elasticsearch в образе}/snapshots`.

Используя API, [зарегистрируйте](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshots-register-repository.html#snapshots-register-repository) 
эту директорию как `snapshot repository` c именем `netology_backup`.

**Приведите в ответе** запрос API и результат вызова API для создания репозитория.

Создайте индекс `test` с 0 реплик и 1 шардом и **приведите в ответе** список индексов.

[Создайте `snapshot`](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshots-take-snapshot.html) 
состояния кластера `Elasticsearch`.

**Приведите в ответе** список файлов в директории со `snapshot`.

Удалите индекс `test` и создайте индекс `test-2`. **Приведите в ответе** список индексов.

[Восстановите](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshots-restore-snapshot.html) состояние
кластера `Elasticsearch` из `snapshot`, созданного ранее. 

**Приведите в ответе** запрос к API восстановления и итоговый список индексов.

Подсказки:

- возможно, вам понадобится доработать `elasticsearch.yml` в части директивы `path.repo` и перезапустить `Elasticsearch`.
```

```



---
