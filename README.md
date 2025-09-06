realtime-data-pipeline/
│── README.md
│── docker-compose.yml
│── requirements.txt
│── producer/
│   └── kafka_producer.py
│── consumer/
│   └── spark_streaming.py
│── data/
│   └── sample_data.json

# Real-Time Data Pipeline with Kafka & Spark  

🚀 This project demonstrates a real-time data pipeline using **Kafka** as a message broker and **Apache Spark Structured Streaming** for processing.  

## ⚙️ Architecture
1. A **Kafka Producer** simulates streaming data (JSON events).  
2. Kafka acts as a **message broker**.  
3. **Spark Streaming** consumes the data, transforms it, and prints it (can also write to BigQuery, PostgreSQL, etc.).  

## 🛠️ Tech Stack
- Python
- Apache Kafka
- Apache Spark
- Docker Compose  

## ▶️ How to Run
1. Start Kafka & Zookeeper:
   ```bash
   docker-compose up -d

   python producer/kafka_producer.py
   spark-submit consumer/spark_streaming.py
   +----------+------------+
| username | action     |
+----------+------------+
| user123  | click      |
| user456  | purchase   |

+----------+------------+
| username | action     |
+----------+------------+
| user123  | click      |
| user456  | purchase   |



---

### **docker-compose.yml**
```yaml
version: '3'
services:
  zookeeper:
    image: wurstmeister/zookeeper:3.4.6
    ports:
      - "2181:2181"

  kafka:
    image: wurstmeister/kafka:2.12-2.3.0
    ports:
      - "9092:9092"
    environment:
      KAFKA_ADVERTISED_HOST_NAME: localhost
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
kafka-python
pyspark
from kafka import KafkaProducer
import json
import time
import random

producer = KafkaProducer(
    bootstrap_servers='localhost:9092',
    value_serializer=lambda v: json.dumps(v).encode('utf-8')
)

users = ["user123", "user456", "user789"]
actions = ["click", "view", "purchase"]

while True:
    event = {
        "username": random.choice(users),
        "action": random.choice(actions)
    }
    producer.send("events", event)
    print(f"Produced: {event}")
    time.sleep(2)
from pyspark.sql import SparkSession
from pyspark.sql.functions import from_json, col
from pyspark.sql.types import StructType, StringType

spark = SparkSession.builder \
    .appName("KafkaSparkStreaming") \
    .getOrCreate()

spark.sparkContext.setLogLevel("WARN")

schema = StructType() \
    .add("username", StringType()) \
    .add("action", StringType())

df = spark.readStream \
    .format("kafka") \
    .option("kafka.bootstrap.servers", "localhost:9092") \
    .option("subscribe", "events") \
    .load()

json_df = df.selectExpr("CAST(value AS STRING) as json") \
    .select(from_json(col("json"), schema).alias("data")) \
    .select("data.*")

query = json_df.writeStream \
    .outputMode("append") \
    .format("console") \
    .start()

query.awaitTermination()

