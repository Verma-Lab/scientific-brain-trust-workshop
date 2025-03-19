# GNU Parallel
- **Parallelize** - This refers to the process of dividing a workload into multiple smaller tasks and executing them simultaneously. It allows for better utilization of computational resources, such as multi-core CPUs or multiple nodes in a cluster
- **Massive**: Indicates the scale or volume of tasks being handled. It often refers to workloads that consist of hundreds, thousands, or even millions of tasks, which would take a long time to process sequentially
- **Individual**: Each task operates independently of the others. This means there’s no dependency between tasks, allowing them to be executed in parallel without waiting for one another
- **Task**: These are the units of work or commands you want to execute. Each task could be a program, script, or function that processes a specific set of inputs.

## Getting Started
- download latest from gnu parallel ftp: http://ftp.gnu.org/gnu/parallel/

```sh
# on LPC
module load parallel
```

## Serial Application
```sh
# serial application sumbitted to gnu-parallel
$parallel "$WDIR/my_command.sh {} {/.} $N_THREADS" :::: $WDIR/input_list.txt
{} = pull out one from input file
{/.} = just grab basename
{.} = grab everything
```

## parallel syntax
```sh
# parallel command arguments on CLI:
parallel [OPTIONS] CMD {} ::: FILELIST
# parallel CMD arguments from file
parallel [OPTIONS] CMD {} ::: FILELIST
parallel -a FILELIST [OPTIONS] CMD {}
```

- where `FILELIST` contains:

```sh
cat input_file_list.txt

/project/my_project/data/input001.faa
/project/my_project/data/input002.faa
...
/project/my_project/data/input200.faa
```

- replace a for loop with parallel
```sh
# for loop
for i in *gz; do 
  zcat $i > $(basename $i .gz).unpacked
done
# in parallel:
parallel 'zcat {} > {.}.unpacked' ::: *.gz
# The {} will be substituted with arguments following the separator ':::'
parallel CMD -input prefix{}suffix ::: *.fastas
# piping the input
parallel 'read_fasta -i {} | extract_seq -l 5 | write_fasta -o {.}_trim.fasta -x' ::: *.fasta
```

## Manipulating input string
```sh
# Default string {}
parallel echo {} ::: path/input1.fa --> path/input1.fa
# Remove Extension {.}
parallel echo {.} ::: path/input1.fa --> path/input1
# BASENAME (Remove Path)
parallel echo {/} ::: path/input1.fa --> input1.fa
# Remove Path and Extension {/.}
parallel echo {/.} ::: path/input1.fa --> input1
# change extention and path
parallel echo output/{/.}.out ::: path/input1.fa --> output/input1.out
```

| OPTION                                              | DESCRIPTION                                                                                          |
| --------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| `--jobs N (-j N)`                                   | number of jobs/node; 0=many as possible; default=1job/core                                           |
| `--sshloginfile $PBS_NODEFILE (-slf $PBS_NODEFILE)` | used when more than 1 node requested/job                                                             |
| `--workdir (-wd)`                                   | working directory. Use `$PBS_O_WORKDIR` for DIR script was called from                               |
| `--progress`                                        | show overall computational progress for each subtask                                                 |
| `--joblog`                                          | generates logfile of each completed subtask; use `--resume` to finish unfinished tasks               |
| `--resume --joblog job.log`                         | use to resume unfinished tasks; NOTE: if you need to resume a job from start, delete old joblog file |
| `--timeout secs`                                    | job gets terminated if command runs longer than seconds                                              |


## Submitting with a TASKFILE
```sh
# Reading command arguments on the command line:
parallel [OPTIONS] COMMAND {} ::: TASKLIST
parallel echo {} ::: A B
parallel echo {1} {2} ::: A B ::: C D
# A C; A D; B C; B D
parallel --link echo {1} {2} ::: A B ::: C D
# A C; B D
parallel --link app-xxx {1} {2} ::: a1 a2 ::: b1 b2
# app-xxx a1 b1; app-xxx a2 b2
```