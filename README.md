# Prince_singhx_
Yes well
import tkinter as tk
from tkinter import ttk, messagebox
import re

# ---------- Helper: evaluate password ----------
def evaluate_password(pwd: str):
    """Return tuple(score:int, details:dict)"""
    details = {
        "length": len(pwd) >= 8,
        "uppercase": bool(re.search(r"[A-Z]", pwd)),
        "lowercase": bool(re.search(r"[a-z]", pwd)),
        "digit": bool(re.search(r"\d", pwd)),
        "special": bool(re.search(r"[!@#$%^&*(),.?\":{}|<>]", pwd))
    }
    score = sum(details.values())  # 0..5
    return score, details

# ---------- UI update ----------
def on_key_release(event=None):
    pwd = entry.get()
    score, d = evaluate_password(pwd)

    # Update checklist labels
    checks["length"].config(text="✔︎ At least 8 characters" if d["length"] else "✖︎ At least 8 characters",
                             fg="green" if d["length"] else "red")
    checks["uppercase"].config(text="✔︎ Uppercase letter (A-Z)" if d["uppercase"] else "✖︎ Uppercase letter (A-Z)",
                               fg="green" if d["uppercase"] else "red")
    checks["lowercase"].config(text="✔︎ Lowercase letter (a-z)" if d["lowercase"] else "✖︎ Lowercase letter (a-z)",
                               fg="green" if d["lowercase"] else "red")
    checks["digit"].config(text="✔︎ Digit (0-9)" if d["digit"] else "✖︎ Digit (0-9)",
                           fg="green" if d["digit"] else "red")
    checks["special"].config(text="✔︎ Special char (!@#...)" if d["special"] else "✖︎ Special char (!@#...)",
                             fg="green" if d["special"] else "red")

    # Update progressbar (0-100)
    percent = int((score / 5) * 100)
    progress_var.set(percent)

    # Update strength label
    if score <= 2:
        strength_text = "Weak"
        color = "red"
    elif score == 3 or score == 4:
        strength_text = "Medium"
        color = "orange"
    else:
        strength_text = "Strong"
        color = "green"

    strength_label.config(text=f"Strength: {strength_text}", fg=color)

# ---------- Show / Hide password ----------
def toggle_password():
    if entry.cget("show") == "":
        entry.config(show="*")
        btn_toggle.config(text="Show")
    else:
        entry.config(show="")
        btn_toggle.config(text="Hide")

# ---------- Copy to clipboard ----------
def copy_password():
    pwd = entry.get()
    if pwd:
        root.clipboard_clear()
        root.clipboard_append(pwd)
        messagebox.showinfo("Copied", "Password copied to clipboard")
    else:
        messagebox.showwarning("Empty", "Nothing to copy")

# ---------- Build GUI ----------
root = tk.Tk()
root.title("Live Password Strength Checker")
root.resizable(False, False)
root.geometry("420x360")

frm = ttk.Frame(root, padding=12)
frm.pack(fill="both", expand=True)

title = ttk.Label(frm, text="Password Strength Checker", font=("Arial", 14, "bold"))
title.pack(pady=(0,10))

# Entry + buttons
entry_frame = ttk.Frame(frm)
entry_frame.pack(fill="x", pady=(0,8))

entry = ttk.Entry(entry_frame, width=34, font=("Arial", 12))
entry.pack(side="left", padx=(0,8))
entry.bind("<KeyRelease>", on_key_release)

btn_toggle = ttk.Button(entry_frame, text="Show", width=8, command=toggle_password)
btn_toggle.pack(side="left")

btn_copy = ttk.Button(entry_frame, text="Copy", width=8, command=copy_password)
btn_copy.pack(side="left", padx=(6,0))

# Progressbar
progress_var = tk.IntVar(value=0)
progress = ttk.Progressbar(frm, maximum=100, variable=progress_var, length=360)
progress.pack(pady=(6,8))

strength_label = ttk.Label(frm, text="Strength: ", font=("Arial", 11, "bold"))
strength_label.pack()

# Checklist
check_frame = ttk.Frame(frm)
check_frame.pack(pady=(10,0), fill="x")

checks = {}
checks["length"] = ttk.Label(check_frame, text="✖︎ At least 8 characters", font=("Arial", 10))
checks["length"].pack(anchor="w", pady=2)
checks["uppercase"] = ttk.Label(check_frame, text="✖︎ Uppercase letter (A-Z)", font=("Arial", 10))
checks["uppercase"].pack(anchor="w", pady=2)
checks["lowercase"] = ttk.Label(check_frame, text="✖︎ Lowercase letter (a-z)", font=("Arial", 10))
checks["lowercase"].pack(anchor="w", pady=2)
checks["digit"] = ttk.Label(check_frame, text="✖︎ Digit (0-9)", font=("Arial", 10))
checks["digit"].pack(anchor="w", pady=2)
checks["special"] = ttk.Label(check_frame, text="✖︎ Special char (!@#...)", font=("Arial", 10))
checks["special"].pack(anchor="w", pady=2)

# Hint
hint = ttk.Label(frm, text="Tip: Use at least 8 characters, mix letters, numbers, and symbols.", font=("Arial", 9))
hint.pack(pady=(12,0))

# Initialize (evaluate empty)
on_key_release()

root.mainloop()