# PulsBlitz
Single-Sender, Multi-Receiver, Non-Mesh Realtime Radio Protocol

The name is a reference to the German term "Potzblitz"

## Motivation
I wanted to have a open protocol that suits realtime-control data really well but also has some features some other protocols do not support:
* Be agnostic of the underlying radio hardware or libraries
* Allow multi-receiver operation (RC-control protocols for model cars, boats, planes, multicopter) are usually single-RX only

## Features
* Single-Transmitter protocol. One node in the network is the primary transmitter, controlling the network and sending/broadcasting most of the data
* Controller sends all data as broadcasts, the receivers need to decide if the data in question is relevant for them or not
* Nodes have a pseudo-unique, 16 byte long ID. It's UUID-style but can be true random, derived from the unique SPI-flash identifier or configured by the user
* There is a discovery mechanism in place so that the controller can query associated receivers
* Channel Hopping is planned but not yet implemented. If channel hopping, Master sends rendevouz-packet on rendevouz-channel, containing hopping pattern and offset
* Packets can be fragmented so we support low-payload-size lower-levels (looking at you, nRF24 ...), max 32 fragments per packet (5 bit plus 1 bit "last fragment" plus 2 bit packet counter)
* Packets are NOT acknowledged, do expect packet loss. It's a realtime control-value protocol, not for reliable data transfer. It's more UDP than TCP
* Each transmitter can send up to 64 different payload channels. One channel can contain things like one DMX universe, one opus audio stream, one set of flight-control-values, one location information, one file-transfer-session, ...
* The transmitter sends "payload channel description" packets regularly, describing data type, name, status of one payload channel
* The protocol thinks in time-slots. Their exact length depends on the underlying radio hardware and configuration (payload length, data rate, ...)
* The transmitter can announce that the up-coming time slot is to be used by a certain, announced receiver. This way, a small back-channels exists for values such as battery level, packet loss rate, sensor values, ... However, this does not make it a multi-directional or multi-sender realtime protocol
* Encryption is optional and can be configured per payload channel
* Snappy compression is required to be implemented on all nodes. The size savings might not be huge but calculation complexity and increase in latency is usually so low that it's worth it. However, the usage of compression can vary from packet to packet. The transmitter decides if compression makes sense for that packet or not
