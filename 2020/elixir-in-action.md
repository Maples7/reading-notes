# Elixir in Action

## 1.1 About Erlang

Erlang has recently gained more attention. It powers various large systems and has been doing so for more than two decades, such as the WhatsApp messaging application, the Riak distributed database, the Heroku cloud, the Chef deployment auto-mation system, the RabbitMQ message queue, financial systems, and multiplayer backends. It’s truly a proven technology, both in time and scale.

## 1.1.2 Erlang concurrency

To make systems work 24/7 without any downtime, you have to tackle some technical challenges:

Fault-tolerance—A system has to keep working when something unforeseen happens. Unexpected errors occur, bugs creep in, components occasionally fail, network connections drop, or the entire machine where the system is running crashes. Whatever happens, you want to localize the impact of an error as much as possible, recover from the error, and keep the system running and providing service.

Scalability—A system should be able to handle any possible load. Of course, you don’t buy tons of hardware just in case the entire planet’s population might start using your system some day. But you should be able to respond to a load increase by adding more hardware resources without any software intervention. Ideally, this should be possible without a system restart.

Distribution—To make a system that never stops, you have to run it on multiple machines. This promotes the overall stability of the system: if a machine is taken down, another one can take over. Furthermore, this gives you the means to scale horizontally — you can address load increase by adding more machines to the system, thus adding work units to support the higher demand.

Responsiveness—It goes without saying that a system should always be reasonably fast and responsive. Request handling shouldn’t be drastically prolonged, even if the load increases or unexpected errors happen. In particular, occasional lengthy tasks shouldn’t block the rest of the system or have a significant effect on performance.

Live update—In some cases, you may want to push a new version of your software without restarting any servers. For example, in a telephone system, you don’t want to disconnect established calls while you upgrade the software.

## 1.1.3 Server-side systems

I’ve mentioned that Erlang takes the execution of multiple processes into its own hands by employing dedicated schedulers that interchangeably execute many Erlang processes. A scheduler is preemptive — it gives a small execution window to each process and then pauses it and runs another process. Because the execution window is small, a single long-running process can’t block the rest of the system. Furthermore, I/O operations are internally delegated to separate threads, or a kernel-poll service of the underlying OS is used if available. This means any process that waits for an I/O operation to finish won’t block the execution of other processes.

## 1.1.4 The development platform

Erlang is more than a programming language. It’s a full-blown development platform consisting of four distinct parts: the language, the virtual machine, the framework, and the tools.

Erlang, the language, is the primary way of writing code that runs in the Erlang virtual machine. It’s a simple, functional language with basic concurrency primitives.

Source code written in Erlang is compiled into bytecode that’s then executed in the BEAM. This is where the true magic happens. The virtual machine parallelizes your concurrent Erlang programs and takes care of process isolation, distribution, and the overall responsiveness of the system.

The standard part of the release is a framework called Open Telecom Platform (OTP). Despite its somewhat unfortunate name, the framework has nothing to do with telecom systems. It’s a general-purpose framework that abstracts away many typical Erlang tasks:

- Concurrency and distribution patterns
- Error detection and recovery in concurrent systems
- Packaging code into libraries
- Systems deployment
- Live code updates

All these things can be done without OTP, but that makes no sense. OTP is battle-tested in many production systems and is such an integral part of Erlang that it’s hard to draw a line between the two. Even the official distribution is called Erlang/OTP.

The tools are used for various typical tasks such as compiling Erlang code, starting a BEAM instance, creating deployable releases, running the interactive shell, connecting to the running BEAM instance, and so on. Both BEAM and its accompanying tools are cross-platform. You can run them on most mainstream operating systems, such as Unix, Linux, and Windows.

## 1.2.1 Code simplification

The problem with Erlang is that this boilerplate is almost impossible to remove, even if it’s identical in most places (which in my experience is the case). The language provides almost no support for eliminating this noise. In all fairness, there is a way to reduce the boilerplate using a construct called parse transform, but it’s clumsy and complicated to use.

## 2.1 The interactive shell

Everything in Elixir is an expression that has a return value. This includes not only function calls but also constructs like `if` and `case`.

## 2.2 Working with variables

The quickest way to leave the shell is to press Ctrl-C twice. Doing so brutally kills the OS process and all background jobs that are executing. Because the shell is mostly used for experimenting and shouldn’t be used to run real production systems, it’s usually fine to terminate it this way. But if you want a more polite way of stopping the system, you can invoke `System.halt`.

Elixir is a dynamic programming language, which means you don’t explicitly declare a variable or its type. Instead, the variable type is determined by whatever data it contains at the moment. In Elixir terms, assignment is called binding. When you initialize a variable with a value, the variable is bound to that value.

In Elixir, a variable name always starts with a lowercase alphabetic character or an underscore. After that, any combination of alphanumerics and underscores is allowed. The prevalent convention is to use only lowercase ASCII letters, digits, and underscores: `valid_variable_name`, `also_valid_1`, `validButNotRecommended`, `NotValid`.

Rebinding doesn’t mutate the existing memory location. It reserves new memory and reassigns the symbolic name to the new location. You should always keep in mind that data is immutable. Once a memory location is occupied with data, it can’t be modified until it’s released. But variables can be rebound, which makes them point to a different memory location. Thus, variables are mutable, but the data they point to is immutable.

## 2.3.1 Modules

Every Elixir function must be defined inside a module.

There are two ways you can use this module. You can copy/paste this definition directly into `iex` — as mentioned, almost anything can be typed into the shell. Another way is to tell iex to interpret the file while starting:

```sh
> $ iex geometry.ex
```

Regardless of which method you choose, the effect is the same. The code is compiled, and the resulting module is loaded into the runtime and can be used from the shell session.

## 2.3.2 Functions

In the source code, a module must be defined in a single file. A single file may contain multiple module definitions:

```elixir
defmodule Module1 do
 ...
end

defmodule Module2 do
 ...
end
```

A module name must follow certain rules. It starts with an uppercase letter and is usually written in CamelCase style. A module name can consist of alphanumerics, underscores, and the dot (.) character. The latter is often used to organize modules hierarchically:

```elixir
defmodule Geometry.Rectangle do
 ...
end

defmodule Geometry.Circle do
 ...
end
```

The dot character is a convenience. Once the code is compiled, there are no special hierarchical relations between modules, nor are there services to query the hierarchy. It’s just syntactic sugar that can help you scope your names.

You can also nest modules:

```elixir
defmodule Geometry do
  defmodule Rectangle do
    ...
  end
  ...
end
```

The child module can be referenced with `Geometry.Rectangle`. Again, this nesting is a convenience. After the compilation, there’s no special relation between the modules `Geometry` and `Geometry.Rectangle`.

A function must always be a part of a module. Function names follow the same conventions as variables: they start with a lowercase letter or underscore character, followed by a combination of alphanumerics and underscores.

Notice that `defmodule` and `def` aren’t referred to as keywords. That’s because they’re not! Instead, these are compilation constructs called `macros`.

## 2.3.3 Function arity

Always keep in mind that default values generate multiple functions of the same name with different arities.

Because arity distinguishes multiple functions of the same name, it’s not possible to have a function accept a variable number of arguments. There’s no counterpart of C’s … or JavaScript’s `arguments`.

## 2.3.5 Imports and aliases

Calling functions from another module can sometimes be cumbersome because you need to reference the module name. If your module often calls functions from another module, you can import that other module into your own. Importing a module allows you to call its public functions without prefixing them with the module name.

In fact, the standard library’s `Kernel` module is automatically imported into every module. `Kernel` contains functions that are often used, so automatic importing makes their use easier.

## 2.3.6 Module attributes

The purpose of module attributes is twofold: they can be used as compile-time constants, and you can register any attribute, which can then be queried in runtime.

The important thing about the `@pi` constant is that it exists only during the compilation of the module, when the references to it are inlined. Moreover, an attribute can be _registered_, which means it will be stored in the generated binary and can be accessed at runtime. Elixir registers some module attributes by default. For example, the attributes `@moduledoc` and `@doc` can be used to provide documentation for modules and functions.

Save this code to the circle.ex file somewhere, and then run `elixirc circle.ex`. This will generate the file Elixir.Circle.beam. Next, start the `iex` shell from the same folder.

You can now retrieve the attribute at runtime:

```sh
iex(1)> Code.get_docs(Circle, :moduledoc)
{1, "Implements basic circle functions"}
```

More interesting is that other tools from the Elixir ecosystem know how to work with these attributes. For example, you can use the help feature of `iex` to see the module’s documentation:

```sh
iex(2)> h Circle
Circle
Implements basic circle functions

iex(3)> h Circle.area
def area(r)
Computes the area of a circle
```

The underlying point is that registered attributes can be used to attach meta information to a module, which can then be used by other Elixir (and even Erlang) tools. There are many other preregistered attributes, and you can also register your own custom attributes. Take a look at the documentation for the `Module` module (https://hexdocs.pm/elixir/Module.html) for more details.

## 2.4.1 Numbers

At its core, Elixir uses the Erlang type system.

The division operator `/` works differently than you might expect. It always returns a float value:

```sh
iex(6)> 4/2
2.0

iex(7)> 3/2
1.5
```

To perform integer division or to calculate the remainder, you can use auto-imported `Kernel` functions:

```sh
iex(8)> div(5,2)
2

iex(9)> rem(5,2)
1
```

As added syntactic sugar, you can use the underscore character as a visual delimiter:

```sh
iex(10)> 1_000_000
1000000
```

There’s no upper limit on an integer’s size, and you can use arbitrarily large numbers:

```sh
iex(11)> 999999999999999999999999999999999999999999999999999999999999
999999999999999999999999999999999999999999999999999999999999
```

If you’re worried about memory size, it’s best to consult the official Erlang memory guide at http://erlang.org/doc/efficiency_guide/advanced.html. An integer takes up as much space as needed to accommodate the number, whereas a float occupies either 32 or 64 bits, depending on the build architecture of the virtual machine. Floats are internally represented in IEEE 754-1985 (binary precision) format.

## 2.4.2 Atoms

Atoms are literal named constants. They’re similar to symbols in Ruby or enumera-tions in C/C++. Atom constants start with a colon character, followed by a combination of alphanumerics and/or underscore characters:

```sh
:an_atom
:another_atom
```

It’s possible to use spaces in the atom name with the following syntax:

```sh
:"an atom with spaces"
```

An atom consists of two parts: the _text_ and the _value_. The atom text is whatever you put after the colon character. At runtime, this text is kept in the _atom table_. The value is the data that goes into the variable, and it's merely a reference to the atom table.

This is exactly why atoms are best used for named constants. They’re efficient both memory- and performance-wise. When you say `variable = :some_atom` the variable doesn’t contain the entire text, but only a reference to the atom table. Therefore, memory consumption is low, the comparisons are fast, and the code is still readable.

There’s another syntax for atom constants. You can omit the beginning colon and start with an uppercase character: `AnAtom`. This is called an alias, and at compile time it’s transformed into this `:"Elixir.AnAtom"`:

```sh
iex(1)> AnAtom == :"Elixir.AnAtom"
true
```

When you use an alias, the compiler implicitly adds the `Elixir.` prefix to its text and inserts an atom there. But if an alias already contains that prefix, it’s not added. Consequently, the following also works:

```sh
iex(2)> AnAtom == Elixir.AnAtom
true
```

You may recall from earlier that you can also use aliases to give alternate names to modules:

```sh
iex(3)> alias IO, as: MyIO
iex(4)> MyIO.puts("Hello!")
Hello!
```

It’s no accident that the term `alias` is used for both things. When you write `alias IO, as: MyIO`, you instruct the compiler to transform `MyIO` into `IO`. Resolving this further, the final result emitted in the generated binary is `:Elixir.IO`. Therefore, with an alias set up, the following also holds:

```sh
iex(5)> MyIO == Elixir.IO
true
```

All of this may seem strange, but it has an important underlying purpose. Aliases sup-port the proper resolution of modules.

It may come as a surprise that Elixir doesn’t have a dedicated Boolean type. Instead, the atoms `:true` and `:false` are used. As syntactic sugar, Elixir allows you to reference these atoms without the starting colon character:

```sh
iex(1)> :true == true
true
iex(2)> :false == false
true
```

The term _Boolean_ is still used in Elixir to denote an atom that has a value of either `:true`
or `:false`. Always keep in mind that a Boolean is just an atom that has a value of `true` or `false`.

## 2.4.3 Tuples

To extract an element from the tuple, you can use the `Kernel.elem/2` function, which accepts a tuple and the zero-based index of the element. Recall that the `Kernel` module is auto-imported, so you can call `elem` instead of `Kernel.elem`:

```sh
iex(2)> age = elem(person, 1)
25
```

To modify an element of the tuple, you can use the `Kernel.put_elem/3` function, which accepts a tuple, a zero-based index, and the new value of the field in the given position:

```sh
iex(3)> put_elem(person, 1, 26)
{"Bob", 26}
```

The function `put_elem` doesn’t modify the tuple. It returns the new version, keeping the old one intact. Recall that data in Elixir is immutable, so you can’t do an in-memory modification of a value. You can verify that the previous call to `put_elem` didn’t change the `person` variable:

```sh
iex(4)> person
{"Bob", 25}
```

So how can you use the `put_elem` function, then? You need to store its result to another variable:

```sh
iex(5)> older_person = put_elem(person, 1, 26)
{"Bob", 26}

iex(6)> older_person
{"Bob", 26}
```

Recall that variables can be rebound, so you can also do the following:

```sh
iex(7)> person = put_elem(person, 1, 26)
{"Bob", 26}
```

By doing this, you’ve effectively rebound the `person` variable to the new memory location. The old location isn’t referenced by any other variable, so it’s eligible for garbage collection.

You may wonder if this approach is memory efficient. In most cases, there will be little data copying, and the two variables will share as much memory as possible. This will be explained later in this section, when we discuss immutability.

Tuples are most appropriate for grouping a small, fixed number of elements together. When you need a dynamically sized collection, you can use lists.

## 2.4.4 Lists

To get an element of a list, you can use the `Enum.at/2` function:

```sh
iex(3)> Enum.at(prime_numbers, 3)
7
```

`Enum.at` is again an O(n) operation: it iterates from the beginning of the list to the desired element. Lists are never a good fit when direct access is called for. For those purposes, tuples, maps, or a higher-level data structure is appropriate.

You can check whether a list contains a particular element with the help of the `in` operator:

```sh
iex(4)> 5 in prime_numbers
true

iex(5)> 4 in prime_numbers
false
```

To manipulate lists, you can use functions from the `List` module. For example, `List.replace_at/3`
modifies the element at a certain position:

```sh
iex(6)> List.replace_at(prime_numbers, 0, 11)
[11, 3, 5, 7]
```

As was the case with tuples, the modifier doesn’t mutate the variable, but returns the modified version of it, which you need to store to another variable:

```sh
iex(7)> new_primes = List.replace_at(prime_numbers, 0, 11)
[11, 3, 5, 7]
```

Or you can rebind to the same one:

```sh
iex(8)> prime_numbers = List.replace_at(prime_numbers, 0, 11)
[11, 3, 5, 7]
```

You can insert a new element at the specified position with the `List.insert_at` function:

```sh
iex(9)> List.insert_at(prime_numbers, 3, 13)
[11, 3, 5, 13, 7]
```

To append to the end, you can use a negative value for the insert position:

```sh
iex(10)> List.insert_at(prime_numbers, -1, 13)
[11, 3, 5, 7, 13]
```

Like most list operations, modifying an arbitrary element has a complexity of O(n). In particular, appending to the end is expensive because it always takes _n_ steps, _n_ being the length of the list.

In addition, the dedicated operator `++` is available. It concatenates two lists:

```sh
iex(11)> [1, 2, 3] ++ [4, 5]
[1, 2, 3, 4, 5]
```

Again, the complexity is O(n), _n_ being the length of the left list (the one you’re appending to). In general, you should avoid adding elements to the end of a list. Lists are most efficient when new elements are pushed to the top, or popped from it. To understand why, let’s look at the recursive nature of lists.

An alternative way of looking at lists is to think of them as recursive structures. A list can be represented by a pair (_head_, _tail_), where _head_ is the first element of the list and _tail_ “points” to the (_head_, _tail_) pair of the remaining elements. If you’re familiar with Lisp, then you know this concept as _cons cells_.

In Elixir, there’s a special syntax to support recursive list definition: `a_list = [head | tail]`. `head`
can be any type of data, whereas `tail` is itself a list. If `tail` is an empty list, it indicates the end of the entire list.

Let’s look at some examples:

```sh
iex(1)> [1 | []]
[1]

iex(2)> [1 | [2 | []]]
[1, 2]

iex(3)> [1 | [2]]
[1, 2]

iex(4)> [1 | [2, 3, 4]]
[1, 2, 3, 4]
```

This is just another syntactical way of defining lists, but it illustrates what a list is. It’s a pair with two values: a head and a tail, the tail being itself a list.

The following snippet is a canonical recursive definition of a list:

```sh
iex(1)> [1 | [2 | [3 | [4 | []]]]]
[1, 2, 3, 4]
```

Of course, nobody wants to write constructs like this one. But it’s important that you’re always aware that, internally, lists are recursive structures of (_head_, _tail_) pairs.

To get the head of the list, you can use the `hd` function. The tail can be obtained by calling the `tl` function:

```sh
iex(1)> hd([1, 2, 3, 4])
1

iex(2)> tl([1, 2, 3, 4])
[2, 3, 4]
```

Both operations are O(1), because they amount to reading one or the other value from the (_head_, _tail_) pair.

For the sake of completeness, it should be mentioned that the tail doesn’t need to be a list. It can be any type. When the tail isn’t a list, it’s said that the list is improper, and most of the standard list manipulations won’t work. Improper lists have some special uses, but we won’t deal with them in this book.

Once you know the recursive nature of the list, it’s simple and efficient to push a new element to the top of the list:

```sh
iex(1)> a_list = [5, :value, true]
[5, :value, true]

iex(2)> new_list = [:new_element | a_list]
[:new_element, 5, :value, true]
```

Construction of the `new_list` is an O(1) operation, and no memory copying occurs — the tail of the `new_list` _is_ the `a_list`.

## 2.4.5 Immutability

As has been mentioned before, Elixir data can’t be mutated. Every function returns the new, modified version of the input data. You have to take the new version into another variable or rebind it to the same symbolic name. In any case, the result resides in another memory location. The modification of the input will result in some data copying, but generally, most of the memory will be shared between the old and the new version.

Let’s start with tuples. A modified tuple is always a complete, shallow copy of the old version. Consider the following code:

```elixir
a_tuple = {a, b, c}
new_tuple = put_elem(a_tuple, 1, b2)
```

The variable `new_tuple` will contain a shallow copy of `a_tuple`, differing only in the second element. Both tuples reference variables `a` and `c`, and whatever is in those variables is shared (and not duplicated) between both tuples. `new_tuple` is a shallow copy of the original `a_tuple`.

What happens if you rebind a variable? In this case, after rebinding, the variable `a_tuple`
references another memory location. The old location of `a_tuple` isn’t accessible and is available for garbage collection.

Keep in mind that tuples are always copied, but the copying is shallow.

When you modify the n-th element of a list, the new version will contain shallow copies of the first _n_ – 1 elements, followed by the modified element. After that, the tails are completely shared. This is precisely why adding elements to the end of a list is expensive. To append a new element at the tail, you have to iterate and (shallow) copy the entire list.

Immutability may seem strange, and you may wonder about its purpose. There are two important benefits of immutability: side-effect-free functions and data consistency.

Given that data can’t be mutated, you can treat most functions as side-effect-free transformations. They take an input and return a result. More complicated programs are written by combining simpler transformations:

```elixir
def complex_transformation(data) do
  data
  |> transformation_1(...)
  |> transformation_2(...)
  ...
  |> transformation_n(...)
end
```

This code relies on the previously mentioned pipeline operator that chains two functions together, feeding the result of the previous call as the first argument of the next call.

Side-effect-free functions are easier to analyze, understand, and test. They have well-defined inputs and outputs. When you call a function, you can be sure that no variable will be implicitly changed. Whatever the function does, you must take its result and do something with it.

Elixir isn’t a pure functional language, so functions may still have side effects. For example, a function may write something to a file and issue a database or network call, which causes it to produce a side effect. But you can be certain that a function won’t modify the value of any variable.

The implicit consequence of immutable data is the ability to hold all versions of a data structure in the program. This, in turn, makes it possible to perform atomic in-memory operations.

## 2.4.6 Maps

In addition, a map is also enumerable, which means that all the functions from the `Enum` module can work with maps.

To retrieve a field, you can use the `[]` operator:

```sh
iex(3)> bob[:works_at]
"Initech"

iex(4)> bob[:non_existent_field]
nil
```

Atom keys again receive special syntax treatment. The following snippet fetches a value stored under the `:age` key:

```sh
iex(5)> bob.age
25
```

With this syntax you’ll get an error if you try to fetch a nonexistent field:

```sh
iex(6)> bob.non_existent_field
** (KeyError) key :non_existent_field not found
```

To change a field value, you can use the following syntax:

```sh
iex(7)> next_years_bob = %{bob | age: 26}
%{age: 26, name: "Bob", works_at: "Initech"}
```

This syntax can be used to change multiple attributes as well:

```sh
iex(8)> %{bob | age: 26, works_at: "Initrode"}
%{age: 26, name: "Bob", works_at: "Initrode"}
```

But you can only modify values that already exist in the map. This makes the update syntax a perfect choice for powering maps that represent structures. If you mistype the field name, you’ll get an immediate runtime error:

```sh
iex(9)> %{bob | works_in: "Initech"}
** (KeyError) key :works_in not found
```

Using maps to hold structured data is a frequent pattern in Elixir. The common pattern is to provide all the fields while creating the map, using atoms as keys. If the value for some field isn’t available, you can set it to `nil`. Such a map, then, always has all the fields. Using the update syntax, you can modify the map with the update syntax. Finally, to fetch a desired field, you can use the `a_map.some_field` syntax.

Of course, such data is still a map, so you can also use the functions from the `Map` module, such as `Map.put/3`, or `Map.fetch/2`. But these functions are usually suitable for the cases where maps are used to manage a dynamically sized key/value structure.

## 2.4.7 Binaries and bitstrings

If you provide a byte value bigger than 255, it’s truncated to the byte size:

```sh
iex(2)> <<256>>
<<0>>

iex(3)> <<257>>
<<1>>

iex(4)> <<512>>
<<0>>
```

You can specify the size of each value and thus tell the compiler how many bits to use for that particular value:

```sh
iex(5)> <<257::16>>
<<1, 1>>
```

This expression places the number 257 into 16 bits of consecutive memory space. The output indicates that you use 2 bytes, both having a value of 1. This is due to the binary representation of 257, which in 16-bit form is written 00000001 00000001.

The size specifier is in bits and need not be a multiplier of 8. The following snippet creates a binary by combining two 4-bit values:

```sh
iex(6)> <<1::4, 15::4>>
<<31>>
```

The resulting value has 1 byte and is represented in the output using the normalized form 31 (0001 1111).

If the total size of all the values isn’t a multiple of 8, the binary is called a _bitstring_ — a sequence of bits:

```sh
iex(7)> <<1::1, 0::1, 1::1>>
<<5::size(3)>>
```

You can also concatenate two binaries or bitstrings with the operator <>:

```sh
iex(8)> <<1, 2>> <> <<3, 4>>
<<1, 2, 3, 4>>
```

## 2.4.8 Strings

It may come as a surprise, but Elixir doesn’t have a dedicated string type. Instead, strings are represented by using either a binary or a list type.

Heredocs strings start with a triple double-quote. The ending triple double-quote must be on its own line:

```sh
iex(9)> """
        Heredoc must end on its own line """
        """
"Heredoc must end on its own line \"\"\"\n"
```

The alternative way of representing strings is to use single-quote syntax:

```sh
iex(1)> 'ABC'
'ABC'
```

This creates a _character list_, which is essentially a list of integers in which each element represents a single character.

The previous result is exactly the same as if you manually construct the list of integers:

```sh
iex(2)> [65, 66, 67]
'ABC'
```

Just like with binary strings, there are syntax counterparts for various definitions of character lists:

```sh
iex(3)> 'Interpolation: #{3 + 0.14}'
'Interpolation: 3.14'

iex(4)> ~c(Character list sigil)
'Character list sigil'

iex(5)> ~C(Unescaped sigil #{3 + 0.14})
'Unescaped sigil \#{3 + 0.14}'

iex(6)> '''
        Heredoc
        '''
'Heredoc\n'
```

Character lists aren’t compatible with binary strings. Most of the operations from the `String`
module won’t work with character lists. In general, you should prefer binary strings over character lists. Occasionally, some functions may work only with character lists. This mostly happens with pure Erlang libraries. In this case, you can convert a binary string to a character list version using the `String.to_charlist/1` function:

```sh
iex(7)> String.to_charlist("ABC")
'ABC'
```

To convert a character list to a binary string, you can use `List.to_string/1`.

In general, you should prefer binary strings as much as possible, using character lists only when some third-party library (most often written in pure Erlang) requires it.

## 2.4.9 First-class functions

You may wonder why the dot operator is needed here. The motivation behind the dot operator is to make the code more explicit. When you encounter a `square.(5)` expression in the source code, you know an anonymous function is being invoked. In contrast, the expression `square(5)` is invoking a named function defined somewhere else in the module. Without the dot operator, you’d have to parse the surrounding code to understand whether you’re calling a named or an anonymous function.

The `&` operator, also known as the _capture operator_, takes the full function qualifier — a module name, a function name, and an arity — and turns that function into a lambda that can be assigned to a variable. You can use the capture operator to simplify the call to `Enum.each`:

```sh
iex(6)> Enum.each(
          [1, 2, 3],
          &IO.puts/1
        )
```

A closure always captures a specific memory location. Rebinding a variable doesn’t affect the previously defined lambda that references the same symbolic name:

```sh
iex(1)> outside_var = 5
iex(2)> lambda = fn -> IO.puts(outside_var) end
iex(3)> outside_var = 6
iex(4)> lambda.()
5
```

The preceding code illustrates another important point. Normally, after you have rebound `outside_var` to the value 6, the original memory location would be eligible for garbage collection. But because the `lambda` function captures the original location (the one that holds the number 5), and you’re still referencing that lambda, the original location isn’t available for garbage collection.

## 2.4.10 Other built-in types

A _reference_ is an almost unique piece of information in a BEAM instance. It’s generated by calling `Kernel.make_ref/0` (or `make_ref`). According to the Elixir documentation, a reference will reoccur after approximately 2^82 calls. But if you restart a BEAM instance, reference generation starts from the beginning, so its uniqueness is guaranteed only during the lifetime of the BEAM instance.

The _port identifier_ is important when using ports. It’s a mechanism used in Erlang to talk to the outside world. File I/O and communication with external programs are done through ports.

## 2.4.11 Higher-level types

It’s important to realize that a range(e.g. `1..3`) isn’t a special type. Internally, it’s represented as a map that contains range boundaries. You shouldn’t rely on this knowledge, because the range representation is an implementation detail, but it’s good to be aware that the memory footprint of a range is very small, regardless of the size. A million-number range is still just a small map.

A _keyword_ list is a special case of a list, where each element is a two-element tuple, and the first element of each tuple is an atom. The second element can be of any type. Let’s look at an example:

```sh
iex(1)> days = [{:monday, 1}, {:tuesday, 2}, {:wednesday, 3}]
```

Elixir allows a slightly more elegant syntax for defining a keyword list:

```sh
iex(2)> days = [monday: 1, tuesday: 2, wednesday: 3]
```

Keyword lists are often used for small-size key/value structures, where keys are atoms. Many useful functions are available in the `Keyword` module (https://hexdocs.pm/ elixir/Keyword.html). For example, you can use `Keyword.get/2` to fetch the value for a key:

```sh
iex(3)> Keyword.get(days, :monday)
1

iex(4)> Keyword.get(days, :noday)
nil
```

Just as with maps, you can use the operator `[]` to fetch a value:

```sh
iex(5)> days[:tuesday]
2
```

Don’t let that fool you, though. Because you’re dealing with a list, the complexity of a lookup operation is O(n).

Keyword lists are most often useful for allowing clients to pass an arbitrary number of optional arguments. For example, the result of the function `IO.inspect`, which prints a string representation of a term to the console, can be controlled by providing additional options through a keyword list:

```sh
iex(6)> IO.inspect([100, 200, 300])
[100, 200, 300]
iex(7)> IO.inspect([100, 200, 300], [width: 3])
[100,
 200,
 300]
```

In fact, this pattern is so frequent that Elixir allows you to omit the square brackets if the last argument is a keyword list:

```sh
iex(8)> IO.inspect([100, 200, 300], width: 3, limit: 1)
[100,
 ...]
```

Notice in this example that you’re still sending two arguments to `IO.inspect/2`: a number and a two-element keyword list. But this snippet demonstrates how to simulate optional arguments. You can accept a keyword list as the last argument of your function, and make that argument default to an empty list:

```elixir
def my_fun(arg1, arg2, opts \\ []) do
 ...
end
```

Your clients can then pass options via the last argument. Of course, it’s up to you to check the contents in the `opts` argument and perform some conditional logic depending on what the caller has sent you.

You may wonder if it’s better to use maps instead of keywords for optional arguments. A keyword list can contain multiple values for the same key. In addition, you can control the ordering of keyword list elements — something that isn’t possible with maps. Finally, many functions in standard libraries of Elixir and Erlang take their options as keyword lists. It’s best to stick to the existing convention and accept optional parameters via keyword lists.

A `MapSet` is the implementation of a set — a store of unique values, where a value can be of any type. Let’s look at some examples:

```sh
iex(1)> days = MapSet.new([:monday, :tuesday, :wednesday])
#MapSet<[:monday, :tuesday, :wednesday]>

iex(2)> MapSet.member?(days, :monday)
true

iex(3)> MapSet.member?(days, :noday)
false

iex(4)> days = MapSet.put(days, :thursday)
#MapSet<[:monday, :thursday, :tuesday, :wednesday]>
```

A `MapSet` is also an enumerable, so you can pass it to functions from the `Enum` module:

```sh
iex(5)> Enum.each(days, &IO.puts/1)
monday
thursday
tuesday
wednesday
```

As you can tell from the output, `MapSet` doesn’t preserve the ordering of the items.

The `DateTime` module can be used to work with datetimes in some timezone. Unlike with other types, no sigil is available. Instead, you can create a datetime by using `DateTime` functions:

```sh
iex(4)> datetime = DateTime.from_naive!(naive_datetime, "Etc/UTC")
```

## 2.4.12 IO lists

An IO list is a special sort of list that’s useful for incrementally building output that will be forwarded to an I/O device, such as a network or a file. Each element of an IO list must be one of the following:

- An integer in the range of 0 to 255
- A binary
- An IO list

In other words, an IO list is a deeply nested structure in which leaf elements are plain bytes (or binaries, which are again a sequence of bytes). For example, here’s `"Hello world"` represented as a convoluted IO list:

```sh
iex(1)> iolist = [[['H', 'e'], "llo,"], " worl", "d!"]
```

Notice how you can combine character lists and binary strings into a deeply nested list.

IO lists are useful when you need to incrementally build a stream of bytes. Lists usually aren’t good in this case, because appending to a list is an O(n) operation. In contrast, appending to an IO list is O(1), because you can use nesting. Here’s a demonstration of this technique:

```sh
iex(3)> iolist = []
        iolist = [iolist, "This"]
        iolist = [iolist, " is"]
        iolist = [iolist, " an"]
        iolist = [iolist, " IO list."]

[[[[[], "This"], " is"], " an"], " IO list."]
```

Here, you append to an IO list by creating a new list with two elements: a previous version of the IO list and the suffix that’s appended. Each such operation is O(1), so this is performant. And, of course, you can send this data to an IO function:

```sh
iex(4)> IO.puts(iolist)
This is an IO list.
```

## 2.5 Operators

The unary operator `!` returns false if the value is truthy; otherwise it returns true.

## 2.6 Macros

Macros are arguably one of the most important features Elixir brings to the table, compared to plain Erlang. They make it possible to perform powerful code transformations in compile time, thus reducing boilerplate and providing elegant, mini-DSL constructs.

## 2.7.1 Modules and functions in the runtime

In fact, if multiple modules are defined in a single source file, the compiler will produce multiple .beam files that correspond to those modules. You can try this by calling the Elixir compiler (`elixirc`) from the command line

```sh
>$ elixirc source.ex
```

where the file source.ex defines a couple of modules. Assuming there are no syntax errors, you’ll see multiple .beam files generated on the disk.

```sh
>$ iex my_source.ex
```

This command compiles the source file and then immediately loads all generated modules. Notice that in this case, .beam files aren’t saved to disk. The `iex` tool performs an in-memory compilation.

Similarly, you can define modules in the shell:

```sh
iex(1)> defmodule MyModule do
          def my_fun, do: :ok # In-memory bytecode generation and loading of a module
        end

iex(2)> MyModule.my_fun
:ok
```

Again, the bytecode isn’t saved to the disk in this case.

The important thing to remember from this discussion is that at runtime, module names are atoms.

## 2.7.2 Starting the runtime

It’s important to be aware that input is interpreted, because that means it won’t be as performant as the compiled code. This is generally fine, because you use the shell only to experiment with the language. But you shouldn’t try to measure performance directly from `iex`. On the other hand, modules are always compiled. Even if you define a module in the shell, it will be compiled and loaded in memory, so there will be no performance hit.

The elixir command can be used to run a single Elixir source file. Here’s the basic syntax:

```sh
>$ elixir my_source.ex
```

When you start this, the following actions take place:

1. The BEAM instance is started.
2. The file my_source.ex is compiled in memory, and the resulting modules are loaded to the VM. No .beam file is generated on the disk.
3. Whatever code resides outside of a module is interpreted.
4. Once everything is finished, BEAM is stopped.

This is generally useful for running scripts. In fact, it’s recommended that such a script have an .exs extension, the trailing “s” indicating that it’s a script.

If you don’t want a BEAM instance to terminate, you can provide the `--no-halt` parameter:

```sh
>$ elixir --no-halt script.exs
```

This is most often useful if your main code (outside a module) starts concurrent tasks that perform all the work. In this situation, your main call finishes as soon as the concurrent tasks are started, and BEAM is immediately terminated (and no work is done). Providing the `--no-halt` option keeps the entire system alive and running.

The `mix` tool is used to manage projects that are made up of multiple source files. Whenever you need to build a production-ready system, `mix` is your best option.

```sh
>$ mix compile
Compiling 1 file (.ex)
Generated my_project app
```

The compilation goes through all the files from the lib folder and places the resulting .beam files in the ebin folder.

Elixir is a dynamic language. The type of a variable is determined by the value it holds. Data is immutable — it can’t be modified. A function can return the modified version of the input that resides in another memory location. The modified version shares as much memory as possible with the original data. The most important primitive data types are numbers, atoms, and binaries. There is no Boolean type. Instead, the atoms `true` and `false` are used.There is no nullability. The atom `nil` can be used for this purpose. `Range`, keyword lists, `MapSet`, `Date`, `Time`, `NaiveDateTime`, and `DateTime` are abstractions built on top of the existing built-in types.

## 3.1.7 Matching bitstrings and binaries

Recall that a _bitstring_ is a chunk of bits, and a _binary_ is a special case of a bitstring that's always aligned to the byte size.

The following example takes the binary apart by taking its first byte into one variable and the rest of the binary into another:

```sh
iex(6)> <<b1, rest :: binary>> = binary
<<1, 2, 3>>

iex(7)> b1
1

iex(8)> rest
<<2, 3>>
```

`rest::binary` states that you expect an arbitrary-sized binary. You can even extract separate bits or groups of bits.

## 3.1.8 Compound matches

An important consequence of this is that match expressions can be chained:

```sh
iex(3)> a = (b = 1 + 3)
4
```

In this (not so useful) example, the following things happen:

1. The expression `1 + 3` is evaluated.
2. The result (4) is matched against the pattern `b`.
3. The result of the inner match (which is again 4) is matched against the pattern `a`.

Consequently, both a and b have the value `4`.

Parentheses are optional, and many developers omit them in this case:

```sh
iex(4)> a = b = 1 + 3
4
```

## 3.2.1 Multiclause functions

You can create a function value with the capture operator, `&`: `&Module.fun/arity`. If you capture Geometry.area/1, you capture all of its clauses.

Functions with the same name but different arities are in reality two different functions.

## 3.2.2 Guards

Guards are an extension of the basic pattern-matching mechanism. They allow you to state additional broader expectations that must be satisfied for the entire pattern to match.

If an error is raised from inside the guard, it won’t be propagated, and the guard expression will return `false`. The corresponding clause won’t match, but some other might.

## 3.3.3 The with special form

The `with` special form allows you to use pattern matching to chain multiple expressions, verify that the result of each conforms to the desired pattern, and return the first unexpected result.

In its simplest form, `with` has the following shape:

```elixir
with pattern_1 <- expression_1,
     pattern_2 <- expression_2,
     ...
do
  ...
end
```

You start from the top, evaluating the first expression and matching the result against the corresponding pattern. If the match succeeds, you move to the next expression. If all the expressions are successfully matched, you end up in the `do` block, and the result of the `with` expression is the result of the last expression in the `do` block.

If any match fails, however, `with` will not proceed to evaluate subsequent expressions. Instead, it will immediately return the result that couldn’t be matched.

## 3.4 Loops and iterations

The principal looping tool in Elixir is recursion.

Although recursion is the basic building block of any kind of looping, most production Elixir code uses it sparingly. That’s because there are many higher-level abstractions that hide the recursion details.

You probably know from other languages that a function call will lead to a stack push, and therefore will consume some memory. A very deep recursion might lead to a stack overflow and crash the entire program. This isn’t necessarily a problem in Elixir, because of the tail-call optimization.

## 3.4.2 Tail function calls

Elixir (or, more precisely, Erlang) treats tail calls in a specific manner by performing a _tail-call optimization_. In this case, calling a function doesn’t result in the usual stack push. Instead, something more like a goto or a jump statement happens. You don’t allocate additional stack space before calling the function, which in turn means the tail function call consumes no additional memory.

Because tail recursion doesn’t consume additional memory, it’s an appropriate solution for arbitrarily large iterations. There is a downside, though. Whereas classical (non-tail) recursion has a more declarative feel to it, tail recursion usually looks more procedural.

```elixir
defmodule ListHelper do
  def sum(list) do
    do_sum(0, list)
  end

  defp do_sum(current_sum, []) do
    current_sum
  end

  defp do_sum(current_sum, [head | tail]) do
    new_sum = head + current_sum
    do_sum(new_sum, tail)
  end
end
```

Tail vs. non-tail recursion: Given the properties of tail recursion, you might think it’s always a preferred approach for doing loops. It’s not that simple. Non-tail recursion often looks more elegant and concise, and it can in some circumstances yield better performance. When you write recursion, you should choose the solution that seems like a better fit. If you need to run an infinite loop, tail recursion is the only way that will work. Otherwise, the choice amounts to which looks like a more elegant and performant solution.

Recursion is the basic looping technique, and no loop can be done without it. Still, you won’t need to write explicit recursion all that often. Many typical tasks can be performed by using higher-order functions.

## 3.4.3 Higher-order functions

Under the hood, `Enum.each/2` is powered by recursion: there’s no other way to do loops and iterations in Elixir.

Notice how when capturing the function, you don’t specify the module name. That’s because `add_num/2` resides in the same module, so you can omit the module prefix. In fact, because `add_num/2` is private, you can’t capture it with the module prefix.

## 3.4.4 Comprehensions

The comprehension iterates through each element and runs the `do/end` block. The result is a list that contains all the results returned by the `do/end` block. In this basic form, `for` is no different than `Enum.map/2`.

Comprehensions have various other features that often make them elegant, compared to `Enum`-based iterations. For example, it’s possible to perform nested iterations over multiple collections. The following example takes advantage of this feature to calculate a small multiplication table:

```sh
iex(2)> for x <- [1, 2, 3], y <- [1, 2, 3], do: {x, y, x*y}
[
  {1, 1, 1}, {1, 2, 2}, {1, 3, 3},
  {2, 1, 2}, {2, 2, 4}, {2, 3, 6},
  {3, 1, 3}, {3, 2, 6}, {3, 3, 9}
]
```

In the examples so far, the result of the comprehension has been a list. But comprehensions can return anything that’s collectable. _Collectable_ is an abstract term for a functional data type that can collect values. Some examples include lists, maps, `MapSet`, and file streams; you can even make your own custom type collectable (more on that in chapter 4).

In more general terms, a comprehension iterates through enumerables, calling the provided block for each value and storing the results in some collectable structure.

Another interesting comprehension feature is that you can specify filters. This gives you the possibility to skip some elements from the input.

The comprehension filter is evaluated for each element of the input enumerable, prior to block execution. If the filter returns `true`, the block is called and the result is collected. Otherwise the comprehension moves on to the next element.

As you can see, comprehensions are an interesting feature, allowing you to do some elegant transformations of the input enumerable. Although this can be done with `Enum` functions, most notably `Enum.reduce/3`, often the resulting code looks more elegant when comprehensions are used. This is particularly true when you have to perform a Cartesian product (cross-join) of multiple enumerables, as was the case with the multiplication table.

## 3.4.5 Streams

Streams are a special kind of enumerable that can be useful for doing lazy composable operations over anything enumerable.

Going back to the example of printing employees, using a stream allows you to print employees in a single go. The change to the original code is simple enough. Instead of using `Enum.with_index/1`, you can rely on its lazy equivalent, `Stream.with_index/1`:

```sh
iex(7)> employees
        |> Stream.with_index
        |> Enum.each(
          fn {employee, index} ->
            IO.puts("#{index + 1}. #{employee}")
          end)
1. Alice
2. Bob
3. John
```

The output is the same, but the list iteration is done only once. This becomes increasingly useful when you need to compose multiple transformations of the same list.

Even though you stack multiple transformations, everything is performed in a single pass when you call `Enum.each`. In contrast, if you used `Enum` functions everywhere, you’d have to run multiple iterations over each intermediate list, which would incur a performance and memory-usage penalty. This lazy property of streams can become useful for consuming slow and potentially large enumerable input. A typical case is when you need to parse each line of a file. Relying on eager `Enum` functions means you have to read the entire file into memory and then iterate through each line. In contrast, using streams makes it possible to read and immediately parse one line at a time.

```elixir
def large_lines!(path) do
  File.stream!(path)
  |> Stream.map(&String.replace(&1, "\n", ""))
  |> Enum.filter(&(String.length(&1) > 80))
end
```

Here you rely on the `File.stream!/1` function, which takes the path of a file and returns a stream of its lines. Because the result is a stream, the iteration through the file happens only when you request it. After `File.stream!` returns, no byte from the file has been read yet. Then you remove the trailing newline character from each line, again in the lazy manner. Finally, you eagerly take only long lines, using `Enum.filter/2`. It’s at this point that iteration happens. The consequence is that you never read the entire file in memory; instead, you work on each line individually.

There are no special tricks in the Elixir compiler that allow these lazy enumerations. The real implementation is fairly involved, but the basic idea behind streams is simple and relies on anonymous functions. In a nutshell, to make a lazy computation, you need to return a lambda that performs the computation. This makes the computation lazy, because you return its description rather than its result. When the computation needs to be materialized, the consumer code can call the lambda.

Recursion is the main tool for implementing loops. Tail recursion is used when you need to run an arbitrarily long loop.

## 4: Data abstractions

Being a functional language, Elixir promotes decoupling of data from the code. Instead of classes, you use modules, which are collections of functions.

The basic principles of data abstraction in Elixir can be summarized as follows:

- A module is in charge of abstracting some data.
- The module’s functions usually expect an instance of the data abstraction as the first argument.
- Modifier functions return a modified version of the abstraction.
- Query functions return some other type of data.

```elixir
defmodule TodoList do
  def new(), do: MultiDict.new()

  def add_entry(todo_list, date, title) do
    MultiDict.add(todo_list, date, title)
  end

  def entries(todo_list, date) do
    MultiDict.get(todo_list, date)
  end
end
```

This is a classical separation of concerns, where you extract a distinct responsibility into a separate abstraction, and then create another abstraction on top of it. A distinct `MultiDict` abstraction is now readily available to be used in other places in code if needed. Furthermore, you can extend `TodoList` with additional functions that don’t belong to `MultiDict`. For example, `due_today/2` would return all entries for today.

## 4.1.4 Abstracting with structs

A struct may exist only in a module, and a single module can define only one struct. Underneath, a struct instance is a special kind of map. Therefore, individual fields are accessed just like maps:

```sh
iex(2)> one_half.a
1

iex(3)> one_half.b
2
```

The nice thing about structs is that you can pattern-match on them:

```sh
iex(4)> %Fraction{a: a, b: b} = one_half
%Fraction{a: 1, b: 2}

iex(5)> a
1

iex(6)> b
2
```

This makes it possible to assert that some variable is really a struct:

```sh
iex(6)> %Fraction{} = one_half
%Fraction{a: 1, b: 2}

iex(7)> %Fraction{} = %{a: 1, b: 2}
** (MatchError) no match of right hand side value: %{a: 1, b: 2}
```
