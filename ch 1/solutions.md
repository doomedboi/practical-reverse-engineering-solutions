Page 11: <br>
Q: This function uses a combination SCAS and STOS to do its work. First, explain <br>
what is the type of the [EBP+8] and [EBP+C] in line 1 and 8, respectively. <br>
Next, explain what this snippet does. <br>
A: [EBP+8] = char*(null terminating string), [EBP+C] = char <br>
   In short, this function acts like memset for string. <br>
   The function finds string's end(0 character), calculates string's name and then replaces each character of target string to second parameter. <br>
Pseudo c: <br>
   **fff(char* src, char ch) <br>
      {memset(src, ch, strlen(src));} <br>**
/* There is no explicit call to memset bt logic is true in terms of reusage. */
<br>
In more details: <br> 
	or ecx, 0FFFFFFFFh = -1<br>
	add ecx, 2 = actual len of the string
	without counting null-term and numerating from 0<br>
That's all. IMO it's the main trick in this ex.<br>
	
Page 17 <br>
Q:Come up with at least two code sequences to set EIP to 0xAABBCCDD.
A: mov ax, 0    call ax || push 0  ret

Q:In the example function, addme , what would happen if the stack pointer
were not properly restored before executing RET ?
A:In short, we would not control over execute flow. Put another word,
 when ret instruction pops invalid ret address, it jumps to that invalid address and
 continue execute commands from the wrong space. Hance, this will result in an incorrect behavior.
Q:In all of the calling conventions explained, the return value is stored in a
32-bit register ( EAX ). What happens when the return value does not fi t in a
32-bit register? Write a program to experiment and evaluate your answer.
Does the mechanism change from compiler to compiler?
A: In case of msvc nothing checked. Bytes interprets just as it. <br>
No ant additional check or smth like this. <br>
