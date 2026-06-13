# Normalization Notes

## Reference

Most of this content is based on:

- <https://www.youtube.com/watch?v=GFQaEYEc8_8>

## 1NF Rules

1. Using row order to convey info is not allowed
2. Mixing data types within a column is not allowed
3. Having a table without a primary key is not allowed
4. Repeating groups are not allowed

Given Table: Player_Inventory

Primary Key: Non-key attributes:
{ Player_ID, Item_Type } Item_Quantity, Player_Rating

| Player_ID | Item_Type    | Item_Quantity | Player_Rating |
| --------- | ------------ | ------------- | ------------- |
| jdog21    | amulets      | 2             | Intermediate  |
| jdog21    | amulets      | 2             | Intermediate  |
| gila19    | rings        | 4             | Beginner      |
| trev73    | shields      | 2             | Advanced      |
| trev73    | arrows       | 2             | Advanced      |
| trev73    | copper coins | 2             | Advanced      |
| trev73    | rings        | 2             | Advanced      |

2NF
When data is gone that should have been there, that is called a "deletion anomaly" ... like the Player_Rating of the Player Inventory Table
When data has a logical inconsistency that is called an "update anomaly". Like a player having both an Advanced Rating and an Intermediate rating in a Player Inventory table that is NOT 2NF

Also, if a player has no inventory then they would not have a player rating of Beginner for the player inventory table. This is called an "insertion anomaly" since the player inventory table is NOT in 2NF.

Non-key attributes: such as Item_Quantity, and Player_Rating.

Definition of 2NF: Each non-key attribute
must depend on the entire primary key

{ Player_ID, Item_Type } -> { Item_Quantity } OK

where -> means
that what is to the right, depends on what is to the
left of the arrow i.e. a functional dependency - each value of the
thing on the left side of the arrow is associated with exactly
one value of the thing on the right side of the arrow.
Each combination of Player_ID and Item_Type is associated with
a specific value of Item_Quantity -

For example: jdog21 / amulets is associated with an Item_Quantity of 2

That works for 2NF

Player_Rating only depends on Player_ID so
{ Player_Rating, Item_Type } -> { Player_Rating } Not OK

That does NOT work for 2NF
Fix - create

Table Player

| Player_ID | Player_Rating |
| --------- | ------------- |
| jdog21    | Intermediate  |
| gila19    | Beginner      |
| trev73    | Advanced      |
| tina42    | Beginner      |

{ Player_ID } -> { Player_Rating } where Player_ID is the primary key

Given Table: Player_Inventory

Primary Key: Non-key attributes:
{ Player_ID, Item_Type } Item_Quantity

| Player_ID | Item_Type    | Item_Quantity |
| --------- | ------------ | ------------- |
| jdog21    | amulets      | 2             |
| jdog21    | amulets      | 2             |
| gila19    | rings        | 4             |
| trev73    | shields      | 2             |
| trev73    | arrows       | 2             |
| trev73    | copper coins | 2             |
| trev73    | rings        | 2             |

{ Player_ID, Item_Type } -> { Item_Quantity }

So both tables are now in 2NF

## 3NF

Add a Player_Skill_Level to table Player

Table Player

| Player_ID | Player_Rating | Player_Skill_Level     |
| --------- | ------------- | ---------------------- |
| jdog21    | Intermediate  | 4                      |
| gila19    | **Beginner**  | **4** <-- Inconsistent |
| trev73    | Advanced      | 8                      |
| tina42    | Beginner      | 1                      |

Where Skill Level is 1 .. 9 where 9 is the highest and 1 the lowest skill level
and
1 .. 3 is a Rating of Beginner
4 .. 6 is a Rating of Intermediate
7 .. 9 is a Rating of Advanced

{ Player_ID } -> { Player_Skill_Level }
{ Player_ID } -> { Player_Skill_Level } -> { Player_Rating } <- this is a transitive dependency

**This violates 3NF because a non-key attribute depends on another non-key attribute.**
Because Player_Rating depends on Player_Skill

Fix: remove Player_Rating from the Player Table:

| Player_ID | Player_Skill_Level |
| --------- | ------------------ |
| jdog21    | 4                  |
| gila19    | 4                  |
| trev73    | 8                  |
| tina42    | 1                  |

Add a new table: Player_Skill_Levels with key of Player_Skill_Level

| Player_Skill_Level | Player_Rating |
| ------------------ | ------------- |
| 1                  | Beginner      |
| 2                  | Beginner      |
| 3                  | Beginner      |
| 4                  | Intermediate  |
| 5                  | Intermediate  |
| 6                  | Intermediate  |
| 7                  | Advanced      |
| 8                  | Advanced      |
| 9                  | Advanced      |

3NF: Every non-key attribute in a table should depend on the key,
the whole key, and nothing but the key
or

### Boyce-Codd Normal Form (BCNF) also _ 3NF _ but stronger

Every attribute in a table should depend on the key,
the whole key, and nothing but the key

Most tables are good at this level, the next two levels are 4NF and 5NF which are more for theoretical purposes than practical purposes. They are about multi-valued dependencies and join dependencies, respectively.

## 4NF

A table is in 4NF if it is in BCNF and has no multi-valued dependencies. A multi-valued dependency occurs when one attribute in a table uniquely determines another attribute, but there are multiple values of the second attribute for each value of the first attribute.

DesignMyBirdhouse.com

| Color | Style |
| ----- | ----- |

Such as Model "Tweety":
Available colors: Yellow, Blue
Available styles: Bungalow, Duplex

Model "Metro"
Available colors: Brown, Grey
Available styles: High-Rise, Modular

Model "Prairie"
Available colors: Brown, Beige
Available styles: Bungalow, Schoolhouse

Primary Key: { Model, Color, Style }
Table: Model_Colors_And_Styles_Available

| Model   | Color  | Style       |
| ------- | ------ | ----------- |
| Tweety  | Yellow | Bungalow    |
| Tweety  | Yellow | Duplex      |
| Tweety  | Blue   | Bungalow    |
| Tweety  | Blue   | Duplex      |
| Metro   | Brown  | High-Rise   |
| Metro   | Brown  | Modular     |
| Metro   | Grey   | High-Rise   |
| Metro   | Grey   | Modular     |
| Prairie | Brown  | Bungalow    |
| Prairie | Brown  | Schoolhouse |
| Prairie | Beige  | Bungalow    |
| Prairie | Beige  | Schoolhouse |
| Prairie | Green  | Schoolhouse |

| Prairie | Green | Bungalow | <-- this is missing from the table, but should be there if the table is in 4NF

{ Model } -> { Color} Can we say this? No
{ Model } ->> { Color} Yes, because there are multiple colors for each model
{ Model } ->> { Style} Yes, because there are multiple styles for each model

4NF: Multi-valued dependencies in a table
must be multi-valued dependencies on a key

Fix: Split the table into two tables:

Model_Colors_Available

| Model   | Color  |
| ------- | ------ |
| Tweety  | Yellow |
| Tweety  | Blue   |
| Metro   | Brown  |
| Metro   | Grey   |
| Prairie | Brown  |
| Prairie | Beige  |

Model_Styles_Available

| Model   | Style       |
| ------- | ----------- |
| Tweety  | Bungalow    |
| Tweety  | Duplex      |
| Metro   | High-Rise   |
| Metro   | Modular     |
| Prairie | Bungalow    |
| Prairie | Schoolhouse |

To expand the range of Prairie model colors, we expand the range of Prairie-Model colors to include Green, we simply add a new row to the Model_Colors_Available table:

Model_Colors_Available

| Model   | Color  |
| ------- | ------ |
| Tweety  | Yellow |
| Tweety  | Blue   |
| Metro   | Brown  |
| Metro   | Grey   |
| Prairie | Brown  |
| Prairie | Beige  |
| Prairie | Green  |

and no anomalies are possible.

## 5NF

A table is in 5NF if it is in 4NF and has no join dependencies. A join dependency occurs when a table can be decomposed into two or more tables that can be joined back together to produce the original table, but there is no loss of information.

Quick way to test 5NF:

1. Ask: Is this table just the result of joining smaller relationship tables?
2. If yes, the big table is not in 5NF.
3. Decompose it into those smaller tables.
4. If each smaller table now has no further non-trivial join dependency, those smaller tables are in 5NF.

In short: the original combined table is often not in 5NF, but the decomposed schema can be in 5NF.

Given 3 brands of Ice Cream:

- Frosty's with flavors: Chocolate, Vanilla, Strawberry, and Mint Chocolate Chip
- Alpine with flavors: Vanilla, and Rum Raisin
- Ice Queen with flavors: Vanilla, Strawberry, and Mint Chocolate Chip
  Each brand offers a different range of flavors.

Jason says he likes vanilla and chocolate. And he only likes the brands Frosty's and Alpine.

Susie says she likes rum raisin, mint chocolate chip, and strawberry. And she only likes the brands Alpine and Ice Queen.

We devise a table:
Preferred_Ice_Cream_Products_By_Person

| Person | Brand     | Flavor              |
| ------ | --------- | ------------------- |
| Jason  | Frosty's  | Vanilla             |
| Jason  | Frosty's  | Chocolate           |
| Jason  | Alpine    | Vanilla             |
| Susie  | Alpine    | Rum Raisin          |
| Susie  | Alpine    | Mint Chocolate Chip |
| Susie  | Ice Queen | Vanilla             |
| Susie  | Ice Queen | Strawberry          |

But time passes, tastes change, and at some point Susie announces that she now likes Frosty's brand too, and we update the table:

but not strawberry or mint chocolate chip. He can get vanilla from all three brands, but only Frosty's has chocolate. So he buys Frosty's vanilla and chocolate.

Preferred_Ice_Cream_Products_By_Person

| Person | Brand     | Flavor              |
| ------ | --------- | ------------------- |
| Jason  | Frosty's  | Vanilla             |
| Jason  | Frosty's  | Chocolate           |
| Jason  | Alpine    | Vanilla             |
| Susie  | Alpine    | Rum Raisin          |
| Susie  | Alpine    | Mint Chocolate Chip |
| Susie  | Ice Queen | Vanilla             |
| Susie  | Ice Queen | Strawberry          |
| Susie  | Frosty's  | Strawberry          |

But what if we forgot to add a row for:
| Susie | Frosty's | Mint Chocolate Chip |
Then we would have an update anomaly, because Susie likes mint chocolate chip, but we forgot to add that row to the table. This is a join dependency, because we can decompose the table into three tables:

Avaliable_Flavors_By_Brand

| Brand     | Flavor              |
| --------- | ------------------- |
| Frosty's  | Vanilla             |
| Frosty's  | Strawberry          |
| Frosty's  | Mint Chocolate Chip |
| Alpine    | Vanilla             |
| Alpine    | Rum Raisin          |
| Ice Queen | Vanilla             |
| Ice Queen | Strawberry          |
| Ice Queen | Mint Chocolate Chip |

Preferred_Flavors_By_Person

| Person | Flavor              |
| ------ | ------------------- |
| Jason  | Vanilla             |
| Jason  | Chocolate           |
| Susie  | Rum Raisin          |
| Susie  | Mint Chocolate Chip |
| Susie  | Strawberry          |

Preferred_Brands_By_Person

| Person | Brand     |
| ------ | --------- |
| Jason  | Frosty's  |
| Jason  | Alpine    |
| Susie  | Alpine    |
| Susie  | Ice Queen |

We can join these three tables together to get the original table, but if we forget to add a row to any of the three tables, we would have an update anomaly. Therefore, the original table is not in 5NF.

SELECT
FROM
WHEREpbrand.Person,
bf.Brand,
pf.Flavor
FROM Preferred_Brands_By_Person AS pbrand
INNER JOIN Preferred_Flavors_By_Person AS pf
ON pbrand.Person = pf.Person
INNER JOIN Avaliable_Flavors_By_Brand AS bf
Available_Flavors_By_Brand AS bf
ON pf.Flavor = bf.Flavor
AND pbrand.Brand = bf.Brand

To sum things up: if we want to ensure that a table that's 4NF is also in 5NF, we need to ask ourselves whether the table can be logically thought of as being the result of joing some other tables together.
If it can't be thought of that way, then it is in 5NF.

5NF: The table (which must in in 4NF) cannot be describable as the logical result of joing some other tabes together. If it can be described as the logical result of joining some other tables together, then it is not in 5NF.
