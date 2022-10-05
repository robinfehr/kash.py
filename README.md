# kash.py

*kash.py* is a Python-based shell based on [confluent-kafka-python](https://github.com/confluentinc/confluent-kafka-python) by Magnus Edenhill, which is itself based on the native Kafka client library [librdkafka](https://github.com/edenhill/librdkafka) by the same author.

The aim of *kash.py* is to gift you with a wonderful Kafka scripting experience, both interactively and non-interactively. It is both like a *shell* for bash fans (you have *ls*, *touch*, *rm*, *cp*, *grep*, *wc* etc.) and *functional* for those who once fell in love with Haskell, Scala or Mozart-Oz (there are *foldl*s, *flatmap*s and *map*s for you to explore). You can use *kash.py* either way :-)

## Tutorial

This little tutorial shows you how *kash.py* can help you unlock the Kafka black box.

TODO

## Installation

### Install Dependencies
```
pip install -r requirements.txt
```

### Have fun :)
For interactive use, e.g. using your local cluster configured in the file "clusters_unsecured/local.conf", just do the following to list the topics:
```
python3
>> from kash import *
>> c = Cluster("local")
>> c.ls()
['__consumer_offsets', '_schemas']
>>
```

## Documentation

### _class_ kash.Cluster(cluster_str)

Initialize a kash.py Cluster object.

Initialize a kash.py Cluster object based on a kash.py cluster configuration file.

kash.py cluster configuration files have up to three sections: “kafka”, “schema_registry”, and “kash”.

The “kafka” and “schema_registry” sections configure the Kafka cluster and Schema Registry according to the confluent_kafka/librdkafka configuration.

The “kash” section can be used to configure the following:


* AdminClient (currently only applies to `create()`):


    * `retention.ms`: Retention time (in milliseconds) for creating topics. Defaults to 604800000 (seven days). You can set it to -1 to create topics with infinite retention by default.


* Producer (applies to all functions/methods producing messages to a cluster):


    * `flush.num.messages`: Number of messages produced before calling `confluent_kafka.Producer.flush()`. Defaults to 10000.


    * `flush.timeout`: Timeout (in seconds) for calling `confluent_kafka.Producer.flush()`. Defaults to -1 (no timeout).


* Consumer (applies to all functions/methods consuming messages from a cluster):


    * `consume.timeout`: Timeout (in seconds) for calling `confluent_kafka.Consumer.consume()`. Lower (e.g. 1.0) for local or very fast Kafka clusters, higher for remote/not-so-fast Kafka clusters (e.g. 10.0). Defaults to 3.0.


    * `auto.offset.reset`: Either “earliest” or “latest”. Directly translates to the confluent_kafka/librdkafka consumer configuration. Defaults to “earliest”.


    * `enable.auto.commit`: Either “True” or “False”. Directly translates to the confluent_kafka/librdkafka consumer configuration. Defaults to “True”.


    * `session.timeout.ms`: Timeout (in milliseconds) to detect client failures. Defaults to 45000.


* Progress display:


    * `progress.num.messages`: Number of messages to be consumed/produced until a status print out to standard out/the console is triggered (if verbosity level > 0). Defaults to 1000.


* Blocking (applies to `create()` and `delete()`/`rm()`)


    * `block.num.retries.int`: The number of retries when blocking to wait for topics to have been created/deleted (if `block` is set to True). Defaults to 50.


    * `block.interval`: Time (in seconds) between retries when blocking to wait for topics to have been created/deleted (if `block` is set to True). Defaults to 0.1.


* **Parameters**

    **cluster_str** (*str*) – Name of the cluster; kash.py searches the two folders “clusters_secured” and “clusters_unsecured” for kash.py configuration files named “<cluster.str>.conf”.


### Examples

Simplest kash.py configuration file example (just “bootstrap.servers” is set):

```default
[kafka]
bootstrap.servers=localhost:9092
```

Simple kash.py configuration file example with additional Schema Registry URL:

```default
[kafka]
bootstrap.servers=localhost:9092

[schema_registry]
schema.registry.url=http://localhost:8081
```

kash.py configuration file with all bells and whistles - all that can currently be configured:

```default
[kafka]
bootstrap.servers=localhost:9092
[schema_registry]
schema.registry.url=http://localhost:8081

[kash]
flush.num.messages=10000
flush.timeout=-1.0
retention.ms=-1
consume.timeout=1.0
auto.offset.reset=earliest
enable.auto.commit=true
session.timeout.ms=10000
progress.num.messages=1000
block.num.retries.int=50
block.interval=0.1
```

kash.py configuration file for a typical Confluent Cloud cluster (including Schema Registry):

```default
[kafka]
bootstrap.servers=CLUSTER.confluent.cloud:9092
security.protocol=SASL_SSL
sasl.mechanisms=PLAIN
sasl.username=CLUSTER_USERNAME
sasl.password=CLUSTER_PASSWORD

[schema_registry]
schema.registry.url=https://SCHEMA_REGISTRY.confluent.cloud
basic.auth.credentials.source=USER_INFO
basic.auth.user.info=SCHEMA_REGISTRY_USERNAME:SCHEMA_REGISTRY_PASSWORD

[kash]
flush.num.messages=10000
flush.timeout=-1.0
retention.ms=-1
consume.timeout=10.0
auto.offset.reset=earliest
enable.auto.commit=true
session.timeout.ms=10000
progress.num.messages=1000
block.num.retries.int=50
block.interval=0.1
```

kash.py configuration file for a self-hosted Redpanda cluster (without Schema Registry):

```default
[kafka]
bootstrap.servers=CLUSTER:9094
security.protocol=sasl_plaintext
sasl.mechanisms=SCRAM-SHA-256
sasl.username=CLUSTER_USERNAME
sasl.password=CLUSTER_PASSWORD

[kash]
flush.num.messages=10000
flush.timeout=-1.0
retention.ms=-1
consume.timeout=5.0
auto.offset.reset=earliest
enable.auto.commit=true
session.timeout.ms=10000
progress.num.messages=1000
block.num.retries.int=50
block.interval=0.1
```


#### acls(restype='any', name=None, resource_pattern_type='any', principal=None, host=None, operation='any', permission_type='any')
List ACLs.

List ACLs of the cluster.


* **Parameters**

    
    * **restype** (*str**, **optional*) – The resource type (“unknown”, “any”, “topic”, “group” or “broker”). Defaults to “any”.


    * **name** (*str**, **optional*) – The name. Defaults to None.


    * **resource_pattern_type** (*str**, **optional*) – The resource pattern type (“unknown”, “any”, “match”, “literal” or “prefixed”). Defaults to “any”.


    * **principal** (*str**, **optional*) – The principal. Defaults to None.


    * **host** (*str**, **optional*) – The host. Defaults to None.


    * **operation** (*str**, **optional*) – The operation (“unknown”, “any”, “all”, “read”, “write”, “create”, “delete”, “alter”, “describe”, “cluster_action”, “describe_configs”, “alter_configs”, “idempotent_write”). Defaults to “any”


    * **permission_type** (*str**, **optional*) – The permission type (“unknown”, “any”, “deny” or “allow”). Defaults to “any”.



* **Returns**

    List of ACL Binding dictionaries (converted from confluent_kafka.AclBinding objects) of the selected ACLs.



* **Return type**

    list(aclBinding_dict)


### Examples

c.acls():

    List all ACLs of the cluster.

c.acls(restype=”topic”):

    List all ACLs for the topics of the cluster.


#### broker_config(pattern_int_or_str_or_int_or_str_list)
List the configurations of brokers of the cluster.

List the configurations of brokers of the cluster. Optionally only list those brokers whose identifiers match the pattern (or list of patterns) pattern.


* **Parameters**

    **pattern_int_or_str_or_int_or_str_list** (*str** | **list**(**str**)**, **optional*) – The pattern or list of patterns for selecting those brokers which shall be listed. Defaults to None.



* **Returns**

    Dictionary of integers (broker identifiers) and configuration dictionaries (dictionaries of strings (keys) and strings (values)).



* **Return type**

    dict(int, dict(str, str))


### Examples

c.broker_config(0)

    List the configuration of broker 0 of the cluster.

c.broker_config([0, 1])

    List the configurations of brokers 0 and 1 of the cluster.


#### brokers(pattern=None)
List brokers of the cluster.

List brokers of the cluster. Optionally only list those brokers whose identifiers match the pattern (or list of patterns) pattern.


* **Parameters**

    **pattern** (*str** | **list**(**str**)**, **optional*) – The pattern or list of patterns for selecting those brokers which shall be listed. Defaults to None.



* **Returns**

    Dictionary of integers (broker identifiers) and strings (broker URLs and ports).



* **Return type**

    dict(int, str)


### Examples

c.brokers()

    List all brokers of the cluster.

c.brokers([0, 1])

    List only the brokers 0 and 1 of the cluster.


#### cat(topic_str, foreach_function=<built-in function print>, group=None, offsets=None, config={}, key_type='str', value_type='str', n=-1, batch_size=1)
Subscribe to and consume messages from a topic and call an operation on each of them.

Subscribe to and consume messages from a topic and call an operation on each of them, optionally explicitly set the consumer group, initial offsets, and augment the consumer configuration. Stops either if the consume timeout is exceeded (`consume.timeout` in the kash.py cluster configuration) or the number of messages specified in `n` has been consumed.


* **Parameters**

    
    * **topic_str** (*str*) – The topic to subscribe to and consume from.


    * **foreach_function** (*function**, **optional*) – Foreach function (takes a message dictionary and returns None). Defaults to `print`.


    * **group** (*str**, **optional*) – Consumer group name used for subscribing to the topic. If set to None, creates a new unique consumer group name. Defaults to None.


    * **offsets** (*dict**, **optional*) – Dictionary of offsets (keys: partitions (int), values: offsets for the partitions (int)) for subscribing to the topic. If set to None, subscribe to the topic using the offsets from the consumer group. Defaults to None.


    * **config** (*dict**(**str**, **str**)**, **optional*) – Dictionary of strings (keys) and strings (values) to augment the consumer configuration for the topic. Defaults to {}.


    * **key_type** (*str**, **optional*) – Message key type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “str”.


    * **value_type** (*str**, **optional*) – Message value type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “str”.


    * **n** (*int**, **optional*) – Number of messages to consume from the topic. Defaults to ALL_MESSAGES = -1.


    * **batch_size** (*int**, **optional*) – Maximum number of messages to consume from the topic at a time. Defaults to 1.



* **Returns**

    Number of messages consumed from the topic (integer).



* **Return type**

    int


### Examples

c.cat(“test”)

    Consume topic “test” and print out all the consumed messages to standard out/the console.


#### commit()
Commit the last consumed message from the topic subscribed to.

Commit the last consumed message from the topic subscribed to.


* **Returns**

    Last consumed message dictionary (converted from confluent_kafka.Message).



* **Return type**

    message_dict



#### config(pattern_str_or_str_list)
Return the configuration of topics.

Return the configuration of those topics whose names match the bash-like pattern (or list of patterns) pattern_str_or_str_list.


* **Parameters**

    **pattern_str_or_str_list** (*str** | **list**(**str**)*) – The pattern (or list of patterns) for selecting the topics for which the configuration shall be returned.



* **Returns**

    Dictionary of strings (topic names) and dictionaries of strings (configuration keys) and strings (configuration values).



* **Return type**

    dict(str, dict(str, str))


### Examples

c.config(“test”)

    Return the configuration of the topic “test”.

c.config([“test?”, “bla?”])

    Return the configuration of all topics matching the patterns “test?” or “bla?”.


#### consume(n=1)
Consume messages from a subscribed topic.

Consume messages from a subscribed topic.


* **Parameters**

    **n** (*int**, **optional*) – Maximum number of messages to return. Defaults to 1.



* **Returns**

    List of message dictionaries (converted from confluent_kafka.Message).



* **Return type**

    list(message_dict)


### Examples

c.consume()

    Consume the next message from the topic subscribed to before.

c.consume(n=100)

    Consume the next 100 messages from the topic subscribed to before.


#### cp(source_str, target_str, group=None, offsets=None, config={}, flatmap_function=<function Cluster.<lambda>>, source_key_type='str', source_value_type='str', target_key_type='str', target_value_type='str', target_key_schema=None, target_value_schema=None, key_value_separator=None, message_separator='\\n', overwrite=True, keep_timestamps=True, n=-1, batch_size=1, bufsize=4096)
Copy local files to topics, topics to local files, or topics to topics.

Copy files to topics, topics to files, or topics to topics. Uses `Cluster.upload()` for copying files to topics, `Cluster.download()` for copying topics to files, and `cp` for copying topics to topics. Paths to local files are distinguished from topics by having a forward slash “/” in their path.


* **Parameters**

    
    * **source_str** (*str*) – The source local file/topic.


    * **target_str** (*str*) – The target local file/topic.


    * **group** (*str**, **optional*) – Consumer group name used for subscribing to the topic to consume from. If set to None, creates a new unique consumer group name. Defaults to None.


    * **offsets** (*dict**, **optional*) – Dictionary of offsets (keys: partitions (int), values: offsets for the partitions (int)) for subscribing to the topic to consume from. If set to None, subscribe to the topic using the offsets from the consumer group. Defaults to None.


    * **config** (*dict**(**str**, **str**)**, **optional*) – Dictionary of strings (keys) and strings (values) to augment the consumer configuration for the topic. Defaults to {}.


    * **flatmap_function** (*function**, **optional*) – Flatmap function (either takes a message dictionary and returns a list of message dictionaries if source_str points to a topic, or takes a pair of strings (keys and values) of the messages read from the local file if source_str points to a local file). Defaults to lambda x: [x] (=the identify function for flatmap, leading to a one-to-one copy).


    * **source_key_type** (*str**, **optional*) – Source message key type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “str”.


    * **source_value_type** (*str**, **optional*) – Source message value type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “str”.


    * **target_key_type** (*str**, **optional*) – Target message key type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “str”.


    * **target_value_type** (*str**, **optional*) – Target message value type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “str”.


    * **target_key_schema** (*str**, **optional*) – Target key message type schema (for “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to None.


    * **target_value_schema** (*str**, **optional*) – Target value message type schema (for “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to None.


    * **key_value_separator** (*str**, **optional*) – The separator between the keys and the values in the local file to read from/write to, e.g. “:”. If set to None, only read/write the values, not the keys. Defaults to None.


    * **message_separator** (*str**, **optional*) – The separator between individual messages in the local file to read from/write to. Defaults to the newline character.


    * **overwrite** (*bool**, **optional*) – Overwrite the target local file if set to True, append to it otherwise. Defaults to True.


    * **keep_timestamps** (*bool**, **optional*) – Replicate the timestamps of the source messages in the target messages. Defaults to True.


    * **n** (*int**, **optional*) – Number of messages to consume from the topic. Defaults to ALL_MESSAGES = -1.


    * **batch_size** (*int**, **optional*) – Maximum number of messages to consume from the topic at a time. Defaults to 1.


    * **bufsize** (*int**, **optional*) – The buffer size for reading from the local file. Defaults to 4096.



* **Returns**

    Pair of the number of messages consumed from the source topic/read from the source local file and the number of messages produced to the target topic/written to the target local file.



* **Return type**

    tuple(int, int)


### Examples

c.cp(“./test.txt”, “test”)

    Upload the messages from the local file “./test.txt” to the topic “test”.

c.cp(“test”, “./test.txt”)

    Download the messages from topic “test” to the local file “./test.txt”.

c.cp(“test1”, “test2”)

    Replicate the messages from topic “test1” to topic “test2”.


#### create(topic_str, partitions=1, config={}, block=True)
Create a topic.

Create a topic.


* **Parameters**

    
    * **topic_str** (*str*) – The name of the topic to be created.


    * **partitions** (*int**, **optional*) – The number of partitions for the topic to be created. Defaults to 1.


    * **config** (*dict**(**str**, **str**)**, **optional*) – Configuration overrides for the topic to be created. Note that the default “retention.ms” can also be set in the kash.py cluster configuration file (e.g. you can set it to -1 to have infinite retention for all topics that you create). Defaults to {}.


    * **block** (*bool**, **optional*) – Block until the topic is created. Defaults to True.



* **Returns**

    Name of the created topic.



* **Return type**

    str


### Examples

c.create(“test”)

    Create the topic “test” with one partition, and block until it is created.

c.create(“test”, partitions=2)

    Create the topic “test” with two partitions, and block until it is created.

c.create(“test”, config={“retention.ms”: “4711”})

    Create the topic “test” with one partition, a retention time of 4711ms and block until it is created.

c.create(“test”, block=False)

    Create the topic “test” with one partition, and *do not* block until it is created.


#### create_acl(restype='any', name=None, resource_pattern_type='any', principal=None, host=None, operation='any', permission_type='any')
Create an ACL.

Create an ACL on the cluster.


* **Parameters**

    
    * **restype** (*str**, **optional*) – The resource type (“unknown”, “any”, “topic”, “group” or “broker”). Defaults to “any”.


    * **name** (*str**, **optional*) – The name. Defaults to None.


    * **resource_pattern_type** (*str**, **optional*) – The resource pattern type (“unknown”, “any”, “match”, “literal” or “prefixed”). Defaults to “any”.


    * **principal** (*str**, **optional*) – The principal. Defaults to None.


    * **host** (*str**, **optional*) – The host. Defaults to None.


    * **operation** (*str**, **optional*) – The operation (“unknown”, “any”, “all”, “read”, “write”, “create”, “delete”, “alter”, “describe”, “cluster_action”, “describe_configs”, “alter_configs”, “idempotent_write”). Defaults to “any”


    * **permission_type** (*str**, **optional*) – The permission type (“unknown”, “any”, “deny” or “allow”). Defaults to “any”.



* **Returns**

    ACL Binding dictionary (converted from an confluent_kafka.AclBinding object) of the created ACL.



* **Return type**

    aclBinding_dict


### Examples

c.create_acl(restype=”topic”, name=”test”, resource_pattern_type=”literal”, principal=”User:abc”, host=”\*”, operation=”read”, permission_type=”allow”):

    Grant user “abc” read permission on topic “test”.


#### delete(pattern_str_or_str_list, block=True)
Delete topics.

Delete those topics whose names match the bash-like pattern (or list of patterns) pattern_str_or_str_list.


* **Parameters**

    
    * **pattern_str_or_str_list** (*str** | **list**(**str**)*) – The pattern (or list of patterns) for selecting the topics to be deleted.


    * **block** (*bool**, **optional*) – Block until the topic is deleted. Defaults to True.



* **Returns**

    List of strings of names of the deleted topics.



* **Return type**

    list(str)


### Examples

c.delete(“test”)

    Delete the topic “test” and block until it is deleted.

c.delete(“test”, block=False)

    Delete the topic “test” and *do not* block until it is deleted.

c.delete([“test\*”, “bla\*”])

    Delete all topics starting with “test” or “bla”.


#### delete_acl(restype=ResourceType.ANY, name=None, resource_pattern_type=ResourcePatternType.ANY, principal=None, host=None, operation=AclOperation.ANY, permission_type=AclPermissionType.ANY)
Delete ACLs.

Delete ACLs from the cluster.


* **Parameters**

    
    * **restype** (*str**, **optional*) – The resource type (“unknown”, “any”, “topic”, “group” or “broker”). Defaults to “any”.


    * **name** (*str**, **optional*) – The name. Defaults to None.


    * **resource_pattern_type** (*str**, **optional*) – The resource pattern type (“unknown”, “any”, “match”, “literal” or “prefixed”). Defaults to “any”.


    * **principal** (*str**, **optional*) – The principal. Defaults to None.


    * **host** (*str**, **optional*) – The host. Defaults to None.


    * **operation** (*str**, **optional*) – The operation (“unknown”, “any”, “all”, “read”, “write”, “create”, “delete”, “alter”, “describe”, “cluster_action”, “describe_configs”, “alter_configs”, “idempotent_write”). Defaults to “any”


    * **permission_type** (*str**, **optional*) – The permission type (“unknown”, “any”, “deny” or “allow”). Defaults to “any”.



* **Returns**

    List of ACL Binding dictionaries (converted from confluent_kafka.AclBinding objects) of the deleted ACLs.



* **Return type**

    List(aclBinding_dict)


### Examples

c.delete_acl(restype=”topic”, name=”test”, resource_pattern_type=”literal”, principal=”User:abc”, host=”\*”, operation=”read”, permission_type=”allow”):

    Delete the ACL which granted user “abc” read permission on topic “test”.


#### describe(pattern_str_or_str_list)
Describe topics.

Describe all topics matching the bash-like pattern (or list of patterns) pattern_str_or_str_list.


* **Parameters**

    **pattern_str_or_str_list** (*str** | **list**(**str**)*) – The pattern (or list of patterns) for selecting the topics.



* **Returns**

    Dictionary of strings (topic names) and topic dictionaries describing the topic (converted from confluent_kafka.TopicMetadata objects).



* **Return type**

    dict(str, topic_dict)


### Examples

c.describe(“test”)

    Describe the topic “test”.

c.describe([“test\*”, “bla\*”])

    Describe all topics whose names start with “test” or “bla”.


#### describe_groups(pattern_str_or_str_list)
Describe consumer groups on the cluster.

Describe consumer groups on the cluster whose names match bash-like patterns.


* **Parameters**

    **pattern** (*str** | **list**(**str**)**, **optional*) – The pattern or list of patterns for selecting those consumer groups which shall be listed. Defaults to None.



* **Returns**

    Dictionary of strings (consumer group names) and group dictionaries describing the consumer group (converted from confluent_kafka.GroupMetadata objects).



* **Return type**

    dict(str, group_dict)


### Examples

c.describe_groups(“test\*”)

    Describe all those consumer groups of the cluster whose name starts with “test”.

c.describe_groups([“test\*”, “bla\*”])

    Describe all those consumer groups of the cluster whose name starts with “test” or “bla”.


#### download(topic_str, path_str, flatmap_function=<function Cluster.<lambda>>, group=None, offsets=None, config={}, key_type='str', value_type='str', key_value_separator=None, message_separator='\\n', overwrite=True, n=-1, batch_size=1)
Download messages from a topic to a local file while optionally transforming them in a flatmap-like manner.

Subscribe to and consume messages from a topic, optionally transform them in a flatmap-like manner and write the resulting messages to a local file, optionally explicitly set the consumer group, initial offsets, and augment the consumer configuration. Stops either if the consume timeout is exceeded (`consume.timeout` in the kash.py cluster configuration) or the number of messages specified in `n` has been consumed.


* **Parameters**

    
    * **topic_str** (*str*) – The topic to subscribe to and consume from.


    * **flatmap_function** (*function**, **optional*) – Flatmap function (takes a message dictionary and returns a list of message dictionaries). Defaults to lambda x: [x] (=the identify function for flatmap, leading to a one-to-one copy from the messages in the topic to the messages in the file).


    * **group** (*str**, **optional*) – Consumer group name used for subscribing to the topic. If set to None, creates a new unique consumer group name. Defaults to None.


    * **offsets** (*dict**, **optional*) – Dictionary of offsets (keys: partitions (int), values: offsets for the partitions (int)) for subscribing to the topic. If set to None, subscribe to the topic using the offsets from the consumer group. Defaults to None.


    * **config** (*dict**(**str**, **str**)**, **optional*) – Dictionary of strings (keys) and strings (values) to augment the consumer configuration for the topic. Defaults to {}.


    * **key_type** (*str**, **optional*) – Message key type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “str”.


    * **value_type** (*str**, **optional*) – Message value type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “str”.


    * **key_value_separator** (*str**, **optional*) – The separator between the keys and the values in the local file to write to, e.g. “:”. If set to None, only write the values, not the keys. Defaults to None.


    * **message_separator** (*str**, **optional*) – The separator between individual messages in the local file to write to. Defaults to the newline character.


    * **overwrite** (*bool**, **optional*) – Overwrite the local file if set to True, append to it otherwise. Defaults to True.


    * **n** (*int**, **optional*) – Number of messages to consume from the topic. Defaults to ALL_MESSAGES = -1.


    * **batch_size** (*int**, **optional*) – Maximum number of messages to consume from the topic at a time. Defaults to 1.



* **Returns**

    Pair of integers of the number of messages consumed from the topic, and the number of lines/messages written to the local file.



* **Return type**

    tuple(int, int)


### Examples

c.download(“test”, “./test.txt”)

    Download the messages from topic “test” to the local file “./test.txt”.


#### exists(topic_str)
Test whether a topic exists on the cluster.

Test whether a topic exists on the cluster.


* **Parameters**

    **topic_str** (*str*) – A topic.



* **Returns**

    True if the topic topic_str exists, False otherwise.



* **Return type**

    bool


### Examples

c.exists(“test”)

    Test whether the topic “test” exists on the cluster.


#### flatmap(topic_str, flatmap_function, group=None, offsets=None, config={}, key_type='str', value_type='str', n=-1, batch_size=1)
Subscribe to and consume messages from a topic and transform them in a flatmap-like manner.

Subscribe to and consume messages from a topic and transform them in a flatmap-like manner, optionally explicitly set the consumer group, initial offsets, and augment the consumer configuration. Stops either if the consume timeout is exceeded (`consume.timeout` in the kash.py cluster configuration) or the number of messages specified in `n` has been consumed.


* **Parameters**

    
    * **topic_str** (*str*) – The topic to subscribe to and consume from.


    * **flatmap_function** (*function*) – Flatmap function (takes a message dictionary and returns a list of message dictionaries).


    * **group** (*str**, **optional*) – Consumer group name used for subscribing to the topic. If set to None, creates a new unique consumer group name. Defaults to None.


    * **offsets** (*dict**, **optional*) – Dictionary of offsets (keys: partitions (int), values: offsets for the partitions (int)) for subscribing to the topic. If set to None, subscribe to the topic using the offsets from the consumer group. Defaults to None.


    * **config** (*dict**(**str**, **str**)**, **optional*) – Dictionary of strings (keys) and strings (values) to augment the consumer configuration for the topic. Defaults to {}.


    * **key_type** (*str**, **optional*) – Message key type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “str”.


    * **value_type** (*str**, **optional*) – Message value type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “str”.


    * **n** (*int**, **optional*) – Number of messages to consume from the topic. Defaults to ALL_MESSAGES = -1.


    * **batch_size** (*int**, **optional*) – Maximum number of messages to consume from the topic at a time. Defaults to 1.



* **Returns**

    Pair of the list of message dictionaries and the number of messages consumed from the topic (integer).



* **Return type**

    tuple(list(message_dict), int)


### Examples

c.flatmap(“test”, lambda x: [x])

    Consume topic “test” and return a list of all its messages as message dictionaries.

c.flatmap(“test”, lambda x: [x, x, x])

    Consume topic “test” and return a list of all its messages repeated three times.


#### flatmap_from_file(path_str, topic_str, flatmap_function, key_type='str', value_type='str', key_schema=None, value_schema=None, partition=-1, key_value_separator=None, message_separator='\\n', n=-1, bufsize=4096)
Read messages from a local file and produce them to a topic, while transforming the messages in a flatmap-like manner.

Read messages from a local file with path path_str and produce them to topic topic_str, while transforming the messages in a flatmap-like manner.


* **Parameters**

    
    * **path_str** (*str*) – The path to the local file to read from.


    * **topic_str** (*str*) – The topic to produce to.


    * **flatmap_function** (*function*) – Flatmap function (takes a message and returns a list of messages)


    * **key_type** (*str**, **optional*) – The key type (“bytes”, “str”, “json”, “avro”, “protobuf” or “pb”, or “jsonschema”). Defaults to “str”.


    * **value_type** (*str**, **optional*) – The value type (“bytes”, “str”, “json”, “avro”, “protobuf” or “pb”, or “jsonschema”). Defaults to “str”.


    * **key_schema** (*str**, **optional*) – The schema of the key of the message to be produced (if key_type is either “avro”, “protobuf” or “pb”, or “jsonschema”). Defaults to None.


    * **value_schema** (*str**, **optional*) – The schema of the value of the message to be produced (if key_type is either “avro”, “protobuf” or “pb”, or “jsonschema”). Defaults to None.


    * **partition** (*int**, **optional*) – The partition to produce to. Defaults to RD_KAFKA_PARTITION_UA = -1, i.e., the partition is selected by configured built-in partitioner.


    * **key_value_separator** (*str**, **optional*) – The separator between the keys and the values in the local file to read from, e.g. “:”. If set to None, only read the values, not the keys. Defaults to None.


    * **message_separator** (*str**, **optional*) – The separator between individual messages in the local file to read from. Defaults to the newline character.


    * **n** (*int**, **optional*) – The number of messages to read from the local file. Defaults to ALL_MESSAGES = -1.


    * **bufsize** (*int**, **optional*) – The buffer size for reading from the local file. Defaults to 4096.



* **Returns**

    Pair of the number of messages read from the local file (integer) and the number of messages produced to the topic (integer).



* **Return type**

    (int, int)


### Examples

c.flatmap(“./snacks_value.txt”, “test”, flatmap_function=lambda x: [x])

    Read all messages from the local file “./snacks_value.txt” and produce them to the topic “test”.

c.flatmap(“./snacks_value.txt”, “test”, flatmap_function=lambda x: [x, x])

    Read all messages from the local file “./snacks_value.txt” and duplicate each of them in the topic “test”.

c.flatmap(“./snacks_value.txt”, “test”, flatmap_function=lambda x: [x], value_type=”protobuf”, value_schema=’message Snack { required string name = 1; required float calories = 2; optional string colour = 3; }’)

    Read all messages from the local file “./snacks_value.txt” and produce them to the topic “test” in Protobuf using schema ‘message Snack { required string name = 1; required float calories = 2; optional string colour = 3; }’.


#### flatmap_to_file(topic_str, path_str, flatmap_function, group=None, offsets=None, config={}, key_type='str', value_type='str', key_value_separator=None, message_separator='\\n', overwrite=True, n=-1, batch_size=1)
Subscribe to and consume messages from a topic, transform them in a flatmap-like manner and write the resulting messages to a local file.

Subscribe to and consume messages from a topic, transform them in a flatmap-like manner and write the resulting messages to a local file, optionally explicitly set the consumer group, initial offsets, and augment the consumer configuration. Stops either if the consume timeout is exceeded (`consume.timeout` in the kash.py cluster configuration) or the number of messages specified in `n` has been consumed.


* **Parameters**

    
    * **topic_str** (*str*) – The topic to subscribe to and consume from.


    * **flatmap_function** (*function*) – Flatmap function (takes a message dictionary and returns a list of message dictionaries).


    * **group** (*str**, **optional*) – Consumer group name used for subscribing to the topic. If set to None, creates a new unique consumer group name. Defaults to None.


    * **offsets** (*dict**, **optional*) – Dictionary of offsets (keys: partitions (int), values: offsets for the partitions (int)) for subscribing to the topic. If set to None, subscribe to the topic using the offsets from the consumer group. Defaults to None.


    * **config** (*dict**(**str**, **str**)**, **optional*) – Dictionary of strings (keys) and strings (values) to augment the consumer configuration for the topic. Defaults to {}.


    * **key_type** (*str**, **optional*) – Message key type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “str”.


    * **value_type** (*str**, **optional*) – Message value type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “str”.


    * **key_value_separator** (*str**, **optional*) – The separator between the keys and the values in the local file to write to, e.g. “:”. If set to None, only write the values, not the keys. Defaults to None.


    * **message_separator** (*str**, **optional*) – The separator between individual messages in the local file to write to. Defaults to the newline character.


    * **overwrite** (*bool**, **optional*) – Overwrite the local file if set to True, append to it otherwise. Defaults to True.


    * **n** (*int**, **optional*) – Number of messages to consume from the topic. Defaults to ALL_MESSAGES = -1.


    * **batch_size** (*int**, **optional*) – Maximum number of messages to consume from the topic at a time. Defaults to 1.



* **Returns**

    Pair of integers of the number of messages consumed from the topic, and the number of lines/messages written to the local file.



* **Return type**

    tuple(int, int)


### Examples

c.flatmap_to_file(“test”, “./test.txt”, lambda x: [x])

    Consume all the messages from topic “test” and write them to the local file “./test.txt”.

c.flatmap_to_file(“test”, “./test3.txt”, lambda x: [x, x, x])

    Consume all the messages from topic “test” and write three messages for each of them to the local file “./test3.txt”.


#### flush()
Wait for all messages in the Producer queue to be delivered.

Wait for all messages in the Producer queue to be delivered. Uses the “flush.timeout” setting from the cluster configuration file (“kash”-section).


#### foldl(topic_str, foldl_function, initial_acc, group=None, offsets=None, config={}, key_type='str', value_type='str', n=-1, batch_size=1)
Subscribe to and consume messages from a topic and transform them in a foldl-like manner.

Subscribe to and consume messages from a topic and transform them in a foldl-like manner, optionally explicitly set the consumer group, initial offsets, and augment the consumer configuration. Stops either if the consume timeout is exceeded (`consume.timeout` in the kash.py cluster configuration) or the number of messages specified in `n` has been consumed.


* **Parameters**

    
    * **topic_str** (*str*) – The topic to subscribe to and consume from.


    * **foldl_function** (*function*) – Foldl function (takes an accumulator (any type) and a message dictionary and returns the updated accumulator).


    * **initial_acc** – Initial value of the accumulator (any type).


    * **group** (*str**, **optional*) – Consumer group name used for subscribing to the topic. Creates a new unique consumer group name if set to None. Defaults to None.


    * **offsets** (*dict**, **optional*) – Dictionary of offsets (keys: partitions (int), values: offsets for the partitions (int)) for subscribing to the topic. If set to None, subscribe to the topic using the offsets from the consumer group. Defaults to None.


    * **config** (*dict**(**str**, **str**)**, **optional*) – Dictionary of strings (keys) and strings (values) to augment the consumer configuration for the topic. Defaults to {}.


    * **key_type** (*str**, **optional*) – Message key type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “str”.


    * **value_type** (*str**, **optional*) – Message value type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “str”.


    * **n** (*int**, **optional*) – Number of messages to consume from the topic. Defaults to ALL_MESSAGES = -1.


    * **batch_size** (*int**, **optional*) – Maximum number of messages to consume from the topic at a time. Defaults to 1.



* **Returns**

    Pair of the accumulator (any type) and the number of messages consumed from the topic (integer).



* **Return type**

    tuple(acc, int)


### Examples

c.foldl(“test”, lambda acc, message_dict: acc + [message_dict], [])

    Consume topic “test” and return a list of all its messages as message dictionaries.

c.foldl(“test”, lambda acc, x: acc + x[“value”][“calories”], 0, value_type=”json”)

    Consume topic “test” and sum up the “calories” value of the individual messages.


#### foreach(topic_str, foreach_function, group=None, offsets=None, config={}, key_type='str', value_type='str', n=-1, batch_size=1)
Subscribe to and consume messages from a topic and call an operation on each of them.

Subscribe to and consume messages from a topic and call an operation on each of them, optionally explicitly set the consumer group, initial offsets, and augment the consumer configuration. Stops either if the consume timeout is exceeded (`consume.timeout` in the kash.py cluster configuration) or the number of messages specified in `n` has been consumed.


* **Parameters**

    
    * **topic_str** (*str*) – The topic to subscribe to and consume from.


    * **foreach_function** (*function*) – Foreach function (takes a message dictionary and returns None).


    * **group** (*str**, **optional*) – Consumer group name used for subscribing to the topic. If set to None, creates a new unique consumer group name. Defaults to None.


    * **offsets** (*dict**, **optional*) – Dictionary of offsets (keys: partitions (int), values: offsets for the partitions (int)) for subscribing to the topic. If set to None, subscribe to the topic using the offsets from the consumer group. Defaults to None.


    * **config** (*dict**(**str**, **str**)**, **optional*) – Dictionary of strings (keys) and strings (values) to augment the consumer configuration for the topic. Defaults to {}.


    * **key_type** (*str**, **optional*) – Message key type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “str”.


    * **value_type** (*str**, **optional*) – Message value type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “str”.


    * **n** (*int**, **optional*) – Number of messages to consume from the topic. Defaults to ALL_MESSAGES = -1.


    * **batch_size** (*int**, **optional*) – Maximum number of messages to consume from the topic at a time. Defaults to 1.



* **Returns**

    Number of messages consumed from the topic (integer).



* **Return type**

    int


### Examples

c.foreach(“test”, print)

    Consume topic “test” and print out all the consumed messages to standard out/the console.


#### grep(topic_str, re_pattern_str, group=None, offsets=None, config={}, key_type='str', value_type='str', n=-1, batch_size=1)
Find matching messages in a topic (regular expression matching).

Find matching messages in a topic using regular expression matching. Optionally explicitly set the consumer group, initial offsets, and augment the consumer configuration. Stops either if the consume timeout is exceeded (`consume.timeout` in the kash.py cluster configuration) or the number of messages specified in `n` has been consumed.


* **Parameters**

    
    * **topic_str** (*str*) – The topic to subscribe to and consume from.


    * **re_pattern_str** (*str*) – Regular expression to for matching messages.


    * **group** (*str**, **optional*) – Consumer group name used for subscribing to the topic. If set to None, creates a new unique consumer group name. Defaults to None.


    * **offsets** (*dict**, **optional*) – Dictionary of offsets (keys: partitions (int), values: offsets for the partitions (int)) for subscribing to the topic. If set to None, subscribe to the topic using the offsets from the consumer group. Defaults to None.


    * **config** (*dict**(**str**, **str**)**, **optional*) – Dictionary of strings (keys) and strings (values) to augment the consumer configuration for the topic. Defaults to {}.


    * **key_type** (*str**, **optional*) – Message key type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “str”.


    * **value_type** (*str**, **optional*) – Message value type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “str”.


    * **n** (*int**, **optional*) – Number of messages to consume from the topic. Defaults to ALL_MESSAGES = -1.


    * **batch_size** (*int**, **optional*) – Maximum number of messages to consume from the topic at a time. Defaults to 1.



* **Returns**

    Tuple of the list of message dictionaries of the matching messages, the number of matching messages (integer), and the number of messages consumed from the topic (integer).



* **Return type**

    tuple(list(message_dict), int, int)


### Examples

c.grep(“test”, “.\*name.\*cake”)

    Consume topic “test” and return all messages whose value matches the regular expression “.\*name.\*cake”.


#### grep_fun(topic_str, match_function, group=None, offsets=None, config={}, key_type='str', value_type='str', n=-1, batch_size=1)
Find matching messages in a topic (custom function matching).

Find matching messages in a topic using a custom match function match_function. Optionally explicitly set the consumer group, initial offsets, and augment the consumer configuration. Stops either if the consume timeout is exceeded (`consume.timeout` in the kash.py cluster configuration) or the number of messages specified in `n` has been consumed.


* **Parameters**

    
    * **topic_str** (*str*) – The topic to subscribe to and consume from.


    * **match_function** (*function*) – Match function (takes a message dictionary and returns a True for a match and False otherwise).


    * **group** (*str**, **optional*) – Consumer group name used for subscribing to the topic. If set to None, creates a new unique consumer group name. Defaults to None.


    * **offsets** (*dict**, **optional*) – Dictionary of offsets (keys: partitions (int), values: offsets for the partitions (int)) for subscribing to the topic. If set to None, subscribe to the topic using the offsets from the consumer group. Defaults to None.


    * **config** (*dict**(**str**, **str**)**, **optional*) – Dictionary of strings (keys) and strings (values) to augment the consumer configuration for the topic. Defaults to {}.


    * **key_type** (*str**, **optional*) – Message key type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “str”.


    * **value_type** (*str**, **optional*) – Message value type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “str”.


    * **n** (*int**, **optional*) – Number of messages to consume from the topic. Defaults to ALL_MESSAGES = -1.


    * **batch_size** (*int**, **optional*) – Maximum number of messages to consume from the topic at a time. Defaults to 1.



* **Returns**

    Tuple of the list of message dictionaries of the matching messages, the number of matching messages (integer), and the number of messages consumed from the topic (integer).



* **Return type**

    tuple(list(message_dict), int, int)


### Examples

c.grep_fun(“test”, lambda x: x[“value”][“name”] == “cake”, value_type=”json”)

    Consume topic “test” and return all messages whose value is a JSON with the “name” attribute set to “cake”.


#### groups(pattern=None)
List consumer groups on the cluster.

List consumer groups on the cluster. Optionally return only those consumer groups whose names match bash-like patterns.


* **Parameters**

    **pattern** (*str** | **list**(**str**)**, **optional*) – The pattern or list of patterns for selecting those consumer groups which shall be listed. Defaults to None.



* **Returns**

    List of strings (consumer group names).



* **Return type**

    list(str)


### Examples

c.groups()

    List all consumer groups of the cluster.

c.groups(“test\*”)

    List all those consumer groups of the cluster whose name starts with “test”.

c.groups([“test\*”, “bla\*”])

    List all those consumer groups of the cluster whose name starts with “test” or “bla”.


#### l(pattern=None, size=True, partitions=False)
List topics on the cluster (shortcut for topics(size=True), a la the “l” alias in bash).

List topics on the cluster. Optionally return only those topics whose names match bash-like patterns. Optionally return the total sizes of the topics and the sizes of their individual partitions.


* **Parameters**

    
    * **pattern** (*str**, **optional*) – The pattern or list of patterns for selecting those topics which shall be listed. Defaults to None.


    * **size** (*bool**, **optional*) – Return the total sizes of the topics if set to True. Defaults to True.


    * **partitions** (*bool**, **optional*) – Return the sizes of the individual partitions of the topics if set to True. Defaults to False.



* **Returns**

    List of strings if size=False and partitions=False; dictionary of strings (topic name) and integers (total size of the topic) if size=True and partitions=False; dictionary of strings (topic name) and dictionaries of integers (partition) and integers (size of the partition) if size=False and partitions=True; dictionary of strings (topic name) and pairs of integers (total size of the topic) and dictionaries of integers (partition) and integers (size of the partition) if size=True and partitions=True.



* **Return type**

    list(str) | dict(str, int) | dict(str, dict(int, int)) | dict(str, tuple(int, dict(int, int)))


### Examples

c.l()

    List all the topics of the cluster and their total sizes.

c.l(size=False)

    List all the topics of the cluster.

c.l(partitions=True)

    List all the topics of the cluster, their total sizes and the sizes of their individual partitions.

c.l(size=False, partitions=True)

    List all the topics of the cluster and the sizes of their individual partitions.

c.l(“test\*”)

    List all those topics of the cluster whose name starts with “test” and their total sizes.

c.l([“test\*”, “bla\*”])

    List all those topics of the cluster whose name starts with “test” or “bla”.


#### ll(pattern=None, size=True, partitions=False)
List topics on the cluster (shortcut for topics(size=True), a la the “ll” alias in bash).

List topics on the cluster. Optionally return only those topics whose names match bash-like patterns. Optionally return the total sizes of the topics and the sizes of their individual partitions.


* **Parameters**

    
    * **pattern** (*str**, **optional*) – The pattern or list of patterns for selecting those topics which shall be listed. Defaults to None.


    * **size** (*bool**, **optional*) – Return the total sizes of the topics if set to True. Defaults to True.


    * **partitions** (*bool**, **optional*) – Return the sizes of the individual partitions of the topics if set to True. Defaults to False.



* **Returns**

    List of strings if size=False and partitions=False; dictionary of strings (topic name) and integers (total size of the topic) if size=True and partitions=False; dictionary of strings (topic name) and dictionaries of integers (partition) and integers (size of the partition) if size=False and partitions=True; dictionary of strings (topic name) and pairs of integers (total size of the topic) and dictionaries of integers (partition) and integers (size of the partition) if size=True and partitions=True.



* **Return type**

    list(str | dict(str, int) | dict(str, dict(int, int)) | dict(str, tuple(int, dict(int, int))))


### Examples

c.ll()

    List all the topics of the cluster and their total sizes.

c.ll(size=False)

    List all the topics of the cluster.

c.ll(partitions=True)

    List all the topics of the cluster, their total sizes and the sizes of their individual partitions.

c.ll(size=False, partitions=True)

    List all the topics of the cluster and the sizes of their individual partitions.

c.ll(“test\*”)

    List all those topics of the cluster whose name starts with “test” and their total sizes.

c.ll([“test\*”, “bla\*”])

    List all those topics of the cluster whose name starts with “test” or “bla” and their total sizes.


#### ls(pattern=None, size=False, partitions=False)
List topics on the cluster (shortcut for topics()).

List topics on the cluster. Optionally return only those topics whose names match bash-like patterns. Optionally return the total sizes of the topics and the sizes of their individual partitions.


* **Parameters**

    
    * **pattern** (*str**, **optional*) – The pattern or list of patterns for selecting those topics which shall be listed. Defaults to None.


    * **size** (*bool**, **optional*) – Return the total sizes of the topics if set to True. Defaults to False.


    * **partitions** (*bool**, **optional*) – Return the sizes of the individual partitions of the topics if set to True. Defaults to False.



* **Returns**

    List of strings if size=False and partitions=False; dictionary of strings (topic name) and integers (total size of the topic) if size=True and partitions=False; dictionary of strings (topic name) and dictionaries of integers (partition) and integers (size of the partition) if size=False and partitions=True; dictionary of strings (topic name) and pairs of integers (total size of the topic) and dictionaries of integers (partition) and integers (size of the partition) if size=True and partitions=True.



* **Return type**

    list(str) | dict(str, int) | dict(str, dict(int, int)) | dict(str, tuple(int, dict(int, int)))


### Examples

c.ls()

    List all topics of the cluster.

c.ls(size=True)

    List all the topics of the cluster and their total sizes.

c.ls(partitions=True)

    List all the topics of the cluster and the sizes of their individual partitions.

c.ls(size=True, partitions=True)

    List all the topics of the cluster, their total sizes and the sizes of their individual partitions.

c.ls(“test\*”)

    List all those topics of the cluster whose name starts with “test”.

c.ls([“test\*”, “bla\*”])

    List all those topics of the cluster whose name starts with “test” or “bla”.


#### map(topic_str, map_function, group=None, offsets=None, config={}, key_type='str', value_type='str', n=-1, batch_size=1)
Subscribe to and consume messages from a topic and transform them in a map-like manner.

Subscribe to and consume messages from a topic and transform them in a map-like manner, optionally explicitly set the consumer group, initial offsets, and augment the consumer configuration. Stops either if the consume timeout is exceeded (`consume.timeout` in the kash.py cluster configuration) or the number of messages specified in `n` has been consumed.


* **Parameters**

    
    * **topic_str** (*str*) – The topic to subscribe to and consume from.


    * **map_function** (*function*) – Map function (takes a message dictionary and returns a message dictionary).


    * **group** (*str**, **optional*) – Consumer group name used for subscribing to the topic. If set to None, creates a new unique consumer group name. Defaults to None.


    * **offsets** (*dict**, **optional*) – Dictionary of offsets (keys: partitions (int), values: offsets for the partitions (int)) for subscribing to the topic. If set to None, subscribe to the topic using the offsets from the consumer group. Defaults to None.


    * **config** (*dict**(**str**, **str**)**, **optional*) – Dictionary of strings (keys) and strings (values) to augment the consumer configuration for the topic. Defaults to {}.


    * **key_type** (*str**, **optional*) – Message key type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “str”.


    * **value_type** (*str**, **optional*) – Message value type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “str”.


    * **n** (*int**, **optional*) – Number of messages to consume from the topic. Defaults to ALL_MESSAGES = -1.


    * **batch_size** (*int**, **optional*) – Maximum number of messages to consume from the topic at a time. Defaults to 1.



* **Returns**

    Pair of the list of message dictionaries and the number of messages consumed from the topic (integer).



* **Return type**

    tuple(list(message_dict), int)


### Examples

c.map(“test”, lambda x: x)

    Consume topic “test” and return a list of all its messages as message dictionaries.

c.map(“test”, lambda x: x[“value”].update({“colour”: “yellow”}) or x, value_type=”json”)

> Consume topic “test” and return a list of all its messages where the “colour” is set to “yellow”.


#### offsets(timeout=-1.0)
Get committed offsets of the subscribed topic.

Get committed offsets of the subscribed topic.


* **Parameters**

    **timeout** (*float**, **optional*) – Timeout (in seconds) for calling confluent_kafka.committed(). Defaults to -1.0 (no timeout).



* **Returns**

    Dictionary of partitions (integers) and offsets (integers).



* **Return type**

    offsets_dict


### Examples

c.offsets()

    Get the offsets of the subscribed topic.

c.offsets(timeout=1.0)

    Get the offsets of the subscribed topic, using a timeout of 1 second.


#### offsets_for_times(pattern_str_or_str_list, partition_int_timestamp_int_dict, timeout=-1.0)
Look up offsets corresponding to message timestamps in the partitions of topics.

Look up those offsets in the individual partitions of all topics matching the bash-like pattern (or list of patterns) pattern_str_or_str_list which correspond to the timestamps provided in partition_int_timestamp_int_dict (for the individual partitions).


* **Parameters**

    
    * **pattern_str_or_str_list** (*str** | **list**(**str**)*) – The pattern (or list of patterns) for selecting the topics.


    * **partition_int_timestamp_int_dict** (*dict**(**int**, **int**)*) – Dictionary of integers (partitions) and integers (timestamps).


    * **timeout** (*float**, **optional*) – The timeout (in seconds) for the individual offsets_for_times() method calls from confluent_kafka.Consumer. Defaults to -1.0 (infinite=no timeout).



* **Returns**

    Dictionary of strings (topic names) and dictionaries of integers (partitions) and integers (offsets).



* **Return type**

    dict(str, dict(int, int))


### Examples

c.offsets_for_times(“test”, {0: 1664644769886})

    Look up the offset of the first message in the first partition of the topic “test” which has a timestamp greater or equal to 1664644769886 milliseconds from epoch. If the provided timestamp exceeds that of the last message in the partition, a value of -1 will be returned.

c.offsets_for_times(“te\*st”, {0: 1664644769886, 1: 1664645155987}, timeout=1.0)

    Look up the offset of the first message in the first partition of those topics starting with “te” and ending with “st” with a timestamp greater or equal to 1664644769886 milliseconds from epoch; and look up the offset of the first message in the second partition of those topics with a timestamp greater or equal to 1664645155987 milliseconds from epoch. Time out the internally used offsets_for_times() calls after one second.


#### partitions(pattern_str_or_str_list)
Get the number of partitions of topics.

Get the number of partitions of all topics matching the bash-like pattern (or list of patterns) pattern_str_or_str_list.


* **Parameters**

    **pattern_str_or_str_list** (*str** | **list**(**str**)*) – The pattern (or list of patterns) for selecting the topics.



* **Returns**

    Dictionary of strings (topic names) and their respective numbers of partitions.



* **Return type**

    dict(str, int)


### Examples

c.partitions(“test”)

    Get the number of partitions of the topic “test”.

c.partitions([“test\*”, “bla\*”])

    Get the numbers of partitions of all topics whose names start with “test” or “bla”.


#### produce(topic_str, value, key=None, key_type='str', value_type='str', key_schema=None, value_schema=None, partition=-1, timestamp=0, headers=None)
Produce a message to a topic.

Produce a message to a topic. The key and the value of the message can be either bytes, a string, a dictionary, or a schema-based format supported by the Confluent Schema Registry (currently Avro, Protobuf or JSONSchema).


* **Parameters**

    
    * **topic_str** (*str*) – The topic to produce to.


    * **value** (*bytes** | **str** | **dict*) – The value of the message to be produced.


    * **key** (*bytes** | **str** | **dict**, **optional*) – The key of the message to be produced. Defaults to None.


    * **key_type** (*str**, **optional*) – The key type (“bytes”, “str”, “json”, “avro”, “protobuf” or “pb”, or “jsonschema”). Defaults to “str”.


    * **value_type** (*str**, **optional*) – The value type (“bytes”, “str”, “json”, “avro”, “protobuf” or “pb”, or “jsonschema”). Defaults to “str”.


    * **key_schema** (*str**, **optional*) – The schema of the key of the message to be produced (if key_type is either “avro”, “protobuf” or “pb”, or “jsonschema”). Defaults to None.


    * **value_schema** (*str**, **optional*) – The schema of the value of the message to be produced (if key_type is either “avro”, “protobuf” or “pb”, or “jsonschema”). Defaults to None.


    * **partition** (*int**, **optional*) – The partition to produce to. Defaults to RD_KAFKA_PARTITION_UA = -1, i.e., the partition is selected by configured built-in partitioner.


    * **timestamp** (*int**, **optional*) – Message timestamp (CreateTime) in milliseconds since epoch UTC. Defaults to CURRENT_TIME = 0.


    * **headers** (*dict** | **list*) – Message headers to set on the message. The header key must be a string while the value must be binary, unicode or None. Accepts a list of (key,value) or a dict.



* **Returns**

    Pair of bytes or string and bytes or string (=the key and the value of the produced message).



* **Return type**

    tuple(bytes | str, bytes | str)


### Examples

c.produce(“test”, “value 1”)

    Produce a message with value = “value 1” and key = None to the topic “test”.

c.produce(“test”, “value 1”, key=”key 1”)

    Produce a message with value = “value 1” and key = “key 1” to the topic “test”.

c.produce(“test”, “value 1”, key=”key 1”, partition=0)

    Produce a message with value = “value 1” and key = “key 1” to partition 0 of the topic “test”.

c.produce(“test”, “value 1”, key=”key 1”, timestamp=1664902656169)

    Produce a message with value = “value 1” and key = “key 1” to the topic “test”, set the timestamp of this message to 1664902656169.

c.produce(“test”, “value 1”, headers={“bla”: “blups”})

    Produce a message with value = “value 1” and key = None to the topic “test”, set the headers of this message to {“bla”: “blups”}.

c.produce(“test”, {‘name’: ‘cookie’, ‘calories’: 500.0, ‘colour’: ‘brown’}, value_type=”json”)

    Produce a message with value = {‘name’: ‘cookie’, ‘calories’: 500.0, ‘colour’: ‘brown’} and key = None to the topic “test”, using JSON without schema.

c.produce(“test”, {‘name’: ‘cookie’, ‘calories’: 500.0, ‘colour’: ‘brown’}, value_type=”avro”, value_schema=’{ “type”: “record”, “name”: “myrecord”, “fields”: [{“name”: “name”,  “type”: “string” }, {“name”: “calories”, “type”: “float” }, {“name”: “colour”, “type”: “string” }] }’)

    Produce a message with value = {‘name’: ‘cookie’, ‘calories’: 500.0, ‘colour’: ‘brown’} and key = None to the topic “test”, using Avro with the schema ‘{ “type”: “record”, “name”: “myrecord”, “fields”: [{“name”: “name”,  “type”: “string” }, {“name”: “calories”, “type”: “float” }, {“name”: “colour”, “type”: “string” }] }’.

c.produce(“test”, {‘name’: ‘cookie’, ‘calories’: 500.0, ‘colour’: ‘brown’}, value_type=”protobuf”, value_schema=’message Snack { required string name = 1; required float calories = 2; optional string colour = 3; }’)

    Produce a message with value = {‘name’: ‘cookie’, ‘calories’: 500.0, ‘colour’: ‘brown’} and key = None to the topic “test”, using Protobuf with the schema ‘message Snack { required string name = 1; required float calories = 2; optional string colour = 3; }’.

c.produce(“test”, {‘name’: ‘cookie’, ‘calories’: 500.0, ‘colour’: ‘brown’}, value_type=”jsonschema”, value_schema=’{ “title”: “abc”, “definitions”

    Produce a message with value = {‘name’: ‘cookie’, ‘calories’: 500.0, ‘colour’: ‘brown’} and key = None to the topic “test”, using JSONSchema with the schema ‘{ “title”: “abc”, “definitions” : { “record:myrecord” : { “type” : “object”, “required” : [ “name”, “calories” ], “additionalProperties” : false, “properties” : { “name” : {“type” : “string”}, “calories” : {“type” : “number”}, “colour” : {“type” : “string”} } } }, “$ref” : “#/definitions/record:myrecord” }’.


#### recreate(topic_str)
Recreate a topic.

Recreate a topic by 1) replicating it to a temporary topic, 2) deleting the original topic, 3) re-creating the original topic, and 4) replicating the temporary topic back to the original topic.


* **Parameters**

    **topic_str** (*str*) – The topic to recreate.



* **Returns**

    Pair of pairs of the number of messages; the first pair indicating the number of messages consumed from the original topic and produced to the temporary topic, the second pair indicating the number of messages consumed from the temporary topic and produced back to the re-created original topic.



* **Return type**

    tuple(tuple(int, int), tuple(int, int))


### Examples

c.recreate(“test”)

    Recreate the topic “test”.


#### rm(pattern_str_or_str_list, block=True)
Delete topics (shortcut for delete(), a la bash “rm”)

Delete those topics whose names match the bash-like pattern (or list of patterns) pattern_str_or_str_list.


* **Parameters**

    
    * **pattern_str_or_str_list** (*str** | **list**(**str**)*) – The pattern (or list of patterns) for selecting the topics to be deleted.


    * **block** (*bool**, **optional*) – Block until the topic is deleted. Defaults to True.



* **Returns**

    List of strings of names of the deleted topics.



* **Return type**

    list(str)


### Examples

c.rm(“test”)

    Delete the topic “test” and block until it is deleted.

c.rm(“test”, block=False)

    Delete the topic “test” and *do not* block until it is deleted.

c.rm([“test\*”, “bla\*”])

    Delete all topics starting with “test” or “bla”.


#### set_broker_config(pattern_int_or_str_or_int_or_str_list, key_str, value_str, test=False)
Set a configuration item of brokers.

Set the configuration item with key key_str and value value_str of those brokers whose identifiers match the bash-like pattern (or list of patterns) pattern_int_or_str_or_int_or_str_list.


* **Parameters**

    
    * **pattern_str_or_str_list** (*str** | **list**(**str**)*) – The pattern (or list of patterns) for selecting those topics whose configuration shall be changed.


    * **key_str** (*str*) – Configuration key.


    * **value_str** (*str*) – Configuration value.


    * **test** (*bool**, **optional*) – If True, the request is only validated without changing the configuration. Defaults to False.



* **Returns**

    Dictionary of integers (broker identifiers) and tuples of strings (configuration keys) and strings (configuration values)



* **Return type**

    dict(int, tuple(str, str))


### Examples

c.set_broker_config(0, “background.threads”, “5”)

    Sets the configuration key “background.threads” to configuration value “5” for broker 0.

c.set_broker_config(0, “background.threads”, “5”, test=True)

    Verifies if the configuration key “background.threads” can be set to configuration value “5” for broker 0, but does not change the configuration.

c.set_broker_config(“[0-2]”, “background.threads”, “5”)

    Sets the configuration key “background.threads” to configuration value “5” for brokers 0, 1 and 2.


#### set_config(pattern_str_or_str_list, key_str, value_str, test=False)
Set a configuration item of topics.

Set the configuration item with key key_str and value value_str of those topics whose names match the bash-like pattern (or list of patterns) pattern_str_or_str_list.


* **Parameters**

    
    * **pattern_str_or_str_list** (*str** | **list**(**str**)*) – The pattern (or list of patterns) for selecting those topics whose configuration shall be changed.


    * **test** (*bool**, **optional*) – If True, the request is only validated without changing the configuration. Defaults to False.



* **Returns**

    Dictionary of strings (topic names) and tuples of strings (configuration keys) and strings (configuration values)



* **Return type**

    dict(str, tuple(str, str))


### Examples

c.set_config(“test”, “retention.ms”, “4711”)

    Sets the configuration key “retention.ms” to configuration value “4711” for topic “test”.

c.set_config(“test”, “retention.ms”, “4711”, test=True)

    Verifies if the configuration key “retention.ms” can be set to configuration value “4711” for topic “test”, but does not change the configuration.

c.set_config([“test\*”, “bla\*”], “42”)

    Sets the configuration key “retention.ms” to configuration value “42” for all topics whose names start with “test” or “bla”.


#### set_config_dict(resourceType, resource_str, new_config_dict, test=False)

#### set_partitions(pattern_str_or_str_list, num_partitions_int, test=False)
Set the number of partitions of topics.

Set the number of partitions of all topics matching the bash-like pattern (or list of patterns) pattern_str_or_str_list. The number of partitions of a topic can only be increased but not decreased, i.e., only additional partitions can be created.


* **Parameters**

    
    * **pattern_str_or_str_list** (*str** | **list**(**str**)*) – The pattern (or list of patterns) for selecting the topics.


    * **num_partitions_int** (*int*) – The number of partitions to set for the selected topics. The number of partitions of a topic can only be increased but not decreased, i.e., only additional partitions can be created.


    * **test** (*bool**, **optional*) – If True, the request is only validated without creating the partitions.



* **Returns**

    Dictionary of strings (topic names) and their respective new numbers of partitions.



* **Return type**

    dict(str, int)


### Examples

c.set_partitions(“test”, 2)

    Set the number of partitions for the topic “test” to 2.

c.set_partitions([“test\*”, “bla\*”], 4)

    Set the numbers of partitions of all topics whose names start with “test” or “bla” to 4.


#### set_verbose(verbose_int)
Set verbosity level.


* **Parameters**

    **verbose_int** (*int*) – Verbosity level.



#### size(pattern_str_or_str_list, timeout=-1.0)
List topics, their total sizes and the sizes of their partitions.

List topics on the cluster whose names match the pattern pattern_str, their total sizes and the sizes of their partitions.


* **Parameters**

    
    * **pattern_str_or_str_list** (*str** | **list**(**str**)*) – The pattern or a list of patterns for selecting those topics which shall be listed.


    * **timeout** (*float**, **optional*) – The timeout (in seconds) for the internally used get_watermark_offsets() method calls from confluent_kafka.Consumer. Defaults to -1.0 (infinite=no timeout).



* **Returns**

    Dictionary of strings (topic name) and pairs of integers (total size of the topic) and dictionaries of integers (partition) and integers (size of the partition).



* **Return type**

    dict(str, tuple(int, dict(int, int)))


### Examples

c.size(”\*”)

    List all topics of the cluster, their total sizes and the sizes of their partitions.

c.size([”\*test”, “bla”], timeout=1.0)

    List those topics whose name end with “test” or whose name is “bla”, their total sizes and the sizes of their partitions and time out the internally used get_watermark_offsets() method after one second.


#### subscribe(topic_str, group=None, offsets=None, config={}, key_type='str', value_type='str')
Subscribe to a topic.

Subscribe to a topic, optionally explicitly set the consumer group, initial offsets, and augment the consumer configuration. Prerequisite for consuming from a topic. Set “auto.offset.reset” to the configured “auto.offset.value” in the configuration (“kash”-section), and “enable.auto.commit” and “session.timeout.ms” as well.


* **Parameters**

    
    * **topic_str** (*str*) – The topic to subscribe to.


    * **group** (*str**, **optional*) – The consumer group name. If set to None, automatically create a new unique consumer group name. Defaults to None.


    * **offsets** (*dict**(**int**, **int**)**, **optional*) – Dictionary of integers (partitions) and integers (initial offsets for the individual partitions of the topic). If set to None, does not set any initial offsets. Defaults to None.


    * **config** (*dict**(**str**, **str**)**, **optional*) – Dictionary of strings (keys) and strings (values) to augment the consumer configuration. Defaults to {}.


    * **key_type** (*str**, **optional*) – The key type (“bytes”, “str”, “json”, “avro”, “protobuf” or “pb”, or “jsonschema”). Defaults to “str”.


    * **value_type** (*str**, **optional*) – The value type (“bytes”, “str”, “json”, “avro”, “protobuf” or “pb”, or “jsonschema”). Defaults to “str”.



* **Returns**

    Pair of the topic subscribed to (string) and the used consumer group name (string).



* **Return type**

    (str, str)


### Examples

c.subscribe(“test”)

    Subscribe to the topic “test” using an automatically created new unique consumer group.

c.subscribe(“test”, group=”test_group”)

    Susbcribe to the topic “test” using the consumer group name “test_group”.

c.subscribe(“test”, offsets={0: 42, 1: 4711})

    Subscribe to the topic “test” using an automatically created new unique consumer group. Set the initial offset for the first partition to 42, and for the second partition to 4711.

c.subscribe(“test”, config={“enable.auto.commit”: “False”})

    Subscribe to the topic “test” using an automatically created new unique consumer group. Set the configuration key “enable.auto.commit” to “False”.

c.subscribe(“test”, key_type=”avro”, value_type=”avro”)

    Subscribe to the topic “test” using an automatically created new unique consumer group. Consume with key and value type “avro”.


#### topics(pattern=None, size=False, partitions=False)
List topics on the cluster.

List topics on the cluster. Optionally return only those topics whose names match bash-like patterns. Optionally return the total sizes of the topics and the sizes of their individual partitions.


* **Parameters**

    
    * **pattern** (*str** | **list**(**str**)**, **optional*) – The pattern or list of patterns for selecting those topics which shall be listed. Defaults to None.


    * **size** (*bool**, **optional*) – Return the total sizes of the topics if set to True. Defaults to False.


    * **partitions** (*bool**, **optional*) – Return the sizes of the individual partitions of the topics if set to True. Defaults to False.



* **Returns**

    List of strings if size=False and partitions=False; dictionary of strings (topic name) and integers (total size of the topic) if size=True and partitions=False; dictionary of strings (topic name) and dictionaries of integers (partition) and integers (size of the partition) if size=False and partitions=True; dictionary of strings (topic name) and pairs of integers (total size of the topic) and dictionaries of integers (partition) and integers (size of the partition) if size=True and partitions=True.



* **Return type**

    list(str) | dict(str, int) | dict(str, dict(int, int)) | dict(str, tuple(int, dict(int, int)))


### Examples

c.topics()

    List all topics of the cluster.

c.topics(size=True)

    List all the topics of the cluster and their total sizes.

c.topics(partitions=True)

    List all the topics of the cluster and the sizes of their individual partitions.

c.topics(size=True, partitions=True)

    List all the topics of the cluster, their total sizes and the sizes of their individual partitions.

c.topics(“test\*”)

    List all those topics of the cluster whose name starts with “test”.

c.topics([“test\*”, “bla\*”])

    List all those topics of the cluster whose name starts with “test” or “bla”.


#### unsubscribe()
Unsubscribe from a topic.

Unsubscribe from a topic.


* **Returns**

    Pair of the topic unsubscribed from (string) and the used consumer group (string).



* **Return type**

    (str, str)



#### upload(path_str, topic_str, flatmap_function=<function Cluster.<lambda>>, key_type='str', value_type='str', key_schema=None, value_schema=None, key_value_separator=None, message_separator='\\n', n=-1, bufsize=4096)
Upload messages from a local file to a topic, while optionally transforming the messages in a flatmap-like manner.

Read messages from a local file with path path_str and produce them to topic topic_str, while optionally transforming the messages in a flatmap-like manner.


* **Parameters**

    
    * **path_str** (*str*) – The path to the local file to read from.


    * **topic_str** (*str*) – The topic to produce to.


    * **flatmap_function** (*function**, **optional*) – Flatmap function (takes a message and returns a list of messages). Defaults to lambda x: [x] (=the identify function for flatmap, leading to a one-to-one copy from the messages in the file to the messages in the topic).


    * **key_type** (*str**, **optional*) – The key type (“bytes”, “str”, “json”, “avro”, “protobuf” or “pb”, or “jsonschema”). Defaults to “str”.


    * **value_type** (*str**, **optional*) – The value type (“bytes”, “str”, “json”, “avro”, “protobuf” or “pb”, or “jsonschema”). Defaults to “str”.


    * **key_schema** (*str**, **optional*) – The schema of the key of the message to be produced (if key_type is either “avro”, “protobuf” or “pb”, or “jsonschema”). Defaults to None.


    * **value_schema** (*str**, **optional*) – The schema of the value of the message to be produced (if key_type is either “avro”, “protobuf” or “pb”, or “jsonschema”). Defaults to None.


    * **partition** (*int**, **optional*) – The partition to produce to. Defaults to RD_KAFKA_PARTITION_UA = -1, i.e., the partition is selected by configured built-in partitioner.


    * **key_value_separator** (*str**, **optional*) – The separator between the keys and the values in the local file to read from, e.g. “:”. If set to None, only read the values, not the keys. Defaults to None.


    * **message_separator** (*str**, **optional*) – The separator between individual messages in the local file to read from. Defaults to the newline character.


    * **n** (*int**, **optional*) – The number of messages to read from the local file. Defaults to ALL_MESSAGES = -1.


    * **bufsize** (*int**, **optional*) – The buffer size for reading from the local file. Defaults to 4096.



* **Returns**

    Pair of the number of messages read from the local file (integer) and the number of messages produced to the topic (integer).



* **Return type**

    (int, int)


### Examples

c.upload(“./snacks_value.txt”, “test”)

    Read all messages from the local file “./snacks_value.txt” and produce them to the topic “test”.

c.upload(“./snacks_value.txt”, “test”, flatmap_function=lambda x: [x, x])

    Read all messages from the local file “./snacks_value.txt” and duplicate each of them in the topic “test”.

c.upload(“./snacks_value.txt”, “test”, value_type=”protobuf”, value_schema=’message Snack { required string name = 1; required float calories = 2; optional string colour = 3; }’)

    Read all messages from the local file “./snacks_value.txt” and produce them to the topic “test” in Protobuf using schema ‘message Snack { required string name = 1; required float calories = 2; optional string colour = 3; }’.


#### verbose()
Get verbosity level.


* **Returns**

    Verbosity level.



* **Return type**

    int



#### watermarks(pattern_str_or_str_list, timeout=-1.0)
Get low and high offsets (=so called “watermarks”) of topics on the cluster.

Returns a dictionary of the topics whose names match the bash-like pattern pattern_str and the low and high offsets of their partitions.


* **Parameters**

    
    * **pattern_str_or_str_list** (*str** | **list**(**str**)*) – The pattern or list of patterns for selecting the topics.


    * **timeout** (*float**, **optional*) – The timeout (in seconds) for the individual get_watermark_offsets() method calls from confluent_kafka.Consumer. Defaults to -1.0 (infinite=no timeout).



* **Returns**

    Dictionary of strings (topic names) and dictionaries of integers (partition) and pairs of integers (low and high offsets of the respective partition of the respective topic)



* **Return type**

    dict(str, dict(int, tuple(int, int)))


### Examples

c.watermarks(“test\*”)

    Return the watermarks for all topics whose name starts with “test” and their partitions.

c.watermarks([“test”, “bla”], timeout=1.0)

    Return the watermarks for the topics “test” and “bla” and time out the internally used get_watermark_offsets() method after one second.


#### wc(topic_str, group=None, offsets=None, config={}, key_type='str', value_type='str', n=-1, batch_size=1)
Count the number of messages, words, and bytes in a topic.

Count the number of messages, words, and bytes in a topic. Optionally explicitly set the consumer group, initial offsets, and augment the consumer configuration. Stops either if the consume timeout is exceeded (`consume.timeout` in the kash.py cluster configuration) or the number of messages specified in `n` has been consumed.


* **Parameters**

    
    * **topic_str** (*str*) – The topic to subscribe to and consume from.


    * **group** (*str**, **optional*) – Consumer group name used for subscribing to the topic. If set to None, creates a new unique consumer group name. Defaults to None.


    * **offsets** (*dict**, **optional*) – Dictionary of offsets (keys: partitions (int), values: offsets for the partitions (int)) for subscribing to the topic. If set to None, subscribe to the topic using the offsets from the consumer group. Defaults to None.


    * **config** (*dict**(**str**, **str**)**, **optional*) – Dictionary of strings (keys) and strings (values) to augment the consumer configuration for the topic. Defaults to {}.


    * **key_type** (*str**, **optional*) – Message key type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “str”.


    * **value_type** (*str**, **optional*) – Message value type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “str”.


    * **n** (*int**, **optional*) – Number of messages to consume from the topic. Defaults to ALL_MESSAGES = -1.


    * **batch_size** (*int**, **optional*) – Maximum number of messages to consume from the topic at a time. Defaults to 1.



* **Returns**

    Tuple of the number of messages (integer), the number of words (integer) and the number of bytes (int) in the topic.



* **Return type**

    tuple(int, int, int)


### Examples

c.wc(“test”)

    Consume topic “test” and return the number of messages, words and bytes.


### kash.cp(source_cluster, source_topic_str, target_cluster, target_topic_str, flatmap_function=<function <lambda>>, group=None, offsets=None, config={}, source_key_type='bytes', source_value_type='bytes', target_key_type=None, target_value_type=None, target_key_schema=None, target_value_schema=None, keep_timestamps=True, n=-1, batch_size=1)
Replicate a topic and optionally transform the messages in a flatmap-like manner.

Replicate (parts of) a topic (source_topic_str) on one cluster (source_cluster) to another topic (target_topic_str) on another (or the same) cluster (target_cluster). Each replicated message can be transformed into a list of other messages in a flatmap-like manner. The source and target topics can have different message key and value types; e.g. the source topic can have value type Avro whereas the target topic will be written with value type Protobuf. Stops either if the consume timeout is exceeded on the source cluster (`consume.timeout` in the kash.py cluster configuration) or the number of messages specified in `n` has been consumed.


* **Parameters**

    
    * **source_cluster** (*Cluster*) – Source cluster


    * **source_topic_str** (*str*) – Source topic


    * **target_cluster** (*Cluster*) – Target cluster


    * **target_topic_str** (*str*) – Target topic


    * **flatmap_function** (*function**, **optional*) – Flatmap function (takes a message dictionary and returns a list of message dictionaries). Defaults to lambda x: [x].


    * **group** (*str**, **optional*) – Consumer group name used for subscribing to the source topic. If set to None, creates a new unique consumer group name. Defaults to None.


    * **offsets** (*dict**, **optional*) – Dictionary of offsets (keys: partitions (int), values: offsets for the partitions (int)) for subscribing to the source topic. If set to None, subscribe to the topic using the offsets from the consumer group for the topic. Defaults to None.


    * **config** (*dict**(**str**, **str**)**, **optional*) – Dictionary of strings (keys) and strings (values) to augment the consumer configuration for the source topic. Defaults to {}.


    * **source_key_type** (*str**, **optional*) – Source topic message key type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “bytes”.


    * **source_value_type** (*str**, **optional*) – Source topic message value type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “bytes”.


    * **target_key_type** (*str**, **optional*) – Target topic message key type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). If set to None, target_key_type = source_key_type. Defaults to None.


    * **target_value_type** (*str**, **optional*) – Target topic message value type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). If set to None, target_value_type = source_value_type. Defaults to None.


    * **target_key_schema** (*str**, **optional*) – Target key message type schema (for “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to None.


    * **target_value_schema** (*str**, **optional*) – Target value message type schema (for “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to None.


    * **keep_timestamps** (*bool**, **optional*) – Replicate the timestamps of the source messages in the target messages. Defaults to True.


    * **n** (*int**, **optional*) – Number of messages to consume from the source topic. Defaults to ALL_MESSAGES = -1.


    * **batch_size** (*int**, **optional*) – Maximum number of messages to consume from the source topic at a time. Defaults to 1.



* **Returns**

    Pair of the number of messages consumed from the source topic and the number of messages produced to the target topic.



* **Return type**

    tuple(int, int)


### Examples

cp(cluster1, “topic1”, cluster2, “topic2”)

    Replicate “topic1” on cluster1 to “topic2” on cluster2.

cp(cluster1, “topic1”, cluster2, “topic2”, flatmap_function=lambda message_dict: [message_dict, message_dict])

    Replicate “topic1” on cluster1 to “topic2” on cluster2, while duplicating each message from topic1 in topic2.

cp(cluster1, “topic1”, cluster2, “topic2”, source_value_type=”avro”, target_value_type=”protobuf”, target_value_schema=”message Snack { required string name = 1; required float calories = 2; optional string colour = 3; }”, n=100)

    Replicate the first 100 messages from “topic1” on cluster1 to “topic2” on cluster2, changing the value schema from avro to protobuf.

cp(cluster1, “topic1”, cluster2, “topic2”, offsets={0:100}, keep_timestamps=False, n=500)

    Replicate the messages 100-600 from “topic1” on cluster1 to “topic2” on cluster2. Create new timestamps for the messages produced to the target topic.


### kash.diff(cluster1, topic_str1, cluster2, topic_str2, group1=None, group2=None, offsets1=None, offsets2=None, key_type1='bytes', value_type1='bytes', key_type2='bytes', value_type2='bytes', n=-1, batch_size=1)
Create a diff of topic 1 on cluster 1 and topic 2 on cluster 2 using a diff function.

Create a diff of (parts of) a topic (topic_str1) on one cluster (cluster1) and another topic (topic_str2) on another (or the same) cluster (cluster2) with respect to their keys and values. Stops on either topic/cluster if either the consume timeout is exceeded (`consume.timeout` in the kash.py cluster configuration) or the number of messages specified in `n` has been consumed.


* **Parameters**

    
    * **cluster1** (*Cluster*) – Cluster 1


    * **topic_str1** (*str*) – Topic 1


    * **cluster2** (*Cluster*) – Cluster 2


    * **topic_str2** (*str*) – Topic 2


    * **group1** (*str**, **optional*) – Consumer group name used for consuming from topic 1. If set to None, creates a new unique consumer group name. Defaults to None.


    * **group2** (*str**, **optional*) – Consumer group name used for consuming from topic 2. If set to None, creates a new unique consumer group name. Defaults to None.


    * **offsets1** (*dict**, **optional*) – Dictionary of offsets (keys: partitions (int), values: offsets for the partitions (int)) for consuming from topic 1. If set to None, consume topic 1 using the offsets from the consumer group for topic 1. Defaults to None.


    * **offsets2** (*dict**, **optional*) – Dictionary of offsets (keys: partitions (int), values: offsets for the partitions (int)) for consuming from topic 2. If set to None, consume topic 2 using the offsets from the consumer group for topic 2. Defaults to None.


    * **key_type1** (*str**, **optional*) – Topic 1 message key type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “bytes”.


    * **value_type1** (*str**, **optional*) – Topic 1 message value type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “bytes”.


    * **key_type2** (*str**, **optional*) – Topic 2 message key type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). If set to None, target_key_type = source_key_type. Defaults to None.


    * **value_type2** (*str**, **optional*) – Topic 2 message value type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). If set to None, target_value_type = source_value_type. Defaults to None.


    * **n** (*int**, **optional*) – Number of messages to consume from the topic 1 and topic 2. Defaults to ALL_MESSAGES = -1.


    * **batch_size** (*int**, **optional*) – Maximum number of messages to consume from topic 1 and topic 2 at a time. Defaults to 1.



* **Returns**

    Tuple of message dictionaries from topic 1 and topic 2 which are different with respect to their keys and values.



* **Return type**

    list(tuple(message_dict, message_dict))


### Examples

diff(cluster1, “topic1”, cluster2, “topic2”)

    Create a diff of “topic1” on cluster1 and “topic2” on cluster2 with respect to their keys and values.


### kash.diff_fun(cluster1, topic_str1, cluster2, topic_str2, diff_function, group1=None, group2=None, offsets1=None, offsets2=None, key_type1='bytes', value_type1='bytes', key_type2='bytes', value_type2='bytes', n=-1, batch_size=1)
Create a diff of topic 1 on cluster 1 and topic 2 on cluster 2 using a diff function.

Create a diff of (parts of) a topic (topic_str1) on one cluster (cluster1) and another topic (topic_str2) on another (or the same) cluster (cluster2) using a diff function (diff_function). Stops on either topic/cluster if either the consume timeout is exceeded (`consume.timeout` in the kash.py cluster configuration) or the number of messages specified in `n` has been consumed.


* **Parameters**

    
    * **cluster1** (*Cluster*) – Cluster 1


    * **topic_str1** (*str*) – Topic 1


    * **cluster2** (*Cluster*) – Cluster 2


    * **topic_str2** (*str*) – Topic 2


    * **diff_function** (*function*) – Diff function (takes a message dictionary from topic 1 and a message dictionary from topic 2 and returns the updated accumulator)


    * **group1** (*str**, **optional*) – Consumer group name used for consuming from topic 1. If set to None, creates a new unique consumer group name. Defaults to None.


    * **group2** (*str**, **optional*) – Consumer group name used for consuming from topic 2. If set to None, creates a new unique consumer group name. Defaults to None.


    * **offsets1** (*dict**, **optional*) – Dictionary of offsets (keys: partitions (int), values: offsets for the partitions (int)) for consuming from topic 1. If set to None, consume topic 1 using the offsets from the consumer group for topic 1. Defaults to None.


    * **offsets2** (*dict**, **optional*) – Dictionary of offsets (keys: partitions (int), values: offsets for the partitions (int)) for consuming from topic 2. If set to None, consume topic 2 using the offsets from the consumer group for topic 2. Defaults to None.


    * **key_type1** (*str**, **optional*) – Topic 1 message key type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “bytes”.


    * **value_type1** (*str**, **optional*) – Topic 1 message value type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “bytes”.


    * **key_type2** (*str**, **optional*) – Topic 2 message key type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). If set to None, target_key_type = source_key_type. Defaults to None.


    * **value_type2** (*str**, **optional*) – Topic 2 message value type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). If set to None, target_value_type = source_value_type. Defaults to None.


    * **n** (*int**, **optional*) – Number of messages to consume from the topic 1 and topic 2. Defaults to ALL_MESSAGES = -1.


    * **batch_size** (*int**, **optional*) – Maximum number of messages to consume from topic 1 and topic 2 at a time. Defaults to 1.



* **Returns**

    Tuple of message dictionaries from topic 1 and topic 2 which are different according to the diff_function (=where diff_function(message_dict1, message_dict2) returned True).



* **Return type**

    list(tuple(message_dict, message_dict))


### Examples

diff_fun(cluster1, “topic1”, cluster2, “topic2”, lambda message_dict1, message_dict2: message_dict1[“value”] != message_dict2[“value”])

    Create a diff of “topic1” on cluster1 and “topic2” on cluster2 by comparing the message values.


### kash.flatmap(source_cluster, source_topic_str, target_cluster, target_topic_str, flatmap_function, group=None, offsets=None, config={}, source_key_type='bytes', source_value_type='bytes', target_key_type=None, target_value_type=None, target_key_schema=None, target_value_schema=None, keep_timestamps=True, n=-1, batch_size=1)
Replicate a topic and transform the messages in a flatmap-like manner.

Replicate (parts of) a topic (source_topic_str) on one cluster (source_cluster) to another topic (target_topic_str) on another (or the same) cluster (target_cluster). Each replicated message is transformed into a list of other messages in a flatmap-like manner. The source and target topics can have different message key and value types; e.g. the source topic can have value type Avro whereas the target topic will be written with value type Protobuf.


* **Parameters**

    
    * **source_cluster** (*Cluster*) – Source cluster


    * **source_topic_str** (*str*) – Source topic


    * **target_cluster** (*Cluster*) – Target cluster


    * **target_topic_str** (*str*) – Target topic


    * **flatmap_function** (*function*) – Flatmap function (takes a message dictionary and returns a list of message dictionaries)


    * **group** (*str**, **optional*) – Consumer group name used for subscribing to the source topic. If set to None, creates a new unique consumer group name. Defaults to None.


    * **offsets** (*dict**, **optional*) – Dictionary of offsets (keys: partitions (int), values: offsets for the partitions (int)) for subscribing to the source topic. If set to None, subscribe to the topic using the offsets from the consumer group for the topic. Defaults to None.


    * **config** (*dict**(**str**, **str**)**, **optional*) – Dictionary of strings (keys) and strings (values) to augment the consumer configuration for the source topic. Defaults to {}.


    * **source_key_type** (*str**, **optional*) – Source topic message key type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “bytes”.


    * **source_value_type** (*str**, **optional*) – Source topic message value type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “bytes”.


    * **target_key_type** (*str**, **optional*) – Target topic message key type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). If set to None, target_key_type = source_key_type. Defaults to None.


    * **target_value_type** (*str**, **optional*) – Target topic message value type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). If set to None, target_value_type = source_value_type. Defaults to None.


    * **target_key_schema** (*str**, **optional*) – Target key message type schema (for “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to None.


    * **target_value_schema** (*str**, **optional*) – Target value message type schema (for “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to None.


    * **keep_timestamps** (*bool**, **optional*) – Replicate the timestamps of the source messages in the target messages. Defaults to True.


    * **n** (*int**, **optional*) – Number of messages to consume from the source topic. Defaults to ALL_MESSAGES = -1.


    * **batch_size** (*int**, **optional*) – Maximum number of messages to consume from the source topic at a time. Defaults to 1.



* **Returns**

    Pair of the number of messages consumed from the source topic and the number of messages produced to the target topic.



* **Return type**

    tuple(int, int)


### Examples

flatmap(cluster1, “topic1”, cluster2, “topic2”, lambda message_dict: [message_dict])

    Replicate “topic1” on cluster1 to “topic2” on cluster2.

flatmap(cluster1, “topic1”, cluster2, “topic2”, lambda message_dict: [message_dict, message_dict])

    Replicate “topic1” on cluster1 to “topic2” on cluster2, while duplicating each message from topic1 in topic2.

flatmap(cluster1, “topic1”, cluster2, “topic2”, lambda message_dict: [message_dict], source_value_type=”avro”, target_value_type=”protobuf”, target_value_schema=”message Snack { required string name = 1; required float calories = 2; optional string colour = 3; }”, n=100)

    Replicate the first 100 messages from “topic1” on cluster1 to “topic2” on cluster2, changing the value schema from avro to protobuf.

flatmap(cluster1, “topic1”, cluster2, “topic2”, lambda message_dict: [message_dict], offsets={0:100}, keep_timestamps=False, n=500)

    Replicate the messages 100-600 from “topic1” on cluster1 to “topic2” on cluster2. Create new timestamps for the messages produced to the target topic.


### kash.map(source_cluster, source_topic_str, target_cluster, target_topic_str, map_function, group=None, offsets=None, config={}, source_key_type='bytes', source_value_type='bytes', target_key_type=None, target_value_type=None, target_key_schema=None, target_value_schema=None, keep_timestamps=True, n=-1, batch_size=1)
Replicate a topic and optionally transform the messages in a map-like manner.

Replicate (parts of) a topic (source_topic_str) on one cluster (source_cluster) to another topic (target_topic_str) on another (or the same) cluster (target_cluster). Each replicated message can be transformed into another messages in a map-like manner. The source and target topics can have different message key and value types; e.g. the source topic can have value type Avro whereas the target topic will be written with value type Protobuf. Stops either if the consume timeout is exceeded on the source cluster (`consume.timeout` in the kash.py cluster configuration) or the number of messages specified in `n` has been consumed.


* **Parameters**

    
    * **source_cluster** (*Cluster*) – Source cluster


    * **source_topic_str** (*str*) – Source topic


    * **target_cluster** (*Cluster*) – Target cluster


    * **target_topic_str** (*str*) – Target topic


    * **map_function** (*function**, **optional*) – Map function (takes a message dictionary and returns a message dictionary). Defaults to lambda x: [x].


    * **group** (*str**, **optional*) – Consumer group name used for subscribing to the source topic. If set to None, creates a new unique consumer group name. Defaults to None.


    * **offsets** (*dict**, **optional*) – Dictionary of offsets (keys: partitions (int), values: offsets for the partitions (int)) for subscribing to the source topic. If set to None, subscribe to the topic using the offsets from the consumer group for the topic. Defaults to None.


    * **config** (*dict**(**str**, **str**)**, **optional*) – Dictionary of strings (keys) and strings (values) to augment the consumer configuration for the source topic. Defaults to {}.


    * **source_key_type** (*str**, **optional*) – Source topic message key type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “bytes”.


    * **source_value_type** (*str**, **optional*) – Source topic message value type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “bytes”.


    * **target_key_type** (*str**, **optional*) – Target topic message key type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). If set to None, target_key_type = source_key_type. Defaults to None.


    * **target_value_type** (*str**, **optional*) – Target topic message value type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). If set to None, target_value_type = source_value_type. Defaults to None.


    * **target_key_schema** (*str**, **optional*) – Target key message type schema (for “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to None.


    * **target_value_schema** (*str**, **optional*) – Target value message type schema (for “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to None.


    * **keep_timestamps** (*bool**, **optional*) – Replicate the timestamps of the source messages in the target messages. Defaults to True.


    * **n** (*int**, **optional*) – Number of messages to consume from the source topic. Defaults to ALL_MESSAGES = -1.


    * **batch_size** (*int**, **optional*) – Maximum number of messages to consume from the source topic at a time. Defaults to 1.



* **Returns**

    Pair of the number of messages consumed from the source topic and the number of messages produced to the target topic.



* **Return type**

    tuple(int, int)


### Examples

map(cluster1, “topic1”, cluster2, “topic2”, lambda x: x)

    Replicate “topic1” on cluster1 to “topic2” on cluster2.


### kash.zip_foldl(cluster1, topic_str1, cluster2, topic_str2, zip_foldl_function, initial_acc, group1=None, group2=None, offsets1=None, offsets2=None, config1={}, config2={}, key_type1='bytes', value_type1='bytes', key_type2='bytes', value_type2='bytes', n=-1, batch_size=1)
Subscribe to and consume from topic 1 on cluster 1 and topic 2 on cluster 2 and combine the messages using a foldl function.

Consume (parts of) a topic (topic_str1) on one cluster (cluster1) and another topic (topic_str2) on another (or the same) cluster (cluster2) and combine them using a foldl function. Stops on either topic/cluster if either the consume timeout is exceeded (`consume.timeout` in the kash.py cluster configuration) or the number of messages specified in `n` has been consumed.


* **Parameters**

    
    * **cluster1** (*Cluster*) – Cluster 1


    * **topic_str1** (*str*) – Topic 1


    * **cluster2** (*Cluster*) – Cluster 2


    * **topic_str2** (*str*) – Topic 2


    * **zip_foldl_function** (*function*) – Foldl function (takes an accumulator (any type) and a message dictionary and returns the updated accumulator).


    * **initial_acc** – Initial value of the accumulator (any type).


    * **group1** (*str**, **optional*) – Consumer group name used for consuming from topic 1. If set to None, creates a new unique consumer group name. Defaults to None.


    * **group2** (*str**, **optional*) – Consumer group name used for consuming from topic 2. If set to None, creates a new unique consumer group name. Defaults to None.


    * **offsets1** (*dict**, **optional*) – Dictionary of offsets (keys: partitions (int), values: offsets for the partitions (int)) for consuming from topic 1. If set to None, consume topic 1 using the offsets from the consumer group for topic 1. Defaults to None.


    * **offsets2** (*dict**, **optional*) – Dictionary of offsets (keys: partitions (int), values: offsets for the partitions (int)) for consuming from topic 2. If set to None, consume topic 2 using the offsets from the consumer group for topic 2. Defaults to None.


    * **config1** (*dict**(**str**, **str**)**, **optional*) – Dictionary of strings (keys) and strings (values) to augment the consumer configuration for topic 1 on cluster 1. Defaults to {}.


    * **config2** (*dict**(**str**, **str**)**, **optional*) – Dictionary of strings (keys) and strings (values) to augment the consumer configuration for topic 2 on cluster 2. Defaults to {}.


    * **key_type1** (*str**, **optional*) – Topic 1 message key type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “bytes”.


    * **value_type1** (*str**, **optional*) – Topic 1 message value type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). Defaults to “bytes”.


    * **key_type2** (*str**, **optional*) – Topic 2 message key type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). If set to None, target_key_type = source_key_type. Defaults to None.


    * **value_type2** (*str**, **optional*) – Topic 2 message value type (“bytes”, “str”, “json”, “avro”, “protobuf”/”pb” or “jsonschema”). If set to None, target_value_type = source_value_type. Defaults to None.


    * **n** (*int**, **optional*) – Number of messages to consume from topic 1 and topic 2. Defaults to ALL_MESSAGES = -1.


    * **batch_size** (*int**, **optional*) – Maximum number of messages to consume from topic 1 and topic 2 at a time. Defaults to 1.



* **Returns**

    Tuple of the accumulator (any type), the number of messages consumed from topic 1 and the number of messages consumed from topic 2.



* **Return type**

    tuple(acc, int, int)


### Examples

zip_foldl(cluster1, “topic1”, cluster2, “topic2”, lambda acc, message_dict1, message_dict2: acc + [(message_dict1, message_dict2)], [])

    Consume “topic1” on cluster1 and “topic2” on cluster2 and return a list of pairs of message dictionaries from topic 1 and topic 2, respectively.
