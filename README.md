# 必要なライブラリをインポート
from pynput.mouse import Listener, Button  # マウス操作を監視するためのライブラリ
import tkinter as tk                       # GUI作成用ライブラリ
import threading                           # スレッド処理用ライブラリ

# 電卓GUIを起動する関数
def launch_calculator():
    root = tk.Tk()
    root.title("Python電卓")
    root.geometry("300x400")
    root.resizable(False, False)

    expression = ""

    # 表示ラベル（数式や結果を表示）
    display = tk.Label(
        root, text="", anchor="e", bg="white", fg="black",
        font=("Arial", 24), relief="ridge", height=2
    )
    display.pack(fill="both", padx=10, pady=10)

    # ボタンが押されたときの処理（数式に追加）
    def press(key):
        nonlocal expression
        expression += str(key)
        display.config(text=expression)

    # クリアボタンの処理（数式をリセット）
    def clear():
        nonlocal expression
        expression = ""
        display.config(text="")

    # 計算ボタンの処理（数式を評価して結果を表示）
    def calculate():
        nonlocal expression
        try:
            result = str(eval(expression))
            display.config(text=result)
            expression = result
        except:
            display.config(text="エラー")
            expression = ""

    # 電卓のボタン配置（4行×4列）
    buttons = [
        ["7", "8", "9", "/"],
        ["4", "5", "6", "*"],
        ["1", "2", "3", "-"],
        ["0", ".", "=", "+"],
    ]

    frame = tk.Frame(root)
    frame.pack()

    # 各ボタンを作成して配置
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

    # クリアボタン（C）
    clear_btn = tk.Button(root, text="C", font=("Arial", 18), bg="lightgray", command=clear)
    clear_btn.pack(fill="both", padx=10, pady=5)

    root.mainloop()

# マウスのクリックイベントを監視する関数
def on_click(x, y, button, pressed):
    if button == Button.middle and pressed:
        threading.Thread(target=launch_calculator).start()

# マウスリスナーを開始（終了するまで待機）
with Listener(on_click=on_click) as listener:
    listener.join()
