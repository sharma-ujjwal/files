Servlets and Filters
	> When a broswer sends a HTTP request to our application, as JAVA code will not directly understand the HTTP protocol, there will be a midddleman sitting between our java code and the broswer, which is a servlet containers or web servers. We usually use web servers like Apache Tomcat which is a servlet container.
	
	What a servlet container will do is, they will convert the HTTP protocol based request which was sent by the browser into a HttPServletRequest object. The same object will then be provided to the Java code or the framework that we are using in our application.
	
	Similarly, when we are sending the response back to the browser, the servlet container will convert the HttPServletResponse object to the Http response so that browser can understand.
	
	Just like servlets there is another concept called as Filters which are used in web application.
	>	Filters are special kind of servlets which we can use to intercept each and every request coming to our web application.
	
	Inside the filters we can define a logic that will be implemented before we will write our business logic.
	
	
Spring Security Internal Flow:

	Step 1 - User will enter their own credentials to the login page, user can invoke the request to the backend server with the help of browser or Postman.
	
	Step 2 - The request is then passed through Spring Security Filters. The purpose of the filter is to monitor each and every request that is coming to our application, it will check the path of the request which the client is trying to access. Based upon the path and configurations that we have done, filters will determine whether it is a protected/public resource. 
	
	One of the most important filters is the AuthenticationFilter (like UsernamePasswordAuthenticationFilter). This filter attempts to extract the username and password from the HTTP request and then prepare the authentication type object. If the user is successfully authenticated, an Authentication object is created. The filter will then decide whether the authentication needs to enforced on the end user. This authentication will only happen at the beginning of the request. After successful login at the beginning, the filter will also check if the user is already logged in or not, if already logged in, these filters will not enforce login page to the user.
	
	The username and password that user sends while authentication, filters will convert these into authentication object. Once the request has been authenticated, this Authentication object is then stored in the SecurityContext, which is held in the SecurityContextHolder. This object will help when the same user who has already autheticated don't need to authenticate again.
	
	Step 3 - Once we get the authentication object, it is passed to the Authentication Manager. It is manager interface which handles the authentication logic. It will check, what are all the Authentication Providers available in our application.
	
	Step 4 - In Authentication Providers, we can define the actual logic of authentication, whether we want to validate the user credentials from a database or a LDAP server or cache or any authentication server. There can be multiple Authentication Provider in a web application, eg 1 Authentication Provider can be used to hanle Username and Password authentication and another authentication provider can act as some other provider.
	
	Step 5 - If we want to levarage the already created Authentication Provider for authentication of the user, we can also do that which will directly use its own Manager/Service.
	When comparing the username and password that are provided by the end user and the username, password from the database the predefined logic for the same is given inside the UserDetails Manager/Service.
	
	Step 6 - When validating the password, we can levarage Password Encoder in order to encrypt and decrypt the stored database or any manager. So for performing password related standards and logic we can use Password Encoder interface and its implementations in Spring Security.
	
	It is the responsibility of the authentication manager in identifying what are all the available authentication providers for a particular request and accordingly it will send the request to the particular Authentication Provider. If in case authentication fails from a particular Provider, Manager will also make sure that the credentials are undergone through all of the remaining Providers, and if it fails again, Manager will send failed authentication as response to the end user.
	
	
	Step -7 ,8, 9
	 After the validation is done, the Authentication Provider sends the response to the Authentication Manager and Manager will send it back to the Spring Security filters. Before sending the response back to the end user, filters will store the Authentication object that was created in Step - 2. The SecurityContext and thus the Authentication object can be accessed from anywhere in your code, regardless of how many methods deep you are, as long as it's in the same thread.
	
	And finally the response will be send back to the end user.
	
	
	
	
