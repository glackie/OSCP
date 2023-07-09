The **expr** command in Unix evaluates a given expression and displays its corresponding output. It is used for:

- Basic operations like addition, subtraction, multiplication, division, and modulus on integers.
- Evaluating regular expressions, string operations like substring, length of strings etc.

**Syntax:**

$expr expression

**Options:**

- **Option –version :** It is used to show the version information.
    
    **Syntax:**
    
    $expr --version
    
    **Example:**
    
    [![](https://media.geeksforgeeks.org/wp-content/uploads/expr-1.png)](https://media.geeksforgeeks.org/wp-content/uploads/expr-1.png)
    
- **Option –help :** It is used to show the help message and exit.
    
    **Syntax:**
    
    $expr --help
    
    **Example:**
    
    [![](https://media.geeksforgeeks.org/wp-content/uploads/expr-2.png)](https://media.geeksforgeeks.org/wp-content/uploads/expr-2.png)
    

_Below are some examples to demonstrate the use of “expr” command:_

**1. Using expr for basic arithmetic operations :**

**Example:** Addition

$expr 12 + 8 

**Example:** Multiplication

$expr 12 \* 2

**Output**

![](https://media.geeksforgeeks.org/wp-content/uploads/1-979.png)

**Note:**The multiplication operator * must be escaped when used in an arithmetic expression with _expr_.

**2. Performing operations on variables inside a shell script**

**Example:** Adding two numbers in a script

echo "Enter two numbers"

read x 

read y

sum=`expr $x + $y`

echo "Sum = $sum"

**Output:**

![](https://media.geeksforgeeks.org/wp-content/uploads/2-291.png)

**Note:** _expr_ is an external program used by Bourne shell. It uses _expr_ external program with the help of backtick. The _backtick(`)_ is actually called command substitution.

**3. Comparing two expressions**

**Example:**

x=10

y=20

# matching numbers with '='
res=`expr $x = $y`
echo $res

# displays 1 when arg1 is less than arg2
res=`expr $x \< $y`
echo $res

# display 1 when arg1 is not equal to arg2
res=`expr $x \!= $y`
echo $res

**Output:**

![](https://media.geeksforgeeks.org/wp-content/uploads/3-210.png)

**Example:** Evaluating boolean expressions

# OR operation
$expr length  "geekss"  "<"  5  "|"  19  -  6  ">"  10

**Output:**  
![](https://media.geeksforgeeks.org/wp-content/uploads/4-134.png)

# AND operation
$expr length  "geekss"  "<"  5  "&"  19  -  6  ">"  10

**Output:**

![](https://media.geeksforgeeks.org/wp-content/uploads/5-104.png)

**4. For String operations**

**Example:** Finding length of a string

x=geeks

len=`expr length $x`

echo $len

**Output:**

![](https://media.geeksforgeeks.org/wp-content/uploads/6-78.png)

**Example:** Finding substring of a string

x=geeks

sub=`expr substr $x 2 3` 
#extract 3 characters starting from index 2

echo $sub

**Output:**

![](https://media.geeksforgeeks.org/wp-content/uploads/sub.png)

**Example:** Matching number of characters in two strings

$ expr geeks : geek

**Output:**

![](https://media.geeksforgeeks.org/wp-content/uploads/Screenshot-from-2018-12-22-19-37-08-1.png)



$expr index STRING CHARS

index STRING CHARS
              index in STRING where any CHARS is found, or 0

