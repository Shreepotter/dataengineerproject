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
