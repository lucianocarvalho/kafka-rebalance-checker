# Kafka Rebalance Checker

Visualize what Apache Kafka will do rebalancing your topics.

- How the partitions will be distributed among the brokers
- What changes will be made to the partitions
- Number of topics per broker

### Getting rebalance strategy from Apache Kafka

You can run these commands inside any broker.

```bash
# Export zookeepers hosts
export ZK_HOSTS='zookeeper:2181'

# Create topics-to-move.json
echo '{"topics": [' > topics-to-move.json && ./kafka-topics.sh --zookeeper $ZK_HOSTS --list | grep -v __consumer_offsets | awk '{print "{\"topic\":\""$1"\"},"}' >> topics-to-move.json && truncate --size=-2 topics-to-move.json && echo '],"version":1}' >> topics-to-move.json

# Generate the strategy to rebalance all topics between 1-3 brokers.
./kafka-reassign-partitions.sh --zookeeper $ZK_HOSTS --broker-list "1,2,3" --topics-to-move-json-file topics-to-move.json --generate > full-reassignment-file.json
```

You will need the file `full-reassignment-file.json` below.

### Rebalance checker

```bash
# Clone the repository
git clone git@github.com:lucianocarvalho/kafka-rebalance-checker.git

# Permissions
cd kafka-rebalance-checker && chmod +x src/checker

# Run!
./checker full-reassignment-file.json
```

Have a good analysis!

## Author

Luciano Carvalho ([@lucianocarvalho](https://github.com/lucianocarvalho))
