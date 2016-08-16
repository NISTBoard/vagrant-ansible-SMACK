# vagrant-ansible-mesos

Mesos cluster. Tested with 2 nodes on VirtualBox.

## Run

	vagrant up

## Test

### Test Zookeeper

From the host:

	echo "ruok" | nc 192.168.9.11 2181

### Test Mesos
	
	MASTER=$(mesos-resolve `cat /etc/mesos/zk`)
	mesos-execute --master=$MASTER --name="cluster-test" --command="sleep 5"

### Test Kafka

	echo "Hello, World" | ~/kafka/bin/kafka-console-producer.sh --broker-list localhost:9092 --topic MyTopic > /dev/null
	~/kafka/bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic MyTopic --from-beginning

## Troubleshooting

- Run playbook manually

		ansible-playbook --private-key=~/.vagrant.d/insecure_private_key -u vagrant -i staging site.yml

- "Failed to connect to the host via ssh."
You might get this error after running `vagrant destroy && vagrant up` because the ECDSA host key for 192.168.9.11 will have changed. The suggested sollution is: `ssh-keygen -f ~/.ssh/known_hosts -R 192.168.9.11`. Try to manually connect via ssh, to see if this is the case.

	ssh -i ~/.vagrant.d/insecure_private_key vagrant@192.168.9.11 -p 22

- Gathering facts

	ansible --private-key=~/.vagrant.d/insecure_private_key -u vagrant -i staging -m setup all

## More information

- https://open.mesosphere.com/getting-started/install/
- https://www.digitalocean.com/community/tutorials/how-to-install-apache-kafka-on-ubuntu-14-04

