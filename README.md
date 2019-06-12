# Remote code execution vulnerability affecting Mavenir IP-SM-GW MCO

[Mavenir](https://mavenir.com/) sells MCO product which serves as IP-SM-GW to deliver SMS over IMS service to VoLTE users.

An end-to-end vulnerability has been discovered on MCO product, rated as high (score 8.1, CVSS:3.0/AV:N/AC:H/PR:N/UI:N/S:U/C:H/I:H/A:H).

The vulnerability has been fixed, and vulnerable systems shall now have been patched - according to vendor answer.

## SMSoIP at a glance

When UE sends a short message, it is carried through IMS to IP-SM-GW, and next to SMS-C, ensuring interoperability with legacy subsystems:

```
+--+        +---------+    +---------+          +---------+
|UE|        |  S-CSCF |    |IP-SM-GW |          |  SMS-C  |
+--+        +---------+    +---------+          +---------+
  | -----------> |              |                   |
  |  SIP MESSAGE |              |                   |
  |  RP-DATA     | -----------> |                   |
  |              |  SIP MESSAGE |                   |
  |              |  RP-DATA     |                   |
  |              | <----------- |                   |
  |              |  202 Accepted|                   |
  | <----------- |              |                   |
  |  202 Accepted|              |                   |
  |              |              | ----------------> |
  |              |              |  MAP mo-FwdSM-Req |
  |              |              | <---------------- |
  |              |              |  MAP mo-FwdSM-Ack |
...
```

Due to the way SMS over IP works, UE controls RP-DATA, and **IMS merely routes RP-DATA to IP-SM-GW**. Hence a vulnerability on IP-SM-GW would become exploitable from UE. Such a vulnerability has been discovered, and an end-to-end exploit has been successfully achieved. Any attacker with a SMSoIP enabled profile may trigger remote code execution on IP-SM-GW MCO.

## Vulnerability details

The version on which the vulnerability has been discovered is R3.0-10.09.B09. Vendor has issued ticket reference MCO-6083 to track and correct this vulnerability. A fix has been supplied by the vendor and tested, in version R3.0-10.12.B11. We encourage all operators who have this solution deployed in their network to ensure they are not vulnerable to this issue.

The process that handles SIP messages on MCO is `scn`, which runs under unprivileged account `mco`. To our knowledge, all functional processes run under `mco` account, which means that all the signalling traffic passing through IP-SM-GW, personal data associated with short messages – recipient & sender, date and time, content - , and configuration files are at risk.

## Timeline

* 21 Jan 2019: Initial contact to report a remotely exploitable stack overflow
* 22 Jan 2019: Feedback from vendor, working on a secure communication channel
* 27 Jan 2019: Channel established
* 28 Jan 2019: Initial communication of vulnerability details
* 28 Jan 2019: Vendor confirmed it has received them, working with engineering
* 30 Jan 2019: Vendor identified vulnerable versions
* 30 Jan 2019: Orange asked for CVE to vendor
* 31 Jan 2019: Vendor do not plan to ask for a CVE
* 26 Feb 2019: Orange indicates a public disclosure will be made on 11th of March
* 06 Mar 2019: Orange inquires about vendor feeback
* 06 Mar 2019: Vendor acknowledges vulnerability and asks to report disclosure to 27th of March
* 08 Mar 2019: Orange accepts to postpone the public disclosure
* 27 Mar 2019: [public disclosure](https://github.com/orangecertcc/mavenir-mco-rce)
