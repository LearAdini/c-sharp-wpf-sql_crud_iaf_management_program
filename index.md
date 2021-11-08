
# Midterm Project

*Using:*

● WPF 

● Hashing registration data

● EntityFramework FluentAPI

● SQL Querys & Stored Procedures

● MainMigration.cs *generating the register and login stored procedures , generates 20 new random columns into soliders&orderes tables*

● Login validation

● Admin privileges  

● UML State Machine

● Use ***`update-database`*** before build/debug (Tools>NuGet Package Manager>Package Manager Console>type: *update-database*)

***Download: send email***

> **vs הערה חשובה לבוחן, במידה וכל הרשומות מופיעות עם סימני שאלה זה מכיוון שאין תמיכה בעברית דרך*****

> **במידה וזה המצב בבקשה כתוב באנגלית אם תרצה להוסיף/למחוק/לעדכן**

> **במחשב שלי לא נתקלתי בבעיה הזאת, כשניסיתי על מחשב אחר עם וויז'ואל סטודיו חדש כל האותיות בעברית ברשומות היו כסימני שאלה,יצרתי גיף שמדגים איך זה מתחולל ללא בעיה עם עברית במחשב שלי (סוף הדף)*****


I document here my middle year project which where you can register into the data base using hashing querys, login with a validation procedure and
connect to my app where you can add soldiers/orders into the data base.

While using the pmc command, the migration file will generate 20 random soldiers and 20 random orders


You can also view all aircrafts in use in the IAF and all the operating squadrons.
# 
*-encrypted registration demonstration:*

![final](https://user-images.githubusercontent.com/80118008/139539477-992615c3-bc6b-445d-8f51-60268955677a.gif)


## Models
**● Fluent_SoldierInfo Class**

![fclass](https://user-images.githubusercontent.com/80118008/139536692-b918aaa6-5013-4c3b-898c-02a063fbb2a3.PNG)

**● Fluent_OrdersInfo Class**

![oclass](https://user-images.githubusercontent.com/80118008/139536206-c05d55c7-aee5-4977-ba8d-6b00347ddfe8.PNG)

**● Fluent_LoginInfoHashed Class**

![fli](https://user-images.githubusercontent.com/80118008/139403495-3cf6a74c-eea3-4b4f-accd-4038368b7c08.PNG)


# Fluent Api Config

The new context classes are located under FluentConfig folder,They will inherit from IEntityTypeConfiguration interface
and use the Configure function which contains an EntityTypeBuilder with a generic object (the class for configuration), and now I can start using the fluent code,for example if I want "שם__פרטי" to be my PK I can write objectName.HasKey(p => p.שם__פרטי);

**● Fluent_SoldierContext Class**

![scontext](https://user-images.githubusercontent.com/80118008/139536712-d571159a-8635-4066-9f40-e317f19dcbb8.PNG)


**● Fluent_OrdersContext Class**

![ocontext](https://user-images.githubusercontent.com/80118008/139536241-7327eea6-9f5f-4d85-be86-dbd2f6d33846.PNG)

**● Fluent_LoginInfoHashedContext Class**

![fluentlogininfocontext](https://user-images.githubusercontent.com/80118008/139403651-1eef49fc-d581-4153-a1c2-0df98bdfc5e6.PNG)

# MainMigration.cs

> *Made raw SQL execution codes to create the stored procedures & generate random columns in both tables*


## **Stored Procedures** 

**● sp_RegisterUserHash**

![b](https://user-images.githubusercontent.com/80118008/139836126-45eb7d0a-635d-401f-af29-8f51e6e9ede5.PNG)


> Inserts into Fluent_LoginInfoHashed (Table) a user name , password and salt.

salt is a unique id for a specefic column, the unique id is generated randomly and each new output is completly diffrenet every time,

for example if 2 users have the same password, in the data base they will have completly diffrenet hashed password strings stored inside the database.

*-see Registration Data below for the registration query validation for user registration*


**● sp_LoginUserHash**


![createsploginhashedmig](https://user-images.githubusercontent.com/80118008/139395427-a1a0d660-0293-4953-abdf-7ac490b4c227.PNG)

> Select the count from LoginInfoHashed where UserName&Password matches the inputted userName and password from the user

(if count is 0 = user doesnt exists in database)

(if count is 1 = user already exists in database)

In this case, count 1 means access granted for user login

count 0 cannot login because user is not registered in the database

#

**● Generating columns**

> Created a for loop to execute raw sql codes, each modelbuilder in the scope is generating 20 random columns into Soldier & Order tables

 The orders table uses an INNER JOIN query saying: which soldier *(by his id number)* ordered which item

![forloop](https://user-images.githubusercontent.com/80118008/139536805-206809a1-0219-4a8c-a9da-8146bc501a5a.PNG)

-*see RandomColumns class for all random functions (Utilites folder)*


# Registration Data


![139132078-de46deff-2c10-43b9-8926-f6223e649163](https://user-images.githubusercontent.com/80118008/140164659-9d6a8b0b-89ff-4abe-9930-0e6f95881f0d.gif)

-*using the stored procedure sp_RegisterUserHash*

First I check if the username textbox is empty or if password box is empty.

Check if password confirmation box is empty.

Check if the password box input is diffrenet from the confirmation password box.

If the password box is matched with the confirmation password box and if the username textbox is NOT empty

start the UserCheckAsync function.


UserCheckAsync function:

First it runs a query which will tell if count equals to 0 or 1

0 meaning the username you typed in does not exists in the database and 1 meaning already exists,

In this case where we want to register a user, count 0 will create a new user because theres no one using that user name

# Login Data

![loginpageblur](https://user-images.githubusercontent.com/80118008/139132038-c4c96bcf-1fa6-4846-a89e-2653d47e5650.gif)
*using stored procedure sp_LoginUserHash (see SQL Stored Procedures)*

if count is 1(the amount of users in the database) with the information you type in meaning the user exists and can log in.

if count is 0 user is not in the database and can create a new one but cannot log in with a count of 0.

# Data Access Layer

**● DbSoldiersContext Class**

The DbSoldiersContext class is connecting to SQL via the OnConfiguring method which contains a DbContextOptionsBuilder object where I can connect into the database. It will use the OnModelCreating method which contains a ModelBuilder object which will apply configurations to the database so in the code I am using the FluentConfig classes that I made before.

![soldiersdbcontext](https://user-images.githubusercontent.com/80118008/139403732-bd567f63-15ef-4ac1-9a81-ddd98c67bd70.PNG)


**● DAL Class**

![DALpage](https://user-images.githubusercontent.com/80118008/139084660-37600585-2272-4e71-9f86-8b0dedfc6694.png)



# SQL Stored Procedures

**● sp_RegisterUserHash**

![sp_RegisterUserHash](https://user-images.githubusercontent.com/80118008/139083455-e0b59e26-c137-479a-80ee-1ac80c88986f.PNG)

**● sp_LoginUserHash**

![sp_LoginUserHash](https://user-images.githubusercontent.com/80118008/139083316-1fa4ac07-a2fb-48a6-8e17-55ac1326a5e7.PNG)



# Images

*-Info Page:*

![Capture](https://user-images.githubusercontent.com/80118008/139078753-81f408d6-c262-42fe-baa4-a3b981b0d0f8.PNG)

*-Soldier's List:*

![soliderlist](https://user-images.githubusercontent.com/80118008/139078910-c9ed3cae-860a-4213-95db-fae3e0b7f874.PNG)

*-Add Soldiers:*

![addsoldier](https://user-images.githubusercontent.com/80118008/139078763-61836081-af2d-4677-9c3f-35f9206f8a3d.PNG)

*-Update Soldiers:*

![updatesoldier](https://user-images.githubusercontent.com/80118008/139078802-72c3b122-7571-42e3-8741-b0d69838e9ff.PNG)

*-Delete Soldiers:*

![deletesoldier](https://user-images.githubusercontent.com/80118008/139078820-fd218086-bff6-4f74-9ea4-70631f52c491.PNG)

*-Order's List:*

![orders](https://user-images.githubusercontent.com/80118008/139078929-8f25659a-d431-420f-bae9-4b8c6057ddde.PNG)

*-Add Order:*

![neworder](https://user-images.githubusercontent.com/80118008/139078858-99e943ed-8049-47ac-b6b3-05df0a316ad2.PNG)

*-Update & Delete Order:*

![updatendeleteorder](https://user-images.githubusercontent.com/80118008/139078957-0899e1af-d4cc-4dcd-82c2-85f828752a72.PNG)

*-Squadron's Page:*

![tyasotpage](https://user-images.githubusercontent.com/80118008/139078978-62418d42-d1d7-4dcd-b17a-a9645fb6eb50.gif)

*-Aircraft's Page:*

![aircraftspage](https://user-images.githubusercontent.com/80118008/139079008-0a601742-74d4-4546-a570-b56eebe84ad2.gif)



*-Update-database with PMC:*

![updatedatabase](https://user-images.githubusercontent.com/80118008/139591185-f209d663-5aee-4a6b-9f91-36d6e3e1bb85.gif)


