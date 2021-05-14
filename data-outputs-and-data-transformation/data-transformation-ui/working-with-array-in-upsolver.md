# Work with Arrays

Upsolver supports arrays natively.  The data transformations are defined on fields directly. It is important to understand how arrays are handled for data transformations. Understanding the following concepts will allow you control the output structure precisely in order to achieve your goals.

## Transformations on array fields 

This section explains what happens in Upsolver when performing a calculation on inputs that are arrays. In Upsolver, transformations are defined on the field names and the values of those fields are then passed in to the calculation at runtime.

#### Example 1: calculation without an array

The following simple calculation is defined in Upsolver:

 `data.value1 + data.value2` 

This is the simplest calculation that you may have and it doesn't involve arrays.  This example being used as the starting point.

If the input looked like :

 `{"value1": 1, "value2": 2}` 

 Upsolver will calculate: `1 + 2 = 3`

#### Example 2: calculation with an array

The input data:

 `{"value1": [1,2], "value2": 2}` 

The calculation definition in Upsolver: 

 `data.value1[] + data.value2` 

The calculation will look like this: `[1,2] + 2` 

This will result in Upsolver performing the add operation for each value in the first input with the value 2 from the second input. 

The result of the calculation:

 `[1+2, 2+2] = [3,4]` 

#### Example 2: calculation with two arrays

The input data:

 `{"value1": [1,2], "value2": [20,30]}` 

In this case the following calculation is performed:

`[1,2] + [20,30]`

The two arrays need to be added together. In this case, Upsolver will perform a Cartesian Product on the two arrays and then perform the sum operation on each resulting pair. Since the Cartesian Product is: 

`[1,2] X [20,30] = [(1,20),(1,30),(2,20),(2,30)]`

We will get the following result:

`[1+20, 1+30, 2+20, 2+30] = [21,31,22,32]`

As you can see we ended up with `2 X 2 = 4` elements, and in general Cartesian Product calculations will result in `length(value1[]) * length(value2[])` elements. 

Usually Cartesian Product calculations are undesirable and we will see how to avoid them in the next section.

## Array Context: Define Transformations

We will be using an _Hierarchical Data Output_ to demonstrate the way Upsolver handles transformations. When using Hierarchical Outputs, the objects will be written without changes. This makes it easy for us to understand what's happening internally. Later on we will discuss flattening this data to tabular formats.

#### Example 1 

Assume we have the following input event:

```javascript
{
    "salaries": [{
        "employee": "Jhon",
        "baseRate": 100000,
        "bonus": 23000
    },
    {
        "employee": "Jacob",
        "baseRate": 78000,
        "bonus": 12000
    }
    ]
}
```

In this sample data we have an array of salaries that include the base rate and a bonus value. We are interested in calculating the final amount to be paid for every employee. We want to get an output that looks like this:

```javascript
{
    "toPay": [
    {
        "employee": "Jhon",
        "salary": 123000
    },
    {
        "employee": "Jacob",
        "salary": 100000
    }
    ]
}
```

{% hint style="info" %}
Note: we will be using SQL syntax to quickly demonstrate the transformations, the same applies for transformations defined using the UI Mode.
{% endhint %}

In our Hierarchical Data Output we will define the following select:

```sql
SET salary = data.salaries[].baseRate + data.salaries[].bonus;
SELECT data.salaries[].employee as toPay.employee,
       salary[] as toPay.salary 
    FROM my_data_source
```

If you preview the result of the above query you will get something that is not the desired result:

```sql
{
    "toPay": {
        "employee": [
            "Jhon",
            "Jacob"
        ],
        "salary": [
            123000,
            112000,
            101000,
            90000
        ]
    }
}
```

As you can see we ended up with two independent arrays, one for the employees, and one for the salaries. Moreover the array of salaries has 4 items instead of the expected 2 items.

We can see some clues about what is happening if we look at the query we wrote more carefully. The first thing we may notice is that when we select the `salary` field we are selecting it as an array: `salary[]`. We can also notice this by looking at the calculated field in the field list:

![](../../.gitbook/assets/image%20%28254%29.png)

The reason this calculated field is an array is because it had 2 fields from within an array as inputs, but we chose to **write it outside of the context of the input array.** Meaning we wrote the result to `salaries` in the root of our object. Essentially what we have done is taken our input event and added the calculation like this:

```sql
{
    "salaries": [{
        "employee": "Jhon",
        "baseRate": 100000,
        "bonus": 23000
    },
    {
        "employee": "Jacob",
        "baseRate": 78000,
        "bonus": 12000
    }
    ],
     "salary": salaries[].baseRate + salaries[].bonus
}
```

Since the calculated field is being placed outside of the array it has no local context when performing the calculation. Therefore, what must happen is that Upsolver will take all the values that are available and pass them to the SUM function. So we end up with the result of the following calculation: 

`[100000, 78000] + [23000, 12000]`

Now as we mentioned at the start of this guide this will lead to a the calculation being performed on a pairs of values that are the result of the Cartesian Product: 

`[100000, 78000] X [23000, 12000]`

So we will get: 

`[100000 + 78000, 100000 + 12000, 23000 + 78000, 23000 + 12000] =` 

`[ 123000, 112000, 101000, 90000]`

So we got an array back from our calculation which explains why salary is an array field. Not only that we lost the context of the calculation we don't know which employee belongs to which salary.

#### Solution

Now let's see how to fix this. The solution is really simple all that we need to do is change the context of the calculation. We will solve it with two simple changes that we will show one step at a time. You will understand why both are needed. 

**Step 1: change where we save the calculated field:**

```sql
SET data.salaries[].salary = data.salaries[].baseRate + data.salaries[].bonus;
SELECT data.salaries[].employee as toPay.employee,
       data.salaries[].salary as toPay.salary 
    FROM salaries
```

In this case we changed the target field name from `salary` to  `data.salaries[].salary`  If you preview this you will see we have made some progress, but we haven't completed the calculation.

```sql
{
    "toPay": {
        "employee": [
            "Jhon",
            "Jacob"
        ],
        "salary": [
            123000,
            90000
        ]
    }
}
```

**Step 2: add context to calculated values:**

We have solved part of our problem, we now only have two salaries and they are the correct values! However, we don't know which salary belongs to which employee. This is because we sent our results to the fields `toPay.employee` and `toPay.salary` So we have fields from within a single array `salaries[]` being sent to the fields that are not within the same array record. We can solve this problem by placing the resulting fields within a shared array:

```sql
SET data.salaries[].salary = data.salaries[].baseRate + data.salaries[].bonus;
SELECT data.salaries[].employee as toPay[].employee,
       data.salaries[].salary as toPay[].salary 
    FROM salaries
```

We have changed the result fields that we are writing to to be within a shared array. The result is as expected:

```sql
{
    "toPay": [
        {
            "employee": "Jhon",
            "salary": 123000
        },
        {
            "employee": "Jacob",
            "salary": 90000
        }
    ]
}
```

### Using the ZIP function

In some cases the data can arrive in a slightly different format:

```sql
{
    "employee": ["Jhon", "Jacob"],
    "baseRate": [100000, 78000],
    "bonus": [23000, 12000]
}
```

In this case what we want the first element of each array is to be related to the first element of every other array and so on. 

Unlike the example above we can't achieve the desired calculated salary by placing the calculation in the correct context since there is no shared context array.  We can create the required context by zipping together the arrays using the `ZIP` function:

```sql
SET salaries = ZIP('employee,baseRate,bonus', 
               data.employee[], 
               data.baseRate[], 
               data.bonus[]);
```

The `ZIP` function gets an optional comma separated list of field names. If this the field names aren't provided it will default to `field1, field2,...`with a list of array inputs. The `ZIP` function will stitch the arrays together on an element by element basis: the first element with the first element, second element with the second element and so on.

This will allow access to a calculated field that is in the exact some structure as the first example:  

```sql

{
    "salaries": [
        {
            "employee": "Jhon",
            "baseRate": 100000,
            "bonus": 23000
        },
        {
        	"employee": "Jacob",
            "baseRate": 78000,
            "bonus": 12000
        }
    ]
}
```

You may use the same output query that was used initially to define the output:

```sql
SET salaries = ZIP('employee,baseRate,bonus', data.employee[], data.baseRate[], data.bonus[]);
SELECT 
       salaries[].employee AS salaries[].employee,
       salaries[].baseRate AS salaries[].baseRate,
       salaries[].bonus AS salaries[].bonus
  FROM "salaries"  

```

## Flattening Data

Now that we understand how transformations on arrays work, and how the context and position of the fields matter. Next we will discuss flattening arrays. This is useful when we want to convert nested objects with arrays to flat tables. Outputs that send data to tabular systems will use flattening by default. 

When using a tabular output if you select array fields Upsolver will flatten your data by the array. For example, if we have the following event:

```sql
{
    "values": [1,2,3],
    "name": "Oleg",
    "id": 123
}
```

And the following tabular output defined in Upsolver:

```sql
SELECT data.values[] as value,
       data.name as name,
       data.id as id
   FROM my_data_source
```

We will get the resulting rows:

| value | name | id |
| :--- | :--- | :--- |
| 1 | Oleg | 123 |
| 2 | Oleg | 123 |
| 3 | Oleg | 123 |

The result shows three rows from one source event. This is because the data was flattened based on the `values[]` array that contained three values. 

How do you flatten multiple arrays? 

**Example:**

```sql
{
    "values": ["apple", "NY"],
    "keys": ["fruit", "city"]
}
```

In this case, if we simply SELECT the arrays:

```sql
SELECT data.values[] as value,
       data.keys[] as key
   FROM my_data_source
```

It will result in a Cartesian Product:

| value | key |
| :--- | :--- |
| apple | fruit |
| NY | fruit |
| apple | city |
| NY | city |

If you have independent arrays being selected without a shared context, it will end up with a Cartesian Product in the output. If you wish to avoid this you must use the `ZIP`function to combine the arrays into a single context:

```sql
SET zipped = ZIP('key,value', data.values[], data.keys[]);
SELECT zipped[].value as value
       zipped[].key as key
   FROM my_data_source
```

The result is as expected: 

| value | key |
| :--- | :--- |
| apple | fruit |
| NY | city |

## Try it yourself

Given this input data:

```sql
{
    "events": [ {
        "units" : ["meters","hours"],
        "data": [
            {"index": "a", "values": [ 1, 2]},
            {"index": "b", "values": [ 3, 4]}
        ]
        },
        {
        "units" : ["volts","amps"],
        "data": [
            {"index": "c", "values": [ 5, 6]},
            {"index": "d", "values": [ 7, 8]}
        ]
        }
    ]
}
```

Get the following tabular output:

| unit | value | index |
| :--- | :--- | :--- |
| meters | 1 | a |
| hours | 2 | a |
| meters | 3 | b |
| hours | 4 | b |
| volts | 5 | c |
| amps | 6 | c |
| volts | 7 | d |
| amps | 8 | d |

