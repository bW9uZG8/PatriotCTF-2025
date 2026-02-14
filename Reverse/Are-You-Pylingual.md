<img width="387" height="146" alt="pylingualchal" src="https://github.com/user-attachments/assets/eb403c0c-70fb-458c-918e-a3ba641de85c" />

# Are You Pylingual

This challenge provides you with two files, "pylinguese.pyc" and "output.txt." Opening the output file, you are met with a string of random negative numbers.
### Output.txt
<img width="1266" height="656" alt="image" src="https://github.com/user-attachments/assets/35d32e1b-8551-4a59-aa97-a233920df164" />

This did not help me, so instead, I opened "pylinguese.pyc" in Binary Ninja to see what we were working with. Opening the file up, I immediately noticed the text `"flag.txt"` and `"MASONCC IS THE BEST CLUB EVER,"` but everything else was not readable.
### pylinguese.pyc in Binary Ninja
<img width="1130" height="780" alt="image" src="https://github.com/user-attachments/assets/dcd285a4-a977-4379-9387-9721e4aca0ec" />

After looking up what a .pyc file is and what to do with it, I learned that a .pyc file contains the compiled bytecode of the source file for Python. Using an online Python bytecode decompiler I found, https://pylingual.io/, I was able to decompile the code into human readable code.
### pylinguese.pyc Decompiled
<img width="502" height="534" alt="image" src="https://github.com/user-attachments/assets/825884e0-c1d6-4edd-83c0-02e04aa4fd93" />

Awesome! Now we could start the process of understanding what the code does and how to obtain the flag!
## Understanding the code
This part was a little grueling for me because I had to learn/relearn Python before I could continue on and understand what was happening. After a full day of learning, I decided it was time to give the challenge a shot. Looking into the code, it seems that the first half is taking the words `"MASONCC IS THE BEST CLUB EVER,"` transforming it into ASCII art with pyfiglet, placing the flag within the ASCII art seen below, and joining everything together into a string.
### First half
<img width="450" height="269" alt="image" src="https://github.com/user-attachments/assets/cee4ad3e-f6d9-4ea5-afe1-ef99f2654e7b" />

The second half of the code is where the obfuscation happens. The string is first split into two halves, stored in the variables `first_half` and `second_half`. Each character of both halves is first converted into its ASCII value with the `ord()` function, then a bitwise NOT operation `(~)` is applied to each character. Finally, the result is XORed `(^)` with set values from `first_val = 5` for the first half, and `second_val = 6` for the second half. After all of that, both halves are combined starting with the second half and ending with the first half and printed as the output.
### Second half
<img width="495" height="154" alt="image" src="https://github.com/user-attachments/assets/60db693c-4a3b-4c3c-9ae4-80cc90fa5b93" />

## Reversing the output/Writing Code
Now we understand that the output is the flag obfuscated several times in different ways, all of which is fully reversible!

This part was a LOT of trial and error for me, but I will go over the major steps I took to reverse the operations done to the flag.

I started with modifying the "output.txt" file by removing `"output = "` and the `brackets` so it doesn't interfere with the code and I just have the raw numbers **<ins>(I manually removed them from the file, I did not include this part in my code)</ins>**. After that, I started with opening and parsing the file into an array converting each number into an integer.
### Reading the output
<img width="463" height="76" alt="image" src="https://github.com/user-attachments/assets/033fcbfc-2d66-4b66-b18a-801c91d7c779" />

After that, we need to undo all the steps backwards in order, starting with splitting the array in half and assigning them to which half they are. **I assigned the halves in reverse order for the order they were originally in, the second half of the output is set as `first_half` and vice versa to make it easier to understand what order they will be in at the end.** I achieved this with the section below.
### Splitting the array in half
<img width="306" height="64" alt="image" src="https://github.com/user-attachments/assets/26b50f99-1d72-48a4-aeeb-092d1d3697a9" />

Next, I iterated and XORed `(^)` each number in both halves with the same values set in the original code, `first_val = 5` and `second_val = 6`. I did this because XOR can be reversed by XORing the same data again.
### XOR
<img width="423" height="98" alt="image" src="https://github.com/user-attachments/assets/25de1d5f-4a9c-4534-b6ad-829d12ce4542" />

Now I need to reverse the bitwise NOT `(~)` for both halves. The bitwise NOT operation is self-inverse, which means applying `~` twice would give you the original value. After the XOR operation, I stepped through each character in both halves and applied a bitwise NOT operation `(~)`.
### Bitwise NOT
<img width="310" height="59" alt="image" src="https://github.com/user-attachments/assets/eb730a08-c754-464a-ab1f-9957fcea5be7" />

Lastly, I combined both halves together starting with `first2` and ending with `second2`. We are almost there, we just need to convert all the ASCII values back into characters using the `chr()` function and joining all the characters together in one big string with `''.join(flag)`. 
### Converting back to characters
<img width="314" height="122" alt="image" src="https://github.com/user-attachments/assets/a23f5864-5bbc-4675-9164-9c332589bd9c" />

Now we print it! 

<img width="552" height="362" alt="image" src="https://github.com/user-attachments/assets/99fbd141-726f-436f-b9a8-76373fc1a486" />

At first I was confused what I was looking at, but you could clearly see the flag mixed in with the ASCII art. I knew the flag was in the format `pctf{}`, so following from the first curly bracket `{` and following the string left to right we can read out the flag! At first I tried the flag as `pctf{obFusc4ti0ni5n'tEncRypt1oN}`, but it failed. I figured it followed the same format as the other flags and has underscores between each word so I added in the underscores.

Success!

**pctf{obFusc4ti0n_i5n't_EncRypt1oN}**

This challenge really reinforced the importance of understanding reversible operations in obfuscation methods and how obfuscation doesn't mean the data is encrypted. The challenge looked complicated at first, but after breaking it down in steps and analyzing it, it was a matter of understanding what each step does and how to tackle it. Overall, this was a great challenge for learning Python and stepping into reverse engineering and adding a couple of new tools under my belt!


# Full Code
```
with open('output.txt') as f:
    data = f.read().strip()
    output = [int(i.strip()) for i in data.split(',')]

half = len(output) // 2
second_half = output[:half]
first_half = output[half:]

first_val = 5
second_val = 6

first = [i ^ first_val for i in first_half]
second = [i ^ second_val for i in second_half]

first2 = [~i for i in first]
second2 = [~i for i in second]

combined = first2 + second2

flag = [chr(i) for i in combined]
flag = ''.join(flag)

print(flag)
```
