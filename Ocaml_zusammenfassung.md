# Types

```ocaml
1.  type color = Red | Green | Blue
    let rot = red

2.  type nat = Zero | Succ of nat
    let zwei  = Succ (Succ Zero)

3.  type person = {name: string; age: int}
    let tobi = {name = "tobias"; age = 22}
```

## Variants
```ocaml
type primary_color = Red | Green | Blue
type point = float * float

type shape = 
    | Circle of {center: point; radius: float}
    | Rectangle

let c1 = Circle {center = (0., 0.); radius = 1.}
```
## Exceptions

### Defining Exceptions
Exceptions are Variants. 
In OCaml there is the pre-defined Variant ```type exn``` which allows to add Constructers afterwards. (which is normally not possible with Variants)

By adding Constructors to this (extensible) Variant, exceptions can be definded:
```ocaml
exception OhNo of string;; (*exception OhNo of string*)
```
We just defined the exception Constructor named ```OhNo``` which carries a string.
Now we can construct an exception with our new Constructor:
```ocaml
OhNo "oops";; (* : exn = OhNo "oops"*)
```
As we can see, this is of type ```exn```.

### Raising Exceptions
To raise an exception we, shockingly, use the ```raise``` keyword:
```ocaml
raise (OhNo "oops");; (*Exception: OhNo "oops".*)
```
 If we, for example, divide by zero, the Exception with the Constructor ```Divide_by_zero```, which is predefined in the stdlib will be raised:
```ocaml
1 / 0;; (*Exception: Division_by_zero.*)
```
Because ```raise``` never actually returns anything, we are allowed to use it as a placeholder for any type, without getting a compiler warining:
```ocaml
let x : int = raise (OhNo "damn");; (*Exception: OhNo "damn".*)
```
### Handling Exceptions with try/catch
Since Exceptions are variants we handle them with pattern matches.
```ocaml
let safe_div x y = 
    try (x / y) with
        | Division_by_zero -> 0

safe_div 6 0;; (*: int = 0*)
safe_div 6 3;; (*: int = 2*)
```
There is one key difference to normale patteren matches though:
**We only match if an Exception is raised .** If no exception is raised then the whole ```try``` expression will evalute to the value of the expression we are matching.
This is why the code above returns a valid value for ```6 2``` and is syntatic sugar for:
```ocaml
let safe_div x y = 
    let n = try (x / y) with
        | Division_by_zero -> 0
    in n
```

## Options 
Options are, like, Exceptions an extendable Variant of the stdlib:
```ocaml
type 'a t = 'a option = None | Some of 'a
```
As this type comes with stdlib, we can use it of the box with utop:
```ocaml
None;; (*: 'a option = None*)
Some 1;; (*: int option = Some 1*)
```
As options are just variants, we can get the ```int``` out of ```int option``` simply by pattern matching:
```ocaml
match (Some 1) with
| None -> "Some default value"
| Some n -> string_of_int n
(*: string = "1"*)
```



# Pattern Matching
```ocaml
1.  match tobi with
    {name; age} -> name ^ " " ^ string_of_int age;;

2.  match tobi with
    {name = "tobias" as name; age} -> name ^ " " ^ string_of_int age;;

3.  match zwei with
    | Zero -> 0
    | Succ (Succ n) -> 2;;
    | Succ n -> 1;;
```

## When Syntax @TODO

## Destruction
This will cause a compiler warining 
(non exhaustive Pattern Match)
Use with caution:
```ocaml
let x::xs = [1; 2; 3;] in ....

let Succ n = Succ Zero in ....
```

# Funny Operators
### Functions
let f be a functin and x some expression/value:
```ocaml
(*these two are the same*)
f @@ x;; f x;;

(*Implicit Paranthesis:*)
f x y;; (f x) y;;
f @@ x @@ y;; f @@ (x @@ y);;
(*so the @@ operator is right-assoc 
while the normal function call is left-assoc*)
```

```ocaml
x |> f;; (* = f x, reverses the order *)
2 |> (fun x -> x + 5)  |> (fun x -> x);; (*: int = 7*)
```

### User-Defined Operators
```ocaml 
let ( % ) a b = (a + b) * 2;;
 3 % 5;; (*: int = 16*)
```

# Functions
This is a value:
```ocaml
fun a b c -> a + b + c;; (*: int -> int -> int -> int = <fun>*)
```

These two are the same
```ocaml
function 
[] -> 0 
| _::_ -> 1;;

fun x -> 
match x with 
##[] -> 0
| _::_ -> 1
(*: 'a list -> int = <fun>*)
```
Bind function to a value:
```ocaml
let a = function 
[] -> 0 
| _::_ -> 1;;
```


# I/O
## I/O on the Terminal
You can easily read in a String from the command line during the execution of the Progoramm by using the function ```read_line``` and the equivalents for the other data types.
```ocaml 
let n = read_int() in (*User enters "3"*)
  n * 10;; (*: int = 30*)
```
Printing works by simply using f.e. ```print_string "Hallo Mama"```.


## I/O on Files
### Writing to a file
```ocaml
(* Create an outputchannel*)
let channel = open_out "db.txt" in
(*Write to the file*)
Printf.fprintf channel "Name: %s, Nummer: %i" "Peter" 3;
(*close the output Channel*)
close_out channel;;
```
db.txt:
```txt 
Name: Peter, Nummer: 3
```
### Reading from a file
It is possible to read a file line by line as a string by using ```input_line```:

```ocaml
let channel = open_in "db.txt" in
let line1 = input_line channel in 
let line2 = input_line channel in 
line1::line2::[];;
(*: string list = ["Name: Peter, Nummer: 3"; "Name: Max, Nummer: 9"]*)
```








# Modules
**All Module Names Have To Be Upper Case**
## Module Types (Signatures)
Module types are basically **Interfaces**.
They contain a blueprint of
* types 
* values
* functions

that a module must provide. While giving no instructions for the concrete implementation. 
A module type is defined using the ```module type``` keyword and the ```sig``` keyword to open the body.
The following example specifies that a module implementing this type must provide a type ```t``` and a function ```f```:
```ocaml
module type ModType = sig
    type t
    val name : string
    val f : t -> t
end
```
**Bonus:** you can import code with from another module type with the keyword ```include <Signature-name>```:
```ocaml
module type OrderedPrintable = sig
include Printable
val compare : t -> t -> int
end
```

## Modules
Modules are like Classes in Java. They can (but don't have to) implement a module type. 
**Note**: Modules that implement module types cannot not contain additional attributes! (We say the module is *sealed*)
Everything inside a Module is concretly implementent. 
Syntaxwise they begin with the keyword ```module```, and open the body with ```struct```. Any type parameteres from the module type have to be specified in the head **and** body of the module:
```ocaml 
module Mod : ModType with type t = int = struct
    type t = int
    let name = "Aragon"
    let f x = x + 1
end
```
If there was a second type parameter ```k```, we would use the ```and``` keyword like this:
```ocaml
module Mod : ModType with type t = int and type k = string = struct
    type t = int
    type k = string
    let name = "Aragon"
    let f x = x + 1
end
```


We can call functions and values inside a Module via dot-notation:

```ocaml
Mod.f 1 (*: int = 2*)
Mod.name (*: string = "Aragon"*)

```

## Functors
Functors are Functions that take in a module and compute a diffrent module. 
The Input-Module **has to have a module type** specified. Here is a simple example:

```ocaml
module type ModType = sig
    val age : int
end

module Mod : ModType = struct
    let age = 20;;
end

module Funct (Mod : ModType) = struct
    let years_till_death = 80 - Mod.age;;
end

module Mydeath = Funct(Mod);;
Mydeath.years_till_death;; (*: int = 60*)
```

Here is an example with type parameters:
```ocaml
module Funct (Mod: ModType with type t = int) : ModType with type t = M.t = struct
    type t = M.t
    let f x = x + 2
end
```
Note that type parameters have to be se **3 times**.




# Tail recursion
In order to properly test tail recursiveness, you must run tests inside ```OCAMLRUNPARAM=l=9000 utop```

Tail recursive functions:
* ```List.rev```

**Not** tail recursive:
* ```l1 @ l2```
* ```fold_right```

# Lazy Lists
```ocaml 
let lazy_seven = fun () -> 7;;
lazy_seven ();;
(* : int = 7 *)
```

```ocaml
type 'a inf_list = Cons of 'a * (unit -> 'a inf_list)
```