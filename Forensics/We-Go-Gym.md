<img width="460" height="275" alt="image" src="https://github.com/user-attachments/assets/b8fda431-3ada-4451-968a-5e23e131c4f6" />

# We Go Gym

This challenge gives us a packet capture file named `"wegogym.pcap"`. Opening up and analyzing the file, I noticed that it was mostly full of `SSH` and `TCP` packets. 

<img width="1920" height="742" alt="image" src="https://github.com/user-attachments/assets/af989486-40c8-4dfc-b400-2c8c40b80d4e" />

One of the first things I always check when doing a packet capture challenge is following the TCP stream. After filtering through the streams, I noticed that there were many curl requests containing random Base64 strings.
## Curl
<img width="811" height="272" alt="image" src="https://github.com/user-attachments/assets/9f4122dc-c20c-4235-9143-f6fea3d60e59" />

Trying to decode them gave me nothing, and I figured they were just a red herring or noise because of how many there were. This suspicion was later strengthened after finding the same requests with a different User-Agent requesting the file `noise?d?d.txt`
### Noise
<img width="810" height="258" alt="image" src="https://github.com/user-attachments/assets/1ee407d9-3c4b-4128-9f02-2e8e5c6a01d6" />

At this stage, I was pretty lost and did not know where to look. Trying to export objects would just give me the same curl requests and noise, and everything else was encrypted. After looking around for quite a while, I finally found an anomaly that stood out to me! Looking through the `Statistics` tab on Wireshark, I went to `Statistics` -> `IPv4 Statistics` -> `Source TTLs`. You can immediately notice an anomaly of the TTL field for packets from the IP `192.168.1.235` and sending to the IP `192.168.1.203`.
## TTL (Time to Live)
<img width="802" height="491" alt="image" src="https://github.com/user-attachments/assets/202d9f6c-4a91-41e8-83e0-6dd92689fd46" />

## What is TTL?
TTL (Time to Live) is a value given to packets under the IPv4 header to prevent a packet from endlessly circulating in a network. It does this by starting with a set number and decrementing it by 1 for every router it passes through, and once this value reaches 0, the packet will be discarded. Some common initial TTL values include 64 for Linux and 128 for Windows systems. Now knowing this, we can see how unusual it is to see many random values within a short time frame from the same address.

Using the filter `ip.src == 192.168.1.235 && ip.dst == 192.168.1.203` I was able to isolate the conversation between the two addresses and find what packets this TTL anomaly was coming from. Looking through, it was the `curl` requests that contained the anomaly! There were also `noise.txt` requests but those didn't display anything unusual so we can filter these out.
## Curl with TTL
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/1b8edbf6-344a-4994-a544-ad99da4df873" />

We can isolate the curl requests with the filter `http.user_agent == "CURL"`. And now we only have the packets that we care about. I found the TTL field under IPv4, right clicked, and applied as column to make the next step easier.
## Filtered Curl with TTL Column
<img width="877" height="381" alt="image" src="https://github.com/user-attachments/assets/2bb0008f-4c7f-4ac7-85a3-418dbbc2746e" />

Lastly, I made sure that all my packets were sorted in order by time and extracted each value. This gives the values: [80 67 84 70 123 116 49 109 51 95 116 48 95 103 51 55 95 53 119 48 49 125]. These looked like ASCII values to me, so I immediately decoded them. Doing so reveals the flag!

**PCTF{t1m3_t0_g37_5w01}**
