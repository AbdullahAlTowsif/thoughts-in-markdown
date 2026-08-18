# Removing Already-Tracked Files with `.gitignore`

## The Problem
Adding a `.gitignore` file only prevents **future** tracking of files. If files like `.vs`, `bin`, or `obj` were already committed and pushed to GitHub *before* adding `.gitignore`, they will keep being tracked until you explicitly remove them from Git.

---

## Step 1: Add a `.gitignore` File

Create a `.gitignore` at your repo root. Since project folders can be nested (e.g. `Class 02/Homework2/...`), use the `**/` prefix so patterns match at **any depth**:

```
**/.vs/
**/bin/
**/obj/
*.user
```

---

## Step 2: Find Exactly What's Already Tracked

Don't guess paths — ask Git directly:

```powershell
git ls-files | Select-String "\.vs/|bin/|obj/"
```

This lists the exact relative paths Git currently tracks for those folders.

---

## Step 3: Untrack the Files (Keep Them Locally)

Remove the files from Git's index only (`--cached`), which keeps them on your local disk — only their Git tracking is removed.

### Case A: Default — folders are in the repo root

If `.vs`, `bin`, and `obj` sit directly at the root of your repo, this simple command works:

```powershell
git rm -r --cached .vs bin obj
```

### Case B: Folders are nested / in a different path

If you get this error:

```
fatal: pathspec '.vs' did not match any files
```

...it means Git can't find those folders at the root — they're nested somewhere else (e.g. inside a subfolder for a specific class/homework). Use the **exact paths** you found in Step 2 instead:

```powershell
git rm -r --cached "Class 02/Homework2/.vs"
git rm -r --cached "Class 02/Homework2/Homework2/obj"
```

> ⚠️ Always confirm exact paths with `git ls-files` first — don't guess based on what you see in your file explorer/IDE, since Git needs the path relative to the repo root.

---

## Step 4: Verify the Cleanup

Run the same search again — it should return nothing:

```powershell
git ls-files | Select-String "\.vs/|bin/|obj/"
```

---

## Step 5: Commit and Push

```powershell
git add .gitignore
git commit -m "Remove tracked .vs and obj files, update .gitignore"
git push
```

---

## Notes

- This removes the files going forward, but they'll **still exist in earlier commit history**. For most coursework/assignments, this is acceptable since reviewers care about the repo being clean going forward.
- If you truly need to scrub these files from **all** Git history (rare for coursework), you'd need `git filter-repo` or BFG Repo-Cleaner — a more advanced/destructive process.
- Always double-check branch source/target when opening a PR (e.g. don't do `main → main`) — confirm the correct branching strategy from your course material.
