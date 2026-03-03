# philo

Five philosophers, five forks, one table. No deadlock, no starvation, no semaphores — just threads, mutexes, and careful timing.

## Quick Start

```bash
make
./philo <nb_philosophers> <time_to_die> <time_to_eat> <time_to_sleep> [nb_meals]
```

```bash
# 5 philosophers die if they go 800ms without eating, eat for 200ms, sleep for 200ms
./philo 5 800 200 200

# stop after each philosopher has eaten at least 7 times
./philo 5 800 200 200 7
```

All times are in milliseconds.

## How it works

**Threading model**

Each philosopher is a POSIX thread. Each fork is a mutex. The monitor runs in the main thread, polling timestamps to detect starvation.

**Deadlock prevention**

Without intervention, philosophers can deadlock: everyone picks up their left fork and waits forever for their right. The fix: philosophers with an even index pick up the right fork first, odd-index philosophers pick up the left fork first. This breaks the circular wait condition without requiring a central coordinator.

**Death detection**

The monitor checks each philosopher's last-meal timestamp on every iteration. When a timestamp exceeds `time_to_die`, the monitor sets a shared `dead` flag and prints the death message. All threads check this flag before their next action and exit cleanly.

**No data races**

Every shared variable — last meal time, death flag, fork availability — is protected by a mutex. The monitor reads timestamps under lock. `gettimeofday` is used for millisecond precision.

## Edge cases handled

- A single philosopher: picks up one fork, waits, dies. No deadlock — just physics.
- `time_to_eat > time_to_die`: philosopher dies immediately after first meal. Handled correctly.

## Project structure

```
├── includes/          — t_philo and t_data structs, prototypes
└── srcs/
    └── mandatory/     — philosopher threads, monitor, timing, utils
```
