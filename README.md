# clusterKafka
sigue la configuracion en base a este articulo https://blog.logrocket.com/apache-kafka-real-time-data-streaming-app/

1. descargamos el kafka apache
wget https://archive.apache.org/dist/kafka/3.1.0/kafka_2.13-3.1.0.tgz

2. desempaquetamos en el servidor cbk51
cd servidor-kafka

tar -zxf kafka_2.13-3.1.0.tgz
cd kafka_2.13-3.1.0/
cd config/kraft/

3. copiamos y editamos
cp server.properties server1.properties
```
# The node id associated with this instance's roles
node.id=1

# The connect string for the controller quorum
#controller.quorum.voters=1@localhost:9093    # ORIGINAL MAGZ
#NUEVO MAGZ:
controller.quorum.voters=1@localhost:19092,2@localhost:19093,3@locahost:19094
# ORIGINAL MAGZ
#listeners=PLAINTEXT://:9092,CONTROLLER://:9093
#NUEVO MAGZ
listeners=PLAINTEXT://:9092,CONTROLLER://:19092
inter.broker.listener.name=PLAINTEXT
...
############################# Log Basics #############################

# A comma separated list of directories under which to store log files
#ORIGINAL MAGZ
#log.dirs=/tmp/kraft-combined-logs
#NUEVO MAGZ
log.dirs=/tmp/server1/kraft-combined-logs
```
  
Editamos el servidor2.properties
```
# The node id associated with this instance's roles
node.id=2

# The connect string for the controller quorum
#controller.quorum.voters=1@localhost:9093    # ORIGINAL MAGZ
#NUEVO MAGZ:
controller.quorum.voters=1@localhost:19092,2@localhost:19093,3@locahost:19094
# ORIGINAL MAGZ
#listeners=PLAINTEXT://:9092,CONTROLLER://:9093
#NUEVO MAGZ
listeners=PLAINTEXT://:9093,CONTROLLER://:19093
inter.broker.listener.name=PLAINTEXT
...
############################# Log Basics #############################

# A comma separated list of directories under which to store log files
#ORIGINAL MAGZ
#log.dirs=/tmp/kraft-combined-logs
#NUEVO MAGZ
log.dirs=/tmp/server2/kraft-combined-logs

```
  
Editamos el servidor3.properties
```
# The node id associated with this instance's roles
node.id=3

# The connect string for the controller quorum
#controller.quorum.voters=1@localhost:9093    # ORIGINAL MAGZ
#NUEVO MAGZ:
controller.quorum.voters=1@localhost:19092,2@localhost:19093,3@locahost:19094
# ORIGINAL MAGZ
#listeners=PLAINTEXT://:9092,CONTROLLER://:9093
#NUEVO MAGZ
listeners=PLAINTEXT://:9094,CONTROLLER://:19094
inter.broker.listener.name=PLAINTEXT
...
############################# Log Basics #############################

# A comma separated list of directories under which to store log files
#ORIGINAL MAGZ
#log.dirs=/tmp/kraft-combined-logs
#NUEVO MAGZ
log.dirs=/tmp/server3/kraft-combined-logs

```
  
Creamos un UUI para nuestro cluster
```
cd kafka_2.13-3.1.0/
./bin/kafka-storage.sh random-uuid
-- se crea este codigo:
eG2f5q0IQ1izMTaRV3hjTQ
```
  
Ejecutamos este codigo por cada serverX.properties
```
./bin/kafka-storage.sh format -t eG2f5q0IQ1izMTaRV3hjTQ -c ./config/kraft/server1.properties

./bin/kafka-storage.sh format -t eG2f5q0IQ1izMTaRV3hjTQ -c ./config/kraft/server2.properties

./bin/kafka-storage.sh format -t eG2f5q0IQ1izMTaRV3hjTQ -c ./config/kraft/server3.properties

```
  
Configuramos memoria
```
export KAFKA_HEAP_OPTS="-Xmx200M -Xms100M"
-- y ejecutamos:
./bin/kafka-server-start.sh -daemon ./config/kraft/server1.properties
./bin/kafka-server-start.sh -daemon ./config/kraft/server2.properties
./bin/kafka-server-start.sh -daemon ./config/kraft/server3.properties
-- probamos que todo esta okey
ps -ef | grep kafka
```
  
Creamos un topic para interactuar con kafka
```
./bin/kafka-topics.sh --create --topic kraft-test --partitions 3 --replication-factor 3 --bootstrap-server localhost:9092
```

producer:
```
./bin/kafka-console-producer.sh --topic test --bootstrap-server localhost:9092
```
consumer:
```
./bin/kafka-console-consumer.sh --topic test --from-beginning --bootstrap-server localhost:9092
```

lista topic
```
./bin/kafka-topics.sh --bootstrap-server=localhost:9092 --list
```
  
En este punto tenemos el cluster Kafka operativo
