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
That's all. IMO it the main trick in this ex.<br>
	
