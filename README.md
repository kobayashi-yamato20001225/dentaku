#作成日2025/07/30
#更新日2025/08/19

from pynput.mouse import Listener, Button  # マウス操作を監視するためのライブラリ
import tkinter as tk  # GUI（電卓）を作るためのライブラリ
import threading  # スレッドを使って非同期で電卓を起動するためのライブラリ

# 電卓がすでに起動しているかどうかを判定するフラグ
calculator_running = False

# 電卓を起動する関数
def launch_calculator():
    global calculator_running
    if calculator_running:
        return  # すでに起動していれば何もしない
    calculator_running = True

    # 電卓ウィンドウの設定
    root = tk.Tk()
    root.title("Python電卓")
    root.geometry("300x400")
    root.resizable(False, False)

    expression = ""  # 入力された式を保持する変数

    # 表示ラベル（計算式や結果を表示）
    display = tk.Label(
        root, text="", anchor="e", bg="white", fg="black",
        font=("Arial", 24), relief="ridge", height=2
    )
    display.pack(fill="both", padx=10, pady=10)

    # ボタンが押されたときの処理
    def press(key):
        nonlocal expression
        expression += str(key)
        display.config(text=expression)

    # クリアボタンの処理
    def clear():
        nonlocal expression
        expression = ""
        display.config(text="")

    # 計算処理
    def calculate():
        nonlocal expression
        try:
            result = eval(expression)  # 式を評価
            result = int(result * 100) / 100  # 小数第2位で切り捨て
            formatted = "{:,}".format(result)  # カンマ付きで表示
            display.config(text=formatted)
            expression = str(result)
        except:
            display.config(text="エラー")
            expression = ""

    # 電卓のボタン配置
    buttons = [
        ["7", "8", "9", "/"],
        ["4", "5", "6", "*"],
        ["1", "2", "3", "-"],
        ["0", ".", "=", "+"],
    ]

    frame = tk.Frame(root)
    frame.pack()

    # ボタンを作成して配置
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

    # クリアボタン
    clear_btn = tk.Button(root, text="C", font=("Arial", 18), bg="lightgray", command=clear)
    clear_btn.pack(fill="both", padx=10, pady=5)

    # ウィンドウを閉じたときの処理
    def on_close():
        global calculator_running
        calculator_running = False
        root.destroy()

    root.protocol("WM_DELETE_WINDOW", on_close)
    root.mainloop()

# マウスの中ボタンがクリックされたときに電卓を起動
def on_click(x, y, button, pressed):
    if button == Button.middle and pressed:
        threading.Thread(target=launch_calculator).start()

# マウスのクリックイベントを監視
with Listener(on_click=on_click) as listener:
    listener.join()
