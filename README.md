# ASSIGNMENT
#信息组织小组组员：张笑笑 刘佳雯 方振宇（排名不分先后）

from flask import Flask, render_template, request
import pymysql

app = Flask(__name__)
app.debug = True

def get_cursor():
    config = {
        'host': 'localhost',
        'port': 3306,
        'user': 'root',
        'passwd': 'lpjVAE567',
        'db': 'text2',
        'charset': 'utf8'
    }
    conn = pymysql.connect(**config)
    conn.autocommit(1)  # conn.autocommit(True)
    return conn.cursor()


@app.route('/')
def index():
    return render_template("index.html")

@app.route('/index')
def index2():
    return render_template("index.html")

@app.route('/login', methods=['post'])
def login():
    name = request.form.get("name")
    password = request.form.get("password")
    cursor = get_cursor()  # 打开数据库
    row = cursor.execute("select * from manager where name = %s", (name))  # 获取查询到的信息条数
    if row:
        cursor.execute("select login_password from manager where name = %s", (name))
        message = cursor.fetchone()
        print(message[0])
        print(password)
        if password == str(message[0]):
            cursor.execute("select * from manager")
            lists = cursor.fetchall()
            return render_template("manage.html", name=name, lists=lists)
        else:
            msg = "密码错误！"
            return render_template("index.html", msg=msg)
    else:
        msg = "您不是管理员！"
        return render_template("index.html", msg=msg)


@app.route('/insert', methods=['post'])
def insert():
    name = request.form.get("name")
    studentnumber = request.form.get("studentnumber")
    gender = request.form.get("gender")
    dormitorybuilding = request.form.get("dormitorybuilding")
    roomnumber = request.form.get("roomnumber")
    loginpassword = request.form.get("loginpassword")

    value = (name, studentnumber, gender, dormitorybuilding, loginpassword)
    insert_sql = '''INSERT INTO manager(name, student_number, gender, dormitory_building, login_password) values (%s,%s,%s,%s,%s)'''
    cursor = get_cursor()  # 打开数据库
    cursor.execute(insert_sql, value)  # 执行sql语句
    msg="添加成功！"
    return msg


@app.route('/delete/<int:id>')
def delete(name):
    cursor = get_cursor()  # 打开数据库
    cursor.execute("delete from manager where name = %s", (name))  # 删除某一行值
    msg="删除成功！"
    return msg


@app.route('/update',methods=['post'])
def update():
    name = request.form.get("name")
    studentnumber = request.form.get("student number")
    gender = request.form.get("gender")
    dormitorybuilding = request.form.get("dormitory building")
    roomnumber = request.form.get("room number")
    loginpassword = request.form.get("login password")
    value = (name, studentnumber, gender, dormitorybuilding, loginpassword)
    cursor = get_cursor()  # 打开数据库
    cursor.execute("update manager set name = %s,student_number = %s,gender =%s,dormitory_building = %s,room_number =%s,login_password =%s where name = %s", value)  # 删除某一行值
    msg = "编辑成功！"
    return msg


if __name__ == '__main__':
    app.run()
