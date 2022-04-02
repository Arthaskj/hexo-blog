title: 3、树莓派编程
category: 技能
tags: 
	- 树莓派
	- GPIO
	- python
date: 2019-08-14
index_img: 

---

3、树莓派编程

<!--more-->

<!--
 * @Author: 柯军
 * @Date: 2019-08-14 18:54:51
 * @Description: 
 -->
###  3、树莓派编程

```python
#coding=utf-8
import RPi.GPIO as GPIO
import time
import MySQLdb
import os
import maila
GPIO.setmode(GPIO.BCM)			#设置GPIO格式
GPIO.setwarnings(False)
led = 14
led2 = 17
led3 = 18
led4 = 27
led5 = 22
led6 = 23
GPIO.setup(led,GPIO.OUT)
GPIO.setup(led2,GPIO.OUT)
GPIO.setup(led3,GPIO.OUT)
GPIO.setup(led4,GPIO.OUT)
GPIO.setup(led5,GPIO.OUT)
GPIO.setup(led6,GPIO.OUT)

conn=MySQLdb.connect(host="192.168.0.1",port=3306,user="arthas",passwd="********",db="mytest");
cur = conn.cursor()
cur.execute('select * from user where name="arthas"')
sss = cur.fetchone()
#data = sss.split(",")
change = sss[6]
show = ['<span style="color:red">关闭</span>','<span style="color:green">打开</span>']
if change == 'true':
	state = sss[5]
	print(state)
	sdata = state.split(",")
	GPIO.output(led,int(sdata[0]))
	GPIO.output(led2,int(sdata[1]))
	GPIO.output(led3,int(sdata[2]))
	GPIO.output(led4,int(sdata[3]))
	GPIO.output(led5,int(sdata[4]))
	GPIO.output(led6,int(sdata[5]))
	cur.execute('update user set ch="false" where name="arthas"')
	maila.sendMail("""
	<h1>主人！你刚刚更新了家里电器的状态！！！</h1> 
	<h2>电器实时状态如下</h1>
	<label style="display:inline-block;width:100px">
	客厅窗帘：</label>"""+ show[int(sdata[0])] +"""<br>
	<label style="display:inline-block;width:100px">
	卧室窗帘：</label>"""+ show[int(sdata[1])] +"""<br>
	<label style="display:inline-block;width:100px">
	热水器：</label>""" + show[int(sdata[2])]+ """<br>
	<label style="display:inline-block;width:100px">
	空调：</label>""" + show[int(sdata[3])]+ """<br>
	<label style="display:inline-block;width:100px">
	卧室灯：</label>""" + show[int(sdata[4])]+ """<br>
	<label style="display:inline-block;width:100px">
	取暖器：</label>""" + show[int(sdata[5])]+ """<br>
	""")
cur.close()
conn.commit()
conn.close()
#try:
#	while 1:
#		GPIO.output(led,1)
#		time.sleep(0.5)
#		GPIO.output(led,0)
#		time.sleep(0.5)
#xcept KeyboardInterrupt:
#	GPIO.cleanup()
```