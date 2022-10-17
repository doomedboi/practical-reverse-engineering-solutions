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
were not properly restored before executing RET ?<br>
A:In short, we would not control over execute flow. Put another word,
 when ret instruction pops invalid ret address, it jumps to that invalid address and
 continue execute commands from the wrong space. Hance, this will result in an incorrect behavior.<br>
Q:In all of the calling conventions explained, the return value is stored in a
32-bit register ( EAX ). What happens when the return value does not fi t in a
32-bit register? Write a program to experiment and evaluate your answer.
Does the mechanism change from compiler to compiler?
<br>
A: In case of msvc nothing checked. Bytes interprets just as it. <br>
No ant additional check or smth like this. <br>

Page 35: <br>
Q1: Repeat the walk-through by yourself. Draw the stack layout, including<br>
parameters and local variables. <br>
A: Ok, it's to easy. <br>
Q2: See attachments <br>
Q3: That number means a number of bytes calee have to return to the stack.<br> In other word, how much bytes return instruction will return. Example: ret12.<br>
Q4: I have to implement:<br>
note: I didn't test this funcs because don't have enough time :'C It's like concept<br>


```asm
push esi
call strlen
strlen:
push ebp
mov ebp, esp

xor eax, eax
mov edi, [ebp+8] //arg0
mov ecx, edi
SCAS 
lea eax, [ecx - edi]
ret
```

strchr:
```asm
strchar:
push ebp
mov ebp, esp

mov EDI, [ebp+8] //str
mov ax, [ebp+12] //charracter
SCAS

mov EAX, EDI

ret
```

```asm
memcpy:(dst, src, len)
push EBP
mov EBP, ESP

mov EDI, [ebp + 8] //dst
mov ESI, [ebp + 12] //src
mov ECX, [ebp+ 16] //len

REP
movsb

ret
```

```asm
memset(dst, val, sz):
push ebp
mov ebp, esp

mov EDI, [ebp + 8]
mov EAX, [ebp + 12]
mov ECX, [ebp + 16]

REP
STOS

ret
```

To be continued bt now I'm too lazy

Q5: Decompile win kernel routines: <br>
KeInitializeDpc:<br>
```C++
It's x64 routine. Remeber ABI of x64 I assume that it function takes 3 arg because of r8 - last register in the reversed code.
ABI: rcx, rdx, r8, r8
Next it's time to open MSDN and find function's signature.
void KeInitializeDpc(
  [out]          __drv_aliasesMem PRKDPC Dpc,
  [in]           PKDEFERRED_ROUTINE      DeferredRoutine,
  [in, optional] __drv_aliasesMem PVOID  DeferredContext
);
Let's pullout to the windbg.

void KeInitializeDpc(
  [out]          __drv_aliasesMem PRKDPC Dpc,
  [in]           PKDEFERRED_ROUTINE      DeferredRoutine,
  [in, optional] __drv_aliasesMem PVOID  DeferredContext
)
{
	
	dpc->Type = 13h;
	dpc->Importance   = 1;
	dpc->Number = 0;
	dpc->DpcData = 0;
	dpc->ProcessorHistory = 0;
	dpc->DeferredRoutine = DeferredRoutine;
	dpc->DeferredContext = DeferredContext;

	return;
}

Interesting moment is that I didn't find any definition of PRKDPC. The Project Vergilius helped me with this trick.
In case you haven't enternet connection or whathever else  you can use "dt" (w/o " ") command to look at any win structure.
Btch I prefer Project Vergilius.

```

```C++
VOID
KeInitializeApc(
  IN  PRKAPC Apc,
  IN  PRKTHREAD Thread,
  IN  KAPC_ENVIRONMENT Environment,
  IN  PKKERNEL_ROUTINE KernelRoutine,
  IN  PKRUNDOWN_ROUTINE RundownRoutine OPTIONAL,
  IN  PKNORMAL_ROUTINE NormalRoutine OPTIONAL,
  IN  KPROCESSOR_MODE ApcMode OPTIONAL,
  IN  PVOID NormalContext OPTIONAL
  )
{
	Apc->Type = 0x12;
	Apc->Size = 0x58;
	
	
	if (Environment == CurrentApcEnv)
	    Apc->ApcStateIndex = Thread->ApcStateIndex;
	else
	    Apc->ApcStateIndex  = Environment;
	
	
	Apc->Reserved[1] = [rsp + 0x28]
	Apc->Thread = Thread;
	Apc->Reserved[3] = [rsp + 0x30]	
	Apc->Reserved[0] = KernelRoutine;
	
	if (NormalRoutine == 0) {
	    Apc->ApcMode = 0;
 	    Apc->NormalContext = 0;
	}
	else {
        	    Apc->ApcMode = ProcessMode;
                Apc->NormalContext = NormalContext;
    	}
	Apc->Inserted = 0;
	
	return;
}

```

```C++
NTSYSAPI NTSTATUS NTAPI 
RtlValidateUnicodeString	(
_In_ ULONG 	Flags,
_In_ PCUNICODE_STRING String 
)
{
	if (!Flags)
		return STATUS_INVALID_PARAMETER
	if (!String)
		return STATUS_SUCCESS
	if (String->Length != 1)
		STATUS_INVALID_PARAMETER
	if (String->MaximumLength != 1)
		STATUS_SUCCESS

	if (String->Length > String->MaximumLength)	
		return STATUS_INVALID_PARAMETER;
	if (String->MaximumLength > 0xFFFE)
		return STATUS_INVALID_PARAMETER
	if (String->Buffer != 0) {
		return STATUS_SUCCESS;
	}
	if (String->Length != 0)
		return STATUS_INVALID_PARAMETER
	if (String->MaximumLength == )
		return STATUS_SUCCESS;
}
```
