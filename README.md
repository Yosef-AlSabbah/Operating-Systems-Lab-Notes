### Forking a Process in Operating Systems

- **`fork()`** creates a new process by duplicating the parent process. 
- Each process has a unique identifier called a **PID** (Process Identifier).
- Both the parent and child processes run concurrently after the `fork()` call.

#### Example: Forking a Process

```c
#include <stdio.h>
#include <unistd.h>

int main() {
    pid_t pid = fork(); // Create a new process

    if (pid < 0) {
        // Error occurred
        perror("Fork failed");
        return 1;
    }

    if (pid == 0) {
        // Child process
        printf("This is the child process. PID: %d\n", getpid());
    } else {
        // Parent process
        printf("This is the parent process. Child PID: %d\n", pid);
    }

    return 0;
}
```

### Behavior of `fork()`
- **Child Process**: If `fork()` returns `0`, the process executing is the **child** process.
- **Parent Process**: If `fork()` returns a value greater than `0`, this value represents the **PID** of the child process, and the executing process is the **parent**.
- If `fork()` returns a value less than `0`, it indicates a **failure** in creating the child process.

#### Example: Using `fork()` and Handling Return Values

```c
#include <stdio.h>
#include <unistd.h>

int main() {
    pid_t pid = fork(); // Create a new process

    if (pid < 0) {
        // Error occurred
        perror("Fork failed");
        return 1;
    }

    if (pid == 0) {
        // Child process
        printf("Child process executing. PID: %d\n", getpid());
    } else {
        // Parent process
        printf("Parent process executing. Child PID: %d\n", pid);
    }

    return 0;
}
```

### Wait and Process Termination
- The **parent process** can call `wait()`, which pauses the execution of the parent until the **first child process** terminates.

#### Example: Parent Process Waiting for Child to Terminate

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
    pid_t pid = fork();

    if (pid < 0) {
        // Error occurred
        perror("Fork failed");
        return 1;
    }

    if (pid == 0) {
        // Child process
        printf("Child process: Executing...\n");
        sleep(2); // Simulate some work in the child process
        printf("Child process: Done\n");
    } else {
        // Parent process
        wait(NULL); // Wait for the child process to finish
        printf("Parent process: Child has terminated\n");
    }

    return 0;
}
```

### Executing a New Program
- **`exec()`** family functions, such as `execle()`, take a path and commands as arguments. When a new program is executed, it replaces the current process image, and the remaining code in the parent does not execute.

#### Example: Using `execle()` to Replace the Current Process

```c
#include <stdio.h>
#include <unistd.h>

int main() {
    pid_t pid = fork();

    if (pid < 0) {
        // Error occurred
        perror("Fork failed");
        return 1;
    }

    if (pid == 0) {
        // Child process: Replace current process with `ls` command
        printf("Child process: Executing 'ls'\n");
        execlp("ls", "ls", "-l", (char *) NULL); // Executing 'ls -l'
        // This will not be reached if execle() is successful
        perror("execle failed");
    } else {
        // Parent process
        printf("Parent process: Waiting for child to execute\n");
        wait(NULL); // Wait for child process to finish
    }

    return 0;
}
```

### Getting Process Information
- **`getpid()`** returns the **PID** of the current process.
- **`getppid()`** returns the **PID** of the parent process.

#### Example: Using `getpid()` and `getppid()`

```c
#include <stdio.h>
#include <unistd.h>

int main() {
    printf("Current Process ID: %d\n", getpid());  // Get current process PID
    printf("Parent Process ID: %d\n", getppid());  // Get parent process PID

    return 0;
}
```

### Data Type of `fork()` Result
- The return value of `fork()` is of type **`pid_t`**:
  - A value **less than 0** indicates a **fork() failure**.
  - A value **greater than 0** represents the **PID** of the child process, with the parent process continuing its execution.

### System Commands
- **`system('pwd')`** is used to execute a shell command from within the program, in this case, to print the current working directory.

#### Example: Using `system()` to Run a Shell Command

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    printf("Executing the 'pwd' command using system():\n");
    system("pwd"); // Execute the 'pwd' command

    return 0;
}
```
