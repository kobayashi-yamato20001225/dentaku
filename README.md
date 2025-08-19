from pynput.mouse import Listener, Button
import tkinter as tk
import threading

# 電卓がすでに起動しているかを判定するフラグ
calculator_running = False

def launch_calculator():
    global calculator_running
    if calculator_running:
        return  # すでに起動していれば何もしない
    calculator_running = True

    root = tk.Tk()
    root.title("Python電卓")
    root.geometry("300x400")
    root.resizable(False, False)

    expression = ""

    display = tk.Label(
        root, text="", anchor="e", bg="white", fg="black",
        font=("Arial", 24), relief="ridge", height=2
    )
    display.pack(fill="both", padx=10, pady=10)

    def press(key):
        nonlocal expression
        expression += str(key)
        display.config(text=expression)

    def clear():
        nonlocal expression
        expression = ""
        display.config(text="")

    def calculate():
        nonlocal expression
        try:
            result = eval(expression)
            result = int(result * 100) / 100  # 少数第2位で切り捨て
            formatted = "{:,}".format(result)  # カンマ付き表示
            display.config(text=formatted)
            expression = str(result)
        except:
            display.config(text="エラー")
            expression = ""

    buttons = [
        ["7", "8", "9", "/"],
        ["4", "5", "6", "*"],
        ["1", "2", "3", "-"],
        ["0", ".", "=", "+"],
    ]

    frame = tk.Frame(root)
    frame.pack()

    for row in buttons:
        r = tk.Frame(frame)
        r.pack(expand=True, fill="both")
        for btn in row:
            b = tk.Button(r, text=btn, font=("Arial", 18), relief="groove", borderwidth=1)
            b.pack(side="left", expand=True, fill="both")
            if btn == "=":
                b.config(command=calculate)
            else:
                b.config(command=lambda x=btn: press(x))

    clear_btn = tk.Button(root, text="C", font=("Arial", 18), bg="lightgray", command=clear)
    clear_btn.pack(fill="both", padx=10, pady=5)

    def on_close():
        global calculator_running
        calculator_running = False
        root.destroy()

    root.protocol("WM_DELETE_WINDOW", on_close)
    root.mainloop()

def on_click(x, y, button, pressed):
    if button == Button.middle and pressed:
        threading.Thread(target=launch_calculator).start()

with Listener(on_click=on_click) as listener:
    listener.join()
