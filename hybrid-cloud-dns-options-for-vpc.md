# Hybrid Cloud DNS Solutions for Amazon VPC

## Introduction
* AWS Services that need DNS: ELB, RDS, EC2, Redshift, CloudFront
* decouple IP address from resource
* R53 can't send queries to customer managed authoritative DNS servers on-premises or in VPC
* for a simpler hybrid cloud DNS solutions, see R53 Resolver
* Two possible solutions:
	* multiple EC2 instances running DNS forwarder to AmazonProvidedDNS or org's on-premise DNS
	* replicate on-premises DNS domain to R53 private hosted zone
## Key Concepts
#### Amazon VPC DHCP Options Set
* Dynamic Host Configuration Protocol
* standard for passing config info to hosts on TCI/IP network
* config includes _domain-name-servers_, _domain-name_, _ntp-servers_, and _netbios-node-type_
* created and applied per VPC
* cant have DNS server list at AZ level
#### Elastic Network Interfaces
* virtual interfaces you attach to instances in VPC
* like network adapters, are interfaces that let devices connect to a network
#### Route 53 Private Hosted Zone
* container that holds DNS records visible to 1 or more VPCs
#### Amazon DNS Server
* provides full public DNS resolution
* additional resolution for internal records for VPC and customer-defined records
* AmazonProvidedDNS maps to DNS server running on reserved IP address
	* base of VPC network range plus 2
	* 10.0.0.0/16 VPC -> DNS server is located on 10.0.0.2
	* primary CIDR block only
#### Connection Tracking
* track info about traffic to and from an instance
* stateful 
#### Linux Resolver
* _/etc/resolv.conf_
* queries DNS listed in config file
* leads to full DNS resolution
#### Linux DHCP Client
* customize the set of DNS servers the instance uses for resolution
* further updates the _resolv.conf_ with list of DNS server IPs
* can replace DNS servers provided by AWS DHCP options set with static list
* _/etc/dhcp/dhclient.conf_
#### Conditional Forwarder - Unbound
* examines DNS queries from instance and forwards to different DNS server based on rules set in config
* play vital role in bridge name resolution b/w on-premises and cloud
## Contraints
#### Packet per Second (PPS) per Network Interface Limit
* hard limit of 1024 packets send to Amazon Provided DNS server every second
#### Connection Tracking
* in rare cases, with large amount of concurrent connections, exhausting all connection tracking resources
* recommend to disable connection tracking and configure rules for both inbound and outbound
#### Linux Resolver
* max DNS servers in _resolv.conf_ is 3
## Solutions
* best practices to architect DNS solution in hybrid cloud
#### Secondary DNS in an Amazon VPC
* R53 mirror on-premises DNS zones without needing DNS forwarding resources
* Use cases:
	* don't want to build/manage conditional forwarder instnaces
	* don't have in-house DevOps expertise
	* infrequently changing DNS environment
* Advantages
	* Low management overhead
	* Low operational cost
	* High resilient DNS infrastructure
	* low possibility for instances to breach PPS per network interface 
* Limitations
	* On-premises instances can't query Amazon DNS server directly for EC2 hostnames
	* Requires on-premises DNS server to support full zone transfer query (AXFR)
	* Requires working with R53 API limits
#### Decentralized conditional forwarders
* forward queries meant for on-premises resources to on-premises DNS server, rest to Amazon DNS servers
* two options:
	* highly distributed forwarders - run forwarders on every instancec 
		* Use cases:
			* high volumes of DNS queries
			* infrequently changing DNS environment
		* Advantages:
			* resilient DNS infrastructure 
			* low possibility for instances to breach PPS
		* Limitations
			* complex setup and management
			* investment in relevant skill set for config management 
	* zonal forwarders using supersede - localizing forwarders to specific AZ
		* Use cases:
			* have exsting set of conditional forwarders
			* doesn't generate high volume of DNS traffic
		* Advantages:
			* fewer forwarders to manage
			* better overall resiliency
		* Limitations:
			* complex setup and management as DNS environment grows
			* possibility of breaching PPS limit


