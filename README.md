# comp_pro
boo


import sys
import matplotlib.pyplot as plt
import mysql.connector
import numpy as np
mycon=mysql.connector.connect(host='localhost',user='root',
                              password='1234',database='exam')

mycur=mycon.cursor(buffered=True)

def Student_Profile():
    print('\nPLEASE PROVIDE THE REQUIRED INFORMATION\n')
    ad=int(input('\nENTER THE ADMISSION NUMBER TO REGISTER FOR EXAM:'))
    nm=input('\nENTER THE STUDENT NAME:')
    cls=int(input('\nENTER THE CLASS(11/12):'))
    sec=input('\nENTER THE SECTION(A-D):')
    sql="Insert into student values({0},'{1}',{2},'{3}');".format(ad,nm,cls,sec)

    try:
        mycur.execute(sql)
        print(nm,'ADDED SUCCESSFULLY TO EXAM MODULE')
        mycon.commit()
    except:
        print('UNABLE TO INSERT!!!!!')



def Edit_Profile():
    ph=int(input('\nENTER THE ADMISSION NUMBER WHOSE SECTION TO MODIFY:'))
    nm=input('\nENTER THE NEW SECTION(A-D):')
    sql="Update student set section='{0}' where Admission_No={1};".format(nm,ph)
    try:
        mycur.execute(sql)
        mycon.commit()
        print('RECORD UPDATED SUCCESSFULLY')
    except:
        print('UNABLE TO UPDATE SECTION!!!!')



def Remove_Profile():

    #you can delete by exam name and adm_no#do if sir asks
    ph=int(input('\nENTER THE ADMISSION NUMBER TO DELETE:'))
    sql="Delete from student where Admission_No={};".format(ph)
    try:
        mycur.execute(sql)
        mycon.commit()
        print('RECORD DELETED SUCCESSFULLY')
    except:
        mycon.rollback()
        print('UNABLE TO DELETE RECORD!!!')



def Record_Entry():

    #presence of primary key in result table means that there cannot be multiple record entries of the same student in result, which means multiple exams
    #cannot be store in the table for one student, only one exam can be stored for one student
    #SOLUTION:remove primary key from adm_no from result but foreign key remains#done

    print('\nPLEASE PROVIDE THE REQUIRED INFORMATION\n')
    z=1
    while z==1:        
        ad=int(input('\nENTER THE ADMISSION NUMBER TO ENTER RECORD:'))
        sqlp="SELECT * FROM student WHERE admission_no={};".format(ad)#this is doneeeeee
        mycur.execute(sqlp)
        row = mycur.fetchone()
        if row == None:
            print("Admission No. doesn't exist")
            
            mycon.rollback()

        else:
            z=2
            mycon.commit()

    # notifies that adm_no isn't in student after typing marks. can be notified here itself#done

    mycur.execute("select distinct exam_name from result;")
    pq=mycur.fetchall()
    print(pq)
    nm=input('\nENTER THE EXAM NAME')


    #You can display already entered exam names here from result table#done

    sub1=int(input('ENTER MARKS IN SUBJECT 1(MAX:100):'))
    sub2=int(input('ENTER MARKS IN SUBJECT 2(MAX:100):'))
    sub3=int(input('ENTER MARKS IN SUBJECT 3(MAX:100):'))
    sub4=int(input('ENTER MARKS IN SUBJECT 4(MAX:100):'))
    sub5=int(input('ENTER MARKS IN SUBJECT 5(MAX:100):'))

    # no limit on the number of marks put here ( add a condition)#do if sir asks
    
    total=sub1+sub2+sub3+sub4+sub5
    per=total//5
    wrkday=int(input('ENTER TOTAL NUMBER OF WORKING DAYS:'))
    present=int(input('ENTER NO OF DAYS PRESENT:'))
    att=present/wrkday*100
    att=int(att)
    if(per>=90):
        g='A'
        rem='EXCELLENT PERFORMANCE!!'
    elif(per>=75 and per<90):
        g='B'
        rem='VERY GOOD PERFORMANCE!!'
    elif(per>=55 and per<=75):
        g='C'
        rem='SATISFACTORY PERFORMANCE!!'
    elif(per>=35 and per<55):
        g='D'
        rem='AVERAGE PERFORMANCE!!'
    else:
        g='E'
        rem='SCOPE FOR IMPROVEMENT!!'
    sql="Insert into result values({0},'{1}',{2},{3},{4},{5},{6},{7},{8},{9},'{10}','{11}');".format(ad,nm,sub1,sub2,sub3,sub4,sub5,total,per,att,g,rem)
    print(sql)
    try:
        mycur.execute(sql)
        print('RECORD ADDED SUCCESSFULLY TO EXAM MODULE')
        mycon.commit()        
    except:
        print('UNABLE TO INSERT!!!!!\nEnsure that student profile is created')


def Report_Card():
    # find a way to display multiple exams
    ad=int(input('\nENTER THE ADMISSION NUMBER TO SEARCH:'))
    boss='select count(*) from result where admission_no={};'.format(ad)
    mycur.execute(boss)
    ans=mycur.fetchone()
    print(ans[0])
    
    sql1='Select * from student where admission_no={};'.format(ad)
    mycur.execute(sql1)

    rec1=mycur.fetchone()
    if(rec1!=None):
           adm=rec1[0]
           name=rec1[1]
           cls=rec1[2]
           sec=rec1[3]

    sql2='Select * from result where admission_no={};'.format(ad)
    mycur.execute(sql2)
    #since primary key exists in student this above line need not be repeatedly done
    #only result can have multiple records with same adm_no
    
    
    

    # loop corresponding to cardinality?
    #YESSS IT WORKE#done
    print('\n\n--------REPORT CARD OF',name,'----------\n\n')      
    print('\nCLASS-',cls,'SECTION-',sec,'\n')
    
    for i in range(0,ans[0]):


        
        rec2=mycur.fetchone()
        if(rec2!=None):
               adm=rec2[0]
               exname=rec2[1]
               sub1=rec2[2]
               sub2=rec2[3]
               sub3=rec2[4]
               sub4=rec2[5]
               sub5=rec2[6]
               total=rec2[7]
               per=rec2[8]
               att=rec2[9]
               g=rec2[10]
               rem=rec2[11]
        if(rec1==None and rec2==None):
             print('WRONG ADMISSION NUMBER GIVEN!!!!!!')

        #a nicer way of showing the marks of different exams     
        else:
             print('\n------------------------------\n')
             print('\nRESULT OF',exname,'\n')
             print('\n------------------------------\n')
             if(sec=='A'):
                 print('\n ENGLISH    : ',sub1)
                 print('\n HISTORY    : ',sub2)
                 print('\n POL. SC    : ',sub3)
                 print('\n ECONOMICS  : ',sub4)
                 print('\n GEOGRAPHY  : ',sub5)
                 print('\n TOTAL      : ',total)
                 print('\n PERCENTAGE : ',per)
                 print('\n ATTENDANCE : ',att,'%')
                 print('\n GRADE      : ',g)
                 print('\n REMAKS     : ',rem)
             elif(sec=='B'):
                 print('\n ENGLISH    : ',sub1)
                 print('\n ACCOUNTANCY: ',sub2)
                 print('\n B.STUDIES  : ',sub3)
                 print('\n ECONOMICS  : ',sub4)
                 print('\n INFO.PRAC  : ',sub5)
                 print('\n TOTAL      : ',total)
                 print('\n PERCENTAGE : ',per)
                 print('\n ATTENDANCE : ',att,'%')
                 print('\n GRADE      : ',g)
                 print('\n REMAKS     : ',rem)
             elif(sec=='C'):
                  print('\n ENGLISH    : ',sub1)
                  print('\n PHYSICS    : ',sub2)
                  print('\n COMP.SC    : ',sub3)
                  print('\n CHEMISTRY  : ',sub4)
                  print('\n MATHEMATICS: ',sub5)
                  print('\n TOTAL      : ',total)
                  print('\n PERCENTAGE : ',per)
                  print('\n ATTENDANCE : ',att,'%')
                  print('\n GRADE      : ',g)
                  print('\n REMAKS     : ',rem)
             elif(sec=='D'):
                  print('\n ENGLISH    : ',sub1)
                  print('\n PHYSICS    : ',sub2)
                  print('\n BIO.SC    : ',sub3)
                  print('\n CHEMISTRY  : ',sub4)
                  print('\n MATHEMATICS: ',sub5)
                  print('\n TOTAL      : ',total)
                  print('\n PERCENTAGE : ',per)
                  print('\n ATTENDANCE : ',att,'%')
                  print('\n GRADE      : ',g)
                  print('\n REMAKS     : ',rem)


def Remove_Record():
    ph=input('\nENTER THE ADMISSION NUMBER TO DELETE:')
    sql='Delete from RESULT where Admission_no={};'.format(ph)
    try:
        mycur.execute(sql)
        mycon.commit()
        print('RECORD DELETED SUCCESSFULLY')
    except:
        mycon.rollback()
        print('UNABLE TO DELETE RECORD!!!')

def Graph():
    ad=int(input('\nENTER THE ADMISSION NUMBER TO SEARCH:'))
    #view exam wise or all together
    lmn=int(input('Type \n1 for Exam-wise \n2 for all together\n:'))

    w=0
    # put a3 options to 1)examwise 2)all together 3)exit
    if lmn==1:#donee
        while w==0:
            df='select exam_name from result where admission_no={}'.format(ad)
            mycur.execute(df)
            po=mycur.fetchall()
            for j in range (0,len(po)):
                print(j+1,')',po[j][0])
            vb=int(input("choose exam:"))
            sql="select * from result where admission_no={} and exam_name='{}';".format(ad,po[vb-1][0])
            mycur.execute(sql)
            T=mycur.fetchone()
            L=[T[2],T[3],T[4],T[5],T[6]]

            sql2='Select section from student where admission_no={};'.format(ad)

            mycur.execute(sql2)
            s=mycur.fetchone()
            
            sec=s[0]
            if(sec=='A'):
             sub1,sub2,sub3,sub4,sub5='English','History','Pol.Sc','Economics','Geography'
            elif(sec=='B'):
             sub1,sub2,sub3,sub4,sub5='English','Accountancy','B.Studies','Economics','Info.Practices'
            elif(sec=='C'):
             sub1,sub2,sub3,sub4,sub5='English','Physics','Computer Sc.','Chemistry','Mathematics'    
            elif(sec=='D'):
             sub1,sub2,sub3,sub4,sub5='English','Physics','Biology','Chemistry','Mathematics'
            sub=[sub1,sub2,sub3,sub4,sub5]
            clr=('red','green','blue','orange','brown')
            width=0.5
            plt.bar(sub,L,width,color=clr)
            
            plt.xlabel('Subjects')
            plt.ylabel('Marks')
            plt.yticks(np.arange(0, 101, 10))
            plt.title(po[vb-1][0])
            plt.show()

            yn=int(input('Do You want to see other exams or no\n1)yes\n2)no\n:'))
            if yn==1:
                w=0
            if yn==2:
                w=1
                break

    if lmn==2:#doneeee
        sql='select * from result where admission_no={}'.format(ad)
        mycur.execute(sql)
        T=mycur.fetchall()


        sql2='Select section from student where admission_no={};'.format(ad)

        mycur.execute(sql2)
        s=mycur.fetchone()
        
        sec=s[0]
        if(sec=='A'):
         sub1,sub2,sub3,sub4,sub5='English','History','Pol.Sc','Economics','Geography'
        elif(sec=='B'):
         sub1,sub2,sub3,sub4,sub5='English','Accountancy','B.Studies','Economics','Info.Practices'
        elif(sec=='C'):
         sub1,sub2,sub3,sub4,sub5='English','Physics','Computer Sc.','Chemistry','Mathematics'    
        elif(sec=='D'):
         sub1,sub2,sub3,sub4,sub5='English','Physics','Biology','Chemistry','Mathematics'
        sub=[sub1,sub2,sub3,sub4,sub5]
        
        
        x = np.arange(5)  # the label locations
        width = 0.2  # the width of the bars
        multiplier = 0
        mult=0

        boss='select count(*) from result where admission_no={};'.format(ad)
        mycur.execute(boss)
        ans=mycur.fetchone()
        
        for i in range(0,ans[0]):
            
            
            L=[T[i][2],T[i][3],T[i][4],T[i][5],T[i][6]]
            x=mult+x
            plt.bar(x,L,width)
            mult=0.2
            
        df='select exam_name from result where admission_no={}'.format(ad)
        mycur.execute(df)
        po=mycur.fetchall()
        
        plt.xticks(x,sub)
        plt.xlabel("Subjects")
        plt.ylabel("Marks")
        plt.legend([po][0])
        plt.show()
    



     
def Close():

    print('\nTHANK YOU FOR USING THE APPLICATION')
    sys.exit()


def teacher():
    print('-----------WELCOME TO EXAMINATION MODULE  SYSTEM FOR CLASS-XI & XII-------------\n\n')
    while(True):
        print('\n\nPRESS 1 TO CREATE A STUDENT PROFILE')
        print('PRESS 2 TO EDIT A STUDENT PROFILE')
        print('PRESS 3 TO DELETE A STUDENT PROFILE')
        print('PRESS 4 FOR MARKS AND ATTENDANCE ENTRY')
        print('PRESS 5 TO GENERATE REPORT CARD')
        print('PRESS 6 TO DELETE MARKS DETAILS')
        print('PRESS 7 TO PRODUCE A GRAPH PERFORMANCE')
        print('PRESS 8 TO CLOSE THE APPLICATION')
        choice=int(input('ENTER YOUR CHOICE : '))
        if(choice==1):
            Student_Profile()
        elif(choice==2):
            Edit_Profile()
        elif(choice==3):
            Remove_Profile()
        elif(choice==4):
            Record_Entry()
        elif(choice==5):
            Report_Card()
        elif(choice==6):
            Remove_Record()
        elif(choice==7):
            Graph()
        elif(choice==8):
            Close()


def student():

    # make more user interactive
    # add an option to view exam-wise#done
    # learn about the graph and add different options like edumerge, rip-off#done
    print("Welcome to ..>>*name* ")#add message here
    Report_Card()
    t=input("Want to see graph")
    if t=="y":
        Graph()
    
             
q=0
print("Are you teacher or student")
ts=int(input("1 for teacher\n2 for student\n"))
if ts==1:
    while q==0:
        pl=input("Enter Password:")
        if pl=="qwerty":
            q=2
            teacher()
        else:
            print("Wrong Password\nTry Again")
elif ts==2:
    student()
    
    

    
        
           
    
















