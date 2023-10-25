import tkinter as tk
from tkinter import ttk
import sqlite3


# Создание главного окна
class Main(tk.Frame):
        [self.tree.insert('', 'end', values=i) for i in self.db.cur.fetchall()]


# Класс окна добавления
class Add(tk.Toplevel):
        )


# Класс окна обновления
class Update(Add):
    def __init__(self):
        self.load_data()

    def init_edit(self):
        self.title('Редактирование сотрудника')
        self.btn_ok.destroy()
        self.btn_ok = tk.Button(self, text='Готово')
        self.btn_ok.bind('<Button-1>', lambda ev: self.view.edit_record(
            self.entry_name.get(), self.entry_phone.get(),
            self.entry_email.get(), self.entry_salary.get()))

        self.btn_ok.bind('<Button-1>', lambda ev: self.destroy(),
                         add='+')
        self.btn_ok.place(x=300, y=160)

    # Метод автозаполнения
    def load_data(self):
        self.db.cur.execute('''SELECT * FROM Users WHERE id = ?''',
                            self.view.tree.set(self.view.tree.selection()[0], '#1'))
        row = self.db.cur.fetchone()
        self.entry_name.insert(0, row[1])
        self.entry_phone.insert(0, row[2])
        self.entry_email.insert(0, row[3])
        self.entry_salary.insert(0, row[4])


# Класс окна поиска сотрудников
class Search(tk.Toplevel):
    def __init__(self):
        super().__init__(root)
        self.btn_ok = tk.Button(self, text='Найти')
        self.entry_name = tk.Entry(self)
        self.init_search()
        self.view = app

    def init_search(self):
        self.title('Поиск сотрудника')
        self.geometry('300x100')
        self.resizable(False, False)
        self.grab_set()
        self.focus_set()
        # Создание формы
        label_name = tk.Label(self, text='ФИО')
        label_name.place(x=50, y=30)
        self.entry_name.place(x=150, y=30)
        self.btn_ok.bind('<Button-1>',
                         lambda ev: self.view.search_records(self.entry_name.get()))
        self.btn_ok.bind('<Button-1>',
                         lambda ev: self.destroy(), add='+')
        self.btn_ok.place(x=230, y=70)
        btn_cancel = tk.Button(self, text='Закрыть',
                               command=self.destroy)
        btn_cancel.place(x=160, y=70)


# Класс БД
class Db:
    def __init__(self):
        self.conn = sqlite3.connect('Employees.db')
        self.cur = self.conn.cursor()
        self.cur.execute('''
                CREATE TABLE IF NOT EXISTS Users(
                        id INTEGER PRIMARY KEY,
                        name TEXT,
                        phone TEXT,
                        email TEXT,
                        salary INTEGER
                        )''')

    # Метод добавления данных в БД
    def insert_data(self, name, phone, email, salary):
        self.cur.execute('''
            INSERT INTO Users (name, phone, email, salary) 
            VALUES (?,?,?,?)''', (name, phone, email, salary))


# Основной цикл
if __name__ == '__main__':
    root = tk.Tk()
    db = Db()
    app = Main(root)
    app.pack()
    root.title('Список сотрудников компании')
    root.geometry('655x400')
    root.resizable(False, False)
    root.protocol('WM_DELETE_WINDOW', app.quit)
    root.mainloop()
