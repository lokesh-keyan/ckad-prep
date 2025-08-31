
# 🚀 How to Use `vi` to Edit a File

`vi` (or `vim`) is a powerful command-line text editor commonly available in Linux environments, including the CKAD exam terminals.

---

## 📝 1. Open the File

```bash
vi myfile.yaml
```

- If the file doesn't exist, `vi` will create it.

---

## ✍️ 2. Enter Insert Mode

- Press `i` to enter **Insert mode**.
- You'll see `-- INSERT --` at the bottom.
- Now you can type or paste your content.

---

## 💾 3. Save and Exit

| Action                     | Command              |
|----------------------------|----------------------|
| Exit Insert Mode           | Press `Esc`          |
| Save and Quit              | `:wq` + `Enter`      |
| Just Save (no quit)        | `:w` + `Enter`       |
| Quit Without Saving        | `:q!` + `Enter`      |

---

## ✅ Quick Workflow Example

```bash
vi pod.yaml        # open or create the file
```
Then:

1. Press `i` → Paste your YAML
2. Press `Esc`
3. Type `:wq` → Hit `Enter`

---

## 🧠 Pro Tip

If you accidentally enter the wrong mode:

- Press `Esc` to return to command mode.
- Then type the command you need (like `:wq` to save and quit).

---

## 🔧 Advanced Editing Techniques

### Indent and Unindent

**In Visual Mode:**
1. Select lines with `V` and move up/down with arrow keys
2. Press `>` to indent, `<` to unindent
3. You can press `.` to repeat the last indent/unindent operation

---

## 🗑️ Delete All Content and Replace

### ✅ Option 1: Delete All Lines

Press the following in normal mode (press `Esc` to be sure you're not in insert mode):

```
gg    # move to the beginning of the file
dG    # delete from current line to the end of file
```

### ✅ Option 2: Select and Delete All

Alternatively:

```
ggVG  # visually select the whole file
d     # delete selected content
```