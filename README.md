# philo

Implementation of the dining philosophers problem using POSIX threads and mutexes.

## Build

```bash
make
./philo <nb_philosophers> <time_to_die> <time_to_eat> <time_to_sleep> [nb_meals]
```

```bash
./philo 5 800 200 200        # basic run
./philo 5 800 200 200 7      # stop after 7 meals each
```

Times are in milliseconds.

## How it works

Each philosopher is a thread, each fork is a mutex. A monitor in the main thread checks timestamps to detect starvation.

**Deadlock prevention:** even-index philosophers grab the right fork first, odd-index grab the left fork first. This breaks the circular wait.

**Death detection:** the monitor loops over each philosopher's last meal timestamp. If `current_time - last_meal > time_to_die`, it sets a shared death flag and prints the message. All threads check this flag before acting.

All shared state (last meal time, death flag, fork status) is mutex-protected. Timing uses `gettimeofday`.

## Edge cases

- 1 philosopher: picks up one fork, can't get the second one, dies.
- `time_to_eat > time_to_die`: philosopher dies during/after eating. Handled correctly.

## Structure

```
├── includes/          - structs and prototypes
└── srcs/mandatory/    - threads, monitor, timing, utils
```
