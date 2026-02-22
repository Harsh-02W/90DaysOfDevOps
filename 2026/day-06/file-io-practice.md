# 🐧 Linux Practice – File I/O Basics  
**Day 06 – 90 Days DevOps Challenge**  
> Hands-on verification. Practicing fundamental file read/write operations.

Today’s focus was basic file input/output using simple Linux commands.  
The goal: strengthen command-line muscle memory through repetition.
  

---

## 📁 Step 1 – Create File

```bash
touch notes.txt
```

→ It will create an empty file named `notes.txt` in the current directory.


## ✍️ Step 2 – Write Initial Content

```bash
echo "Line 1 - DevOps begins with discipline." > notes.txt
```

→ It will write the first line into the file.
→ If the file already contains data, it will overwrite it.


## ➕ Step 3 – Append Second Line

```bash
echo "Line 2 - Automation reduces friction." >> notes.txt
```

→ It will append the second line to the file without deleting existing content.


## 🔄 Step 4 – Append & Display Using tee

```bash
echo "Line 3 - Small changes reduce risk." | tee -a notes.txt
```

→ It will append the third line to the file and display it on the terminal at the same time.


## 📖 Step 5 – Read Full File

```bash
cat notes.txt
```

→ It will display the complete content of the file.


## 🔍 Step 6 – Read First Two Lines

```bash
head -n 5 notes.txt
```

→ It will display the first 5 lines of the file.


## 🔍 Step 7 – Read Last Two Lines

```bash
tail -n 10 notes.txt
```

→ It will display the last 10 lines of the file.

---

## ✅ Conclusion

Today’s practice strengthened understanding of shell redirection and file handling behavior.

By manually creating, modifying, and verifying `notes.txt`, I observed how Linux processes input and output in real time.

Mastery of these basics is essential before moving to higher-level automation tools.
