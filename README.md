# Final
Create a connection to the database
def create_connection():
    conn = sqlite3.connect("student.db")
    return conn

# Create tables in the database
def create_tables(conn):
    cursor = conn.cursor()
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS students (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            name TEXT NOT NULL,
            email TEXT NOT NULL UNIQUE,
            age INTEGER NOT NULL
        );
    """)
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS grades (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            student_id INTEGER NOT NULL,
            course TEXT NOT NULL,
            grade TEXT NOT NULL,
            FOREIGN KEY (student_id) REFERENCES students (id)
        );
    """)
    conn.commit()

# Add a student to the database
def add_student(conn, name, email, age):
    cursor = conn.cursor()
    cursor.execute("INSERT INTO students (name, email, age) VALUES (?, ?, ?)", (name, email, age))
    conn.commit()

# Enter a grade for a student
def enter_grade(conn, student_id, course, grade):
    cursor = conn.cursor()
    cursor.execute("INSERT INTO grades (student_id, course, grade) VALUES (?, ?, ?)", (student_id, course, grade))
    conn.commit()

# Generate a student report
def generate_report(conn, student_id):
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM students WHERE id = ?", (student_id,))
    student = cursor.fetchone()
    if student:
        print(f"\nReport for {student[1]} (ID: {student[0]})")
        cursor.execute("SELECT course, grade FROM grades WHERE student_id = ?", (student_id,))
        grades = cursor.fetchall()
        for course, grade in grades:
            print(f"{course}: {grade}")
    else:
        print("Student not found.")

# Teacher menu
def teacher_menu(conn):
    while True:
        print("\n===== Teacher Menu =====")
        print("1. Add Student")
        print("2. Enter Grade")
        print("3. Generate Report")
        print("4. Exit")
        choice = input("Choose an option: ")
        if choice == '1':
            name = input("Enter student name: ")
            email = input("Enter student email: ")
            age = int(input("Enter student age: "))
            add_student(conn, name, email, age)
        elif choice == '2':
            student_id = int(input("Enter student ID: "))
            course = input("Enter course: ")
            grade = input("Enter grade: ")
            enter_grade(conn, student_id, course, grade)
        elif choice == '3':
            student_id = int(input("Enter student ID: "))
            generate_report(conn, student_id)
        elif choice == '4':
            print("Goodbye!")
            break
        else:
            print("Invalid choice. Try again.")

# Student menu
def student_menu(conn):
    while True:
        print("\n===== Student Menu =====")
        print("1. View Profile")
        print("2. View Grades")
        print("3. Exit")
        choice = input("Choose an option: ")
        if choice == '1':
            student_id = int(input("Enter your student ID: "))
            cursor = conn.cursor()
            cursor.execute("SELECT * FROM students WHERE id = ?", (student_id,))
            student = cursor.fetchone()
            if student:
                print(f"\n--- Student Profile ---")
                print(f"ID: {student[0]}")
                print(f"Name: {student[1]}")
                print(f"Email: {student[2]}")
                print(f"Age: {student[3]}")
            else:
                print("Student not found.")
        elif choice == '2':
            student_id = int(input("Enter your student ID: "))
            cursor = conn.cursor()
            cursor.execute("SELECT course, grade FROM grades WHERE student_id = ?", (student_id,))
            grades = cursor.fetchall()
            if grades:
                print("\n--- Grades ---")
                for course, grade in grades:
                    print(f"{course}: {grade}")
            else:
                print("No grades available.")
        elif choice == '3':
            print("Goodbye!")
            break
        else:
            print("Invalid choice. Try again.")

# Login function
def login():
    conn = create_connection()
    create_tables(conn)
    print("\n=== LOGIN ===")
    role = input("Login as (teacher/student): ").strip().lower()
    if role == 'teacher':
        username = input("Enter teacher username: ")
        password = input("Enter teacher password: ")
        if username == 'teacher' and password=='as123':
            print("Login successful! Welcome Teacher.")
            teacher_menu(conn)
        else:
            print("Invalid teacher credentials")
    elif role == 'student':
        student_id = int(input("Enter your student ID: "))
        print("Login successful! Welcome Student.")
        student_menu(conn)
    else:
        print("Invalid role! Please choose teacher or student")

# Run the login function
if __name__ == "__main__":
    login()

Find out the topper student
import sqlite3  

conn = sqlite3.connect("student.db")  
cursor=conn.cursor()
# Query to find topper
cursor.execute("SELECT * FROM students ORDER BY marks DESC LIMIT 1;")
topper = cursor.fetchone()

if topper:
    print("Topper: ID={ topper[0]  }, Name={  topper [1]    }, Marks={  topper[2]  }")
else:
    print("No student records found.")

conn.close()
Explain this
