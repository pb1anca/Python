import tkinter as tk
from tkinter import messagebox
import calendar
import locale

def translate_month(month):
    months_ro = ["Ianuarie", "Februarie", "Martie", "Aprilie", "Mai", "Iunie",
                 "Iulie", "August", "Septembrie", "Octombrie", "Noiembrie", "Decembrie"]
    return months_ro[month - 1]

def translate_weekday(weekday):
    weekdays_ro = ["Luni", "Marti", "Miercuri", "Joi", "Vineri", "Sâmbătă", "Duminică"]
    return weekdays_ro[weekday]

def show_calendar(event=None):  # Adăugăm un parametru optional pentru eveniment (event)
    date = date_entry.get()
    
    try:
        day, month_val, year = map(int, date.split('.'))
        # Setăm localizarea la limba română
        locale.setlocale(locale.LC_TIME, 'ro_RO.UTF-8')
        cal = calendar.month(year, month_val)
        # Traducem zilele săptămânii și lunile în limba română
        weekdays_ro = ["Luni", "Marti", "Miercuri", "Joi", "Vineri", "Sâmbătă", "Duminică"]
        months_ro = ["Ianuarie", "Februarie", "Martie", "Aprilie", "Mai", "Iunie",
                     "Iulie", "August", "Septembrie", "Octombrie", "Noiembrie", "Decembrie"]
        for en, ro in zip(calendar.day_abbr[:7], weekdays_ro):
            cal = cal.replace(en, ro)
        for en, ro in zip(calendar.month_name[1:], months_ro):
            cal = cal.replace(en, ro)
        # Highlight the entered day in blue
        output_text.config(state=tk.NORMAL)
        output_text.delete('1.0', tk.END)
        output_text.insert(tk.END, cal)
        highlight_day(output_text, day)
        output_text.config(state=tk.DISABLED)
        # Highlight the entered date in the entry widget
        date_entry.config(fg="blue")
    except ValueError:
        messagebox.showerror("Error", "Te rog introdu o dată validă de tipul ZZ.LL.AAAA")
        # Reset text color to black in case of error
        date_entry.config(fg="black")

def highlight_day(text_widget, day):
    # Find all occurrences of the entered day in the text widget and highlight them in blue
    start_pos = "1.0"
    while True:
        start_pos = text_widget.search(str(day), start_pos, stopindex=tk.END)
        if not start_pos:
            break
        end_pos = f"{start_pos}+2c"
        text_widget.tag_add("blue", start_pos, end_pos)
        text_widget.tag_configure("blue", foreground="blue")
        start_pos = end_pos

# Create the main window
root = tk.Tk()
root.title("Calendar App")

# Create a label and entry for inputting the date
date_label = tk.Label(root, text="Introdu Data (ZZ.LL.AAAA):")
date_label.grid(row=0, column=0, padx=5, pady=5)
date_entry = tk.Entry(root)
date_entry.grid(row=0, column=1, padx=5, pady=5)
# Legăm apăsarea tastei Enter de funcția show_calendar()
date_entry.bind("<Return>", show_calendar)

# Create a button to show the calendar
show_button = tk.Button(root, text="Arată calendarul", command=show_calendar)
show_button.grid(row=1, columnspan=2, padx=5, pady=5)

# Create a text widget to display the calendar
output_text = tk.Text(root, height=10, width=25, state=tk.DISABLED)
output_text.grid(row=2, columnspan=2, padx=5, pady=5)

# Run the Tkinter event loop
root.mainloop()
