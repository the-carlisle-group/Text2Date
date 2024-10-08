# Text2Date

The Text2Date function converts time strings to time numbers:

~~~
     R←Y [Z [T]] Text2Date X
~~~

X is a vector of vectors or a simple character matrix, each element or each row a time string.
Y is one or more formatting patterns. Z is an optional fixed century or sliding century window (default is 50). 
T is the anchor year for the sliding century window (default is `⊃⎕TS`).
R is a type 60 time number or 0 for an invalid conversion.

If Z is not provided, then Y may be unenclosed.

For example:

~~~
      'MM-DD-YY' Text2Date '12-01-99'  '02-29-01' '01-01-00'
19991201 0 20000101
~~~

## Time Strings
Dyalog v18 introduced `⎕DT` and defines the term ***date-time***, which may be represented as a ***time number***, a ***timestamp***,
or ***military time zone character***.
Here we add the term ***time string*** to the lexicon as yet another way to represent a date-time. A time string is a simple character vector that contains
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
Text2Date uses a modified version of the `1200⌶` formatting pattern specification.

Text2Date formally divides time strings and their associated formatting patterns into two categories "fixed" and "variable".
Variable formats must be delimited, fixed formats may or may not be delimited.

A valid formatting pattern (and time string) must contain all elements representing units larger than its smallest element.
For example, if a pattern contains days, it must contain years and months.

A delimited fixed-width  pattern should contain only the following sequences: YY, YYYY, MM, MMM, DD, hh, mm, and ss. The elements
must be ordered and spaced appropriately to line up with the input.
All other characters or sequences are ignored. For example:

~~~
      '.....MM-DD-YY' Text2Date 'DOB: 07/24/10' 'DOB: 12/04/02'
20100724 20021204
~~~

An undelimited fixed-width pattern should contain only the following sequences: YY, YYYY, MM, DD, hh, mm, and ss. 
No other characters or sequences should appear in the pattern. The elements
must be ordered appropriately. The characters in the input matrix do not need to line up in any way. 
In addition, leading zero characters may be elided. For example:

~~~
      'MMDDYY' Text2Date '43022' '123199' '   031724'
20220430 19991231 20240317
~~~

A variable-width pattern should contain only the following sequences: Y, M, MMM, MMMM, D, h, m, and s. The elements
must be ordered appropriately. Any additional characters
are ignored. There is no need to specify the delimiters or space out the elements. For example:

~~~
      'MDYhm' Text2Date 'Party: 12/31/22 23:59' 'Hangover: 1/1/23 9:03'
20221231.2359 20230101.0903
~~~

## Century Window
The century window specifies how a century is added when only a two digit year is provided.
If the century window is 1000 or greater, it is interpreted as a fixed century, typically specified as 1900 or 2000.
Otherwise, the century window is an integer usually from 0 to 99 (it defaults to 50), indicating the number of years preceeding the current (or anchor)
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

## InferFormat

In addition to the `Text2Date` function itself, the API of this project includes the function `InferFormat`:

~~~
      R←Inferformat X
~~~

X is a vector of vectors or a simple character matrix, each element or each row a time string.
R is a namespace containing:

|Name|Value|
|:-------------|:-------------|
|`AllFormats`|a list of format strings that will convert one or more items in X to a valid date, ordered from most successful to least successful| 
|`Absolute`|an integer vector corresponding to `AllFormats` with the absolute count of valid conversions|
|`Relative`|an integer vector corresponding to `AllFormats` with the relative count of valid conversions|
|`Ambiguous`|a boolean scaler indicating that the first two items in AllFormats yield an equal number valid conversions on the same items|
|`Formats`|`AllFormats` where Relative > 0|
|`PrimaryFormat`|the first item of `Formats`|

This function attempts to infer the format strings or strings what will successfully convert
the items in X to a valid date. It only examines formats with year, month, and day elements.
It does not handle hours, minutes and seconds.

## Distribution

Text2Date is a Dado project that may be included as a dependency in the normal way.
However, the releases also contain a stand-alone namespace script **Text2Date.apln**,
which may be copy/pasted or fixed into a project or workspace.  

## Further Reading
Additional commentary on design and coding decisions can be found in [this post](https://toolofthought.com/posts/text-to-date)
at the [Tool of Thought](https://toolofthought.com) blog.

