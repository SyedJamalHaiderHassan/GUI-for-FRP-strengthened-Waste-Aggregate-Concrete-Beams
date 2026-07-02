import tkinter as tk
from tkinter import messagebox
import pandas as pd
import numpy as np
from xgboost import XGBRegressor
import matplotlib as plt
plt.rcParams['font.family'] = 'Times New Roman'

# =========================
# Load Dataset
# =========================
DATA_PATH = r"C:\Users\HP\Documents\NEW RP\New RP Data.xlsx"
df = pd.read_excel(DATA_PATH)

X = df.iloc[:, :-1]   # input features
y = df.iloc[:, -1]    # target

# =========================
# Train XGB Model
# =========================
model = XGBRegressor(
    n_estimators=33,        # replace if needed
    max_depth=10,
    learning_rate=0.5623828845800036,
    subsample=0.4632294980690297,
    reg_alpha=0.0003807093138453316,
    colsample_bylevel=0.9438012186082686,
    colsample_bytree=0.8822610080781149,
    objective='reg:squarederror',
    random_state=42
)

model.fit(X, y)

# =========================
# GUI
# =========================
root = tk.Tk()
root.title("GUI for Flexural Strength Prediction of FRP-Waste Aggregate Concrete")
root.geometry("720x720")
root.configure(bg="white")

# Header
header = tk.Label(
    root,
    text="GUI for Flexural Strength Prediction of FRP-Waste Aggregate Concrete",
    bg="#b11226",
    fg="white",
    font=("Arial", 14, "bold"),
    pady=20
)
header.pack(fill="x")

frame = tk.Frame(root, bg="white")
frame.pack(pady=10)

entries = {}

labels = [
    "Length (mm)",
    "Width (mm)",
    "Depth (mm)",
    "Concrete Strength (MPa)",
    "Fiber Length (mm)",
    "Fiber Width (mm)",
    "Fiber Tensile Strength (MPa)",
    "Fiber Thickness (mm)",
]

dataset_columns = list(X.columns)

for i, label in enumerate(labels):
    tk.Label(
        frame,
        text=label,
        bg="#6a5acd",
        fg="white",
        font=("Arial", 10),
        width=35,
        anchor="w",
        padx=10,
        pady=5
    ).grid(row=i, column=0, pady=4)

    entry = tk.Entry(frame, width=10, font=("Arial", 10))
    entry.grid(row=i, column=1, padx=10)
    entries[dataset_columns[i]] = entry

# Output
output_frame = tk.Frame(root, bg="white")
output_frame.pack(pady=20)

tk.Label(
    output_frame,
    text="TPE-XGB",
    bg="#6a0dad",
    fg="white",
    font=("Arial", 10, "bold"),
    width=12
).grid(row=0, column=0, padx=5)

result_var = tk.StringVar()
result_entry = tk.Entry(output_frame, textvariable=result_var, width=20)
result_entry.grid(row=0, column=1, padx=5)

# =========================
# Functions
# =========================
def predict():
    try:
        values = []
        for col in dataset_columns:
            values.append(float(entries[col].get()))

        input_array = np.array(values).reshape(1, -1)
        prediction = model.predict(input_array)[0]
        result_var.set(f"{prediction:.2f}")

    except Exception as e:
        messagebox.showerror("Input Error", str(e))

def clear():
    for entry in entries.values():
        entry.delete(0, tk.END)
    result_var.set("")

# =========================
# Buttons
# =========================
btn_frame = tk.Frame(root, bg="white")
btn_frame.pack(pady=10)

tk.Button(
    btn_frame,
    text="Predict",
    bg="green",
    fg="white",
    width=12,
    command=predict
).grid(row=0, column=0, padx=10)

tk.Button(
    btn_frame,
    text="Clear",
    bg="red",
    fg="white",
    width=12,
    command=clear
).grid(row=0, column=1, padx=10)

root.mainloop()
