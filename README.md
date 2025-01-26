
# Table of Contents

1.  [Welcome to Table Madness](#orgf5707c6)
2.  [Org Table Basics](#org3d674d7)
    1.  [Identifying Table Cells](#orgcfa172f)
    2.  [Creating Formulas](#orgf390328)
3.  [Common Tasks](#orgaa8fe98)
    1.  [Auto Incrementing](#org13f1a5a)
    2.  [Sorting By Column](#org9839562)
    3.  [Summing a Column](#org70e240f)
    4.  [Summing a Row](#org752e796)
    5.  [Using Horizontal Separators](#orgf579c47)
4.  [Sums, Averages, and Other Transforms](#orgeb978b2)
    1.  [Averages](#orgf11dbde)
    2.  [Mean, Median, and Standard Deviation](#orgd9fa9be)
5.  [Trickery, Magic, and Other Hacks](#org9a1ac72)



<a id="orgf5707c6"></a>

# Welcome to Table Madness

Understanding Org Tables can be one of the more frustrating parts of advanced Org Mode usage. The concepts seem simple, but applying them to produce useful results can often be quite challenging. The devil, as they say, is in the details. The official documentation is often a bit hard to understand and teasing out complex behavior from the core rules can often leave less experienced users giving up in frustration. To make using tables easier I've created this tutorial, which serves as a huge cheat sheet for the most common operations as well as a reference for more complex formulas.


<a id="org3d674d7"></a>

# Org Table Basics

If you haven't already, check out the [offical Org Mode Tables documentation](https://orgmode.org/worg/org-tutorials/tables.html) to get a good overview of how tables work. 


<a id="orgcfa172f"></a>

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
<td class="org-left">@1 $1</td>
<td class="org-left">@1 $2</td>
<td class="org-left">@1 $3</td>
</tr>


<tr>
<td class="org-left">@2 $1</td>
<td class="org-left">@2 $2</td>
<td class="org-left">@2 $3</td>
</tr>


<tr>
<td class="org-left">@3 $1</td>
<td class="org-left">@3 $2</td>
<td class="org-left">@3 $3</td>
</tr>
</tbody>
</table>


<a id="orgf390328"></a>

## Creating Formulas

Create a formula by adding this to be end of any table `,#+TBLFM: <insert formula here>`

Here is a simple example. The formula should be read as "column 2 equals column 1 multiplied by 3"

    | 1 |  3 |
    | 2 |  6 |
    | 3 |  9 |
    | 4 | 12 |
    | 5 | 15 |
    #+TBLFM: $2=$1*3

This is as simple as an example as you will find. No headers, no complex functions, just a little bit of multiplication. 


<a id="orgaa8fe98"></a>

# Common Tasks

There are quite a few common functions that your average user will frequently need. Numbering a list, summing up a column of numbers, etc. This section provides tables, formulas, and explanations as we walk through how to achieve the desired results.


<a id="org13f1a5a"></a>

## Auto Incrementing

Let's assume that you are keeping track of books you wish to read in a table. You want to record the book's name, whether or not you've read it, and you want a running count of the books in the table. Of course you could manually enter 1,2,3,etc. but that is tedious. A better way is to have a formula to automatically generate the indexes.

    |   | BOOKS             | READ? |
    |   | IT                | yes   |
    |   | The Hobbit        | yes   |
    |   | The Black Company | yes   |
    |   | Salem's Lot       | no    |
    |   | Leaves of grass   | no    |

A naive attempt would be to set the value of the first column equal to number of the row. That produces this output:

    | 1 | BOOKS             | READ? |
    | 2 | IT                | yes   |
    | 3 | The Hobbit        | yes   |
    | 4 | The Black Company | yes   |
    | 5 | Salem's Lot       | no    |
    | 6 | Leaves of grass   | no    |
    #+TBLFM: $1=@#

And while that works, that isn't really want we want. Having the first index start at the title row is not how we want this to operate. Let's add a separator by adding `|--` to the second line and hitting the `tab` key. 

    |   | BOOKS             | READ? |
    |---+-------------------+-------|
    | 2 | IT                | yes   |
    | 3 | The Hobbit        | yes   |
    | 4 | The Black Company | yes   |
    | 5 | Salem's Lot       | no    |
    | 6 | Leaves of grass   | no    |
    #+TBLFM: $1=@#

This is looking better! The formula is smart enough to know that we don't want to include something before the separator. However, it still isn't correct. Even though it didn't show the first value, it still incremented it, which leaves our starting value at 2. We can do better. Modify the formula to subtract one from each value to produce the result we want.

    |   | BOOKS             | READ? |
    |---+-------------------+-------|
    | 1 | IT                | yes   |
    | 2 | The Hobbit        | yes   |
    | 3 | The Black Company | yes   |
    | 4 | Salem's Lot       | no    |
    | 5 | Leaves of grass   | no    |
    #+TBLFM: $1=@#-1


<a id="org9839562"></a>

## Sorting By Column

It is often very useful to sort a table by a column. Take the table we have below. It contains a list of NFL QB stats. 

    | PLAYER         | CMP% | PASSING YARDS | RUSHING YARDS |
    |----------------+------+---------------+---------------|
    | Joe Burrow     | 69.8 |          4641 |           202 |
    | Lamar Jackson  | 67.9 |          3955 |           852 |
    | Josh Allen     | 63.6 |          3731 |           531 |
    | Jayden Daniels | 69.4 |          3530 |           864 |

Let's start with something simple such as sorting the players by name. Navigate to the first column and put your cursor on any one of the four lines containing a player name. If your cursor is on the horizontal line or the column title it won't sort. In this case I'm going to put my cursor on the record with `Lamar Jackson` and then type `C-c ^` to invoke `org-sort`. In the mini buffer you will see this message:

`Sort Table: [a]lphabetic, [n]umeric, [t]ime, [f]unc. A/N/TF means reversed:`

Which gives you the sorting options. Since we want the names alphabetized type `a` and it produces this:

    | PLAYER         | CMP% | PASSING YARDS | RUSHING YARDS |
    |----------------+------+---------------+---------------|
    | Jayden Daniels | 69.4 |          3530 |           864 |
    | Joe Burrow     | 69.8 |          4641 |           202 |
    | Josh Allen     | 63.6 |          3731 |           531 |
    | Lamar Jackson  | 67.9 |          3955 |           852 |

Which is what we wanted! How about if we wanted to sort them by `PASSING YARDS`? Then we do the same thing only we execute `org-sort` from the 3rd column and select `N` to produce this:

    | PLAYER         | CMP% | PASSING YARDS | RUSHING YARDS |
    |----------------+------+---------------+---------------|
    | Joe Burrow     | 69.8 |          4641 |           202 |
    | Lamar Jackson  | 67.9 |          3955 |           852 |
    | Josh Allen     | 63.6 |          3731 |           531 |
    | Jayden Daniels | 69.4 |          3530 |           864 |

The order is reversed because we want the most yards as the first record.


<a id="org70e240f"></a>

## Summing a Column

You have started a side business going to estate sales, purchasing old items cheaply, and then reselling them for a profit. To keep track of your business you have started entering everything in a handy org table. Now you have sold five items and you want to see how your balance sheet looks. So, let's start by doing a vector sum on your costs to see how much you spent. Here is the obvious way to do do this:

    | ITEM   | COST | PRICE |
    |--------+------+-------|
    | Bike   |   50 |   100 |
    | Sword  |   20 |    35 |
    | Drill  |   30 |    60 |
    | Cooler |   10 |    70 |
    | TV     |   50 |    40 |
    |--------+------+-------|
    |        |  160 |       |
    #+TBLFM: @7$2=vsum(@2..@6)

This works as a first attempt, but it isn't ideal. What if we add another item? That presents a problem. If you buy a blender for 25 and sell it for 45, then execute the same formula, you get this:

    | ITEM    | COST | PRICE |
    |---------+------+-------|
    | Bike    |   50 |   100 |
    | Sword   |   20 |    35 |
    | Drill   |   30 |    60 |
    | Cooler  |   10 |    70 |
    | TV      |   50 |    40 |
    | Blender |  160 |    45 |
    |---------+------+-------|
    |         |      |       |
    #+TBLFM: @7$2=vsum(@2..@6)

The formula still wants to write the value into the 7th row, however the 7th row is no longer the last row. Thus the cost value was overwritten. This is not what we want. We need a way to tell the formula to always write to the last line, regardless of how many lines are in the table. What we need is the `@>` operator, that signifies the last row of the table.

    | ITEM    | COST | PRICE |
    |---------+------+-------|
    | Bike    |   50 |   100 |
    | Sword   |   20 |    35 |
    | Drill   |   30 |    60 |
    | Cooler  |   10 |    70 |
    | TV      |   50 |    40 |
    | Blender |   25 |    45 |
    | Boots   |   10 |    20 |
    |---------+------+-------|
    |         |  160 |       |
    #+TBLFM: @>$2=vsum(@2..@6)

The good news is that nothing got overwritten and our sum got correctly added to the last line. The bad news is that our new values aren't being added. The output is dynamic, but the input is locked in at the first four values with `@2..@6` which doesn't work as our table expands. We need the input to be dynamic too. To achieve that we can change the lower bound to be `@-1` so that the correct values are used. Now we have a much more usable formula for calculating costs.

What we need now is a formula for summing up the `PRICE` column. We could just create another formula line, increment a column value, and then execute both lines, but there is a cleaner solution. Let's tell org mode to sum up all the columns!

    | ITEM    | COST | PRICE |
    |---------+------+-------|
    | Bike    |   50 |   100 |
    | Sword   |   20 |    35 |
    | Drill   |   30 |    60 |
    | Cooler  |   10 |    70 |
    | TV      |   50 |    40 |
    | Blender |   25 |    45 |
    | Boots   |   20 |    20 |
    |---------+------+-------|
    |         |  205 |       |
    #+TBLFM: @>$2=vsum(@2..@-1)

By using the `@>` operator we can tell org mode to reference the entire last row. Let's see how this works.

    | ITEM                                                 | COST | PRICE |
    |------------------------------------------------------+------+-------|
    | Bike                                                 |   50 |   100 |
    | Sword                                                |   20 |    35 |
    | Drill                                                |   30 |    60 |
    | Cooler                                               |   10 |    70 |
    | TV                                                   |   50 |    40 |
    | Blender                                              |   25 |    45 |
    | Boots                                                |   20 |    20 |
    |------------------------------------------------------+------+-------|
    | Bike + Sword + Drill + Cooler + TV + Blender + Boots |  205 |   370 |
    #+TBLFM: @>=vsum(@2..@-1)

Hmmmm. This is not exactly what we want. Unintended consequences are the hallmark of org tables, thankfully this is an easy fix. By restricting the output to the range `$2..$3` we get a much nicer table! Perfect, right?

    | ITEM    | COST | PRICE |
    |---------+------+-------|
    | Bike    |   50 |   100 |
    | Sword   |   20 |    35 |
    | Drill   |   30 |    60 |
    | Cooler  |   10 |    70 |
    | TV      |   50 |    40 |
    | Blender |   25 |    45 |
    | Boots   |   20 |    20 |
    |---------+------+-------|
    |         |  205 |   370 |
    #+TBLFM: @>$2..$3=vsum(@2..@-1)

Everything should be great unless we need to add another column or two.

    | ITEM    | COST | PRICE | SHIPPING | MILEAGE |
    |---------+------+-------+----------+--------|
    | Bike    |   50 |   100 |        0 |     23 |
    | Sword   |   20 |    35 |       10 |     12 |
    | Drill   |   30 |    60 |        5 |     51 |
    | Cooler  |   10 |    70 |        0 |     32 |
    | TV      |   50 |    40 |       20 |     19 |
    | Blender |   25 |    45 |        0 |      9 |
    | Boots   |   20 |    20 |        0 |     38 |
    |---------+------+-------+----------+--------|
    |         |  205 |   370 |          |        |
    #+TBLFM: @>$2..$3=vsum(@2..@-1)

Well that's not going to work. Now we need to make the amount of columns dynamic too. We can achieve this by replacing the previous bound on the output of `$3` with `$>`, the "last column" operator. Now it doesn't matter how many columns we add in the future, it will just work.

    | ITEM    | COST | PRICE | SHIPPING | MILEAGE |
    |---------+------+-------+----------+--------|
    | Bike    |   50 |   100 |        0 |     23 |
    | Sword   |   20 |    35 |       10 |     12 |
    | Drill   |   30 |    60 |        5 |     51 |
    | Cooler  |   10 |    70 |        0 |     32 |
    | TV      |   50 |    40 |       20 |     19 |
    | Blender |   25 |    45 |        0 |      9 |
    | Boots   |   20 |    20 |        0 |     38 |
    |---------+------+-------+----------+--------|
    |         |  205 |   370 |       35 |    184 |
    #+TBLFM: @>$2..$>=vsum(@2..@-1)

Doesn't that look much better?


<a id="org752e796"></a>

## Summing a Row

Now that we know how to sum up column data we can move on to rows. Let's use the previous table of NFL QBs and calculate who had the most yards total.

    | PLAYER         | CMP% | PASSING YARDS | RUSHING YARDS | TOTAL YARDS |
    |----------------+------+---------------+---------------+-------------|
    | Joe Burrow     | 69.8 |          4641 |           202 |        4843 |
    | Lamar Jackson  | 67.9 |          3955 |           852 |        4807 |
    | Josh Allen     | 63.6 |          3731 |           531 |        4262 |
    | Jayden Daniels | 69.4 |          3530 |           864 |        4394 |
    #+TBLFM: @2$5=@2$3+@2$4
    #+TBLFM: @3$5=@3$3+@3$4
    #+TBLFM: @4$5=@4$3+@4$4
    #+TBLFM: @5$5=@5$3+@5$4

Perfect! It only took four different formulas, all with nearly identical values, executed four times, to produce what we want. So maybe this isn't the most efficient approach, but it does work. Sort of. Fine, let's try to make it better. Let's start by getting rid of the duplication. First, we need stop manually entering the output bounds. Just designate the 5th column to be the output.

    | PLAYER         | CMP% | PASSING YARDS | RUSHING YARDS | TOTAL YARDS |
    |----------------+------+---------------+---------------+-------------|
    | Joe Burrow     | 69.8 |          4641 |           202 |        4843 |
    | Lamar Jackson  | 67.9 |          3955 |           852 |        4843 |
    | Josh Allen     | 63.6 |          3731 |           531 |        4843 |
    | Jayden Daniels | 69.4 |          3530 |           864 |        4843 |
    #+TBLFM: $5=@2$3+@2$4

That is certainly simpler, but now all the output values are the same. Since we are calculating the output for each row, we can get rid of the row specifiers in the input. Let's try that and see how that looks.

    | PLAYER         | CMP% | PASSING YARDS | RUSHING YARDS | TOTAL YARDS |
    |----------------+------+---------------+---------------+-------------|
    | Joe Burrow     | 69.8 |          4641 |           202 |        4843 |
    | Lamar Jackson  | 67.9 |          3955 |           852 |        4807 |
    | Josh Allen     | 63.6 |          3731 |           531 |        4262 |
    | Jayden Daniels | 69.4 |          3530 |           864 |        4394 |
    #+TBLFM: $5=$3+$4

Much better! Let's add some more columns. 

    |----------------+------+---------------+---------------+-------------+-------------+-------------+-----------|
    | PLAYER         | CMP% | PASSING YARDS | RUSHING YARDS | TOTAL YARDS | PASSING TDs | RUSHING TDs | TOTAL TDs |
    |----------------+------+---------------+---------------+-------------+-------------+-------------+-----------|
    | Joe Burrow     | 69.8 |          4641 |           202 |        4843 |          42 |           2 |           |
    | Lamar Jackson  | 67.9 |          3955 |           852 |        4807 |          39 |           4 |           |
    | Josh Allen     | 63.6 |          3731 |           531 |        4262 |          28 |          12 |           |
    | Jayden Daniels | 69.4 |          3530 |           864 |        4394 |          25 |           6 |           |
    |----------------+------+---------------+---------------+-------------+-------------+-------------+-----------|
    #+TBLFM: $5=$3+$4

Now that we have each QBs passing and rushing TDs, we need to calculate their total TDs.

    |----------------+------+---------------+---------------+-------------+-------------+-------------+-----------|
    | PLAYER         | CMP% | PASSING YARDS | RUSHING YARDS | TOTAL YARDS | PASSING TDs | RUSHING TDs | TOTAL TDs |
    |----------------+------+---------------+---------------+-------------+-------------+-------------+-----------|
    | Joe Burrow     | 69.8 |          4641 |           202 |        4843 |          42 |           2 |        44 |
    | Lamar Jackson  | 67.9 |          3955 |           852 |        4807 |          39 |           4 |        43 |
    | Josh Allen     | 63.6 |          3731 |           531 |        4262 |          28 |          12 |        40 |
    | Jayden Daniels | 69.4 |          3530 |           864 |        4394 |          25 |           6 |        31 |
    |----------------+------+---------------+---------------+-------------+-------------+-------------+-----------|
    #+TBLFM: $5=$3+$4
    #+TBLFM: $8=$6+$7

These two formulas could be written on one line like this:

`,#+TBLFM: $5=$3+$4 :: $8=$6+$7`

However I'm putting each formula on a different line so you can easily see the result of each call.


<a id="orgf579c47"></a>

## Using Horizontal Separators

Horizontal lines can make your table much easier to read but they have a hidden use as well. You can specify all of the values between the first and second lines using `@I..@II`. These are NOT Roman numerals, so you must put `IIIII` for the 5th horizontal line, not `V` which would be the correct Roman numeral. In the example below you have decided to host a party and are trying to tally up the costs of different types of expenses. 

    | ITEM    | COST | 
    |---------+------|
    | rum     |   20 |
    | gin     |   18 |
    | beer    |   50 |
    |---------+------|
    | coke    |   10 |
    | sprite  |    5 |
    |---------+------|
    | chips   |   10 |
    | cookies |   20 |
    | pizza   |   60 |
    |---------+------|
    | plates  |   10 |
    | napkins |    8 |
    | cups    |   12 |
    |---------+------|
    | ALCOHOL |   88 |
    | SODA    |   15 |
    | FOOD    |   90 |
    | MISC    |   30 |
    #+TBLFM: @13$2=vsum(@I..@II)
    #+TBLFM: @14$2=vsum(@II..@III)
    #+TBLFM: @15$2=vsum(@III..@IIII)
    #+TBLFM: @16$2=vsum(@IIII..@IIIII)


<a id="orgeb978b2"></a>

# Sums, Averages, and Other Transforms

In previous sections all of the formulas were mostly addition combined with some built in operators to make referencing cells easier. In this section we are going to start using some of the math functions that come with org mode. 


<a id="orgf11dbde"></a>

## Averages

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


<a id="orgd9fa9be"></a>

## Mean, Median, and Standard Deviation

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="org-right" />

<col  class="org-right" />
</colgroup>
<thead>
<tr>
<th scope="col" class="org-right">INDEX</th>
<th scope="col" class="org-right">VALUE</th>
</tr>
</thead>

<tbody>
<tr>
<td class="org-right">1</td>
<td class="org-right">3</td>
</tr>


<tr>
<td class="org-right">2</td>
<td class="org-right">1</td>
</tr>


<tr>
<td class="org-right">3</td>
<td class="org-right">1</td>
</tr>


<tr>
<td class="org-right">4</td>
<td class="org-right">2</td>
</tr>


<tr>
<td class="org-right">5</td>
<td class="org-right">1</td>
</tr>


<tr>
<td class="org-right">6</td>
<td class="org-right">3</td>
</tr>


<tr>
<td class="org-right">7</td>
<td class="org-right">4</td>
</tr>


<tr>
<td class="org-right">8</td>
<td class="org-right">1</td>
</tr>


<tr>
<td class="org-right">9</td>
<td class="org-right">5</td>
</tr>
</tbody>

<tbody>
<tr>
<td class="org-right">MEAN</td>
<td class="org-right">2.3333333</td>
</tr>


<tr>
<td class="org-right">MEDIAN</td>
<td class="org-right">2</td>
</tr>


<tr>
<td class="org-right">STD DEV</td>
<td class="org-right">1.5</td>
</tr>


<tr>
<td class="org-right">SUM</td>
<td class="org-right">21</td>
</tr>
</tbody>
</table>


<a id="org9a1ac72"></a>

# Trickery, Magic, and Other Hacks

