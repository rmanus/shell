# shell commands

### Get GDB backtrace for all threads

```
thread apply all bt
```

### Finds the 10 largest files on your current filesystem

```
find / -xdev -type f -exec du -Sh {} + | sort -rh | head -n 10
```

### Memory usage statistics for each running Docker container on the system
```
for c in `docker container ls --format "{{.Names}}"`; do echo -n "$c => " ; for i in `docker top $c | awk '{print $2}' | grep -v PID`; do pmap $i | tail -1 | awk '{print $2}' | rev | cut -c2- | rev | xargs bash -c 'echo $(($0 * 1024))'; done | paste -sd+ - | bc | numfmt --to=iec; done
```

This shell command is a complex one-liner that retrieves memory usage statistics for each running Docker container on the system. Let's break it down step by step:

1. `docker container ls --format "{{.Names}}"`: This command lists the names of all running Docker containers. The `--format` option specifies the format in which the container names are displayed, using the Go template syntax (`{{.Names}}` retrieves the container names).

2. `for c in `docker container ls --format "{{.Names}}"`; do ...; done`: This loop iterates over each container name obtained from the previous step and performs the following actions for each container.

3. `docker top $c | awk '{print $2}' | grep -v PID`: The `docker top` command shows the processes running inside the container. This part of the command retrieves the process IDs (PIDs) of those processes using `awk` and then filters out the "PID" header using `grep -v PID`.

4. `pmap $i | tail -1 | awk '{print $2}' | rev | cut -c2- | rev | xargs bash -c 'echo $(($0 * 1024))'`: For each PID obtained in the previous step, `pmap` command is used to display the memory map of the process. The `tail -1` command extracts the last line of the `pmap` output. Then, a series of `awk`, `rev`, and `cut` commands are used to extract the memory usage value in kilobytes. The extracted value is then multiplied by 1024 (to convert it to bytes) using the arithmetic expansion `$(($0 * 1024))`.

5. `paste -sd+ - | bc`: This part of the command takes all the memory usage values obtained from the previous step, separates them with "+" symbols using `paste`, and then passes the resulting expression to `bc` (basic calculator) to calculate the sum of all memory usage values.

6. `numfmt --to=iec`: The `numfmt` command is used to format the memory sum calculated in the previous step into a human-readable form using the IEC (International Electrotechnical Commission) standard.

7. `echo -n "$c => "`: This part of the command prints the container name followed by "=> " without a newline.

Putting it all together, the command displays the memory usage of each running Docker container on the system in a human-readable format, showing the container name followed by its memory usage in bytes (formatted using IEC standards).

Note: Be cautious while running complex shell commands like this as they can have unintended consequences, especially when it comes to Docker containers and resource-intensive operations. Always ensure you understand what a command does before executing it on your system.
