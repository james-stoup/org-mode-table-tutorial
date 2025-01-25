
# Table of Contents

1.  [Welcome to Table Madness](#orgcbb3bc5)
2.  [Org Table Basics](#org7b65ee5)
    1.  [Identifying Table Cells](#org5bed56d)
    2.  [Creating Formulas](#org363c602)
3.  [Common Tasks](#orgca018ea)
    1.  [Auto Incrementing](#org6eee0a5)
    2.  [Sorting By Multiple Columns](#org12ef746)
    3.  [Summing a Column](#org230ef92)
    4.  [Summing a Row](#org913dd2c)
4.  [Simple Formulas](#org2f4eec6)
    1.  [Computations on Multiple Cells](#orgdb8ff75)
    2.  [Averaging Values](#orgec3da8b)
5.  [Complex Formulas](#orgad3b70a)
6.  [Trickery, Magic, and Other Hacks](#org62c599b)



<a id="orgcbb3bc5"></a>

# Welcome to Table Madness

Understanding Org Tables can be one of the more frustrating parts of advanced Org Mode usage. The concepts seem simple, but applying them to produce useful results can often be quite challenging. The official documentation is often a bit hard to understand and teasing out complex behavior from the core rules can often leave less experienced users giving up in frustration. To make using tables easier I've created this tutorial, which serves as a huge cheat sheet for the most common operations as well as a reference for more complex formulas.


<a id="org7b65ee5"></a>

# Org Table Basics

If you haven't already, check out the [offical Org Mode Tables documentation](https://orgmode.org/worg/org-tutorials/tables.html) to get a good overview of how tables work. 


<a id="org5bed56d"></a>

## Identifying Table Cells

Org mode uses the `@` symbol to denote rows and the `$` symbol to denote columns. As you see here:

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="org-left" />

<col  class="org-left" />

<col  class="org-left" />
</colgroup>
<tbody>
<tr>
<td class="org-left">@1$1</td>
<td class="org-left">@1$2</td>
<td class="org-left">@1$3</td>
</tr>


<tr>
<td class="org-left">@2$1</td>
<td class="org-left">@2$2</td>
<td class="org-left">@2$3</td>
</tr>


<tr>
<td class="org-left">@3$1</td>
<td class="org-left">@3$2</td>
<td class="org-left">@3$3</td>
</tr>
</tbody>
</table>


<a id="org363c602"></a>

## Creating Formulas

Create a formula by adding this to be end of any table `#+TBLFM: <insert formula here>`

Here is a simple example. The formula should be read as "column 2 equals column 1 multiplied by 3"

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="org-right" />

<col  class="org-right" />
</colgroup>
<tbody>
<tr>
<td class="org-right">1</td>
<td class="org-right">3</td>
</tr>


<tr>
<td class="org-right">2</td>
<td class="org-right">6</td>
</tr>


<tr>
<td class="org-right">3</td>
<td class="org-right">9</td>
</tr>


<tr>
<td class="org-right">4</td>
<td class="org-right">12</td>
</tr>


<tr>
<td class="org-right">5</td>
<td class="org-right">15</td>
</tr>
</tbody>
</table>


<a id="orgca018ea"></a>

# Common Tasks


<a id="org6eee0a5"></a>

## Auto Incrementing

Let's assume that you are keeping track of books you wish to read in a table. You want to record the book's name, whether or not you've read it, and you want a running count of the books in the table. Of course you could manually enter 1,2,3,etc. but that is tedious. A better way is to have a formula to automatically generate the indexes.

A naive attempt would be to set the value of the first column equal to number of the row. That produces this output:

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="org-right" />

<col  class="org-left" />

<col  class="org-left" />
</colgroup>
<tbody>
<tr>
<td class="org-right">1</td>
<td class="org-left">BOOKS</td>
<td class="org-left">READ?</td>
</tr>


<tr>
<td class="org-right">2</td>
<td class="org-left">IT</td>
<td class="org-left">yes</td>
</tr>


<tr>
<td class="org-right">3</td>
<td class="org-left">The Hobbit</td>
<td class="org-left">yes</td>
</tr>


<tr>
<td class="org-right">4</td>
<td class="org-left">The Black Company</td>
<td class="org-left">yes</td>
</tr>


<tr>
<td class="org-right">5</td>
<td class="org-left">Salem's Lot</td>
<td class="org-left">no</td>
</tr>


<tr>
<td class="org-right">6</td>
<td class="org-left">Leaves of grass</td>
<td class="org-left">no</td>
</tr>
</tbody>
</table>

And while that works, that isn't really want we want. Having the first index start at the title row is not how we want this to operate. Let's add a separator by adding `|--` to the second line and hitting the `tab` key. 

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="org-right" />

<col  class="org-left" />

<col  class="org-left" />
</colgroup>
<thead>
<tr>
<th scope="col" class="org-right">&#xa0;</th>
<th scope="col" class="org-left">BOOKS</th>
<th scope="col" class="org-left">READ?</th>
</tr>
</thead>

<tbody>
<tr>
<td class="org-right">2</td>
<td class="org-left">IT</td>
<td class="org-left">yes</td>
</tr>


<tr>
<td class="org-right">3</td>
<td class="org-left">The Hobbit</td>
<td class="org-left">yes</td>
</tr>


<tr>
<td class="org-right">4</td>
<td class="org-left">The Black Company</td>
<td class="org-left">yes</td>
</tr>


<tr>
<td class="org-right">5</td>
<td class="org-left">Salem's Lot</td>
<td class="org-left">no</td>
</tr>


<tr>
<td class="org-right">6</td>
<td class="org-left">Leaves of grass</td>
<td class="org-left">no</td>
</tr>
</tbody>
</table>

This is looking better! The formula is smart enough to know that we don't want to to include something before the separator. However, it still isn't correct. Even though it didn't show the first value, it still incremented it, which leaves our starting value at 2. We can do better. Modify the formula to subtract one from each value to produce the result we want.

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="org-right" />

<col  class="org-left" />

<col  class="org-left" />
</colgroup>
<thead>
<tr>
<th scope="col" class="org-right">&#xa0;</th>
<th scope="col" class="org-left">BOOKS</th>
<th scope="col" class="org-left">READ?</th>
</tr>
</thead>

<tbody>
<tr>
<td class="org-right">1</td>
<td class="org-left">IT</td>
<td class="org-left">yes</td>
</tr>


<tr>
<td class="org-right">2</td>
<td class="org-left">The Hobbit</td>
<td class="org-left">yes</td>
</tr>


<tr>
<td class="org-right">3</td>
<td class="org-left">The Black Company</td>
<td class="org-left">yes</td>
</tr>


<tr>
<td class="org-right">4</td>
<td class="org-left">Salem's Lot</td>
<td class="org-left">no</td>
</tr>


<tr>
<td class="org-right">5</td>
<td class="org-left">Leaves of grass</td>
<td class="org-left">no</td>
</tr>
</tbody>
</table>


<a id="org12ef746"></a>

## Sorting By Multiple Columns


<a id="org230ef92"></a>

## Summing a Column


<a id="org913dd2c"></a>

## Summing a Row


<a id="org2f4eec6"></a>

# Simple Formulas


<a id="orgdb8ff75"></a>

## Computations on Multiple Cells

Here is a simple enough task. The field marked `AVERAGE GRADE` should contain an average of the 3 tests and the final exam grades. The `CLASS GRADE` is computed similarly but the final exam is now weighted in respect to the other grades. To generate the averages go to the first formula and execute `C-c C-c`. To generate the class grade, do the same thing on the second formula.

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="org-left" />

<col  class="org-right" />

<col  class="org-right" />

<col  class="org-right" />

<col  class="org-right" />

<col  class="org-right" />

<col  class="org-right" />
</colgroup>
<thead>
<tr>
<th scope="col" class="org-left">STUDENT</th>
<th scope="col" class="org-right">TEST 1</th>
<th scope="col" class="org-right">TEST 2</th>
<th scope="col" class="org-right">TEST 3</th>
<th scope="col" class="org-right">FINAL EXAM</th>
<th scope="col" class="org-right">AVERAGE GRADE</th>
<th scope="col" class="org-right">CLASS GRADE</th>
</tr>
</thead>

<tbody>
<tr>
<td class="org-left">Alice</td>
<td class="org-right">89</td>
<td class="org-right">93</td>
<td class="org-right">75</td>
<td class="org-right">77</td>
<td class="org-right">83.5</td>
<td class="org-right">82.2</td>
</tr>


<tr>
<td class="org-left">Bob</td>
<td class="org-right">78</td>
<td class="org-right">99</td>
<td class="org-right">69</td>
<td class="org-right">80</td>
<td class="org-right">81.5</td>
<td class="org-right">81.2</td>
</tr>


<tr>
<td class="org-left">Cathy</td>
<td class="org-right">91</td>
<td class="org-right">90</td>
<td class="org-right">90</td>
<td class="org-right">75</td>
<td class="org-right">86.5</td>
<td class="org-right">84.2</td>
</tr>


<tr>
<td class="org-left">Doug</td>
<td class="org-right">48</td>
<td class="org-right">90</td>
<td class="org-right">85</td>
<td class="org-right">82</td>
<td class="org-right">76.25</td>
<td class="org-right">77.4</td>
</tr>
</tbody>
</table>


<a id="orgec3da8b"></a>

## Averaging Values


<a id="orgad3b70a"></a>

# Complex Formulas


<a id="org62c599b"></a>

# Trickery, Magic, and Other Hacks

