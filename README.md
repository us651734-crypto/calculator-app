# calculator-app
#calculator app using tkinter
#print("Numbers after discarding 100:",numbers)
import math
import tkinter
#setting button 
button_values=[
    ["AC","+/-","%","÷"],
    ["7","8","9","×"],
    ["4","5","6","-"],
    ["1","2","3","+"],
    ["0",".","√","=",]
]
#gettting row and column count
row_count= len (button_values)
column_count= len (button_values[0])


RIGHT_SYMBOLS=["÷","×","-","+","="]
top_symbols=["AC","+/-","%"]
#colour seettings

colour_light_grey="#D4D4D2"
colour_black="#1C1C1C"
colour_orange="#FF9500"
colour_dark_grey="#505050"
colour_white="#FFFFFF"

# Calculator state
current_value = "0"
stored_value = None
pending_operator = None
start_new_number = False

#function to remove trailing zeros and decimal point 

def format_number(number):
    if number == int(number):
        return str(int(number))
    return str(number)

# function to calculate output
def calculate(left, right, operation):
    if operation == "+":
        return left + right
    if operation == "-":
        return left - right
    if operation == "×":
        return left * right
    if operation == "÷":
        if right == 0:
            raise ZeroDivisionError
        
        return left / right
    return right

#function for button click
def button_clicked(value):
    global current_value, stored_value, pending_operator, start_new_number

    if value in "0123456789":
        if start_new_number or current_value == "0":
            current_value = value
            start_new_number = False
        else:
            current_value += value
        display["text"] = current_value
        return

    if value == ".":
        if start_new_number:
            current_value = "0"
            start_new_number = False
        if "." not in current_value:
            current_value += "."
        display["text"] = current_value
        return

    if value == "AC":
        current_value = "0"
        stored_value = None
        pending_operator = None
        start_new_number = False
        display["text"] = current_value
        return

    if value == "+/-":
        if current_value != "0":
            current_value = current_value[1:] if current_value.startswith("-") else "-" + current_value
        display["text"] = current_value
        return

    if value == "%":
        current_value = format_number(float(current_value) / 100)
        display["text"] = current_value
        return

    if value == "√":
        number = float(current_value)
        if number < 0:
            display["text"] = "Error"
            current_value = "0"
        else:
            current_value = format_number(math.sqrt(number))
            display["text"] = current_value
        start_new_number = True
        return

    if value in ("+", "-", "×", "÷"):
        if pending_operator is not None and not start_new_number:
            try:
                current_value = format_number(calculate(stored_value, float(current_value), pending_operator))
            except ZeroDivisionError:
                display["text"] = "Error"
                current_value = "0"
                stored_value = None
                pending_operator = None
                return
        stored_value = float(current_value)
        pending_operator = value
        start_new_number = True
        display["text"] = current_value
        return

    if value == "=" and pending_operator is not None:
        try:
            current_value = format_number(calculate(stored_value, float(current_value), pending_operator))
            display["text"] = current_value
            stored_value = None
            pending_operator = None
            start_new_number = True
        except ZeroDivisionError:
            display["text"] = "Error"
            current_value = "0"
            stored_value = None
            pending_operator = None


# Window setup
window = tkinter.Tk()
window.title("Calculator")
window.resizable(False, False)

frame = tkinter.Frame(window)
display = tkinter.Label(frame, text="0", anchor=tkinter.E, font=("Arial", 30),
                        bg=colour_black, fg=colour_white)
display.grid(row=0, column=0, columnspan=column_count, sticky="nsew")
for column in range(column_count):
    frame.grid_columnconfigure(column, weight=1, uniform="calculator_column")
for row in range(row_count):
    for column in range(column_count):
        value = button_values[row][column]
        button = tkinter.Button(frame, text=value, font=("Arial", 30),
                                 width=column_count - 1, height=1,
                                 command=lambda value=value: button_clicked(value))
        if value in top_symbols:
            button.configure(bg=colour_light_grey, fg=colour_black)
        elif value in RIGHT_SYMBOLS:
            button.configure(bg=colour_orange, fg=colour_white)
        else:
            button.configure(bg=colour_dark_grey, fg=colour_white)
        button.grid(row=row + 1, column=column, sticky="nsew")
    

frame.pack(expand=True,fill="both")


window.update()
window_width=window.winfo_width()
window_height=window.winfo_height()
screen_width=window.winfo_screenwidth()
screen_height=window.winfo_screenheight()

window_x= int((screen_width/2)-(window_width/2)  )
window_y= int((screen_height/2)-(window_height/2)  )
window.geometry(f"{window_width}x{window_height}+{window_x}+{window_y}")

#run the application






window.mainloop()

