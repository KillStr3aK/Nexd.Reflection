# Description

Provides generic methods that gives you access to classes, methods, properties and fields that is unavailable due to their protection level.

# Installation

Available on [NuGet](https://www.nuget.org/packages/Nexd.Reflection/)
[![NuGet version (Nexd.Reflection)](https://img.shields.io/nuget/v/Nexd.Reflection.svg?style=flat-square)](https://www.nuget.org/packages/Nexd.Reflection/)

```
dotnet add package Nexd.Reflection --version 1.0.1
```

# Examples

#### Assembly A

```csharp
namespace Example
{
    public class ExampleClass
    {
        private int ExampleField;
        
        private int ExampleProperty { get; set; }
        
        private static int ExampleStaticField;
        
        private static int ExampleStaticProperty { get; set; }
        
        private void ExampleVoidMethod()
            { }
            
        private int ExampleIntMethod()
            => this.ExampleField;
            
        private static void ExampleStaticVoidMethod()
            { }
        
        private static int ExampleStaticIntMethod()
            => 5; // Constant value for demonstration
    }
}
```

#### Your code

```csharp
namespace YourCode
{
    using Nexd.Reflection;

    public class Program
    {
        public static void Main(string[] args)
        {
            // You have to get an instance on your own, or instantiate a new one, really depends on context
            // Instance only required for non-static fields, properties and methods
            ExampleClass instance = ...;

            /* Instance */

            // Get instance field
            int fieldValue = Pumped.GetFieldValue<int, ExampleClass>(instance, "ExampleField");

            // Set instance field
            Pumped.SetFieldValue<ExampleClass, int>(instance, "ExampleField", fieldValue + 1);

            // Get instance property
            int propertyValue = Pumped.GetPropertyValue<int, ExampleClass>(instance, "ExampleProperty");

            // Set instance property
            Pumped.SetPropertyValue<ExampleClass, int>(instance, "ExampleProperty", propertyValue + 1);

            // Invoke instance void method
            Pumped.InvokeVoid<ExampleClass>(instance, "ExampleVoidMethod");

            // Invoke instance method
            int result = Pumped.Invoke<int, ExampleClass>(instance, "ExampleIntMethod");

            /* Static */

            // Get static field
            int staticFieldValue = Pumped.GetFieldValue<int, ExampleClass>("ExampleStaticField"); /* or Pumped.GetFieldValue<int, ExampleClass>(null, "ExampleStaticField"); */

            // Set static field
            Pumped.SetFieldValue<ExampleClass, int>("ExampleStaticField", staticFieldValue + 1); /* or Pumped.SetFieldValue<ExampleClass, int>(null, "ExampleStaticField", staticFieldValue + 1); */

            // Get static property
            int staticPropertyValue = Pumped.GetPropertyValue<int, ExampleClass>("ExampleStaticProperty"); /* or Pumped.GetPropertyValue<int, ExampleClass>(null, "ExampleStaticProperty"); */

            // Set static property
            Pumped.SetPropertyValue<ExampleClass, int>("ExampleStaticProperty", staticPropertyValue + 1); /* or Pumped.SetPropertyValue<ExampleClass, int>(null, "ExampleStaticProperty", staticPropertyValue + 1); */

            // Invoke static void method
            Pumped.InvokeVoid<ExampleClass>("ExampleStaticVoidMethod"); /* or Pumped.InvokeVoid<ExampleClass>(null, "ExampleStaticVoidMethod"); */

            // Invoke static method
            int result = Pumped.Invoke<int, ExampleClass>("ExampleStaticIntMethod"); /* or Pumped.Invoke<int, ExampleClass>(null, "ExampleStaticIntMethod"); */
        } 
    }
}
```

#### Internal Class

```csharp
namespace Example
{
    internal class ExampleClass
    {
        static void InternalStaticVoidMethod()
            { }
        
        static int InternalStaticIntMethod()
            => 0;
    }
}

namespace YourCode
{
    using System.Reflection;
    using Nexd.Reflection;

    public class Program
    {
        public static void Main(string[] args)
        {
            // Get target Assembly instance
            Assembly targetAssembly = Pumped.GetAssembly("TargetAssembly"); // AssemblyName.Name, so no .dll needed

            if(targetAssembly != null)
            {
                int result = Pumped.InvokeInternal<int>(targetAssembly, "ExampleClass", "InternalStaticIntMethod");

                Pumped.InvokeInternalVoid(targetAssembly, "ExampleClass", "InternalStaticVoidMethod");
            }
        } 
    }
}
```
