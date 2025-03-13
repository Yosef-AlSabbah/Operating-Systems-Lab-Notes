# Operating Systems Lab Repository

## Table of Contents
1. [Description](#description)
2. [Lecture 02: Linux Commands](#lecture-02-linux-commands)
   - [Directory Management](#directory-management)
   - [File Management](#file-management)
   - [Text Processing](#text-processing)
3. [Lecture 03: IO System Calls](#lecture-03-io-system-calls)
   - [File Creation](#file-creation)
   - [File Opening](#file-opening)
   - [File Reading](#file-reading)
   - [File Writing](#file-writing)
   - [File Seeking](#file-seeking)
   - [File Status](#file-status)
   - [Directory Operations](#directory-operations)
4. [Lecture 04: Process in OS](#lecture-04-process-in-os)
   - [Process Creation with `fork()`](#process-creation-with-fork)
   - [Process Identification](#process-identification)
   - [Executing Commands with `exec()`](#executing-commands-with-exec)
   - [Shell Command Execution with `system()`](#shell-command-execution-with-system)
5. [References](#references)

---

## Description
This repository contains explanations, notes, and code examples from the Operating Systems Lab lectures. It covers key concepts such as process creation, process synchronization, system calls, and practical coding exercises. The repository is organized by lecture topics, with each section providing detailed explanations, example code, and execution instructions.

---
## Lecture 02: Linux Commands

### Directory Management

#### `mkdir`
- **Usage**: Creates a new directory.
- **Example**:
  ```bash
  mkdir mydir
  ```
- **Create Multiple Directories**:
  ```bash
  mkdir dir1 dir2 dir3
  ```

#### `mkdir -p`
- **Usage**: Creates nested directories (parent directories if they don’t exist).
- **Example**:
  ```bash
  mkdir -p parent/child/grandchild
  ```

---

### File Management

#### `touch`
- **Usage**: Creates an empty file or updates the timestamp of an existing file.
- **Example**:
  ```bash
  touch file.txt
  ```

#### `echo`
- **Usage**: Writes text to a file.
- **Overwrite File**:
  ```bash
  echo 'Hello' > file.txt
  ```
- **Append to File**:
  ```bash
  echo 'World' >> file.txt
  ```

#### `cp`
- **Usage**: Copies files or directories.
- **Example**:
  ```bash
  cp file.txt copy.txt
  ```
- **Copy Directory**:
  ```bash
  cp -r dir1 dir2
  ```

#### `mv`
- **Usage**: Moves or renames files or directories.
- **Example**:
  ```bash
  mv file.txt newfile.txt
  ```

#### `rm`
- **Usage**: Removes files or directories.
- **Example**:
  ```bash
  rm file.txt
  ```
- **Remove Directory**:
  ```bash
  rm -r dir1
  ```
- **Interactive Removal**:
  ```bash
  rm -ri dir1
  ```

#### `rmdir`
- **Usage**: Removes an empty directory.
- **Example**:
  ```bash
  rmdir emptydir
  ```

---

### Text Processing

#### `cat`
- **Usage**: Displays the contents of a file.
- **Example**:
  ```bash
  cat file.txt
  ```
- **Pipe with `tr`**:
  ```bash
  cat file.txt | tr a-z A-Z > output.txt
  ```

#### `wc`
- **Usage**: Counts lines, words, and characters in a file.
- **Example**:
  ```bash
  wc file.txt
  ```

#### `head`
- **Usage**: Displays the first few lines of a file.
- **Example**:
  ```bash
  head -3 file.txt
  ```

#### `tail`
- **Usage**: Displays the last few lines of a file.
- **Example**:
  ```bash
  tail -3 file.txt
  ```

#### `more`
- **Usage**: Views file content page by page.
- **Example**:
  ```bash
  more file.txt
  ```

#### `grep`
- **Usage**: Searches for a pattern in a file.
- **Example**:
  ```bash
  grep 'hello' file.txt
  ```

---

### Text Editors

#### `vi`
- **Usage**: Opens the `vi` text editor.
- **Example**:
  ```bash
  vi file.txt
  ```

#### `nano`
- **Usage**: Opens the `nano` text editor.
- **Example**:
  ```bash
  nano file.txt
  ```

---
## Lecture 03: IO System Calls

### File Creation
- **System Call**: `creat()`
- **Usage**: Creates a new file or truncates an existing file.
- **Input**: 
  - `filename`: Name of the file (e.g., `filename.txt`).
  - `mode`: File permissions (e.g., `S_READ | S_IWRITE`).
- **Output**: 
  - `fd`: File descriptor (a non-negative integer). Returns `-1` if an error occurs.

**Example Code**:
```c
#include <fcntl.h>
#include <sys/stat.h>

int main() {
    int fd = creat("filename.txt", S_IRUSR | S_IWUSR);
    if (fd == -1) {
        perror("Error creating file");
        return 1;
    }
    printf("File created successfully with fd: %d\n", fd);
    close(fd);
    return 0;
}
```

---

### File Opening
- **System Call**: `open()`
- **Usage**: Opens a file and returns a file descriptor.
- **Input**: 
  - `filename`: Name of the file (e.g., `filename.txt`).
  - `flags`: Mode of opening (e.g., `O_RDONLY | O_CREAT`).
- **Output**: 
  - `fd`: File descriptor (a non-negative integer). Returns `-1` if an error occurs.

**Example Code**:
```c
#include <fcntl.h>

int main() {
    int fd = open("filename.txt", O_RDONLY | O_CREAT, 0644);
    if (fd == -1) {
        perror("Error opening file");
        return 1;
    }
    printf("File opened successfully with fd: %d\n", fd);
    close(fd);
    return 0;
}
```

---

### File Reading
- **System Call**: `read()`
- **Usage**: Reads data from an open file.
- **Input**: 
  - `fd`: File descriptor returned by `open()`.
  - `buffer`: Pointer to a buffer where the data will be stored.
  - `count`: Number of bytes to read.
- **Output**: 
  - Number of bytes read. Returns `-1` if an error occurs.

**Example Code**:
```c
#include <fcntl.h>
#include <unistd.h>

int main() {
    int fd = open("filename.txt", O_RDONLY);
    if (fd == -1) {
        perror("Error opening file");
        return 1;
    }

    char buffer[100];
    ssize_t bytes_read = read(fd, buffer, 12);
    if (bytes_read == -1) {
        perror("Error reading file");
        close(fd);
        return 1;
    }
    printf("Read %zd bytes: %.*s\n", bytes_read, (int)bytes_read, buffer);
    close(fd);
    return 0;
}
```

---

### File Writing
- **System Call**: `write()`
- **Usage**: Writes data to an open file.
- **Input**: 
  - `fd`: File descriptor returned by `open()`.
  - `buffer`: Pointer to the data to be written.
  - `count`: Number of bytes to write.
- **Output**: 
  - Number of bytes written. Returns `-1` if an error occurs.

**Example Code**:
```c
#include <fcntl.h>
#include <unistd.h>

int main() {
    int fd = open("filename.txt", O_WRONLY | O_CREAT, 0644);
    if (fd == -1) {
        perror("Error opening file");
        return 1;
    }

    const char *text = "Hello, World!";
    ssize_t bytes_written = write(fd, text, 13);
    if (bytes_written == -1) {
        perror("Error writing to file");
        close(fd);
        return 1;
    }
    printf("Wrote %zd bytes\n", bytes_written);
    close(fd);
    return 0;
}
```

---

### File Seeking
- **System Call**: `lseek()`
- **Usage**: Moves the file pointer to a specific position.
- **Input**: 
  - `fd`: File descriptor returned by `open()`.
  - `offset`: Number of bytes to move.
  - `whence`: Starting point (`SEEK_SET`, `SEEK_CUR`, or `SEEK_END`).
- **Output**: 
  - New file pointer position. Returns `-1` if an error occurs.

**Example Code**:
```c
#include <fcntl.h>
#include <unistd.h>

int main() {
    int fd = open("filename.txt", O_RDWR);
    if (fd == -1) {
        perror("Error opening file");
        return 1;
    }

    off_t offset = lseek(fd, 0L, SEEK_SET);
    if (offset == -1) {
        perror("Error seeking file");
        close(fd);
        return 1;
    }
    printf("File pointer moved to offset: %ld\n", offset);
    close(fd);
    return 0;
}
```

---

### File Status
- **System Call**: `stat()`
- **Usage**: Retrieves information about a file.
- **Input**: 
  - `filename`: Name of the file (e.g., `file.txt`).
  - `stat_buff`: Pointer to a `struct stat` object.
- **Output**: 
  - `0` on success, `-1` on error.

**Example Code**:
```c
#include <sys/stat.h>
#include <stdio.h>

int main() {
    struct stat stat_buff;
    if (stat("file.txt", &stat_buff) == -1) {
        perror("Error getting file status");
        return 1;
    }
    printf("File size: %ld bytes\n", stat_buff.st_size);
    return 0;
}
```

---

### Directory Operations
- **System Calls**: `opendir()`, `readdir()`, `closedir()`
- **Usage**: Opens, reads, and closes a directory.
- **Input**: 
  - `dirname`: Name of the directory.
- **Output**: 
  - Directory stream (`DIR *`).

**Example Code**:
```c
#include <stdio.h>
#include <dirent.h>
#include <stdlib.h>

int main() {
    char dirname[100];
    printf("Enter Directory Name: ");
    scanf("%s", dirname);

    DIR *dirp = opendir(dirname);
    if (dirp == NULL) {
        perror("Error opening directory");
        return 1;
    }

    struct dirent *dptr;
    while ((dptr = readdir(dirp)) != NULL) {
        printf("%s\n", dptr->d_name);
    }

    closedir(dirp);
    return 0;
}
```

---

## Lecture 04: Process in OS

### Process Creation with `fork()`
- **System Call**: `fork()`
- **Usage**: Creates a new process by duplicating the parent process.
- **Output**: 
  - `0` in the child process.
  - `> 0` (child's PID) in the parent process.
  - `-1` if an error occurs.

**Example Code**:
```c
#include <stdio.h>
#include <unistd.h>

int main() {
    pid_t pid = fork();
    if (pid < 0) {
        perror("Fork failed");
        return 1;
    } else if (pid == 0) {
        printf("Child process (PID: %d)\n", getpid());
    } else {
        printf("Parent process (PID: %d, Child PID: %d)\n", getpid(), pid);
    }
    return 0;
}
```

---

### Process Identification
- **System Calls**: `getpid()`, `getppid()`
- **Usage**: Retrieves the process ID of the current process and its parent.
- **Output**: 
  - `getpid()`: Current process ID.
  - `getppid()`: Parent process ID.

**Example Code**:
```c
#include <stdio.h>
#include <unistd.h>

int main() {
    printf("Current PID: %d\n", getpid());
    printf("Parent PID: %d\n", getppid());
    return 0;
}
```

---

### Executing Commands with `exec()`
- **System Call**: `exec()`
- **Usage**: Replaces the current process image with a new one.
- **Input**: 
  - `path`: Path to the executable.
  - `args`: Command-line arguments.
- **Output**: 
  - Does not return on success. Returns `-1` if an error occurs.

**Example Code**:
```c
#include <stdio.h>
#include <unistd.h>

int main() {
    execl("/bin/ls", "ls", "-l", NULL);
    perror("Exec failed");
    return 1;
}
```

---

### Shell Command Execution with `system()`
- **System Call**: `system()`
- **Usage**: Executes a shell command.
- **Input**: 
  - `command`: Shell command (e.g., `"pwd"`).
- **Output**: 
  - Returns the exit status of the command.

**Example Code**:
```c
#include <stdlib.h>

int main() {
    int status = system("pwd");
    if (status == -1) {
        perror("System command failed");
        return 1;
    }
    return 0;
}
```

---

## References
1. **Instructor's Lectures**:
   - Lecture #02: Linux Commands
   - Lecture #03: IO System Calls
   - Lecture #04: Process in OS
   - These lectures are the primary source of the content and examples provided in this repository.

3. **External Resources**:
   - [Linux Programmer's Manual](https://man7.org/linux/man-pages/): Official documentation for Linux system calls and functions.
   - Advanced Programming in the UNIX Environment by W. Richard Stevens: A comprehensive book on UNIX system programming.
   - [GNU C Library (glibc) Documentation](https://www.gnu.org/software/libc/manual/): Official documentation for the GNU C Library.
   - [The Linux Kernel Documentation](https://www.kernel.org/doc/html/latest/): Documentation for the Linux kernel, including system calls and process management.
