<img width="487" height="428" alt="image" src="https://github.com/user-attachments/assets/e9e20d43-6e02-4184-b750-622ad6546ea0" />

# Word Sea Adventures

This challenge starts by giving you a docx file named `"word_sea_adventures.docx"`. Attempting to open the file results in an error that states, "Archive type not supported."

<img width="450" height="178" alt="image" src="https://github.com/user-attachments/assets/3bf4ed3b-5cb3-463f-86f7-a047111ae877" />

One tool I love to use, especially with forensics challenges, is `binwalk`. One feature of binwalk is being able to analyze binaries to find embedded files within them and extract the files for further analysis. Using binwalk, we see three embedded files that stand out, `crab.jpg`, `sponge.jpg`, and `squid.jpg`.
## Binwalk results
<img width="667" height="165" alt="image" src="https://github.com/user-attachments/assets/e8612131-8f6b-47a5-9538-13497266a93a" />

We can then extract these files using `binwalk -e word_sea_adventures.docx`
## Extraction
<img width="325" height="54" alt="image" src="https://github.com/user-attachments/assets/02142889-c617-4e32-abe8-6f83b7a06de7" />

Now, we have three image files from SpongeBob.

<img width="405" height="326" alt="image" src="https://github.com/user-attachments/assets/64e31653-0160-4020-9f67-ebdcb8380eaf" />

Reading the description of the challenge where it says `"No passphrases are needed for this challenge"` and the nature of it being forensics, I assumed this challenge would involve steganography with a blank password. We can test this with the `steghide` tool which is a command line steganography tool. Using `steghide extract -sf [file]` and pressing enter for the password, I was able to extract a text file from every image. Two images seemed to be decoys, but I was able to extract a file named `flag.txt` from `squid.jpg`.
## More extraction
<img width="517" height="265" alt="image" src="https://github.com/user-attachments/assets/5c7c6e6c-8dc5-4638-845b-f5a8b7bf933a" />

Reading this file gives us the flag, success!
## Flag
<img width="610" height="86" alt="image" src="https://github.com/user-attachments/assets/0ea2d73b-6cb0-4f94-b5c3-84cdfb19722c" />
