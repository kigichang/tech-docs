# Cassandra Introduction On Ubuntu

## Install Ubuntu
*	先都用預設
*	裝 OpenSSH Server

## Install JDK

*	sudo apt-get install openjdk-7-jdk

ps. (ProcRun 用在 Windows, 不裝)

## Install JNA (Java Native Access)
*	https://github.com/twall/jna
*	sudo apt-get install libjna-java

## Install Cassandra
*	到 http://cassandra.apache.org/download/
*	下載最新版本
	*	wget http://apache.cdpa.nsysu.edu.tw/cassandra/2.0.6/apache-cassandra-2.0.6-bin.tar.gz
*	解開剛剛下載的壓縮檔
	*	tar zxvf apache-cassandra-2.0.6-bin.tar.gz

*	修改設定檔 conf/cassandra.yaml
	*	修改 cluster_name, ex: kigi-cassandra-1
	*	修改 seeds 建議是兩台以上(因為 cassandra 是 P2P，需要 seed 去查所有的 nodes)，只有一台，改成機器用的 IP
	*	修改 listen_address, 改成機器用的 IP，或者內網的 IP, 不要用 Localhost or 127.0.0.1
	*	修改 rpc_address, 同上
		(p.s. seeds, listen_address, rpc_address 最好都一樣，要不然會有問題)
	*	修改 endpoint_snitch
	
		```
			endpoint_snitch: GossipingPropertyFileSnitch
		```
		
	*	建目錄
		*	sudo mkdir -p /var/log/cassandra
		*	sudo chown -R `whoami` /var/log/cassandra
		*	sudo mkdir -p /var/lib/cassandra
		*	sudo chown -R `whoami` /var/lib/cassandra

## Run Cassandra
*	bin/cassandra -f (前景作業) or bin/cassandra


## Test
*	bin/cqlsh ip (因為改了 listen_address，所以預設的localhost會連不上，改連 IP)

## Shutdown Cassandra
```
bin/nodetool -h ip disablegossip
bin/nodetool -h ip disablethrift
bin/nodetool -h ip drain
kill cassandra-process-id or /etc/init.d/cassandra stop if service installed
```

## Dump Data
```copy table_name to 'file_name';```


## Import Data
```copy table_name from 'file_name';```


