# vagrant-ansible-mesos

Mesos cluster. Tested with 2 nodes on VirtualBox.

## Run

	curl http://apache.cs.uu.nl/spark/spark-1.6.1/spark-1.6.1-bin-hadoop2.6.tgz > spark-1.6.1-bin-hadoop2.6.tgz
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

### Test Spark in client mode

From within the cluster:

	spark-submit \ 
	--master mesos://192.168.9.11:5050 \ 
	--deploy-mode client \ 
	--total-executor-cores 1 \ 
	--executor-memory 500M \ 
	--supervise \ 
	$SPARK_HOME/examples/src/main/python/pi.py 10

or

	spark-submit \ 
	--master mesos://zk://192.168.9.11:2181/mesos \ 
	--deploy-mode client \ 
	--total-executor-cores 1 \ 
	--executor-memory 500M \ 
	--supervise \ 
	$SPARK_HOME/examples/src/main/python/pi.py 10

## Mesos UI

	http://192.168.9.11:5050

## Marathon UI

	http://192.168.9.11:8080

## Spark UI (client mode)

	http://192.168.9.11:4040

## Troubleshooting

- Run playbook manually

		ansible-playbook --private-key=~/.vagrant.d/insecure_private_key -u vagrant -i staging site.yml

- "Failed to connect to the host via ssh."
You might get this error after running `vagrant destroy && vagrant up` because the ECDSA host key for 192.168.9.11 will have changed. The suggested sollution is: `ssh-keygen -f ~/.ssh/known_hosts -R 192.168.9.11`. Try to manually connect via ssh, to see if this is the case.

	ssh -i ~/.vagrant.d/insecure_private_key vagrant@192.168.9.11 -p 22

- Gathering facts

	ansible --private-key=~/.vagrant.d/insecure_private_key -u vagrant -i staging -m setup all

## Notes

- The documentation of spark 1.6.1 is inconsistent when it comes to cluster mode support on mesos. In spark 2.0.0 support seems to have been improved.

## More information

- https://open.mesosphere.com/getting-started/install/
- https://www.digitalocean.com/community/tutorials/how-to-install-apache-kafka-on-ubuntu-14-04
- http://spark.apache.org/docs/1.6.1/running-on-mesos.html
- https://spark.apache.org/docs/1.6.1/submitting-applications.html


