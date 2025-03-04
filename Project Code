import sqlite3
from tkinter import *
from tkinter import ttk, messagebox

# Database setup
def initialize_db():
    conn = sqlite3.connect("emergency_db.sqlite")
    cursor = conn.cursor()

    # Table for users (Username, Password, Address)
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS users (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            username TEXT UNIQUE,
            password TEXT,
            address TEXT
        )
    """)

    cursor.execute("""
        CREATE TABLE IF NOT EXISTS police_emergencies (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            name TEXT,
            address TEXT,
            emergency_type TEXT
        )
    """)
    
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS hospital_emergencies (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            name TEXT,
            address TEXT,
            emergency_type TEXT
        )
    """)
    
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS fire_emergencies (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            name TEXT,
            address TEXT,
            emergency_type TEXT
        )
    """)
    
    conn.commit()
    conn.close()

initialize_db()


root = Tk()
root.title("NCR Emergency Response Chatbot")
root.geometry("1920x1080")
root.configure(bg="lightgray")

# img = PhotoImage(file="aaaa.png")
# label = Label(root, image=img)
# label.pack()


global logged_in_user
logged_in_user = None

def login():
    login_window = Toplevel(root)
    login_window.title("Login")
    login_window.geometry("400x300")
    
    Label(login_window, text="Username:", font=("Times New Roman", 14)).pack(pady=5)
    username_entry = Entry(login_window, font=("Times New Roman", 14))
    username_entry.pack(pady=5)

    Label(login_window, text="Password:", font=("Times New Roman", 14)).pack(pady=5)
    password_entry = Entry(login_window, font=("Times New Roman", 14), show="*")
    password_entry.pack(pady=5)
    
    def verify_login():
        global logged_in_user
        username = username_entry.get()
        password = password_entry.get()
        conn = sqlite3.connect("emergency_db.sqlite")
        cursor = conn.cursor()
        cursor.execute("SELECT * FROM users WHERE username = ? AND password = ?", (username, password))
        user = cursor.fetchone()
        conn.close()
        if user:
            messagebox.showinfo("Login Success", f"Welcome, {username}!")
            logged_in_user = username
            login_window.destroy()
            setup_main_screen()
        else:
            messagebox.showerror("Login Failed", "Invalid username or password.")

    Button(login_window, text="Login", font=("Times New Roman", 14), bg="gray", fg="white", command=verify_login).pack(pady=10)
    
    
def register():
    register_window = Toplevel(root)
    register_window.title("Register")
    register_window.geometry("400x400")

    Label(register_window, text="Username:", font=("Times New Roman", 14)).pack(pady=5)
    username_entry = Entry(register_window, font=("Times New Roman", 14))
    username_entry.pack(pady=5)

    Label(register_window, text="Password:", font=("Times New Roman", 14)).pack(pady=5)
    password_entry = Entry(register_window, font=("Times New Roman", 14), show="*")
    password_entry.pack(pady=5)

    Label(register_window, text="Address:", font=("Times New Roman", 14)).pack(pady=5)
    address_entry = Entry(register_window, font=("Times New Roman", 14))
    address_entry.pack(pady=5)

    def process_register():
        username = username_entry.get()
        password = password_entry.get()
        address = address_entry.get()
        if username and password and address:
            conn = sqlite3.connect("emergency_db.sqlite")
            cursor = conn.cursor()
            try:
                cursor.execute("INSERT INTO users (username, password, address) VALUES (?, ?, ?)", (username, password, address))
                conn.commit()
                messagebox.showinfo("Registration Successful", "You can now log in.")
                register_window.destroy()
            except sqlite3.IntegrityError:
                messagebox.showerror("Registration Failed", "Username already exists.")
            conn.close()
        else:
            messagebox.showerror("Registration Failed", "All fields are required.")

    Button(register_window, text="Register", font=("Times New Roman", 14), bg="blue", fg="white", command=process_register).pack(pady=10)
    
def logout():
    global logged_in_user
    logged_in_user = None
    setup_main_screen()

def return_to_main():
    for widget in root.winfo_children():
        widget.destroy()
    setup_main_screen()

def show_emergency_form(emergency_type):
    for widget in root.winfo_children():
        widget.destroy()
    
    Label(root, text=f"You have chosen {emergency_type} Emergency", font=("Times New Roman", 30, "bold"), bg="lightgray").pack(pady=40)
    Label(root, text="Please fill up the boxes to send an authorized personnel to your location.", font=("Times New Roman", 20), bg="lightgray").pack(pady=20)
    
    Label(root, text="Name:", font=("Times New Roman", 18), bg="lightgray").pack()
    name_entry = Entry(root, width=70, font=("Times New Roman", 18))
    name_entry.pack(pady=10)
    
    Label(root, text="Type of Emergency:", font=("Times New Roman", 18), bg="lightgray").pack()
    emergency_dropdown = ttk.Combobox(root, values=["General Emergency", "Cardiac Emergency", "Fire Emergency"], state="readonly", width=68, font=("Times New Roman", 18))
    emergency_dropdown.pack(pady=10)
    emergency_dropdown.current(0)
    
    
    Label(root, text="Address:", font=("Times New Roman", 18), bg="lightgray").pack()
    address_entry = Entry(root, width=70, font=("Times New Roman", 18))
    address_entry.pack(pady=10)

    
    def save_to_db():
        name = name_entry.get()
        address = address_entry.get()
        emergency_type_selected = emergency_dropdown.get()
        
        if not name or not address:
            messagebox.showwarning("Input Error", "Please fill in all fields.")
            return
        
        conn = sqlite3.connect("emergency_db.sqlite")
        cursor = conn.cursor()
        
        table_name = f"{emergency_type.lower()}_emergencies"
        cursor.execute(f"INSERT INTO {table_name} (name, address, emergency_type) VALUES (?, ?, ?)", (name, address, emergency_type_selected))
        
        conn.commit()
        conn.close()
        
        messagebox.showinfo("Success", f"An authorized personnel is on the way!\n\nDetails:\nName: {name}\nAddress: {address}\nEmergency Type: {emergency_type_selected}")
        restart_program()
    
    Button(root, text="Submit", font=("Times New Roman", 18, "bold"), bg="green", fg="white", width=20, height=2, command=save_to_db).pack(pady=40)
    Button(root, text="Go Back", font=("Times New Roman", 18, "bold"), bg="gray", fg="white", width=20, height=2, command=restart_program).pack(pady=20)
def view_history(emergency_type):
    conn = sqlite3.connect("emergency_db.sqlite")
    cursor = conn.cursor()
    
    table_name = f"{emergency_type.lower()}_emergencies"
    cursor.execute(f"SELECT * FROM {table_name}")
    records = cursor.fetchall()
    conn.close()
    
    history_window = Toplevel(root)
    history_window.title(f"{emergency_type} Emergency History")
    history_window.geometry("800x600")
    
    text_area = Text(history_window, font=("Times New Roman", 14))
    text_area.pack(expand=True, fill=BOTH, padx=10, pady=10)
    
    for record in records:
        text_area.insert(END, f"ID: {record[0]}\nName: {record[1]}\nAddress: {record[2]}\nEmergency Type: {record[3]}\n{'-'*50}\n")

def restart_program():
    for widget in root.winfo_children():
        widget.destroy()
    setup_main_screen()


def setup_main_screen():
    # Create the main screen
    root.configure(bg="lightgray")

    Label(root, text="Welcome to the NCR Emergency Response Chatbot!", font=("Times New Roman", 28, "bold"), bg="lightgray").pack(pady=10)
    Label(root, text="How to use: Click on the type of emergency you are facing -> Provide the Information -> Submit",
          font=("Times New Roman", 20), bg="lightgray").pack()

    # Emergency Buttons
    Button(root, text="Police Emergency", font=("Times New Roman", 24, "bold"), width=70, height=5, fg='white', bg='red', command=lambda: show_emergency_form("Police")).pack(pady=30)
    Button(root, text="Hospital Emergency", font=("Times New Roman", 24, "bold"), width=70, height=5, fg='white', bg='blue', command=lambda: show_emergency_form("Hospital")).pack(pady=30)
    Button(root, text="Fire Emergency", font=("Times New Roman", 24, "bold"), width=70, height=5, fg='white', bg='orange', command=lambda: show_emergency_form("Fire")).pack(pady=30)

    Button(root, text="View Police Emergency History", font=("Times New Roman", 14), bg="gray", fg="white",
       width=30, height=2, command=lambda: view_history("police")).place(x=400, y=900)

    Button(root, text="View Hospital Emergency History", font=("Times New Roman", 14), bg="gray", fg="white",
       width=30, height=2, command=lambda: view_history("hospital")).place(x=800, y=900)  # Increased spacing

    Button(root, text="View Fire Emergency History", font=("Times New Roman", 14), bg="gray", fg="white",
       width=30, height=2, command=lambda: view_history("fire")).place(x=1200, y=900)  # Increased spacing
    if logged_in_user:
        Button(root, text="Logout", font=("Times New Roman", 14), bg="red", fg="white", width=30, command=logout).place(x=1550, y=10)
    else:
        Button(root, text="Login", font=("Times New Roman", 14), bg="gray", fg="white", width=15, command=login).place(x=1700, y=10)
        Button(root, text="Register", font=("Times New Roman", 14), bg="blue", fg="white", width=15, command=register).place(x=1550, y=10)

        
setup_main_screen()
root.mainloop()

