FROM debian:jessie

ENV DEBIAN_FRONTEND noninteractive

RUN echo "deb http://ppa.launchpad.net/webupd8team/java/ubuntu trusty main" | tee /etc/apt/sources.list.d/webupd8team-java.list
RUN echo "deb-src http://ppa.launchpad.net/webupd8team/java/ubuntu trusty main" | tee -a /etc/apt/sources.list.d/webupd8team-java.list
RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys EEA14886
RUN apt-get update
RUN echo debconf shared/accepted-oracle-license-v1-1 select true | debconf-set-selections
RUN apt-get install -y oracle-java8-installer

RUN wget -O - http://packages.elasticsearch.org/GPG-KEY-elasticsearch | apt-key add -
RUN echo 'deb http://packages.elasticsearch.org/elasticsearch/1.4/debian stable main' | tee /etc/apt/sources.list.d/elasticsearch.list
RUN apt-get update && apt-get -y install elasticsearch=1.4.4
RUN sed -i -e "s/#network.host: 192.168.0.1/network.host: localhost/g" /etc/elasticsearch/elasticsearch.yml

ADD https://download.elasticsearch.org/kibana/kibana/kibana-4.0.1-linux-x64.tar.gz /tmp/kibana.tar.gz
RUN tar xvf /tmp/kibana.tar.gz -C /opt
RUN mv /opt/kibana-4.0.1-linux-x64 /opt/kibana
ADD https://gist.githubusercontent.com/thisismitch/8b15ac909aed214ad04a/raw/bce61d85643c2dcdfbc2728c55a41dab444dca20/kibana4 /etc/init.d/kibana4
RUN chmod +x /etc/init.d/kibana4

RUN echo 'deb http://packages.elasticsearch.org/logstash/1.5/debian stable main' | tee /etc/apt/sources.list.d/logstash.list
RUN apt-get update && apt-get install -y logstash

RUN mkdir -p /etc/pki/tls/certs && mkdir /etc/pki/tls/private

EXPOSE 5601 9200 5000

VOLUME  ["/etc/logstash/conf.d","/etc/pki/tls/certs"]

COPY entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]

CMD service elasticsearch start && service kibana4 start && service logstash start && tail -F /var/log/nginx/error.log
