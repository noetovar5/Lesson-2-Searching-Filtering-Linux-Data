# Lesson-2-Searching-Filtering-Linux-Data
Week 1 — Lesson 2: Searching &amp; Filtering Linux Data




Exactly right. **That is the concept I wanted you to understand.**

`diff` answers:

> **“Are these two files different?”**

It does **not** answer:

> **“Is the information inside these files correct?”**

So two completely wrong files could still produce no output from `diff` if they're identical.

**Week 1 — Lesson 1: COMPLETE. ✅**

You’ve demonstrated the skill rather than just reading about it, which is exactly how I want us to approach RHCSA.

## Week 1 — Lesson 2: Searching & Filtering Linux Data

Now we're going to learn a skill you'll use constantly as a Linux administrator: taking lots of output and finding exactly what you need.

Today we'll work with:

```text
cat      Display file contents
less     Read large files page-by-page
head     Show beginning of a file
tail     Show end of a file
grep     Search for matching text
|        Send output from one command into another
>        Redirect/overwrite output
>>       Append output
*        Wildcard
?        Single-character wildcard
```

### Part 1 — Build our lab

Start here:

```bash
cd /root/rhcsa-labs/week1
mkdir -p lesson2/logs
cd lesson2
pwd
```

You should end at:

```text
/root/rhcsa-labs/week1/lesson2
```

Now we'll create a small pretend server log:

```bash
cat > logs/application.log <<'EOF'
INFO WebServer started
INFO Database connected
WARNING Disk usage 75 percent
INFO User noe logged in
ERROR Database connection lost
INFO Database reconnecting
INFO Database connected
WARNING Memory usage high
ERROR Backup failed
INFO Backup retry started
INFO Backup completed
ERROR Authentication failure
INFO User admin logged in
WARNING CPU usage high
INFO System healthy
EOF
```

Verify:

```bash
cat logs/application.log
```

Don't worry about the `EOF` construction yet. It is simply an efficient way of creating a multiline file.

---

## Part 2 — `head` and `tail`

Run:

```bash
head logs/application.log
```

By default, `head` displays the first **10 lines**.

But I can specify exactly how many:

```bash
head -n 5 logs/application.log
```

Think:

> **HEAD = top of the file.**

Now:

```bash
tail logs/application.log
```

And:

```bash
tail -n 5 logs/application.log
```

Think:

> **TAIL = bottom of the file.**

This becomes extremely useful with logs.

For example, on a RHEL server you might investigate:

```bash
tail /var/log/messages
```

Or monitor a file continuously:

```bash
tail -f /var/log/messages
```

`-f` means **follow**.

You stop `tail -f` with:

```text
Ctrl+C
```

That's a command worth remembering for real-world administration.

---

# Part 3 — `grep`

Here's where things get much more powerful.

Our log contains:

```text
INFO
WARNING
ERROR
```

Suppose I only care about errors.

Run:

```bash
grep ERROR logs/application.log
```

You should get:

```text
ERROR Database connection lost
ERROR Backup failed
ERROR Authentication failure
```

You just searched an entire file for a particular pattern.

Now try:

```bash
grep WARNING logs/application.log
```

You should see the warnings.

Try:

```bash
grep Database logs/application.log
```

Now Linux shows every line containing `Database`.

### Case sensitivity

Linux is generally case-sensitive.

This:

```bash
grep database logs/application.log
```

may not find:

```text
Database
```

But:

```bash
grep -i database logs/application.log
```

means:

> Search while ignoring uppercase/lowercase differences.

That's a very useful option.

---

# Part 4 — Count your matches

Suppose I don't need to see the errors. I just want to know **how many** there are.

Run:

```bash
grep -c ERROR logs/application.log
```

You should get:

```text
3
```

Now:

```bash
grep -c WARNING logs/application.log
```

Again:

```text
3
```

This starts to feel much more like real troubleshooting.

Instead of manually reading 10,000 log lines, I can ask Linux to find exactly what interests me.

---

# Part 5 — Show line numbers

Run:

```bash
grep -n ERROR logs/application.log
```

You'll get something similar to:

```text
5:ERROR Database connection lost
9:ERROR Backup failed
12:ERROR Authentication failure
```

`-n` gives me the **line number**.

That's especially helpful when troubleshooting configuration files.

---

# Part 6 — The pipe `|`

This is one of the most important Linux concepts you'll learn.

The pipe:

```text
|
```

takes the output of one command and gives it to another command.

For example:

```bash
cat logs/application.log | grep ERROR
```

Conceptually:

```text
cat
 ↓
all log contents
 ↓
|
 ↓
grep ERROR
 ↓
only ERROR lines
```

You don't actually need `cat` here because:

```bash
grep ERROR logs/application.log
```

is cleaner.

But I want you to understand the pipe because soon we'll do things like:

```bash
ps aux | grep ssh
```

or:

```bash
systemctl --type=service | grep running
```

or:

```bash
ip addr | grep inet
```

That is enormously useful in Linux administration.

---

# Part 7 — Chain commands

Let's combine `grep` with another command.

Run:

```bash
grep INFO logs/application.log | head -n 3
```

Read it left-to-right:

```text
Find INFO lines
       ↓
send them through |
       ↓
show only the first 3
```

Now:

```bash
grep INFO logs/application.log | tail -n 3
```

Same search, but now we get the **last three matches**.

---

# Part 8 — Redirect search results

Now let's save our errors.

```bash
grep ERROR logs/application.log > errors.txt
```

Check:

```bash
cat errors.txt
```

You should have:

```text
ERROR Database connection lost
ERROR Backup failed
ERROR Authentication failure
```

Now we're combining concepts from Lesson 1 with today's lesson.

We searched:

```text
grep
```

and redirected:

```text
>
```

the results into another file.

---

# Part 9 — Wildcards

Create some files:

```bash
touch server1.log
touch server2.log
touch server3.log
touch database1.log
touch database2.log
touch notes.txt
```

Run:

```bash
ls
```

Now:

```bash
ls *.log
```

The `*` means:

> Match zero or more characters.

So:

```text
*.log
```

means:

> Anything ending in `.log`.

Try:

```bash
ls server*.log
```

You should only get:

```text
server1.log
server2.log
server3.log
```

Now try:

```bash
ls server?.log
```

`?` represents **exactly one character**.

That's different from `*`.

---

# Your Lesson 2 Guided Lab

Run:

```bash
echo "===== ALL ERRORS ====="
grep ERROR logs/application.log

echo
echo "===== ERROR COUNT ====="
grep -c ERROR logs/application.log

echo
echo "===== WARNINGS ====="
grep WARNING logs/application.log

echo
echo "===== DATABASE EVENTS ====="
grep -i database logs/application.log

echo
echo "===== FIRST FIVE LINES ====="
head -n 5 logs/application.log

echo
echo "===== LAST FIVE LINES ====="
tail -n 5 logs/application.log

echo
echo "===== LOG FILES ====="
ls *.log

echo
echo "===== SERVER LOGS ====="
ls server*.log
```

Paste the complete output here.

**Don't move ahead after that.** I'll check your results first.

Then I'm going to give you an administrator-style challenge such as:

> *“Users report database problems. Examine the application log, determine how many database-related events occurred, identify any errors, and save those errors to an incident file.”*

And unlike the guided lab, **I won't give you the commands**. You'll have to decide whether you need `grep`, pipes, redirection, `head`, `tail`, or a combination.

That's where this starts becoming RHCSA training rather than command memorization.

















https://chatgpt.com/share/6abf217e-41fc-83ea-b261-d93ff8aac916
