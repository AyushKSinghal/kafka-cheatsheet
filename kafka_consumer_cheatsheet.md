
# Kafka Consumer Cheatsheet

## List all the consumer group
```bash
kafka-consumer-groups --list --bootstrap-server <kafkahost:port> 
```

## Search/Find for consumer group you are looking for
```bash
kafka-consumer-groups --list --bootstrap-server <kafkahost:port> | grep supply_lodging_availability_eg_v
```

## List the topics and offsets to which the group is subscribed
```bash
kafka-consumer-groups --bootstrap-server <kafkahost:port> --group <group_id> --describe
```
Note the values under "CURRENT-OFFSET" and "LOG-END-OFFSET". "CURRENT-OFFSET" is the offset where this consumer group is currently at in each of the partitions.

## Reset the consumer offset for a topic (preview)
Kafka 0.11.0.0 (Confluent 3.3.0) added support to manipulate offsets for a consumer group via cli `kafka-consumer-groups` command.
```bash
kafka-consumer-groups --bootstrap-server <kafkahost:port> --group <group_id> --topic <topic_name> --reset-offsets --to-earliest
```
This will print the expected result of the reset, but not actually run it.

## Reset the consumer offset for a topic (execute)
```bash
kafka-consumer-groups --bootstrap-server <kafkahost:port> --group <group_id> --topic <topic_name> --reset-offsets --to-earliest --execute
```
This will execute the reset and reset the consumer group offset for the specified topic back to 0.

## List the topics and offsets to check if the reset is successful

### Note
* The consumer group should not have a running instance when performing the reset. Otherwise the reset will be rejected.
* Other resetting options, run `kafka-consumer-groups` for details
  * --shift-by <positive_or_negative_integer>
  * --to-current
  * --to-latest
  * --to-offset <offset_integer>
  * --to-datetime <datetime_string>
  * --by-duration <duration_string>

Examples:
```bash
kafka-consumer-groups --bootstrap-server <kafkahost:port> --group <group_id> --topic <topic_name> --reset-offsets --shift-by -1000000 --execute
```
```bash
kafka-consumer-groups --bootstrap-server <kafkahost:port> --group <group_id> --topic <topic_name> --reset-offsets --to-latest --execute
```
```bash
kafka-consumer-groups --bootstrap-server <kafkahost:port> --group <group_id> --topic <topic_name> --reset-offsets --to-datetime 2019-11-11T00:00:00.000
```
DATETIME format is yyyy-MM-ddTHH:mm:ss.xxx
```bash
kafka-consumer-groups --bootstrap-server <kafkahost:port> --group <group_id> --topic <topic_name> --reset-offsets --by-duration PT0H30M0S
```
Duration format is PnDTnHnMnS
P2D => PT48H
P-2DT-20S => PT-48H-20S
P4DT12H20M20.3S => PT108H20M20.3S
P-4DT-12H-20M-20.3S => PT-108H-20M-20.3S
-P4DT12H20M20.3S => PT-108H-20M-20.3S

## Reset offsets for all topics the consumer group subscribes 
Use: --all-topics
```bash
kafka-consumer-groups --bootstrap-server <kafkahost:port> --group <group_id> --reset-offsets --all-topics --shift-by -1000000 --execute
```

References: https://kafka.apache.org/23/documentation/streams/developer-guide/app-reset-tool.html
