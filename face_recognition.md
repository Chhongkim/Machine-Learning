# How to fix Router-on-a-strick

<img width="1201" height="750" alt="image" src="https://github.com/user-attachments/assets/e87b6f32-8c4a-4797-8997-5778b18400ca" />

## 1. Configure Router-on-a-Stick
### R1

```
Router>enable
Router#configure terminal

Router(config)#interface g0/0/0
Router(config-if)#no shutdown
Router(config-if)#end

Router(config)#interface g0/0/0.10
Router(config-subif)#encapsulation dot1Q 10
Router(config-subif)#ip address 192.168.10.99 255.255.255.0

Router(config)#interface g0/0/0.20
Router(config-subif)#encapsulation dot1Q 20
Router(config-subif)#ip address 192.168.20.99 255.255.255.0

Router(config)#interface g0/0/0.30
Router(config-subif)#encapsulation dot1Q 30
Router(config-subif)#ip address 192.168.30.99 255.255.255.0
Router(config-subif)#end

Router#write
```
## 2. Configure Port to Router as Trunk
### S2
```
S2>enable
S2#configure terminal
S2(config)#interface fa0/7
S2(config-if)#switchport mode trunk
S2(config-if)#switchport trunk allowed vlan 10,20,30
S2(config-if)#no shutdown
S2(config-if)#end
```
