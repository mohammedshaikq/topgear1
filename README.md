# Different Instance of ISE with NATed IP

This issue happens because during successful login, ISE creates a cookie name APPSESSIONID and a value. The session value is stored in a session database. The browser will send the cookie in every request, ISE will check the value of APPSESSIONID in the database, if it valid then only the page will be served. This cookie will be invalidated during logout.

![image](https://user-images.githubusercontent.com/77893857/192778005-6158e171-64cc-481c-aac7-87b5def85e2a.png)

Now, in case of NATed IP multiple instances of ISE will be assigned same IP but different port.
For example: 
ISE1  10.105.238.51
ISE2  10.105.238.52 


![image](https://user-images.githubusercontent.com/77893857/192778100-885271bf-65cb-40ce-a463-17e37958ce28.png)


There are 2 ISE Instance 51 and 52 both the IPs are NATed via Switch, 51 is accessible via port 3365 of public ip 10.105.238.2, and 52 is accessible on port 3366.

If user is logged in to ISE1, cookie APPSESSIONID will be created with value EBB26977AD003966797668B54D4B15C9

Then again if user tries to open ISE2 and login then browser will override the APPSESSIONID. Because browser does not differentiate domain based on port number.

When user returns to ISE1, he will be logged out because valid cookie is overridden by browser.

Solution
-----------

One possible solution is to create a unique id during ISE Installation and while creating the session cookie use this id instead of APPSESSIONID. Now when browser sends the cookie to the ISE Server it will find the required cookie name and serve the page. Next, when NATed IP ISE2 is accessed, it will create another cookie which is different from ISE1. So, browser does not override the cookie.

![image](https://user-images.githubusercontent.com/77893857/192778480-c4146b53-b6df-4cdd-975b-a96c0bbb8c12.png)

Installation
Create a unique id which will be used as session cookie name

Login
During login check for valid credentials
If credentials are valid then send a cookie name with unique id created during installation
Browser saves the cookie and send this in every further request

NATed Environment

ISE 1 - login check for valid credentials

If credentials are valid then send a cookie name with unique id created during installation, this ID will be unique to only ISE1

ISE 2 - login check for valid credentials

If credentials are valid then send a cookie name with unique id created during installation, this ID will be unique to only ISE2

Browser will send the cookie of both Instance to ISE server, ISE1 cookie will not be overridden by ISE2 cookie

Now, both the instance can work simultaneously in NATed environment.


