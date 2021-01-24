# DependencyInjectionBasic
Document for DI basics

# Dependency Injection

## what is dependency injection:-

https://www.tutorialsteacher.com/ioc/dependency-injection

Dependecy injection is a prgramming technique that makes a class independent of its dependencies.

​                                                Or

It allows the creation of dependent objects outside of a class and provides those objects to a class through different ways.

-  it help us to make more decouple classes.
-  it also help us to follow SOLID’s [dependency inversion](https://stackify.com/dependency-inversion-principle/) and [single responsibility principles](https://stackify.com/solid-design-principles/).
-  it also reduce the frequency of object creation.
-  It also reduces the risk that you have to change a class just because one of its dependencies changed.

***The goal of the dependency injection technique is to remove this dependency by separating the usage from the creation of the object. This reduces the amount of required boilerplate code and improves flexibility.***

Mean if you use dependency injection then you do not need to handle the object creation (which is the dependency of a class or more classes).

# Dependency Inversion.

> A. High level modules should not depend upon low level modules. Both should depend upon abstractions.
>
> B. Abstractions should not depend upon details. Details should depend upon abstractions.

1. So abstraction is a OOPs principle which tell us that Client or User should only see/operate the application by using UI they don't care about back-end processing or programming things...so usually we abstracts our code into UI. Or  you also can  say hiding those parts which are not the related or concerns of the clients. And we create abstraction by using abstract or Interface so client implement these interface and not able to create the objects and clients will on handle those methods which available on this abstraction and all thing will hide from the clients which are not the concerns of clients.
2. A high-level module is a module which depends on other modules.

Lets understand by example

```java
public class CustomerBusinessLogic
{
    public CustomerBusinessLogic()
    {
    }

    public string GetCustomerName(int id)
    {
        DataAccess _dataAccess = DataAccessFactory.GetDataAccessObj();

        return _dataAccess.GetCustomerName(id);
    }
}

public class DataAccessFactory
{
    public static DataAccess GetDataAccessObj() 
    {
        return new DataAccess();
    }
}

public class DataAccess
{
    public DataAccess()
    {
    }

    public string GetCustomerName(int id) {
        return "Dummy Customer Name"; // get it from DB in real app
    }
}
```

The `CustomerBusinessLogic` class uses the concrete `DataAccess` class. Therefore, it is still tightly coupled,

Lets implements the abstraction

```java
public interface ICustomerDataAccess
{
    string GetCustomerName(int id);
}

public class CustomerDataAccess: ICustomerDataAccess
{
    public CustomerDataAccess() {
    }

    public string GetCustomerName(int id) {
        return "Dummy Customer Name";        
    }
}

public class DataAccessFactory
{
    public static ICustomerDataAccess GetCustomerDataAccessObj() 
    {
        return new CustomerDataAccess();
    }
}

public class CustomerBusinessLogic
{
    ICustomerDataAccess _custDataAccess;

    public CustomerBusinessLogic()
    {
        _custDataAccess = DataAccessFactory.GetCustomerDataAccessObj();
    }

    public string GetCustomerName(int id)
    {
        return _custDataAccess.GetCustomerName(id);
    }
}
```

The advantages of implementing DIP in the above example is that the **`CustomerBusinessLogic`** and **`CustomerDataAccess`** classes are loosely coupled classes because **`CustomerBusinessLogic`** does not depend on the concrete **`DataAccess`** class, instead it includes a reference of the **`ICustomerDataAccess`** interface. So now, we can easily use another class which implements **`ICustomerDataAccess`** with a different implementation.

Still, we have not achieved fully loosely coupled classes because the **`CustomerBusinessLogic`** class includes a factory class to get the reference of **`ICustomerDataAccess `**.  or we can say `**CustomerBusinessLogic** depends on the **DataAccessFactory** to get the reference of  **ICustomerDataAccess**, `

 So, suppose there is another implementation of `ICustomerDataAccess` and we want to use that new class inside `CustomerBusinessLogic`. Then, we need to change the source code of the `CustomerBusinessLogic` class as well

 So this is condition where the Dependency Injection pattern helps us.



## Second part

### First remind what is dependecny Injection by seeing in the starting of the page

The Dependency Injection pattern involves 3 types of classes.

1. **Client Class:** The client class (dependent class) is a class which depends on the service class
2. **Service Class:** The service class (dependency) is a class that provides service to the client class.
3. **Injector Class:** The injector class injects the service class object into the client class.

In this way, the DI pattern separates the responsibility of creating an object of the service class out of the client class.

**Constructor Injection:** In the constructor injection, the injector supplies the service (dependency) through the client class constructor.

**Property Injection:** In the property injection (aka the Setter Injection), the injector supplies the dependency through a public property of the client class.

**Method Injection:** In this type of injection, the client class implements an interface which declares the method(s) to supply the dependency and the injector uses this interface to supply the dependency to the client class.



Lets explore Constructor Injection,

```java
public class CustomerBusinessLogic
{
    ICustomerDataAccess _dataAccess;

    public CustomerBusinessLogic(ICustomerDataAccess custDataAccess)
    {
        _dataAccess = custDataAccess;
    }

    public string ProcessCustomerData(int id)
    {
        return _dataAccess.GetCustomerName(id);
    }
}

public interface ICustomerDataAccess
{
    string GetCustomerName(int id);
}

public class CustomerDataAccess: ICustomerDataAccess
{
    public CustomerDataAccess()
    {
    }

    public string GetCustomerName(int id) 
    {
        //get the customer name from the db in real application        
        return "Dummy Customer Name"; 
    }
}
```



`CustomerBusinessLogic` includes the constructor with one parameter of type `ICustomerDataAccess`. Now, the calling class must inject an object of ICustomerDataAccess.

```java
public class CustomerInjector
{
    CustomerBusinessLogic _customerBL;

    public CustomerInjector()
    {
        _customerBL = new CustomerBusinessLogic(new CustomerDataAccess());
    }

    public string GetCustomerName(int id) {
        return _customerBL.ProcessCustomerData(id);
    }
}
```



As you can see in the above example, the `CustomerService` class creates and injects the `CustomerDataAccess` object into the `CustomerBusinessLogic` class. Thus, the `CustomerBusinessLogic` class doesn't need to create an object of `CustomerDataAccess` using the `new` keyword or using factory class. The calling class (CustomerService) creates and sets the appropriate DataAccess class to the `CustomerBusinessLogic` class. In this way, the `CustomerBusinessLogic` and `CustomerDataAccess` classes become "more" loosely coupled classes.


