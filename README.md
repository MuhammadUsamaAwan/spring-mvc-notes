# Spring MVC

## Setting Up Development Enivornment

Create a new dynamic web project. Place place your Spring jar files inside src > main > webapp > WEB-INF > lib. You also need to copy servlet jsp files.<br>
Add configuration to file inside WEB-INF

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns="http://xmlns.jcp.org/xml/ns/javaee"
	xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
	id="WebApp_ID" version="3.1">

	<display-name>spring-mvc</display-name>

	<absolute-ordering />

	<!-- Spring MVC Configs -->

	<!-- Step 1: Configure Spring MVC Dispatcher Servlet -->
	<servlet>
		<servlet-name>dispatcher</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>

		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>/WEB-INF/spring-mvc-servlet.xml</param-value>
		</init-param>

        <load-on-startup>1</load-on-startup>
	</servlet>

	<!-- Step 2: Set up URL mapping for Spring MVC Dispatcher Servlet -->
	<servlet-mapping>
		<servlet-name>dispatcher</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>

</web-app>
```

Spring Configuration File

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="
		http://www.springframework.org/schema/beans
    	http://www.springframework.org/schema/beans/spring-beans.xsd
    	http://www.springframework.org/schema/context
    	http://www.springframework.org/schema/context/spring-context.xsd
    	http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">

	<!-- Step 3: Add support for component scanning -->
	<context:component-scan base-package="packageName" />

	<!-- Step 4: Add support for conversion, formatting and validation support -->
	<mvc:annotation-driven/>

	<!-- Step 5: Define Spring MVC view resolver -->
	<bean
		class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/WEB-INF/view/" />
		<property name="suffix" value=".jsp" />
	</bean>

</beans>
```

<br>

## Creating Controller & View

```java
@Controller
public class HomeController {
    @RequestMapping("/")
    public String showPage() {
        return "main-menu";
    }
}
```

/WEB-INF/view/main-menu.jsp

```html
<!DOCTYPE html>
<html>
    <body>
        <h1>Main Page</h1>
    <body>
</html>
```

<br>

## HTML Form Data

```java
@Controller
public class FormController {

    @RequestMapping("/showForm")
    public String showForm() {
        return "form";
    }
    @RequestMapping("/processForm")
    public String processForm() {
        return "process-form";
    }
}
```

```html
<!-- Form View -->
<!DOCTYPE html>
<html>
    <body>
        <form action="processForm" method="GET">
            <input name="name" />
            <input type="submit" />
        </form>
    <body>
</html>
```

```html
<!-- Process form View -->
<!DOCTYPE html>
<html>
    <body>
        Name: ${param.name}
    <body>
</html>
```

<br>

## Adding Data to Spring Model

```java
@RequestMapping("/processForm")
public String processForm(@RequestParam("name") String name, Model model) {
    model.addAttribute("theName", name);
    return "process-form";
}
```

```html
<!DOCTYPE html>
<html>
    <body>
        Name: ${theName}
    <body>
</html>
```

<br>

## Controller Level Mapping

```java
@Controller
@@RequestMapping("/forms")
public class FormController {

    @RequestMapping("/showForm")
    public String showForm() {
        return "form";
    }
}
```

<br>

## Form Tags

```java
//student class with setters
```

```java
@RequestMapping("/showForm")
public String showForm(Model model) {
    model.addAttribute("student", new Student());
    return "form";
}
@RequestMapping("/processForm")
public String processForm(@ModelAttribute("student") Student theStudent) {
    System.out.println("theStudent", theStudent.getName());
    return "process-form";
}
```

```html
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<!DOCTYPE html>
<html>
    <body>
        <form:form action:"processForm" modelAttribute="student">
            Name: <form:input path="name" />
            <form type="submit" />
        </from:form>
    </body>
</html>
```

```html
The student name is : ${student.name}
```

<br>

## Form Validation

Download hibernate validation jar files and copy to lib directory.

```java
// 1. Add validation rule to Class
public class Customer {
    // Sting
    private String name;
    @NotNull(message="is required");
    @Size(min=1, message="is required");
    // Min & Max
    private int age;
    @Min(value=0)
    @Max(value=100)
    // Regular Expression
    @Pattern(regexp="[a-zA-Z0-9]{5}") //a to z, A to Z, 0 to 9
    // Setters
}
```

```html
<!-- 2. Display error message on HTML form -->
<form:errors path="name" cssClass="error" />
```

```java
// 3. Perform validation in controller class
@RequestMapping("/processForm")
public String processForm(
    @Valid @ModelAttribute("customer") Customer theCustomer,
    BindingResult theBindingResult) {

    if (theBindingResult.hasErrors()) {
        return "form";
    }
    else {
        return "process-form";
    }
}
```

### Add Pre-Processing

```java
// Inside controller
@InitBinder
public void initBinder(WebDataBinder dataBinder) {
    StringerTimmerEditor stringTimmerEditor = new StringerTimmerEditor(true); //true means trim to null incase of all whitespace
    dataBinder.registerCustomEditor(String.class, stringTimmerEditor); //Pre process every string
}
```
