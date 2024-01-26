# SOME/IP

## 创建环境

### create subnet
```shell
$ docker network create --subnet=192.168.120.0/24 testbridge
$ docker run --network testbridge --ip 192.168.120.24 --name someip1 -v $PWD:/repo -it ubuntu
$ docker run --network testbridge --ip 192.168.120.25 --name someip2 -v $PWD:/repo -it ubuntu
```

### build two container
```shell
$ apt update
$ apt install -y git g++ make cmake libboost-all-dev
$ git clone git@cc-github.bmwgroup.net:networking/vsomeip-lib.git

$ cmake -B build -DCMAKE_INSTALL_PREFIX=../install_folder -DENABLE_SIGNAL_HANDLING=1 -S .
$ cmake --build build --parallel 32
$ cd build && make install
$ ldconfig
```

### start two service, and use wireshark capture
### for service
```json
{
  "unicast" : "192.168.120.24",
  "logging" :
  {
    "level" : "debug",
    "console" : "true",
    "file" : { "enable" : "false", "path" : "/tmp/vsomeip.log" },
    "dlt" : "false"
  },
  "applications" :
  [
    {
      "name" : "World",
      "id" : "0x1212"
    }
  ],
  "services" :
  [
    {
      "service" : "0x1234",
      "instance" : "0x5678",
      "unreliable" : "30509"
    }
  ],
  "routing" : "World",
  "service-discovery" :
  {
    "enable" : "true",
    "multicast" : "224.224.224.245",
    "port" : "30490",
    "protocol" : "udp",
    "initial_delay_min" : "10",
    "initial_delay_max" : "100",
    "repetitions_base_delay" : "200",
    "repetitions_max" : "3",
    "ttl" : "3",
    "cyclic_offer_delay" : "2000",
    "request_response_delay" : "1500"
  }
}
```

#### for client
```json
{
    "unicast": "192.168.120.25",
    "logging": {
        "level": "debug",
        "console": "true",
        "file": {
            "enable": "false",
            "path": "/var/log/vsomeip.log"
        },
        "dlt": "false"
    },
    "applications": [
        {
            "name": "Hello",
            "id": "0x1313"
        }
    ],
    "routing": "Hello",
    "service-discovery": {
        "enable": "true",
        "multicast": "224.224.224.245",
        "port": "30490",
        "protocol": "udp",
        "initial_delay_min": "10",
        "initial_delay_max": "100",
        "repetitions_base_delay": "200",
        "repetitions_max": "3",
        "ttl": "3",
        "cyclic_offer_delay": "2000",
        "request_response_delay": "1500"
    }
}
```