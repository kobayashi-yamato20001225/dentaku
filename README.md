    
from pynput.mouse import Listener, Button  
import tkinter as tk                       
import threading                           

   
def launch_calculator():
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
            result = str(eval(expression))
            display.config(text=result)
            expression = result
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

    root.mainloop()

   
    def on_click(x, y, button, pressed):
        if button == Button.middle and pressed:
            threading.Thread(target=launch_calculator).start()

   
    with Listener(on_click=on_click) as listener:
        listener.join()
