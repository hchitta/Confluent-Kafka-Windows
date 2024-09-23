# Confluent-Kafka-Windows

1. Download Confluent Kafka community edition from https://www.confluent.io/
   
2.Fixing “Classpath is empty. Please build the project first e.g. by running ‘gradlew jarAll’ ” and “The System cannot find the path specified” errors on Windows Confluent Kafka

Lets add the below snippet above the “rem Classpath addition for core” line in kafka-run-class.bat file. [\confluent-7.7.0\bin\windows]

rem classpath addition for LSB style path
if exist %BASE_DIR%\share\java\kafka\* (
 call:concat %BASE_DIR%\share\java\kafka\*
)


1) Start Zookeeper
C:\confluent-7.7.0>bin\windows\zookeeper-server-start.bat C:\confluent-7.7.0\etc\kafka\zookeeper.properties

2) Start Broker
C:\confluent-7.7.0>bin\windows\kafka-server-start.bat c:\confluent-7.7.0\etc\kafka\server.properties

3) Create a Topic
C:\confluent-7.7.0>bin\windows\kafka-topics --bootstrap-server localhost:9092 --create --topic NewTopic1 --partitions 3 -- replication-factor 1

4) list all the topics
C:\confluent-7.7.0>bin\windows\kafka-topics --bootstrap-server localhost:9092 --list
NewTopic1

5) describe a topic 
C:\confluent-7.7.0>bin\windows\kafka-topics --bootstrap-server localhost:9092 --describe --topic NewTopic1

6) start producer
C:\confluent-7.7.0>bin\windows\kafka-console-producer --broker-list localhost:9092 --topic NewTopic1

7) start consumer
C:\confluent-7.7.0>bin\windows\kafka-console-consumer --bootstrap-server localhost:9092 --topic NewTopic1

8) use offset explorer to see messages in partitions
------------------------------------------------------------------------------------------

Instaed of storing All kafka cluster meta data in zookeeper, we store in kafka server only

Kafka without zookeeper using Kraft Mode::

C:\confluent-7.7.0>cd bin

C:\confluent-7.7.0\bin>cd windows

1) Generate cluster id
C:\confluent-7.7.0\bin\windows>kafka-storage.bat random-uuid
TEK_5q1WRYCSE0A60jc9aQ

2) Set cluster id
C:\confluent-7.7.0\bin\windows>set KAFKA_CLUSTER_ID=TEK_5q1WRYCSE0A60jc9aQ

C:\confluent-7.7.0\bin\windows>echo %KAFKA_CLUSTER_ID%
TEK_5q1WRYCSE0A60jc9aQ

3) set server.properties in kraft folder
C:\confluent-7.7.0\bin\windows>kafka-storage.bat format -t %KAFKA_CLUSTER_ID% -c C:\confluent-7.7.0\etc\kafka\kraft\server.properties

metaPropertiesEnsemble=MetaPropertiesEnsemble(metadataLogDir=Optional.empty, dirs={/tmp/kraft-combined-logs: EMPTY})
Formatting /tmp/kraft-combined-logs with metadata.version 3.7-IV4.

4) start kafka server
C:\confluent-7.7.0\bin\windows>kafka-server-start.bat C:\confluent-7.7.0\etc\kafka\kraft\server.properties

5) create a topic
C:\confluent-7.7.0\bin\windows>kafka-topics.bat --create --topic first-kraft-topic --bootstrap-server localhost:9092
Created topic first-kraft-topic.

6) start producer
To produce message on topic
kafka-console-producer.bat --topic first-kraft-topic --bootstrap-server localhost:9092

7) start consumer
To consume message on topic
kafka-console-consumer.bat --topic first-kraft-topic --from-beginning --bootstrap-server localhost:9092

---------------------------------------------------------------------------------------------------------------------
To check meta data information

C:\confluent-7.7.0\bin\windows>kafka-dump-log.bat --cluster-metadata-decoder --files C:\tmp\kraft-combined-logs\__cluster_metadata-0\00000000000000000000.log

C:\confluent-7.7.0\bin\windows>kafka-metadata-quorum.bat --bootstrap-server localhost:9092 describe --status

C:\confluent-7.7.0\bin\windows>kafka-metadata-quorum.bat --bootstrap-server localhost:9092 describe --replication
