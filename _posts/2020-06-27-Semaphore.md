---
layout: single
title: "세마포어를 활용한 행렬곱셈 프로그램"
categories: 
   - Operating System
author_profile: true
read_time: true
comments: true
---

### Semaphore

세마포어를 활용한 행렬곱셈 프로그램을 만들었다.

> 세마포어(Semaphore)는 에츠허르 데이크스트라가 고안한, 두 개의 원자적 함수로 조작되는 정수 변수로서, 멀티프로그래밍 환경에서 공유 자원에 대한 접근을 제한하는 방법으로 사용된다. 이는 철학자들의 만찬 문제의 고전적인 해법이지만 모든 교착 상태를 해결하지는 못한다.

각각의 파일에 저장된 두 행렬의 행렬곱을 구해 파일에 저장한다. 

여러 개의 worker thread들을 생성하여 작업을 분담한다. 

각 worker thread는 자신이 구할 영역을 위해 필요한 파일을 읽고 결과 파일에 저장한다.

```c
#include <stdio.h>
#include <semaphore.h>
#include <pthread.h>
#include <stdlib.h>
#include <time.h>

#define SIZE 2048
#define div_for_nano 1000000000

typedef struct
{
    int size;
    pthread_t *threads;
} thread_pool;

typedef struct
{
    int use;
    int fill;
    int *index;
    int size;
} bounded_buffer;

void *consumer(void *arg);
void *producer(void *arg);
int get();
void put(int idx);
int readFile(char *fname, float (*array)[SIZE]);
int init_buffer(int size);
int init_pool(int size);
void calc(int y);

float A_arr[SIZE][SIZE];
float B_arr[SIZE][SIZE];
float C_arr[SIZE][SIZE];

thread_pool pool;
bounded_buffer buffer;

sem_t full;
sem_t empty;
sem_t mutex;

struct timespec tspec;
long timeDist = 0;
long totalDist = 0;

int main(int argc, char *argv[])
{
    clock_gettime(CLOCK_REALTIME, &tspec);
    totalDist = tspec.tv_sec;

    pthread_t main_thread;
    float(*array[])[SIZE] = {A_arr, B_arr};
    int bb_size = 1;
    int tp_size = 1;
    FILE *fp = NULL;

    if (argc < 4)
    {
        fprintf(stderr, "[ERROR] Insufficient factors.\n");
        exit(-1);
    }
    else if (argc == 5)
    {
        bb_size = atoi(argv[4]);
        tp_size = 1;
    }
    else if (argc > 5)
    {
        bb_size = atoi(argv[4]);
        tp_size = atoi(argv[5]);
    }
    else if (argc == 4)
    {
        bb_size == 1;
        tp_size == 1;
    }

    // init thread pool
    if (!init_pool(tp_size))
    {
        fprintf(stderr, "[ERROR] malloc error\n");
        exit(-1);
    }

    // init bounded buffer
    if (!init_buffer(bb_size))
    {
        fprintf(stderr, "[ERROR] malloc error\n");
        exit(-1);
    }

    // Data Read
    clock_gettime(CLOCK_REALTIME, &tspec);
    timeDist = tspec.tv_nsec;
    for (int idx = 1; idx < 3; ++idx)
    {
        if (readFile(argv[idx], array[idx - 1]) < 0)
        {
            fprintf(stderr, "[ERROR] File Read Failed\n");
            exit(-1);
        }
    }
    clock_gettime(CLOCK_REALTIME, &tspec);
    fprintf(stdout, "[NOTICE] Data read time : %f\n", (double)(tspec.tv_nsec - timeDist) / div_for_nano);

    // init sem
    sem_init(&mutex, 0, 1);
    sem_init(&empty, 0, buffer.size);
    sem_init(&full, 0, 0);

    // worker threads - consumer
    clock_gettime(CLOCK_REALTIME, &tspec);
    timeDist = tspec.tv_sec;
    for (int idx = 0; idx < pool.size; ++idx)
    {
        if (pthread_create(&pool.threads[idx], NULL, consumer, NULL))
        {
            fprintf(stderr, "[ERROR] pthread_create error\n");
            exit(-1);
        }
    }

    // main thread - producer
    if (pthread_create(&main_thread, NULL, producer, NULL))
    {
        fprintf(stderr, "[ERROR] pthread_create error\n");
        exit(-1);
    }

    // JOIN
    for (int idx = 0; idx < pool.size; ++idx)
    {
        if (pthread_join(pool.threads[idx], NULL))
        {
            fprintf(stderr, "[ERROR] thread_join error\n");
            exit(-1);
        }
    }

    clock_gettime(CLOCK_REALTIME, &tspec);
    fprintf(stdout, "[NOTICE] calculate time : %ld\n", tspec.tv_sec - timeDist);

    // delete
    free(pool.threads);
    free(buffer.index);

    // Data Write
    clock_gettime(CLOCK_REALTIME, &tspec);
    timeDist = tspec.tv_nsec;

    if ((fp = fopen(argv[3], "wb")) == NULL)
    {
        fprintf(stderr, "[ERROR] file write error\n");
        exit(-1);
    }
    fwrite(C_arr, sizeof(float), SIZE * SIZE, fp);
    fclose(fp);

    clock_gettime(CLOCK_REALTIME, &tspec);
    fprintf(stdout, "[NOTICE] Data write time : %f\n", (double)(tspec.tv_nsec - timeDist) / div_for_nano);

    fprintf(stdout, "[SUCCESS] ALL FINISH!\n");

    clock_gettime(CLOCK_REALTIME, &tspec);
    fprintf(stdout, "[NOTICE] total time : %ld\n", tspec.tv_sec - totalDist);

    return 0;
}

int init_pool(int size)
{
    pool.size = size;
    pool.threads = (pthread_t *)malloc(sizeof(pthread_t) * pool.size);
    if (pool.threads == NULL)
        return 0;
    return 1;
}

int init_buffer(int size)
{
    buffer.size = size;
    buffer.fill = 0;
    buffer.use = 0;
    buffer.index = (int *)malloc(sizeof(int) * buffer.size);
    if (buffer.index == NULL)
        return 0;
    return 1;
}

int readFile(char *fname, float (*array)[SIZE])
{
    FILE *fp;

    if ((fp = fopen(fname, "rb")) == NULL)
    {
        fprintf(stderr, "[ERROR] file read error\n");
        exit(-1);
    }

    fread(array, sizeof(float), SIZE * SIZE, fp);
    fclose(fp);
    return 0;
}

void put(int idx)
{
    buffer.index[buffer.fill] = idx;
    buffer.fill = (buffer.fill + 1) % buffer.size;
}

int get()
{
    if (buffer.index[buffer.use] >= SIZE)
        return -1;
    int temp = buffer.index[buffer.use];
    buffer.use = (buffer.use + 1) % buffer.size;

    return temp;
}

void *consumer(void *arg)
{
    int result = 0;
    while (1)
    {
        sem_wait(&full);
        sem_wait(&mutex);
        result = get();
        sem_post(&mutex);
        sem_post(&empty);
        if (result == -1)
            break;

        calc(result);
    }
}

void *producer(void *arg)
{
    for (int idx = 0; idx < SIZE + pool.size; ++idx)
    {
        sem_wait(&empty);
        sem_wait(&mutex);
        put(idx);
        sem_post(&mutex);
        sem_post(&full);
    }
}

void calc(int y)
{
    int x = 0;
    float sum = 0;
    for (int idx = 0; idx < SIZE; ++idx)
    {
        sum = 0;
        for (int jdx = 0; jdx < SIZE; ++jdx)
        {
            sum += A_arr[y][jdx] * B_arr[jdx][idx];
        }
        C_arr[y][idx] = sum;
    }
}
```

하나의 main thread로만 행렬 곱 연산을 수행하면 수행 시간이 오래 걸려 작업의 효율성이 떨어진다. 

이를 해결하기 위해 여러 worker thread들을 생성하고 생성한 thread들에게 main thraed는 작업할 영역의 행인 index를 배분한다.

이를 Producer-consumer relation으로 연계하여 main thread는 producer로 worker thread들은 consumer로 취급하여 진행한다.

이 때, 더 이상 처리해야 할 행이 없으면 작업종료를 지시하는 값을 전달한다.

이 후 worker thread들은 할당받은 인덱스를 통해 행렬 C에 계산한다.

Main thread가 계산된 2048 x 2048 행렬 C를 c.dat라는 파일로 쓴다.

***

producer와 consumer 관계가 잘 이해가 가지 않는다면 피자 가게로 예를 든다.

producer는 피자를 만드는 요리사이다.

consumer는 그 피자를 한 조각씩 차례를 지켜가는 소비자들이고, 해당 피자의 맛은 index맛이다.

피자가 올려지는 판은 'bounded buffer'라는 판이다.

손님들이 차례를 지키기 위해 그리고 피자를 만드는 요리사가 피자조각 위에 만든 피자조각을 올려놓는? 즉 겹쳐서 치즈가 눌러붙게되는 사태를 방지하기 위해 semaphore를 활용하여 임계영역을 지정한다.

각 손님들은 한 조각씩 자신의 테이블로 가져가 먹고 다시 접시를 들고 받으러 온다.

모든 피자조각 위의 예에서는 index맛이 2047까지 다 손님들에게 배분이 되었다면, 피자 생산을 그만하고 모든 접시들을 받아(JOIN) 설거지를 진행한다.

이러한 예를 들어 이해하면 편할거라고 생각한다.

처음 bounded buffer 개념을 배울 때, 원형 큐를 보아 피자와 producer-consumer라는 이름을 듣고 피자 가게라고 이해하면 편할거라고 생각했다.

***

## Details

가장 핵심이 되는 producer함수와 consumer함수 그리고 그 함수들이 하는 일인 put과 get에 대하여 다뤄본다.

***

우선 피자를 만드는 요리사인 producer이다.

하는 일은 피자를 올려놓는 put이다.

```c
void *producer(void *arg)
{
    for (int idx = 0; idx < SIZE + pool.size; ++idx)
    {
        sem_wait(&empty);
        sem_wait(&mutex);
        put(idx);
        sem_post(&mutex);
        sem_post(&full);
    }
}

void put(int idx)
{
    buffer.index[buffer.fill] = idx;
    buffer.fill = (buffer.fill + 1) % buffer.size;
}
```

피자를 올려 놓는다면 피자판에 빈 공간이 줄게된다.

empty는 빈 피자판의 공간을 나타내는 세마포어 변수로 최초에는 모두 비어있는 상태라 피자판의 크기로 초기화한다.

sem_wait(&empty)로 인해 피자를 올려두면 이 변수가 1 감소하고, 만약 empty가 감소하여 -1이 된다면 해당 쓰레드는 sleep하게 된다.

1로 초기화를 해둔 mutex를 sem_wait(&mutex)를 통해 임계영역으로 락을 걸고 들어간 후 producer가 하는 일인 put함수(피자조각을 놓는다.)에 진입한다.

> 임계 구역(critical section) 또는 공유변수 영역은 병렬컴퓨팅에서 둘 이상의 스레드가 동시에 접근해서는 안되는 공유 자원(자료 구조 또는 장치)을 접근하는 코드의 일부를 말한다. 임계 구역은 지정된 시간이 지난 후 종료된다. 때문에 어떤 스레드(태스크 또는 프로세스)가 임계 구역에 들어가고자 한다면 지정된 시간만큼 대기해야 한다. 스레드가 공유자원의 배타적인 사용을 보장받기 위해서 임계 구역에 들어가거나 나올때는 세마포어 같은 동기화 매커니즘이 사용된다.

put함수는 전형적인 원형 큐 문제의 삽입 함수라고 생각하면 편하다.

채워질 위치를 가리키는 포인터 부분에 인자로 입력받은 idx를 원형 큐에 즉 피자판에 채워 놓는다.

이 후 다음 채워질 위치를 가리키는 포인터를 원형 큐 형식에 맞게 이동시킨다.

put함수가 종료되고, 다시 producer로 돌아와 세마포어 변수 mutex를 sem_post(&mutex)를 통해 임계영역을 나오며 락을 해제한다.

피자를 올려 놓았다면 피자판의 찬 공간이 늘게된다.

full은 피자판에서 찬 공간을 나타내는 세마포어 변수로 최초에는 모두 비어있는 상태라 0으로 초기화한다.

sem_post(&full)로 인해 피자를 올려두면 이 변수(full)가 1 증가한다.

***

다음은 피자를 받아가는 손님인 consumer이다.

하는 일은 피자를 받아가는 get이다.

```c
void *consumer(void *arg)
{
    int result = 0;
    while (1)
    {
        sem_wait(&full);
        sem_wait(&mutex);
        result = get();
        sem_post(&mutex);
        sem_post(&empty);
        if (result == -1)
            break;

        calc(result);
    }
}

int get()
{
    if (buffer.index[buffer.use] >= SIZE)
        return -1;
    int temp = buffer.index[buffer.use];
    buffer.use = (buffer.use + 1) % buffer.size;

    return temp;
}
```

피자를 가져 간다면 에 찬 공간이 줄게된다.

full은 찬 피자판의 공간을 나타내는 세마포어 변수로 최초에는 모두 비어있는 상태라 0으로 초기화한다.

sem_wait(&full)로 인해 피자를 가져가면 이 변수가 1 감소하고, 만약 full이 감소하여 -1이 된다면 해당 쓰레드는 sleep하게 된다.

1로 초기화를 해둔 mutex를 sem_wait(&mutex)를 통해 임계영역으로 락을 걸고 들어간 후 consumer가 하는 일인 get함수(피자조각을 가져간다.)에 진입한다.

get함수는 전형적인 원형 큐 문제의 삭제 함수라고 생각하면 편하다.

사용할 위치를 가리키는 포인터 부분에 해당하는 피자 조각을 임시 변수에 넣어두고, 이 후 다음 사용할 위치를 가리키는 포인터를 원형 큐 형식에 맞게 이동시킨다.

임시 변수에 넣어둔 피자 조각(index)를 반환한다.

put과 달리 가져가는 피자 조각의 index가 최초로 지정한 2048보다 크거나 같아질 경우 비정상적인 값을 반환하여 영업이 종료됨을 알린다.

get함수가 종료되고, 다시 consumer로 돌아와 세마포어 변수 mutex를 sem_post(&mutex)를 통해 임계영역을 나오며 락을 해제한다.

피자를 가져간다면 피자판의 빈 공간이 늘게된다.

empty는 피자판에서 빈 공간을 나타내는 세마포어 변수로 최초에는 모두 비어있는 상태라 피자판의 크기로 초기화한다.

sem_post(&empty)로 인해 피자를 가져가면 이 변수(empty)가 1 증가한다.
