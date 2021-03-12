---
layout: post
title: C# SOLID Principles 
date: 2021-03-09 09:29:20 +0700
modified: 2021-03-09 21:49:47 +07:00
categories: Blog
tags: [TIL, C#]
description: C# SOLID Principles.
---

SOLID 원칙은 대부분의 소프트웨어 디자인 문제를 해결해주는 기본 원칙들이다. 

## Single Responsibility Principle (SRP)

a. Every software module should have only one reason to change.

모든 소프트웨어 모듈은 단 하나의 **책임안**을 가져야 한다. OOP에서 객체가 하나 이상의 책임(역할)을 가지면 응집도는 낮아지고 결합도는 올라가게 된다. 응집도가 낮고 결합도가 높은 코드는 새로운 요구사항이나 변경이 있을 때 관련된 모든 코드를 수정해야 된다. 즉 유지보수가 어려워진다.

{% highlight cs %}
public class UserService
{
    public void Register() { ... }
    public bool ValidateEmail() { ... }
    public bool SendEmail() { ... }
}
{% endhighlight %}

위에 코드는 SRP를 어기고 있다. `ValidateEmail()`과 `SendEmail()`은 유저 서비스와 관련이 없다. **단일 책임 원칙**을 따르면 다음과 같이 변경된다.

{% highlight cs %}
public class UserService
{
    public void Register() { ... }
}

public class EmailService
{
    public bool ValidateEmail() { ... }
    public bool SendEmail() { ... }
}
{% endhighlight %}

## Open Closed Principle (OCP)

a. The Open/closed Principle says "A software module/class is open for extension and closed for modification.

소프트웨어 모듈(클래스)은 기존 코드를 변경하지 않으면서 새로운 기능을 추가할 수 있어야 한다. **개방 폐쇄 원칙**은 상속을 이용해 설계할 수 있다.

{% highlight cs %}
public abstract class Shape
{
    public abstract double Area();
}
{% endhighlight %}

{% highlight cs %}
public class Square: Shape
{
    ...
    public override double Area()
    {
        return Height * Width;
    }
}
public class Circle: Shape
{
    ...
    public override double Area()  
    {
        return Radius * Radus * Math.PI;
    }
}  
{% endhighlight %}

{% highlight cs %}
public class AreaCalculator  
{
    public double TotalArea(Shape[] arrShapes)
    {
        double area=0;
        foreach(var objShape in arrShapes)
        {
            area += objShape.Area();
        }
        return area;
    }
}
{% endhighlight %}

새로운 도형이 추가될 때 마다 <kbd>Shape</kbd> 추상 클래스를 상속받아 구현하면 된다. 기존에 있던 <kbd>AreaCalculator</kbd> 클래스는 수정할 필요가 없다.


## Liskov Substitution Principle (LSP)

a. You should be able to use any derived class instead of a parent class and have it behave in the same manner without modification

**리스코프 치환 원칙**은 자식 클래스에서 부모 클래스의 기능을 수정하지 않고 사용할 수 있어야 한다. 상속 관계에서는 일반화 관계(IS-A)가 성립해야 한다. 결국 OCP의 확장된 형태이다. 

{% highlight cs %}
public abstract class Employee
{
    public virtual string GetProjectDetails(int employeeId)
    {
        return "Base Project";
    }
    public virtual string GetEmployeeDetails(int employeeId)
    {
        return "Base Employee";
    }
}
{% endhighlight %}

{% highlight cs %}
public class ContractualEmployee : Employee
{
    public override string GetProjectDetails(int employeeId)
    {
        return "Child Project";
    }
    /* May be for contractual employee
    we do not need to store the details into database. */
    public override string GetEmployeeDetails(int employeeId)
    {
        throw new NotImplementedException();
    }
}
{% endhighlight %}

{% highlight cs %}
List<Employee> employeeList = new List<Employee>();
employeeList.Add(new ContractualEmployee());
employeeList.Add(new CasualEmployee());
foreach (Employee e in employeeList)
{
    e.GetEmployeeDetails(1245);
}
{% endhighlight %}

ContractualEmployee는 not implemented exception이 발생한다. LSP를 위반하는 것이다. 이를 해결하려면 2개의 인터페이스로 나누면 된다. 

{% highlight cs %}
public interface IEmployee
{
    string GetEmployeeDetails(int employeeId);
}
public interface IProject
{
    string GetProjectDetails(int employeeId);
}
{% endhighlight %}


## Interface Segregation Principle (ISP)

a. Clients should not be forced to depend upon interfaces that they do not use.

**인터페이스 분리의 원칙**의 원리는 한 클래스가 자신이 사용하지 않는 인터페이스는 구현하지 않는 것이다. 하나의 큰 인터페이스 보다 하나의 서브 모듈을 제공하는 여러 개의 구체적인 인터페이스가 낫다.

{% highlight cs %}
public class Manager: ILead  
{  
    public void AssignTask()  
    {  
        //Code to assign a task.  
    }  
    public void CreateSubTask()  
    {  
        //Code to create a sub task.  
    }  
    public void WorkOnTask()  
    {
        // ISP 위반
        throw new Exception("Manager can't work on Task");  
    }  
}  
{% endhighlight %}

## Dependency Inversion Principle (DIP)

a. The Dependency Inversion Principle (DIP) states that high-level modules/classes should not depend on low-level modules/classes. Both should depend upon abstractions.

b. Abstractions should not depend upon details. Details should depend upon abstractions.

**의존성역전의 원칙**은 구체적인 클래스에 의존하는 것보다 추상 클래스에 의존하는 것을 의미한다.

{% highlight cs %}
public interface IEmployeeDataAccess
{
    Employee GetEmployeeDetails(int id);
}
{% endhighlight %}

{% highlight cs %}
public class EmployeeDataAccess : IEmployeeDataAccess
{
    public Employee GetEmployeeDetails(int id)
    {
        Employee emp = new Employee()
        {
            ...
        };
        return emp;
    }
}
{% endhighlight %}

{% highlight cs %}
public class DataAccessFactory
{
    public static IEmployeeDataAccess GetEmployeeDataAccessObj()
    {
        return new EmployeeDataAccess();
    }
}
{% endhighlight %}

#### Reference

[SOLID Principles In C#](https://www.c-sharpcorner.com/UploadFile/damubetha/solid-principles-in-C-Sharp/)\
[객체지향 개발 5대 원리](https://www.nextree.co.kr/p6960/)