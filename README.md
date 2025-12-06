# why-wifi-router-many-not-save

# WiFi Security Analysis Using Linux CLI, nmcli, and Zeek

This project demonstrates how to perform a passive WiFi and network security audit using Linux tools.  
All data is anonymized, no WiFi passwords are used, and no intrusion methods are involved.  
Only **broadcast traffic and local logs** are analyzed (fully legal & ethical).

## 🔍 1. Scanning Nearby WiFi Networks with `nmcli`

List available WiFi networks:

```bash
nmcli device wifi list

Example result (anonymized):

IN-USE  BSSID              SSID         MODE   CHAN  RATE         SIGNAL  BARS  SECURITY
*       70:3A:CB:AA:BB:01  Rumah-1      Infra  3     270 Mbit/s   64      ***   WPA2
        70:3A:CB:AA:BB:02  Rumah-2      Infra  5     270 Mbit/s   29      *     WPA1 WPA2
        70:3A:CB:AA:BB:03  Toko-A       Infra  1     130 Mbit/s   29      *     WPA1 WPA2
        70:3A:CB:AA:BB:04  Kos-B        Infra  8     130 Mbit/s   29      *     WPA1 WPA2

📝 Findings

    Several routers still use WPA1/WPA2 Mixed Mode

    TKIP cipher likely enabled (unsafe)

    Some routers use overlapping channels (e.g., channel 3)

    Many users still use ISP default settings

📸 Screenshot

🔐 2. Why WPA1/WPA2 Mixed Mode Is Dangerous

    Router downgrades to TKIP
    Old devices force AP to switch from AES → TKIP.

    TKIP is deprecated
    Similar weaknesses to WEP:

        vulnerable to replay attacks

        packet injection possible

        weak MIC protection

        downgrade attack possible

    Mixed mode = weakest device wins
    Router always chooses the lowest security supported.

📡 3. Passive Traffic Analysis with Zeek

Start Zeek in passive mode:

sudo zeek -i wlp3s0

Zeek generates multiple logs:

    conn.log – connection metadata

    dns.log – DNS lookups

    ssl.log – TLS handshake info

    x509.log – certificate data

Extract TLS SNI (server_name):

zeek-cut server_name < ssl.log

Example output:

cdn.fwupd.org
cdn.fwupd.org

This shows Linux system firmware metadata updates — safe and normal.
📸 Screenshot

⚠️ 4. Indicators of Suspicious Traffic

During a passive audit, these 6 patterns are worth monitoring:

    DNS queries to strange/unfamiliar domains

    Traffic to blacklisted ASN

    Sudden spikes in outbound traffic

    Unusual ports (1337, 4444, 8888, etc.)

    Invalid or self-signed SSL certificates

    Long-duration connections to unknown IPs

Result: no malicious traffic detected.
🛠 5. Recommended WiFi Security Settings

    Use WPA2-AES or WPA3
    Disable WPA and TKIP entirely.

    Disable WPS (important)
    WPS PIN brute force is still widely abused.

    Use clean channels: 1 / 6 / 11
    Avoid channels like 3, 4, 7 — they overlap.

    Change default router password
    Default admin passwords are trivial to guess.

    Update router firmware
    Many router exploits target outdated firmware.

🎯 Conclusion

This project shows that:

    Many home WiFi networks still use weak configuration

    The weakness usually comes from ISP default settings, not user carelessness

    With simple passive tools (nmcli + Zeek), anyone can audit their own network

    Strengthening WiFi is mostly about configuration, not buying new hardware

📘 Ethical Notice

All scans and logs shown here are:

    100% passive

    no packet injection

    no brute forcing

    no joining someone’s WiFi

    SSID/BSSID redacted

This project is for research, education, and home network hardening only.
