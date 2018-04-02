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


