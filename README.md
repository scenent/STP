# STP : Super-Typed Programming
I would like to introduce my idea to new programming paradigm.  
It's called STP - stands for super-typed programming - and inspired by [Halting Problem](https://en.wikipedia.org/wiki/Halting_problem).  
This documentation just defines the STP standards.

## What's the STP?
STP-based language should provide two sections of object type - inner type and outer type.

### Outer type
Outer types are simple - It's just a normal type which you can define constants or variables with.  
You can think of `int`, `float`, `str` types in Python.  

### Inner type
Inner types are defined as **types of language itself**.  
If you know AST([Abstract Syntax Tree](https://en.wikipedia.org/wiki/Abstract_syntax_tree)), you can think Inner type as a type of each AST node.  
For example, there are (inner) types called `expr` and `stmt`, which contains expression and statement of language itself.  
Furthermore, there's (inner) type that represents (inner or outer) type. It's like `typedef` keyword in C but informations of type are saved in new constant or variable.

As you noticed, inner type and outer type are both usable "type".  
You can use both types like this :

```
var outerTypedVar : int = 10;	    // declare variable using outer type
var innerTypedVar : expr = (1 + 2); // declare variable using inner type
```

Of course, `innerTypedVar` can be evaluated at any time via `expr::evaluate()`.  

```
var value := innerTypedVar.evaluate(); // it should be integer 3.
```

I would like to show you the example of how to use inner types.  
There is four parent inner types, `expr`, `stmt`, `type` and `ast`.  
Expressions and statements like `+`, `-` and `if`, `for`, `while` inherit parent inner types.
To declare inner type variable, initial value should be wrapped with parenthesis.

```
var myExpr : expr = (i + 10);	// it doesn't give error because it acts like templates in C++. it will grab nearest variable called "i" when it's being evaluated.
var myStmt : stmt = (var i = 5;); // of course, variable called "i" is not defined until "myStmt" is being evaluated.

myStmt.evaluate();
print(myExpr.evaluate()); // it will print 15

print(myStmt.to_string()); // it will print the full content of saved statement.
print(declvar(myStmt).get_variable_name()); // it down-casting `myStmt` to `declvar` inner type (which inherits `stmt`), then prints "i".
print(declvar(myStmt).get_initial_expr().to_string()); // it down-casting `myStmt` to `declvar` inner type (which inherits `stmt`), then prints "5".

var myType : type = (int);
myType = myExpr.get_type(); // now myType is `type` type contains `if` type.
var mySuperTypedVar : myType = (2 + 3); // you can use `type` variable to determine variable's type.

var myAST : ast = @parse(myExpr.to_string()); // it parses string data to abstract syntax tree.
print(myAST.to_string()); // it prints "add { .lhs = getvar { .sub = "i" }, .rhs = 10 }".

var myMergedExpr : expr = @merge(myExpr, *, 3); // it merges two expressions into one.
print(myMergedExpr.to_string()) // it will print "i + 10 * 3".
```

Now you should look for the children of `expr` and `stmt` :

```
var myAddExpr : add = (1 + 2);
var myModExpr : mod = (k % 10);
var myEqlExpr : eql = (p == 20);

var myIfStmt : if = (  if (i == 10) { print("foo"); } else { print("bar"); }  );

myIfStmt.set_if_cond(myEqlExpr); // it changes if statement's condition with given `expr` type variable.
myIfStmt.add_elif($(p == 30), $({ print("hi"); })); // you can pass expression or statement literals instead of using temporary variable via dollar sign and parenthesis.

fn myFunction(args){
	for (arg in args) {
		print(arg);
	}
}

// both of it works exactly the same.
myFunction([1, 2, 3]);
var myFnPtr : fn = myFunction;
myFnPtr.call([[1, 2, 3]]);

```

## List of two type sections
| outer type | description |
| ---------- | ----------- |
| int        | 32 bit integer |
| float      | 32 bit floating point |
| double     | 64 bit floating point |
| string     | string |
| list       | linked list |
| map        | hashmap |

| inner type | description |
| ---------- | ----------- |
| type       | contains inner / outer / custom type |
| expr       | represents expression |
| stmt       | represents single statement |
| block      | represents several statements |
| ast        | represents abstract syntax tree |
| add        | represents adding two values |
| sub        | represents subtracting two values |
| mul        | represents multiplying two values |
| div        | represents dividing two values |
| mod        | represents modulating two values |
| shl        | represents a << b |
| shr        | represents a >> b |
| eql        | represents equivalent of two values |
| neql       | represents opposite equivalent of two values |
| grt        | represents a > b |
| les        | represents a < b |
| ge         | represents a >= b |
| le         | represents a <= b |
| mov        | represents a = b |
| land       | represents a && b |
| lor        | represents a \|\| b |
| not        | represents !value |
| and        | represents a & b |
| or         | represents a \| b |
| xor        | represents a ^ b |
| fn         | represents function statement |
| var        | represents variable declaration |
| if         | represents if statement |
| for        | represents for statement |
| while      | represents while statement |
| declkeyword| represents declaring new keyword |


## Conclusion
STP is union of meta programming and programmable programming language.  
It can be simply extended to modern programming theory like `TMP`, `lambda function` and `clojure`.

## About its actual language implementation
'Cause dynamic type which has been defined with `declkeyword` cannot be evaluated in compile time,  
STP paradigm can be implemented in interpreted languages only.
