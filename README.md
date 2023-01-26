# Text2Date
The Text2Date function converts time strings to time numbers:

~~~
     R←Y [Z] Text2Date X
~~~

X is a vector of vectors, each item a time string, or a simple character matrix, each row a time string.
Z is a formatting pattern. Z is an optional fixed century or sliding century window. 
R a type 60 time number or 0 for an invalid conversion.

For example:

~~~
      'MM-DD-YY' Text2Date '12-01-99'  '02-29-01' '01-01-00'
19991201 0 20000101
~~~

## Time Strings
Dyalog v18 introduced `⎕DT` and defines the terms date-time, which may be represented as a time number, a timestamp, or military time zone character.
Here we add the term 'time string' as yet another way to represent a date-time. A time string is a simple character vector that contains sufficient information
to reasonably convert it to a time number. For example:

~~~
1/1/99
12/31/2023
2:00 PM January 1st, 2022
2024-02-28 23:59:01
~~~

In conjunction with `⎕DT`, Dyalog introduced `1200⌶` "Format Date-time" which can produce a time string given a formtting pattern.
Note that this formatting function can produce all sorts of strings that are **not** time strings. For example:

~~~
Jan 1
Tuesday, March 4th
Wed
Hello world!
10 PM
~~~

## Formatting Patterns
Text2Date uses a modified version of the `1200⌶` formatting pattern.

Text2Date formally divides time strings and their associated formatting patterns into two categories "fixed" and "variable".
Variable formats must be delimited, fixed formats may or may not be delimited. (There is one exception to this, noted below)

A fixed width pattern should contain only the following sequences: YY, YYYY, MM, MMM, DD, hh, mm, and ss. The elements
must be ordered and spaced appropriatly to line up with the input. All other characters or sequences are ingorned. For example:

A variable width pattern should contain only the single letters Y M D h m s ordered appropriately. Any additional characters
are ignored. There is no need to specify the delimiters. For example:

A valid formatting pattern (and time string) must contain all elements representing units larger than its smallest element.
For example, if a pattern contains days, it must contain years and months.

## Leading Variable Elements in Undelimited Fixed Width Patterns
Undelimited fixed-width patterns may have a leading variable length element. For example:

## Century Window
