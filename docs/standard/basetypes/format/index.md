---
title: Formatting Types
description: Formatting Types
keywords: .NET, .NET Core
author: shoag
manager: wpickett
ms.date: 06/20/2016
ms.topic: article
ms.prod: .net-core
ms.technology: .net-core-technologies
ms.devlang: dotnet
ms.assetid: 371ad7d7-e3ce-4be3-83f3-1f2c2e4e6d14
---

# Formatting Types

Formatting is the process of converting an instance of a class, structure, or enumeration value to its string representation, often so that the resulting string can be displayed to users or deserialized to restore the original data type. This conversion can pose a number of challenges:

* The way that values are stored internally does not necessarily reflect the way that users want to view them. For example, a telephone number might be stored in the form **8009999999**, which is not user-friendly. It should instead be displayed as **800-999-9999**. See the [Custom Format Strings](#Custom-Format-Strings) section for an example that formats a number in this way. 

* Sometimes the conversion of an object to its string representation is not intuitive. For example, it is not clear how the string representation of a **Temperature** object or a **Person** object should appear. For an example that formats a **Temperature** object in a variety of ways, see the [Standard Format Strings](#Standard-Format-Strings) section.

* Values often require culture-sensitive formatting. For example, in an application that uses numbers to reflect monetary values, numeric strings should include the current culture’s currency symbol, group separator (which, in most cultures, is the thousands separator), and decimal symbol. For an example, see the [Culture-Sensitive Formatting with Format Providers and the IFormatProvider Interface](#Culture-Sensitive-Formatting-with-Format-Providers-and-the-IFormatProvider-Interface) section. 

* An application may have to display the same value in different ways. For example, an application may represent an enumeration member by displaying a string representation of its name or by displaying its underlying value. For an example that formats a member of the [DayOfWeek](https://docs.microsoft.com/dotnet/core/api/System.DayOfWeek) enumeration in different ways, see the [Standard Format Strings](#Standard-Format-Strings) section.

.NET Core provides rich formatting support that enables developers to address these requirements. 

> **Note**
>
> Formatting converts the value of a type into a string representation. Parsing is the inverse of formatting. A parsing operation creates an instance of a data type from its string representation. 

This overview contains the following sections:

* [Formatting in .NET Core](#Formatting-in-.NET-Core)

* [Default Formatting Using the ToString Method](#Default-Formatting-Using-the-ToString-Method)

* [Overriding the ToString Method](#Overriding-the-ToString-Method)

* [The ToString Method and Format Strings](#The-ToString-Method-and-Format-Strings)

    * [Standard Format Strings](#Standard-Format-Strings)
    
    * [Custom Format Strings](#Custom-Format-Strings)
    
    * [Format Strings and .NET Core Types](#Format-Strings-and-.NET-Core-Types)
    
* [Culture-Sensitive Formatting with Format Providers and the IFormatProvider Interface](#Culture-Sensitive-Formatting-with-Format-Providers-and-the-IFormatProvider-Interface)

    * [Culture-Sensitive Formatting of Numeric Values](#Culture-Sensitive-Formatting-of-Numeric-Values)
    
    * [Culture-Sensitive Formatting of Date and Time Values](#Culture-Sensitive-Formatting-of-Date-and-Time-Values)
    
* [The IFormattable Interface](#The-IFormattable-Interface)

* [Composite Formatting](#Composite-Formatting)

* [Custom Formatting with ICustomFormatter](#Custom-Formatting-with-ICustomFormatter)

* [Related Topics](#Related-Topics)

* [Reference](#Reference)

## Formatting in .NET Core

The basic mechanism for formatting is the default implementation of the [Object.ToString](https://docs.microsoft.com/dotnet/core/api/System.Object#System_Object_ToString) method, which is discussed in the [Default Formatting Using the ToString Method](#Default-Formatting-Using-the-ToString-Method) section later in this topic. However, .NET Core provides several ways to modify and extend its default formatting support. These include the following:

* Overriding the [Object.ToString](https://docs.microsoft.com/dotnet/core/api/System.Object#System_Object_ToString) method to define a custom string representation of an object’s value. For more information, see the [Overriding the ToString Method](#Overriding-the-ToString-Method) section later in this topic.

* Defining format specifiers that enable the string representation of an object’s value to take multiple forms. For example, the "X" format specifier in the following statement converts an integer to the string representation of a hexadecimal value.

  ```csharp
  int integerValue = 60312;
Console.WriteLine(integerValue.ToString("X"));   // Displays EB98.
  ```
  
  For more information about format specifiers, see the [The ToString Method and Format Strings](#The-ToString-Method-and-Format-Strings) section.
  
* Using format providers to take advantage of the formatting conventions of a specific culture. For example, the following statement displays a currency value by using the formatting conventions of the en-US culture. 

  ```csharp
  double cost = 1632.54; 
Console.WriteLine(cost.ToString("C", 
                  new System.Globalization.CultureInfo("en-US")));   
// The example displays the following output:
//       $1,632.54
  ```
  
  For more information about formatting with format providers, see the [Culture-Sensitive Formatting with Format Providers and the IFormatProvider Interface](#Culture-Sensitive-Formatting-with-Format-Providers-and-the-IFormatProvider-Interface) section.  
  
* Implementing the [IFormattable](https://docs.microsoft.com/dotnet/core/api/System.IFormattable) interface to support both string conversion with the [Convert](https://docs.microsoft.com/dotnet/core/api/System.Convert) class and composite formatting. For more information, see the [The IFormattable Interface](#The-IFormattable-Interface) section.

* Using composite formatting to embed the string representation of a value in a larger string. For more information, see the [Composite Formatting](#Composite-Formatting) section.

* Implementing [ICustomFormatter](https://docs.microsoft.com/dotnet/core/api/System.ICustomFormatter) and [IFormatProvider](https://docs.microsoft.com/dotnet/core/api/System.IFormatProvider) to provide a complete custom formatting solution. For more information, see the [Custom Formatting with ICustomFormatter](#Custom-Formatting-with-ICustomFormatter) section.

The following sections examine these methods for converting an object to its string representation.

## Default Formatting Using the ToString Method

Every type that is derived from [System.Object](https://docs.microsoft.com/dotnet/core/api/System.Object) automatically inherits a parameterless [ToString](https://docs.microsoft.com/dotnet/core/api/System.Object#System_Object_ToString) method, which returns the name of the type by default. The following example illustrates the default [ToString](https://docs.microsoft.com/dotnet/core/api/System.Object#System_Object_ToString) method. It defines a class named `Automobile` that has no implementation. When the class is instantiated and its [ToString](https://docs.microsoft.com/dotnet/core/api/System.Object#System_Object_ToString) method is called, it displays its type name. Note that the [ToString](https://docs.microsoft.com/dotnet/core/api/System.Object#System_Object_ToString) method is not explicitly called in the example. The [Console.WriteLine(Object)](https://docs.microsoft.com/dotnet/core/api/System.Console#System_Console_WriteLine_System_Object_) method implicitly calls the [ToString](https://docs.microsoft.com/dotnet/core/api/System.Object#System_Object_ToString) method of the object passed to it as an argument. 

```csharp
using System;

public class Automobile
{
   // No implementation. All members are inherited from Object.
}

public class Example
{
   public static void Main()
   {
      Automobile firstAuto = new Automobile();
      Console.WriteLine(firstAuto);
   }
}
// The example displays the following output:
//       Automobile
```

Because all types other than interfaces are derived from [Object](https://docs.microsoft.com/dotnet/core/api/System.Object), this functionality is automatically provided to your custom classes or structures. However, the functionality offered by the default [ToString](https://docs.microsoft.com/dotnet/core/api/System.Object#System_Object_ToString) method, is limited: Although it identifies the type, it fails to provide any information about an instance of the type. To provide a string representation of an object that provides information about that object, you must override the [ToString](https://docs.microsoft.com/dotnet/core/api/System.Object#System_Object_ToString) method.

> **Note**
>
> Structures inherit from [ValueType](https://docs.microsoft.com/dotnet/core/api/System.ValueType), which in turn is derived from [Object](https://docs.microsoft.com/dotnet/core/api/System.Object). Although [ValueType](https://docs.microsoft.com/dotnet/core/api/System.ValueType) overrides [Object.ToString](https://docs.microsoft.com/dotnet/core/api/System.Object#System_Object_ToString), its implementation is identical.

## Overriding the ToString Method

Displaying the name of a type is often of limited use and does not allow consumers of your types to differentiate one instance from another. However, you can override the [ToString](https://docs.microsoft.com/dotnet/core/api/System.Object#System_Object_ToString) method to provide a more useful representation of an object’s value. The following example defines a `Temperature` object and overrides its [ToString](https://docs.microsoft.com/dotnet/core/api/System.Object#System_Object_ToString) method to display the temperature in degrees Celsius.

```csharp
using System;

public class Temperature
{
   private decimal temp;

   public Temperature(decimal temperature)
   {
      this.temp = temperature;   
   }

   public override string ToString()
   {
      return this.temp.ToString("N1") + "°C";
   }
}

public class Example
{
   public static void Main()
   {
      Temperature currentTemperature = new Temperature(23.6m);
      Console.WriteLine("The current temperature is " +
                        currentTemperature.ToString());
   }
}
// The example displays the following output:
//       The current temperature is 23.6°C.
```

In .NET Core, the [ToString](https://docs.microsoft.com/dotnet/core/api/System.Object#System_Object_ToString) method of each primitive value type has been overridden to display the object’s value instead of its name. The following table shows the override for each primitive type. Note that most of the overridden methods call another overload of the [ToString](https://docs.microsoft.com/dotnet/core/api/System.Object#System_Object_ToString) method and pass it the "G" format specifier, which defines the general format for its type, and an [IFormatProvider](https://docs.microsoft.com/dotnet/core/api/System.IFormatProvider) object that represents the current culture.

Type | ToString override
---- | -----------------
[Boolean](https://docs.microsoft.com/dotnet/core/api/System.Boolean) | Returns either [Boolean.TrueString](https://docs.microsoft.com/dotnet/core/api/System.Boolean#System_Boolean_TrueString) or [Boolean.FalseString](https://docs.microsoft.com/dotnet/core/api/System.Boolean#System_Boolean_FalseString).
[Byte](https://docs.microsoft.com/dotnet/core/api/System.Byte) | Calls `Byte.ToString("G", NumberFormatInfo.CurrentInfo)` to format the [Byte](https://docs.microsoft.com/dotnet/core/api/System.Byte) value for the current culture.
[Char](https://docs.microsoft.com/dotnet/core/api/System.Char) | Returns the character as a string.
[DateTime](https://docs.microsoft.com/dotnet/core/api/System.DateTime) | Calls `DateTime.ToString("G", DatetimeFormatInfo.CurrentInfo)` to format the date and time value for the current culture. 
[Decimal](https://docs.microsoft.com/dotnet/core/api/System.Decimal) | Calls `Decimal.ToString("G", NumberFormatInfo.CurrentInfo)` to format the [Decimal](https://docs.microsoft.com/dotnet/core/api/System.Decimal) value for the current culture.
[Double](https://docs.microsoft.com/dotnet/core/api/System.Double) | Calls `Double.ToString("G", NumberFormatInfo.CurrentInfo)` to format the [Double](https://docs.microsoft.com/dotnet/core/api/System.Double) value for the current culture.
[Int16](https://docs.microsoft.com/dotnet/core/api/System.Int16) | Calls `Int16.ToString("G", NumberFormatInfo.CurrentInfo)` to format the [Int16](https://docs.microsoft.com/dotnet/core/api/System.Int16) value for the current culture.
[Int32](https://docs.microsoft.com/dotnet/core/api/System.Int32) | Calls `Int16.ToString("G", NumberFormatInfo.CurrentInfo)` to format the [Int32](https://docs.microsoft.com/dotnet/core/api/System.Int32) value for the current culture.
[Int64](https://docs.microsoft.com/dotnet/core/api/System.Int64) | Calls `Int16.ToString("G", NumberFormatInfo.CurrentInfo)` to format the [Int64](https://docs.microsoft.com/dotnet/core/api/System.Int64) value for the current culture.
[SByte](https://docs.microsoft.com/dotnet/core/api/System.SByte) | Calls `Int16.ToString("G", NumberFormatInfo.CurrentInfo)` to format the [SByte](https://docs.microsoft.com/dotnet/core/api/System.SByte) | value for the current culture.
[Single](https://docs.microsoft.com/dotnet/core/api/System.Single) | Calls `Int16.ToString("G", NumberFormatInfo.CurrentInfo)` to format the [Single](https://docs.microsoft.com/dotnet/core/api/System.Single) value for the current culture.
[UInt32](https://docs.microsoft.com/dotnet/core/api/System.UInt32) | Calls `Int16.ToString("G", NumberFormatInfo.CurrentInfo)` to format the [UInt32](https://docs.microsoft.com/dotnet/core/api/System.UInt32)value for the current culture.
[UInt32](https://docs.microsoft.com/dotnet/core/api/System.UInt32) | Calls `Int16.ToString("G", NumberFormatInfo.CurrentInfo)` to format the [UInt32](https://docs.microsoft.com/dotnet/core/api/System.UInt32) value for the current culture.
[UInt64](https://docs.microsoft.com/dotnet/core/api/System.UInt64) | Calls `Int16.ToString("G", NumberFormatInfo.CurrentInfo)` to format the [UInt64](https://docs.microsoft.com/dotnet/core/api/System.UInt64)  value for the current culture.

## The ToString Method and Format Strings

Relying on the default [ToString](https://docs.microsoft.com/dotnet/core/api/System.Object#System_Object_ToString) method or overriding [ToString](https://docs.microsoft.com/dotnet/core/api/System.Object#System_Object_ToString) is appropriate when an object has a single string representation. However, the value of an object often has multiple representations. For example, a temperature can be expressed in degrees Fahrenheit, degrees Celsius, or kelvins. Similarly, the integer value 10 can be represented in numerous ways, including 10, 10.0, 1.0e01, or $10.00.

To enable a single value to have multiple string representations, .NET Core uses format strings. A format string is a string that contains one or more predefined format specifiers, which are single characters or groups of characters that define how the [ToString](https://docs.microsoft.com/dotnet/core/api/System.Object#System_Object_ToString) method should format its output. The format string is then passed as a parameter to the object's [ToString](https://docs.microsoft.com/dotnet/core/api/System.Object#System_Object_ToString) method and determines how the string representation of that object's value should appear.

All numeric types, date and time types, and enumeration types in .NET Core support a predefined set of format specifiers. You can also use format strings to define multiple string representations of your application-defined data types.

### Standard Format Strings

A standard format string contains a single format specifier, which is an alphabetic character that defines the string representation of the object to which it is applied, along with an optional precision specifier that affects how many digits are displayed in the result string. If the precision specifier is omitted or is not supported, a standard format specifier is equivalent to a standard format string. 

.NET Core defines a set of standard format specifiers for all numeric types, all date and time types, and all enumeration types. For example, each of these categories supports a "G" standard format specifier, which defines a general string representation of a value of that type.

Standard format strings for enumeration types directly control the string representation of a value. The format strings passed to an enumeration value’s [ToString](https://docs.microsoft.com/dotnet/core/api/System.Object#System_Object_ToString) method determine whether the value is displayed using its string name (the "G" and "F" format specifiers), its underlying integral value (the "D" format specifier), or its hexadecimal value (the "X" format specifier). The following example illustrates the use of standard format strings to format a [DayOfWeek](https://docs.microsoft.com/dotnet/core/api/System.DayOfWeek) enumeration value. 

```csharp
DayOfWeek thisDay = DayOfWeek.Monday;
string[] formatStrings = {"G", "F", "D", "X"};

foreach (string formatString in formatStrings)
   Console.WriteLine(thisDay.ToString(formatString));
// The example displays the following output:
//       Monday
//       Monday
//       1
//       00000001
```

For information about enumeration format strings, see [Enumeration Format Strings](enumerationformat.md).

Standard format strings for numeric types usually define a result string whose precise appearance is controlled by one or more property values. For example, the "C" format specifier formats a number as a currency value. When you call the [ToString](https://docs.microsoft.com/dotnet/core/api/System.Object#System_Object_ToString) method with the "C" format specifier as the only parameter, the following property values from the current culture’s [NumberFormatInfo](https://docs.microsoft.com/dotnet/core/api/System.Globalization.NumberFormatInfo) object are used to define the string representation of the numeric value:

* The [CurrencySymbol](https://docs.microsoft.com/dotnet/core/api/System.Globalization.NumberFormatInfo#System_Globalization_NumberFormatInfo_CurrencySymbol) property, which specifies the current culture’s currency symbol.

* The [CurrencyNegativePattern](https://docs.microsoft.com/dotnet/core/api/System.Globalization.NumberFormatInfo#System_Globalization_NumberFormatInfo_CurrencyNegativePattern) or [CurrencyPositivePattern]()https://docs.microsoft.com/dotnet/core/api/System.Globalization.NumberFormatInfo#System_Globalization_NumberFormatInfo_CurrencyPositivePattern property, which returns an integer that determines the following: 

    * The placement of the currency symbol.
    
    * Whether negative values are indicated by a leading negative sign, a trailing negative sign, or parentheses.
    
    * Whether a space appears between the numeric value and the currency symbol.
    
* The [CurrencyDecimalDigits](https://docs.microsoft.com/dotnet/core/api/System.Globalization.NumberFormatInfo#System_Globalization_NumberFormatInfo_CurrencyDecimalDigits) property, which defines the number of fractional digits in the result string.

* The [CurrencyDecimalSeparator](https://docs.microsoft.com/dotnet/core/api/System.Globalization.NumberFormatInfo#System_Globalization_NumberFormatInfo_CurrencyDecimalSeparator) property, which defines the decimal separator symbol in the result string.

* The [CurrencyGroupSeparator](https://docs.microsoft.com/dotnet/core/api/System.Globalization.NumberFormatInfo#System_Globalization_NumberFormatInfo_CurrencyGroupSeparator) property, which defines the group separator symbol.

* The [CurrencyGroupSizes](https://docs.microsoft.com/dotnet/core/api/System.Globalization.NumberFormatInfo#System_Globalization_NumberFormatInfo_CurrencyGroupSizes) property, which defines the number of digits in each group to the left of the decimal.

* The [NegativeSign](https://docs.microsoft.com/dotnet/core/api/System.Globalization.NumberFormatInfo#System_Globalization_NumberFormatInfo_NegativeSign) property, which determines the negative sign used in the result string if parentheses are not used to indicate negative values.

In addition, numeric format strings may include a precision specifier. The meaning of this specifier depends on the format string with which it is used, but it typically indicates either the total number of digits or the number of fractional digits that should appear in the result string. For example, the following example uses the "X4" standard numeric string and a precision specifier to create a string value that has four hexadecimal digits.

```csharp
byte[] byteValues = { 12, 163, 255 };
foreach (byte byteValue in byteValues)
   Console.WriteLine(byteValue.ToString("X4"));
// The example displays the following output:
//       000C
//       00A3
//       00FF
```

For more information about standard numeric formatting strings, see [Standard Numeric Format Strings](standardnumeric.md). 

Standard format strings for date and time values are aliases for custom format strings stored by a particular [DateTimeFormatInfo](https://docs.microsoft.com/dotnet/core/api/System.Globalization.DateTimeFormatInfo) class. For example, calling the [ToString](https://docs.microsoft.com/dotnet/core/api/System.Object#System_Object_ToString) method of a date and time value with the "D" format specifier displays the date and time by using the custom format string stored in the current culture’s [DateTimeFormatInfo.LongDatePattern](https://docs.microsoft.com/dotnet/core/api/System.Globalization.DateTimeFormatInfo#System_Globalization_DateTimeFormatInfo_LongDatePattern) property. (For more information about custom format strings, see the [Custom Format Strings](#Custom-Format-Strings) section.) The following example illustrates this relationship.

```csharp
using System;
using System.Globalization;

public class Example
{
   public static void Main()
   {
      DateTime date1 = new DateTime(2017, 6, 30);
      Console.WriteLine("D Format Specifier:     {0:D}", date1);
      string longPattern = CultureInfo.CurrentCulture.DateTimeFormat.LongDatePattern;
      Console.WriteLine("'{0}' custom format string:     {1}", 
                        longPattern, date1.ToString(longPattern));
   }
}
// The example displays the following output when run on a system whose
// current culture is en-US:
//    D Format Specifier:     Tuesday, June 30, 2017
//    'dddd, MMMM dd, yyyy' custom format string:     Tuesday, June 30, 2017
```

For more information about standard date and time format strings, see [Standard Date and Time Format Strings](standarddatetime.md).

You can also use standard format strings to define the string representation of an application-defined object that is produced by the object’s `ToString(String)` method. You can define the specific standard format specifiers that your object supports, and you can determine whether they are case-sensitive or case-insensitive. Your implementation of the `ToString(String)` method should support the following:

* A "G" format specifier that represents a customary or common format of the object. The parameterless overload of your object's `ToString` method should call its `ToString(String)` overload and pass it the "G" standard format string.

* Support for a format specifier that is equal to a null reference. A format specifier that is equal to a null reference should be considered equivalent to the "G" format specifier.

For example, a `Temperature` class can internally store the temperature in degrees Celsius and use format specifiers to represent the value of the `Temperature` object in degrees Celsius, degrees Fahrenheit, and kelvins. The following example provides an illustration.

```csharp
using System;

public class Temperature
{
   private decimal m_Temp;

   public Temperature(decimal temperature)
   {
      this.m_Temp = temperature;
   }

   public decimal Celsius
   {
      get { return this.m_Temp; }
   }

   public decimal Kelvin
   {
      get { return this.m_Temp + 273.15m; }   
   }

   public decimal Fahrenheit
   {
      get { return Math.Round(((decimal) (this.m_Temp * 9 / 5 + 32)), 2); }
   }

   public override string ToString()
   {
      return this.ToString("C");
   }

   public string ToString(string format)
   {  
      // Handle null or empty string.
      if (String.IsNullOrEmpty(format)) format = "C";
      // Remove spaces and convert to uppercase.
      format = format.Trim().ToUpperInvariant();      

      // Convert temperature to Fahrenheit and return string.
      switch (format)
      {
         // Convert temperature to Fahrenheit and return string.
         case "F":
            return this.Fahrenheit.ToString("N2") + " °F";
         // Convert temperature to Kelvin and return string.
         case "K":
            return this.Kelvin.ToString("N2") + " K";
         // return temperature in Celsius.
         case "G":
         case "C":
            return this.Celsius.ToString("N2") + " °C";
         default:
            throw new FormatException(String.Format("The '{0}' format string is not supported.", format));
      }      
   }
}

public class Example
{
   public static void Main()
   {
      Temperature temp1 = new Temperature(0m);
      Console.WriteLine(temp1.ToString());
      Console.WriteLine(temp1.ToString("G"));
      Console.WriteLine(temp1.ToString("C"));
      Console.WriteLine(temp1.ToString("F"));
      Console.WriteLine(temp1.ToString("K"));

      Temperature temp2 = new Temperature(-40m);
      Console.WriteLine(temp2.ToString());
      Console.WriteLine(temp2.ToString("G"));
      Console.WriteLine(temp2.ToString("C"));
      Console.WriteLine(temp2.ToString("F"));
      Console.WriteLine(temp2.ToString("K"));

      Temperature temp3 = new Temperature(16m);
      Console.WriteLine(temp3.ToString());
      Console.WriteLine(temp3.ToString("G"));
      Console.WriteLine(temp3.ToString("C"));
      Console.WriteLine(temp3.ToString("F"));
      Console.WriteLine(temp3.ToString("K"));

      Console.WriteLine(String.Format("The temperature is now {0:F}.", temp3));
   }
}
// The example displays the following output:
//       0.00 °C
//       0.00 °C
//       0.00 °C
//       32.00 °F
//       273.15 K
//       -40.00 °C
//       -40.00 °C
//       -40.00 °C
//       -40.00 °F
//       233.15 K
//       16.00 °C
//       16.00 °C
//       16.00 °C
//       60.80 °F
//       289.15 K
//       The temperature is now 16.00 °C.
```

### Custom Format Strings

In addition to the standard format strings, .NET Core defines custom format strings for both numeric values and date and time values. A custom format string consists of one or more custom format specifiers that define the string representation of a value. For example, the custom date and time format string "yyyy/mm/dd hh:mm:ss.ffff t zzz" converts a date to its string representation in the form "2008/11/15 07:45:00.0000 P -08:00" for the en-US culture. Similarly, the custom format string "0000" converts the integer value 12 to "0012". For a complete list of custom format strings, see [Custom Date and Time Format Strings](customdatetime.md) and [Custom Numeric Format Strings](customnumeric.md).

If a format string consists of a single custom format specifier, the format specifier should be preceded by the percent (%) symbol to avoid confusion with a standard format specifier. The following example uses the "M" custom format specifier to display a one-digit or two-digit number of the month of a particular date.

```csharp
DateTime date1 = new DateTime(2009, 9, 8);
Console.WriteLine(date1.ToString("%M"));       
// Displays 9
```

Many standard format strings for date and time values are aliases for custom format strings that are defined by properties of the [DateTimeFormatInfo](https://docs.microsoft.com/dotnet/core/api/System.Globalization.DateTimeFormatInfo) object. Custom format strings also offer considerable flexibility in providing application-defined formatting for numeric values or date and time values. You can define your own custom result strings for both numeric values and date and time values by combining multiple custom format specifiers into a single custom format string. The following example defines a custom format string that displays the day of the week in parentheses after the month name, day, and year.

```csharp
string customFormat = "MMMM dd, yyyy (dddd)";
DateTime date1 = new DateTime(2009, 8, 28);
Console.WriteLine(date1.ToString(customFormat));   
// The example displays the following output if run on a system
// whose language is English:
//       August 28, 2009 (Friday) 
```

The following example defines a custom format string that displays an [Int64](https://docs.microsoft.com/dotnet/core/api/System.Int64) value as a standard, seven-digit U.S. telephone number along with its area code. 

```csharp
using System;

public class Example
{
   public static void Main()
   {
      long number = 8009999999;
      string fmt = "000-000-0000";
      Console.WriteLine(number.ToString(fmt));
   }
}
// The example displays the following output:
//        800-999-9999
```

Although standard format strings can generally handle most of the formatting needs for your application-defined types, you may also define custom format specifiers to format your types. 

### Format Strings and .NET Core Types

All numeric types (that is, the [Byte](https://docs.microsoft.com/dotnet/core/api/System.Byte), [Decimal](https://docs.microsoft.com/dotnet/core/api/System.Decimal), [Double](https://docs.microsoft.com/dotnet/core/api/System.Double), [Int16](https://docs.microsoft.com/dotnet/core/api/System.Int16), [Int32](https://docs.microsoft.com/dotnet/core/api/System.Int32), [Int64](https://docs.microsoft.com/dotnet/core/api/System.Int64), [SByte](https://docs.microsoft.com/dotnet/core/api/System.SByte), [Single](https://docs.microsoft.com/dotnet/core/api/System.Single), [UInt16](https://docs.microsoft.com/dotnet/core/api/System.UInt16), [UInt32](https://docs.microsoft.com/dotnet/core/api/System.UInt32), [UInt64](https://docs.microsoft.com/dotnet/core/api/System.UInt64), and [BigInteger](https://docs.microsoft.com/dotnet/core/api/System.Numerics.BigInteger) types), as well as the [DateTime](https://docs.microsoft.com/dotnet/core/api/System.DateTime), [DateTimeOffset](https://docs.microsoft.com/dotnet/core/api/System.DateTimeOffset), [TimeSpan](https://docs.microsoft.com/dotnet/core/api/System.TimeSpan), [Guid](https://docs.microsoft.com/dotnet/core/api/System.Guid), and all enumeration types, support formatting with format strings. For information on the specific format strings supported by each type, see the following topics: 

Title | Definition
----- | ----------
[Standard Numeric Format Strings](standardnumeric.md) | Describes standard format strings that create commonly used string representations of numeric values. 
[Custom Numeric Format Strings](customnumeric.md) | Describes custom format strings that create application-specific formats for numeric values.
[Standard Date and Time Format Strings](standarddatetime.md) | Describes standard format strings that create commonly used string representations of [DateTime](https://docs.microsoft.com/dotnet/core/api/System.DateTime) values.
[Custom Date and Time Format Strings](customdatetime.md) | Describes custom format strings that create application-specific formats for [DateTime](https://docs.microsoft.com/dotnet/core/api/System.DateTime) values.
[Standard TimeSpan Format Strings](standardtimespan.md) | Describes standard format strings that create commonly used string representations of time intervals.
[Custom TimeSpan Format Strings](customtimespan.md) | Describes custom format strings that create application-specific formats for time intervals.
[Enumeration Format Strings](enumerationformat.md) | Describes standard format strings that are used to create string representations of enumeration values.
[Guid.ToString(String)](https://docs.microsoft.com/dotnet/core/api/System.Guid#System_Guid_ToString_System_String_) | Describes standard format strings for [Guid](https://docs.microsoft.com/dotnet/core/api/System.Guid) values.

## Culture-Sensitive Formatting with Format Providers and the IFormatProvider Interface

Although format specifiers let you customize the formatting of objects, producing a meaningful string representation of objects often requires additional formatting information. For example, formatting a number as a currency value by using either the "C" standard format string or a custom format string such as "$ #,#.00" requires, at a minimum, information about the correct currency symbol, group separator, and decimal separator to be available to include in the formatted string. In .NET Core, this additional formatting information is made available through the [IFormatProvider](https://docs.microsoft.com/dotnet/core/api/System.IFormatProvider) interface, which is provided as a parameter to one or more overloads of the `ToString` method of numeric types and date and time types. [IFormatProvider](https://docs.microsoft.com/dotnet/core/api/System.IFormatProvider) implementations are used in .NET Core to support culture-specific formatting. The following example illustrates how the string representation of an object changes when it is formatted with three [IFormatProvider](https://docs.microsoft.com/dotnet/core/api/System.IFormatProvider) objects that represent different cultures.

```csharp
using System;
using System.Globalization;

public class Example
{
   public static void Main()
   {
      decimal value = 1603.42m;
      Console.WriteLine(value.ToString("C3", new CultureInfo("en-US")));
      Console.WriteLine(value.ToString("C3", new CultureInfo("fr-FR")));
      Console.WriteLine(value.ToString("C3", new CultureInfo("de-DE")));
   }
}
// The example displays the following output:
//       $1,603.420
//       1 603,420 €
//       1.603,420 €
```

The [IFormatProvider](https://docs.microsoft.com/dotnet/core/api/System.IFormatProvider) interface includes one method, [GetFormat(Type)](https://docs.microsoft.com/dotnet/core/api/System.IFormatProvider#System_IFormatProvider_GetFormat_System_Type_), which has a single parameter that specifies the type of object that provides formatting information. If the method can provide an object of that type, it returns it. Otherwise, it returns a null reference.

[IFormatProvider.GetFormat](https://docs.microsoft.com/dotnet/core/api/System.IFormatProvider#System_IFormatProvider_GetFormat_System_Type_) is a callback method. When you call a `ToString` method overload that includes an [IFormatProvider](https://docs.microsoft.com/dotnet/core/api/System.IFormatProvider) parameter, it calls the [GetFormat](https://docs.microsoft.com/dotnet/core/api/System.IFormatProvider#System_IFormatProvider_GetFormat_System_Type_) method of that [IFormatProvider](https://docs.microsoft.com/dotnet/core/api/System.IFormatProvider) object. The [GetFormat](https://docs.microsoft.com/dotnet/core/api/System.IFormatProvider#System_IFormatProvider_GetFormat_System_Type_) method is responsible for returning an object that provides the necessary formatting information, as specified by its *formatType* parameter, to the `ToString` method. 

A number of formatting or string conversion methods include a parameter of type [IFormatProvider](https://docs.microsoft.com/dotnet/core/api/System.IFormatProvider), but in many cases the value of the parameter is ignored when the method is called. The following table lists some of the formatting methods that use the parameter and the type of the [Type](https://docs.microsoft.com/dotnet/core/api/System.Type) object that they pass to the [IFormatProvider.GetFormat](https://docs.microsoft.com/dotnet/core/api/System.IFormatProvider#System_IFormatProvider_GetFormat_System_Type_) method. 

Method | Type of *formatType* parameter
------ | ------------------------------
`ToString` method of numeric types | [System.Globalization.NumberFormatInfo](https://docs.microsoft.com/dotnet/core/api/System.Globalization.NumberFormatInfo)
`ToString` method of date and time types | [System.Globalization.DateTimeFormatInfo](https://docs.microsoft.com/dotnet/core/api/System.Globalization.DateTimeFormatInfo)
[String.Format](https://docs.microsoft.com/dotnet/core/api/System.String#System_String_Format_System_IFormatProvider_System_String_System_Object_) | [System.ICustomFormatter](https://docs.microsoft.com/dotnet/core/api/System.ICustomFormatter)
[StringBuilder.AppendFormat](https://docs.microsoft.com/dotnet/core/api/System.Text.StringBuilder#System_Text_StringBuilder_AppendFormat_System_IFormatProvider_System_String_System_Object_) | [System.ICustomFormatter](https://docs.microsoft.com/dotnet/core/api/System.ICustomFormatter)

.NET Core provides three classes that implement [IFormatProvider](https://docs.microsoft.com/dotnet/core/api/System.IFormatProvider): 

* [DateTimeFormatInfo](https://docs.microsoft.com/dotnet/core/api/System.Globalization.DateTimeFormatInfo), a class that provides formatting information for date and time values for a specific culture. Its [IFormatProvider.GetFormat](https://docs.microsoft.com/dotnet/core/api/System.IFormatProvider#System_IFormatProvider_GetFormat_System_Type_) implementation returns an instance of itself.

* [NumberFormatInfo](https://docs.microsoft.com/dotnet/core/api/System.Globalization.NumberFormatInfo), a class that provides numeric formatting information for a specific culture. Its [IFormatProvider.GetFormat](https://docs.microsoft.com/dotnet/core/api/System.IFormatProvider#System_IFormatProvider_GetFormat_System_Type_) implementation returns an instance of itself.

* [CultureInfo](https://docs.microsoft.com/dotnet/core/api/System.Globalization.CultureInfo). Its [IFormatProvider.GetFormat](https://docs.microsoft.com/dotnet/core/api/System.IFormatProvider#System_IFormatProvider_GetFormat_System_Type_) implementation can return either a [NumberFormatInfo](https://docs.microsoft.com/dotnet/core/api/System.Globalization.NumberFormatInfo) object to provide numeric formatting information or a [DateTimeFormatInfo](https://docs.microsoft.com/dotnet/core/api/System.Globalization.DateTimeFormatInfo) object to provide formatting information for date and time values. 

You can also implement your own format provider to replace any one of these classes. However, your implementation’s `GetFormat` method must return an object of the type listed in the previous table if it has to provide formatting information to the `ToString` method.

### Culture-Sensitive Formatting of Numeric Values

By default, the formatting of numeric values is culture-sensitive. If you do not specify a culture when you call a formatting method, the formatting conventions of the current thread culture are used. This is illustrated in the following example, which changes the current thread culture four times and then calls the [Decimal.ToString(String)](https://docs.microsoft.com/dotnet/core/api/System.Decimal#System_Decimal_ToString) method. In each case, the result string reflects the formatting conventions of the current culture. This is because the `ToString` and `ToString(String)` methods wrap calls to each numeric type's `ToString(String, IFormatProvider)` method. 

```csharp
using System;
using System.Globalization;
using System.Threading;

public class Example
{
   public static void Main()
   {
      string[] cultureNames = { "en-US", "fr-FR", "es-MX", "de-DE" };
      Decimal value = 1043.17m;

      foreach (var cultureName in cultureNames) {
         // Change the current thread culture.
         Thread.CurrentThread.CurrentCulture = CultureInfo.CreateSpecificCulture(cultureName);
         Console.WriteLine("The current culture is {0}", 
                           Thread.CurrentThread.CurrentCulture.Name);
         Console.WriteLine(value.ToString("C2"));
         Console.WriteLine();
      }   
   }
}
// The example displays the following output:
//       The current culture is en-US
//       $1,043.17
//       
//       The current culture is fr-FR
//       1 043,17 €
//       
//       The current culture is es-MX
//       $1,043.17
//       
//       The current culture is de-DE
//       1.043,17 €
```

You can also format a numeric value for a specific culture by calling a `ToString` overload that has a *provider* parameter and passing it either of the following: 

* A [CultureInfo](https://docs.microsoft.com/dotnet/core/api/System.Globalization.CultureInfo) object that represents the culture whose formatting conventions are to be used. Its [CultureInfo.GetFormat](https://docs.microsoft.com/dotnet/core/api/System.Globalization.CultureInfo#System_Globalization_CultureInfo_GetFormat_System_Type_) method returns the value of the [CultureInfo.NumberFormat](https://docs.microsoft.com/dotnet/core/api/System.Globalization.CultureInfo#System_Globalization_CultureInfo_NumberFormat) property, which is the [NumberFormatInfo](https://docs.microsoft.com/dotnet/core/api/System.Globalization.NumberFormatInfo) object that provides culture-specific formatting information for numeric values.

* A [NumberFormatInfo](https://docs.microsoft.com/dotnet/core/api/System.Globalization.NumberFormatInfo) object that defines the culture-specific formatting conventions to be used. Its [GetFormat](https://docs.microsoft.com/dotnet/core/api/System.Globalization.NumberFormatInfo#System_Globalization_NumberFormatInfo_GetFormat_System_Type_) method returns an instance of itself.

The following example uses [NumberFormatInfo](https://docs.microsoft.com/dotnet/core/api/System.Globalization.NumberFormatInfo) objects that represent the English (United States) and English (Great Britain) cultures and the French and Russian neutral cultures to format a floating-point number.

```csharp
using System;
using System.Globalization;

public class Example
{
   public static void Main()
   {                                                                                                    
      Double value = 1043.62957;
      string[] cultureNames = { "en-US", "en-GB", "ru", "fr" };

      foreach (var name in cultureNames) {
         NumberFormatInfo nfi = CultureInfo.CreateSpecificCulture(name).NumberFormat;
         Console.WriteLine("{0,-6} {1}", name + ":", value.ToString("N3", nfi));
      }   
   }
}
// The example displays the following output:
//       en-US: 1,043.630
//       en-GB: 1,043.630
//       ru:    1 043,630
//       fr:    1 043,630
```

### Culture-Sensitive Formatting of Date and Time Values

By default, the formatting of date and time values is culture-sensitive. If you do not specify a culture when you call a formatting method, the formatting conventions of the current thread culture are used. This is illustrated in the following example, which changes the current thread culture four times and then calls the [DateTime.ToString(String)](https://docs.microsoft.com/dotnet/core/api/System.DateTime#System_DateTime_ToString_System_String_) method. In each case, the result string reflects the formatting conventions of the current culture. This is because the [DateTime.ToString()](https://docs.microsoft.com/dotnet/core/api/System.DateTime#System_DateTime_ToString), [DateTime.ToString(String)](https://docs.microsoft.com/dotnet/core/api/System.DateTime#System_DateTime_ToString_System_String_), [DateTimeOffset.ToString()](https://docs.microsoft.com/dotnet/core/api/System.DateTimeOffset#System_DateTimeOffset_ToString), and [DateTimeOffset.ToString(String)](https://docs.microsoft.com/dotnet/core/api/System.DateTimeOffset#System_DateTimeOffset_ToString_System_String_) methods wrap calls to the [DateTime.ToString(String, IFormatProvider)](https://docs.microsoft.com/dotnet/core/api/System.DateTime#System_DateTime_ToString_System_String_System_IFormatProvider_) and [DateTimeOffset.ToString(String, IFormatProvider)](https://docs.microsoft.com/dotnet/core/api/System.DateTimeOffset#System_DateTimeOffset_ToString_System_String_System_IFormatProvider_) methods.

```csharp
using System;
using System.Globalization;
using System.Threading;

public class Example
{
   public static void Main()
   {
      string[] cultureNames = { "en-US", "fr-FR", "es-MX", "de-DE" };
      DateTime dateToFormat = new DateTime(2012, 5, 28, 11, 30, 0);

      foreach (var cultureName in cultureNames) {
         // Change the current thread culture.
         Thread.CurrentThread.CurrentCulture = CultureInfo.CreateSpecificCulture(cultureName);
         Console.WriteLine("The current culture is {0}", 
                           Thread.CurrentThread.CurrentCulture.Name);
         Console.WriteLine(dateToFormat.ToString("F"));
         Console.WriteLine();
      }   
   }
}
// The example displays the following output:
//       The current culture is en-US
//       Monday, May 28, 2012 11:30:00 AM
//       
//       The current culture is fr-FR
//       lundi 28 mai 2012 11:30:00
//       
//       The current culture is es-MX
//       lunes, 28 de mayo de 2012 11:30:00 a.m.
//       
//       The current culture is de-DE
//       Montag, 28. Mai 2012 11:30:00
```

You can also format a date and time value for a specific culture by calling a [DateTime.ToString](https://docs.microsoft.com/dotnet/core/api/System.DateTime#System_DateTime_ToString_System_IFormatProvider_) or [DateTimeOffset.ToString](https://docs.microsoft.com/dotnet/core/api/System.DateTimeOffset#System_DateTimeOffset_ToString_System_IFormatProvider_) overload that has a provider parameter and passing it either of the following: 

* A [CultureInfo](https://docs.microsoft.com/dotnet/core/api/System.Globalization.CultureInfo) object that represents the culture whose formatting conventions are to be used. Its [CultureInfo.GetFormat](https://docs.microsoft.com/dotnet/core/api/System.Globalization.CultureInfo#System_Globalization_CultureInfo_GetFormat_System_Type_) method returns the value of the [CultureInfo.NumberFormat](https://docs.microsoft.com/dotnet/core/api/System.Globalization.CultureInfo#System_Globalization_CultureInfo_NumberFormat) property, which is the [DateTimeFormatInfo](https://docs.microsoft.com/dotnet/core/api/System.Globalization.DateTimeFormatInfo) object that provides culture-specific formatting information for numeric values.

* A [DateTimeFormatInfo](https://docs.microsoft.com/dotnet/core/api/System.Globalization.DateTimeFormatInfo) object that defines the culture-specific formatting conventions to be used. Its [GetFormat](https://docs.microsoft.com/dotnet/core/api/System.Globalization.DateTimeFormatInfo#System_Globalization_DateTimeFormatInfo_GetFormat_System_Type_) method returns an instance of itself.

The following example uses [DateTimeFormatInfo](https://docs.microsoft.com/dotnet/core/api/System.Globalization.DateTimeFormatInfo) objects that represent the English (United States) and English (Great Britain) cultures and the French and Russian neutral cultures to format a date. 

```csharp
using System;
using System.Globalization;

public class Example
{
   public static void Main()
   {                                                                                                    
      DateTime dat1 = new DateTime(2012, 5, 28, 11, 30, 0);
      string[] cultureNames = { "en-US", "en-GB", "ru", "fr" };

      foreach (var name in cultureNames) {
         DateTimeFormatInfo dtfi = CultureInfo.CreateSpecificCulture(name).DateTimeFormat;
         Console.WriteLine("{0}: {1}", name, dat1.ToString(dtfi));
      }   
   }
}
// The example displays the following output:
//       en-US: 5/28/2012 11:30:00 AM
//       en-GB: 28/05/2012 11:30:00
//       ru: 28.05.2012 11:30:00
//       fr: 28/05/2012 11:30:00
```

## The IFormattable Interface

Typically, types that overload the `ToString` method with a format string and an [IFormatProvider](https://docs.microsoft.com/dotnet/core/api/System.IFormatProvider) parameter also implement the [IFormattable](https://docs.microsoft.com/dotnet/core/api/System.IFormattable) interface. This interface has a single member, [IFormattable.ToString(String, IFormatProvider)](https://docs.microsoft.com/dotnet/core/api/System.IFormattable#System_IFormattable_ToString_System_String_System_IFormatProvider_), that includes both a format string and a format provider as parameters.

Implementing the [IFormattable](https://docs.microsoft.com/dotnet/core/api/System.IFormattable) interface for your application-defined class offers two advantages: 

* Support for string conversion by the [Convert](https://docs.microsoft.com/dotnet/core/api/System.Convert) class. Calls to the [Convert.ToString(Object)](https://docs.microsoft.com/dotnet/core/api/System.Convert#System_Convert_ToString_System_Object_) and [Convert.ToString(Object, IFormatProvider)](https://docs.microsoft.com/dotnet/core/api/System.Convert#System_Convert_ToString_System_Object_System_IFormatProvider_) methods call your [IFormattable](https://docs.microsoft.com/dotnet/core/api/System.IFormattable) implementation automatically.

* Support for composite formatting. If a format item that includes a format string is used to format your custom type, the Common Language Runtime automatically calls your [IFormattable](https://docs.microsoft.com/dotnet/core/api/System.IFormattable) implementation and passes it the format string. For more information about composite formatting with methods such as `String.Format` or `Console.WriteLine`, see the [Composite Formatting](#Composite-Formatting) section.

The following example defines a `Temperature` class that implements the [IFormattable](https://docs.microsoft.com/dotnet/core/api/System.IFormattable) interface. It supports the "C" or "G" format specifiers to display the temperature in Celsius, the "F" format specifier to display the temperature in Fahrenheit, and the "K" format specifier to display the temperature in Kelvin.

```csharp
using System;
using System.Globalization;

public class Temperature : IFormattable
{
   private decimal m_Temp;

   public Temperature(decimal temperature)
   {
      this.m_Temp = temperature;
   }

   public decimal Celsius
   {
      get { return this.m_Temp; }
   }

   public decimal Kelvin
   {
      get { return this.m_Temp + 273.15m; }   
   }

   public decimal Fahrenheit
   {
      get { return Math.Round((decimal) this.m_Temp * 9 / 5 + 32, 2); }
   }

   public override string ToString()
   {
      return this.ToString("G", null);
   }

   public string ToString(string format)
   {
      return this.ToString(format, null);
   }

   public string ToString(string format, IFormatProvider provider)  
   {
      // Handle null or empty arguments.
      if (String.IsNullOrEmpty(format)) format = "G";
      // Remove any white space and convert to uppercase.
      format = format.Trim().ToUpperInvariant();

      if (provider == null) provider = NumberFormatInfo.CurrentInfo;

      switch (format)
      {
         // Convert temperature to Fahrenheit and return string.
         case "F":
            return this.Fahrenheit.ToString("N2", provider) + "°F";
         // Convert temperature to Kelvin and return string.
         case "K":
            return this.Kelvin.ToString("N2", provider) + "K";
         // Return temperature in Celsius.
         case "C":
         case "G":
            return this.Celsius.ToString("N2", provider) + "°C";
         default:
            throw new FormatException(String.Format("The '{0}' format string is not supported.", format));
      }      
   }
}
```

The following example instantiates a `Temperature` object. It then calls the [ToString](https://docs.microsoft.com/dotnet/core/api/System.Convert#System_Convert_ToString_System_Object_System_IFormatProvider_) method and uses several composite format strings to obtain different string representations of a `Temperature` object. Each of these method calls, in turn, calls the [IFormattable](https://docs.microsoft.com/dotnet/core/api/System.IFormattable) implementation of the `Temperature` class.

```csharp
public class Example
{
   public static void Main()
   {
      Temperature temp1 = new Temperature(22m);
      Console.WriteLine(Convert.ToString(temp1, new CultureInfo("ja-JP")));
      Console.WriteLine("Temperature: {0:K}", temp1);
      Console.WriteLine("Temperature: {0:F}", temp1);
      Console.WriteLine(String.Format(new CultureInfo("fr-FR"), "Temperature: {0:F}", temp1));
   }
}
// The example displays the following output:
//       22.00°C
//       Temperature: 295.15°K
//       Temperature: 71.60°F
//       Temperature: 71,60°F
```

## Composite Formatting

Some methods, such as `String.Format` and `StringBuilder.AppendFormat`, support composite formatting. A composite format string is a kind of template that returns a single string that incorporates the string representation of zero, one, or more objects. Each object is represented in the composite format string by an indexed format item. The index of the format item corresponds to the position of the object that it represents in the method's parameter list. Indexes are zero-based. For example, in the following call to the `String.Format` method, the first format item, `{0:D}`, is replaced by the string representation of `thatDate`; the second format item, `{1}`, is replaced by the string representation of `item1`; and the third format item, `{2:C2}`, is replaced by the string representation of `item1.Value`.

```csharp
result = String.Format("On {0:d}, the inventory of {1} was worth {2:C2}.", 
                       thatDate, item1, item1.Value);
Console.WriteLine(result);                            
// The example displays output like the following if run on a system
// whose current culture is en-US:
//       On 5/1/2009, the inventory of WidgetA was worth $107.44.
```

In addition to replacing a format item with the string representation of its corresponding object, format items also let you control the following: 

* The specific way in which an object is represented as a string, if the object implements the [IFormattable](https://docs.microsoft.com/dotnet/core/api/System.IFormattable) interface and supports format strings. You do this by following the format item's index with a : (colon) followed by a valid format string. The previous example did this by formatting a date value with the "d" (short date pattern) format string (e.g., `{0:d}`) and by formatting a numeric value with the "C2" format string (e.g., `{2:C2}` to represent the number as a currency value with two fractional decimal digits. 

* The width of the field that contains the object's string representation, and the alignment of the string representation in that field. You do this by following the format item's index with a , (comma) followed the field width. The string is right-aligned in the field if the field width is a positive value, and it is left-aligned if the field width is a negative value. The following example left-aligns date values in a 20-character field, and it right-aligns decimal values with one fractional digit in an 11-character field. 

```csharp
DateTime startDate = new DateTime(2015, 8, 28, 6, 0, 0);
decimal[] temps = { 73.452m, 68.98m, 72.6m, 69.24563m,
                   74.1m, 72.156m, 72.228m };
Console.WriteLine("{0,-20} {1,11}\n", "Date", "Temperature");
for (int ctr = 0; ctr < temps.Length; ctr++)
   Console.WriteLine("{0,-20:g} {1,11:N1}", startDate.AddDays(ctr), temps[ctr]);

// The example displays the following output:
//       Date                 Temperature
//
//       8/28/2015 6:00 AM           73.5
//       8/29/2015 6:00 AM           69.0
//       8/30/2015 6:00 AM           72.6
//       8/31/2015 6:00 AM           69.2
//       9/1/2015 6:00 AM            74.1
//       9/2/2015 6:00 AM            72.2
//       9/3/2015 6:00 AM            72.2
```

Note that, if both the alignment string component and the format string component are present, the former precedes the latter (for example, `{0,-20:g}`. 

For more information about composite formatting, see [Composite Formatting](compositeformat.md).

## Custom Formatting with ICustomFormatter

Two composite formatting methods, [String.Format(IFormatProvider, String, Object[])](https://docs.microsoft.com/dotnet/core/api/System.String#System_String_Format_System_IFormatProvider_System_String_System_Object_) and [StringBuilder.AppendFormat(IFormatProvider, String, Object[])](https://docs.microsoft.com/dotnet/core/api/System.Text.StringBuilder#System_Text_StringBuilder_AppendFormat_System_IFormatProvider_System_String_System_Object_), include a format provider parameter that supports custom formatting. When either of these formatting methods is called, it passes a [Type](https://docs.microsoft.com/dotnet/core/api/System.Type) object that represents an [ICustomFormatter](https://docs.microsoft.com/dotnet/core/api/System.ICustomFormatter) interface to the format provider’s `GetFormat` method. The `GetFormat` method is then responsible for returning the [ICustomFormatter](https://docs.microsoft.com/dotnet/core/api/System.ICustomFormatter) implementation that provides custom formatting.

The [ICustomFormatter](https://docs.microsoft.com/dotnet/core/api/System.ICustomFormatter) interface has a single method, [Format(String, Object, IFormatProvider)](https://docs.microsoft.com/dotnet/core/api/System.ICustomFormatter#System_ICustomFormatter_Format_System_String_System_Object_System_IFormatProvider_), that is called automatically by a composite formatting method, once for each format item in a composite format string. The [Format(String, Object, IFormatProvider)](https://docs.microsoft.com/dotnet/core/api/System.ICustomFormatter#System_ICustomFormatter_Format_System_String_System_Object_System_IFormatProvider_) method has three parameters: a format string, which represents the *formatString* argument in a format item, an object to format, and an [IFormatProvider](https://docs.microsoft.com/dotnet/core/api/System.IFormatProvider) object that provides formatting services. Typically, the class that implements [ICustomFormatter](https://docs.microsoft.com/dotnet/core/api/System.ICustomFormatter) also implements [IFormatProvider](https://docs.microsoft.com/dotnet/core/api/System.IFormatProvider), so this last parameter is a reference to the custom formatting class itself. The method returns a custom formatted string representation of the object to be formatted. If the method cannot format the object, it should return a null reference.

The following example provides an [ICustomFormatter](https://docs.microsoft.com/dotnet/core/api/System.ICustomFormatter) implementation named `ByteByByteFormatter` that displays integer values as a sequence of two-digit hexadecimal values followed by a space.

```csharp
public class ByteByByteFormatter : IFormatProvider, ICustomFormatter
{
   public object GetFormat(Type formatType)
   { 
      if (formatType == typeof(ICustomFormatter))
         return this;
      else
         return null;
   }

   public string Format(string format, object arg, 
                          IFormatProvider formatProvider)
   {   
      if (! formatProvider.Equals(this)) return null;

      // Handle only hexadecimal format string.
      if (! format.StartsWith("X")) return null;

      byte[] bytes;
      string output = null;

      // Handle only integral types.
      if (arg is Byte) 
         bytes = BitConverter.GetBytes((Byte) arg);
      else if (arg is Int16)
         bytes = BitConverter.GetBytes((Int16) arg);
      else if (arg is Int32)
         bytes = BitConverter.GetBytes((Int32) arg);
      else if (arg is Int64)   
         bytes = BitConverter.GetBytes((Int64) arg);
      else if (arg is SByte)
         bytes = BitConverter.GetBytes((SByte) arg);
      else if (arg is UInt16)
         bytes = BitConverter.GetBytes((UInt16) arg);
      else if (arg is UInt32)
         bytes = BitConverter.GetBytes((UInt32) arg);
      else if (arg is UInt64)
         bytes = BitConverter.GetBytes((UInt64) arg);
      else
         return null;

      for (int ctr = bytes.Length - 1; ctr >= 0; ctr--)
         output += String.Format("{0:X2} ", bytes[ctr]);   

      return output.Trim();
   }
}
```

The following example uses the `ByteByByteFormatter` class to format integer values. Note that the [ICustomFormatter.Format](https://docs.microsoft.com/dotnet/core/api/System.ICustomFormatter#System_ICustomFormatter_Format_System_String_System_Object_System_IFormatProvider_) method is called more than once in the second [String.Format(IFormatProvider, String, Object[])](https://docs.microsoft.com/dotnet/core/api/System.String#System_String_Format_System_IFormatProvider_System_String_System_Object_) method call, and that the default [NumberFormatInfo](https://docs.microsoft.com/dotnet/core/api/System.Globalization.NumberFormatInfo) provider is used in the third method call because the `.ByteByByteFormatter.Format` method does not recognize the "N0" format string and returns a null reference.

```csharp
public class Example
{
   public static void Main()
   {
      long value = 3210662321; 
      byte value1 = 214;
      byte value2 = 19;

      Console.WriteLine(String.Format(new ByteByByteFormatter(), "{0:X}", value));
      Console.WriteLine(String.Format(new ByteByByteFormatter(), "{0:X} And {1:X} = {2:X} ({2:000})", 
                                      value1, value2, value1 & value2));                                
      Console.WriteLine(String.Format(new ByteByByteFormatter(), "{0,10:N0}", value));
   }
}
// The example displays the following output:
//       00 00 00 00 BF 5E D1 B1
//       00 D6 And 00 13 = 00 12 (018)
//       3,210,662,321
```

## Related Topics

Title | Definition
----- | ----------
[Standard Numeric Format Strings](standardnumeric.md) | Describes standard format strings that create commonly used string representations of numeric values. 
[Custom Numeric Format Strings](customnumeric.md) | Describes custom format strings that create application-specific formats for numeric values.
[Standard Date and Time Format Strings](standarddatetime.md) |  Describes standard format strings that create commonly used string representations of [DateTime](https://docs.microsoft.com/dotnet/core/api/System.DateTime) values.
[Custom Date and Time Format Strings](customdatetime.md) | Describes custom format strings that create application-specific formats for [DateTime](https://docs.microsoft.com/dotnet/core/api/System.DateTime) values.
[Standard TimeSpan Format Strings](standardtimespan.md) | Describes standard format strings that create commonly used string representations of time intervals.
[Custom TimeSpan Format Strings](customtimespan.md) | Describes custom format strings that create application-specific formats for time intervals.
[Enumeration Format Strings](enumerationformat.md) | Describes standard format strings that are used to create string representations of enumeration values.
[Composite Formatting](compositeformat.md) | Describes how to embed one or more formatted values in a string. The string can subsequently be displayed on the console or written to a stream.

## Reference

[System.IFormattable](https://docs.microsoft.com/dotnet/core/api/System.IFormattable)

[System.IFormatProvider](https://docs.microsoft.com/dotnet/core/api/System.IFormatProvider)

[System.ICustomFormatter](https://docs.microsoft.com/dotnet/core/api/System.ICustomFormatter)



