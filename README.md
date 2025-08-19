from pynput.mouse import Listener, Button  # マウス操作を監視するためのライブラリ
import tkinter as tk                       # GUI作成用ライブラリ
import threading                           # スレッド処理用ライブラリ

def launch_calculator():  # 電卓GUIを起動する関数
    root = tk.Tk()  # 新しいウィンドウを作成
    root.title("Python電卓")  # ウィンドウのタイトル
    root.geometry("300x400")  # ウィンドウサイズ
    root.resizable(False, False)  # サイズ変更を不可にする

    expression = ""  # 入力された数式を保持する変数

    display = tk.Label(  # 表示ラベル（数式や結果を表示）
        root, text="", anchor="e", bg="white", fg="black",
        font=("Arial", 24), relief="ridge", height=2
    )
    display.pack(fill="both", padx=10, pady=10)

    def press(key):  # ボタンが押されたときの処理（数式に追加）
        nonlocal expression
        expression += str(key)
        display.config(text=expression)

    def clear():  # クリアボタンの処理（数式をリセット）
        nonlocal expression
        expression = ""
        display.config(text="")

    def calculate():  # 計算ボタンの処理（数式を評価して結果を表示）
        nonlocal expression
        try:
            result = str(eval(expression))  # evalで計算
            display.config(text=result)
            expression = result  # 結果を次の入力に使えるように
        except:
            display.config(text="エラー")  # エラー処理
            expression = ""

    buttons = [  # 電卓のボタン配置（4行×4列）
        ["7", "8", "9", "/"],
        ["4", "5", "6", "*"],
        ["1", "2", "3", "-"],
        ["0", ".", "=", "+"],
    ]

    frame = tk.Frame(root)
    frame.pack()

    for row in buttons:  # 各ボタンを作成して配置
        r = tk.Frame(frame)
        r.pack(expand=True, fill="both")
        for btn in row:
            b = tk.Button(r, text=btn, font=("Arial", 18), relief="groove", borderwidth=1)
            b.pack(side="left", expand=True, fill="both")
            if btn == "=":
                b.config(command=calculate)  # "="は計算処理
            else:
                b.config(command=lambda x=btn: press(x))  # その他は入力処理

    clear_btn = tk.Button(root, text="C", font=("Arial", 18), bg="lightgray", command=clear)  # クリアボタン（C）
    clear_btn.pack(fill="both", padx=10, pady=5)

    root.mainloop()  # GUIのイベントループ開始

def on_click(x, y, button, pressed):  # マウスのクリックイベントを監視する関数
    if button == Button.middle and pressed:  # 中央ボタンが押されたとき
        threading.Thread(target=launch_calculator).start()  # 電卓を別スレッドで起動

with Listener(on_click=on_click) as listener:
    listener.join()
