# Kafka quick start

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - 
sudo add-apt-repository    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
sudo apt update

sudo apt install -y docker-ce=18.06.1~ce~3-0~ubuntu

sudo usermod -aG docker edge

apt install docker-compose 

# docker-machine trouble-shootings:

Getting this:

```
docker-machine regenerate-certs duyhardkafka
Regenerate TLS machine certs?  Warning: this is irreversible. (y/n): y
Regenerating TLS certificates
Waiting for SSH to be available...
Detecting the provisioner...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...

This machine has been allocated an IP address, but Docker Machine could not
reach it successfully.
```
Do this:

` sudo ifconfig vboxnet1 down && sudo ifconfig vboxnet1 up`

Create docker-machine:

`docker-machine create --driver virtualbox duyhardkafka`

Set a machine as active:

`eval "$(docker-machine env duyhardkafka)"`

`docker exec -it b6c642b48db4 /opt/kafka_2.11-0.10.1.0/bin/kafka-console-producer.sh --broker-list $KAFKA --topic test`

`docker exec -it b6c642b48db4 /opt/kafka_2.11-0.10.1.0/bin/kafka-console-consumer.sh --zookeeper $ZOOKEEPER --topic test`
