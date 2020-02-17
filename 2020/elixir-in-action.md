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

```elixir
defmodule Fraction do
  ...
  def value(%Fraction{a: a, b: b}) do
    a / b
  end
  ...
end
```

`value/1` matches a fraction, taking its fields into individual variables and using them to compute the final result. The benefit of pattern matching is that the input type is enforced. If you pass anything that isn’t a fraction instance, you’ll get a match error.

Instead of decomposing fields into variables, you could also use dot notation:

```elixir
def value(fraction) do
  fraction.a / fraction.b
end
```

This code is arguably clearer, but it will run slightly more slowly than the previous case where you read all fields in a match. This performance penalty shouldn’t make much of a difference in most situations, so you can choose the approach you find more readable.

You should always be aware that structs are in reality just maps, so they have the same characteristics with respect to performance and memory usage. But a struct instance receives special treatment. Some things that can be done with maps don’t work with structs. For example, you can’t call the `Enum` function on a struct:

```sh
iex(1)> one_half = Fraction.new(1, 2)

iex(2)> Enum.to_list(one_half)
\*\* (Protocol.UndefinedError) protocol Enumerable not implemented for %Fraction{a: 1, b: 2}
```

Remember, a struct is a functional abstraction and should therefore behave according to the implementation of the module where it’s defined. In the case of the `Fraction` abstraction, you must define whether `Fraction` is enumerable and, if so, in what way. If this isn’t done, `Fraction` isn’t an enumerable, so you can’t call `Enum` functions on it.

In contrast, a plain map is an enumerable, so you can convert it to a list:

```sh
iex(3)> Enum.to_list(%{a: 1, b: 2})
[a: 1, b: 2]
```

On the other hand, because structs are maps, directly calling `Map` functions works:

```sh
iex(4)> Map.to_list(one_half)
[__struct__: Fraction, a: 1, b: 2]
```

The `struct` field has an important consequence for pattern matching. A struct pattern can’t match a plain map:

```sh
iex(5)> %Fraction{} = %{a: 1, b: 2}
** (MatchError) no match of right hand side value: %{a: 1, b: 2}
```

But a plain map pattern can match a struct:

```sh
iex(5)> %{a: a, b: b} = %Fraction{a: 1, b: 2}
%Fraction{a: 1, b: 2}

iex(6)> a
1

iex(7)> b
2
```

This is due to the way pattern matching works with maps. Remember, all fields from the pattern must exist in the matched term. When matching a map to a struct pattern, this isn’t the case, because `%Fraction{}` contains the field `struct`, which isn’t present in the map being matched.

The opposite works, because you match a struct to the `%{a: a, b: b}` pattern. Because all these fields exist in the `Fraction` struct, the match is successful.

## 4.1.5 Data transparency

It’s important to be aware that data in Elixir is always transparent. Clients can read any information from your structs (and any other data type), and there’s no easy way of preventing that. In that sense, encapsulation works differently than in typical OO languages. In Elixir, modules are in charge of abstracting the data and providing opera-tions to manipulate and query that data, but the data is never hidden.

```elixir
iex(1)> mapset = MapSet.new([:monday, :tuesday])
#MapSet<[:monday, :tuesday]>
```

Notice how `#MapSet` is printed in a special way, using `#MapSet<…>` output. This is due to the inspection mechanism in Elixir: whenever a result is printed in the shell, the function `Kernel.inspect/1` is called to transform the structure into an inspected string. For each abstraction you build, you can override the default behavior and provide your own inspected format. This is exactly what `MapSet` does, and you’ll learn how to do this for your type later in this chapter when we discuss protocols.

Occasionally you may want to see the pure data structure, without this decorated output. This can be useful when you’re debugging, analyzing, or reverse-engineering code. To do so, you can provide a special option to the `inspect` function:

```sh
iex(2)> IO.puts(inspect(mapset, structs: false))
%{__struct__: MapSet, map: %{monday: [], tuesday: []}, version: 2}
```

The output now reveals the complete structure of a date, and you can “see through” the `MapSet` abstraction. This demonstrates that data privacy can’t be enforced in functional abstractions; you can see the naked structure of the data. Remember from chapter 2 that the only complex types are tuples, lists, and maps. Any other abstraction, such as `MapSet` or your own `TodoList`, will ultimately be built on top of these types.

The benefit of data transparency is that the data can be easily inspected, which can be useful for debugging purposes. But as a client of a data abstraction, you shouldn’t rely on its internal representation, even though it’s visible to you.

One final thing you should know, related to data inspection, is the `IO.inspect/1` function. This function prints the inspected representation of a structure to the screen and returns the structure itself. This is particularly useful when debugging a piece of code. Look at the following example:

```sh
iex(1)> Fraction.new(1, 4) |>
          Fraction.add(Fraction.new(1, 4)) |>
          Fraction.add(Fraction.new(1, 2)) |>
          Fraction.value()
1.0
```

This code relies on the pipeline operator to perform a series of fraction operations. Let’s say you want to inspect the entire structure after each step. You can easily insert the call to `IO.inspect/1` after every line:

```sh
iex(2)> Fraction.new(1, 4) |>
          IO.inspect() |>
          Fraction.add(Fraction.new(1, 4)) |>
          IO.inspect() |>
          Fraction.add(Fraction.new(1, 2)) |>
          IO.inspect() |>
          Fraction.value()

%Fraction{a: 1, b: 4}
%Fraction{a: 8, b: 16}
%Fraction{a: 32, b: 32}
```

This works because `IO.inspect/1` prints the data structure and then returns that same data structure unchanged.

## 4.2.1 Generating IDs

Notice how you use `Map.put/3` to update the entry map. The input map may not contain the `id` field, so you can’t use the standard `%{entry | id: auto_id}` technique, which works only if the `id` field is already present in the map.

This function takes advantage of the fact that a map is an enumerable. When you use a map instance with functions from `Enum` or `Stream`, each map element is treated in the form of `{key, value}`.

## 4.2.2 Updating entries

You can go a step further and assert that the ID value of the entry hasn’t been changed in the lambda:

```sh
old_entry_id = old_entry.id
new_entry = %{id: ^old_entry_id} = updater_fun.(old_entry)
```

## 4.3 Polymorphism with protocols

_Polymorphism_ is a runtime decision about which code to execute, based on the nature of the input data.

## 4.3.1 Protocol basics

A _protocol_ is a module in which you declare functions without implementing them. Consider it a rough equivalent of an OO interface. The generic logic relies on the pro-tocol and calls its functions. Then you can provide a concrete implementation of the protocol for different data types.

## 4.3.2 Implementing a protocol

How do you implement a protocol for a specific type? Let’s refer to the Elixir source again. The following snippet implements `String.Chars` for integers:

```elixir
defimpl String.Chars, for: Integer do
  def to_string(term) do
    Integer.to_string(term)
  end
end
```

You start the implementation by calling the `defimpl` macro. Then you specify which protocol to implement and the corresponding data type. Finally, the `do/end` block contains the implementation of each protocol function. In the example, the implementation delegates to the existing standard library function `Integer.to_string/1`.

The `for: Type` part deserves some explanation. The type is an atom and can be any of following aliases: `Tuple`, `Atom`, `List`, `Map`, `BitString`, `Integer`, `Float`, `Function`, `PID`, `Port`, or `Reference`. These values correspond to built-in Elixir types.

In addition, the alias `Any` is allowed, which makes it possible to specify a fallback implementation. If a protocol isn’t defined for a given type, an error will be raised, unless a fallback to `Any` is specified in the protocol definition and an `Any` implementation exists. Refer to the `defprotocol` documentation (https://hexdocs.pm/elixir/Kernel.html#defprotocol/2) for details.

Finally, and most importantly, the type can be any other arbitrary alias (but not a regular, simple atom):

```elixir
defimpl String.Chars, for: SomeAlias do
  ...
end
```

This implementation will be called if the first argument of the protocol function (the `thing`) is a struct defined in the corresponding module.

For example, you can implement `String.Chars` for `TodoList`. Do this:

```sh
iex(1)> defimpl String.Chars, for: TodoList do
          def to_string(_) do
            "#TodoList"
          end
        end
```

Now you can pass a to-do list instance to `IO.puts/1`:

```sh
iex(2)> IO.puts(TodoList.new())
#TodoList
```

## 5.2.2 Message passing

Recall from chapter 3 that an error is raised when you can’t pattern-match the given term. The `receive` expression is an exception to this rule. If a message doesn’t match any of the provided clauses, it’s put back into the process mailbox, and the next message is processed.

Notice that the caller process is neither blocked nor interrupted while receiving messages. Sending a message doesn’t disturb the receiving process in any way. If the process is performing computations, it continues to do so. The only thing affected is the content of the receiving process’s mailbox. Messages remain in the mailbox until they’re consumed or the process terminates.

## 5.3.1 Server processes

This is ensured in the private `loop/0` function, which waits for a message, handles it, and finally calls itself, thus ensuring that the process never stops. This loop isn’t CPU-in-tensive. Waiting for a message puts the process in a suspended state and doesn’t waste CPU cycles.

It’s important to realize that a server process is internally sequential. It runs a loop that processes one message at a time. Thus, if you issue five asynchronous query requests to a single server process, they will be handled one by one, and the result of the last query will come after 10 seconds.

A server process can be considered a synchronization point. If multiple actions need to happen synchronously, in a serialized manner, you can introduce a single process and forward all requests to that process, which handles the requests sequentially.

Keep in mind that the server handles messages in the order received, so requests are handled in the proper order.

Why make the arithmetic operations asynchronous? Because you don’t care when they’re executed. Until you request the server’s state (via the `value/1` function), you don’t want the client to block. This makes the client more efficient, because it doesn’t block while the server is doing a computation.

## 5.3.4 Complex states

A stateful process is then used on top of functional abstractions as a kind of concurrent controller that keeps the state and that can be used by other processes in the system to manipulate or read parts of that state. For example, if you’re implementing a web server that manages multiple to-do lists, you most likely have one server process for each to-do list. While handling an HTTP request, you can find the corresponding to-do server and have it run the requested operation. Each to-do list manipulation runs concurrently, thus making your server scalable and more performant. Moreover, there are no synchronization problems, because each to-do list is managed in a dedicated process. Recall that a single process is always sequential, so multiple competing requests that manipulate the same to-do list are serialized and handled sequentially in the corresponding process.

## 5.3.5 Registered processes

If you know there will always be only one instance of some type of server, you can give the process a _local name_ and use that name to send messages to the process. The name is called _local_ because it has meaning only in the currently running BEAM instance.

## 5.4.1 A process is sequential

It has already been mentioned, but it’s very important, so I’ll stress it again: although multiple processes may run in parallel, a single process is always sequential — it either runs some code or waits for a message. If many processes send messages to a single process, that single process can significantly affect overall throughput.

## 5.4.2 Unlimited process mailboxes

Theoretically, a process mailbox has an unlimited size. In practice, the mailbox size is limited by available memory. Thus, if a process constantly falls behind, meaning messages arrive faster than the process can handle them, the mailbox will constantly grow and increasingly consume memory. Ultimately, a single slow process may cause an entire system to crash by consuming all the available memory. A more subtle version of the same problem occurs if a process doesn’t handle some messages at all.

In addition, large mailbox contents cause performance slowdowns. Recall how pattern matching works in the `receive` expression: messages are analyzed one by one, from oldest to newest, until a message is matched. Let’s say your process contains a million unhandled messages. When a new message arrives, receive iterates through the existing million messages before processing the new one. Consequently, the process wastes time iterating through unhandled messages, and its throughput suffers.

## 5.4.3 Shared-nothing concurrency

Deep-copying is an in-memory operation, so it should be reasonably fast, and occasionally sending a big message shouldn’t present a problem. But having many processes frequently send big messages may affect system performance.

A special case where deep-copying doesn’t take place involves binaries (including strings) that are larger than 64 bytes. These are maintained on a special shared binary heap, and sending them doesn’t result in a deep copy. This can be useful when you need to send information to many processes, and the processes don’t need to decode the string.

## 5.4.4 Scheduler inner workings

There are some special cases when a process will implicitly yield execution to the scheduler before its execution time is up. The most notable situation is when using `receive`. Another example is a call to the `Process.sleep/1` function. In both cases, the process is suspended, and the scheduler can run other processes. Another important case of implicit yielding involves I/O operations, which are internally executed on separate threads called _async_ threads. When issuing an I/O call, the calling process is preempted, and other processes get the execution slot. After the I/O operation finishes, the scheduler resumes the calling process. A great benefit of this is that your I/O code looks synchronous, while under the hood it still runs asynchronously. By default, BEAM fires up 10 async threads, but you can change this via the `+A n` Erlang flag.

## 7.2.3 Analyzing process dependencies

If you need to make sure part of the code is synchronized — that is, that there are no multiple simultaneous executions of critical code — it’s best to run that code in a dedicated process. When multiple clients want this code to run, they issue a request to that process. The process then serves as a synchronization point, making sure the critical code is run in a single process.

Also recall from chapter 5 that a process waiting for a message is suspended and doesn’t waste CPU resources. Thus, regardless of the number of processes, only those that are actually doing computations consume CPU. In this case, that means a client process doesn’t use CPU while it waits for a to-do server to finish.

Finally, you can be sure that a single list can’t be modified by two simultaneous clients. Recall that the list is managed by a single process. Even if a million clients try to modify the same list, their requests will be serialized in the corresponding to-do server and handled one by one.

## 7.3.1 Encoding and persisting

To encode an arbitrary Elixir/Erlang term, you use the `:erlang.term_to_binary/1` function, which accepts an Erlang term and returns an encoded byte sequence as a binary value. The input term can be of arbitrary complexity, including deep hierarchies of nested lists and tuples. The result can be stored to disk, retrieved at a later point, and decoded to an Erlang term with the inverse function `:erlang.binary_to_term/1`.

It’s worth noting that the `store` request is a cast, whereas `get` is a call. In this implementation, I decided to turn `store` into a cast because the client isn’t interested in a response. Using casts promotes scalability of the system because the caller issues a request and goes about its business. A huge downside of a cast is that the caller can’t know whether the request was successfully handled. In fact, the caller can’t even be sure that the request reached the target process. This is a property of casts. Casts promote overall availability by allowing client processes to move on immediately after a request is issued. But this comes at the cost of consistency, because you can’t be confident about whether a request has succeeded.

## 7.3.2 Using the database

you should generally be careful about possibly long-running `init/1` callbacks. Recall that `GenServer.start` returns only after the process has been initialized. Consequently, a long-running `init/1` function will cause the creator process to block. In this case, a long initialization of a to-do server will block the cache process, which is used by many clients. To circumvent this problem, there’s a simple trick. You can use `init/1` to send yourself an internal message and then initialize the process state in the corresponding `handle_info` callback:

```elixir
def init(params) do
  send(self(), :real_init)
  {:ok, nil}
end

...

def handle_info(:real_init, state) do
  ...
end
```

By sending yourself a message, you place a request in your own message queue. Then you return immediately, which makes the corresponding `GenServer.start` return, and the creator process can continue running other code. In the meantime, the process loop starts and immediately handles the first message, which is `:real_init`. This technique will generally work as long as your process isn’t registered under a local name. If the process isn’t registered, someone has to know its pid to send it a message, and that pid will only be known after `init/1` has finished. Hence, you can be sure that the message you send to yourself is the first one being handled. But if the process is registered, there’s a chance that someone else will put the message in the queue first by referencing the process via registered name. This can happen because at the moment `init/1` is invoked, the process is already registered under the name (due to the inner workings of `GenServer`). There are a couple of workarounds for this problem, the simplest one being not using the `:name` option and opting instead for manual registration of the process in the `init/1` callback after the message to self is sent:

```elixir
def init(params) do
  ...

  send(self(), :real_init)
  register(self(), :some_name)
end
```

It’s worth repeating that the synchronous call won’t block indefinitely. Recall that `GenServer.call` has a default timeout of five seconds, and you can configure it to be less for better responsiveness. Still, when a request times out, it isn’t removed from the receiver’s mailbox. A request is a message that’s placed in the receiver’s mailbox. A timeout means you give up waiting on the response, but the message remains in the receiver’s mailbox and will be processed at some point.

## 7.3.4 Addressing the process bottleneck

There are various reasons for running a piece of code in a dedicated server process:

- The code must manage a long-living state.
- The code handles a kind of a resource that can and should be reused, such as a TCP connection, database connection, file handle, pipe to an OS process, and so on.
- A critical section of the code must be synchronized. Only one process may run this code in any moment.

As you can see, each request is still serialized through the central server process, but this server process spawns one-off worker processes that perform the actual request handling. If you keep the code in the database process short and fast, you’ll get to keep a high degree of scalability with many workers running concurrently.

The server process spawns another worker process and then returns `{:noreply, state}`, indicating to `GenServer` that you won’t reply at this point. In the meantime, the spawned process handles the request and reports back to the caller with `GenServer.reply/2`. This is one situation where you need to use the second argument of `handle_call/3`: the caller pid and the unique ID of the request. This information is used in the spawned process to send the response message to the caller.

Always keep in mind that multiple processes run concurrently, whereas a single process handles requests sequentially. If computations can safely run in parallel, you should consider running them in separate processes. In contrast, if an operation must be synchronized, you’ll want to run it in a single process.

## 7.4 Reasoning with processes

A server process is a simple entity, something like a concurrent object. From within, it’s a sequential thing that accepts and handles requests, optionally managing internal state. From the outside, it’s a concurrent agent that exposes a well-defined communication interface. Another way to look at server processes is to think of them as services. Each process is like a small service that’s responsible for a single task.

## 8 Fault-tolerance basics

It’s somewhat surprising that the core tool for error handling is concurrency. In the BEAM world, two concurrent processes are completely separated; they share no memory, and a crash in one process can’t by default compromise the execution flow of another. Process isolation allows you to confine the negative effects of an error to a single process or a small group of related processes, which keeps most of the system functioning normally.

## 8.1.1 Error types

BEAM distinguishes three types of runtime errors: _errors_, _exits_, and _throws_.

Another type of error is the _exit_, which is used to deliberately terminate a process. To exit the current process, you can call `exit/1`, providing an exit reason:

```sh
iex(2)> spawn(fn ->
          exit("I'm done")
          IO.puts("This doesn't happen")
        end)
```

The exit reason is an arbitrary term that describes why you’re terminating the process.

The final runtime error type is a _throw_. To issue a throw, you can call `throw/1`:

```sh
iex(3)> throw(:thrown_value)
** (throw) :thrown_value
```

The purpose of throws is to allow nonlocal returns.

## 8.1.2 Handling errors

It’s worth noting that the `after` clause doesn’t affect the result of the entire `try` expression. The result of `try` is the result of the last statement either from the `do` block or from the corresponding `catch` block if something was caught.

You may recall the tail-call optimization from chapter 3. If the last thing a function does is call another function (or itself), then a simple jump will occur without a stack push. This optimization isn’t possible if the function call resides in a `try` expression. This is fairly obvious, because the last thing a function does is a `try` block, and it won’t finish until its `do` or `catch` block is done. Consequently, whatever is called in `try` isn’t the last thing a function does and is therefore not available for tail-call optimization.

## 8.2 Errors in concurrent systems

Concurrency plays a central role in building fault-tolerant, BEAM-based systems. This is due to the total isolation and independence of individual processes. A crash in one process won’t affect the others (unless you explicitly want it to).

Usually you don’t want a linked process to crash. Instead, you want to detect the process crash and do something about it. This can be done by _trapping exits_. When a process is trapping exits, it isn’t taken down when a linked process crashes. Instead, an exit signal is placed in the surviving process’s message queue, in the form of a standard message. A trapping process can receive these messages and do something about the crash. To set up an exit trap, you call `Process.flag(:trap_exit, true)`, which makes the current process trap exit signals.

There are two main differences between monitors and links. First, monitors are unidirectional — only the process that created a monitor receives notifications. In addition, unlike a link, the observer process won’t crash when the monitored process terminates. Instead, a message is sent, which you can handle or ignore.

## 8.3.3 Child specification

In any case, you can pass the specification map directly to `Supervisor.start_link`. Here’s an example:

```elixir
Supervisor.start_link(
  [
    %{
      id: Todo.Cache,
      start: {Todo.Cache, :start_link, [nil]}
    }
  ],
  strategy: :one_for_one
)
```

This will instruct the supervisor to invoke `Todo.Cache.start_link(nil)` to start the child. Recall that you changed `Todo.Cache.start_link` to take one argument (which is ignored), so you need to pass some value, in this example `nil`. One problem with this approach is that it’s error-prone. If something changes in the implementation of the cache, such as the signature of the start function, you need to remember to adapt the specification in the code starting the supervisor. To address this issue, `Supervisor` allows you to pass a tuple `{module_name, arg}` in the child specification list. In this case, `Supervisor` will first invoke `module_name.child_spec(arg)` to get the actual specification. This function must return the specification map. The supervisor then proceeds to start the child according to the specification. The `Todo.Cache` module already has `child_spec/1` defined, even though you didn’t write it yourself. The default implementation is injected by `use GenServer`. Therefore, you can also start the supervisor in the following way:

```elixir
Supervisor.start_link(
  [{Todo.Cache, nil}],
  strategy: :one_for_one
)
```

As a consequence, `Supervisor` will invoke `Todo.Cache.child_spec(nil)` and start the child according to the returned specification.

## 8.3.6 Linking all processes

By linking a group of interdependent processes, you can ensure that the crash of one process takes down its dependencies as well. Regardless of which process crashes, links make sure the entire structure is terminated. Because this will lead to the termination of the cache process, it will be noticed by the supervisor, which will start a new system.

## 9.1.2 Rich process discovery

Elixir’s standard library includes the implementation of a process registry in the `Registry`
module. This module allows you to associate a process with one or more arbitrary complex keys, and then find the process (its pid) by doing a key-based lookup.

A very useful property of `Registry` is that it links to all the registered processes. This allows the registry to notice the termination of these processes and remove the corresponding entry from its internal structure.

## 9.1.3 Via tuples

A _via tuple_ is a mechanism that allows you to use an arbitrary third-party registry to register OTP-compliant processes, such as `GenServer` and supervisor.

## 9.1.5 Supervising database workers

It’s worth repeating how the registry supports proper behavior in the system regarding restarted processes. When a worker is restarted, the new process has a different pid. But owing to the registry, the client code doesn’t care about that. You resolve the pid at the latest possible moment, doing a registry lookup prior to issuing a request to the database worker. Therefore, in most cases the lookup will succeed, and you’ll talk to the proper process. In some cases, the discovery of the database worker might fail, such as if the database worker crashes after the client process found its pid, but before the request is sent. In this case, the client process has a stale pid, so the request will fail. A similar problem can occur if a client wants to find a database worker that has just crashed. Restarting and registration run concurrently with the client, so the client might not find the worker pid in the registry. Both scenarios lead to the same result: the client process, in this case a to-do server, will crash, and the error will be propagated to the end user. This is a consequence of the highly concurrent nature of the system. A failure recovery is performed concurrently in the supervisor process, so some part of the system might not be in a consistent state for a brief period of time.

## 9.1.6 Organizing the supervision tree

By default, a supervisor restarts a terminated process regardless of the exit reason. Even if the process terminates with the reason `:normal`, it will be restarted. Sometimes you may want to alter this behavior. For example, consider a process that handles an HTTP request or a TCP connection. If such a process fails, the socket will be closed, and there’s no point in restarting the process (the remote party will be disconnected anyway). You still want to have such processes under a supervision tree because this makes it possible to terminate the entire supervisor subtree without leaving dangling processes. In this situation, you can set up a _temporary_ worker by providing `restart: :temporary` in `child_spec/1`. A temporary worker isn’t restarted on termination. Another option is a _transient_ worker, which is restarted only if it terminates abnormally. Transient workers can be used for processes that may terminate normally, as part of the standard system workflow. A typical example for this is a one-off job that you want to execute when the system is started. You could start the corresponding process (usually powered by the `Task` module) in the supervision tree, and configure it as transient. A transient worker can be specified by providing `restart: :transient` in `child_spec/1`.

## 9.2.4 Using temporary restart strategy

You might wonder why to-do servers are supervised if they’re not restarted. There are two important benefits. First, this structure ensures that the failure of a single to-do server doesn’t affect any other process in the system. In addition, as explained in section 9.1.6, this allows you to properly take down the system, or some service in the system, without leaving any dangling processes behind. To stop all to-do servers, you need to stop the `Todo.Cache` supervisor. In other words, supervision isn’t just about restarting crashed processes, but also about isolating individual crashes and enabling proper termination.

## 9.3 “Let it crash”

“Let it crash” can initially seem confusing, and people may mistake it for the “let everything crash” approach. There are two important situations in which you should explicitly handle an error:

- In critical processes that shouldn’t crash
- When you expect an error that can be dealt with in a meaningful way

## 9.3.1 Processes that shouldn't crash

Keep in mind that this technique doesn’t completely guard against a process crash. For example, you can always kill a process by invoking `Process.exit(pid, :kill)`, because a `:kill` exit reason can’t be intercepted even if you’re trapping exits. Therefore, you should always have a recovery plan for the crash of a critical process. Set up a proper supervision hierarchy to ensure the termination of all dependent processes in the case of an error-kernel process crash.

## 9.3.2 Handling expected errors

The whole point of the let-it-crash approach is to leave recovery of unexpected errors to supervisors. But if you can predict an error and you have a way to deal with it, there’s no reason to let the process crash.

As a general rule, if you know what to do with an error, you should definitely handle it. Otherwise, for anything unexpected, let the process crash and ensure proper error isolation and recovery via supervisors.

## 9.3.3 Preserving the state

Persistent state can have a negative effect on restarts. Let’s say an error is caused by state that’s somehow invalid (perhaps due to a bug). If this state is persisted, your process can never restart successfully, because the process will restore the invalid state and then crash again (either on starting or when handling a request). You should be careful when persisting state. If you can afford to, it’s better to start clean and terminate all other dependent processes.

## 10.1 Tasks

The `Task` module can be used to concurrently run a job — a process that takes some input, performs some computation, and then stops. In this sense, task-powered processes have a different flow than server processes. Whereas a `GenServer` process acts as a long-running server, a `Task`-powered process starts its work immediately, doesn’t serve requests, and stops when the work is done.

## 10.1.1 Awaited tasks

Awaited tasks can be very useful when you need to run a couple of mutually independent one-off computations and wait for all the results.

It should be noted that `Task.async/1` links the new task to the starter process. Therefore, if any task process crashes, the starter process will crash too (unless it’s trapping exits). The crash of the starter process will, in turn, cause all the other tasks started by the same process to crash. In other words, starting multiple tasks with `Task.async/1` has all-or-nothing semantics. The crash of a single task takes down all other tasks as well as the starter process.

## 10.1.2 Non-awaited tasks

Furthermore, in some cases you won’t want to link the task process to the starter process. This is typically done to make sure the task process lives on even if the starter process terminates. In such situations, you can use `Task.start_link/1`. The `Task.start_link/1` function can be thought of as an OTP-compliant wrapper around plain `spawn_link`. The function starts a separate process and links it to the caller. Then the provided lambda is executed in the started process. Once the lambda finishes, the process terminates with the reason `:normal`. Unlike `Task.async/1`, `Task.start_link/1` won’t send any message to the starter process.

## 10.2 Agents

The `Agent` module provides an abstraction that’s similar to `GenServer`. Agents require a bit less ceremony and can therefore eliminate some boilerplate associated with `GenServer`s. On the flip side, `Agent` doesn’t support all the scenarios that `GenServer` does. As a mechanical rule, if a `GenServer`-powered module implements `only init/1`, `handle_cast/2`, and `handle_call/3`, it can be replaced with an `Agent`. But if you need to use `handle_info/2` or `terminate/1`, `Agent` won’t suffice, and you’ll need to use `GenServer`.

## 10.2.1 Basic use

It’s worth mentioning that `Agent.update/2` is synchronous. The function only returns after the update has succeeded. An asynchronous update can be performed with `Agent.cast/2`.

## 10.2.3 Agent-powered to-do server

One problem with agents is that they completely open the process’s state. Recall that with `GenServer`, the state is private to the server and can only be manipulated via well-defined messages. With an `Agent`, though, the state can be manipulated in an arbitrary way through lambdas passed to `Agent` functions, which means the state is prone to accidental corruption. To guard against this problem, you’re advised to always wrap an agent in a dedicated module, and to only manipulate the agent process through functions of that module.

## 10.2.4 Limitations of agents

The `Agent` module can’t be used if you need to handle plain messages, or if you want to run some logic on termination. In such cases, you need to use `GenServer`.

Personally, I mostly don’t use `Agent` and start immediately with `GenServer`. Because converting an `Agent` into a `GenServer` requires some work, I’d much rather start with `GenServer` immediately. As an added bonus, this keeps the code more uniform, because all the server processes are implemented using the same abstraction. If you feel confused and aren’t sure whether to use `Agent` or `GenServer`, my advice is to always go for `GenServer` because it covers more scenarios and it’s not much more complicated than `Agent`.

## 10.3 ETS tables

ETS (Erlang Term Storage) tables are a mechanism that allows you to share some state between multiple processes in a more efficient way. ETS tables can be thought of as an optimization tool. Whatever you can do with an ETS table can also be done with `GenServer` or `Agent`, but the ETS version can often perform much better.

`mix run -e "Bench.run(KeyValue)"`: You haven’t seen the mix run command before. This command compiles the project, starts a BEAM instance, and then executes the expression provided via the `-e` argument, which means that `Bench.run/1` is invoked. Once the function is done, the BEAM instance stops.

the VM goes to great lengths to use CPUs as well as possible, but the fact remains that you have many processes competing for limited resources. As a result, the key/value server doesn’t get a single CPU core all to itself. The process must sometimes wait for its turn if BEAM schedulers run other processes in the system. Because the key/value server has fewer CPU resources for doing its job, it will take more time to compute the results.

## 10.3.1 Basic operations

ETS table is a separate memory-data structure where you can store Erlang terms. This makes it possible to share the system-wide state without introducing a dedicated server process. The data is kept in an _ETS table_ — a dynamic memory structure where you can store tuples.

Compared to other data structures, ETS tables have some unusual characteristics:

- There’s no specific ETS data type. A table is identified by its ID (a reference type) or a global name (an atom).
- ETS tables are mutable. A write to a table will affect subsequent read operations.
- Multiple processes can write to or read from a single ETS table. Writes and reads are concurrent.
- Minimum isolation is ensured. Multiple processes can safely write to the same row of the same table. The last write wins.
- An ETS table resides in a separate memory space. Any data coming in or out is deep-copied.
- ETS doesn’t put pressure on the garbage collector. Overwritten or deleted data is immediately released.
- An ETS table is deeply connected to its owner process (by default, the process that created the table). If the owner process terminates, the ETS table is reclaimed.
- Other than on owner-process termination, there’s no automatic garbage collection of an ETS table. Even if you don’t hold a reference to the table, it still occupies memory.

These characteristics mean ETS tables somewhat resemble processes. In fact, it’s often said that ETS tables have process semantics. You could implement ETS table with processes, but such an implementation would be much less efficient. In BEAM, ETS tables are powered by C code, which ensures better speed and efficiency. The fifth point in the previous list is especially interesting. Because data is deep-copied to and from an ETS table, there’s no classical mutability problem. Once you read data from an ETS table, you have a snapshot that no one can change. Regardless of other processes possibly modifying the contents of those rows in the ETS table, the data you read remains unaffected.

`:ets.new(:some_table, [:public, :duplicate_bag])`: Finally, it’s worth discussing the table name. This argument must be an atom, and by default it serves no purpose (although, strangely enough, you must still provide it). You can create multiple tables with the same name, and they’re still different tables. But if you provide a `:named_table` option, the table becomes accessible via its name.

ETS tables are a limited resource. You can create at most 1,400 tables per BEAM instance. This number can be increased by setting the environment variable `ERL_MAX_ETS_TABLES` while starting the system (for example, `iex --erl '-env ERL_MAX_ETS_TABLES 10000'`). The reason for the limitation is that an ETS table initially takes up a couple of kilobytes of memory. You should, in general, avoid overusing ETS tables; having a large number of them in the system is usually an indication of misuse.

## 10.3.2 ETS powered key/value store

There are a couple of reasons for this improvement. First, ETS operations are handled immediately in the client process. In contrast, a cross-process request involves putting a message in the mailbox of the receiver and then waiting for the receiver to be scheduled in and to handle the request. If the request is a synchronous call, the client process also has to wait for the response message to arrive. In addition, changes to ETS tables are destructive. If a value under some key is changed, the old value is immediately released. Therefore, data managed in ETS tables doesn’t put any pressure on a garbage collector. In contrast, transforming standard immutable data generates garbage. In a `GenServer`-based key/value store, frequent writes will generate a lot of garbage, and this means that the server process is occasionally blocked while it’s being garbage-collected.

The main reason for this scaling lies in the fact that cache operations are executed in the client process, so you don’t need to perform `GenServer`-based serialization. The atomic operations provided by the `:ets` module are properly synchronized and can safely run simultaneously in multiple processes. Operations working on different keys can run in parallel. Even the reads of the same key can work in parallel. Only writes will block other operations on the same key.

On the flip side, the vocabulary of write operations is small. You can perform key/value writes with `:ets.insert/2`, delete a row with `:ets.delete/2`, modify a row with `:ets.update_element/3`, and atomically update an integer in a row with `:ets.update_counter/4`. For more complex scenarios, you’ll likely need to channel writes through a `GenServer`. Therefore, you can think of ETS tables as being an optimization tool. They’re extremely efficient in simple scenarios, but not as powerful or flexible as server processes.

You might wonder why `GenServer` is still used in the ETS-based key/value store. The sole purpose of this process is to keep the table alive. Remember, an ETS table is released from memory when the owner process terminates. Therefore, you need to have a distinct, long-running process that creates and owns the table.

## 10.3.3 Other ETS operations

It’s also possible to use ETS tables as a faster alternative to immutable data structures, such as maps. Because changes to ETS tables are destructive and data is immediately released, there won’t be any garbage-collection penalty involved, so you can expect more predictable latency, with fewer deviations. There is a caveat, though. Remember that data is copied between an ETS table and a client process. Consequently, if your row data is complex and large, ETS tables may yield worse performance than pure, immutable data structures. Another important downside of ETS tables is that unlike plain data, they can’t be sent over the network to another BEAM instance. That means relying on ETS makes it harder to take advantage of distribution facilities (described in chapter 12). In general, you should avoid using ETS and instead favor immutable structures as much as possible. Resort to ETS only in cases where you can obtain significant performance gains.

## 11.1.2 The application behavior

The critical part of the application description is `mod: {HelloWorld, []}`, specified by `application/0`. This part describes the module that will be used to start the application. When you start the application, the function `HelloWorld.Application.start/2` is called with arguments provided in the second element of the tuple (`[]` in this case).

## 11.1.3 Starting the application

It should be noted that you can’t start multiple instances of a single application. In this sense, an application is like a singleton in a single BEAM instance. Trying to start an already-running application will return an error.

But `Application.stop/1` stops only the specified application, leaving dependencies (other applications) running. To stop the entire system in a controlled way, you can invoke `System.stop/0`. This function will take down all the OTP applications, and then the BEAM instance itself. Both `Application.stop/1` and `System.stop/0` work in a polite way. Every process in the supervision tree can perform some final cleanup in its `terminate/1` callback.

## 11.1.4 Library applications

You don’t need to provide the `mod: …` option from the `application/0` function in mix.exs:

```elixir
defmodule HelloWorld.Application do
  ...

  def application do
    []
  end

  ...
end
```

In this case, there’s no application callback module, which in turn means there’s no top-level process to be started. Oddly enough, this is still a proper OTP application. You can even start it and stop it.
What’s the purpose of such applications? This technique is used for _library applications_: components that don’t need to create their own supervision tree. As the name indicates, these are usually simpler libraries; a typical example is a JSON parser. For example, Erlang’s own `stdlib` application (http://erlang.org/doc/apps/stdlib/index.html) is a pure library application because it exposes various utility modules but doesn’t need to manage its own supervision tree. Library applications are useful because you can list them as runtime dependencies. This plays an important role when you start to assemble the deployable release.

## 11.1.5 Creating a to-do application

It’s worth noting that you get the same benefit when running tests. When you invoke `mix test`, all of the essential processes in your system are started.

## 11.1.6 The application folder structure

For most `mix` tasks, the default environment is dev, indicating that you’re dealing with development. One exception to that rule is the test task. When you invoke `mix test`, the mix environment is automatically set to test.

## 11.3.2 Starting the server

When you include `Todo.Web` in a supervisor, a bunch of new processes will be started. There will be at least one process that listens on a given port and accepts requests. Then, each distinct TCP connection will be handled in a separate process, and your callbacks (which you have to implement) will be invoked in those request-specific processes. But you don’t need to care about any of these details. Owing to `child_spec/1`, the Plug library keeps these internals to itself.

## 11.4 Configuring applications

Config scripts are evaluated before the project is compiled and started, and their sole purpose is to provide values for application environments. The `mix` tool makes sure that the configuration is loaded into the application environment before the application is started. Finally, from the application code, you can retrieve an environment value using functions from the `Application` module.

## 11.4.1 Application environment

The config.exs file is a script that’s evaluated by the `mix` tool when compiling the project and starting the application.

## 11.4.2 Varying configuration

The function `Mix.env/0` is not available at runtime, so you shouldn’t invoke it from your regular code (standard functions that are compiled and executed at runtime). This function can only be used in config scripts, in the code of custom mix tasks, and in the code that’s executed at compile time (Elixir macros).

## 11.4.3 Config script considerations

You should keep in mind that scripts such as config.exs, or environment-specific ones like dev.exs, are evaluated before the project is compiled and started. This means you’re limited in what you can do in such scripts. Even though the scripts are regular Elixir code, you can only invoke functions from the Elixir standard library and `mix` helper functions (such as `Mix.env/0`). You can’t invoke functions from your own modules, nor any code from your dependencies. That’s because these modules aren’t yet available when the config scripts are being evaluated.

## 12 Building a distributed system

Much like remote components, processes live their own lives and run in total isolation from each other. Issuing a request to another local process can be considered a remote call, and message-passing has much in common with remote network communication.

## 12.1.1 Starting a cluster

It’s of course possible to connect multiple nodes. In fact, BEAM by default tries to establish a fully connected cluster. If you start a third node, `node3`, and connect it to `node2`, a connection is established to all other nodes that `node2` is connected to.

Adding a new node to such a cluster amounts to establishing a connection to a single node from the cluster. The new node will then automatically connect to all nodes in the cluster.

Node disconnection deserves a special mention. After the connection is established, each node periodically sends tick messages to all of its connected peers, to check whether they’re still alive. All nodes that fail to respond to four consecutive tick messages are considered to be disconnected and are removed from the list of connected nodes. There’s no automatic attempt to reconnect those nodes, but it’s possible to register and receive notifications when a node is disconnected, using the `Node.monitor/1` function (https://hexdocs.pm/elixir/Node.html#monitor/2). Moreover, you can monitor all node connections and disconnections with the help of `:net_kernel.monitor_nodes` (http://erlang.org/doc/man/net_kernel.html#monitor_nodes-1).

## 12.1.2 Communicating between nodes

Something unexpected is happening in listing 12.1. Even though the lambda has been executed on `node2`, the output is printed in the shell of `node1`. How is this possible? The reason lies in how Erlang does standard I/O operations. All standard I/O calls (such as `IO.puts/1`) are forwarded to the group leader — a process that’s in charge of performing the actual input or output. A spawned process inherits the group leader from the process that spawned it, even when you’re spawning a process on another node. Therefore, your process may run on `node2`, but its group leader is still on `node1`. As a consequence, the string to be printed is created on `node2` (as the string contents prove), but the output is printed on `node1`.

Another important primitive is the ability to send messages to processes regardless of their location. This property is also known as _location transparency_. The send operation works the same way regardless of the node on which the target process is running.

Even though the lambda runs on another node, the closure mechanism still works.

There are no limits to what can be sent as a message. Whatever works in one BEAM instance will work across different instances (with a small caveat, described in the sidebar on lambdas). When the destination process is on another node, the message is encoded using `:erlang.term_to_binary/1` and decoded on the target node with `:erlang.binary_to_term/1`.

Avoid spawning lambdas or sending them to different nodes: You can spawn lambdas from your shell, which is a somewhat special case because shell-defined lambdas embed their own code and are interpreted dynamically on each invocation. In contrast, lambdas that are defined in module functions can be spawned remotely (or sent to a remote node via a message) only if both nodes are powered by exactly the same compiled code. These requirements are hard to satisfy if you start running a multinode cluster and then need to update the code. You can’t simultaneously upgrade all the nodes in the cluster, so at some point the code on the nodes will differ. Therefore, it’s generally better to avoid passing lambdas to a remote node. Instead, you should use the `Node.spawn/4` function, which accepts an MFA (module, function, argu-ments list) that identifies a function to be invoked on the target node. This is safe to use as long as the module exists on the target node and exports the corresponding function.

It’s possible to reference a locally registered process on another node by using `{some_alias, some_node}`.

You can also use the `{some_alias, some_node}` form when making `GenServer`-powered requests (casts and calls). Finally, there are two special functions, `GenServer.abcast/3` and `GenServer.multi_call/4`, that let you issue a request to all locally registered processes on given nodes.

## 12.1.3 Process discovery

The simplest way to do cluster-wide discovery is to use the `:global` module (http://erlang.org/doc/man/global.html), which provides a global name registration facility.

Note that lookups are local. When a registration is being performed, all nodes are contacted, and they cache the registration information in their local ETS tables. Each subsequent lookup on any node is performed on that node, without any additional chatter. This means a lookup can be performed quickly, whereas registration requires chatting between nodes.

Take a look at the shape of this pid: `#PID<7954.90.0>`. The first number in the pid string representation isn’t 0: this indicates that you’re dealing with a process from some other node.

All the pids you’ve seen up to now have had a similar form: `<0.X.0>`, where `X` is a positive integer. Internally, each process has a node-wide unique identifier. This identifier can be seen in the last two numbers of the string representation. If you create enough processes on a single node, the third number will also be greater than zero. The first number represents the node number — an internal identifier of the node where the process is running. When this number is zero, the process is from the local node. Conversely, when the output includes a pid in the form `<X.Y.Z>` and `X` isn’t zero, you can be sure it’s a remote process. To programmatically determine the node where a process is running, you can use `Kernel.node/1` (https://hexdocs.pm/elixir/Kernel.html#node/1).

Another frequent discovery pattern occurs when you want to register multiple processes under the same alias. This may sound strange, but it’s useful in situations where you want to categorize processes in a cluster and broadcast messages to all processes in a category. For this particular problem, you can use the strangely named `:pg2` (process groups, version 2) module (http://erlang.org/doc/man/pg2.html). This module allows you to create arbitrarily named cluster-wide groups and add multiple processes to those groups. This addition is propagated across all nodes, and later you can query the group and get the list of all processes belonging to it.

How can you use this technique? When you want to make an update to Bob’s to-do list, you can query the corresponding process group and get a list of all processes responsible for Bob’s list. Then you can issue your request to all processes: for example, by using `GenServer.multi_call/4`. This ensures that all replicas in the cluster are updated. But when you need to issue a query (for example, to retrieve to-do list entries), you can do this on a single process from the group (no need to perform multiple queries on all replicas, unless you want better confidence). Therefore, you can choose a single pid from the process group. For this purpose, you can use `:pg2.get_closest_pid/1`, which returns the pid of a local process, if one exists, or a random process from the group otherwise.

Just like the `:global` module, `:pg2` is implemented in pure Erlang and is also an elaborate version of a process registry. Group creations and joins are propagated across the cluster, but lookups are performed on a locally cached ETS table. Process crashes and node disconnects are automatically detected, and nonexistent processes are removed from the group.

## 12.1.5 Other distribution services

More often, you’ll want to obtain the result of a remote function call or invoke a function on multiple nodes and collect all the results. In such cases, you can refer to the `:rpc` Erlang module (http://erlang.org/doc/man/rpc.html), which provides various useful helpers. For example, to call a function on another node and get its result, you can use `:rpc.call/4`, which accepts a node and an MFA identifying the function to be called remotely.

There’s also a simple helper for the acquire/release pattern available in the form of `:global.trans/2`(http://erlang.org/doc/man/global.html#trans-2), which takes the lock, runs the provided lambda, and finally releases the lock. Locking is something you should usually avoid because it causes the same kinds of problems as classical synchronization approaches. Excessively relying on locks increases the possibility of deadlocks, livelocks, or starvation. Generally you should synchronize through processes because it’s easier to reason about the system this way. But used judiciously, locks can sometimes improve performance. Remember that message passing has an associated cost; this is especially true in distributed systems, where a message must be serialized and transmitted over the network. If a message is very large, this can introduce significant delays and hurt system performance. Locks can help here, because they let you synchronize multiple processes on different nodes without needing to send large messages to another process. Here’s a sketch of this idea. Let’s say you need to ensure that the processing of a large amount of data is serialized in the entire cluster (at any point in time, at most one process may run in the entire cluster). Normally this is done by passing the data to a process that acts as a synchronization point. But passing a large chunk of data may introduce a performance penalty because data must be copied and transmitted over the network. To avoid this, you can synchronize different processes with locks and then process the data in the caller context.

## 12.2.2 The distributed to-do cache

You may be puzzled by all these different registration facilities, so let’s recall the key differences:

- The basic registration facility is a local registration that allows you to use a simple atom as an alias to the single process on a node.
- `Registry` extends this by letting you use rich aliases — any term can be used as an alias.
- `:global` allows you to register a cluster-wide alias.
- `:pg2` is useful for registering multiple processes behind a cluster-wide alias (process group), which is usually suitable for distributed pub-sub scenarios.

## 12.3.1 Node names

It’s also worth noting that a node can connect only to a node that has the same type of name. In other words, a connection between a long-named node and a short-named node isn’t possible.

## 12.3.2 Cookies

In order to connect two nodes, they must agree on a magical cookie — a kind of passphrase that’s verified while the nodes are connecting. The first time you start a BEAM instance, a random cookie is generated for you and persisted in your home folder in the .erlang.cookie file. By default, all nodes you start on that machine will have this cookie. A node running on another machine will have a different cookie, so connecting two nodes on different machines won’t work by default; you need to somehow make all nodes use the same cookie.

## 12.3.3 Hidden nodes

Keep in mind, though, that a hidden node is still maintained in the node’s connection list, albeit under a different `hidden` tag. You can explicitly retrieve hidden nodes by calling `Node.list([:hidden])`. Calling `Node.list([:connected])` returns all connected nodes, both hidden and visible, whereas calling `Node.list([:visible])` returns only visible nodes. When you want to perform a cluster-wide operation, you should generally use the `:visible` option. Services provided by `:global`, `:rpc`, and `:pg2` ignore hidden nodes. Registering a global alias on one node won’t affect any hidden peer, and vice versa.

## 12.3.4 Firewalls

The first component, the Erlang Port Mapper Daemon (EPMD), is an OS process that’s started automatically when you start the first Erlang node on the host machine. This component acts as a node name resolver on the host machine. EPMD knows the names of all currently running BEAM nodes on the machine. When a node wants to connect to a node on this machine, it first queries EPMD to determine which port the target node is listening on, and then it contacts the target node. EPMD listens on port 4369, and this port must be accessible from remote machines.

In addition, each node listens on a random port that needs to be accessible as well, because it’s used to establish the connection between two nodes. Obviously it’s not particularly helpful that the node’s listening port is random, because it’s not possible to define firewall rules. Luckily, you can provide a fixed range of ports on which a node will listen. This can be done by setting the `inet_dist_listen_min` and `inet_dist_listen_max` environment variables of the kernel app at the command line.

Other than the magical cookie, no particular security model is provided. When you connect to a remote node, you can do anything on that node, including running system commands. If the remote node has root privileges, you have full access to the entire remote host. Erlang’s distributed model was designed to run in a trusted environment, and you should be aware of that. In particular, this means that in production your BEAM instances should run under minimal privileges. Moreover, you shouldn’t expose your BEAM instances over the internet. If you need to connect nodes from different networks, you should consider switching to SSL as the communication protocol. Some pointers for doing this are provided in the Erlang documentation at http://erlang.org/doc/apps/ssl/ssl_distribution.html.

## 13.1.1 Using the mix and elixir commands

A slightly more elaborate option is to use the `elixir` command (compared to `mix run --no-halt`):

```sh
>$ elixir -S mix run --no-halt

Starting to-do cache
```

This approach requires a bit more typing, but it allows you to run the system in the background.

By using the `--detached` flag, you can start the system in _detached mode_. The OS process will be detached from the terminal, and there will be no console output (it’s redirected to `/dev/null`). When starting a detached system, it’s also useful to turn the BEAM instance into a node, so you can later interact with it and terminate it when needed: `elixir --detached --sname todo_system@localhost -S mix run --no-halt`. This starts the BEAM instance in background.

You can connect to a running BEAM instance and interact with it. It’s possible to establish a _remote shell_ — something like a terminal shell session to the running BEAM instance. In particular, with the the `--remsh` option you can start another node and use it as a shell to the `todo_system` node: `iex --sname debugger@localhost --remsh todo_system@localhost --hidden`.

To stop the running system, you can use the `System.stop` function (https://hexdocs.pm/elixir/System.html#stop/1), which takes down the system in a graceful manner. It shuts down all running applications and then terminates the BEAM instance: `iex(todo_system@localhost)1> System.stop()`.

`elixir --sname terminator@localhost stop_node.exs`: Running a script starts a separate BEAM instance and _interprets_ the code in that instance. After the script code is executed, the host instance is terminated. Because the script instance needs to connect to a remote node (the one you want to terminate), you need to give it a name to turn the BEAM instance into a proper node.

## 13.1.2 Running scripts

An .exs script is fine for simpler tools, but it’s not efficient when the code becomes more complex, or if you need to include third-party libraries as dependencies. In those cases, it’s best to use a proper `mix` project and build a full OTP application. But because you’re not building a system that runs continuously, you also need to include a _runner_ module in the project — something that does processing and produces output:

```elixir
defmodule MyTool.Runner do
  def run do
    ...
  end
end
```

Then you can start the tool with `mix run -e MyTool.Runner.run`. This starts the OTP application, invokes the `MyTool.Runner.run/0` function, and terminates as soon as the function is finished.

Finally, you can package the entire tool in an _escript_ — a single binary file that embeds all your .beam files, Elixir .beam files, and the start-up code. An escript file is thus a fully compiled, cross-platform script that requires only the presence of Erlang on the running machine. For more details, refer to the `mix escript.build` documentation (https://hexdocs.pm/mix/Mix.Tasks.Escript.Build.html).

## 13.1.3 Compiling for production

You can use the mix environment to conditionally include code for development- or test-time convenience. For example, you can rely on the `Mix.env/0` function to define different versions of a function. Here’s a simple sketch:

```elixir
defmodule Todo.Database do
  case Mix.env() do
    :dev ->
      def store(key, data) do ... end

    :test ->
      def store(key, data) do ... end

    _ ->
      def store(key, data) do ... end
  end
end
```

Notice how you branch on the result of `Mix.env/0` at the module level, outside of any functions. This is a compile-time construct, and this code runs during compilation. The final definition of `store/2` will depend on the mix environment you’re using to compile the code. In the dev environment, you might run additional logging and benchmarking, whereas in the test environment you might use an alternative database and perhaps in-memory storage, such as a public ETS table. It’s important to understand that `Mix.env/0` has meaning only during compilation. You should never rely on it at runtime. In any case, your code may contain such conditional definitions, so you should assume that your project isn’t completely optimized when compiled in the dev environment.

It should be obvious from the discussion that the default compiled code (in the dev environment) isn’t optimal. The dev environment allows for better development convenience, but it makes the code perform less efficiently. When you decide to measure how your system behaves under a heavier load, you should always compile everything in the prod environment. Measuring with the dev environment may give you false indications about bottlenecks, and you may spend energy and time optimizing code that isn’t problematic at all in production.

## 13.2 OTP releases

This approach provides all sorts of benefits. First, you can build the system on your development machine or the build server and ship only binary artifacts. Furthermore, the host machine doesn’t need to have any tools installed. If you embed the minimum Erlang runtime into the release, you don’t even need Elixir and Erlang installed on the production server. Whatever is required to run the system will be part of your release package. Finally, releases pave the way for systematic online system upgrades (and downgrades), known in Erlang as _release handling_.

## 13.2.3 Release contents

If you need to include additional files in the release, the best way to do it is to create a priv folder under your project root. This folder, if it exists, automatically appears in the release under the application folder. When you need to access a file from the priv folder, you can invoke `Application.app_dir(:an_app_name, "priv")` to find the folder’s absolute path.

The vm.args file can be used to provide flags to the Erlang runtime, such as the aforementioned `+P` flag that sets the maximum number of running processes.

Sys.config is an Erlang file that contains OTP environment variables, as specified in your mix.exs and config.exs files.

Consequently, sys.config can be used to tweak settings without the need to rebuild the release. For example, if you want to change the listening port, you can edit sys.config on the target machine and restart the system.

If you take a closer look at the contents of the `_build/prod/rel/todo/releases/0.1.0` folder, you’ll notice a compressed tarball named todo.tar.gz, which is essentially a compressed version of the entire release. When you want to deploy to the target machine, you can copy this file, unpack it, and start the system by running `bin/todo start`.

This file isn’t just a convenience for faster uploads. It plays an important role in the live upgrade process. When you make changes to your code, you can increase the version number in mix.exs and rebuild the release. The resulting release package will reside in a different folder (such as `_build/prod/rel/todo/releases/0.2.0`). You can then upload this file to the corresponding subfolder of your running system and invoke `bin/todo upgrade "0.0.2"`, which will make your system upgrade on the fly, without restarting.

If you’re running your system as a cluster, you can also migrate nodes one by one, thus keeping the entire system running. Resort to live upgrades only when you establish that simple restarts aren’t good enough for your particular use case.

Tools such as `distillery` assemble the entire release in a single pass on the build machine. This should serve most purposes, but sometimes it may not fit your particular needs. For example, you may want to deploy your system on a large number of different machines. In such cases, embedding the Erlang runtime won’t work, because it’s tied to the particular OS and architecture. It’s more likely you’ll want to assemble just the .beam and configuration files on the build server, finishing the release assembly process on each target machine. It’s even possible to have your node load the code from a remote machine, which means your release doesn’t need to contain application binaries.

## 13.3.1 Debugging

The key to understanding a highly concurrent system lies in logging and tracing. Once something goes wrong, you’ll want to have as much information as possible, which will allow you to find the cause of the problems.

Another useful feature is _pry_, which allows you to temporarily stop execution in the `iex`
shell and inspect the state of the system, such as variables that are in scope.

## 13.3.3 Interacting with the system

Being GUI-based, `observer` works only when there’s a windowing system in the host OS. On the production server, this usually isn’t the case. But you can start the observer locally and have it gather data from a remote node.

The production system doesn’t need to run the `observer` application, but it needs to contain the modules that gather data for the remote `observer` application. These modules are part of the `runtime_tools` application that you need to include in your release. You can easily do this via the `:extra_applications` option in mix.exs.

Notice that `:extra_applications` serves a different purpose than the `deps` function in the mix.exs file. With `deps`, you list third-party dependencies that have to be fetched and compiled. In contrast, with `:extra_applications` you list Elixir and Erlang stock applications that are already compiled on your disk, as a part of Erlang and Elixir installations. The code of these dependencies doesn’t have to be fetched, and nothing needs to be compiled. But you still need to list these dependencies, to ensure that applications are included in the OTP release.

## 13.3.4 Tracing

In addition to this function, an entire module called `:dbg` (http://erlang.org/doc/man/dbg.html) is available, which simplifies tracing. You can run `:dbg` directly on the attached console, but it’s also possible to start another node and make it trace the main system.
