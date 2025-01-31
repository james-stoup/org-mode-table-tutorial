
# Table of Contents

1.  [Welcome to Table Madness](#org5881052)
2.  [Org Table Basics](#org5fa10f5)
    1.  [Identifying Table Cells](#orge021e15)
    2.  [Creating Formulas](#org418c634)
3.  [Common Tasks](#org639eb7b)
    1.  [Auto Incrementing](#orgb9ded0d)
    2.  [Sorting By Column](#orgdadd3bf)
    3.  [Summing a Column](#org36ab4ab)
    4.  [Summing a Row](#org00c7e0a)
    5.  [Using Horizontal Separators](#orge6f0324)
4.  [Sums, Averages, and Other Transforms](#orgc8e84ef)
    1.  [Averages](#org86a8174)
    2.  [Mean, Median, and Standard Deviation](#org9f13cb7)
5.  [Trickery, Magic, and Other Hacks](#orgc057e09)
    1.  [Formatting Numbers](#org9fb31bb)
    2.  [Clearing Cells and Random Values](#org67367c5)
    3.  [Empty Cells](#org44f233c)
6.  [Conclusion](#org01b74da)



<a id="org5881052"></a>

# Welcome to Table Madness

Understanding Org Tables can be one of the more frustrating parts of advanced Org Mode usage. The concepts seem simple, but applying them to produce useful results can often be quite challenging. The devil, as they say, is in the details. The official documentation is often a bit hard to understand and teasing out complex behavior from the core rules can often leave less experienced users giving up in frustration. To make using tables easier I've created this tutorial, which serves as a huge cheat sheet for the most common operations as well as a reference for more complex formulas.


<a id="org5fa10f5"></a>

# Org Table Basics

If you haven't already, check out the [offical Org Mode Tables documentation](https://orgmode.org/worg/org-tutorials/tables.html) to get a good overview of how tables work. 


<a id="orge021e15"></a>

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

Remember this notation, it will be everywhere in this tutorial.


<a id="org418c634"></a>

## Creating Formulas

Create a formula by adding `#+TBLFM: <insert formula here>` to the end of any table.

Here is a simple example. The formula should be read as "column 2 equals column 1 multiplied by 3"

    | 1 |  3 |
    | 2 |  6 |
    | 3 |  9 |
    | 4 | 12 |
    | 5 | 15 |
    #+TBLFM: $2=$1*3

This is as simple as an example as you will find. No headers, no complex functions, just a little bit of multiplication. 


<a id="org639eb7b"></a>

# Common Tasks

There are quite a few common functions that your average user will frequently need. Numbering a list, summing up a column of numbers, etc. This section provides tables, formulas, and explanations as we walk through how to achieve the desired results.


<a id="orgb9ded0d"></a>

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


<a id="orgdadd3bf"></a>

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


<a id="org36ab4ab"></a>

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


<a id="org00c7e0a"></a>

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

`#+TBLFM: $5=$3+$4 :: $8=$6+$7`

However I'm putting each formula on a different line so you can easily see the result of each call.


<a id="orge6f0324"></a>

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


<a id="orgc8e84ef"></a>

# Sums, Averages, and Other Transforms

In previous sections all of the formulas were mostly addition combined with some built in operators to make referencing cells easier. In this section we are going to start using some of the math functions that come with org mode. 


<a id="org86a8174"></a>

## Averages

Here is a simple enough task. The field marked `AVERAGE GRADE` should contain an average of the 3 tests and the final exam grades. The `CLASS GRADE` is computed similarly but the final exam is now weighted in respect to the other grades. To generate the averages go to the first formula and execute `C-c C-c`. To generate the class grade, do the same thing on the second formula.

    | STUDENT | TEST 1 | TEST 2 | TEST 3 | FINAL EXAM | AVERAGE GRADE | CLASS GRADE |
    |---------+--------+--------+--------+------------+---------------+-------------|
    | Alice   |     89 |     93 |     75 |         77 |          83.5 |        82.2 |
    | Bob     |     78 |     99 |     69 |         80 |          81.5 |        81.2 |
    | Cathy   |     91 |     90 |     90 |         75 |          86.5 |        84.2 |
    | Doug    |     48 |     90 |     85 |         82 |         76.25 |        77.4 |
    #+TBLFM: $6=vmean($2..$5)
    #+TBLFM: $7=($2+$3+$4+(2*$5))/5


<a id="org9f13cb7"></a>

## Mean, Median, and Standard Deviation

This example is trickier than the previous ones. Here we are are doing three different things in one formula. First, there are several math functions (such as `vsdev`) making their first appearance in this tutorial. Second, the output lines are being referenced with the `@>` operator. Each additional `>` references one more line above the last line. Thus `@>>>` should be read as "third line from the last line of the table". Finally, all four functions are executed at once because each is separated by a `::` operator. 

    |   INDEX |     VALUE |
    |---------+-----------|
    |       1 |         1 |
    |       2 |         2 |
    |       3 |         4 |
    |       4 |         2 |
    |       5 |         3 |
    |       6 |         1 |
    |       7 |         4 |
    |       8 |         1 |
    |       9 |         5 |
    |---------+-----------|
    |    MEAN | 2.5555556 |
    |  MEDIAN |         2 |
    | STD DEV | 1.5092309 |
    |     SUM |        23 |
    #+TBLFM: @>$2=vsum(@I..@II) :: @>>$2=vsdev(@I..@II) :: @>>>$2=vmedian(@I..@II) :: @>>>>$2=vmean(@I..@II)

There is an entire world of math libraries to discover in the Calc package, but an explanation of that far exceeds the scope of this tutorial.


<a id="orgc057e09"></a>

# Trickery, Magic, and Other Hacks

This is a collection of less used formulas that, while not used as frequently, are still quite helpful in the right circumstances.


<a id="org9fb31bb"></a>

## Formatting Numbers

Let's use this table again only now we are only going to look at the standard deviation.

    |   INDEX |     VALUE |
    |---------+-----------|
    |       1 |         1 |
    |       2 |         2 |
    |       3 |         4 |
    |       4 |         2 |
    |       5 |         3 |
    |       6 |         1 |
    |       7 |         4 |
    |       8 |         1 |
    |       9 |         5 |
    |---------+-----------|
    | STD DEV | 1.5092309 |
    #+TBLFM: @>$2=vsdev(@I..@II)

It would make for a prettier table if the output was but to only three decimal places. Thankfully, we can easily trim it. 

    |   INDEX | VALUE |
    |---------+-------|
    |       1 |     1 |
    |       2 |     2 |
    |       3 |     4 |
    |       4 |     2 |
    |       5 |     3 |
    |       6 |     1 |
    |       7 |     4 |
    |       8 |     1 |
    |       9 |     5 |
    |---------+-------|
    |  MEDIAN |   002 |
    | STD DEV | 1.509 |
    #+TBLFM: @>$2=vsdev(@I..@II);%.3f :: @>>$2=vmedian(@I..@II);%.3d

It works for the output of `MEDIAN` as well. By adding `;%.3d` we get a padded value of `002` instead of a single digit. While not that useful here, in other tables it could be used to make all the values look nice and symmetrical.


<a id="org67367c5"></a>

## Clearing Cells and Random Values

This table has three new formulas that combine for an interesting effect. Let's look at them one at a time.

The first formula, `$1=0`, simply clears everything in the first column by setting each value to 0. This is useful because it allows us to easily reset these values to a base state. By itself this isn't very useful, but becomes invaluable when combined with the next function.

The second formula, `@2$1..@>>$1 = random(1000);%.3d`, creates random numbers. Here we create a random number between 1 and 1,000. Then we ensure that at least 3 digits are displayed with `;%.3d`. This formula generates random numbers that will be used as student IDs. When testing out this formula (or any similar one) you will find that it is helpful to use the previous formula to reset everything to 0 while testing out your code.

The third formula, `'(length'(@I..@II))`, simply counts all the records between the two horizontal lines and returns them. This is a handy way of dealing with large tables that require a count, but don't require a column dedicated to indexes.

    | STUDENT NUMBER | NAME   | TEST 1 | TEST 2 | TEST 3 | HOMEWORK | FINAL EXAM |
    |----------------+--------+--------+--------+--------+----------+------------+
    |            409 | Amy    |     78 |     82 |     91 |       94 |         77 |
    |            016 | Bob    |     77 |     83 |     89 |       90 |         70 |
    |            432 | Clara  |     84 |     88 |     99 |      100 |         80 |
    |            869 | Dylan  |     69 |     74 |     83 |       91 |         65 |
    |            925 | Ed     |     74 |     70 |     77 |       85 |         69 |
    |            723 | Fiona  |     80 |     81 |     86 |       88 |         74 |
    |            688 | Gareth |     79 |     85 |     84 |       89 |         68 |
    |----------------+--------+--------+--------+--------+----------+------------+
    |              7 |        |        |        |        |          |            |
    #+TBLFM: $1=0
    #+TBLFM: @2$1..@>>$1 = random(1000);%.3d
    #+TBLFM: @>$1='(length'(@I..@II))


<a id="org44f233c"></a>

## Empty Cells

Continuing with our theme of a teacher checking grades, look at this table of grades. Some of the students have missing work. We need a breakdown of which students have missing assignments and how many assignments are missing. This requires a bit more magic, but essentially we are looking for empty cells (`""`) and returning lists for each record. Then we count how many empty records were found, and set that number to the value in the `MISSING` column.

    | STUDENT NUMBER | NAME   | TEST 1 | TEST 2 | TEST 3 | HOMEWORK | FINAL EXAM | MISSING |
    |----------------+--------+--------+--------+--------+----------+------------+---------|
    |            409 | Amy    |     78 |     82 |        |       94 |            |       2 |
    |            016 | Bob    |     77 |     83 |     89 |       90 |         70 |       0 |
    |            432 | Clara  |     84 |     88 |     99 |      100 |         80 |       0 |
    |            869 | Dylan  |     69 |        |     83 |       91 |         65 |       1 |
    |            925 | Ed     |     74 |     70 |     77 |       85 |         69 |       0 |
    |            723 | Fiona  |     80 |     81 |     86 |          |         74 |       1 |
    |            688 | Gareth |     79 |     85 |     84 |       89 |         68 |       0 |
    |----------------+--------+--------+--------+--------+----------+------------+---------|
    |              7 |        |        |        |        |          |            |         |
    #+TBLFM: @2$8..@>>$8 = '(length(org-lookup-all "" '($3..$7) nil));E


<a id="org01b74da"></a>

# Conclusion

Congratulations, you are now a master manipulator of org tables. The world is your oyster. Go forth and live your best life, Excel free!

