# Julia Basics {#sec:julia_basics}

> **_NOTE:_**
> Neste capítulo, descreveremos o básico de Julia como linguagem de programação.
> Por favor, note que isso não é *estritamente necessário* para você usar Julia como uma ferramenta de manipulação e visualização de dados.
> Ter um conhecimento básico de Julia definitivamente o tornará mais *eficaz* e *eficiente* no uso de Julia.
> No entanto, se você preferir começar imediatamente, pode pular para @sec:dataframes e aprenda sobre dados tabulares em `DataFrames.jl`.

Aqui, vamos trazer uma visão mais geral sobre a linguagem de Julia, *não* algo aprofundado.
Se você já está familiarizado e confortável com outras linguagens de programação, nós encorajamos você a ler a documentação de Julia (<https://docs.julialang.org/>).
Os documentos são um excelente recurso para você se aprofundar em Julia.
Eles cobrem todos os fundamentos e casos extremos, mas podem ser complicados, especialmente se você não estiver familiarizado com a documentação do software.

Cobriremos o básico de Julia.
Imagine que Julia é um carro sofisticado repleto de recursos, como um Tesla novo.
Vamos apenas explicar a você como "dirigir o carro, estacioná-lo e como navegar no trânsito".
Se você quer saber o que "todos os botões no volante e painel fazem", este não é o livro que você está procurando.

## Sintaxe da linguagem {#sec:syntax}

Julia é uma **linguagem de tipo-dinâmico** com um compilador just-in-time.
Isso significa que você não precisa compilar seu programa antes de executá-lo, como precisaria fazer com C++ ou FORTRAN.
Em vez disso, Julia pegará seu código, adivinhará os tipos quando necessário e compilará partes do código antes de executá-lo.
Além disso, você não precisa especificar explicitamente cada tipo.
Julia vai supor os tipos para você.

As principais diferenças entre Julia e outras linguagens dinâmicas como R e Python são:
Primeiro, Julia **permite ao usuário especificar declarações de tipo**.
Você já viu alguns tipos de declarações em *Por que Julia?* (@sec:why_julia): eles são aqueles dois pontos duplos `::` que às vezes vem depois das variáveis.
No entanto, se você não quiser especificar o tipo de suas variáveis ou funções, Julia terá o prazer de inferir (adivinhar) para você.

Em segundo lugar, Julia permite que os usuários definam o comportamento da função em muitas combinações de tipos de argumento por meio de múltiplos despachos.
Também falamos sobre despachos múltiplos em @sec:julia_accomplish.
Definimos um comportamento de tipo diferente, definindo novas assinaturas de função para tipos de argumento enquanto usamos o mesmo nome de função.

### Variáveis {#sec:variable}

As variáveis são valores que você diz ao computador para armazenar com um nome específico, para que você possa recuperar ou alterar seu valor posteriormente.
Julia tem diversos tipos de variáveis, mas, em ciência de dados, usamos principalmente:

* Integers: `Int64`
* Real Numbers: `Float64`
* Boolean: `Bool`
* Strings: `String`

Inteiros e números reais são armazenados usando 64 bits por padrão, é por isso que eles têm o sufixo `64` no nome do tipo.
Se você precisar de mais ou menos precisão, existem os tipos `Int8` ou `Int128`, por exemplo, onde maior significa mais precisão.
Na maioria das vezes, isso não será um problema, então você pode simplesmente seguir os padrões.

Criamos novas variáveis escrevendo o nome da variável à esquerda e seu valor à direita, e no meio usamos o `=` operador de atribuição.
Por exemplo:

```jl
s = """
    name = "Julia"
    age = 9
    """
scob(s)
```

Observe que a saída de retorno da última instrução (`idade`) foi impresso no console.
Aqui, estamos definindo duas novas variáveis: `nome` e `idade`.
Podemos recuperar seus valores digitando os nomes dados na atribuição:

```jl
scob("name")
```

Se quiser definir novos valores para uma variável existente, você pode repetir as etapas da atribuição.
Observe que Julia agora substituirá o valor anterior pelo novo.
Suponho que o aniversário de Julia já passou e agora fez 10 anos:

```jl
scob("age = 10")
```

Podemos fazer o mesmo com `name`. Suponha que Julia tenha ganho alguns títulos devido à sua velocidade incrível.
Mudaríamos a variável `name` para o novo valor:

```jl
s = """
    name = "Julia Rapidus"
    """
scob(s)
```

Também podemos fazer operações em variáveis como adição ou divisão.
Vamos ver quantos anos Julia tem, em meses, multiplicando `age` por 12:

```jl
s = "12 * age"
scob(s)
```
Podemos inspecionar os tipos de variáveis usando a função `typeof`:

```jl
sco("typeof(age)")
```

A próxima pergunta então se torna:
"O que mais posso fazer com os inteiros?"
Há uma função boa e útil, `methodswith` que expõe todas as funções disponíveis, junto com sua assinatura, para um certo tipo.
Aqui, vamos restringir a saída às primeiras 5 linhas:

```jl
s = """
    first(methodswith(Int64), 5)
    """
sco(s; process=catch_show)
```

### Tipos definidos pelo usuário {#sec:struct}

Ter variáveis sem qualquer tipo de hierarquia ou relacionamento não é ideal.
Em Julia, podemos definir esse tipo de dado estruturado com um `struct` (também conhecido como tipo composto).
Dentro de cada `struct`, você pode especificar um conjunto de campos.
Eles diferem dos tipos primitivos (por exemplo, inteiro e flutuantes) que já são definidos por padrão dentro do núcleo da linguagem Julia.
Já que a maioria dos `struct` são definidos pelo usuário, eles são conhecidos como tipos definidos pelo usuário.

Por exemplo, vamos criar um `struct` para representar linguagens de programação científica em código aberto.
Também definiremos um conjunto de campos junto com os tipos correspondentes dentro do `struct`:

```jl
s = """
    struct Language
        name::String
        title::String
        year_of_birth::Int64
        fast::Bool
    end
    """
sco(s; post=x -> "")
```

Para inspecionar os nomes dos campos, você pode usar o `fieldnames` e passar o `struct` desejado como um argumento:

```jl
sco("fieldnames(Language)")
```

Para usar os `struct`, devemos instanciar instâncias individuais (ou "objetos"), cada um com seus próprios valores específicos para os campos definidos dentro do `struct`.
Vamos instanciar duas instâncias, uma para Julia e outra para Python:

```jl
s = """
    julia = Language("Julia", "Rapidus", 2012, true)
    python = Language("Python", "Letargicus", 1991, false)
    """
sco(s)
```

Algo importante de se notar com os `struct` é que não podemos alterar seus valores, uma vez que são instanciados.
Podemos resolver isso com `mutable struct`.
Além disso, observe que objetos mutáveis geralmente serão mais lentos e mais propensos a erros.
Sempre que possível, faça que tudo seja *imutável*.
Vamos criar uma `mutable struct`.

```jl
s = """
    mutable struct MutableLanguage
        name::String
        title::String
        year_of_birth::Int64
        fast::Bool
    end

    julia_mutable = MutableLanguage("Julia", "Rapidus", 2012, true)
    """
sco(s)
```

Suponha que queremos mudar o título `julia_mutable`.
Agora podemos fazer isso já que `julia_mutable` é um instanciado `mutable struct`:

```jl
s = """
    julia_mutable.title = "Python Obliteratus"

    julia_mutable
    """
sco(s)
```

### Operadores booleanos e comparações numéricas

Agora que cobrimos os tipos, podemos passar para os operadores booleanos e a comparação numérica.

Nós temos três operadores booleanos em Julia:

* `!`: **NOT**
* `&&`: **AND**
* `||`: **OR**

Aqui estão exemplos com alguns deles:

```jl
scob("!true")
```

```jl
scob("(false && true) || (!false)")
```

```jl
scob("(6 isa Int64) && (6 isa Real)")
```

Com relação à comparação numérica, Julia tem três tipos principais de comparações:

1. **Igualdade**: ou algo é *igual* ou *não igual* em relação a outro
    * == "igual"
    * != ou ≠ "não igual"
1. **Menos que**: ou algo é *menos que* ou *menos que ou igual a*
    * <  "menos que"
    * <= ou ≤ "menos que ou igual a"
1. **Maior que**: ou algo é *maior que* ou *maior que ou igual a*
    * \> "maior que"
    * \>= ou ≥ "maior que ou igual a"

Aqui temos alguns exemplos:

```jl
scob("1 == 1")
```

```jl
scob("1 >= 10")
```

Equilibra os trabalhos entre diferentes tipos:

```jl
scob("1 == 1.0")
```

Também podemos misturar e combinar operadores booleanos com comparações numéricas:

```jl
scob("(1 != 10) || (3.14 <= 2.71)")
```

### Funções {#sec:function}

Agora que já sabemos como definir variáveis e tipos personalizados como `struct`, vamps voltar nossa atenção para as **funções**.
Em Julia, a função **mapeia os valores do argumento para um ou mais valores de retorno**.
A sintaxe básica é assim:

```julia
function function_name(arg1, arg2)
    result = stuff with the arg1 and arg2
    return result
end
```

A declaração da função começa com a palavra-chave `function` seguida do nome da função.
Então, entre parênteses `()`, nós definimos os argumentos separados por uma vírgula `,`.
Dentro da função, especificamos o que queremos que Julia faça com os parâmetros que fornecemos.
Todas as variáveis que definimos dentro de uma função são excluídas após o retorno da função. Isso é bom porque é como se realizasse uma limpeza automática.
Depois que todas as operações no corpo da função forem concluídas, instruímos Julia a retornar o resultado final com a confirmação `return`.
Por fim, informamos a Julia que a definição da função terminou com a palavra-chave `end`.

Existe também o compacto **formulário de atribuição**:

```julia
f_name(arg1, arg2) = stuff with the arg1 and arg2
```

É a **mesma função** que antes, mas com um formulário diferente, mais compacto.
Como regra geral, quando seu código pode caber facilmente em uma linha de até 92 caracteres, a forma compacta é adequada.
Caso contrário, basta usar o formulário mais longo com a palavra-chave `function`.
Vamos mergulhar em alguns exemplos.

#### Criando novas funções {#sec:function_example}

Vamos criar uma nova função que adiciona números:

```jl
s = """
    function add_numbers(x, y)
        return x + y
    end
    """
sco(s)
```

Agora, podemos usar nossa função `add_numbers`:

```jl
scob("add_numbers(17, 29)")
```

And it works also with floats:

```jl
scob("add_numbers(3.14, 2.72)")
```

Além disso, podemos definir o comportamento personalizado, especificando declarações de tipo.
Suponha que queremos ter uma função `round_number` que se comporta de maneira diferente se seu argumento for um `Float64` ou `Int64`:

```jl
s = """
    function round_number(x::Float64)
        return round(x)
    end

    function round_number(x::Int64)
        return x
    end
    """
sco(s)
```

Podemos ver que é uma função com múltiplos métodos:

```jl
sco("methods(round_number)")
```

Existe um problema: o que acontece se quisermos arredondar um float de 32 bits, `Float32`?
Ou um inteiro de 8 bits, `Int8`?

Se você quiser que algo funcione em todos os tipos de float e inteiros, voc~e pode usar um **tipo abstrato** como a assinatura de tipo `AbstractFloat` ou `Integer`:

```jl
s = """
    function round_number(x::AbstractFloat)
        return round(x)
    end
    """
sco(s)
```

Agora, funcionará como esperado em qualquer tipo de float:

```jl
s = """
    x_32 = Float32(1.1)
    round_number(x_32)
    """
scob(s)
```

> **_NOTE:_**
> Podemos inspecionar tipos com as funções `supertypes` e `subtypes`.

Vamos voltar ao nosso `Language` `struct` que definimos anteriormente.
Isso é um exemplode despacho múltiplo.
Vamos estender a função `Base.show` que imprime a saída de tipos instanciados e de `struct`.

Por padrão, uma `struct` tem um output básico, que você pode observar do caso de `python`.
Podemos definir um nove método `Base.show` para nosso tipo `Language`, assim temos uma boa impressão para nossas instâncias de linguagens de programação.
Queremos comunicar claramente os nomes, títulos e idades das linguagens de programação em anos.
A função `Base.show` aceita como argumentos o tipo `IO` chamado `io` seguido pelo tipo que você deseja para definir o comportamento personalizado:

```jl
s = """
    Base.show(io::IO, l::Language) = print(
        io, l.name, " ",
        2021 - l.year_of_birth, ", years old, ",
        "has the following titles: ", l.title
    )
    """
sco(s; post=x -> "")
```

Agora, vamos ver como o output `python` será:

```jl
sco("python")
```

#### Valores de Retorno Múltiplo {#sec:function_multiple}

Uma função também pode retornar dois ou mais valores.
Veja a nova função `add_multiply` abaixo:

```jl
s = """
    function add_multiply(x, y)
        addition = x + y
        multiplication = x * y
        return addition, multiplication
    end
    """
sco(s)
```

In that case, we can do two things:

1. We can, analogously as the return values, define two variables to hold the function return values, one for each return value:

   ```jl
   s = """
       return_1, return_2 = add_multiply(1, 2)
       return_2
       """
   scob(s)
   ```

2. Or we can define just one variable to hold the function's return values and access them with either `first` or `last`:

   ```jl
   s = """
       all_returns = add_multiply(1, 2)
       last(all_returns)
       """
   scob(s)
   ```

#### Keyword Arguments {#sec:function_keyword_arguments}

Some functions can accept keyword arguments instead of positional arguments.
These arguments are just like regular arguments, except that they are defined after the regular function's arguments and separated by a semicolon `;`.
For example, let's define a `logarithm` function that by default uses base $e$ (2.718281828459045) as a keyword argument.
Note that, here, we are using the abstract type `Real` so that we cover all types derived from `Integer` and `AbstractFloat`, being both themselves subtypes of `Real`:

```jl
scob("AbstractFloat <: Real && Integer <: Real")
```

```jl
s = """
    function logarithm(x::Real; base::Real=2.7182818284590)
        return log(base, x)
    end
    """
sco(s)
```

It works without specifying the `base` argument as we supplied a **default argument value** in the function declaration:

```jl
scob("logarithm(10)")
```

And also with the keyword argument `base` different from its default value:

```jl
s = """
    logarithm(10; base=2)
    """
scob(s)
```

#### Anonymous Functions {#sec:function_anonymous}

Often we don't care about the name of the function and want to quickly make one.
What we need are **anonymous functions**.
They are used a lot in Julia's data science workflow.
For example, when using `DataFrames.jl` (@sec:dataframes) or `Makie.jl` (@sec:DataVisualizationMakie), sometimes we need a temporary function to filter data or format plot labels.
That's when we use anonymous functions.
They are especially useful when we don't want to create a function, and a simple in-place statement would be enough.

The syntax is simple.
We use the `->` operator.
On the left of `->` we define the parameter name.
And on the right of `->` we define what operations we want to perform on the parameter that we defined on the left of `->`.
Here is an example.
Suppose that we want to undo the log transformation by using an exponentiation:

```jl
scob("map(x -> 2.7182818284590^x, logarithm(2))")
```

Here, we are using the `map` function to conveniently map the anonymous function (first argument) to `logarithm(2)` (the second argument).
As a result, we get back the same number, because logarithm and exponentiation are inverse (at least in the base that we've chosen -- 2.7182818284590)

### Conditional If-Else-Elseif {#sec:conditionals}

In most programming languages, the user is allowed to control the computer's flow of execution.
Depending on the situation, we want the computer to do one thing or another.
In Julia we can control the flow of execution with `if`, `elseif`, and `else` keywords.
These are known as conditional statements.

The `if` keyword prompts Julia to evaluate an expression and, depending on whether it's `true` or `false`, execute certain portions of code.
We can compound several `if` conditions with the `elseif` keyword for complex control flow.
Finally, we can define an alternative portion to be executed if anything inside the `if` or `elseif`s is evaluated to `true`.
This is the purpose of the `else` keyword.
Finally, like all the previous keyword operators that we saw, we must tell Julia when the conditional statement is finished with the `end` keyword.

Here's an example with all the `if`-`elseif`-`else` keywords:

```jl
s = """
    a = 1
    b = 2

    if a < b
        "a is less than b"
    elseif a > b
        "a is greater than b"
    else
        "a is equal to b"
    end
    """
scob(s)
```

We can even wrap this in a function called `compare`:

```jl
s = """
    function compare(a, b)
        if a < b
            "a is less than b"
        elseif a > b
            "a is greater than b"
        else
            "a is equal to b"
        end
    end

    compare(3.14, 3.14)
    """
sco(s)
```


### For Loop {#sec:for}

The classical for loop in Julia follows a similar syntax as the conditional statements.
You begin with a keyword, in this case `for`.
Then, you specify what Julia should "loop" for, i.e., a sequence.
Also, like everything else, you must finish with the `end` keyword.

So, to make Julia print every number from 1 to 10, you can use the following for loop:

```jl
s = """
    for i in 1:10
        println(i)
    end
    """
sco(s; post=x -> "")
```

### While Loop {#sec:while}

The while loop is a mix of the previous conditional statements and for loops.
Here, the loop is executed every time the condition is `true`.
The syntax follows the same form as the previous one.
We begin with the keyword `while`, followed by a statement that evaluates to `true` or `false`.
As usual, you must end with the `end` keyword.

Here's an example:

```jl
s = """
    n = 0

    while n < 3
        global n += 1
    end

    n
    """
scob(s)
```

As you can see, we have to use the `global` keyword.
This is because of **variable scope**.
Variables defined inside conditional statements, loops, and functions exist only inside them.
This is known as the *scope* of the variable.
Here, we had to tell Julia that the `n` inside `while` loop is in the global scope with the `global` keyword.

Finally, we also used the `+=` operator which is a nice shorthand for `n = n + 1`.

## Native Data Structures {#sec:data_structures}

Julia has several native data structures.
They are abstractions of data that represent some form of structured data.
We will cover the most used ones.
They hold homogeneous or heterogeneous data.
Since they are collections, they can be *looped* over with the `for` loops.

We will cover `String`, `Tuple`, `NamedTuple`, `UnitRange`, `Arrays`, `Pair`, `Dict`, `Symbol`.

When you stumble across a data structure in Julia, you can find methods that accept it as an argument with the `methodswith` function.
In Julia, the distinction between methods and functions is as follows.
Every function can have multiple methods like we have shown earlier.
The `methodswith` function is nice to have in your bag of tricks.
Let's see what we can do with a `String` for example:

```jl
s = "first(methodswith(String), 5)"
sco(s; process=catch_show)
```

### Broadcasting Operators and Functions {#sec:broadcasting}

Before we dive into data structures, we need to talk about broadcasting (also known as *vectorization*) and the "dot" operator `.`.

We can broadcast mathematical operations like `*` (multiplication) or `+` (addition) using the dot operator.
For example, broadcasted addition would imply a change from `+` to `.+`:

```jl
sco(
"""
[1, 2, 3] .+ 1
"""
)
```

It also works automatically with functions.
(Technically, the mathematical operations, or infix operators, are also functions, but that is not so important to know.)
Remember our `logarithm` function?

```jl
sco("logarithm.([1, 2, 3])")
```

#### Functions with a bang `!` {#sec:function_bang}

It is a Julia convention to append a bang `!` to names of functions that modify one or more of their arguments.
This convention warns the user that the function is **not pure**, i.e., that it has *side effects*.
A function with side effects is useful when you want to update a large data structure or variable container without having all the overhead from creating a new instance.

For example, we can create a function that adds 1 to each element in a vector `V`:

```jl
s = """
    function add_one!(V)
        for i in 1:length(V)
            V[i] += 1
        end
        return nothing
    end
    """
sc(s)
```

```jl
s = """
    my_data = [1, 2, 3]

    add_one!(my_data)

    my_data
    """
sco(s)
```

### String {#sec:string}

**Strings** are represented delimited by double quotes:

```jl
s = """
    typeof("This is a string")
    """
sco(s)
```

We can also write a multiline string:

```jl
s = """
    text = "
    This is a big multiline string.
    As you can see.
    It is still a String to Julia.
    "
    """
sco(s; post=output_block)
```

But it is usually clearer to use triple quotation marks:

```jl
sco("""
s = \"\"\"
    This is a big multiline string with a nested "quotation".
    As you can see.
    It is still a String to Julia.
    \"\"\"
"""; post=output_block)
```

When using triple-backticks, the indentation and newline at the start is ignored by Julia.
This improves code readability because you can indent the block in your source code without those spaces ending up in your string.

#### String Concatenation {#sec:string_concatenation}

A common string operation is **string concatenation**.
Suppose that you want to construct a new string that is the concatenation of two or more strings.
This is accomplished in Julia either with the `*` operator or the `join` function.
This symbol might sound like a weird choice and it actually is.
For now, many Julia codebases are using this symbol, so it will stay in the language.
If you're interested, you can read a discussion from 2015 about it at
<https://github.com/JuliaLang/julia/issues/11030>.

```jl
s = """
    hello = "Hello"
    goodbye = "Goodbye"

    hello * goodbye
    """
scob(s)
```

As you can see, we are missing a space between `hello` and `goodbye`.
We could concatenate an additional `" "` string with the `*`, but that would be cumbersome for more than two strings.
That's where the `join` function comes in handy.
We just pass as arguments the strings inside the brackets `[]` and the separator:

```jl
scob("""join([hello, goodbye], " ")""")
```

#### String Interpolation {#sec:string_interpolation}

Concatenating strings can be convoluted.
We can be much more expressive with **string interpolation**.
It works like this: you specify whatever you want to be included in your string with the dollar sign `$`.
Here's the example before but now using interpolation:

```jl
s = """
    "\$hello \$goodbye"
    """
scob(s)
```

It even works inside functions.
Let's revisit our `test` function from @sec:conditionals:

```jl
s = """
    function test_interpolated(a, b)
        if a < b
            "\$a is less than \$b"
        elseif a > b
            "\$a is greater than \$b"
        else
            "\$a is equal to \$b"
        end
    end

    test_interpolated(3.14, 3.14)
    """
scob(s)
```

#### String Manipulations {#sec:string_manipulations}

There are several functions to manipulate strings in Julia.
We will demonstrate the most common ones.
Also, note that most of these functions accept a [Regular Expression (RegEx)](https://docs.julialang.org/en/v1/manual/strings/#Regular-Expressions) as arguments.
We won't cover RegEx in this book, but you are encouraged to learn about them, especially if most of your work uses textual data.

First, let us define a string for us to play around with:

```jl
s = """
    julia_string = "Julia is an amazing opensource programming language"
    """
scob(s)
```


1. `occursin`, `startswith` and `endswith`: A conditional (returns either `true` or `false`) if the first argument is a:
    * **substring** of the second argument

       ```jl
       scob("""occursin("Julia", julia_string)""")
       ```

    * **prefix** of the second argument

       ```jl
       scob("""startswith("Julia", julia_string)""")
       ```

    * **suffix** of the second argument

       ```jl
       scob("""endswith("Julia", julia_string)""")
       ```

2. `lowercase`, `uppercase`, `titlecase` and `lowercasefirst`:

     ```jl
     scob("lowercase(julia_string)")
     ```

     ```jl
     scob("uppercase(julia_string)")
     ```

     ```jl
     scob("titlecase(julia_string)")
     ```

     ```jl
     scob("lowercasefirst(julia_string)")
     ```

3. `replace`: introduces a new syntax, called the `Pair`

     ```jl
     scob("""replace(julia_string, "amazing" => "awesome")""")
     ```

4. `split`: breaks up a string by a delimiter:

     ```jl
     sco("""split(julia_string, " ")""")
     ```

#### String Conversions {#sec:string_conversions}

Often, we need to **convert** between types in Julia.
To convert a number to a string we can use the `string` function:

```jl
s = """
    my_number = 123
    typeof(string(my_number))
    """
sco(s)
```

Sometimes, we want the opposite: convert a string to a number.
Julia has a handy function for that: `parse`.

```jl
sco("""typeof(parse(Int64, "123"))""")
```

Sometimes, we want to play safe with these conversions.
That's when `tryparse` function steps in.
It has the same functionality as `parse` but returns either a value of the requested type, or `nothing`.
That makes `tryparse` handy when we want to avoid errors.
Of course, you would need to deal with all those `nothing` values afterwards.

```jl
sco("""tryparse(Int64, "A very non-numeric string")""")
```

### Tuple {#sec:tuple}

Julia has a data structure called **tuple**.
They are really *special* in Julia because they are often used in relation to functions.
Since functions are a important feature in Julia, every Julia user should know the basics of tuples.

A tuple is a **fixed-length container that can hold multiple different types**.
A tuple is an **immutable object**, meaning that it cannot be modified after instantiation.
To construct a tuple, use parentheses `()` to delimit the beginning and end, along with commas `,` as delimiters between values:

```jl
sco("""my_tuple = (1, 3.14, "Julia")""")
```

Here, we are creating a tuple with three values.
Each one of the values is a different type.
We can access them via indexing.
Like this:

```jl
scob("my_tuple[2]")
```

We can also loop over tuples with the `for` keyword.
And even apply functions to tuples.
But we can **never change any value of a tuple** since they are **immutable**.

Remember functions that return multiple values back in @sec:function_multiple?
Let's inspect what our `add_multiply` function returns:

```jl
s = """
    return_multiple = add_multiply(1, 2)
    typeof(return_multiple)
    """
sco(s)
```

This is because `return a, b` is the same as `return (a, b)`:

```jl
sco("1, 2")
```

So, now you can see why they are often related.

One more thing about tuples.
**When you want to pass more than one variable to an anonymous function, guess what you would need to use? Once again: tuples!**

```jl
scob("map((x, y) -> x^y, 2, 3)")
```

Or, even more than two arguments:

```jl
scob("map((x, y, z) -> x^y + z, 2, 3, 1)")
```

### Named Tuple {#sec:namedtuple}

Sometimes, you want to name the values in tuples.
That's when **named tuples** comes in.
Their functionality is pretty much same as tuples:
they are **immutable** and can hold **any type of value**.

The construction of named tuples is slightly different from that of tuples.
You have the familiar parentheses `()` and the comma `,` value separator.
But now you **name the values**:

```jl
sco("""my_namedtuple = (i=1, f=3.14, s="Julia")""")
```

We can access a named tuple's values via indexing like regular tuples or, alternatively, **access by their names** with the `.`:

```jl
scob("my_namedtuple.s")
```

To finish our discussion of named tuples, there is one important *quick* syntax that you'll see a lot in Julia code.
Often Julia users create a named tuple by using the familiar parenthesis `()` and commas `,`, but without naming the values.
To do so you **begin the named tuple construction by specifying first a semicolon `;` before the values**.
This is especially useful when the values that would compose the named tuple are already defined in variables or when you want to avoid long lines:

```jl
s = """
    i = 1
    f = 3.14
    s = "Julia"

    my_quick_namedtuple = (; i, f, s)
    """
sco(s)
```

### Ranges {#sec:ranges}

A **range** in Julia represents an interval between start and stop boundaries.
The syntax is `start:stop`:

```jl
sco("1:10")
```

As you can see, our instantiated range is of type `UnitRange{T}` where `T` is the type inside the `UnitRange`:

```jl
sco("typeof(1:10)")
```

And, if we gather all the values, we get:

```jl
sco("[x for x in 1:10]")
```

We can also construct ranges for other types:

```jl
sco("typeof(1.0:10.0)")
```

Sometimes, we want to change the default interval stepsize behavior.
We can do that by adding a stepsize in the range syntax `start:step:stop`.
For example, suppose we want a range of `Float64` from 0 to 1 with steps of size 0.2:

```jl
sco("0.0:0.2:1.0")
```

If you want to "materialize" a range into a collection, you can use the function `collect`:

```jl
sco("collect(1:10)")
```

We have an array of the type specified in the range between the boundaries that we've set.
Speaking of arrays, let's talk about them.

### Array {#sec:array}

In its most basic form, **array**s hold multiple objects.
For example, they can hold multiple numbers in one-dimension:

```jl
sco("myarray = [1, 2, 3]")
```

Most of the time you would want **arrays of a single type for performance issues**, but note that they can also hold objects of different types:

```jl
sco("myarray = [\"text\", 1, :symbol]"; process=output_block)
```

They are the "bread and butter" of data scientist, because arrays are what underlies most of **data manipulation** and **data visualization** workflows.

Therefore, **Arrays are an essential data structure**.

#### Array Types {#sec:array_types}

Let's start with **array types**.
There are several, but we will focus on the two most used in data science:

* `Vector{T}`: **one-dimensional** array. Alias for `Array{T, 1}`.
* `Matrix{T}`: **two-dimensional** array. Alias for `Array{T, 2}`.

Note here that `T` is the type of the underlying array.
So, for example, `Vector{Int64}` is a `Vector` in which all elements are `Int64`s, and `Matrix{AbstractFloat}` is a `Matrix` in which all elements are subtypes of `AbstractFloat`.

Most of the time, especially when dealing with tabular data, we are using either one- or two-dimensional arrays.
They are both `Array` types for Julia.
But, we can use the handy aliases `Vector` and `Matrix` for clear and concise syntax.

#### Array Construction {#sec:array_construction}

How do we **construct** an array?
In this section, we start by constructing arrays in a low-level way.
This can be necessary to write high performing code in some situations.
However, in most situations, this is not necessary, and we can safely use more convenient methods to create arrays.
These more convenient methods will be described later in this section.

The low-level constructor for Julia arrays is the **default constructor**.
It accepts the element type as the type parameter inside the `{}` brackets and inside the constructor you'll pass the element type followed by the desired dimensions.
It is common to initialize vector and matrices with undefined elements by using the `undef` argument for type.
A vector of 10 `undef` `Float64` elements can be constructed as:

```jl
s = """
    my_vector = Vector{Float64}(undef, 10)
    """
sco(s)
```

For matrices, since we are dealing with two-dimensional objects, we need to pass two dimension arguments inside the constructor: one for **rows** and another for **columns**.
For example, a matrix with 10 rows and 2 columns of `undef` elements can be instantiated as:

```jl
s = """
    my_matrix = Matrix{Float64}(undef, 10, 2)
    """
sco(s)
```

We also have some **syntax aliases** for the most common elements in array construction:

* `zeros` for all elements being initialized to zero.
  Note that the default type is `Float64` which can be changed if necessary:

     ```jl
     s = """
         my_vector_zeros = zeros(10)
         """
     sco(s)
     ```

     ```jl
     s = """
         my_matrix_zeros = zeros(Int64, 10, 2)
         """
     sco(s)
     ```

* `ones` for all elements being initialized to one:

     ```jl
     s = """
         my_vector_ones = ones(Int64, 10)
         """
     sco(s)
     ```

     ```jl
     s = """
         my_matrix_ones = ones(10, 2)
         """
     sco(s)
     ```

For other elements, we can first instantiate an array with `undef` elements and use the `fill!` function to fill all elements of an array with the desired element.
Here's an example with `3.14` ($\pi$):

```jl
s = """
    my_matrix_π = Matrix{Float64}(undef, 2, 2)
    fill!(my_matrix_π, 3.14)
    """
sco(s)
```

We can also create arrays with **array literals**.
For example, here's a 2x2 matrix of integers:

```jl
s = """
    [[1 2]
     [3 4]]
    """
sco(s)
```

Array literals also accept a type specification before the `[]` brackets.
So, if we want the same 2x2 array as before but now as floats, we can do so:

```jl
s = """
    Float64[[1 2]
            [3 4]]
    """
sco(s)
```

It also works for vectors:

```jl
s = """
    Bool[0, 1, 0, 1]
    """
sco(s)
```

You can even **mix and match** array literals with the constructors:

```jl
s = """
    [ones(Int, 2, 2) zeros(Int, 2, 2)]
    """
sco(s)
```

```jl
s = """
    [zeros(Int, 2, 2)
     ones(Int, 2, 2)]
    """
sco(s)
```


```jl
s = """
    [ones(Int, 2, 2) [1; 2]
     [3 4]            5]
    """
sco(s)
```

Another powerful way to create an array is to write an **array comprehension**.
This way of creating arrays is better in most cases: it avoids loops, indexing, and other error-prone operations.
You specify what you want to do inside the `[]` brackets.
For example, say we want to create a vector of squares from 1 to 10:

```jl
s = """
    [x^2 for x in 1:10]
    """
sco(s)
```

They also support multiple inputs:

```jl
s = """
    [x*y for x in 1:10 for y in 1:2]
    """
sco(s)
```

And conditionals:

```jl
s = """
    [x^2 for x in 1:10 if isodd(x)]
    """
sco(s)
```

As with array literals, you can specify your desired type before the `[]` brackets:

```jl
s = """
    Float64[x^2 for x in 1:10 if isodd(x)]
    """
sco(s)
```

Finally, we can also create arrays with **concatenation functions**.
Concatenation is a standard term in computer programming and means "to chain together".
For example, we can concatenate strings with "aa" and "bb" to get "aabb":

```jl
s = """
    "aa" * "bb"
    """
sco(s)
```

And, we can concatenate arrays to create new arrays:

* `cat`: concatenate input arrays along a specific dimension `dims`

     ```jl
     sco("cat(ones(2), zeros(2), dims=1)")
     ```

     ```jl
     sco("cat(ones(2), zeros(2), dims=2)")
     ```

* `vcat`: vertical concatenation, a shorthand for `cat(...; dims=1)`

     ```jl
     sco("vcat(ones(2), zeros(2))")
     ```

* `hcat`: horizontal concatenation, a shorthand for `cat(...; dims=2)`

     ```jl
     sco("hcat(ones(2), zeros(2))")
     ```

#### Array Inspection {#sec:array_inspection}

Once we have arrays, the next logical step is to **inspect** them.
There are a lot of handy functions that allow the user to have an insight into any array.

It is most useful to know what **type of elements** are inside an array.
We can do this with `eltype`:

```jl
sco("eltype(my_matrix_π)")
```

After knowing its types, one might be interested in **array dimensions**.
Julia has several functions to inspect array dimensions:

* `length`: total number of elements

     ```jl
     scob("length(my_matrix_π)")
     ```

* `ndims`: number of dimensions

     ```jl
     scob("ndims(my_matrix_π)")
     ```

* `size`: this one is a little tricky.
    By default it will return a tuple containing the array's dimensions.

     ```jl
     sco("size(my_matrix_π)")
     ```

    You can get a specific dimension with a second argument to `size`.
    Here, the the second axis is columns

     ```jl
     scob("size(my_matrix_π, 2)")
     ```

#### Array Indexing and Slicing {#sec:array_indexing}

Sometimes, we want to inspect only certain parts of an array.
This is called **indexing** and **slicing**.
If you want a particular observation of a vector, or a row or column of a matrix, you'll probably need to **index an array**.

First, I will create an example vector and matrix to play around:

```jl
s = """
    my_example_vector = [1, 2, 3, 4, 5]

    my_example_matrix = [[1 2 3]
                         [4 5 6]
                         [7 8 9]]
    """
sc(s)
```

Let's start with vectors.
Suppose that you want the second element of a vector.
You append `[]` brackets with the desired **index** inside:

```jl
scob("my_example_vector[2]")
```

The same syntax follows with matrices.
But, since matrices are 2-dimensional arrays, we have to specify *both* rows and columns.
Let's retrieve the element from the second row (first dimension) and first column (second dimension):

```jl
scob("my_example_matrix[2, 1]")
```

Julia also has conventional keywords for the **first** and **last** elements of an array: `begin` and `end`.
For example, the second to last element of a vector can be retrieved as:

```jl
scob("my_example_vector[end-1]")
```

This also works for matrices.
Let's retrieve the element of the last row and second column:

```jl
scob("my_example_matrix[end, begin+1]")
```

Often, we are not only interested in just one array element, but in a whole **subset of array elements**.
We can accomplish this by **slicing** an array.
It uses the same index syntax, but with the added colon `:` to denote the boundaries that we are slicing through the array.
For example, suppose we want to get the 2nd to 4th element of a vector:

```jl
sco("my_example_vector[2:4]")
```

We could do the same with matrices.
Particularly with matrices if we want to select **all elements** in a following dimension we can do so with just a colon `:`.
For example, to get all the elements in the second row:

```jl
sco("my_example_matrix[2, :]")
```

You can interpret this with something like "take the 2nd row and all the columns".

It also supports `begin` and `end`:

```jl
sco("my_example_matrix[begin+1:end, end]")
```

#### Array Manipulations {#sec:array_manipulation}

There are several ways we could **manipulate** an array.
The first would be to manipulate a **singular element of the array**.
We just index the array by the desired element and proceed with an assignment `=`:

```jl
s = """
    my_example_matrix[2, 2] = 42
    my_example_matrix
    """
sco(s)
```

Or, you can manipulate a certain **subset of elements of the array**.
In this case, we need to slice the array and then assign with `=`:

```jl
s = """
    my_example_matrix[3, :] = [17, 16, 15]
    my_example_matrix
    """
sco(s)
```

Note that we had to assign a vector because our sliced array is of type `Vector`:

```jl
s = """
    typeof(my_example_matrix[3, :])
    """
sco(s)
```

The second way we could manipulate an array is to **alter its shape**.
Suppose that you have a 6-element vector and you want to make it a 3x2 matrix.
You can do this with `reshape`, by using the array as the first argument and a tuple of dimensions as the second argument:

```jl
s = """
    six_vector = [1, 2, 3, 4, 5, 6]
    tree_two_matrix = reshape(six_vector, (3, 2))
    tree_two_matrix
    """
sco(s)
```

You can convert it back to a vector by specifying a tuple with only one dimension as the second argument:

```jl
sco("reshape(tree_two_matrix, (6, ))")
```

The third way we could manipulate an array is to **apply a function over every array element**.
This is where the "dot" operator `.`, also known as _broadcasting_, comes in.

```jl
sco("logarithm.(my_example_matrix)")
```

The dot operator in Julia is extremely versatile.
You can even use it to broadcast infix operators:

```jl
sco("my_example_matrix .+ 100")
```

An alternative to broadcasting a function over a vector is to use `map`:

```jl
sco("map(logarithm, my_example_matrix)")
```

For anonymous functions, `map` is usually more readable.
For example,

```jl
sco("map(x -> 3x, my_example_matrix)")
```

is quite clear.
However, the same broadcast looks as follows:

```jl
sco("(x -> 3x).(my_example_matrix)")
```

Next, `map` works with slicing:

```jl
sco("map(x -> x + 100, my_example_matrix[:, 3])")
```

Finally, sometimes, and specially when dealing with tabular data, we want to apply a **function over all elements in a specific array dimension**.
This can be done with the `mapslices` function.
Similar to `map`, the first argument is the function and the second argument is the array.
The only change is that we need to specify the `dims` argument to flag what dimension we want to transform the elements.

For example, let's use `mapslice` with the `sum` function on both rows (`dims=1`) and columns (`dims=2`):

```jl
sco(
"""
# rows
mapslices(sum, my_example_matrix; dims=1)
"""
)
```

```jl
sco(
"""
# columns
mapslices(sum, my_example_matrix; dims=2)
"""
)
```

#### Array Iteration {#sec:array_iteration}

One common operation is to **iterate over an array with a `for` loop**.
The **regular `for` loop over an array returns each element**.

The simplest example is with a vector.

```jl
sco(
"""
simple_vector = [1, 2, 3]

empty_vector = Int64[]

for i in simple_vector
    push!(empty_vector, i + 1)
end

empty_vector
"""
)
```

Sometimes, you don't want to loop over each element, but actually over each array index.
**We can use the `eachindex` function combined with a `for` loop to iterate over each array index**.

Again, let's show an example with a vector:

```jl
sco(
"""
forty_twos = [42, 42, 42]

empty_vector = Int64[]

for i in eachindex(forty_twos)
    push!(empty_vector, i)
end

empty_vector
"""
)
```

In this example, the `eachindex(forty_twos)` returns the indices of `forty_twos`, namely `[1, 2, 3]`.

Similarly, we can iterate over matrices.
The standard `for` loop goes first over columns then over rows.
It will first traverse all elements in column 1, from the first row to the last row, then it will move to column 2 in a similar fashion until it has covered all columns.

For those familiar with other programming languages:
Julia, like most scientific programming languages, is "column-major".
Column-major means that the elements in the column are stored next to each other in memory[^pointers].
This also means that iterating over elements in a column is much quicker than over elements in a row.

[^pointers]: or, that the memory address pointers to the elements in the column are stored next to each other

Ok, let's show this in an example:

```jl
sc(
"""
column_major = [[1 3]
                [2 4]]

row_major = [[1 2]
             [3 4]]
"""
)
```

If we loop over the vector stored in column-major order, then the output is sorted:

```jl
sco(
"""
indexes = Int64[]

for i in column_major
    push!(indexes, i)
end

indexes
"""
)
```

However, the output isn't sorted when looping over the other matrix:

```jl
sco(
"""
indexes = Int64[]

for i in row_major
    push!(indexes, i)
end

indexes
"""
)
```

It is often better to use specialized functions for these loops:

* `eachcol`: iterates over an array column first

     ```jl
     sco("first(eachcol(column_major))")
     ```

* `eachrow`: iterates over an array row first

     ```jl
     sco("first(eachrow(column_major))")
     ```

### Pair {#sec:pair}

Compared to the huge section on arrays, this section on pairs will be brief.
**`Pair` is a data structure that holds two objects** (which typically belong to each other).
We construct a pair in Julia using the following syntax:

```jl
sco("""my_pair = "Julia" => 42""")
```

The elements are stored in the fields `first` and `second`.

```jl
scob("my_pair.first")
```

```jl
scob("my_pair.second")
```

But, in most cases, it's easier use `first` and `last`[^easier]:

```jl
scob("first(my_pair)")
```

```jl
scob("last(my_pair)")
```

[^easier]: it is easier because `first` and `last` also work on many other collections, so you need to remember less.

Pairs will be used a lot in data manipulation and data visualization since both `DataFrames.jl` (@sec:dataframes) or `Makie.jl` (@sec:DataVisualizationMakie) take objects of type `Pair` in their main functions.
For example, with `DataFrames.jl` we're going to see that `:a => :b` can be used to rename the column `:a` to `:b`.

### Dict {#sec:dict}

If you understood what a `Pair` is, then `Dict` won't be a problem.
For all practical purposes, **`Dict`s are mappings from keys to values**.
By mapping, we mean that if you give a `Dict` some key, then the `Dict` can tell you which value belongs to that key.
`key`s and `value`s can be of any type, but usually `key`s are strings.

There are two ways to construct `Dict`s in Julia.
The first is by passing a vector of tuples as `(key, value)` to the `Dict` constructor:

```jl
sco(
"""
# tuples # hide
name2number_map = Dict([("one", 1), ("two", 2)])
"""
)
```

There is a more readable syntax based on the `Pair` type described above.
You can also pass `Pair`s of `key => value`s to the `Dict` constructor:

```jl
sco(
"""
# pairs # hide
name2number_map = Dict("one" => 1, "two" => 2)
"""
)
```

You can retrieve a `Dict`s `value` by indexing it by the corresponding `key`:

```jl
scob("""name2number_map["one"]""")
```

To add a new entry, you index the `Dict` by the desired `key` and assign a `value` with the assignment `=` operator:

```jl
scob(
"""
name2number_map["three"] = 3
"""
)
```

If you want to check if a `Dict` has a certain `key` you can use `keys` and `in`:

```jl
scob("\"two\" in keys(name2number_map)")
```

To delete a `key` you can use either the `delete!` function:

```jl
sco(
"""
delete!(name2number_map, "three")
"""
)
```

Or, to delete a key while returning its value, you can use `pop!`:

```jl
scob("""popped_value = pop!(name2number_map, "two")""")
```

Now, our `name2number_map` has only one `key`:

```jl
sco("name2number_map")
```

`Dict`s are also used for data manipulation by `DataFrames.jl` (@sec:dataframes) and for data visualization by `Makie.jl` (@sec:DataVisualizationMakie).
So, it is important to know their basic functionality.

There is another useful way of constructing `Dict`s.
Suppose that you have two vectors and you want to construct a `Dict` with one of them as `key`s and the other as `value`s.
You can do that with the `zip` function which "glues" together two objects (just like a zipper):

```jl
sco(
"""
A = ["one", "two", "three"]
B = [1, 2, 3]

name2number_map = Dict(zip(A, B))
"""
)
```

For instance, we can now get the number 3 via:

```jl
scob("""name2number_map["three"]""")
```

### Symbol {#sec:symbol}

`Symbol` is actually *not* a data structure.
It is a type and behaves a lot like a string.
Instead of surrounding the text by quotation marks, a symbol starts with a colon (:) and can contain underscores:

```jl
sco("sym = :some_text")
```

We can easily convert a symbol to string and vice versa:

```jl
scob("s = string(sym)")
```

```jl
sco("sym = Symbol(s)")
```

One simple benefit of symbols is that you have to type one character less, that is, `:some_text` versus `"some text"`.
We use `Symbol`s a lot in data manipulations with the `DataFrames.jl` package (@sec:dataframes) and data visualizations with the `Makie.jl` package (@sec:DataVisualizationMakie).

### Splat Operator {#sec:splat}

In Julia we have the "splat" operator `...` which is used in function calls as a **sequence of arguments**.
We will occasionally use splatting in some function calls in the **data manipulation** and **data visualization** chapters.

The most intuitive way to learn about splatting is with an example.
The `add_elements` function below takes three arguments to be added together:

```jl
sco("add_elements(a, b, c) = a + b + c")
```

Now, suppose that we have a collection with three elements.
The naïve way to this would be to supply the function with all three elements as function arguments like this:

```jl
scob("""
my_collection = [1, 2, 3]

add_elements(my_collection[1], my_collection[2], my_collection[3])
""")
```

Here is where we use the "splat" operator `...` which takes a collection (often an array, vector, tuple, or range) and converts it into a sequence of arguments:

```jl
scob("add_elements(my_collection...)")
```

The `...` is included after the collection that we want to "splat" into a sequence of arguments.
In the example above, the following are the same:

```jl
scob("""
add_elements(my_collection...) == add_elements(my_collection[1], my_collection[2], my_collection[3])
""")
```

Anytime Julia sees a splatting operator inside a function call, it will be converted on a sequence of arguments for all elements of the collection separated by commas.

It also works for ranges:

```jl
scob("add_elements(1:3...)")
```

## Filesystem {#sec:filesystem}

In data science, most projects are undertaken in a collaborative effort.
We share code, data, tables, figures and so on.
Behind everything, there is the **operating system (OS) filesystem**.
In a perfect world, the same program would give the **same** output when running on **different** operating systems.
Unfortunately, that is not always the case.
One instance of this is the difference between Windows paths, such as `C:\\user\john\`, and Linux paths, such as `/home/john`.
This is why it is important to discuss **filesystem best practices**.

Julia has native filesystem capabilities that **handle the differences between operating systems**.
They are located in the [`Filesystem`](https://docs.julialang.org/en/v1/base/file/) module from the core `Base` Julia library.

Whenever you are dealing with files such as CSV, Excel files or other Julia scripts, make sure that your code **works on different OS filesystems**.
This is easily accomplished with the `joinpath`, `@__FILE__` and `pkgdir` functions.

If you write your code in a package, you can use `pkgdir` to get the root directory of the package.
For example, for the Julia Data Science (JDS) package that we use to produce this book:

```jl
root = pkgdir(JDS)
```

as you can see, the code to produce this book was running on a Linux computer.
If you're using a script, you can get the location of the script file via

```julia
root = dirname(@__FILE__)
```

The nice thing about these two commands is that they are independent of how the user started Julia.
In other words, it doesn't matter whether the user started the program with `julia scripts/script.jl` or `julia script.jl`, in both cases the paths are the same.

The next step would be to include the relative path from `root` to our desired file.
Since different OS have different ways to construct relative paths with subfolders (some use forward slashes `/` while other might use backslashes `\`), we cannot simply concatenate the  file's relative path with the `root` string.
For that, we have the `joinpath` function, which will join different relative paths and filenames according to your specific OS filesystem implementation.

Suppose that you have a script named `my_script.jl` inside your project's directory.
You can have a robust representation of the filepath to `my_script.jl` as:

```jl
scob("""joinpath(root, "my_script.jl")""")
```

`joinpath` also handles **subfolders**.
Let's now imagine a common situation where you have a folder named `data/` in your project's directory.
Inside this folder there is a CSV file named `my_data.csv`.
You can have the same robust representation of the filepath to `my_data.csv` as:

```jl
scob("""joinpath(root, "data", "my_data.csv")""")
```

It's a good habit to pick up, because it's very likely to save problems for you or other people later.

## Julia Standard Library {#sec:standardlibrary}

Julia has a **rich standard library** that is available with *every* Julia installation.
Contrary to everything that we have seen so far, e.g. types, data structures and filesystem; you **must load standard library modules into your environment** to use a particular module or function.

This is done via `using` or `import`.
In this book, we will load code via `using`:

```julia
using ModuleName
```

After doing this, you can access all functions and types inside `ModuleName`.

### Dates {#sec:dates}

Knowing how to handle dates and timestamps is important in data science.
As we said in *Why Julia?* (@sec:why_julia) section, Python's `pandas` uses its own `datetime` type to handle dates.
The same is true in the R tidyverse's `lubridate` package, which also defines its own `datetime` type to handle dates.
In Julia packages don't need to write their own dates logic, because Julia has a dates module in its standard library called `Dates`.

To begin, let's load the `Dates` module:

```julia
using Dates
```

#### `Date` and `DateTime` Types {#sec:dates_types}

The `Dates` standard library module has **two types for working with dates**:

1. `Date`: representing time in days and
2. `DateTime`: representing time in millisecond precision.

We can construct `Date` and `DateTime` with the default constructor either by specifying an integer to represent year, month, day, hours and so on:

```jl
sco(
"""
Date(1987) # year
"""
)
```

```jl
sco(
"""
Date(1987, 9) # year, month
"""
)
```

```jl
sco(
"""
Date(1987, 9, 13) # year, month, day
"""
)
```

```jl
sco(
"""
DateTime(1987, 9, 13, 21) # year, month, day, hour
"""
)
```

```jl
sco(
"""
DateTime(1987, 9, 13, 21, 21) # year, month, day, hour, minute
"""
)
```

For the curious, September 13th 1987, 21:21 is the official time of birth of the first author, Jose.

We can also pass `Period` types to the default constructor.
**`Period` types are the human-equivalent representation of time** for the computer.
Julia's `Dates` have the following `Period` abstract subtypes:

```jl
sco("subtypes(Period)")
```

which divide into the following concrete types, and they are pretty much self-explanatory:

```jl
sco("subtypes(DatePeriod)")
```

```jl
sco("subtypes(TimePeriod)")
```

So, we could alternatively construct Jose's official time of birth as:

```jl
sco("DateTime(Year(1987), Month(9), Day(13), Hour(21), Minute(21))")
```

#### Parsing Dates {#sec:dates_parsing}

Most of the time, we won't be constructing `Date` or `DateTime` instances from scratch.
Actually, we will probably be **parsing strings as `Date` or `DateTime` types**.

The `Date` and `DateTime` constructors can be fed a string and a format string.
For example, the string `"19870913"` representing September 13th 1987 can be parsed with:

```jl
sco("""Date("19870913", "yyyymmdd")""")
```

Notice that the second argument is a string representation of the format.
We have the first four digits representing year `y`, followed by two digits for month `m` and finally two digits for day `d`.

It also works for timestamps with `DateTime`:

```jl
sco("""DateTime("1987-09-13T21:21:00", "yyyy-mm-ddTHH:MM:SS")""")
```

You can find more on how to specify different date formats in the [Julia `Dates`' documentation](https://docs.julialang.org/en/v1/stdlib/Dates/#Dates.DateFormat).
Don't worry if you have to revisit it all the time, we ourselves do that too when working with dates and timestamps.

According to [Julia `Dates`' documentation](https://docs.julialang.org/en/v1/stdlib/Dates/#Constructors), using the `Date(date_string, format_string)` method is fine if it's only called a few times.
If there are many similarly formatted date strings to parse, however, it is much more efficient to first create a `DateFormat` type, and then pass it instead of a raw format string.
Then, our previous example becomes:

```jl
s = """
    format = DateFormat("yyyymmdd")
    Date("19870913", format)
    """
sco(s)
```

Alternatively, without loss of performance, you can use the string literal prefix `dateformat"..."`:

```jl
sco("""Date("19870913", dateformat"yyyymmdd")""")
```

#### Extracting Date Information {#sec:dates_information}

It is easy to **extract desired information from `Date` and `DateTime` objects**.
First, let's create an instance of a very special date:

```jl
sco("""my_birthday = Date("1987-09-13")""")
```

We can extract anything we want from `my_birthday`:

```jl
scob("year(my_birthday)")
```

```jl
scob("month(my_birthday)")
```

```jl
scob("day(my_birthday)")
```

Julia's `Dates` module also has **compound functions that return a tuple of values**:

```jl
sco("yearmonth(my_birthday)")
```

```jl
sco("monthday(my_birthday)")
```

```jl
sco("yearmonthday(my_birthday)")
```

We can also see the day of the week and other handy stuff:

```jl
scob("dayofweek(my_birthday)")
```

```jl
scob("dayname(my_birthday)")
```

```jl
scob("dayofweekofmonth(my_birthday)")
```

Yep, Jose was born on the second Sunday of September.

> **_NOTE:_**
> Here's a handy tip to just recover weekdays from `Dates` instances.
> Just use a `filter` on `dayofweek(your_date) <= 5`.
> For business day you can checkout the [`BusinessDays.jl`](https://github.com/JuliaFinance/BusinessDays.jl) package.

#### Date Operations {#sec:dates_operations}

We can perform **operations** in `Dates` instances.
For example, we can add days to a `Date` or `DateTime` instance.
Notice that Julia's `Dates` will automatically perform the adjustments necessary for leap years, and for months with 30 or 31 days (this is known as *calendrical* arithmetic).

```jl
sco("my_birthday + Day(90)")
```

We can add as many as we like:

```jl
sco("my_birthday + Day(90) + Month(2) + Year(1)")
```

In case you're ever wondering: "What can I do with dates again? What is available?", then you can use `methodswith` to check it out.
We show only the first 20 results here:

```jl
s = "first(methodswith(Date), 20)"
sco(s; process=catch_show)
```

From this, we can conclude that we can also use the plus `+` and minus `-` operator.
Let's see how old Jose is, in days:

```jl
sco("today() - my_birthday")
```

The **default duration** of `Date` types is a `Day` instance.
For the `DateTime`, the default duration is `Millisecond` instance:

```jl
sco("DateTime(today()) - DateTime(my_birthday)")
```

#### Date Intervals {#sec:dates_intervals}

One nice thing about `Dates` module is that we can also easily construct **date and time intervals**.
Julia is clever enough to not have to define the whole interval types and operations that we covered in @sec:ranges.
It just extends the functions and operations defined for range to `Date`'s types.
This is known as multiple dispatch and we already covered this in *Why Julia?* (@sec:why_julia).

For example, suppose that you want to create a `Day` interval.
This is easy done with the colon `:` operator:

```jl
sco("""Date("2021-01-01"):Day(1):Date("2021-01-07")""")
```

There is nothing special in using `Day(1)` as the interval, we can **use whatever `Period` type** as interval.
For example, using 3 days as the interval:

```jl
sco("""Date("2021-01-01"):Day(3):Date("2021-01-07")""")
```

Or even months:

```jl
sco("""Date("2021-01-01"):Month(1):Date("2021-03-01")""")
```

Note that the **type of this interval is a `StepRange` with the `Date` and concrete `Period` type** we used as interval inside the colon `:` operator:

```jl
s = """
    date_interval = Date("2021-01-01"):Month(1):Date("2021-03-01")
    typeof(date_interval)
    """
sco(s)
```

We can convert this to a **vector** with the `collect` function:

```jl
sco("collected_date_interval = collect(date_interval)")
```

And have all the **array functionalities available**, like, for example, indexing:

```jl
sco("collected_date_interval[end]")
```

We can also **broadcast date operations** to our vector of `Date`s:

```jl
sco("collected_date_interval .+ Day(10)")
```

Similarly, these examples work for `DateTime` types too.

### Random Numbers {#sec:random}

Another important module in Julia's standard library is the `Random` module.
This module deals with **random number generation**.
`Random` is a rich library and, if you're interested, you should consult [Julia's `Random` documentation](https://docs.julialang.org/en/v1/stdlib/Random/).
We will cover *only* three functions: `rand`, `randn` and `seed!`.

To begin, we first load the `Random` module.
Since we know exactly what we want to load, we can just as well explicitly load the methods that we want to use:

```julia
using Random: rand, randn, seed!
```

We have **two main functions that generate random numbers**:

* `rand`: samples a **random element** of a data structure or type.
* `randn`: generates a random number that follows a **standard normal distribution** (mean 0 and standard deviation 1) of a specific type.

> **_NOTE:_**
> Note that those two functions are already in the Julia `Base` module.
> So, you don't need to import `Random` if you're planning to use them.

#### `rand` {#sec:random_rand}

By default, if you call `rand` without arguments it will return a `Float64` in the interval $[0, 1)$, which means between 0 inclusive to 1 exclusive:

```jl
scob("rand()")
```

You can modify `rand` arguments in several ways.
For example, suppose you want more than 1 random number:

```jl
sco("rand(3)")
```

Or, you want a different interval:

```jl
scob("rand(1.0:10.0)")
```

You can also specify a different step size inside the interval and a different type.
Here we are using numbers without the dot `.` so Julia will interpret them as `Int64`:

```jl
scob("rand(2:2:20)")
```

You can also mix and match arguments:

```jl
sco("rand(2:2:20, 3)")
```

It also supports a collection of elements as a tuple:

```jl
scob("""rand((42, "Julia", 3.14))""")
```

And also arrays:

```jl
scob("rand([1, 2, 3])")
```

`Dict`s:

```jl
sco("rand(Dict(:one => 1, :two => 2))")
```

To finish off all the `rand` arguments options, you can specify the desired random number dimensions in a tuple.
If you do this, the returned type will be an array.
For example, here's a 2x2 matrix of `Float64` numbers between 1.0 and 3.0:

```jl
sco("rand(1.0:3.0, (2, 2))")
```

#### `randn` {#sec:random_randn}

`randn` follows the same general principle from `rand` but now it only returns numbers generated from the **standard normal distribution**.
The standard normal distribution is the normal distribution with mean 0 and standard deviation 1.
The default type is `Float64` and it only allows for subtypes of `AbstractFloat` or `Complex`:

```jl
scob("randn()")
```

We can only specify the size:

```jl
sco("randn((2, 2))")
```

#### `seed!` {#sec:random_seed}

To finish off the `Random` overview, let's talk about **reproducibility**.
Often, we want to make something **replicable**.
Meaning that, we want the random number generator to generate the **same random sequence of numbers**.
We can do so with the `seed!` function:

```jl
s = """
    seed!(123)
    rand(3)
    """
sco(s)
```

```jl
s = """
    seed!(123)
    rand(3)
    """
sco(s)
```

In order to avoid tedious and inefficient repetition of `seed!` all over the place, we can instead define an instance of a `seed!` and pass it as a first argument of **either `rand` or `randn`**.

```jl
sco("my_seed = seed!(123)")
```


```jl
sco("rand(my_seed, 3)")
```

```jl
sco("rand(my_seed, 3)")
```

> **_NOTE:_**
> If you want your code to be reproducible you can just call `seed!` in the beginning of your script.
> This will take care of reproducibility in sequential `Random` operations.
> No need to use it all `rand` and `randn` usage.

### Downloads {#sec:downloads}

One last thing from Julia's standard library for us to cover is the **`Download` module**.
It will be really brief because we will only be covering a single function named `download`.

Suppose you want to **download a file from the internet to your local storage**.
You can accomplish this with the `download` function.
The first and only required argument is the file's url.
You can also specify as a second argument the desired output path for the downloaded file (don't forget the filesystem best practices!).
If you don't specify a second argument, Julia will, by default, create a temporary file with the `tempfile` function.

Let's load the `download` method:

```julia
using Download: download
```

For example, let's download our [`JuliaDataScience` GitHub repository](https://github.com/JuliaDataScience/JuliaDataScience) `Project.toml` file.
Note that `download` function is not exported by `Downloads` module, so we have to use the `Module.function` syntax.
By default, it returns a string that holds the file path for the downloaded file:

```jl
s = """
    url = "https://raw.githubusercontent.com/JuliaDataScience/JuliaDataScience/main/Project.toml"

    my_file = Downloads.download(url) # tempfile() being created
    """
scob(s)
```

With `readlines`, we can look at the first 4 lines of our downloaded file:

```jl
s = """
    readlines(my_file)[1:4]
    """
sco(s; process=catch_show)
```

> **_NOTE:_**
> For more complex HTTP interactions such as interacting with web APIs, see the [`HTTP.jl` package](https://github.com/JuliaWeb/HTTP.jl) package.