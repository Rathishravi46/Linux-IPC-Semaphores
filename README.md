# EXPERIMENT 05 LINUX IPC SEMAPHORES
## DEVELOPED BY : RATHISH R
## REGISTER NO. : 212224240132
# AIM:
To Write a C program that implements a producer-consumer system with two processes using Semaphores.
# DESIGN STEPS:
### Step 1:
Navigate to any Linux environment installed on the system or installed inside a virtual environment like virtual box/vmware or online linux JSLinux (https://bellard.org/jslinux/vm.html?url=alpine-x86.cfg&mem=192) or docker.
### Step 2:
Write the C Program using Linux Process API - Sempahores
### Step 3:
Execute the C Program for the desired output. 
# PROGRAM:
## Write a C program that implements a producer-consumer system with two processes using Semaphores.
```
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/ipc.h>
#include <sys/sem.h>
#include <sys/wait.h>

int main() {
    int semid = semget(IPC_PRIVATE, 1, 0600);
    if (semid == -1) { perror("semget"); exit(1); }
    printf("Semaphore created, id = %d\n", semid);

    union semun { int val; } arg;
    arg.val = 0;
    semctl(semid, 0, SETVAL, arg);

    if (fork() == 0) { // Child = consumer
        for (int i = 0; i < 10; i++) {
            struct sembuf p = {0, -1, 0}; // wait
            semop(semid, &p, 1);
            printf("consumer: %d\n", i);
            fflush(stdout);
        }
        exit(0);
    } else { // Parent = producer
        for (int i = 0; i < 10; i++) {
            printf("producer: %d\n", i);
            fflush(stdout);
            struct sembuf v = {0, 1, 0}; // signal
            semop(semid, &v, 1);
            usleep(100000); // small delay
        }
        wait(NULL);
        semctl(semid, 0, IPC_RMID, arg);
        printf("Semaphore removed.\n");
    }
}
```
## OUTPUT
$ ./sem.o 

![5a](https://github.com/user-attachments/assets/7af8c51d-4acc-48f4-9d47-77bf4c72849f)

$ ipcs

![5b](https://github.com/user-attachments/assets/58451a53-f974-475a-a9bc-6b4a25d7bf67)

# RESULT:
The program is executed successfully.
