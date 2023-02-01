# Text2Date

> PRE-RELEASE! Do not use in production until version 1.0

The Text2Date function converts time strings to time numbers:

~~~
     R←Y [Z] Text2Date X
~~~

X is a vector of vectors or a simple character matrix, each element or each row a time string.
Y is a formatting pattern. Z is an optional fixed century or sliding century window. 
R a type 60 time number or 0 for an invalid conversion.

For example:

~~~
      'MM-DD-YY' Text2Date '12-01-99'  '02-29-01' '01-01-00'
19991201 0 20000101
~~~

## Time Strings
Dyalog v18 introduced `⎕DT` and defines the terms date-time, which may be represented as a time number, a timestamp, or military time zone character.
Here we add the term 'time string' to the lexicon as yet another way to represent a date-time. A time string is a simple character vector that contains
sufficient information to reasonably convert it to a time number:

~~~
1/1/99
12/31/2023
2:00 PM January 1st, 2022
2024-02-28 23:59:01
~~~

In conjunction with `⎕DT`, Dyalog introduced `1200⌶` "Format Date-time" which can produce a time string given a formatting pattern.
Note that this formatting function can produce all sorts of strings that are **not** time strings:

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
must be ordered and spaced appropriately to line up with the input. All other characters or sequences are ignored. For example:

~~~
      '.....MM-DD-YY' Text2Date 'DOB: 07/24/10' 'DOB: 12/04/02'
20100724 20021204
~~~

A variable width pattern should contain only the following sequences: Y, M, MMM, MMMM, D, h, m, and s. The elements
must be ordered appropriately. Any additional characters
are ignored. There is no need to specify the delimiters or space out the elements. For example:

~~~
      'MDYhm' Text2Date 'Party: 12/31/22 23:59' 'Hangover: 1/1/23 9:03'
20221231.2359 20230101.0903
~~~

A valid formatting pattern (and time string) must contain all elements representing units larger than its smallest element.
For example, if a pattern contains days, it must contain years and months.

## Leading Variable Elements in Undelimited Fixed Width Patterns
Undelimited fixed-width patterns may have a leading variable length element. For example:

~~~
      'MDDYY' Text2Date '43022' '123199'
20220430 19991231
      'YMMDD' Text2Date '21231' '991231'
20021231 19991231
~~~

The sequence MMM is not permitted in an undelimited fixed width pattern with a leading variable element.

## Century Window
The century window specifies how a century is added when only a two digit year is provided.
If the century window is 1000 or greater, it is interpreted as a fixed century, typically specified as 1900 or 2000.
Otherwise, the century window is an integer usually from 0 to 99 (it defaults to 50), indicating the number of years preceeding the current
year that the floating window starts. Thus 50, the default, indicates that dates should be intepreted to be within the last 50 years (and thus next 49 years),
while a value of 99 indicates that dates should be interpreted to be within the last 99 years and the current year,
while a 0 indicates dates should be in the current year and the next 99 years. 

For example:

~~~
      'MDY' Text2Date 'DOB: 7/24/64' 'DOB: 12/4/78' 'DOB: 6/28/02' 
20640724 19781204 20020628
      'MDY' 99 Text2Date 'DOB: 7/24/64' 'DOB: 12/4/78' 'DOB: 6/28/02' 
19640724 19781204 20020628
      'MDY' 1900 Text2Date 'DOB: 7/24/64' 'DOB: 12/4/78' 'DOB: 6/28/02' 
19640724 19781204 19020628
      'MDY' 2000 Text2Date 'DOB: 7/24/64' 'DOB: 12/4/78' 'DOB: 6/28/02' 
20640724 20781204 20020628
~~~

## Distribution

Text2Date is a Dado project containing, as is typical of a project, set of functions in a namespace, which may be included as a dependency in the normal way.
However, the releases also contain a single function **Text2Date.aplf** which may be copy/pasted into a workspace or project.  

## Further Reading
Additional commentary on design and coding decisions can be found at the [Tool Of Thought](https://toolofthought.com) blog.

