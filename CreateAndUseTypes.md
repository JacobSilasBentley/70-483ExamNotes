# 2 Create and Use Types

## 2.3 Enforce Encapsulation

- Encapsulation is a key Object Orientated Software
- It enables you to restrict access to types and type members

```csharp
public class Dog
{
  // accessible everywhere
  public void Bark(){ };
  
  // only accessible inside the assembly
  internal void Feed(){ };
  
  // only acccesible inside the class and derived classes
  protected void Walk(){ };
  
  // only available to derived classes or inside the assembly
  protected internal void Pat(){ };
  
  // only available inside the original class
  private void putDown(){ };
}
```

A private field or property can be exposed to a higher access level using a property
```csharp
public class Example
{
  private myField;
  
  public MyProperty
  {
    get { return myField; }
    set { myField = value; }
  }
}
```
