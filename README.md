# 본인의 과제명 작성
서울 지하철 1~2월 통계에 의한 시간대별 탑승 용이시간 표시툴
학과 | 학번 | 성명
컴퓨터공학과 | 201624462 | 김한영
본인학과 |본인학번 |본인학성명


## 프로젝트 개요
본인의 프로젝트 개요에 대하여 작성합니다.
약속을 잡는 시간대라거나, 출근하는 시간대를 정하는데 있어 지하철의 인원 비가 중요하다고 생각을 해서
서울 지하철 승하차 인원을 토대로 시간대별로, 요일별로 승차 인원과 하차 인원의 차를 사용자가 입력한
역과, 요일에 대하여 그래프를 그려주는 프로그램을 짜 봤습니다.

## 사용한 공공데이터 
[데이터보기]
https://www.data.go.kr/dataset/15024829/fileData.do?lang=ko

## 소스
* [링크로 소스 내용 보기](https://github.com/cybermin/python2019/blob/master/tes.py) 
https://github.com/hanyoung918/final_homework/blob/master/finalhw.py

* 코드 삽입
~~~python
from pandas import*
from tkinter import*
import matplotlib.pyplot as plt
from matplotlib import *

rcParams['axes.unicode_minus'] = False
from matplotlib import font_manager, rc
font_name = font_manager.FontProperties(fname="c:/Windows/Fonts/malgun.ttf").get_name()
rc('font', family=font_name)

from matplotlib.backends.backend_tkagg import (
    FigureCanvasTkAgg, NavigationToolbar2Tk)
# Implement the default Matplotlib key bindings.
from matplotlib.backend_bases import key_press_handler
from matplotlib.figure import Figure

fields = '역 이름', '요일'
ans = [] # 사용자의 역 이름, 요일, 시간을 입력받음

def fetch(entries):
   for entry in entries:
     field = entry[0]
     text  = entry[1].get()
     if(len(ans) > 2):
       del ans[0:2]
     ans.append(text)
   y1,y2 = read(ans[0],ans[1])
   x = [0,1,2,3,4,5,6]
   x1 = ['월','화','수','목','금','토','일']
   x2 = ['05 ~ 06','06 ~ 07','07 ~ 08','08 ~ 09','09 ~ 10','10 ~ 11','11 ~ 12',
    	'12 ~ 13','13 ~ 14','14 ~ 15','15 ~ 16','16 ~ 17','17 ~ 18','18 ~ 19',
    	'19 ~ 20','20 ~ 21','21 ~ 22','22 ~ 23','23 ~ 24','00 ~ 01']
   fig = Figure(figsize=(4, 3), dpi=100)
   a = fig.add_subplot(211)
   a.bar(x1,y1)
   a.grid(True)
   a.set_title(ans[0]+str(" 요일별 현황"))
   a.set_ylabel("인원 수 (명)")
   b = fig.add_subplot(212)
   b.plot(x2,y2)
   b.grid(True)
   b.set_title(ans[1]+str("(요일) ")+str(ans[0])+str(" 시간별 현황"))
   b.set_ylabel("인원 수 (명)")
   b.set_xlabel("시간 (시)")
   canvas = FigureCanvasTkAgg(fig, master=win)  # A tk.DrawingArea.
   canvas.draw()
   canvas.get_tk_widget().pack(side=TOP, fill=BOTH, expand=1,anchor = "w")
   toolbar = NavigationToolbar2Tk(canvas, win)
   toolbar.update()
   canvas.get_tk_widget().pack(side=BOTTOM, fill=BOTH, expand=1)

def makeform(root, fields):
    entries = []
    for field in fields:
      row = Frame(root)
      lab = Label(row, width=7, text=field, anchor='w')
      ent = Entry(row)
      row.pack(side=TOP, fill=X, padx=5, pady=5)
      lab.pack(side=LEFT)
      ent.pack(side=LEFT, fill=X)
      entries.append((field, ent))
    return entries
    print(entries)

def name_display(root):
    left = Frame(root)
    sn = Text(left,height = 50,width=20)
    for name in station():
        sn.insert(END,name)
        sn.insert(END,"\n")
    sn.pack(side = LEFT, fill=Y,anchor='s')
    sc=Scrollbar(left)
    sc.pack(side=LEFT,fill=Y,anchor='s')
    sc.config(command=sn.yview)
    sn.config(yscrollcommand=sc.set)
    left.pack(side=LEFT, fill=Y,anchor='s')



def station():
    df = read_csv('test3.csv')
    station_name = list(df['역명'])
    station_name = list(set(station_name))
    return station_name



def _quit():
    win.quit()     # stops mainloop
    win.destroy()  # this is necessary on Windows to prevent
                    # Fatal Python Error: PyEval_RestoreThread: NULL tstate

def read(user_station_name,user_day):
    if(user_station_name and user_day == ""):
        return 0

    column = -1
    Week = {'Monday':0,'Tuesday':0,'Wednesday':0,'Thursday':0,'Friday':0,'Saturday':0,'Sunday':0}
    Time = {'05 ~ 06':0 ,'06 ~ 07':0,	'07 ~ 08':0,	'08 ~ 09':0,	'09 ~ 10':0,	'10 ~ 11':0,	'11 ~ 12':0,
    	'12 ~ 13':0,	'13 ~ 14':0,	'14 ~ 15':0,	'15 ~ 16':0,	'16 ~ 17':0,	'17 ~ 18':0,	'18 ~ 19':0,
        	'19 ~ 20':0,	'20 ~ 21':0,	'21 ~ 22':0,	'22 ~ 23':0,	'23 ~ 24':0,	'00 ~ 01':0}

    df = read_csv('test3.csv')
    date = df['날짜']



    for num in date:
        column+=1;
        if((num in ['2019-01-07','2019-01-14','2019-01-21','2019-01-28' ,
            '2019-02-04','2019-02-11','2019-02-18','2019-02-25']) and (df.loc[column,'역명'] == user_station_name)): #1월,2월의 월요일들 중 입력한 값의 역이라면
            if(df.loc[column,'구분']=='승차'):
                Week['Monday'] += df.loc[column,'합 계'] #승차 인원은 더하고
                if(user_day == '월' or '월요일'):
                    Time['05 ~ 06'] += df.loc[column,'05 ~ 06']
                    Time['06 ~ 07'] += df.loc[column,'06 ~ 07']
                    Time['07 ~ 08'] += df.loc[column,'07 ~ 08']
                    Time['08 ~ 09'] += df.loc[column,'08 ~ 09']
                    Time['09 ~ 10'] += df.loc[column,'09 ~ 10']
                    Time['10 ~ 11'] += df.loc[column,'10 ~ 11']
                    Time['11 ~ 12'] += df.loc[column,'11 ~ 12']
                    Time['12 ~ 13'] += df.loc[column,'12 ~ 13']
                    Time['13 ~ 14'] += df.loc[column,'13 ~ 14']
                    Time['14 ~ 15'] += df.loc[column,'14 ~ 15']
                    Time['15 ~ 16'] += df.loc[column,'15 ~ 16']
                    Time['16 ~ 17'] += df.loc[column,'16 ~ 17']
                    Time['17 ~ 18'] += df.loc[column,'17 ~ 18']
                    Time['18 ~ 19'] += df.loc[column,'18 ~ 19']
                    Time['19 ~ 20'] += df.loc[column,'19 ~ 20']
                    Time['20 ~ 21'] += df.loc[column,'20 ~ 21']
                    Time['21 ~ 22'] += df.loc[column,'21 ~ 22']
                    Time['22 ~ 23'] += df.loc[column,'22 ~ 23']
                    Time['23 ~ 24'] += df.loc[column,'23 ~ 24']
                    Time['00 ~ 01'] += df.loc[column,'00 ~ 01']
            elif(df.loc[column,'구분']=='하차'):
                Week['Monday'] -= df.loc[column,'합 계'] #하차 인원은 뺀다
                if(user_day == '월' or '월요일'):
                   Time['05 ~ 06'] -= df.loc[column,'05 ~ 06']
                   Time['06 ~ 07'] -= df.loc[column,'06 ~ 07']
                   Time['07 ~ 08'] -= df.loc[column,'07 ~ 08']
                   Time['08 ~ 09'] -= df.loc[column,'08 ~ 09']
                   Time['09 ~ 10'] -= df.loc[column,'09 ~ 10']
                   Time['10 ~ 11'] -= df.loc[column,'10 ~ 11']
                   Time['11 ~ 12'] -= df.loc[column,'11 ~ 12']
                   Time['12 ~ 13'] -= df.loc[column,'12 ~ 13']
                   Time['13 ~ 14'] -= df.loc[column,'13 ~ 14']
                   Time['14 ~ 15'] -= df.loc[column,'14 ~ 15']
                   Time['15 ~ 16'] -= df.loc[column,'15 ~ 16']
                   Time['16 ~ 17'] -= df.loc[column,'16 ~ 17']
                   Time['17 ~ 18'] -= df.loc[column,'17 ~ 18']
                   Time['18 ~ 19'] -= df.loc[column,'18 ~ 19']
                   Time['19 ~ 20'] -= df.loc[column,'19 ~ 20']
                   Time['20 ~ 21'] -= df.loc[column,'20 ~ 21']
                   Time['21 ~ 22'] -= df.loc[column,'21 ~ 22']
                   Time['22 ~ 23'] -= df.loc[column,'22 ~ 23']
                   Time['23 ~ 24'] -= df.loc[column,'23 ~ 24']
                   Time['00 ~ 01'] -= df.loc[column,'00 ~ 01']
        if(num in['2019-01-01','2019-01-08','2019-01-15','2019-01-22','2019-01-29',
        '2019-02-05','2019-02-12','2019-02-19','2019-02-26']): #1월,2월의 화요일들
            if(df.loc[column,'구분']=='승차'):
                Week['Tuesday'] += df.loc[column,'합 계']
                if(user_day == '화' or '화요일'):
                    Time['05 ~ 06'] += df.loc[column,'05 ~ 06']
                    Time['06 ~ 07'] += df.loc[column,'06 ~ 07']
                    Time['07 ~ 08'] += df.loc[column,'07 ~ 08']
                    Time['08 ~ 09'] += df.loc[column,'08 ~ 09']
                    Time['09 ~ 10'] += df.loc[column,'09 ~ 10']
                    Time['10 ~ 11'] += df.loc[column,'10 ~ 11']
                    Time['11 ~ 12'] += df.loc[column,'11 ~ 12']
                    Time['12 ~ 13'] += df.loc[column,'12 ~ 13']
                    Time['13 ~ 14'] += df.loc[column,'13 ~ 14']
                    Time['14 ~ 15'] += df.loc[column,'14 ~ 15']
                    Time['15 ~ 16'] += df.loc[column,'15 ~ 16']
                    Time['16 ~ 17'] += df.loc[column,'16 ~ 17']
                    Time['17 ~ 18'] += df.loc[column,'17 ~ 18']
                    Time['18 ~ 19'] += df.loc[column,'18 ~ 19']
                    Time['19 ~ 20'] += df.loc[column,'19 ~ 20']
                    Time['20 ~ 21'] += df.loc[column,'20 ~ 21']
                    Time['21 ~ 22'] += df.loc[column,'21 ~ 22']
                    Time['22 ~ 23'] += df.loc[column,'22 ~ 23']
                    Time['23 ~ 24'] += df.loc[column,'23 ~ 24']
                    Time['00 ~ 01'] += df.loc[column,'00 ~ 01']

            elif(df.loc[column,'구분']=='하차'):
                Week['Tuesday'] -= df.loc[column,'합 계']
                if(user_day == '화' or '화요일'):
                   Time['05 ~ 06'] -= df.loc[column,'05 ~ 06']
                   Time['06 ~ 07'] -= df.loc[column,'06 ~ 07']
                   Time['07 ~ 08'] -= df.loc[column,'07 ~ 08']
                   Time['08 ~ 09'] -= df.loc[column,'08 ~ 09']
                   Time['09 ~ 10'] -= df.loc[column,'09 ~ 10']
                   Time['10 ~ 11'] -= df.loc[column,'10 ~ 11']
                   Time['11 ~ 12'] -= df.loc[column,'11 ~ 12']
                   Time['12 ~ 13'] -= df.loc[column,'12 ~ 13']
                   Time['13 ~ 14'] -= df.loc[column,'13 ~ 14']
                   Time['14 ~ 15'] -= df.loc[column,'14 ~ 15']
                   Time['15 ~ 16'] -= df.loc[column,'15 ~ 16']
                   Time['16 ~ 17'] -= df.loc[column,'16 ~ 17']
                   Time['17 ~ 18'] -= df.loc[column,'17 ~ 18']
                   Time['18 ~ 19'] -= df.loc[column,'18 ~ 19']
                   Time['19 ~ 20'] -= df.loc[column,'19 ~ 20']
                   Time['20 ~ 21'] -= df.loc[column,'20 ~ 21']
                   Time['21 ~ 22'] -= df.loc[column,'21 ~ 22']
                   Time['22 ~ 23'] -= df.loc[column,'22 ~ 23']
                   Time['23 ~ 24'] -= df.loc[column,'23 ~ 24']
                   Time['00 ~ 01'] -= df.loc[column,'00 ~ 01']
        if(num in['2019-01-02','2019-01-09','2019-01-16','2019-01-23','2019-01-30',
        '2019-02-06','2019-02-13','2019-02-20','2019-02-27']): #1월,2월의 수요일들
            if(df.loc[column,'구분']=='승차'):
                Week['Wednesday'] += df.loc[column,'합 계']
                if(user_day == '수' or '수요일'):
                    Time['05 ~ 06'] += df.loc[column,'05 ~ 06']
                    Time['06 ~ 07'] += df.loc[column,'06 ~ 07']
                    Time['07 ~ 08'] += df.loc[column,'07 ~ 08']
                    Time['08 ~ 09'] += df.loc[column,'08 ~ 09']
                    Time['09 ~ 10'] += df.loc[column,'09 ~ 10']
                    Time['10 ~ 11'] += df.loc[column,'10 ~ 11']
                    Time['11 ~ 12'] += df.loc[column,'11 ~ 12']
                    Time['12 ~ 13'] += df.loc[column,'12 ~ 13']
                    Time['13 ~ 14'] += df.loc[column,'13 ~ 14']
                    Time['14 ~ 15'] += df.loc[column,'14 ~ 15']
                    Time['15 ~ 16'] += df.loc[column,'15 ~ 16']
                    Time['16 ~ 17'] += df.loc[column,'16 ~ 17']
                    Time['17 ~ 18'] += df.loc[column,'17 ~ 18']
                    Time['18 ~ 19'] += df.loc[column,'18 ~ 19']
                    Time['19 ~ 20'] += df.loc[column,'19 ~ 20']
                    Time['20 ~ 21'] += df.loc[column,'20 ~ 21']
                    Time['21 ~ 22'] += df.loc[column,'21 ~ 22']
                    Time['22 ~ 23'] += df.loc[column,'22 ~ 23']
                    Time['23 ~ 24'] += df.loc[column,'23 ~ 24']
                    Time['00 ~ 01'] += df.loc[column,'00 ~ 01']

            elif(df.loc[column,'구분']=='하차'):
                Week['Wednesday'] -= df.loc[column,'합 계']
                if(user_day == '수' or '수요일'):
                   Time['05 ~ 06'] -= df.loc[column,'05 ~ 06']
                   Time['06 ~ 07'] -= df.loc[column,'06 ~ 07']
                   Time['07 ~ 08'] -= df.loc[column,'07 ~ 08']
                   Time['08 ~ 09'] -= df.loc[column,'08 ~ 09']
                   Time['09 ~ 10'] -= df.loc[column,'09 ~ 10']
                   Time['10 ~ 11'] -= df.loc[column,'10 ~ 11']
                   Time['11 ~ 12'] -= df.loc[column,'11 ~ 12']
                   Time['12 ~ 13'] -= df.loc[column,'12 ~ 13']
                   Time['13 ~ 14'] -= df.loc[column,'13 ~ 14']
                   Time['14 ~ 15'] -= df.loc[column,'14 ~ 15']
                   Time['15 ~ 16'] -= df.loc[column,'15 ~ 16']
                   Time['16 ~ 17'] -= df.loc[column,'16 ~ 17']
                   Time['17 ~ 18'] -= df.loc[column,'17 ~ 18']
                   Time['18 ~ 19'] -= df.loc[column,'18 ~ 19']
                   Time['19 ~ 20'] -= df.loc[column,'19 ~ 20']
                   Time['20 ~ 21'] -= df.loc[column,'20 ~ 21']
                   Time['21 ~ 22'] -= df.loc[column,'21 ~ 22']
                   Time['22 ~ 23'] -= df.loc[column,'22 ~ 23']
                   Time['23 ~ 24'] -= df.loc[column,'23 ~ 24']
                   Time['00 ~ 01'] -= df.loc[column,'00 ~ 01']
        if(num in['2019-01-03','2019-01-10','2019-01-17','2019-01-24','2019-01-31',
        '2019-02-07','2019-02-14','2019-02-21','2019-02-28']): #1월,2월의 목요일들
            if(df.loc[column,'구분']=='승차'):
                Week['Thursday'] += df.loc[column,'합 계']
                if(user_day == '목' or '목요일'):
                    Time['05 ~ 06'] += df.loc[column,'05 ~ 06']
                    Time['06 ~ 07'] += df.loc[column,'06 ~ 07']
                    Time['07 ~ 08'] += df.loc[column,'07 ~ 08']
                    Time['08 ~ 09'] += df.loc[column,'08 ~ 09']
                    Time['09 ~ 10'] += df.loc[column,'09 ~ 10']
                    Time['10 ~ 11'] += df.loc[column,'10 ~ 11']
                    Time['11 ~ 12'] += df.loc[column,'11 ~ 12']
                    Time['12 ~ 13'] += df.loc[column,'12 ~ 13']
                    Time['13 ~ 14'] += df.loc[column,'13 ~ 14']
                    Time['14 ~ 15'] += df.loc[column,'14 ~ 15']
                    Time['15 ~ 16'] += df.loc[column,'15 ~ 16']
                    Time['16 ~ 17'] += df.loc[column,'16 ~ 17']
                    Time['17 ~ 18'] += df.loc[column,'17 ~ 18']
                    Time['18 ~ 19'] += df.loc[column,'18 ~ 19']
                    Time['19 ~ 20'] += df.loc[column,'19 ~ 20']
                    Time['20 ~ 21'] += df.loc[column,'20 ~ 21']
                    Time['21 ~ 22'] += df.loc[column,'21 ~ 22']
                    Time['22 ~ 23'] += df.loc[column,'22 ~ 23']
                    Time['23 ~ 24'] += df.loc[column,'23 ~ 24']
                    Time['00 ~ 01'] += df.loc[column,'00 ~ 01']

            elif(df.loc[column,'구분']=='하차'):
                Week['Thursday'] -= df.loc[column,'합 계']
                if(user_day == '목' or '목요일'):
                   Time['05 ~ 06'] -= df.loc[column,'05 ~ 06']
                   Time['06 ~ 07'] -= df.loc[column,'06 ~ 07']
                   Time['07 ~ 08'] -= df.loc[column,'07 ~ 08']
                   Time['08 ~ 09'] -= df.loc[column,'08 ~ 09']
                   Time['09 ~ 10'] -= df.loc[column,'09 ~ 10']
                   Time['10 ~ 11'] -= df.loc[column,'10 ~ 11']
                   Time['11 ~ 12'] -= df.loc[column,'11 ~ 12']
                   Time['12 ~ 13'] -= df.loc[column,'12 ~ 13']
                   Time['13 ~ 14'] -= df.loc[column,'13 ~ 14']
                   Time['14 ~ 15'] -= df.loc[column,'14 ~ 15']
                   Time['15 ~ 16'] -= df.loc[column,'15 ~ 16']
                   Time['16 ~ 17'] -= df.loc[column,'16 ~ 17']
                   Time['17 ~ 18'] -= df.loc[column,'17 ~ 18']
                   Time['18 ~ 19'] -= df.loc[column,'18 ~ 19']
                   Time['19 ~ 20'] -= df.loc[column,'19 ~ 20']
                   Time['20 ~ 21'] -= df.loc[column,'20 ~ 21']
                   Time['21 ~ 22'] -= df.loc[column,'21 ~ 22']
                   Time['22 ~ 23'] -= df.loc[column,'22 ~ 23']
                   Time['23 ~ 24'] -= df.loc[column,'23 ~ 24']
                   Time['00 ~ 01'] -= df.loc[column,'00 ~ 01']
        if(num in['2019-01-04','2019-01-11','2019-01-18','2019-01-25','2019-02-01',
        '2019-02-08','2019-02-15','2019-02-22']): #1월,2월의 금요일들
            if(df.loc[column,'구분']=='승차'):
                Week['Friday'] += df.loc[column,'합 계']
                if(user_day == '금' or '금요일'):
                    Time['05 ~ 06'] += df.loc[column,'05 ~ 06']
                    Time['06 ~ 07'] += df.loc[column,'06 ~ 07']
                    Time['07 ~ 08'] += df.loc[column,'07 ~ 08']
                    Time['08 ~ 09'] += df.loc[column,'08 ~ 09']
                    Time['09 ~ 10'] += df.loc[column,'09 ~ 10']
                    Time['10 ~ 11'] += df.loc[column,'10 ~ 11']
                    Time['11 ~ 12'] += df.loc[column,'11 ~ 12']
                    Time['12 ~ 13'] += df.loc[column,'12 ~ 13']
                    Time['13 ~ 14'] += df.loc[column,'13 ~ 14']
                    Time['14 ~ 15'] += df.loc[column,'14 ~ 15']
                    Time['15 ~ 16'] += df.loc[column,'15 ~ 16']
                    Time['16 ~ 17'] += df.loc[column,'16 ~ 17']
                    Time['17 ~ 18'] += df.loc[column,'17 ~ 18']
                    Time['18 ~ 19'] += df.loc[column,'18 ~ 19']
                    Time['19 ~ 20'] += df.loc[column,'19 ~ 20']
                    Time['20 ~ 21'] += df.loc[column,'20 ~ 21']
                    Time['21 ~ 22'] += df.loc[column,'21 ~ 22']
                    Time['22 ~ 23'] += df.loc[column,'22 ~ 23']
                    Time['23 ~ 24'] += df.loc[column,'23 ~ 24']
                    Time['00 ~ 01'] += df.loc[column,'00 ~ 01']

            elif(df.loc[column,'구분']=='하차'):
                Week['Friday'] -= df.loc[column,'합 계']
                if(user_day == '금' or '금요일'):
                   Time['05 ~ 06'] -= df.loc[column,'05 ~ 06']
                   Time['06 ~ 07'] -= df.loc[column,'06 ~ 07']
                   Time['07 ~ 08'] -= df.loc[column,'07 ~ 08']
                   Time['08 ~ 09'] -= df.loc[column,'08 ~ 09']
                   Time['09 ~ 10'] -= df.loc[column,'09 ~ 10']
                   Time['10 ~ 11'] -= df.loc[column,'10 ~ 11']
                   Time['11 ~ 12'] -= df.loc[column,'11 ~ 12']
                   Time['12 ~ 13'] -= df.loc[column,'12 ~ 13']
                   Time['13 ~ 14'] -= df.loc[column,'13 ~ 14']
                   Time['14 ~ 15'] -= df.loc[column,'14 ~ 15']
                   Time['15 ~ 16'] -= df.loc[column,'15 ~ 16']
                   Time['16 ~ 17'] -= df.loc[column,'16 ~ 17']
                   Time['17 ~ 18'] -= df.loc[column,'17 ~ 18']
                   Time['18 ~ 19'] -= df.loc[column,'18 ~ 19']
                   Time['19 ~ 20'] -= df.loc[column,'19 ~ 20']
                   Time['20 ~ 21'] -= df.loc[column,'20 ~ 21']
                   Time['21 ~ 22'] -= df.loc[column,'21 ~ 22']
                   Time['22 ~ 23'] -= df.loc[column,'22 ~ 23']
                   Time['23 ~ 24'] -= df.loc[column,'23 ~ 24']
                   Time['00 ~ 01'] -= df.loc[column,'00 ~ 01']
        if(num in['2019-01-05','2019-01-12','2019-01-19','2019-01-26','2019-02-02',
        '2019-02-09','2019-02-16','2019-02-23']): #1월,2월의 토요일들
            if(df.loc[column,'구분']=='승차'):
                Week['Saturday'] += df.loc[column,'합 계']
                if(user_day == '토' or '토요일'):
                    Time['05 ~ 06'] += df.loc[column,'05 ~ 06']
                    Time['06 ~ 07'] += df.loc[column,'06 ~ 07']
                    Time['07 ~ 08'] += df.loc[column,'07 ~ 08']
                    Time['08 ~ 09'] += df.loc[column,'08 ~ 09']
                    Time['09 ~ 10'] += df.loc[column,'09 ~ 10']
                    Time['10 ~ 11'] += df.loc[column,'10 ~ 11']
                    Time['11 ~ 12'] += df.loc[column,'11 ~ 12']
                    Time['12 ~ 13'] += df.loc[column,'12 ~ 13']
                    Time['13 ~ 14'] += df.loc[column,'13 ~ 14']
                    Time['14 ~ 15'] += df.loc[column,'14 ~ 15']
                    Time['15 ~ 16'] += df.loc[column,'15 ~ 16']
                    Time['16 ~ 17'] += df.loc[column,'16 ~ 17']
                    Time['17 ~ 18'] += df.loc[column,'17 ~ 18']
                    Time['18 ~ 19'] += df.loc[column,'18 ~ 19']
                    Time['19 ~ 20'] += df.loc[column,'19 ~ 20']
                    Time['20 ~ 21'] += df.loc[column,'20 ~ 21']
                    Time['21 ~ 22'] += df.loc[column,'21 ~ 22']
                    Time['22 ~ 23'] += df.loc[column,'22 ~ 23']
                    Time['23 ~ 24'] += df.loc[column,'23 ~ 24']
                    Time['00 ~ 01'] += df.loc[column,'00 ~ 01']

            elif(df.loc[column,'구분']=='하차'):
                Week['Saturday'] -= df.loc[column,'합 계']
                if(user_day == '토' or '토요일'):
                   Time['05 ~ 06'] -= df.loc[column,'05 ~ 06']
                   Time['06 ~ 07'] -= df.loc[column,'06 ~ 07']
                   Time['07 ~ 08'] -= df.loc[column,'07 ~ 08']
                   Time['08 ~ 09'] -= df.loc[column,'08 ~ 09']
                   Time['09 ~ 10'] -= df.loc[column,'09 ~ 10']
                   Time['10 ~ 11'] -= df.loc[column,'10 ~ 11']
                   Time['11 ~ 12'] -= df.loc[column,'11 ~ 12']
                   Time['12 ~ 13'] -= df.loc[column,'12 ~ 13']
                   Time['13 ~ 14'] -= df.loc[column,'13 ~ 14']
                   Time['14 ~ 15'] -= df.loc[column,'14 ~ 15']
                   Time['15 ~ 16'] -= df.loc[column,'15 ~ 16']
                   Time['16 ~ 17'] -= df.loc[column,'16 ~ 17']
                   Time['17 ~ 18'] -= df.loc[column,'17 ~ 18']
                   Time['18 ~ 19'] -= df.loc[column,'18 ~ 19']
                   Time['19 ~ 20'] -= df.loc[column,'19 ~ 20']
                   Time['20 ~ 21'] -= df.loc[column,'20 ~ 21']
                   Time['21 ~ 22'] -= df.loc[column,'21 ~ 22']
                   Time['22 ~ 23'] -= df.loc[column,'22 ~ 23']
                   Time['23 ~ 24'] -= df.loc[column,'23 ~ 24']
                   Time['00 ~ 01'] -= df.loc[column,'00 ~ 01']
        if(num in['2019-01-06','2019-01-13','2019-01-20','2019-01-27','2019-02-03',
        '2019-02-10','2019-02-17','2019-02-24']): #1월,2월의 일요일들
            if(df.loc[column,'구분']=='승차'):
                Week['Sunday'] += df.loc[column,'합 계']
                if(user_day == '일' or '일요일'):
                    Time['05 ~ 06'] += df.loc[column,'05 ~ 06']
                    Time['06 ~ 07'] += df.loc[column,'06 ~ 07']
                    Time['07 ~ 08'] += df.loc[column,'07 ~ 08']
                    Time['08 ~ 09'] += df.loc[column,'08 ~ 09']
                    Time['09 ~ 10'] += df.loc[column,'09 ~ 10']
                    Time['10 ~ 11'] += df.loc[column,'10 ~ 11']
                    Time['11 ~ 12'] += df.loc[column,'11 ~ 12']
                    Time['12 ~ 13'] += df.loc[column,'12 ~ 13']
                    Time['13 ~ 14'] += df.loc[column,'13 ~ 14']
                    Time['14 ~ 15'] += df.loc[column,'14 ~ 15']
                    Time['15 ~ 16'] += df.loc[column,'15 ~ 16']
                    Time['16 ~ 17'] += df.loc[column,'16 ~ 17']
                    Time['17 ~ 18'] += df.loc[column,'17 ~ 18']
                    Time['18 ~ 19'] += df.loc[column,'18 ~ 19']
                    Time['19 ~ 20'] += df.loc[column,'19 ~ 20']
                    Time['20 ~ 21'] += df.loc[column,'20 ~ 21']
                    Time['21 ~ 22'] += df.loc[column,'21 ~ 22']
                    Time['22 ~ 23'] += df.loc[column,'22 ~ 23']
                    Time['23 ~ 24'] += df.loc[column,'23 ~ 24']
                    Time['00 ~ 01'] += df.loc[column,'00 ~ 01']

            elif(df.loc[column,'구분']=='하차'):
                Week['Sunday'] -= df.loc[column,'합 계']
                if(user_day == '일' or '일요일'):
                   Time['05 ~ 06'] -= df.loc[column,'05 ~ 06']
                   Time['06 ~ 07'] -= df.loc[column,'06 ~ 07']
                   Time['07 ~ 08'] -= df.loc[column,'07 ~ 08']
                   Time['08 ~ 09'] -= df.loc[column,'08 ~ 09']
                   Time['09 ~ 10'] -= df.loc[column,'09 ~ 10']
                   Time['10 ~ 11'] -= df.loc[column,'10 ~ 11']
                   Time['11 ~ 12'] -= df.loc[column,'11 ~ 12']
                   Time['12 ~ 13'] -= df.loc[column,'12 ~ 13']
                   Time['13 ~ 14'] -= df.loc[column,'13 ~ 14']
                   Time['14 ~ 15'] -= df.loc[column,'14 ~ 15']
                   Time['15 ~ 16'] -= df.loc[column,'15 ~ 16']
                   Time['16 ~ 17'] -= df.loc[column,'16 ~ 17']
                   Time['17 ~ 18'] -= df.loc[column,'17 ~ 18']
                   Time['18 ~ 19'] -= df.loc[column,'18 ~ 19']
                   Time['19 ~ 20'] -= df.loc[column,'19 ~ 20']
                   Time['20 ~ 21'] -= df.loc[column,'20 ~ 21']
                   Time['21 ~ 22'] -= df.loc[column,'21 ~ 22']
                   Time['22 ~ 23'] -= df.loc[column,'22 ~ 23']
                   Time['23 ~ 24'] -= df.loc[column,'23 ~ 24']
                   Time['00 ~ 01'] -= df.loc[column,'00 ~ 01']


    return((Week.values()),list(Time.values())) #첫번째 그래프의 x 값이 된다



if __name__ == '__main__':
    win = Tk()

    win.title("기말 과제_201624462")
    win.geometry("1550x700+200+150")
    win.resizable(True,True)

    ents = makeform(win, fields)

    win.bind('<Return>', (lambda event, e = ents: fetch(e)))
    b1 = Button(win, text='Show',command=(lambda e=ents: fetch(e)))
    b1.pack(side=TOP, padx=0,anchor = "w",ipadx=90)
    b2 = Button(master=win, text="Quit", command=_quit)
    b2.pack(side=TOP, padx=0,anchor="w",ipadx=94)

    name_display(win)

    win.mainloop()
~~~
