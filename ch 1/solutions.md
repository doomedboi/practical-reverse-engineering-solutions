Page 11:
Q: This function uses a combination SCAS and STOS to do its work. First, explain
what is the type of the [EBP+8] and [EBP+C] in line 1 and 8, respectively.
Next, explain what this snippet does.
A: [EBP+8] = char*(null terminating string), [EBP+C] = char
   In short, this function acts like memset for string.
   The function finds string's end(0 character), calculates string's name and then replaces each character of target string to second parameter.
Pseudo c:
   fff(char* src, char ch)
      {memset(src, ch, strlen(src));}
/* There is no explicit call to memset bt logic is true in terms of reusage. */

In more details: 
	or ecx, 0FFFFFFFFh = -1
	add ecx, 2 = actual len of the string
	without counting null-term and numerating from 0
That's all. IMO it the main trick in this ex.
	