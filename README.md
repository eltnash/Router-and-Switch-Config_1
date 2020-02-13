# Router-and-Switch-Config_1
Eigrp  Router configurations and Vlan Switch configurations


#!/usr/bin/env python

import getpass
import sys
import telnetlib

user = "elt"
password =  "cisco"

DEVICE = {}
with open("FILE_1") as Openfile:  
    for i in Openfile:
        x = i.split(",") # LIST [ROUTER_1 -3], [192.168.122.249 - 142]
        a = x[0]      # ROUTER_1 - 3
        b = x[-1]     # 192.168.122.249 - 142
        c = len(b)-1  # 14
        b = b[0:c]    # b[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14]
        DEVICE[a]=b   # Dict {"ROUTER_1 - 3": "192.168.122.249"}


if "\n" in DEVICE:
    del DEVICE["\n"]	# key:value pair deletion
print(DEVICE)


for n in DEVICE:
    #if n == "ROUTER_1":
	    #tn = telnetlib.Telnet(DEVICE[n])
		#pass
	#else:
	    #continue
    tn = telnetlib.Telnet(DEVICE[n])
    tn.read_until("Username: ")
    tn.write(user + "\n")
    if password:
        tn.read_until("Password: ")
        tn.write(password + "\n")
    tn.write("enable\n")
    tn.write(password + "\n")
    tn.write("conf t\n")
    def loopbacks(value):
        b = []
        r = 0
        for i in range(1,4):
            a = ("ip address {a}.{b}.{c}.{d} 255.255.255.255\n".format(a=i,b=i,c=i,d=i))
            b.append(a)    
        tn.write("int loop 0\n")
        tn.write(str(b[int(value)]))
        tn.write("exit\n")
		
    def SWITCH_VLANS(vlan,int1,int2,int3):
        print("CONFIGURING SWITCH " + n)
		item =[]
        for a in range (0,4):
		    item.append(a)
            tn.write("vlan " + str(item[int(vlan)]) + "\n")
            tn.write("name Python_VLAN_" + str(item[int(vlan)]) + "\n")
        tn.write("exit\n")
		tn.write("interface vlan " + str(item[int(vlan)]) + "\n")
		tn.write("no shut\n")
		tn.write("exit\n")
        tn.write("interface range ethernet {a}/{b}-{c}\n".format(a=int1,b=int2,c=int3)
        tn.write("switchport mode access\n")
        tn.write("switchport access vlan " + str(item[int(vlan)]) + "\n")
		tn.write("interface ethernet 0/0\n")
		tn.write("switchport trunk encapsulation dot1q\n"}
        tn.write("switchport mode trunk\n")
        tn.write("exit\n")
        tn.write("end\n")
	
	if n == "SWITCH_1":
	    SWITCH_VLANS(2,0,2,2)
		
	elif n == "SWITCH_2":
	    SWITCH_VLANS(3,0,3,3)

    elif n == "ROUTER_1": 
        print("CONFIGURING " + n)
        loopbacks(0)
		tn.write("int f0/1\n")
		tn.write("no shut\n")
        tn.write("int f0/1.2\n")
        tn.write("description Link to Network A\n")
		tn.write("encapsulation dot1q 2\n")
        tn.write("ip address 192.168.10.2 255.255.255.0\n")
        tn.write("int f1/0\n")
        tn.write("description EIGRP_LINK_TO_R2\n")
        tn.write("ip address 10.0.1.1 255.255.255.252\n")
        tn.write("no shut\n")
        tn.write("exit\n")
        tn.write("router eigrp 1\n")
        tn.write("network 1.1.1.1 0.0.0.0\n")
        tn.write("network 10.0.1.0 0.0.0.3\n")
        tn.write("network 192.168.10.0 0.0.0.255\n")
        tn.write("no auto-summary\n")
		tn.write("passive-interface fastethernet 0/0\n")
		tn.write("passive-interface fastethernet 0/1\n")
        tn.write("exit\n")
        tn.write("end\n")
        tn.write("exit\n")
        print(tn.read_all())

    elif n == "ROUTER_2": #if value in (a) is not equal to 3  do not execute this config
        print("CONFIGURING " + n)
        loopbacks(1)
        tn.write("int f0/1\n")
        tn.write("description EIGRP_LINK_TO_R2\n")
        tn.write("ip address 10.0.1.2 255.255.255.252\n")
        tn.write("no shut\n")
        tn.write("exit\n")
        tn.write("int f1/0\n")
        tn.write("description EIGRP_LINK_TO_R3\n")
        tn.write("ip address 10.0.2.1 255.255.255.252\n")
        tn.write("no shut\n")
        tn.write("exit\n")
        tn.write("router eigrp 1\n")
        tn.write("network 2.2.2.2 0.0.0.0\n")
        tn.write("network 10.0.1.0 0.0.0.3\n")
        tn.write("network 10.0.2.0 0.0.0.3\n")
        tn.write("no auto-summary\n")
		tn.write("passive-interface fastethernet 0/0\n")
        tn.write("exit\n")
        tn.write("end\n")
        tn.write("exit\n")
        print(tn.read_all())
		
    elif n == "ROUTER_3": #if value in (a) is not equal to 1  do not execute this config
        print("CONFIGURING " + n)
        loopbacks(2)
		tn.write("int f0/1\n")
		tn.write("no shut\n")
        tn.write("int f0/1.3\n")
        tn.write("description Link to network B\n")
		tn.write("encapsulation dot1q 3\n")
        tn.write("ip address 192.168.20.2 255.255.255.0\n")
        tn.write("int f1/0\n")
        tn.write("description EIGRP_LINK_TO_R2\n")
        tn.write("ip address 10.0.2.2 255.255.255.252\n")
        tn.write("no shut\n")
        tn.write("router eigrp 1\n")
        tn.write("network 3.3.3.3 0.0.0.0\n")
        tn.write("network 10.0.2.0 0.0.0.3\n")
        tn.write("network 192.168.20.0 0.0.0.255\n")
		tn.write("no auto-summary\n")
		tn.write("passive-interface fastethernet 0/0\n")
		tn.write("passive-interface fastethernet 0/1\n")
        tn.write("exit\n")
        tn.write("end\n")
        tn.write("exit\n")
        print(tn.read_all())

    else:
        continue
print("CONFIGURATION COMPLETE!!!")
