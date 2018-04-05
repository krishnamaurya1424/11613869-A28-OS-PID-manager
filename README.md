/*# 11613869-A28-OS-PID-manager
 Programming Exercise 3.20 required you to design a PID manager that allocated a unique process identiﬁer to each process.
 Exercise 4.20 required you to modify your solution to Exercise 3.20 by writing a program that created a number of threads 
 that requested and released process identiﬁers. Now modify your solution to Exercise 4.20 by ensuring that the data structure
 used to represent the availability of process identiﬁers is safe from race conditions. Use Pthreads mutex locks.*/
 
#include<stdio.h>
#include <pthread.h>
#include<unistd.h>
#include <windows.h>                        //for working of Sleep() function in Dev c++

#define MIN_PID 300
#define MAX_PID 5000

int VarOfThread = 0;
pthread_mutex_t l;

struct Struct_pid
{
    int VarOfPid;
    bool ProcessBit;
}ArrForPid[4700];

int allocate_map(void)                      //For Creating & initialising data structure for represent pid(Ex 3.20)            
{
    int i,j;
    for(i = MIN_PID, j =0; i <= MAX_PID; i++, j++)
    {
        ArrForPid[j].VarOfPid= i;
        ArrForPid[j].ProcessBit = 0;
    }

    if(i == MAX_PID && j == 4700)
    return 1;
	else
    return -1;
}

int allocate_pid(void)                            //For allocate and return pid(Ex 3.20)      
{
    for(int i = MIN_PID, j =0; i <= MAX_PID; i++, j++)
    {
        if(ArrForPid[j].ProcessBit == 0)
        {
            ArrForPid[j].VarOfPid= i;
            ArrForPid[j].ProcessBit = 1;
            return i;
            break;
        }
    }

    return -1;
}

void release_pid(int pid)                               //For releasing pid(Ex 3.20)
{
    for(int i = 0; i <= 4700; i++)
    {
        if(ArrForPid[i].VarOfPid == pid)
        {
            ArrForPid[i].ProcessBit = 0;
        }
    }
}

/* For threads the value of VarOfThread is incremented by 1 and exits and process continue for other threads again and exits. 
For every execution there is mutex_lock and mutex_unlock. */

void * AccessThread(void* voidA)                          //created thread call this function(Ex 4.20)
{
    int pro = allocate_pid();       
    while (VarOfThread < 100)
    {
        pthread_mutex_lock(&l);                           //(Ex 5.36)
        if (VarOfThread >= 100)
        {
            pthread_mutex_unlock(&l);
            break;
        }

        VarOfThread++;                    
        Sleep(100);
        printf("\n %d",VarOfThread);
        printf("\n %d",ArrForPid[VarOfThread].VarOfPid);
        pthread_mutex_unlock(&l);                           
    }
    Sleep(5);
    release_pid(pro);                                        //pid gets released
}

int main()
{
	allocate_map();
	allocate_pid();
	release_pid(50);
	
    int i =0;
	pthread_t thread[100];
    printf("\n In output screen 100 threads created. Every thread will print the values and increment it by 1 with a delay for each process execution");
    Sleep(1000);       

    for(i = 0; i < 100; i++)
    {
        pthread_mutex_init(&l, NULL);
        pthread_create(&thread[i], NULL, AccessThread, NULL);
        AccessThread(NULL);
    }

    for(i = 0; i < 100; i++)
    {
        pthread_join(thread[i], NULL);
        pthread_mutex_destroy(&l);
    }
	return 0;
	
}



