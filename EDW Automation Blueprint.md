Airflow · Snowflake · Teradata · S3

# Config in. *Evidence out.*

How the test automation repo is put together, in plain words. You will see every file, what goes in it, what to build first, and what happens from the moment you type the command to the moment the report is ready.

[The words](#words)[Every file](#files)[One run](#run)[Where things go](#goes)[Lookup list](#lookup)[Different tables](#variants)[Settings merge](#config)[Build order](#build)

1. ### Settings files

   Text files that say which table, which environment and which tasks.
2. ### Task runner

   Reads the settings and takes the task names one at a time.
3. ### Lookup list

   Finds the code that matches each task name, then runs it.
4. ### Report

   Every task attaches its proof, so the team can open one page and see what happened.

Start here

## The words, in plain English

The repo uses a few technical names. Each one is a simple idea. Keep this list in mind and the rest of the page will read easily.

The repo

## Every file, and what goes in it

This is the whole folder, including registry.py. The small number beside each file is the build stage when you write it. Pick a stage to see only those files, or click any file to see what it does and an example of its content.

How it behaves

## Follow one run from start to finish

Here is a real example: testing the orders table in QAT. Step through the ten moments. On the right you can see what the backpack holds and what proof has been collected so far.

What it looks like

```

```

The backpack

Proof collected for the report

Day-to-day use

## I want to do this. Where does it go?

Once the repo exists, most changes touch only one or two files. Choose what you want to do.

I want to

The lookup list

## How a task name finds its code

The settings only contain names such as trigger_dag. The lookup list, which lives in steps/registry.py, pairs every name with the function that does the work. Click a name to follow one lookup, or let it play.

1 · Name in the settings

→

2 · Lookup list (STEPS)

→

3 · Code that runs

steps/dag_steps.py

```

```

steps/registry.py (the whole file)

```

```

steps/\_\_init\_\_.py

```

```

steps/dag_steps.py (a task file)

```

```

**The one thing beginners miss**

A task only gets onto the lookup list when its file is imported, because that is when its @step line runs. That is why steps/\_\_init\_\_.py imports every task file. If you add a new task file and forget the import, its names will be reported as unknown.

Different tables

## Different tasks for different tables, and starting in the middle

About 50 tables, two kinds of flow. Forty tables share the standard task list. Ten file-based tables add two tasks at the front. Each table names its list and inherits the rest. A table with unusual needs can also carry its own list.

Tasks in this list, in order

All 50 tables

standard · 40 tablesfile-based · 10 tables

## Try starting from a later task

Pick a table and a starting task. Earlier tasks are skipped. If you skip trigger_dag but still need monitor_dag, you must pass the run id, because the note that the trigger task would have left in the backpack does not exist. This is a simulation with example values.

Table

Environment

Start from task

Make a task fail

Run id of the existing DAG run

Command

```

```

Notes in the backpack after the run

Empty until tasks write to it.

Settings

## How the settings are put together for each run

Four sources are merged in a fixed order and later ones win. Change the environment or the table and watch which values move and where each one came from.

--env

--dataset

--from-step [ ] validate_schema_staging

env file→ task list→ table entry→ command flaglater sources win

| Setting | Value used | Comes from |
| --- | --- | --- |

What to define first

## Seven stages, each one runnable

Build in this order. After each stage there is a simple check you can run, so you always know the foundation is sound before you add the next layer. Tick stages as you finish them. Ticks stay in this browser only.

0 of 7 done

### Two assumptions to confirm

- The Snowflake schema called prompt is a placeholder. Replace it with the real name in both env files.
- For file-based tables, "place the file" is read as: put the file at a landing location, then load it into the staging table. The example uses S3 for the landing step. If your real mechanism is different, only file_steps.py and its client change.