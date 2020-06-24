---
layout: post
title: Elasticsearch-ec2-nodes
date: 2020-06-24 00:00:00 +0300
description: Elasticsearch-ec2-nodes
tags: [Elasticsearch, ec2, ubuntu]
img: elasticsearch.png
---

* EC2에서 복수개의 인스턴스를 생성시켜 복수개의 노드로 하나의 클러스터 구성하기
  1. EC2 인스턴스 생성(네트워크 설정)
  2. ES-elasticsearch 설치(자바 설치 후)
  3. 노드, 클러스터 설정

### EC2 인스턴스 실행 - 하나만 생성한 후 이후에 복제
* 인바운드 규칙에 9200 - 9300 포트, 사용자 위치 무관으로 설정

* [참고][참고], [참고1][참고1] - 설치 참고
###  EC2 서버에 Putty, xshell 등으로 접속 후 자바 설치
{% highlight ruby %}
sudo apt update
sudo apt -y upgrade
sudo apt install default-jdk   
{% endhighlight %}

### ES 설치
* Import the Elasticsearch PGP Key
{% highlight ruby %}
sudo apt -y install gnupg
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
{% endhighlight %}

* Add APT repository
{% highlight ruby %}
sudo apt -y install apt-transport-https
echo "deb https://artifacts.elastic.co/packages/oss-7.x/apt stable main" | sudo tee  /etc/apt/sources.list.d/elastic-7.x.list
{% endhighlight %}

* Install Elasticsearch on Ubuntu
{% highlight ruby %}
sudo apt update - 반드시 해야함
sudo apt -y install elasticsearch-oss
{% endhighlight %}

* JVM Heap 사이즈 변경(두개의 값 동일하게 하는 걸 추천)
{% highlight ruby %}
vim /etc/elasticsearch/jvm.options
-Xms512m
-Xmx512m
{% endhighlight %}

* elasticsearch.yml 편집 - 마스터
{% highlight ruby %}
cluster.name: my-app   (다른 노드에서도 동일해야함)

node.name: node-1
node.master: true
node.data: true
node.ingest: true

path.data, path.logs default 값 유지하되 변경이 필요하면 수정하기

network.host: 0.0.0.0
http:port: 9200

discovery.seed_hosts: ["master_ip", "data_node_ip"]      ## 노드가 많아지면 추가되는 곳
cluster.initial_master_nodes: ["node-1"]   - 위의 node.name 과 동일해야함, 마스터에 데이터 노드 추가할 필요 없음.

{% endhighlight %}

* 데이터 노드용 ec2 인스턴스 생성
  1. AMI 생성하여 복사하면 위의 과정을 거치기 않고 복제본 EC2가 생성된다.(대상 인스턴스 연결이 끊기니 재연결 필요)
  2. 단 하나, 마스터 노드에 설정된 노드 ID를 변경해주어야하는데 이 노드는 path.data 에 저장되어 있으므로 해당 경로에 가서 nodes 지워야함

* elasticsearch.yml 편집 - 데이터 노드
{% highlight ruby %}
다른 건 동일 아래만 변경할 것
node.name: node-2
node.master: false
cluster.initial_master_nodes = ["node-1", "node-2"]   # 마스터노드, 데이터노드 이름 동일하게 ## 노드가 많아지면 추가되는 곳
{% endhighlight %}

* es 서비스 등록 및 마스터 노드, 데이터 노드 순서대로 각각 실행 후 상태 확인
{% highlight ruby %}
sudo systemctl enable elasticsearch.service && sudo systemctl restart elasticsearch.service
systemctl status elasticsearch.service 
{% endhighlight %}

[참고]: https://computingforgeeks.com/install-elasticsearch-on-ubuntu/
[참고1]: https://www.bmc.com/blogs/how-to-setup-elasticsearch-cluster-amazon-ec2/