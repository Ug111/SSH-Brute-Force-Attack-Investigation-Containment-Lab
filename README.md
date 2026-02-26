# SSH-Brute-Force-Attack-Investigation-Containment-Lab
This  project demonstrates a hands-on investigation of SSH brute-force activity on Ubuntu server: 
The objective shows important aspects like:
 • identify suspicious authentication activity
 • Identify the attacking source
 • Analyze attack behavior and timeline 
 • Confirm whether compromise occurred 
 • Contain the threat using firewall rules
 • Implement persistence for production readiness.

#🖥 Lab Environment
	•	Target Machine: Ubuntu VM
	•	Attacker Machine: Kali Linux VM
	•	Monitoring Source: /var/log/auth.log
	•	Firewall Tool: iptables
	•	Network Type: Local VM network

  🚨 Incident Detection
  Command used:
  #'''bash
  sudo grep "Failed password" /var/log/auth.log
  Observation:
  • Multiple failed login attempts 
  • Multiple username targeted 
  • Multiple source IP addresses observed

  Conclusion:
  Brute-force activity detected).
  (screenshot:01_Failed_login_entries.png)

  🌍 Attacker Attribution (Source IP Analysis)
  Command used:
  #'''bash
  sudo grep "Failed password" /var/log/auth.log | grep -oP 'from \K[0-9.]+' | sort | uniq -c 
  Result:
  • 21 attempts from IP 192.168.1.100 (highest)
  • 15 attempts from IP 192.168.1.200
  
  Conclusion:
  IP 192.168.1.100 is identified as primary attacker
  (Screenshot:02_failed_attempts_by_source_ip.png)

  ⏱ Attack Timeline Analysis
  Command used:
  #'''bash
  sudo grep "192.168.1.100" /var/log/auth.l
  Observation:
  • Attempts clustered within short time intervals
  • Baehavior consistent with automated brute-force script

  Conclusion:
  Attack was automated, not manual.
  (screenshot:03_attack_timeline_192.168.1.100.png)

  👤 Username Targeting Analysis
  Command used:
 #'''bash
 sudo grep "Failed password" /var/log/auth.log | grep -oP 'for (invalid user )?\K\S+' | sort | uniq -c
 Findings:
 • Multiple invalid username targeted
 • Indicates credentials enumeration attempt
 (screenshot:04_failed_login_attempts_by_username.png)

 Assessment:
 Attacker was attempting username discovery prior to successful authentication.

🔍 Impact Assessment
Command used:
#'''bash
sudo grep "Accepted password" /var/log/auth.log

Result:
No suucessful authentication from identified attacker IP addresses.

Conclusion:
No confirmed system was compromised.
(screenshot:05_no_confirmation_of_no_successful_compromise.png)

🛑 Block the Highest attacking IP Address
Primary attacking IP "192.168.1.100" was blocked:
Command used:06_
#'''bash
sudo iptables -A input -s 192.168.1.100 -p tcp --dport 22 -j DROP

Result:
Host-based firewall enforcement
(screenshot:blocking_the_attacking_ip_address.png)


🚨 Verified Firewall Rule were Applied
Command used:
#'''bash
sudo iptables -L INPUT --line-numbers -n
Proof of Containment
Shown:
• DROP
• Source IP
• dpt:22
✅ Containment successful.
(Screenshot:07_firewall_rule_after_containment.png)

Persistent Confirmation
command used:
First install iptables-persistent, then save netfilter-persistent save 
#'''bash
sudo iptables-save | grep 192.168

Result: 
• Confirmatiom rules are saved
• IPs appear in saved config
(screenshot:08_persistent_firewall_rules.png)



  
  
