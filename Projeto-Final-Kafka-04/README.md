# Projeto Kafka-04

# Kafka: Idempotencia e garantias

# Verifica os topicos dos brockers Kafka

bin/kafka-topics.sh --bootstrap-server localhost:9091 --describe
bin/kafka-topics.sh --bootstrap-server localhost:9092 --describe
bin/kafka-topics.sh --bootstrap-server localhost:9093 --describe
bin/kafka-topics.sh --bootstrap-server localhost:9094 --describe

# Inserir dados no DataBase

http://localhost:8080/new?email=efcunha01@email.com&amount=1526
http://localhost:8080/new?email=efcunha02@email.com&amount=3625
http://localhost:8080/new?email=efcunha03@email.com&amount=5153

http://localhost:8080/new?email=efcunha07@email.com&amount=1526&uuid=9874
http://localhost:8080/new?email=efcunha07@email.com&amount=3625&uuid=9874
http://localhost:8080/new?email=efcunha07@email.com&amount=5153&uuid=9874

# Gerar informação na aplicação

http://localhost:8080/admin/generate-reports
http://localhost:8080/new?email=efcunha@email.com&amount=500

# Verifica o consumer group
bin/kafka-consumer-groups.sh --all-groups --bootstrap-server localhost:9091 --describe