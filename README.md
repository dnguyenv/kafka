# Kafka quick start

To play around with Kafka, you can either install all components in your native OS, or use the containerized options as following:

## 1. Use docker-machine to host all-in-one-node kafka cluster

This Docker image has everything necessary to run a kafka cluster, including zookeeper: https://hub.docker.com/r/spotify/kafka/

Flow these steps to set it up on your laptop. The example uses Mac OS. 

### Create a machine, I'm using VirtualBox driver:

`$ docker-machine create --driver virtualbox <machinename>`

For example:

`$ docker-machine create --driver virtualbox kafkatest`

### Activate the machine - Connect your Docker client to Docker daemon in side the VM

You may have several `machines` (VMs) created. This is to set one of them as active node which then will be used to deploy the kafka cluster

`$ eval "$(docker-machine env machinename)"` 

Example:

`$ eval "$(docker-machine env kafkatest)"` 

Notes: From now on, I'll use `kafkatest` as the machine name in the commands below

After this command, your Docker client will be interacting with the daemon running inside the `machinename` VM.

### Run the kafka cluster

Now you can run the kafka cluster in the VM:

```bash
$ docker run --name kafkatest -p 2181:2181 -p 9092:9092 --env ADVERTISED_HOST=$(docker-machine ip $(docker-machine active)) --env ADVERTISED_PORT=9092 spotify/kafka
```

### Test it:

Open a new terminal (1) window and run these commands to create a topic and a stream from the termal to the cluster. 

Connect Docker from your local with the deamon running inside the `kafkatest` machine:

```bash
$ eval "$(docker-machine env kafkatest)"
```

Double check to see if the container is running:

```bash
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS              PORTS                                            NAMES
6654ae622c78        spotify/kafka       "supervisord -n"    About a minute ago   Up About a minute   0.0.0.0:2181->2181/tcp, 0.0.0.0:9092->9092/tcp   kafkatest

Create the topic

$ export KAFKA=$(docker-machine ip $(docker-machine active)):9092
$ docker exec -it kafkatest /opt/kafka_2.11-0.10.1.0/bin/kafka-console-producer.sh --broker-list $KAFKA --topic kafkatest
```

Open another terminal (2) window and run this to subscribe to the topic

```bash
$ eval "$(docker-machine env kafkatest)"
$ export ZOOKEEPER=$(docker-machine ip $(docker-machine active)):2181
$ docker exec -it kafkatest /opt/kafka_2.11-0.10.1.0/bin/kafka-console-consumer.sh --zookeeper $ZOOKEEPER --topic kafkatest
```

Now if you type something on terminal(1), you will see the content streamed to terminal(2)

### docker-machine trouble-shootings:

If you see something like this when you can not connect to your VM from docker client:

```
docker-machine regenerate-certs machinename
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
Try to restart the network interface that the VM is attached to, for example, if the interface is `vboxnet1`

`$ sudo ifconfig vboxnet1 down && sudo ifconfig vboxnet1 up`


## 2. Install Kafka from scratch using docker-compose

Within your Ubuntu: 

## Install Docker and docker-compose

```
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - 
sudo add-apt-repository    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
$ sudo apt update

$ sudo apt install -y docker-ce=18.06.1~ce~3-0~ubuntu

$ sudo usermod -aG docker edge

$ curl -L https://github.com/docker/compose/releases/download/1.24.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose

$ chmod +x /usr/local/bin/docker-compose
```

### Start the cluster up

```
$ git clone https://github.com/dnguyenv/kafka.git
$ cd kafka
$ docker-compose up -d --build
```

### Install Java

```
$ sudo apt install -y default-jdk
```

### Getting Kafka binaries

```
$ wget http://mirror.cogentco.com/pub/apache/kafka/2.2.0/kafka_2.12-2.2.0.tgz
$ tar -xvf kafka_2.12-2.2.0.tgz && cd kafka_2.12-2.2.0

```

### Create your first topic:

```
$ ./bin/kafka-topics.sh --zookeeper localhost:2181 --create --topic duyhardtest --partitions 3 --replication-factor 1
```

### Describe the topic:

```
$ ./bin/kafka-topics.sh --zookeeper localhost:2181 --topic duyhardtest --describe
```

