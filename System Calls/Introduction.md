
  <h1>System Calls</h1>
  <p>System Calls are a way for programs to interact with the operating system internally (i.e these calls are gateways between user mode and kernel mode more on that later on), they provide the services of the os to user applications</p>
  <br><hr><br>
  <h2>How Do these System calls Work?</h2>
  <p>A system call is a well-defined controlled interface through which a user program requests a service from the operating system kernel. It provides a secure transistion from unprivileged excution (user mode) to privileged excution (kernel mode).</p>
  <h3>Excution Flow (in-order)</h3>
  <ol>
    <li>Invocation in User Space</li>
    <p>A user program calls a library wrapper (e.g, in the C Standard library), which prepares the system call arguments according to the system's Application Binary Interface (ABI).
    <ul>
      <li>The system call number in a designated register.</li>
      <li>The arguments in a specific registers or on the stack (architecture-dependant).</li>
    </ul>
    <li>Trap to Kernel (Mode Switch)</li>
    <p>The program excutes a special CPU instruction such as:</p>
    <ul>
      <li><code>syscall</code>(x86-64)</li>
      <li><code>sysenter</code>(ikder x86 fast path)</li>
      <li><code>int 0x80</code>(legacy x86)</li>
    </ul>
    <p>This instruction triggers a software interrupt (trap) that:</p>
      <ul>
      <li>Switches the CPU from user mode to kernal mode.</li>
      <li>Transfers control to a predefined kernel entry point.</li>
      <li>Saves the current user-space execution context (program counter, flags, registers)</li>
    </ul>
    <li>Kernel Dispatch and Validation</li>
    <p>Inside the kernel:</p>
    <ul>
      <li>The system call number is read.</li>
      <li>The kernel consults its system call table to locate the corresponding handler.</li>
      <li>Arguments are validated (e.g checking pointer validity, access permissions and memory safety</li>
      <li>Security checks are enforced (e.g file permissions, capability check).</li>
    </ul>
    <li>Excution of the Requested Service</li>
    <p>The kernel performs the requested operation, such as:</p>
    <ul>
      <li>File I/O</li>
      <li>Process creation <code>fork</code>,<code>exec</code></li>
      <li>Memory management <code>mmap</code><code>brk</code></li>
      <li>Inter-process communication</li>
    </ul>
    <p>The operation is executed with full hardware privileges</p>
    <li>Return to User Space</li>
    <p>After completion the operation:</p>
    <ul>
      <li>A return value (or error code) is placed in designated register</li>
      <li>The CPU executes a return-from-trap instruction</li>
      <li>The processor switches back to user mode</li>
      <li>Execution resumes at the instruction immediately following the system call</li>
    </ul>
  </ol>
<br><hr><br>
<h3>Mode Switch vs Context Switch</h3>
<p>It is important to distinguish between these two concepts:</p>
<ul>
  <li>Mode Swittch</li>
  <p>A system call always causes a mode switch (user -> kernel -> user). the same process continues execution unless otherwise scheduled.</p>
  <li>Context Switch</li>
  <p>A Context Switch occurs only if the scheduler decides to run a different process. This may happen if: </p>
  <ul>
    <li>The system call blocks (e.g waiting for I/O)</li>
    <li>The process voluntarily yields</li>
    <li>Its time slice expires</li>
  </ul>
  <p>Note: Therefore, not every system call results in a context switch.</p>
</ul>
<br><hr><br>
<h3>Why are System Calls So Necessary</h3>
<p>Without System Calls:</p>
<ul>
  <li>User programs would need direct hardware access</li>
  <li>There would be no standardized interface yo system resources</li>
  <li>Security and isolation between processes would be compromised</li>
  <li>Resource management would become inconsistent and unsafe</li>
</ul>
<p>System Calls Provide:</p>
<ul>
  <li>Controlled privilege escalation</li>
  <li>Hardware abstraction</li>
  <li>Security enforcement</li>
  <li>Process isolation</li>
  <li>Standardized OS services</li>
</ul>
<br><hr><br>
<h2>Different Types of System Calls</h2>
<p>The OS provides different alot of system calls. they are categorized into different sections:</p>
<ul>
  <li>File System Calls:</li>
  <p>These system calls manage file and directories, including creation, access, modification and metadata handling</p>
  <ul>
    <li><code>open()</code>: Opens a file and returns a file descriptor</li>
    <li><code>close()</code>: Closes an open file descriptor</li>
    <li><code>write()</code>: Writes data from buffer to a file descriptor</li>
    <li><code>read()</code>: Reads data from a file descriptor into a buffer</li>
    <li><code>seek()</code>: Moves the file pointer to a specific position in a file (e.g jump to line 47 to read from there)</li>
    <li><code>lseek()</code>: Repositions the file offset within a file</li>
    <li><code>creat()</code>: Creates a new file or truncates an existing file</li><br>
  </ul>

  
  <li>Process Control Calls:</li>
  <p>These calls manage process life cycle and execution flow, including creation, execution, synchronization and ofc termination.</p>
  <ul>
    <li><code>fork()</code>: Creates a new process by duplicating the calling process</li>
    <li><code>exec()</code>: Replaces the current process image with a new program</li>
    <li><code>wait()</code>: Suspends execution until a child process terminates</li>
    <li><code>exit()</code>: Terminates the calling process and performs cleanup</li>
    <li><code>_exit()</code>: Immediately terminates the process without standard I/O cleanup</li>
    <li><code>kill()</code>: Sends a signal to a process</li><br>
  </ul>

  
  <li>Memory Management:</li>
  <p>These System calls control the allocation, mapping and protection of a process's address space.</p>
  <ul>
    <li><code>brk()</code>: Changes the end of the data segment (heap)</li>
    <li><code>sbrk()</code>: Increments or decrements the program's data space.</li>
    <li><code>mmap()</code>: Maps files or devices into memory</li>
    <li><code>mprotect()</code>: Changes memory protection settings</li>
    <li><code>munmap()</code>: Unmaps a memory region</li>
    <li><code>mlock()</code>: Locks memory pages to prevent swapping</li>
    <li><code>munlock()</code>: Unlocks previously locked memory pages</li><br>
  </ul>
  
  <li>Interprocess Communication aka (IPC):</li>
  <p>These enable data exchange and synchronization between processes</p>
  <ul>
    <li><code>pipe()</code>: Creates a unidirectional communication channel</li>
    <li><code>socket()</code>: Creates an endpoint for a network communication</li>
    <li><code>semget()</code>: Creates or accesses a semaphore set</li>
    <li><code>shmget()</code>: Allocates a shared memory segment</li>
    <li><code>msgget()</code>: Creates or accesses a message queue</li><br>
  </ul>

  
  <li>Device Management:</li>
  <p>These system calls allow progams to interact with hardware devices (which are typically abstracted as special files in UNIX-like systems)</p>
  <ul>
    <li><code>open()</code>: Opens a device file for access</li>
    <li><code>close()</code>: Closes a device file descriptor</li>
    <li><code>read()</code>: Reads data from a device</li>
    <li><code>write()</code>: Writes data to a device</li>
    <li><code>poll()</code>: Monitors multiple file descriptors for readiness</li>
    <li><code>select()</code>: Waits for activity on multiple file descriptors</li>
    <li><code>ioclt()</code>: Performs deviec-specific input/output control operations</li><br>
  </ul>

</ul>
<br><hr><br>
<h2>Code Example:</h2>
<p>For me personaly the only way to understand any computer science concept is to try and code it, that's when it all clickes. in this example </p>
<pre>
  <code class="c">
    #include <stdio.h>
    #include <unistd.h>
    #include <string.h> // strlen()
    #include <fcntl.h> // includes the flags

    int main() {
      /*
      open():
      Parameters:
        1. path of the filename
        2. flags (bitwise OR of access modes and options)
        3. Mode (file permissions) - required when O_CREAT is used 

        O_CREAT --> Create the file if it does not exist
        O_RDWR  --> Open for both reading and writing

        0644 permission breakdown (in octal):
          Owner: read + write(6)
          Group: read(4)
          Others: read(4)
      */
    
      int file = open("file.txt", O_CREAT | O_RDWR, 0644);
      if (file < 0) {    // open() returns -1 on failure
        perror("Couldn't Open File.\n"); // prints error message based on errno
        return 1; // Non-Zero return value conventionnally indicates failure
      }

      //--------------------------------------------------------------------------

      /* write():
         Parameters:
           1. File descriptor
           2. Pointer to Buffer containing data
           3. Number of Bytes to Write
      /*
      
      const char* msg = "Hello World!";  
      ssize_t bytes_written = write(file, msg, strlen(msg); // note: sizeof() would return the size of the pointer
                                                            //  pointers of size on 64-bits systems: 8 bytes
      if (bytes_written == -1) {                            //  "       "    "   on 32-bits systems: 4 bytes
        perror("Write Failed.");
        close(file); // Clean up before leaving
        return 1;
      }

        closes the file
      /* close():
      */
      if (close(file) == -1) {
        perror("Close Failed");
        return 1;
      }

      printf("Written to the file with success! \n");
      return 0;
    }
  </code>
</pre>
