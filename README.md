import mariadb
from app import app
from flask import render_template
from flask import flash, request

conn = mariadb.connect(
    host="localhost", 
    user="root", 
    password="", 
    database="genshin impact"
)

@app.route('/', methods=['GET'])
def add_employee():
   return render_template("view.html",datas=fetchListOfEmployees())
   
@app.route("/add", methods=["POST"])
def home():
 try:
   _UID=request.form.get('UID')
   _NAME=request.form.get('NAME')
   _ITEM=request.form.get('ITEM')
   _STORY=request.form.get('STORY')
   _CODE=request.form.get('CODE')
   _EMAIL=request.form.get('EMAIL')
   sql = "INSERT INTO genshinimpact(UID,NAME,ITEM,STORY,CODE,EMAIL) VALUES(%s, %s,%s, %s,%s, %s)"
   data = (_UID,_NAME,_ITEM,_STORY,_CODE,_EMAIL)
   mycursor=conn.cursor()
   mycursor.execute(sql, data)
   conn.commit()
   return render_template('view.html',datas=fetchListOfEmployees())
 except Exception as e:
   print(e)

@app.route('/deleteEmployee', methods=['POST'])
def deleteEmployee():
 try:
   _UID=request.form.get('UID')
   mycursor=conn.cursor()
   sql = "DELETE FROM genshinimpact  WHERE UID=%s"
   data = (_UID,)
   mycursor=conn.cursor()
   mycursor.execute(sql, data)   
   conn.commit()
   return render_template('view.html',datas=fetchListOfEmployees())
 except Exception as e:
  print(e)

  
def fetchListOfEmployees():
 try:
   mycursor=conn.cursor(dictionary=True)
   mycursor.execute("SELECT * FROM genshinimpact")
   rows = mycursor.fetchall()
   return rows
 except Exception as e:
   print(e)


@app.route('/employeeUpdate', methods=['POST'])
def update_employee():
 try:
   _UID=request.form.get('UID')
   _NAME=request.form.get('NAME')
   _ITEM=request.form.get('ITEM')
   _STORY=request.form.get('STORY')
   _CODE=request.form.get('CODE')
   _EMAIL=request.form.get('EMAIL')
  
   sql = "UPDATE genshinimpact SET NAME=%s, ITEM=%s, STORY=%s, CODE=%s, EMAIL=%s WHERE UID=%s"
   data = (_NAME, _ITEM,_STORY,_CODE,_EMAIL,_UID)
   mycursor=conn.cursor()
   mycursor.execute(sql, data)
   conn.commit()
   return render_template('view.html',datas=fetchListOfEmployees())
 except Exception as e:
  print(e)


@app.errorhandler(404)
def page_not_found(e):
    return render_template("404.html")
@app.errorhandler(500)
def page_not_found(e):
    return render_template("500.html")

if __name__ == "__main__":
    app.run(host='0.0.0.0',port='8080')
