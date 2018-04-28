# REF: https://mosquitto.org

####################
# First time setup #
####################
# Create bind mounted directies
# Mosquitto
mkdir -p /opt/mqtt/config
mkdir -p /opt/mqtt/config/conf.d
mkdir -p /opt/mqtt/config/certs
mkdir -p /opt/mqtt/data
mkdir -p /opt/mqtt/log
cp mosquitto.conf /opt/mqtt/config/mosquitto.conf
cp TCP_1883_Unencrypted_MQTT.conf /opt/mqtt/config/conf.d/TCP_1883_Unencrypted_MQTT.conf
cp TCP_8883_Encrypted_MQTT.conf /opt/mqtt/config/conf.d/TCP_8883_Encrypted_MQTT.conf
cp TCP_9001_Unencrypted_Websockets.conf /opt/mqtt/config/conf.d/TCP_9001_Unencrypted_Websockets.conf
cp TCP_9883_Encrypted_Websockets.conf /opt/mqtt/config/conf.d/TCP_9883_Encrypted_Websockets.conf
cp ca.crt /opt/mqtt/config/certs/ca.crt
cp ca.key /opt/mqtt/config/certs/ca.key
cp ca.srl /opt/mqtt/config/certs/ca.srl
cp nodered.crt /opt/mqtt/config/certs/nodered.crt
cp nodered.key /opt/mqtt/config/certs/nodered.key
cp nodered.csr /opt/mqtt/config/certs/nodered.csr
cp generate-CA.sh /opt/mqtt/config/certs/generate-CA.sh
cp passwd /opt/mqtt/config/passwd
cp aclfile /opt/mqtt/config/aclfile
chmod +x /opt/mqtt/config/certs/generate-CA.sh
chmod a+rw /opt/mqtt
chown -R 100:101 /opt/mqtt


##########
# Deploy #
##########
# Deploy the stack into a Docker Swarm
docker stack deploy -c docker-compose.yml mosquitto
# docker stack rm mosquitto

# Verify
docker service ls | grep mosquitto
docker service logs -f mosquitto_mqtt


###################
# MQTT Basic Test #
###################
# Install MQTT client
# REF: https://mosquitto.org/download/

# CentOS 7x
vi /etc/yum.repos.d/mqtt.repo
[home_oojah_mqtt]
name=mqtt (CentOS_CentOS-7)
type=rpm-md
baseurl=http://download.opensuse.org/repositories/home:/oojah:/mqtt/CentOS_CentOS-7/
gpgcheck=1
gpgkey=http://download.opensuse.org/repositories/home:/oojah:/mqtt/CentOS_CentOS-7/repodata/repomd.xml.key
enabled=1
:wq!
yum -y install mosquitto-clients

# Ubuntu
apt-get install mosquitto-clients

# Pub / Sub via command line (MQTT protocol)
mosquitto_sub -v -h 10.x.y.z -p 1883 -t "Chan19"
mosquitto_pub -d -h 10.x.y.z -p 1883 -t "Chan19" -m "Hello MQTT from command line"

# Pub / Sub via browser (Websocket protocol)
# REF: http://mitsuruog.github.io/what-mqtt/
ws://IP:9001/mqtt
Chan19
Hello from Websocket!

