# KafkaNotes

Handson

Install kafka and create topic called test1
 

Apache Kafka

    - open source, distributed event streaming platform

    - developed by linkedin and later becomes open source since 2011

    - latest 3.3.x

    - capture data in real time from event sources - process it - redirect some appl

    - usecases - capture financial or stock market related transaction, capture social media activities, capture data from IOT devices

    - Kafka is highly scalable, fault tolerant enterprise messaging system

 

Kafka Architecture

Producer - Kafka Topic - Consumer group - consumer

               |

           Partitions

 

Apache Zookeeper

    - It is a distributed open source configuration and synchronization service

    - 3 information

1. Which message consumer has read

2. What is cluster information

3. Topic information

 

Kafka Core Concept

1. Producer

       - It is an appl that sends the message or data or record to kafka topic

       - But according to kafka message will be always array of bytes

 

2. Consumer

       - It is an appl that reads the data from kafka topic

 

3. Broker

       - It is a kafka server/cluster

       - Producer and consumer dosent interact directly, they use broker as an agent to exchange messages

 

4. Topic

       - It represents particular stream of data

       - producer will send message to topic in kafka server

       - we can have multiple topics

 

default size of message - 1MB

 

5. Partition

       - Producer will store data into topic, but if data is large then we split the data into different parts and distribute to multiple brokers/clusters/computers in the form of partitions

       - In case of multiple clusters/brokers, 1 partition will store on 1 computer

      

Producer - Topic - Partition - Offset

 

6. Offset

     - Each message within partition will get an incremental id called offset

     - Offset is position of message in the partition

 

             Partition 0 0 1 2 3 4 5 6 7 8 9 10 11 12 13

Kafka Topic  Partition 1 0 1 2 3 4 5 6 7 8

             Partition 2 0 1 2 3 4 5 6 7 8 9 10 11

 

h1  - p1

h2  - p0

h3  - p1

h4  - p2

h5  - p0

h6  - p0

 

- Partitions are independent, we will be writing to each partition independently, offsets in each partition also independent, offset 3 in partition 0 does not represent same data as offset 3 in partition 1

- If we look at ordering of messages, the order will be guaranteed only within the partition, so we have ordering only at partition level

- Data in kafka by default is kept only for 7 days(ie) 1week

- Kafka is immutable, once the data is written in partition it cannot be changed

 

7. Message = topic name+partition+offset

 

8. Consumer Groups

       - It is group of consumer to share the work

       - maximum number of consumer in a group = total number of partition on topic

       - Generally one consumer can read data only from 1 partition

       - But if we stop 1 consumer, then we have only 2 consumer, then kafka will give responsiblity to one of the consumer to consume messages from 2 partitions

 

Installation

1. Download Kafka

2. Set env variable

 

Path - C:\Softwares\kafka_2.12-2.6.0\bin\windows

 

3. Create folder zookeeper_data inside kafka folder, to store all zookeeper log

 

Zookeeper.properties - contains all zookeeper configuration

By default zookeeper runs on 2181

 

dataDir=C:\Softwares\kafka_2.12-2.6.0\zookeeper_data

 

4. Create folder kafka-logs inside kafka folder, to store all kafka server log

 

server.properties - contains all kafka server/broker configuration

By default kafka server runs on 9092

 

broker.id=1

log.dirs=C:\Softwares\kafka_2.12-2.6.0\kafka-logs

offsets.topic.num.partitions=1

offsets.topic.replication.factor=1

min.insync.replicas=1

default.replication.factor=1

port=9092

advertised.host.name=localhost

 

5. Start zookeeper

C:\Softwares\kafka_2.12-2.6.0\config>zookeeper-server-start.bat zookeeper.properties

 

6. Start kafka server

>kafka-server-start.bat server.properties

 

7. Create kafka topic

C:\Softwares\kafka_2.12-2.6.0\bin\windows>kafka-topics.bat --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic topic1 --create

Created topic topic1.

DAy2::::::::::

--------------------------------------------------------------

1. Start zookeeper

2. Start kafka server

 

3. Create a topic

C:\Softwares\kafka_2.12-2.6.0\config>kafka-topics.bat --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic topic1 --create

Created topic "topic1".

 

4. Start kafka console producer

C:\Softwares\kafka_2.12-2.6.0\config>kafka-console-producer.bat --broker-list localhost:9092 --topic topic1

 

5. Start kafka console consumer

C:\Softwares\kafka_2.12-2.6.0\config>kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic topic1 --from-beginning

 

Fault tolerance

    - It makes the data available even in case of system failure

    - We make multiple copies of data and keep in separate systems

    - Replication factor used to create multiple copies of data

    - Kafka implements Leaders and Followers model

 

Implement 1 Leader and 2 followers - 3 brokers

 

1. Copy and paste server.properties file and rename as server-1.properties and server-2.properties

 

2. Create 2 folders called kafka-logs1 and kafka-logs2

 

3. Change server-1.properties

broker.id=2

log.dirs=C:\Softwares\kafka_2.12-2.6.0\kafka-logs1

port=9093

 

4. Change server-2.properties

broker.id=3

log.dirs=C:\Softwares\kafka_2.12-2.6.0\kafka-logs2

port=9094

 

5. paste server-1 and server-2 in config folder

 

6. start kafka server

C:\Softwares\kafka_2.12-2.6.0\config>kafka-server-start.bat server-1.properties

 

7. start kafka server

C:\Softwares\kafka_2.12-2.6.0\config>kafka-server-start.bat server-2.properties

 

8. C:\Softwares\kafka_2.12-2.6.0\config>zookeeper-shell.bat localhost:2181 ls /brokers/ids

- to display brokers

 

C:\Softwares\kafka_2.12-2.6.0\config>kafka-topics.bat --zookeeper localhost:2181 --replication-factor 3 --partitions 3 --topic topic2 --create

Created topic "topic2".

 

C:\Softwares\kafka_2.12-2.6.0\config>kafka-topics.bat --zookeeper localhost:2181 --describe --topic topic2

Topic:topic2    PartitionCount:3        ReplicationFactor:3     Configs:

        Topic: topic2   Partition: 0    Leader: 3       Replicas: 3,2,1 Isr: 3,2,1

        Topic: topic2   Partition: 1    Leader: 1       Replicas: 1,3,2 Isr: 1

        Topic: topic2   Partition: 2    Leader: 2       Replicas: 2,1,3 Isr: 2,1,3

 

In any kafka cluster there will be only 1 controller node will be present which is responsible for managing the states of partition and replicas for performing adminsitrative tasks like partition reassignment

 

To view controller node

C:\Softwares\kafka_2.12-2.6.0\config>zookeeper-shell.bat localhost:2181 get /controller

 

Leader node is responsible for doing read and write operation on that particular partition

 

Replicas contain the copy of the particular partition

ISR - Insync Replicas which is subset of replicas

 

Controller node task

1. increase number of partitions for a topic

 

C:\Softwares\kafka_2.12-2.6.0\config>kafka-topics.bat --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic testtopic --create

Created topic "testtopic".

 

C:\Softwares\kafka_2.12-2.6.0\config>kafka-topics.bat --zookeeper localhost:2181 --describe --topic testtopic

Topic:testtopic PartitionCount:1        ReplicationFactor:1     Configs:

        Topic: testtopic        Partition: 0    Leader: 2       Replicas: 2     Isr: 2

 

C:\Softwares\kafka_2.12-2.6.0\config>kafka-topics.bat --zookeeper localhost:2181 --alter --topic testtopic --partitions 2

WARNING: If partitions are increased for a topic that has a key, the partition logic or ordering of the messages will be affected

Adding partitions succeeded!

 

C:\Softwares\kafka_2.12-2.6.0\config>kafka-topics.bat --zookeeper localhost:2181 --describe --topic testtopic

Topic:testtopic PartitionCount:2        ReplicationFactor:1     Configs:

        Topic: testtopic        Partition: 0    Leader: 2       Replicas: 2     Isr: 2

        Topic: testtopic        Partition: 1    Leader: 3       Replicas: 3     Isr: 3

 

2. Move partitions across brokers, in this case from 1,2 move to 2,3

 

1. Create topicsToMove.json file inside config folder

{"topics":[{"topic":"testtopic"}],"version":1}

 

2. Now reassign partition from 1,2 to 2,3

C:\Softwares\kafka_2.12-2.6.0\config>kafka-reassign-partitions.bat --zookeeper localhost:2181 --topics-to-move-json-file topicsToMove.json --broker-list "2,3" --generate

Current partition replica assignment

{"version":1,"partitions":[{"topic":"testtopic","partition":0,"replicas":[2],"log_dirs":["any"]},{"topic":"testtopic","partition":1,"replicas":[3],"log_dirs":["any"]}]}

 

Proposed partition reassignment configuration

{"version":1,"partitions":[{"topic":"testtopic","partition":1,"replicas":[2],"log_dirs":["any"]},{"topic":"testtopic","partition":0,"replicas":[1],"log_dirs":["any"]}]}

 

3. Copy proposed partition json and save as suggestedChange.json inside config folder

{"version":1,"partitions":[{"topic":"testtopic","partition":1,"replicas":[2]},{"topic":"testtopic","partition":0,"replicas":[1]}]}

 

4. Now we want to execute

C:\Softwares\kafka_2.12-2.6.0\config>kafka-reassign-partitions.bat --zookeeper localhost:2181 --reassignment-json-file suggestedChange.json --execute

Current partition replica assignment

 

{"version":1,"partitions":[{"topic":"testtopic","partition":0,"replicas":[2],"log_dirs":["any"]},{"topic":"testtopic","partition":1,"replicas":[3],"log_dirs":["any"]}]}

 

Save this to use as the --reassignment-json-file option during rollback

Successfully started reassignment of partitions.

 

4. We have to verify

C:\Softwares\kafka_2.12-2.6.0\config>kafka-reassign-partitions.bat --zookeeper localhost:2181 --reassignment-json-file suggestedChange.json --verify

Status of partition reassignment:

Reassignment of partition testtopic-1 completed successfully

Reassignment of partition testtopic-0 completed successfully

 

C:\Softwares\kafka_2.12-2.6.0\config>kafka-topics.bat --zookeeper localhost:2181 --describe --topic testtopic

Topic:testtopic PartitionCount:2        ReplicationFactor:1     Configs:

        Topic: testtopic        Partition: 0    Leader: 3       Replicas: 3     Isr: 3

        Topic: testtopic        Partition: 1    Leader: 2       Replicas: 2     Isr: 2

 

3. Increasing replication factor

1. Edit suggestedChange.json

 

{"version":1,"partitions":[{"topic":"testtopic","partition":1,"replicas":[1,2]},{"topic":"testtopic","partition":0,"replicas":[2,3]}]}

 

2. Excute it

C:\Softwares\kafka_2.12-2.6.0\config>kafka-reassign-partitions.bat --zookeeper localhost:2181 --reassignment-json-file suggestedChange.json --execute

Current partition replica assignment

 

{"version":1,"partitions":[{"topic":"testtopic","partition":1,"replicas":[2],"log_dirs":["any"]},{"topic":"testtopic","partition":0,"replicas":[3],"log_dirs":["any"]}]}

 

Save this to use as the --reassignment-json-file option during rollback

Successfully started reassignment of partitions.

 

C:\Softwares\kafka_2.12-2.6.0\config>kafka-reassign-partitions.bat --zookeeper localhost:2181 --reassignment-json-file suggestedChange.json --verify

Status of partition reassignment:

Reassignment of partition testtopic-1 completed successfully

Reassignment of partition testtopic-0 completed successfully

 

C:\Softwares\kafka_2.12-2.6.0\config>kafka-topics.bat --zookeeper localhost:2181 --describe --topic testtopic

Topic:testtopic PartitionCount:2        ReplicationFactor:2     Configs:

        Topic: testtopic        Partition: 0    Leader: 3       Replicas: 2,3   Isr: 3,2

        Topic: testtopic        Partition: 1    Leader: 2       Replicas: 1,2   Isr: 2,1

 

 

4. Move replicas of a partition to specific set of brokers

1. Edit suggestedChange.json

{"version":1,"partitions":[{"topic":"testtopic","partition":1,"replicas":[1,2]},{"topic":"testtopic","partition":0,"replicas":[1,2]}]}

 

2. We exeucte it

C:\Softwares\kafka_2.12-2.6.0\config>kafka-reassign-partitions.bat --zookeeper localhost:2181 --reassignment-json-file suggestedChange.json --execute

Current partition replica assignment

 

{"version":1,"partitions":[{"topic":"testtopic","partition":1,"replicas":[1,2],"log_dirs":["any","any"]},{"topic":"testtopic","partition":0,"replicas":[2,3],"log_dirs":["any","any"]}]}

 

Save this to use as the --reassignment-json-file option during rollback

Successfully started reassignment of partitions.

 

C:\Softwares\kafka_2.12-2.6.0\config>kafka-reassign-partitions.bat --zookeeper localhost:2181 --reassignment-json-file suggestedChange.json --verify

Status of partition reassignment:

Reassignment of partition testtopic-1 completed successfully

Reassignment of partition testtopic-0 completed successfully

 

C:\Softwares\kafka_2.12-2.6.0\config>kafka-topics.bat --zookeeper localhost:2181 --describe --topic testtopic

Topic:testtopic PartitionCount:2        ReplicationFactor:2     Configs:

        Topic: testtopic        Partition: 0    Leader: 1       Replicas: 1,2   Isr: 2,1

        Topic: testtopic        Partition: 1    Leader: 1       Replicas: 1,2   Isr: 2,1

 

 

Kakfa-topics.bat --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic xyz --create max.message.bytes=202020020

 

 

server.properties

1. zookeeper.connect= localhost:2181

2. delete.topic.enable= true/false(default)

3. auto.create.topics.enable=true/false

4. default.replication.factor=1

5. num.partitions=1

 

We create appl using kafka API

 

1. <dependency>

          <groupId>org.apache.kafka</groupId>

          <artifactId>kafka-clients</artifactId>

          <version>0.10.1.0</version>

      </dependency>

 

2. Create topic

C:\Softwares\kafka_2.12-2.6.0\config>kafka-topics.bat --zookeeper localhost:2181 --replication-factor 1 --partitions 3 --topic simpletopic --create

Created topic "simpletopic".

 

3. We need to create producer appl

 

KafkaProducer class - to create producer with 3 mandatory configuration

The activity of convertinng java objects into array of bytes is called serialization

The activity of converting array of bytes to java objects is called deserialization

Kafka provides with inbuilt serializer and deserializer, the message will be sending as key value pairs

 

ProducerRecord class which contains the message

 

public class SimpleProducerApplication {

 

              public static void main(String[] args) {

                             Properties prop=new Properties();

                            

                             prop.put("bootstrap.servers", "localhost:9092");

                             prop.put("key.serializer",

                                                         "org.apache.kafka.common.serialization.StringSerializer");

                             prop.put("value.serializer",

                                                         "org.apache.kafka.common.serialization.StringSerializer");

                            

                             Producer<String,String> producer=new KafkaProducer<>(prop);

                            

                             ProducerRecord<String,String> record=

                                                          new ProducerRecord<>("simpletopic","key1","Hello world");

                             producer.send(record);

                             producer.close();

                             System.out.println("Message produced successfully");

                            

              }

 

}

 

4. Start the producer appl

 

5. Start consumer

C:\Softwares\kafka_2.12-2.6.0\config>kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic simpletopic --from-beginning

Hello world

Hello world

 

By default all kafka producer will contain Default partitioner - it will decide which message has to go to which partition number

 

public class SimpleConsumerApplication {

 

              public static void main(String[] args) {

                             Properties prop=new Properties();

                            

                             prop.put("bootstrap.servers", "localhost:9092");

                             prop.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");

                             prop.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");

                             prop.put("group.id", "ggg"); //create consumer group called ggg

                            

                             KafkaConsumer<String,String> consumer=new KafkaConsumer<>(prop);

                             consumer.subscribe(Arrays.asList("simpletopic"));

                            

                             while(true) {

                                           ConsumerRecords<String,String> records=consumer.poll(100);

                                           for(ConsumerRecord<String,String> record:records) {

                                                          System.out.println(record.value());

                                           }

                             }

              }

 

}

. Fire and forgot

          prop.put("acks","0");

       - In this method we send the message to broker and dont care about it whether it is successfully received or not

 

2. Synchronous send

         prop.put("acks","1");

       - We send message and wait until we get the response, in case of sucess  we get RecordMetadataObject, in failure we get exception

 

1. Create topic

C:\Softwares\kafka_2.12-2.6.0\config>kafka-topics.bat --zookeeper localhost:2181 --replication-factor 1 --partitions 3 --topic synctopic --create

Created topic "synctopic".

 

2. Create producer appl

 

public class SynchProducerApplication {

 

              public static void main(String[] args) {

        Properties prop=new Properties();

                            

                             prop.put("bootstrap.servers", "localhost:9092");

                             prop.put("key.serializer",

                                                         "org.apache.kafka.common.serialization.StringSerializer");

                             prop.put("value.serializer",

                                                         "org.apache.kafka.common.serialization.StringSerializer");

                             prop.put("acks", "1");

                             Producer<String,String> producer=new KafkaProducer<>(prop);

                            

                             //In this case default partitioner is disabled and data is send to partition 2

                             //ProducerRecord<String,String> record=new ProducerRecord<>("synctopic",2,"synckey","value1");

                            

                             //If we pass key then kafka perform hashing algorithm on the key to decide

                             //partition no for that message, if we change the value it will go to same

                             //partition no, but if we change the key it will goto different partition no

                             //ProducerRecord<String,String> record=new ProducerRecord<>("synctopic","synckey2","value3");

       

                             //Kafka  will decide partition no for that message using round robin algorithm

                             ProducerRecord<String,String> record=new ProducerRecord<>("synctopic","value6");

                             try {

                                           RecordMetadata metadata=producer.send(record).get();

                                           System.out.println("Message is sent to Partition no "

                                                +metadata.partition()+" and offset is "+metadata.offset());

                                           System.out.println("Synchronous producer completed");

                             }

                             catch(Exception e) {

                                           e.printStackTrace();

                             }

                             finally {

                                           producer.close();

                             }

              }

 

}

 

3. Asynchronous send

       - we send a message and provide callback function to receive acknowledgement, we dont wait for success and failure

        prop.put("acks","all");

 

1. Create topic

 

C:\Softwares\kafka_2.12-2.6.0\config>kafka-topics.bat --zookeeper localhost:2181 --replication-factor 1 --partitions 3 --topic asynctopic --create

 

2. Create producer appl

public class AsynchProducerApplication {

 

              public static void main(String[] args) {

        Properties prop=new Properties();

                            

                             prop.put("bootstrap.servers", "localhost:9092");

                             prop.put("key.serializer",

                                                         "org.apache.kafka.common.serialization.StringSerializer");

                             prop.put("value.serializer",

                                                         "org.apache.kafka.common.serialization.StringSerializer");

                             prop.put("acks", "all");

                             Producer<String,String> producer=new KafkaProducer<>(prop);

                            

                             ProducerRecord<String,String> record=new ProducerRecord<>("asynctopic","Oey","value7");

                             producer.send(record, new MyCallback());

                             System.out.println("Asynchronous producer completed");

                            producer.close();

 

              }

 

}

 

class MyCallback implements Callback {

 

              @Override

              public void onCompletion(RecordMetadata metadata, Exception exception) {

                             if(exception!=null)

                                           System.out.println("Asynchronous producer failed with an exception");

                             else

                                           System.out.println("Message send to partiton no "

                                      +metadata.partition()+" and offset is "+metadata.offset());

              }

             

}

 

Custom Serializer and Deserializer

 

1. Create topic

 

C:\Softwares\kafka_2.12-2.6.0\config>kafka-topics.bat --zookeeper localhost:2181 --replication-factor 1 --partitions 3 --topic suptopic --create

Created topic "suptopic".

 

2. Create Supplier class

 

public class Supplier {

    private int supplierId;

    private String name;

    private Date supplierStartDate;

}

 

3. Create serializer class to convert supplier object to array of bytes, we need to implement Serializer interface

    configure() - for initialization, called only once

    serialize() - conversion logic

    close() - for cleanup

 

public class SupplierSerializer implements Serializer<Supplier>{

 

              @Override

              public void configure(Map<String, ?> configs, boolean isKey) {

                             // TODO Auto-generated method stub

                            

              }

 

              @Override

              public byte[] serialize(String topic, Supplier data) {

                             byte[] serializedName;

                             byte[] serializedDate;

                             try {

                                           if(data==null)

                                                          return null;

                                           serializedName=data.getName().getBytes("UTF8");

                                    serializedDate=data.getSupplierStartDate().toString().getBytes("UTF8");

                                          

                                           ByteBuffer buf=ByteBuffer.allocate(100);

                                           buf.putInt(data.getSupplierId());

                                           buf.putInt(serializedName.length);

                                           buf.put(serializedName);

                                           buf.putInt(serializedDate.length);

                                           buf.put(serializedDate);

                                          

                                           return buf.array();

                             }

                             catch(Exception e) {

                                           throw new SerializationException("Error when serializing supplier object to byte[]");

                             }

                            

              }

 

              @Override

              public void close() {

                             // TODO Auto-generated method stub

                            

              }

 

}

 

 

4. Create deserializer class to convert byte array to supplier object, which implements Deserializer interface

    configure()

    deserialize()

    close()

 

public class SupplierDeserializer implements Deserializer<Supplier>{

 

              @Override

              public void configure(Map<String, ?> configs, boolean isKey) {

                             // TODO Auto-generated method stub

                            

              }

 

              @Override

              public Supplier deserialize(String topic, byte[] data) {

                             try {

                                           if(data==null) {

                                                          System.out.println("Null received at deserialize");

                                                          return null;

                                           }

                                           ByteBuffer buf=ByteBuffer.wrap(data);

                                           int id=buf.getInt();

                                          

                                           byte[] nameBytes=new byte[buf.getInt()];

                                           buf.get(nameBytes);

                                           String deserializedName=new String(nameBytes,"UTF8");

                                          

                                           byte[] dateBytes=new byte[buf.getInt()];

                                           buf.get(dateBytes);

                                           String dateString = new String(dateBytes,"UTF8");

                                           DateFormat df=new SimpleDateFormat("EEE MMM dd HH:mm:ss Z yyyy");

                                          

                                           return new Supplier(id,deserializedName,df.parse(dateString));

                             }

                             catch(Exception e) {

                                           throw new SerializationException("Error when deserializing byte[] to supplier object");

                             }

              }

 

              @Override

              public void close() {

                             // TODO Auto-generated method stub

                            

              }

 

}

 

5. Create producer appl

 

public class SupplierProducer {

 

              public static void main(String[] args) throws Exception {

        Properties prop=new Properties();

                            

                             prop.put("bootstrap.servers", "localhost:9092");

                             prop.put("key.serializer",

                                                         "org.apache.kafka.common.serialization.StringSerializer");

                             prop.put("value.serializer",

                                                          "com.pack.SupplierSerializer");

                            

                             Producer<String,Supplier> producer=new KafkaProducer<>(prop);

                            

                             DateFormat df=new SimpleDateFormat("yyyy-MM-dd");

                             Supplier sup1=new Supplier(1000,"Parle Ltd",df.parse("2020-10-20"));

                             Supplier sup2=new Supplier(1001,"Johnson Ltd",df.parse("2021-11-23"));

                            

                             producer.send(new ProducerRecord<String,Supplier>("suptopic","SUPPLIER",sup1)).get();

                             producer.send(new ProducerRecord<String,Supplier>("suptopic","SUPPLIER",sup2)).get();

                            

                             System.out.println("Supplier producer completed");

                             producer.close();

              }

 

}

 

 

6. Create consumer appl

 

public class SupplierConsumer {

 

              public static void main(String[] args) {

        Properties prop=new Properties();

                            

                             prop.put("bootstrap.servers", "localhost:9092");

                             prop.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");

                             prop.put("value.deserializer", "com.pack.SupplierDeserializer");

                             prop.put("group.id", "ggg"); //create consumer group called ggg

                            

                             KafkaConsumer<String,Supplier> consumer=new KafkaConsumer<>(prop);

                             consumer.subscribe(Arrays.asList("suptopic"));

                            

                             while(true) {

                                           ConsumerRecords<String,Supplier> records=consumer.poll(100);

                                           for(ConsumerRecord<String,Supplier> record:records) {

                                                          System.out.println("Supplier Id = "+record.value().getSupplierId()+" Supplier name = "+record.value().getName()+" Supplier Date = "+record.value().getSupplierStartDate().toString());

                                           }

                             }

 

              }

 

}

 

7. Start consumer appl

8. Start producer appl

 

To list the topic

C:\Softwares\kafka_2.12-2.6.0\config>kafka-topics.bat --zookeeper localhost:2181 --list

 

To delete the topic

C:\Softwares\kafka_2.12-2.6.0\config>kafka-topics.bat --zookeeper localhost:2181 --topic testtopic --delete


