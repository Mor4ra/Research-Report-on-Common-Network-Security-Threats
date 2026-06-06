# Research Report on Common Network Security Threats

## Introduction
- Everyday, organization face attempts to infiltrate, disrupt or compromise their networks and when these attempts succeed, the consequences can be devastating. 
- Financially the average global cost of a data breach in 2025 was $4.44 million according to IBM's [cost of a data breach report](https://www.ibm.com/reports/data-breach). Operationally, these attacks can take critical services offline for several hours, days even. Banks, Healthcare and Government institutions have all been victims to these security attacks. Reputationally, organization that suffer data breaches lose customer trust, sometimes permanently. 
- Beyond individual organizations network infrastructure attacks can have cascading effects across entire industries and regions. On matters landscape, there are different in how they work and the impact they cause, For this reason, there's no one-size-fits-all solution to deal with this, understanding each category matters in order to come up with specific countermeasures to deal with each of them.
- This report examines 5 categories of network security threats -Attacks on Traffic, Network Infrastructure Attacks, DoS/DDoS, Malware & Exploitation and Insider Threats- detailing how each works, the damage it causes and the specific measures organizations can take to defend against them.
## Objectives

- Identify & explain common network security threats covering their technical mechanisms and how they work at the network level.
- Analyze the impact each threat in terms of Confidentiality, Integrity & Availability and specific countermeasures on how to deal with each them.
- Ground each threat in documented, real-world attack cases to illustrate practical consequences.

## Definition
- A Network Security Threat is any action, event or condition that compromises the confidentiality, integrity and availability of data or systems within a network.
- Confidentiality refers to the accessibility of data to only those authorized to do so. Integrity describes information and data being accurate and being unaltered(except through authorized processes.). Availability refers to information being accessible to authorized users whenever needed. Together, these three form the CIA triad, the standard framework used to evaluate security risks.
- Something to note, a threat is different to a vulnerability and an attack, a vulnerability is a weakness that can be exploited, like an unlocked door. An attack is an active attempt to exploit that weakness, walking through the unlocked door, and a threat is the potential for an attack to happen, the possibility that someone could walk through if they tried, thus to emphasize, this report focuses on threats, the categories of risk that organization face on their networks and the conditions that make exploitation possible.

## Threat Analysis
- The following analysis examines five categories of network security threats, each assessed by its mechanism, real-world impact and countermeasures.

### Traffic Interception Threats
- These are threats that target data while it's in transit across a network. This section covers how attackers position themselves to intercept, read or alter communication between two parties, the specific techniques involved such as Man-In-The-Middle(MITM) and packet sniffing and the countermeasures that protect data in motion primarily through encryption.
#### Man in the Middle(MITM)
- A Man-in-the-Middle(MITM) attack is an active network interception technique where the malicious actor positions their system as a transparent proxy between two communicating endpoints. The attacker achieves this by exploiting vulnerabilities in local network protocols(such as ARP spoofing or DNS poisoning) or weaknesses in encryption(like SSL/TLS stripping or rogue Wi-Fi access points) to decrypt, inspect, modify, or reinject traffic.
- Both endpoints authenticate and communicate with the attacker's machine, while believing that they are communicating directly and securely with each other.
##### *Types of MitM* 
There are several attack types that can fall into this category, the most notable are HTTPS Spoofing, SSL/TLS Stripping, ARP Spoofing, DNS Spoofing/Poisoning & Session Hijacking etc. More on MitM can be found [here](https://en.wikipedia.org/wiki/Man-in-the-middle_attack).

- In January 2013, security researcher Gaurang Pandya discovered that Nokia's Xpress Browser was decrypting all HTTPS traffic on Nokia Asha and Lumia phones. The browser was routing both regular HTTP and encrypted HTTPS traffic through Nokia's proxy servers, where it was being decrypted, compressed, and then re-encrypted before being sent to the final destination.
##### How the Decryption worked:
- *Certificate Substitution*
When users visited HTTPS sites like `https://google.com`, the certificate presented to the phone was not Google's certificate, but rather a Nokia certificate for `cloud1.browser.ovi.com` or `wp.browser.ovi.com`

- *MitM Attack*
The browser was configured to trust Nokia's own certificates without warning users. This created what Princeton researcher Steve Schultze called a "man-in-the-client" attack, where the browser itself was complicit in intercepting encrypted traffic.

- *Full Decryption*
Nokia's proxy servers would receive the encrypted HTTPS traffic from the phone, decrypt it using the keys the proxy possessed, compress the data, re-encrypt it with the actual destination's certificate then forward it to the intended website.

##### The Critical Security Flaw:
The phones were configured not to warn users that their HTTPS traffic was being intercepted. This circumvented the fundamental security feature of HTTPS - the certificate validation that's supposed to alert users when they're not connecting directly to the intended server.
- Nokia justified this by stating that the decryption was done to compress data and improve browsing speed, that it helped users get "more value out of their data plans".
This was a serious security problem because:
- Nokia's servers became a massive honeypot, all decrypted user traffic(passwords, credit cards, emails) passed through them.
- Broken end-to-end encryption: the fundamental promise of HTTPS was violated. Users thought they had direct encrypted connections to websites, but they didn't.
- Lack of transparency, the disclosure was buried in vague language. The initial welcome screen mentioned "HTTPS connections will be decrypted in a secure manner," but this was added only after the scandal broke and was unclear to average users.
- No User Control, users couldn't opt out while still using the browser.

From a CIA standpoint, confidentiality was severely compromised, any sensitive information entered on HTTPS sites was visible to Nokia and potentially anyone who compromised Nokia's servers. Integrity, also compromised, users had no guarantee that their transactions, messages, or data weren't being altered before reaching their intended destination. On availability, the impact isn't straightforward, while performance might have improved for some users, the centralized architecture created significant availability risks and removed user control. This is a textbook example of how optimization features can catastrophically compromise security.

##### Countermeasures:
Defending against MitM attacks relies on the following three core principles:
- Authentication - proving who you're talking to.
- Encryption - scrambling the data so it's unreadable if intercepted.
- Integrity - ensuring the data hasn't been altered in transit.
##### *Below are some of the Defensive measures to prevent MitM attacks:*
- *HTTP Strict Transport Security(HSTS)* - this is a web server configuration that forces browsers to only communicate with it over HTTPS, this defeats MitM by preventing SSL stripping, an attacker cannot downgrade a secure HTTPS connection to unencrypted HTTP.
- *Certificate Pinning(Public Key Pinning)* - Hardcodes the expected server certificate(or its public key) directly into a mobile app or browser. If the server presents a different certificate, even if signed by a trusted authority, the connection is stopped.
- *Perfect Forward Secrecy(PFS)* - this generates a unique temporary encryption key for every single session, if an attacker records encrypted traffic and later steals the server's long-term private key, they still cannot decrypt the past recorded sessions.
- *Upgrading to TLS 1.3* - this is the latest version of the Transport Layer Security protocol, removes support for weak, easily broken cryptographic algorithms and speeds up the handshake process.
- *Dynamic ARP Inspection(DAI) & DHCP snooping* - these are configured on network switches to validate ARP(Address Resolution Protocol) packets. It ensures that an IP address is only ever mapped to its legitimate MAC address.
- *DNS over HTTPS(DoH)/DNS over TLS(DoT)* - This encrypts DNS queries(the process of translating `google.com` to an IP address) inside standard HTTPS or TLS tunnels. This stops an attacker from intercepting your DNS request and returning a fake IP address that leads to a phishing site.
- *Virtual Private Networks(VPNs)/Secure Tunnels* - These create an encrypted tunnel from the user's device to a trusted gateway server. All traffic is encapsulated inside this tunnel. This defeats local Wi-Fi eavesdropping because the local router only sees encrypted VPN traffic, not your actual web browsing.

#### Packet Sniffing
- Packet sniffing is the process of intercepting, logging and analyzing data packets as they travel across a network to a destination.
- By default, a computer's network interface card(NIC) ignores any packet not specifically addressed to it. Sniffing tools force the NIC into promiscuous mode, instructing it to read and record all packets passing through the local network segment, regardless of the intended recipient.
- The sniffer captures the raw binary data, decodes it based on network protocols(like TCP/IP) and reconstructs it into a readable format for the user.
- If the traffic is unencrypted, using protocols like HTTP, Telnet, FTP etc. ,the sniffer can easily extract plaintext passwords, emails, and files directly from the captured packets.

In 2007, it was revealed that attackers led by Albert Gonzalez had stolen at least 45 million credit and debit card numbers from TJX companies(parent of TJ Maxx and Marshalls). The breach began with attackers sitting in a store parking lot with a laptop, using wireless packet sniffing tools(Aircrack-ng) to bypass the store's perimeter defenses.
- The targeted store used WEP(Wired Equivalent Privacy) to secure its Wi-Fi which is notoriously flawed because it uses small repeating initialization vectors(IVs) to encrypt data, by analyzing the repeating IVs in captured ARP packets, the software mathematically deduced the WEP encryption key in minutes.
- Lateral movement & pivoting, because TJX's network lacked proper segmentation, the guest/store Wi-Fi had a direct path to the broader corporate network, they were able to scan the internal network and the attackers found weakly protected administrative servers.
- Internal sniffing, getting into the network wasn't enough, they needed the actual payment data. TJX was transmitting raw credit data between stores and central processing servers with inadequate encryption. The attackers installed custom sniffer malware on these internal servers. This malware specifically hunted for and logged Track 1 and Track 2 data - the raw, unencrypted magnetic stripe information(card number, expiration date, and CVV) read by the physical card swipers at the cash registers.

The 2007 TJX breach represents a catastrophic failure of confidentiality. Attackers silently intercepted and stole millions of unencrypted credit card numbers and personal records by copying network traffic in transit. However, because the attack relied purely on passive observation rather that active tampering, the integrity and availability remained completely intact, financial transactions continued to process accurately, and the retail stores experienced zero downtime or performance degradation while the data was being extracted.

##### *Technical Defenses*
Something to note before discussing countermeasures is that you can't stop an attacker from physically capturing packets on a network they have access to, in this case, prevention focuses on making the captured data useless and restricting unauthorized network access.
- Encryption in Transit - using TLS/SSL(HTTPS), SSH, SFTP and WPA3 ensures that even if packets are sniffed, the payload remains unreadable ciphertext.
- Network Segmentation - divide networks into isolated VLANs and subnets using firewalls and routers. if a segment is compromised(guest Wi-Fi), segmentation prevents them from sniffing traffic on critical segments(e.g., payment processing or internal server).
- Switch Security & Port Authentication - prevent unauthorized devices from connecting to the network. Use 802.1X port-based authentication to require credentials before granting network access. Enable Dynamic ARP Inspection(DAI) and DHCP snooping on switches to block ARP spoofing attacks that redirect traffic to a sniffer.
- Secure Wireless Standards - retire WEP and WPA/WPA2-TKIP immediately. Enforce WPA3 or at minimum WPA-AES with strong pre-shared keys or enterprise RADIUS authentication. Disable WPS and hide SSIDs to reduce the attack surface for wireless sniffers.
- Endpoint hardening - deploy host-based intrusion detection systems(HIDS) and endpoint detection and response(EDR) tools to detect when a device enters promiscuous mode or when know sniffing tools(Wireshark, tcpdump, Aircrack-ng) are executed. Restrict admin privileges so users cannot install or run sniffers.
### Network Infrastructure Threats
- These are threats that target systems and protocols that keep a network running(routers, DNS servers and routing tables). Such threats entail how attackers manipulate these foundational components to redirect or disrupt traffic at scale, with BGP hijacking and DNS spoofing as primary examples, and the defenses available at the infrastructure level.
#### BGP Hijacking
- This is the illegitimate takeover of groups of IP addresses by corrupting the internet's routing tables. An attacker announces BGP routes for IP prefixes they do not own or are not authorized to advertise. Because BGP selects routes based on the shortest AS-path length and lacks origin validation, neighboring AS's ([Autonomous systems](https://www.cloudflare.com/learning/network-layer/what-is-an-autonomous-system/)) accept these false announcements as legitimate, redirecting global traffic toward the attacker's infrastructure
In April 2018, a number of IP prefixes(which belonged to Route53 Amazon DNS servers) were announced by a Russian provider, the end result was that individuals who tried logging in to a crypto site were redirected to a fake version of the version controlled by the hackers, these actors managed to steal approximately $152,000 in crypto.

##### *Defending against BGP Hijacking*
*According to [this](https://www.cloudflare.com/learning/security/glossary/bgp-hijacking/) article this are some of the defensive measures against BGP hijacking:* "
- IP prefix filtering - networks should only accept IP prefix declarations if necessary, and should only declare their IP prefixes to certain networks, not the entire internet. Doing so helps prevent accidental route hijacking and could keep the AS from accepting bogus IP prefix declarations; however, in practice, this is difficult to enforce
- BGP hijacking detection - increased latency, degraded network performance, and misdirected internet traffic are all possible signs of a BGP hijack. Many larger networks will monitor BGP updates to ensure their clients do not face latency issues, and a few security researchers do in fact monitor internet traffic and publish their findings.
- Making BGP more secure - BGP was designed to make the internet work, and it certainly does that. But BGP was not designed with security in mind. More secure routing solutions for the internet as a whole(BGPsec) are being developed, but there is no adoption of them yet. For the time being, BGP is inherently vulnerable and will remain so. "

#### DNS Spoofing
- This is the digital equivalent of switching street signs, an adversary injects forged DNS response records into a recursive resolver's cache, they then send a fake reply containing a malicious IP address for a legitimate domain before the authoritative server can respond. Because DNS uses UDP and historically lacked source verification, the resolver accepts the first valid-looking response it receives and caches it. Simply put, when your computer asks "where is my bank's website?", the attacker intercepts the question and shouts back the wrong address faster than the real directory can answer, your machine believes the lie, saves it, and sends you the attacker's fake site instead of the real one.

Real-world example include:
- MyEtherWallet Attack in 2018 - hackers compromised Amazon Route 53 DNS record, redirecting users to a phishing site that stole over $150,000 in Ethereum by harvesting private keys.
- Malaysia Airlines in 2015 - the airline's website was temporarily compromised via DNS spoofing, redirecting visitors to a fake site.
- XLoader Campaign (2018 - 2026) - scammers corrupted DNS settings on public Wi-Fi routers, redirecting Android users to fake Facebook update pages to install malware that harvested banking data.
##### *Defending against DNS Spoofing*
This requires verifying the authenticity of the DNS response, encrypting the communication channel and preventing attackers from forging network traffic.
- DNSSEC (Domain Name System Security Extensions) - This is the definitive fix for data integrity as it adds cryptographic digital signatures to DNS records. When a recursive resolver receives a DNS response, it checks the signature against the authoritative server's public key. If an attacker injects a spoofed IP address, the signature will not match, and the resolver will not match, and the resolver will discard the forged response.
- Transport Encryption(DoH, DoT, DoQ) - these protocols wrap DNS queries inside encrypted tunnels(typically over port 443) which prevents on-path attackers from reading the query or injecting a spoofed response into the plaintext stream.
- Resolver Hardening(Kaminsky defenses) - to prevent "blind" spoofing attacks where an attacker races to guess the DNS transaction ID(TXID), modern DNS resolvers use:
		- TXID randomization - using the full 16-bit entropy for the transaction ID.
		- Source Port randomization - randomizing the UDP source port for each query(instead of always using port 53)
		this expands the attacker's guessing space from 65,536 possibilities to over 4 billion, making it computationally infeasible to guess the correct combination in time.
- Egress Filtering (BCP 38) - network admins configure edge routers to drop outbound packets that have spoofed source IP addresses. This prevents attackers on the inside of a network from sending forged DNS responses that appear to originate from external authoritative servers.
- Endpoint and DHCP Hardening - To prevent local network configuration. This includes locking down router admin interfaces, enforcing strict DHCP policies, and using endpoint security tools to monitor and block unauthorized modifications to the local hosts file or the OS network adapter settings.
### DoS Threats
- Threats that aim to make a system or service unavailable by overwhelming it with traffic or requests. This section covers how volumetric and protocol-based attacks exhaust system resources, how distributed attacks amplify that impact using many machines simultaneously, real documented cases, and mitigation strategies such as rate limiting and traffic filtering.
*The difference between DoS & DDoS attacks* - when the attack is carried out using a single machine, it is a DoS attack, when it is carried out using many machines simultaneously, it is a Distributed Denial of Service, DDoS is significantly harder to defend against because the attack traffic comes from multiples sources, making it difficult to simply block one origin point.

- DDoS & DoS attacks exploit the finite nature of system resources - bandwidth, processing power, memory and connection limits. Volumetric attacks flood the target with massive amounts of traffic, consuming all available bandwidth, the target's network pipe becomes so full that legitimate traffic cannot get through. UDP floods and ICMP floods operate this way.
- Protocol attacks exploit weaknesses in network communication protocols. The most common example is a SYN flood, which exploits the TCP three-way handshake, normally, [to establish connection](https://www.geeksforgeeks.org/computer-networks/tcp-3-way-handshake-process/), the clients sends a SYN request, the server responds with SYN-ACK, and the client completes with ACK. In a SYN flood, the attacker sends a large volume of SYN requests with fake source addresses and never completes the handshake. 
- The server keeps allocating resources waiting for the final ACK that never arrives, eventually exhausting its connection table and becoming unable to serve legitimate requests.
- Application layer attacks target specific services rather than the network broadly. HTTP floods, for example, send what appear to be legitimate web requests at a volume the server cannot handle. These are harder to detect because the individual requests look normal - the problem is the volume.
In a DDos attack, the attacker typically control a botnet, which is a network of compromised machines, could be thousands, or even millions, the attacker commands them to simultaneously send traffic to the target, producing attack volumes no single machine could generate alone.

In October 2016, a DDoS attack targeted Dyn, a company that provided DNS services for a large portion of the internet. The attack used a botnet called Mirai, which was built from a compromised IoT devices(routers, cameras and other connected hardware with weak or default credentials). The botnet generated traffic volumes exceeding 1 terabit per second.
Because Dyn handled DNS resolution for major platforms, the attack took down Twitter, Reddit, Netflix, Spotify and dozens of other services for several hours across the US and Europe.

DoS & DDoS primarily attack Availability when it comes to CIA impact, when a service is overwhelmed, it becomes entirely inaccessible to legitimate users. In the Dyn attack, platforms that depended on Dyn's DNS were unreachable regardless of whether their own systems were functioning.

Confidentiality and Integrity are not directly compromised by DoS attacks, the attacker is not reading or modifying data. Regardless of this, there's an indirect risk worth mentioning, DDoS attacks are sometimes used as a smokescreen, distracting security teams while a separate intrusion occurs elsewhere on the network simultaneously.

##### *Countermeasures*
- Rate limiting - configuring systems to cap the number of requests accepted from a single source within a given time window, this limits the damage a single damage a single machine can cause and slows volumetric attacks.
- Traffic filtering and scrubbing - routing incoming traffic through scrubbing centers that inspect packets and discard malicious or anomalous traffic before it reaches the target. Akamai and Cloudflare offer this service.
- SYN cookies - a technique that modifies the TCP handshake so the server does not allocate resources until the full 3-way handshake is completed, directly neutralizing SYN flood attacks.
- Anycast Network Diffusion - distributing incoming attack traffic across a large network of servers in multiple locations so no single server bears the full load. This is how large CDN providers absorb DDoS traffic
- Redundancy and Failover - maintaining backup systems and multiple points of presence so that if one node is over whelmed, traffic can be rerouted to functional infrastructure automatically.
### Malware & Exploitation Threats
- These threats use malicious software to infiltrate, persist in, and move across networks. The focus will be specifically on the network dimension, how malware is delivered, how it communicates back to attackers, and how it spreads laterally between machines rather than malware analysis broadly.
Common network-based delivery methods include drive-by downloads, where visiting a compromised website triggers an automatic malicious download. Another method is through malicious email attachments that execute when opened.
Exploitation of vulnerable network services is another delivery method, if a service running on an open port has an unpatched vulnerability, an attacker can send a specially crafted request that causes the service to execute malicious code.

Command and Control(C2) is how malware communicates back to the attacker after establishing itself inside a network. Once deployed, most sophisticated malware do not operate independently, they phone home to an attacker-controlled server to receive instructions, exfiltrate data, or download additional malicious components.
This communication happens over the network, often disguised as normal traffic using standard protocols like HTTP or HTTPS to avoid detection.

##### *CIA Impact*
- confidentiality is compromised when malware exfiltrates data back to the attacker via C2 channels - stealing credentials, personal records, financial data, or intellectual property across the network.
- Integrity is compromised when malware alters, corrupts or destroys data on infected systems. 
- Availability is compromised when malware renders systems inoperable, WannaCry is a direct example, encrypted systems could not function, making services entirely unavailable.

##### *Countermeasures*
- **Patch Management** — the WannaCry attack succeeded largely because organizations had not applied an available patch. Keeping operating systems, network services, and software updated closes the vulnerabilities that network-based exploitation depends on.
- **Network Segmentation** — dividing a network into isolated segments so that malware that enters one segment cannot freely move to others. If WannaCry had encountered proper segmentation, its lateral movement would have been contained to a smaller portion of the network.
- **Intrusion Detection and Prevention Systems (IDS/IPS)** — monitoring network traffic for patterns associated with known malware delivery, exploitation attempts, and C2 communication. An IPS can automatically block suspicious traffic in real time.
- **DNS Filtering** — blocking connections to known malicious domains at the DNS level, which disrupts C2 communication. If malware cannot reach its command and control server, the attacker loses the ability to direct it or receive exfiltrated data.
- **Endpoint Detection and Response (EDR)** — security software on individual machines that monitors for malicious behavior and can isolate a compromised machine from the network, preventing lateral movement.
- **Principle of Least Privilege** — restricting user and service accounts to only the access they need. This limits how far malware can spread using captured credentials, since those credentials only grant access to a narrow set of systems.
- **Disabling Unnecessary Network Services** — closing open ports and disabling services that are not needed reduces the attack surface available for network-based exploitation. Eternal Blue exploited SMB — organizations that had disabled SMB where it was not needed were not vulnerable.
### Insider Threats
- Threats originating from individuals with legitimate network access, focusing on how insider access compromises confidentiality and integrity, with the Snowden case as a reference point, and basic access control measures as countermeasures
- Insider threats primarily compromise **Confidentiality** and **Integrity**.

Confidentiality is compromised when an insider accesses and leaks data beyond what their role requires — exfiltrating sensitive files, credentials, or records to unauthorized parties.

Integrity is compromised when an insider deliberately alters, deletes, or corrupts data. Because the access is legitimate, these changes can go undetected for extended periods.

Availability is less commonly affected but not impossible — a disgruntled administrator with system-level access could deliberately take services offline.

In 2013, Edward Snowden, a contractor working for the NSA, used his legitimate system access to exfiltrate a large volume of classified intelligence documents. He did not need to breach any perimeter — he was authorized to be on the systems he accessed. The case exposed how far an insider can reach when access controls are broad and monitoring is insufficient. It remains the most cited example of how insider access, even when granted in good faith, represents a significant security risk.

##### *Countermeasures*

**Principle of Least Privilege** — users should only have access to what they need for their specific role. Snowden had access well beyond what his immediate duties required. Narrowing access limits the damage any single insider can cause.

**Access Logging and Monitoring** — recording who accesses what and when, and flagging anomalous patterns such as large data transfers, access at unusual hours, or retrieval of files outside a user's normal scope.

**Data Loss Prevention (DLP)** — systems that detect and block unauthorized transfer of sensitive data outside the network, whether via email, external drives, or cloud uploads.

**Periodic Access Reviews** — regularly auditing user access rights to ensure they remain appropriate, particularly when roles change or employees leave.

## Conclusion

The five categories examined in this report — traffic interception, network infrastructure attacks, denial of service, malware and exploitation, and insider threats — are mechanically distinct. They operate at different layers, target different components, and require different defenses. However, a common thread runs through all of them: they exploit gaps that exist either in how networks are designed, how protocols were built, or how access is managed.

Traffic interception and network infrastructure attacks exploit the fundamental trust that network protocols were built on — protocols like BGP and DNS were designed in an era where trust between network participants was assumed, not verified. Denial of service attacks exploit the finite nature of system resources, a constraint no engineering solution has fully eliminated. Malware and exploitation exploit the gap between when vulnerabilities are discovered and when they are patched, combined with the freedom of movement that flat, unsegmented networks allow. Insider threats exploit the necessary but risky act of granting humans access to sensitive systems.

What this means practically is that no single defense addresses all of these. Encryption protects data in transit but does nothing against a volumetric flood. Firewalls filter external traffic but are largely blind to a malicious insider. Patch management closes known vulnerabilities but cannot anticipate zero-days. Effective network security requires layered defenses — multiple overlapping controls so that the failure of any one does not leave the network fully exposed.

Understanding these threats individually, as this report has attempted to do, is the necessary first step toward building defenses that are specific, deliberate, and proportionate to the risks organizations actually face.
## References
[# MITM](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)
[# How the Nokia Browser Decrypts SSL Traffic: A "Man in the Client"](https://blog.citp.princeton.edu/2013/01/11/how-the-nokia-browser-decrypts-ssl-traffic-a-man-in-the-client/)
[# Nokia caught decrypting HTTPS traffic - for your own good](https://www.extremetech.com/internet/145373-nokia-caught-decrypting-https-traffic-for-your-own-good)
[# What is Packet Sniffing.](https://www.paessler.com/it-explained/packet-sniffing)
[# BGP leaks and cryptocurrencies](https://blog.cloudflare.com/bgp-leaks-and-crypto-currencies/)
[# What is BGP hijacking?](https://www.cloudflare.com/learning/security/glossary/bgp-hijacking/)
[# DNS spoofing](https://www.imperva.com/learn/application-security/dns-spoofing/)
[# TCP 3-way Handshake Process](https://www.geeksforgeeks.org/computer-networks/tcp-3-way-handshake-process/)
[# A case study on Mirai Botnet Attack of 2016](https://medium.com/@d21dcs151/a-case-study-on-mirai-botnet-attack-of-2016-4b66630e6508)
[# What is C2? Command and Control Infrastructure Explained](https://www.varonis.com/blog/what-is-c2)
[# The case of Edward Snowden](https://www.whistleblowers.org/news/the-case-of-edward-snowden/)


