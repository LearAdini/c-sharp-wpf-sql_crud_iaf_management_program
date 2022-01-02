
# Midterm Project

*Using:*

● **WPF**

● **EntityFramework Core FluentAPI**

● **ADO.NET Stored Procedures**

● **Hashing registration data**

● **MainMigration.cs**
> generating the register user, login validation, email verify, and reset password stored procedures
>
> generating 50 new rows into soldiers & orders tables                                   
> (while generated the system can tell if a soldier is male or female by his/her name and if a soldier is in permanent service by their rank and role)

● **Admin privileges**

> UserName: admin
> 
> Password: admin

● **Reset password via SMTP sending confirmation code**

● **State Machine Pattern**

● **Use ***```update-database```*** before build/debug (Tools>NuGet Package Manager>Package Manager Console> type: *update-database*)**


> **הערה חשובה**
> 
>
> בשני המחשבים שלי לא נתקלתי בבעיה הזאת, כשניסיתי על מחשב אחר את הפרויקט כל האותיות בעברית ברשומות של החיילים והזמנות היו כסימני שאלה, יצרתי גיף שמדגים איך יצירת הרשומות מתחוללת ללא בעיה עם עברית במחשב שלי ב[סוף הדף](#update-database)
>
> **במידה וזה המצב יש הסבר [כאן](#Fix-Hebrew-Letters) שמדגים איך לשנות את הרשומות מעברית לאנגלית**
>
> אם לאחר update-database מופיע השגיאה `.parameters describe an un-representable DateTime`  [לחץ כאן](#Fix-DateTime-Error)
> 
> אם לאחר update-database מופיע השגיאה ` 6.0.0is not compatible with net5.0-windows7.0` או אם במידה ואתה משתמש ב **Visual Studio 2022** אנא היכנס [לכאן](https://aka.ms/dotnet-core-applaunch?framework=Microsoft.WindowsDesktop.App&framework_version=5.0.0&arch=x64&rid=win10-x64)  בכדי להוריד זמן ריצה של NET 5.  והורד על פי מפרט המערכת שלך מ Run desktop apps  


I document here my midterm project, which where you can register into the database using hashing queries, login with a validation procedure and
connect to my app where you can do CRUD operations on soldiers/orders tables in the database.

While using the pmc command, the migration file will generate 50 random soldiers and 50 random orders.


You can also view all aircrafts in use in the IAF and all the operating squadrons.
# 
*-hashed registration demonstration & admin user login:*

https://user-images.githubusercontent.com/80118008/144083245-be9d988c-0335-481c-a25f-a6fa188dbda7.mp4

>**note:** *Username in this demo video is only 4 characters long, while in the project updated username to most contain at least one number and one letter, at minimum length of 4 characters.*
>
>*Updated password to be at least one uppercase letter and one lowercase letter, at minimum length of 8 characters.*



# Models
**● SoldierInfo Class**

![si](https://user-images.githubusercontent.com/80118008/144719840-82ca421b-3b3a-4a92-9d42-7240aedda74b.png)

**● OrdersInfo Class**

![oi](https://user-images.githubusercontent.com/80118008/144719835-a8c7a33d-5a85-4493-9a3b-74316c849741.png)

**● LoginInfoHashed Class**

![lih](https://user-images.githubusercontent.com/80118008/144719824-a52e33e9-b3e3-4c41-b33f-5e474d366cb4.png)


# FluentAPI Config

### Configuration Models

Each config model Implements IEntityTypeConfiguration interface to allow configuration for an entity type to be written in a separate class, rather than in OnModelCreating() at SoldiersDBContext.

Using the Configure function which contains an EntityTypeBuilder with a generic <!TEntity>(Entity being the model for use), and for example,
if I want a certain property of a model / a certain column within the table to be my PrimaryKey, I can write modelBuilder.HasKey(p => p._someProperty);

**● Fluent_SoldierConfig Class**

![sconifg](https://user-images.githubusercontent.com/80118008/144740991-6b5fcf29-d3c7-4fc3-b327-45cfd7a502c7.PNG)

**● Fluent_OrdersConfig Class**

![oconfig](https://user-images.githubusercontent.com/80118008/144740918-730bcce6-bccd-4d16-bf4a-fbc30f4cadd0.PNG)

**● Fluent_LoginInfoHashedConfig Class**

![lihconfig](https://user-images.githubusercontent.com/80118008/144740950-b3786c93-afc1-48ce-babc-e8d0929fdc5c.PNG)

### DbContext

Each model is assigned to a DbSet property which is used to query instances of an <!TEntity>.

Connecting to SQL via .UseSqlServer method, configuring the context to connect to SQL database.

In OnModelCreating using modelBuilder to apply the configurations to the database, using the config models that inherit from IEntityTypeConfiguration to apply the configurations set from in the config model to the database.

**● SoldiersDBContext Class**

![DbContext](https://user-images.githubusercontent.com/80118008/144721005-a20ee9e3-82d7-4dbf-b4b7-1677ed2ff987.PNG)


# MainMigration.cs

> *Made custom SQL operations to create the stored procedures & generate random rows into both tables*


## **Stored Procedures** 

**● sp_RegisterUserHash**

![sp_ruh](https://user-images.githubusercontent.com/80118008/144741871-5fe7c339-2b54-42f9-9004-7a0977832997.PNG)


> Inserts into LoginInfoHashed(Table) a username, email, password, and salt.

salt is a unique id for a password, the unique random id 'salt' is generated for each password before it is hashed, each new output is completely different every time.

For example, if 2 users have the same password, in the database they will both have two completely different salt & hashed password strings.

The password input is being hashed using **'MD5'** HASHBYTES function.

-*see* **Registration Data** *below for registration validation query for user registration*

#
**● sp_LoginUserHash**

![sp_luh](https://user-images.githubusercontent.com/80118008/144741874-5ade4266-d00a-4f4a-9bc5-74ed095dc311.PNG)

> Select the count from LoginInfoHashed where UserName & Password match the input userName and password from the user

(if count equals 0 = user doesn't exist in database)

(if count equals 1 = user already exists in database)

In this case, count 1 means access granted for user login.

count 0 cannot login because the user is not registered in the database.

#
**● sp_VerifyEmail**

![sp_ve](https://user-images.githubusercontent.com/80118008/144741878-9c888bc9-a6d1-40e9-840c-ed69114f06e4.PNG)

**● sp_ResetPassword**

![sp_rp](https://user-images.githubusercontent.com/80118008/144741883-1d18e5d2-f406-4d69-b36d-c0d85ef758b0.PNG)


## **Generating Rows in Migration.cs**

> Created a for loop to execute custom operations by using .Sql method, creating a new random row on each iterate of the loop.


 The orders table uses an INNER JOIN query saying: which soldier *(by his id number)* ordered which item.
 
 A soldier can be either male or female, so by his/her name the randomGender function can tell if it is a female or male.
 
 Each soldiers year of recruit will be 18 years after their birth year, for example:
 
 `If a soldier was born in 1991 his/her recruit year will be 2009`.
 
 It can also tell if a soldier is in permanent service by their rank&role, for example:
 
 `If a soldier's role is a 'Pilot' then he must be in permanent service and he cant have a rank of a non ps soldier`.
 
![loop](https://user-images.githubusercontent.com/80118008/144756143-fb1783ef-f767-4aef-a144-47fb1c9b9115.PNG)

-*see* **RandomColumns** *class for all random functions (Utilities  folder)*

# Registration Data

Check if password has at least one uppercase character and is minimum length of 8 *-regex*.

Check if username has at least one letter & number and is minimum length of 4 *-regex*.

Check if username is up to 20 characters.

Check if password is up to 100 characters.

Check if username textbox, password textbox, or email textbox are empty.

Check if password confirmation box is empty.

Check if password input is different from password confirmation input.

Check if email contains '@' or a domain name.

If password is matched with the confirmation password box and if the username textbox is NOT empty start UserCheckAsync().

***UserCheckAsync function:***

First, it will check if username or email you typed in exists in the database or not,

count 0 meaning the username or email you typed in does not exists in the database,

count 1 meaning user name or email already in use.

In this case where we want to register a user, count 0 will create a new user using *sp_RegisterUserHash*,

because theres no one registered with the given email or username..

![registerpage](https://user-images.githubusercontent.com/80118008/144762242-c3d5684a-fec4-406d-99f9-4592fc421c93.png)


# Login Data


using *sp_LoginUserHash* stored procedure,

if count equals 1 with the information you typed in, meaning user exists and can log into guest page.

if username & password is 'admin' log into admin page.

if count equals 0 user is not in the database and can create a new one but cannot log in with the count of 0.

![loginpage](https://user-images.githubusercontent.com/80118008/144762250-afb0bd4f-df23-462f-8024-9265c8015066.png)

# Reset Password Data

### ● GetCodePage

using *sp_verifyEmail* stored procedure, to check if the input email exist in the database.

if count equals 1 email exist in the database.

if count equals 0 email does not exist in the database.

it will use the sendEmail function from EmailConfirmation class and will open the reset password page only if count is 1.

![checkemail](https://user-images.githubusercontent.com/80118008/144763554-93850e22-f6b2-42a3-8d97-0860df30f6c8.png)

### ● EmailConfirmation

Declared the SMTP connection settings, sending a message from the projects email with a random code which will be the confirmation code to reset the password.

![codepage](https://user-images.githubusercontent.com/80118008/143883903-50fcc5e4-7996-49ed-96b9-f0939c9d5316.PNG)

### ● ResetPassword

When update password button is clicked,

Check if password have at least one uppercase & lowercase character and if its at least 8 characters long.

Check if password box is empty.

Check if password box match with password confirmation.

Check if confirmation code is correct, if so run resetPasswordAsync().

![rp0](https://user-images.githubusercontent.com/80118008/144762289-57327094-dc70-4974-b357-2c198c555ca1.PNG)

***resetPasswordAsnyc function:***

Will run sp_ResetPassword stored procedure, and will update the password of the given email and of course before we ran the count query, you would not be able to get to this page if count was 0 in GetCodePage.

![rp2](https://user-images.githubusercontent.com/80118008/144762294-6b817ce3-fd6f-4656-98d4-bb5c02dcfc33.PNG)

# Data Access Layer

**● DAL Class**

![dalpage](https://user-images.githubusercontent.com/80118008/144720988-35a29754-faff-4411-901b-a40d5e4d6e49.png)


# SQL Stored Procedures

**● sp_RegisterUserHash**

![sp_RegisterUserHash](https://user-images.githubusercontent.com/80118008/139083455-e0b59e26-c137-479a-80ee-1ac80c88986f.PNG)

**● sp_LoginUserHash**

![sp_LoginUserHash](https://user-images.githubusercontent.com/80118008/139083316-1fa4ac07-a2fb-48a6-8e17-55ac1326a5e7.PNG)

#

### update-database
*update-database in PMC:*

![updatedatabase](https://user-images.githubusercontent.com/80118008/139591185-f209d663-5aee-4a6b-9f91-36d6e3e1bb85.gif)

#
# Errors & Fixes

### Fix-Hebrew-Letters
*Change Hebrew rows to English in MainMigration.cs*

![FIX](https://user-images.githubusercontent.com/80118008/144637681-fcc67b0e-3a05-4c15-8183-88e1e20402b3.PNG)

#

### Fix-DateTime-Error

![fixxgif](https://user-images.githubusercontent.com/80118008/143461383-218fd09e-62ed-4c22-9980-5a22881b6649.gif)

If after update-database you get the following error: `parameters describe an un-representable DateTime.` 
> ***It makes sense if you didnt get this error on first hand, it should only occur if soldiers birthdate month is in February and if the days were higher than 28 (except when in Leap Year). 
> 
> Heres the fix:***
1. Refresh Databases in SSMS and delete DbSoldiers.
2. Run update-database again (if error occurs again please delete DbSoldiers(between 1-3 times) and migration should be applied afterward.

*I came across this error when I made the random birthday of a soldier saying if a soldier was born in year x he should be recruited on year y:*
```
if(giusDate.Year == 2009)
{                                           //changed maximum r.Next days to 29 to avoid this error in the project.
birthDate = new DateTime(1991, r.Next(1, 12), r.Next(1, 31)); //Israeli citizens recruit to the army when they are 18
return birthDate;
}
```
*-Error indicates on RandomBirthDay function in RandomColumns.cs for each year of recruit(2009 <= 2018) the operation makes the soldier birth date to be 18 years less than the date of recruit.*

