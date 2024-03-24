# CV_4_Angular_SpringBoot

# Introduction to Spring Boot:

Angular is a popular open-source framework for building web applications, maintained by Google and a community of developers. It's written in TypeScript and follows the component-based architecture, allowing developers to build dynamic, interactive, and scalable web applications.

### Key Features of Angular:

1. **Component-Based Architecture**:
   - Angular applications are built using components, which are self-contained and reusable units of UI. Each component encapsulates the HTML, CSS, and TypeScript logic for a specific part of the application.

2. **Templates**:
   - Angular uses HTML templates with additional syntax and features such as data binding, directives, and pipes. Templates are used to define the UI structure and behavior of components.

3. **Data Binding**:
   - Angular provides various types of data binding mechanisms like Interpolation, Property Binding, Event Binding, and Two-Way Binding, allowing seamless communication between the component class and its template.

4. **Dependency Injection (DI)**:
   - Angular has a powerful dependency injection system that helps in creating modular, testable, and maintainable code. DI allows components and services to be loosely coupled, making it easier to manage dependencies and promote reusability.

5. **Directives**:
   - Angular directives are special markers in the DOM that tell Angular to do something to a DOM element or its children. Directives like *ngIf, *ngFor, and *ngSwitchCase are commonly used for conditionally rendering elements, iterating over lists, and switching between multiple views.

6. **Services**:
   - Services are a way to encapsulate reusable logic and data manipulation that can be shared across multiple components. They are typically used for tasks like making HTTP requests, handling authentication, or managing application state.

7. **Routing**:
   - Angular's built-in router allows developers to implement navigation and define multiple views within a single-page application. It supports features like nested routes, route parameters, lazy loading, and guards for controlling access to routes.

8. **Forms**:
   - Angular provides two approaches for working with forms: template-driven forms and reactive forms (model-driven forms). These mechanisms simplify form handling, validation, and data submission in Angular applications.

9. **HTTP Client**:
   - Angular's HttpClient module simplifies the process of making HTTP requests to a server. It provides features like request/response interception, error handling, and support for observables for handling asynchronous operations.

10. **Modularity and Reusability**:
    - Angular encourages modular development by allowing developers to organize code into modules. Modules help in managing dependencies and keeping the application clean and maintainable. Angular modules can be easily reused across different projects.

Angular continues to evolve with regular updates and improvements, providing developers with a robust framework for building modern web applications. Its rich feature set, strong community support, and backing from Google make it a popular choice for developers worldwide.

Axios and HttpClient are both HTTP client libraries commonly used in JavaScript/TypeScript environments for making HTTP requests. Axios is typically used in frontend applications, particularly with frameworks like React and Vue.js, while HttpClient is used in Angular applications. Here's a description of each:

### Axios:

Axios is a promise-based HTTP client library for JavaScript that can be used in both browser and Node.js environments. It provides an easy-to-use interface for making HTTP requests and handling responses. Some key features of Axios include:

1. **Promise-based**: Axios uses Promises, allowing for easy chaining and handling of asynchronous operations.

2. **Concise API**: Axios provides a simple and intuitive API for making HTTP requests with methods like `axios.get()`, `axios.post()`, `axios.put()`, and `axios.delete()`.

3. **Interceptors**: Interceptors allow you to intercept request and response data, enabling features like request/response logging, error handling, and token authentication.

4. **CSRF Protection**: Axios includes built-in support for Cross-Site Request Forgery (CSRF) protection by automatically attaching CSRF tokens to requests.

5. **Browser Compatibility**: Axios supports all modern browsers and provides features like request cancellation and automatic JSON parsing.

6. **Cancelation**: Axios allows you to cancel requests easily using cancel tokens, which is useful for scenarios like user navigation away from a page while a request is in progress.

Your Angular service using Axios looks good overall. It sets up a base URL, default headers, handles authentication tokens, and provides a generic request method. However, there are a couple of improvements and adjustments we can make:

1. **Error Handling**: It's important to handle errors gracefully in HTTP requests. Axios provides a way to intercept errors and handle them globally, which can be useful for centralized error handling.

2. **Type Definitions**: TypeScript's static typing can help catch errors early and provide better code documentation. We can define interfaces for request and response payloads to ensure type safety.

Here's an updated version of your `AxiosService` with these improvements:

```typescript
import { Injectable } from '@angular/core';
import axios, { AxiosInstance, AxiosRequestConfig, AxiosResponse, AxiosError } from 'axios';
import { Observable, throwError } from 'rxjs';
import { catchError } from 'rxjs/operators';

@Injectable({
  providedIn: 'root'
})
export class AxiosService {
  private axiosInstance: AxiosInstance;

  constructor() {
    this.axiosInstance = axios.create({
      baseURL: 'http://localhost:8080',
      headers: {
        'Content-Type': 'application/json'
      }
    });
  }

  private getAuthToken(): string | null {
    return window.localStorage.getItem('auth_token');
  }

  private setAuthToken(token: string | null): void {
    if (token !== null) {
      window.localStorage.setItem('auth_token', token);
    } else {
      window.localStorage.removeItem('auth_token');
    }
  }

  public request<T>(method: string, url: string, data?: any): Observable<T> {
    const headers: any = {};

    if (this.getAuthToken() !== null) {
      headers.Authorization = `Bearer ${this.getAuthToken()}`;
    }

    const config: AxiosRequestConfig = {
      method: method,
      url: url,
      data: data,
      headers: headers
    };

    return this.axiosInstance.request<T>(config)
      .then((response: AxiosResponse<T>) => response.data)
      .catch((error: AxiosError) => {
        // Handle error here, e.g., logging, redirecting, etc.
        console.error('Request failed:', error);
        return throwError(error);
      });
  }
}
```

In this updated version:

- We import `AxiosInstance`, `AxiosRequestConfig`, `AxiosResponse`, and `AxiosError` from Axios to ensure proper typing.
- We use `Observable` from RxJS to make the request method return an observable, which can be subscribed to in Angular components.
- We use RxJS's `catchError` operator to handle errors and return an observable with the error.
- We define interfaces for request and response payloads to ensure type safety.
- We use string interpolation for the Authorization header for better readability.
- We adjust the method signature of the `request` method to return an observable of a generic type `T`, which corresponds to the expected response type.

These improvements should make your `AxiosService` more robust and easier to use within your Angular application. Adjust it further based on your specific requirements and error handling strategies.

### HttpClient (Angular):

HttpClient is a built-in module in Angular for making HTTP requests to backend servers. It is part of the `@angular/common/http` package. Some key features of HttpClient include:

1. **Observables**: HttpClient returns Observables from its HTTP methods, allowing for easier management of asynchronous data streams and providing features like retrying requests and canceling subscriptions.

2. **Interceptors**: Similar to Axios, HttpClient also supports interceptors, which allow you to intercept and modify HTTP requests and responses globally across your application.

3. **Typed Responses**: HttpClient supports typing responses using generics, making it easier to handle response data with TypeScript interfaces or classes.

4. **Error Handling**: HttpClient provides built-in error handling mechanisms, including error interception and retrying failed requests.

5. **Immutable Requests**: HttpClient's request objects are immutable, meaning that modifications to the request configuration do not affect the original request object.

6. **Testing Support**: HttpClient is designed with testing in mind and provides utilities for mocking HTTP requests in unit tests using Angular's testing framework.

### Comparison:

While both Axios and HttpClient serve similar purposes, they are used in different ecosystems (Axios in JavaScript/Node.js, and HttpClient in Angular). The choice between them often depends on the specific requirements of your project and the framework you are using. If you're working in an Angular project, it's recommended to use HttpClient due to its tight integration with Angular's ecosystem and built-in features like observables. If you're working in a different JavaScript environment or framework, Axios may be a better choice due to its simplicity and versatility.

Certainly! If you want to configure the base URL to be `'http://localhost:8080'` for all requests in the `HttpClientService`, you can modify the service to use this base URL. Here's how you can adjust the service:

```typescript
import { Injectable } from '@angular/core';
import { HttpClient, HttpHeaders, HttpErrorResponse } from '@angular/common/http';
import { Observable, throwError } from 'rxjs';
import { catchError } from 'rxjs/operators';

@Injectable({
  providedIn: 'root'
})
export class HttpClientService {
  private baseUrl = 'http://localhost:8080';

  constructor(private http: HttpClient) { }

  private getAuthToken(): string | null {
    return window.localStorage.getItem('auth_token');
  }

  private setAuthToken(token: string | null): void {
    if (token !== null) {
      window.localStorage.setItem('auth_token', token);
    } else {
      window.localStorage.removeItem('auth_token');
    }
  }

  private getHeaders(): HttpHeaders {
    let headers = new HttpHeaders({
      'Content-Type': 'application/json'
    });

    const authToken = this.getAuthToken();
    if (authToken) {
      headers = headers.append('Authorization', `Bearer ${authToken}`);
    }

    return headers;
  }

  public request<T>(method: string, endpoint: string, data?: any): Observable<T> {
    const url = `${this.baseUrl}/${endpoint}`;
    const options = {
      headers: this.getHeaders()
    };

    let request: Observable<T>;

    switch (method.toLowerCase()) {
      case 'get':
        request = this.http.get<T>(url, options);
        break;
      case 'post':
        request = this.http.post<T>(url, data, options);
        break;
      case 'put':
        request = this.http.put<T>(url, data, options);
        break;
      case 'delete':
        request = this.http.delete<T>(url, options);
        break;
      default:
        throw new Error('Invalid HTTP method');
    }

    return request.pipe(
      catchError((error: HttpErrorResponse) => {
        // Handle error here, e.g., logging, redirecting, etc.
        console.error('Request failed:', error);
        return throwError(error);
      })
    );
  }
}
```

In this modified version:

- We introduced a private property `baseUrl` to store the base URL `'http://localhost:8080'`.
- We construct the full URL by appending the `endpoint` to the `baseUrl` in the `request()` method.
- All requests made using this service will now be sent to the specified endpoint on `'http://localhost:8080'`.

You can then use this `HttpClientService` in your Angular components or other services as before. Adjust it further based on your specific requirements and error handling strategies.


In the context of Spring Boot applications, let's compare JWT (JSON Web Tokens) and Basic Authentication in terms of implementation, security, and usage:

## JWT (JSON Web Tokens):

1. **Implementation**:
   - Implementing JWT authentication in Spring Boot involves integrating libraries like `spring-security` along with `jjwt` (Java JWT) for token generation and validation.
   - You typically configure Spring Security to authenticate requests using JWT tokens, validate them, and extract user details from the token to establish the user's identity.

2. **Stateless and Scalable**:
   - JWT offers stateless authentication, meaning the server doesn't need to store session state. This makes JWT suitable for distributed and scalable applications, such as microservices architectures.

3. **Token-based Authentication**:
   - JWT is a token-based authentication mechanism where the client receives a token upon successful authentication. This token is then sent with subsequent requests to access protected resources.

4. **Claims and Authorization**:
   - JWT tokens can contain custom claims representing user roles, permissions, and other attributes, allowing for fine-grained authorization checks.

5. **Security**:
   - JWT tokens can be digitally signed using cryptographic algorithms, ensuring their integrity and authenticity. This helps prevent tampering and unauthorized access.

### Basic Authentication:

1. **Implementation**:
   - Implementing Basic Authentication in Spring Boot involves configuring Spring Security to use HTTP Basic Authentication.
   - With Basic Authentication, the client includes a username and password in the request headers, encoded in Base64 format.

2. **Stateful**:
   - Basic Authentication can be considered stateful from the server's perspective, as it requires validating credentials with each request. However, it does not maintain session state on the server.

3. **No Tokens**:
   - Unlike JWT, Basic Authentication does not involve tokens. Instead, the client sends credentials (username and password) with each request, typically using the `Authorization` header.

4. **Security**:
   - Basic Authentication sends credentials with each request, potentially exposing them to interception. However, it can be secured by using HTTPS to encrypt communication between client and server.

### Comparison:

- **Security**: JWT is generally considered more secure than Basic Authentication, especially when configured with strong cryptographic algorithms and token validation mechanisms.

- **Statelessness**: JWT offers stateless authentication, making it suitable for distributed and scalable applications. Basic Authentication, on the other hand, may require session management on the server side.

- **Ease of Use**: Basic Authentication is simpler to implement and may be suitable for simple use cases. JWT requires additional configuration but offers more features and flexibility.

- **Token vs Credentials**: JWT involves exchanging tokens between client and server, while Basic Authentication sends credentials with each request.

In summary, JWT authentication is often preferred over Basic Authentication in Spring Boot applications, especially for modern web applications requiring scalability, security, and fine-grained authorization capabilities. However, Basic Authentication may still be suitable for certain use cases or legacy systems where simplicity is prioritized over advanced features.

# Introduction to Spring Boot:

Spring Boot is a powerful and innovative framework built on top of the Spring framework that aims to simplify the process of building robust, production-ready Java applications. It provides a streamlined development experience by offering out-of-the-box solutions for common tasks, reducing boilerplate code, and promoting convention over configuration.

At its core, Spring Boot is designed to address the complexities associated with setting up and configuring Spring-based applications. Traditionally, developing Spring applications required manual configuration of various components such as application context, dependency injection, and web servers, among others. Spring Boot, however, eliminates much of this manual configuration by leveraging sensible defaults and auto-configuration.

<img src='./spring.png' >

Key Features of Spring Boot:

1. **Auto-Configuration**: Spring Boot automatically configures various components of the application based on the dependencies present in the classpath. This feature reduces the need for explicit configuration and allows developers to focus on writing application code rather than spending time on setup and configuration tasks.

2. **Starter Dependencies**: Spring Boot provides a collection of starter dependencies that encapsulate sets of libraries and configurations commonly used for specific tasks, such as web development, data access, security, and testing. Developers can include these starters in their projects to quickly bootstrap their applications with the required dependencies.

3. **Embedded Servers**: Spring Boot includes support for embedded servlet containers such as Tomcat, Jetty, and Undertow. This allows developers to package their applications as standalone JAR files that contain the necessary web server, making deployment and execution simpler and more consistent across different environments.

4. **Production-Ready Features**: Spring Boot includes a range of features aimed at facilitating the development of production-ready applications, such as health checks, metrics, auditing, and externalized configuration. These features enable developers to build applications that are robust, scalable, and easy to manage in a production environment.

5. **Actuator**: Spring Boot Actuator provides built-in endpoints for monitoring and managing the application at runtime. These endpoints expose information about application health, metrics, environment properties, and more, allowing developers to monitor and diagnose their applications effectively.

6. **Integration with Spring Ecosystem**: Spring Boot seamlessly integrates with other components of the Spring ecosystem, including Spring Framework, Spring Data, Spring Security, and Spring Cloud. This integration enables developers to leverage existing Spring knowledge and expertise while benefiting from the streamlined development experience offered by Spring Boot.

Overall, Spring Boot revolutionizes Java application development by simplifying the setup, configuration, and deployment processes, thereby accelerating the development cycle and empowering developers to focus on building high-quality, feature-rich applications. Its extensive features, robustness, and seamless integration with the Spring ecosystem make it a popular choice for developing modern Java applications.

# Infromation

Spring Boot is a Java application development framework that makes it easy and fast to create robust applications. The following concepts are the basic building blocks of the Spring Boot application architecture:

1. **Controller**: A controller is an application component that handles HTTP requests from clients. The controller receives the request, performs the requested actions, and then returns a response to the client. Each Controller defines a set of endpoints that determine how the application responds to different types of requests.

2. **Entity**: An entity is a class that represents a data object in a database. It usually corresponds to a single table in a relational database. Entities define the attributes and relationships of the data that are stored in the database. Within Spring Boot, the Java Persistence API (JPA) library is often used to map these entities to tables in the database.

3. **Repository**: Repository is a component that provides an abstract interface for communicating with the database. Repositories are used to store, read, update and delete data from the database. Spring Boot simplifies the implementation of Repositories by using the Spring Data JPA extension, which automatically generates an implementation based on defined interfaces and methods.

4. **Service**: A service is a component that contains the business logic of an application. Services implement functionalities that perform operations on data and provide them to Controllers. Services are often used to encapsulate application logic, improving modularity and code reusability. They are often used for operations that need more than one operation over data or that are higher level of abstraction than simple CRUD operations (create, read, update, delete).ad data or that are higher level of abstraction than simple CRUD operations (create, read, update, delete).


# Description of each of the technologies:

1. **Spring Web**: Spring Web provides basic support for building web applications using the Spring Framework. It includes various functionalities such as controllers, annotations for URL mapping, form processing solutions, session management, support for creating RESTful APIs, and more.

2. **Thymeleaf**: Thymeleaf is a templating system for creating dynamic web pages in Java. It is widely used in Spring framework for creating views in web applications. Thymeleaf allows you to write HTML pages with nested attributes that are automatically populated with data from the Spring model.

3. **Spring Data JPA**: Spring Data JPA is a part of the Spring framework that makes it easy to work with persistent data in Java using the Java Persistence API (JPA). It provides an abstraction over JPA and makes it easy to create repositories for manipulating data. Spring Data JPA also provides a number of features such as query methods, automatic query generation, and support for paging and sorting.

4. **MySQL Driver**: MySQL Driver is a library (or module) that allows communication with the MySQL database from Java. It provides a means for making database connections, executing queries, and manipulating data.

5. **Spring Boot Dev Tools**: Spring Boot Dev Tools are development tools that are part of the Spring Boot framework. These tools include various features that make web application development easier, such as automatically reloading changes (live reloading) when code is modified, automatically restarting the application when dependencies change, and more. They help speed up the development cycle and improve developer productivity.

# Annotations (`@RequestMapping`, `@PostMapping`, `@GetMapping`) 

1. **@RequestMapping**:
   - `@RequestMapping` was the original annotation for mapping HTTP requests to controller methods in the Spring Framework.
   - It can be used to map different HTTP method types (GET, POST, PUT, DELETE, etc.) and can have multiple values that define different paths.
   - It is generic and can be used for any type of HTTP request.
   - For example:
     ```java
     @RequestMapping(value = "/example", method = RequestMethod.GET)
     public String exampleMethod() {
         // Method code
     }
     ```

2. **@PostMapping**:
   - `@PostMapping` is a special case of `@RequestMapping` used to map HTTP POST requests to controller methods.
   - This annotation ensures that the controller method will only handle HTTP POST requests.
   - It is preferred for handling data that is typically sent using forms or other POST requests.
   - For example:
     ```java
     @PostMapping("/create")
     public String createNewObject() {
         // Code to create a new object
     }
      ```

     3. **@GetMapping**:
   - `@GetMapping` is another special case of `@RequestMapping` used to map HTTP GET requests to controller methods.
   - This annotation ensures that the controller method will only handle HTTP GET requests.
   - It is useful for retrieving data or displaying pages without modifying the data on the server.
   - For example:
     ```java
     @GetMapping("/show")
     public String showData() {
         // Code to display data
     }
     ```

Overall, `@PostMapping` and `@GetMapping` are specialized annotations that specify only one type of HTTP method (POST or GET), while `@RequestMapping` can be used for any type of HTTP method. Using specialized annotations can improve code readability and more clearly express the purpose of a given controller method.


# Tutorial

[Introduction angular and security ](https://www.youtube.com/watch?v=YUqi1IjLX8I)

[Angular course ](https://www.youtube.com/watch?v=JWhRMyyF7nc)

[React and security ](https://www.youtube.com/watch?v=7gZwWSsGIDE&list=PLQZT1RFYyzlKCnsBBxri68Cj-iwT17C2h&index=9&t=1s)

[SpringBoot and Thymeleaf and security ](https://www.youtube.com/watch?v=R76S0tfv36w)


# helpful links with more information

[Angular ](https://angular.io/)

[Angular more ](https://material.angular.io/)

[JWT ](https://jwt.io/)

[React ](https://react.dev/)

[Thymeleaf ](https://www.thymeleaf.org/doc/articles/layouts.html)

[Spring boot ](https://www.tutorialspoint.com/spring_boot/spring_boot_introduction.htm)

[Spring boot 2 ](https://www.javatpoint.com/spring-boot-tutorial)

[Spring boot video ](https://www.youtube.com/watch?v=Nv2DERaMx-4&t=12584s)

[Spring boot app 1 ](https://www.youtube.com/watch?v=HYGnVeCs0Yg)

[Spring boot app 2 ](https://www.youtube.com/watch?v=6zfIxgaVkQI)

[Spring boot app 3 ( with security) ](https://www.youtube.com/watch?v=4ga5bcBPgzo&list=PLvXZgw5HRzfgCcoV2KV2eIIemC6sl8Xla)

[Spring boot app 4 ( with react) ](https://www.youtube.com/watch?v=O_XL9oQ1_To)



