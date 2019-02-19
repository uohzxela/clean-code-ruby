# clean-code-ruby
# Ruby 代码清洁之道

Clean Code concepts adapted for Ruby.
应用于Ruby语言的代码清洁之道。

受[clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript)项目启发。

*Note: The examples are largely ported over from JavaScript so they may not be idiomatic. Feel free to point out any non-idiomatic Ruby code by submitting an issue and I'll correct it right away. Also, pull requests are always welcome!*

* 备注：这些例子大部分是直接从JavaScript移植过来的，所以有些例子可能不够ruby。如果有哪些不够ruby的代码，麻烦各位提交issue，我会尽早改正。当然，直接提PR也是欢迎的。
## 目录
  1. [介绍](#介绍)
  2. [变量](#变量)
  3. [方法](#方法)
  4. [对象和数据结构](#对象和数据结构)
  5. [Classes](#classes)
  6. [SOLID](#solid)
  7. [测试](#测试)
  9. [错误处理](#错误处理)
  10. [字符格式化](#字符格式化)
  11. [注释](#注释)
  12. [翻译](#翻译)



## 介绍 
![Humorous image of software quality estimation as a count of how many expletives
you shout when reading code](http://www.osnews.com/images/comics/wtfm.jpg)

Software engineering principles, from Robert C. Martin's book
软件工程的原则，来自于Robert C. Martin的书

[*Clean Code*](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882),
for Ruby. This is not a style guide. It's a guide to producing
应用于Ruby。这不是样式指南。它是构建

[可读，可复用，可重构](https://github.com/ryanmcdermott/3rs-of-software-architecture) 工业级的Ruby软件工程指南。

Not every principle herein has to be strictly followed, and even fewer will be
universally agreed upon. These are guidelines and nothing more, but they are
ones codified over many years of collective experience by the authors of
*Clean Code*.


不是每一个原则都必须严格的遵守，甚至有一些代码清洁原则没有达到广泛的共识。这些只是一些指导原则而已，但是他们是*清洁之道*作者的多年的经验总结。



Our craft of software engineering is just a bit over 50 years old, and we are
still learning a lot. When software architecture is as old as architecture
itself, maybe then we will have harder rules to follow. For now, let these
guidelines serve as a touchstone by which to assess the quality of the
Ruby code that you and your team produce.

我们的软件工程的这么手艺已经有50多年的历史了，不过我们依然可以从中学到很多。当软件架构和架构本身年纪一样大时，那时可能我们需要遵守更加严格的规则了。
不过，就当下而言，我们最好把这些原则当成你或者你的团队的Ruby代码清洁指南。

One more thing: knowing these won't immediately make you a better software
developer, and working with them for many years doesn't mean you won't make
mistakes. Every piece of code starts as a first draft, like wet clay getting
shaped into its final form. Finally, we chisel away the imperfections when
we review it with our peers. Don't beat yourself up for first drafts that need
improvement. Beat up the code instead!

值得一提的是：知道这些原则不会立即让您成为一个更好的软件开发者，同样，一直努力去遵循这些代码清洁之道，并不意味着你就不会犯错。我们开始写的每行代码，这个过程就像瓷器从泥胚开始，最后变成瓷器一样。最后，当我们和搭档Review代码的时候，一起来把这些代码中的坏味道去掉。不要因为你的第一版需要提高的代码草稿而垂头丧气。相反，让我们打败臭代码。

## **变量**
### Use meaningful and pronounceable variable names
### 使用有意义的和可以发音的变量名

*坏:**
```ruby
yyyymmdstr = Time.now.strftime('%Y/%m/%d')
```

**好：**
```ruby
current_date = Time.now.strftime('%Y/%m/%d')
```
**[⬆ 回到目录](#目录)**

### Use the same vocabulary for the same type of variable
### 为同样类型的变量使用同样的单词

Pick one word for the concept and stick to it.
为某个概念选好一个单词后不要再换来换去。

**坏:**
```ruby
user_info
user_data
user_record

starts_at
start_at
start_time
```

**好：**
```ruby
user

starts_at
```
**[⬆ 回到目录](#目录)**


### Use searchable names and use constants
### 使用可搜索的名称，使用常量

We will read more code than we will ever write. It's important that the code we
do write is readable and searchable. By *not* naming variables that end up
being meaningful for understanding our program, we hurt our readers.
Make your names searchable.

我们读的代码量会大大超过写的代码量。所以写出来可读和易搜索的代码是很重要的。所以*不*好好的给变量起名字，最终会伤害到代码的读者。

Also, instead of hardcoding values and using "magic numbers", create constants.
同样，使用“魔法数字”（常量），而不是硬编码变量。

**坏:**
```ruby
# What the heck is 86400 for?
# 86_400究竟是什么玩意？
status = Timeout::timeout(86_400) do
  # ...
end
```

**好：**
```ruby
# Declare them as capitalized globals.
# 用常量来声明
SECONDS_IN_A_DAY = 86_400

status = Timeout::timeout(SECONDS_IN_A_DAY) do
  # ...
end
```
**[⬆ 回到目录](#目录)**

### Use explanatory variables
### 使用描述性的变量名
**坏:**
```ruby
address = 'One Infinite Loop, Cupertino 95014'
city_zip_code_regex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/
save_city_zip_code(city_zip_code_regex.match(address)[1], city_zip_code_regex.match(address)[2])
```

**好：**
```ruby
address = 'One Infinite Loop, Cupertino 95014'
city_zip_code_regex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/
_, city, zip_code = city_zip_code_regex.match(address).to_a
save_city_zip_code(city, zip_code)
```
**[⬆ 回到目录](#目录)**

### Avoid Mental Mapping
### 避免“心智图法”
Explicit is better than implicit.
明示比暗示要好。

**坏:**
```ruby
locations = ['Austin', 'New York', 'San Francisco']
locations.each do |l|
  do_stuff
  do_some_other_stuff
  # ...
  # ...
  # ...
  # Wait, what is `l` for again?
  dispatch(l)
end
```

**好：**
```ruby
locations = ['Austin', 'New York', 'San Francisco']
locations.each do |location|
  do_stuff
  do_some_other_stuff
  # ...
  # ...
  # ...
  dispatch(location)
end
```
**[⬆ 回到目录](#目录)**

### Don't add unneeded context
### 不要添加不必要的上下文

If your class/object name tells you something, don't repeat that in your
variable name.
假如你的class/object名告诉你些什么，就不要在你的变量名里重复一遍了。

**坏:**
```ruby
car = {
  car_make: 'Honda',
  car_model: 'Accord',
  car_color: 'Blue'
}

def paint_car(car)
  car[:car_color] = 'Red'
end
```

**好：**
```ruby
car = {
  make: 'Honda',
  model: 'Accord',
  color: 'Blue'
}

def paint_car(car)
  car[:color] = 'Red'
end
```
**[⬆ 回到目录](#目录)**

### Use default arguments instead of short circuiting or conditionals
### 使用默认参数，而不是使用异或，或是条件赋值
Default arguments are often cleaner than short circuiting. Be aware that if you use them, your method will only provide default values for undefined arguments. Other "falsy" values such as `false` and `nil` will not be replaced by a default value.

默认参数比异或这种赋值方法会更清洁。
**坏:**
```ruby
def create_micro_brewery(name)
  brewery_name = name || 'Hipster Brew Co.'
  # ...
end
```

**好：**
```ruby
def create_micro_brewery(brewery_name = 'Hipster Brew Co.')
  # ...
end
```
**[⬆ 回到目录](#目录)**

## **Methods**
## ***方法*
### Method arguments (2 or fewer ideally)
### 方法参数（不多于2个）
Limiting the amount of method parameters is incredibly important because it
makes testing your method easier. Having more than three leads to a
combinatorial explosion where you have to test tons of different cases with
each separate argument.

限制方法参数是令人难以想象的重要，因为它会让你容易测试你的方法。多余三个参数的方法常常会让测试变得非常复杂。因为需要测试的组合太多了。

One or two arguments is the ideal case, and three should be avoided if possible.
Anything more than that should be consolidated. Usually, if you have
more than two arguments then your method is trying to do too much. In cases
where it's not, most of the time a higher-level object will suffice as an
argument. Or you can pass data to the method by instance variables.

一个或两个参数是理想的情况，三个参数的情况应该尽量避免。超过三个参数的情况需要重构。通常，假如你的参数个数超过三个，那么意味着这个方法承担的责任太多。尽管有时候并不是这样的，多数的时候，多余的参数可以用一个高级的对象来包装一下。或者你可以通过实例变量来给方法传数据。


Since Ruby allows you to make objects on the fly, without a lot of class
boilerplate, you can use an object if you are finding yourself needing a
lot of arguments. The prevailing pattern in Ruby is to use a hash of arguments.

因为在Ruby中创建对象很方便，不像其他语言一样，假如你需要许多参数的话，你可以使用对象。在Ruby中流行的做法是使用Hash作为参数。
To make it obvious what properties the method expects, you can use the keyword arguments syntax (introduced in Ruby 2.1). This has a few advantages:
为了让方法想要的属性显而易见，你可以使用关键词参数的语法（在Ruby
2.1中引入）。这有几个优点：

1. When someone looks at the method signature, it's immediately clear what
properties are being used.
1. 当有人查找方法的签名时，使用了那个属性一目了然。

2. If a required keyword argument is missing, Ruby will raise a useful `ArgumentError` that tells us which required argument we must include.
2.
假如必要的关键词参数没有传，Ruby会raise一个有用的`ArgumentError`，它会告诉我们我们必须传入哪个参数。

**坏:**
```ruby
def create_menu(title, body)
  # ...
end
```

**好：**
```ruby
def create_menu(title:, body:)
  # ...
end

create_menu(title: 'Foo', body: 'Bar')
```
**[⬆ 回到目录](#目录)**


### Methods should do one thing
### 方法应该仅做一件事
This is by far the most important rule in software engineering. When methods
do more than one thing, they are harder to compose, test, and reason about.
When you can isolate a method to just one action, they can be refactored
easily and your code will read much cleaner. If you take nothing else away from
this guide other than this, you'll be ahead of many developers.
这个到目前为止软件工程中最重要的一条原则。当一个方法的责任超过一个时，它们会变得难以组合，难以测试，难以推断它的具体作用。
当你可以做到一个方法只做一件事时，你重构它们的时候就会比较容易，而且你的代码也会变得更加清洁。假如你通过这个指南就记住了这一条，
那你也已经远远超过许多程序员了。


**坏:**
```ruby
def email_clients(clients)
  clients.each do |client|
    client_record = database.lookup(client)
    email(client) if client_record.active?
  end
end

email_clients(clients)
```

**好：**
```ruby
def email_clients(clients)
  clients.each { |client| email(client) }
end

def active_clients(clients)
  clients.select { |client| active_client?(client) }
end

def active_client?(client)
  client_record = database.lookup(client)
  client_record.active?
end

email_clients(active_clients(clients))
```
**[⬆ 回到目录](#目录)**

### Method names should say what they do
### 方法名应该说明它们做了什么

Poorly named methods add to the code reviewer's cognitive load at best, and mislead the
code reviewer at worst. Strive to capture the the precise intent when naming methods.
差的方法名容易让Review代码的人产生误解。当给方法命名的时候，要尽量体现方法的意图。

**坏:**
```ruby
def add_to_date(date, month)
  # ...
end

date = DateTime.now

# It's hard to tell from the method name what is added
add_to_date(date, 1)
```

**好：**
```ruby
def add_month_to_date(date, month)
  # ...
end

date = DateTime.now
add_month_to_date(date, 1)
```
**[⬆ 回到目录](#目录)**

### Methods should only be one level of abstraction
### 方法应该仅仅是一层抽象

When you have more than one level of abstraction your method is usually
doing too much. Splitting up methods leads to reusability and easier
testing. Furthermore, methods should descend by the level of abstraction: one very abstract method should call methods that are less abstract and so on.
当你的方法有多余一层的抽象时，通常说明这个方法太复杂了。拆分方法会提高可复用性，也会让测试变得很容易。更进一步说，方法应该降低抽象的层级：一个很抽象的方法应该调用稍微不抽象的方法，以此类推。

**坏:**
```ruby
def interpret(code)
  regexes = [
    # ...
  ]

  statements = code.split(' ')
  tokens = []
  regexes.each do |regex|
    statements.each do |statement|
      # ...
    end
  end

  ast = []
  tokens.each do |token|
    # lex...
  end

  result = []
  ast.each do |node|
    # result.push(...)
  end

  result
end
```

**好：**
```ruby
def interpret(code)
  tokens = tokenize(code)
  ast = lex(tokens)
  parse(ast)
end

def tokenize(code)
  regexes = [
    # ...
  ]

  statements = code.split(' ')
  tokens = []
  regexes.each do |regex|
    statements.each do |statement|
      # tokens.push(...)
    end
  end

  tokens
end

def lex(tokens)
  ast = []
  tokens.each do |token|
    # ast.push(...)
  end

  ast
end

def parse(ast)
  result = []
  ast.each do |node|
    # result.push(...)
  end

  result
end
```
**[⬆ 回到目录](#目录)**

### Remove duplicate code
### DRY
Do your absolute best to avoid duplicate code. Duplicate code is bad because it
means that there's more than one place to alter something if you need to change
some logic.

尽你最大的努力去实现DRY的目标。重复的代码意味着假如你需要修改一些逻辑的时候，你必须同时修改它们。


Imagine if you run a restaurant and you keep track of your inventory: all your
tomatoes, onions, garlic, spices, etc. If you have multiple lists that
you keep this on, then all have to be updated when you serve a dish with
tomatoes in them. If you only have one list, there's only one place to update!
设想你开一家餐馆，你记录下你每天购买的蔬菜：所有的西红柿，洋葱，蒜，辣椒等等。假如你同时有几个单子，然后当你上了一盘西红柿以后，你必须更新所有的记录。
假如你仅有一个单子，那么你仅需要更新一个地方！

Oftentimes you have duplicate code because you have two or more slightly
different things, that share a lot in common, but their differences force you
to have two or more separate methods that do much of the same things. Removing
duplicate code means creating an abstraction that can handle this set of
different things with just one method/module/class.

通常，你有重复的代码的情况是因为它们或多或少会有点不一样，其他大部分都一样，但是那些不同点让你不得不写一些大部分代码重复的方法。
移除重复的代码意味着创建抽象，通过抽象出一个方法/模块/类来处理不同的情况。

Getting the abstraction right is critical, that's why you should follow the
SOLID principles laid out in the *Classes* section. 坏 abstractions can be
worse than duplicate code, so be careful! Having said this, if you can make
a good abstraction, do it! Don't repeat yourself, otherwise you'll find yourself
updating multiple places anytime you want to change one thing.
创建正确的抽象有点麻烦，这也是为什么你应该遵循SOLID原则。我们会在后面的*类*的部分来单独讨论SOLID原则。坏的抽象比重复代码更臭，所以一定要更加小心！
继然说到这里了，假如你可以创建好的抽象，为什么不呢！不要重复自己，否则当你想要改变一个地方的时候，你不得不同时修改几个地方。

**坏:**
```ruby
def show_developer_list(developers)
  developers.each do |developer|
    data = {
      expected_salary: developer.expected_salary,
      experience: developer.experience,
      github_link: developer.github_link
    }

    render(data)
  end
end

def show_manager_list(managers)
  managers.each do |manager|
    data = {
      expected_salary: manager.expected_salary,
      experience: manager.experience,
      portfolio: manager.mba_projects
    }

    render(data)
  end
end
```

**好：**
```ruby
def show_employee_list(employees)
  employees.each do |employee|
    data = {
      expected_salary: employee.expected_salary,
      experience: employee.experience
    }

    case employee.type
    when 'manager'
      data[:portfolio] = employee.mba_projects
    when 'developer'
      data[:github_link] = employee.github_link
    end

    render(data)
  end
end
```
**[⬆ 回到目录](#目录)**

### Don't use flags as method parameters
### 不要使用flag作为方法的参数

Flags tell your user that this method does more than one thing. Methods should do one thing. Split out your methods if they are following different code paths based on a boolean.
Flag意味着这个方法做了不只一件事。方法应该只做一件事。依据逻辑变量拆分你的方法。

**坏:**
```ruby
def create_file(name, temp)
  if temp
    fs.create("./temp/#{name}")
  else
    fs.create(name)
  end
end
```

**好：**
```ruby
def create_file(name)
  fs.create(name)
end

def create_temp_file(name)
  create_file("./temp/#{name}")
end
```
**[⬆ 回到目录](#目录)**

### Avoid Side Effects (part 1)
### 避免负效应（第一部分）
A method produces side effects if it does anything more than take values and/or
return values. A side effect could be writing to a file,
modifying some global variable, or accidentally wiring all your money to a
stranger.
一个不仅仅会接受参数，返回值得方法会产生负效应。负效应可能入写入文件，修改全局变量，或者不小心把你的钱转给陌生人。

Now, you do need to have side effects in a program on occasion. Like the previous
example, you might need to write to a file. What you want to do is to
centralize where you are doing this. Don't have several methods and classes
that write to a particular file. Have one service that does it. One and only one.

现在，偶尔，在一个程序里需要一些负效应。例如之前的例子，你可能需要写入文件。你想要做的是在你做这些的地方要中心化它们。不要用几个方法，或者类都去写入一个特殊的文件。
让一个服务来做这件事。一个，而且仅一个。


The main point is to avoid common pitfalls like sharing state between objects
without any structure, using mutable data types that can be written to by anything,
and not centralizing where your side effects occur. If you can do this, you will
be happier than the vast majority of other programmers.
关键在于避免普通的陷阱，例如没有任何结构，在对象间共享状态，使用可变的数据类型，不中心化处理负效应的产生。假如你这样做，你会比绝大多数其他程序员更快乐。

**坏:**
```ruby
# Global variable referenced by following method.
# If we had another method that used this name, now it'd be an array and it could break it.
$name = 'Ryan McDermott'

def split_into_first_and_last_name
  $name = $name.split(' ')
end

split_into_first_and_last_name()

puts $name # ['Ryan', 'McDermott']
```

**好：**
```ruby
def split_into_first_and_last_name(name)
  name.split(' ')
end

name = 'Ryan McDermott'
first_and_last_name = split_into_first_and_last_name(name)

puts name # 'Ryan McDermott'
puts first_and_last_name # ['Ryan', 'McDermott']
```
**[⬆ 回到目录](#目录)**

### Avoid Side Effects (part 2)
### 避免负效应（部分2）
In Ruby, everything is an object and everything is passed by value, but these values are references to objects. In the case of objects and arrays, if your method makes a change
in a shopping cart array, for example, by adding an item to purchase,
then any other method that uses that `cart` array will be affected by this
addition. That may be great, however it can be bad too. Let's imagine a bad
situation:
在Ruby里，所有的都是对象，所有的都是通过值来传递，但是这些值是对象的参考。在对象和数组的例子中，假如你的方法在购物车数组里改变了，例如，通过添加一件商品，
另外一个处理`cart`数组的方法可能被这个影响。这也可能很好，也可能很坏。让我们设想一个坏的情况：

The user clicks the "Purchase", button which calls a `purchase` method that
spawns a network request and sends the `cart` array to the server. Because
of a bad network connection, the `purchase` method has to keep retrying the
request. Now, what if in the meantime the user accidentally clicks "Add to Cart"
button on an item they don't actually want before the network request begins?
If that happens and the network request begins, then that purchase method
will send the accidentally added item because it has a reference to a shopping
cart array that the `add_item_to_cart` method modified by adding an unwanted
item.

用户点击了购买按钮，这个按钮会调用一个`purchase`方法，这个方法会发生一个网络请求，然后把`cart`的值传到服务器。
因为网络环境较差，`purchase`方法不得不重新发起请求。现在，假设在网络请求开始的同时用户又不小心在一个他原本不想要的产品上点击了一下“加入购物车”按钮，
假如这两个同时发生，那么`purchase方法就会把不小心加进购物车的产品一起结算了，因为它调用的数组正好是`add_item_to_cart`方法通过添加不想要的产品而修改过的数组的参考。
译者注：（通常数组等在作为参数的时候，为了节约内存，编译器一般只会把数组的指针，即数组的地址传递给调用它的方法。因为Ruby中没有指针这样的概念，这里的reference意义和指针是一样的。）

A great solution would be for the `add_item_to_cart` to always clone the `cart`,
edit it, and return the clone. This ensures that no other methods that are
holding onto a reference of the shopping cart will be affected by any changes.
好的方案就是`add_item_to_cart`总是克隆`cart`,
然后编辑它，然后返回克隆。这确保了没有别的方法会有购物车数组的参考，因此也不用担心它会被修改。

Two caveats to mention to this approach:
有两点需要说明的是：
  1. There might be cases where you actually want to modify the input object,
but when you adopt this programming practice you will find that those cases
are pretty rare. Most things can be refactored to have no side effects!
  1.
可能有一些情况，你确实希望修改输入的对象，但是当你适应这种编程实践的时候，你会发现这样的情况非常少见。大部分的情况可以在丝毫不影响的情况下进行重构。

  2. Cloning big objects can be very expensive in terms of performance. Luckily,
this isn't a big issue in practice because there are
[great gems](https://github.com/hamstergem/hamster) that allow
this kind of programming approach to be fast and not as memory intensive as
it would be for you to manually clone objects and arrays.
2. 克隆大的对象在性能方面会有很大的影响。不过，幸运的是，因为有[hamster](https://github.com/hamstergem/hamster)。通过它，克隆大型对象在性能方面的牺牲可以忽略不计。

**坏:**
```ruby
def add_item_to_cart(cart, item)
  cart.push(item: item, time: Time.now)
end
```

**好：**
```ruby
def add_item_to_cart(cart, item)
  cart + [{ item: item, time: Time.now }]
end
```
**[⬆ 回到目录](#目录)**

### Favor functional programming over imperative programming
### 与命令式编程相比，更喜欢函数式编程

Ruby isn't a functional language in the way that Haskell is, but it has
a functional flavor to it. Functional languages are cleaner and easier to test.
Favor this style of programming when you can.
Ruby不是函数式语言，就像Haskell一样，但是它也可以做到类似函数式编程。函数式语言更加清洁，也更容易测试。如果你可以，就用函数式的方式去编程。

**坏:**
```ruby
programmer_output = [
  {
    name: 'Uncle Bobby',
    lines_of_code: 500
  }, {
    name: 'Suzie Q',
    lines_of_code: 1500
  }, {
    name: 'Jimmy Gosling',
    lines_of_code: 150
  }, {
    name: 'Grace Hopper',
    lines_of_code: 1000
  }
]

total_output = 0

programmer_output.each do |output|
  total_output += output[:lines_of_code]
end
```

**好：**
```ruby
programmer_output = [
  {
    name: 'Uncle Bobby',
    lines_of_code: 500
  }, {
    name: 'Suzie Q',
    lines_of_code: 1500
  }, {
    name: 'Jimmy Gosling',
    lines_of_code: 150
  }, {
    name: 'Grace Hopper',
    lines_of_code: 1000
  }
]

INITIAL_VALUE = 0

total_output = programmer_output.sum(INITIAL_VALUE) { |output| output[:lines_of_code] }
```
**[⬆ 回到目录](#目录)**

### Encapsulate conditionals
### 封装条件

**坏:**
```ruby
if params[:message].present? && params[:recipient].present?
  # ...
end
```

**好：**
```ruby
def send_message?(params)
  params[:message].present? && params[:recipient].present?
end

if send_message?(params)
  # ...
end
```
**[⬆ 回到目录](#目录)**

### Avoid negative conditionals
### 避免否定条件

**坏:**
```ruby
if !genres.blank?
  # ...
end
```

**好：**
```ruby
unless genres.blank?
  # ...
end

# or

if genres.present?
  # ...
end
```
**[⬆ 回到目录](#目录)**

### Avoid conditionals
### 避免条件
This seems like an impossible task. Upon first hearing this, most people say,
"how am I supposed to do anything without an `if` statement?" The answer is that
you can use polymorphism to achieve the same task in many cases. The second
question is usually, "well that's great but why would I want to do that?" The
answer is a previous clean code concept we learned: a method should only do
one thing. When you have classes and methods that have `if` statements, you
are telling your user that your method does more than one thing. Remember,
just do one thing.
第一次听到这句话的人的第一反应就是这好像是不可能完成的任务.
"没有`if`我什么也干不了"。
答案是在许多情况下，你都可以用多态来完成这些任务。第二个疑问通常是“为什么我要这么做？”。答案是之前我们学到的代码清洁的概念：方法应该只做一件事。
如果你的类或方法中有`if`语句，你就在告诉你的用户，你的方法做了不止一件事情。记住，只做一件事情。

**坏:**
```ruby
class Airplane
  # ...
  def cruising_altitude
    case @type
    when '777'
      max_altitude - passenger_count
    when 'Air Force One'
      max_altitude
    when 'Cessna'
      max_altitude - fuel_expenditure
    end
  end
end
```

**好：**
```ruby
class Airplane
  # ...
end

class Boeing777 < Airplane
  # ...
  def cruising_altitude
    max_altitude - passenger_count
  end
end

class AirForceOne < Airplane
  # ...
  def cruising_altitude
    max_altitude
  end
end

class Cessna < Airplane
  # ...
  def cruising_altitude
    max_altitude - fuel_expenditure
  end
end
```
**[⬆ 回到目录](#目录)**

### Avoid type-checking (part 1)
### 避免类型检查（第一部分）
Ruby is dynamically typed, which means your methods can take any type of argument.
Sometimes you are bitten by this freedom and it becomes tempting to do
type-checking in your methods. There are many ways to avoid having to do this.
The first thing to consider is consistent APIs.
Ruby是动态类型，也就是说你的方法可以传入任意类型的参数。有时这种自由可能会伤害你，因此有种情况可能会诱惑你在你的方法里做类型检查。
有很多方法可以避免这样做。首先需要考虑的是一致的API。

**坏:**
```ruby
def travel_to_texas(vehicle)
  if vehicle.is_a?(Bicycle)
    vehicle.pedal(@current_location, Location.new('texas'))
  elsif vehicle.is_a?(Car)
    vehicle.drive(@current_location, Location.new('texas'))
  end
end
```

**好：**
```ruby
def travel_to_texas(vehicle)
  vehicle.move(@current_location, Location.new('texas'))
end
```
**[⬆ 回到目录](#目录)**

### Avoid type-checking (part 2)
### 避免类型检查（第二部分）

If you are working with basic values like strings and integers,
and you can't use polymorphism but you still feel the need to type-check,
you should consider using [contracts.ruby](https://github.com/egonSchiele/contracts.ruby). The problem with manually type-checking Ruby is that
doing it well requires so much extra verbiage that the faux "type-safety" you get
doesn't make up for the lost readability. Keep your Ruby clean, write
good tests, and have good code reviews.
假如你正使用基础的数据类型，如字符串或者整数，所以你没法使用多态这种解决方案，这时你应该考试使用[contracts.ruby](https://github.com/egonSchiele/contracts.ruby)。
对于需要手动进行类型检查的Ruby代码的主要问题是获得“类型安全”的同时，牺牲了可读性。保持你的Ruby代码清洁，写好的测试代码，这样代码检查就容易了。

**坏:**
```ruby
def combine(val1, val2)
  if (val1.is_a?(Numeric) && val2.is_a?(Numeric)) ||
     (val1.is_a?(String) && val2.is_a?(String))
    return val1 + val2
  end

  raise 'Must be of type String or Numeric'
end
```

**好：**
```ruby
def combine(val1, val2)
  val1 + val2
end
```
**[⬆ 回到目录](#目录)**

### Remove dead code
### 移除死代码

Dead code is just as bad as duplicate code. There's no reason to keep it in
your codebase. If it's not being called, get rid of it! It will still be safe
in your version history if you still need it.
死代码和重复的代码一样臭。没有理由在你的代码里保存它们。假如它不会被调用，去掉它！假如你需要的时候，你还是可以从代码仓库中找回它们。


**坏:**
```ruby
def old_request_module(url)
  # ...
end

def new_request_module(url)
  # ...
end

req = new_request_module(request_url)
inventory_tracker('apples', req, 'www.inventory-awesome.io')
```

**好：**
```ruby
def new_request_module(url)
  # ...
end

req = new_request_module(request_url)
inventory_tracker('apples', req, 'www.inventory-awesome.io')
```
**[⬆ 回到目录](#目录)**

## **Objects and Data Structures**
## **对象和数据结构**
### Use getters and setters
### 使用getters和setters

Using getters and setters to access data on objects could be better than simply
looking for a property on an object. "Why?" you might ask. Well, here's an
unorganized list of reasons why:
使用getters和setters去获取一个对象的属性，可能比简单的查找对象的属性要好。为什么？你可能会问。嗯，这里有个没组织好的一些理由：

* When you want to do more beyond getting an object property, you don't have
to look up and change every accessor in your codebase.
* Makes adding validation simple when doing a `set`.
* Encapsulates the internal representation.
* Easy to add logging and error handling when getting and setting.
* You can lazy load your object's properties, let's say getting it from a
server.
* 当你不仅仅要获取一个对象的属性的时候，你不必查找并且修改你代码库里的每个accessor。
* 当你有`set`时，添加一些属性验证会很容易。
* 封装内部的表达。
* 当有getter和setter时，添加记录和错误处理就会比较容易。
* 比如说你从服务器获取时，你可以延迟加载你的对象的属性。

**坏:**
```ruby
def make_bank_account
  # ...

  {
    balance: 0
    # ...
  }
end

account = make_bank_account
account[:balance] = 100
account[:balance] # => 100
```

**好：**
```ruby
class BankAccount
  def initialize
    # this one is private
    @balance = 0
  end

  # a "getter" via a public instance method
  def balance
    # do some logging
    @balance
  end

  # a "setter" via a public instance method
  def balance=(amount)
    # do some logging
    # do some validation
    @balance = amount
  end
end

account = BankAccount.new
account.balance = 100
account.balance # => 100
```

Alternatively, if your getters and setters are absolutely trivial, you should use `attr_accessor` to define them. This is especially convenient for implementing data-like objects which expose data to other parts of the system (e.g., ActiveRecord objects, response wrappers for remote APIs).

假如你的getter和setter变得非常琐碎，你应该使用`attr_accessor`来定义他们。这个方法在实现数据类的对象时尤其方便，如ActiveRecord的对象，响应远程API调用的包装。
**好：**
```ruby
class Toy
  attr_accessor :price
end

toy = Toy.new
toy.price = 50
toy.price # => 50
```

However, you have to be aware that in some situations, using `attr_accessor` is a code smell, read more [here](http://solnic.eu/2012/04/04/get-rid-of-that-code-smell-attributes.html).

然而，你必须意识到在有些情况，使用`attr_accessor`也是臭代码，你可以看看[这里](http://solnic.eu/2012/04/04/get-rid-of-that-code-smell-attributes.html)
**[⬆ 回到目录](#目录)**


## **Classes**

### Avoid fluent interfaces
A [Fluent interface](https://en.wikipedia.org/wiki/Fluent_interface) is an object
oriented API that aims to improve the readability of the source code by using
[method chaining](https://en.wikipedia.org/wiki/Method_chaining).

While there can be some contexts, frequently builder objects, where this
pattern reduces the verbosity of the code (e.g., ActiveRecord queries),
more often it comes at some costs:

1. Breaks [Encapsulation](https://en.wikipedia.org/wiki/Encapsulation_%28object-oriented_programming%29)
2. Breaks [Decorators](https://en.wikipedia.org/wiki/Decorator_pattern)
3. Is harder to [mock](https://en.wikipedia.org/wiki/Mock_object) in a test suite
4. Makes diffs of commits harder to read

For more information you can read the full [blog post](https://ocramius.github.io/blog/fluent-interfaces-are-evil/)
on this topic written by [Marco Pivetta](https://github.com/Ocramius).

**坏:**
```ruby
class Car
  def initialize(make, model, color)
    @make = make
    @model = model
    @color = color
    # NOTE: Returning self for chaining
    self
  end

  def set_make(make)
    @make = make
    # NOTE: Returning self for chaining
    self
  end

  def set_model(model)
    @model = model
    # NOTE: Returning self for chaining
    self
  end

  def set_color(color)
    @color = color
    # NOTE: Returning self for chaining
    self
  end

  def save
    # save object...
    # NOTE: Returning self for chaining
    self
  end
end

car = Car.new('Ford','F-150','red')
  .set_color('pink')
  .save
```

**好：**
```ruby
class Car
  attr_accessor :make, :model, :color

  def initialize(make, model, color)
    @make = make
    @model = model
    @color = color
  end

  def save
    # Save object...
  end
end

car = Car.new('Ford', 'F-150', 'red')
car.color = 'pink'
car.save
```
**[⬆ 回到目录](#目录)**

### Prefer composition over inheritance
As stated famously in [*Design Patterns*](https://en.wikipedia.org/wiki/Design_Patterns) by the Gang of Four,
you should prefer composition over inheritance where you can. There are lots of
good reasons to use inheritance and lots of good reasons to use composition.
The main point for this maxim is that if your mind instinctively goes for
inheritance, try to think if composition could model your problem better. In some
cases, it can.

You might be wondering then, "when should I use inheritance?" It
depends on your problem at hand, but this is a decent list of when inheritance
makes more sense than composition:

1. Your inheritance represents an "is-a" relationship and not a "has-a"
relationship (Human->Animal vs. User->UserDetails).
2. You can reuse code from the base classes (Humans can move like all animals).
3. You want to make global changes to derived classes by changing a base class.
(Change the caloric expenditure of all animals when they move).

**坏:**
```ruby
class Employee
  def initialize(name, email)
    @name = name
    @email = email
  end

  # ...
end

# 坏 because Employees "have" tax data. EmployeeTaxData is not a type of Employee
class EmployeeTaxData < Employee
  def initialize(ssn, salary)
    super()
    @ssn = ssn
    @salary = salary
  end

  # ...
end
```

**好：**
```ruby
class EmployeeTaxData
  def initialize(ssn, salary)
    @ssn = ssn
    @salary = salary
  end

  # ...
end

class Employee
  def initialize(name, email)
    @name = name
    @email = email
  end

  def set_tax_data(ssn, salary)
    @tax_data = EmployeeTaxData.new(ssn, salary)
  end
  # ...
end
```
**[⬆ 回到目录](#目录)**

## **SOLID**
### Single Responsibility Principle (SRP)
As stated in Clean Code, "There should never be more than one reason for a class
to change". It's tempting to jam-pack a class with a lot of functionality, like
when you can only take one suitcase on your flight. The issue with this is
that your class won't be conceptually cohesive and it will give it many reasons
to change. Minimizing the number of times you need to change a class is important.
It's important because if too much functionality is in one class and you modify
a piece of it, it can be difficult to understand how that will affect other
dependent modules in your codebase.

**坏:**
```ruby
class UserSettings
  def initialize(user)
    @user = user
  end

  def change_settings(settings)
    return unless valid_credentials?
    # ...
  end

  def valid_credentials?
    # ...
  end
end
```

**好：**
```ruby
class UserAuth
  def initialize(user)
    @user = user
  end

  def valid_credentials?
    # ...
  end
end

class UserSettings
  def initialize(user)
    @user = user
    @auth = UserAuth.new(user)
  end

  def change_settings(settings)
    return unless @auth.valid_credentials?
    # ...
  end
end
```
**[⬆ 回到目录](#目录)**

### Open/Closed Principle (OCP)
As stated by [Bertrand Meyer](https://en.wikipedia.org/wiki/Bertrand_Meyer), "software entities (classes, modules, functions,
etc.) should be open for extension, but closed for modification." What does that
mean though? This principle basically states that you should allow users to
add new functionalities without changing existing code.

**坏:**
```ruby
class Adapter
  attr_reader :name
end

class AjaxAdapter < Adapter
  def initialize
    super()
    @name = 'ajaxAdapter'
  end
end

class NodeAdapter < Adapter
  def initialize
    super()
    @name = 'nodeAdapter'
  end
end

class HttpRequester
  def initialize(adapter)
    @adapter = adapter
  end

  def fetch(url)
    case @adapter.name
    when 'ajaxAdapter'
      make_ajax_call(url)
    when 'nodeAdapter'
      make_http_call(url)
    end
  end

  def make_ajax_call(url)
    # ...
  end

  def make_http_call(url)
    # ...
  end
end
```

**好：**
```ruby
class Adapter
  attr_reader :name
end

class AjaxAdapter < Adapter
  def initialize
    super()
    @name = 'ajaxAdapter'
  end

  def request(url)
    # ...
  end
end

class NodeAdapter < Adapter
  def initialize
    super()
    @name = 'nodeAdapter'
  end

  def request(url)
    # ...
  end
end

class HttpRequester
  def initialize(adapter)
    @adapter = adapter
  end

  def fetch(url)
    @adapter.request(url)
  end
end
```
**[⬆ 回到目录](#目录)**

### Liskov Substitution Principle (LSP)
This is a scary term for a very simple concept. It's formally defined as "If S
is a subtype of T, then objects of type T may be replaced with objects of type S
(i.e., objects of type S may substitute objects of type T) without altering any
of the desirable properties of that program (correctness, task performed,
etc.)." That's an even scarier definition.

The best explanation for this is if you have a parent class and a child class,
then the base class can always be replaced by the child class without getting
incorrect results. This might still be confusing, so let's take a look at the
classic Square-Rectangle example. Mathematically, a square is a rectangle, but
if you model it using the "is-a" relationship via inheritance, you quickly
get into trouble.

**坏:**
```ruby
class Rectangle
  def initialize
    @width = 0
    @height = 0
  end

  def color=(color)
    # ...
  end

  def render(area)
    # ...
  end

  def width=(width)
    @width = width
  end

  def height=(height)
    @height = height
  end

  def area
    @width * @height
  end
end

class Square < Rectangle
  def width=(width)
    @width = width
    @height = width
  end

  def height=(height)
    @width = height
    @height = height
  end
end

def render_large_rectangles(rectangles)
  rectangles.each do |rectangle|
    rectangle.width = 4
    rectangle.height = 5
    area = rectangle.area # BAD: Returns 25 for Square. Should be 20.
    rectangle.render(area)
  end
end

rectangles = [Rectangle.new, Rectangle.new, Square.new]
render_large_rectangles(rectangles)
```

**好：**
```ruby
class Shape
  def color=(color)
    # ...
  end

  def render(area)
    # ...
  end
end

class Rectangle < Shape
  def initialize(width, height)
    super()
    @width = width
    @height = height
  end

  def area
    @width * @height
  end
end

class Square < Shape
  def initialize(length)
    super()
    @length = length
  end

  def area
    @length * @length
  end
end

def render_large_shapes(shapes)
  shapes.each do |shape|
    area = shape.area
    shape.render(area)
  end
end

shapes = [Rectangle.new(4, 5), Rectangle.new(4, 5), Square.new(5)]
render_large_shapes(shapes)
```
**[⬆ 回到目录](#目录)**

### Interface Segregation Principle (ISP)
Ruby doesn't have interfaces so this principle doesn't apply as strictly
as others. However, it's important and relevant even with Ruby's lack of
type system.

ISP states that "Clients should not be forced to depend upon interfaces that
they do not use." Interfaces are implicit contracts in Ruby because of
duck typing.

When a client depends upon a class that contains interfaces that the client does not use, but that other clients do use, then that client will be affected by the changes that those other clients force upon the class.

The following example is taken from [here](http://geekhmer.github.io/blog/2015/03/18/interface-segregation-principle-in-ruby/).

**坏:**
```ruby
class Car
  # used by Driver
  def open
    # ...
  end

  # used by Driver
  def start_engine
    # ...
  end

  # used by Mechanic
  def change_engine
    # ...
  end
end

class Driver
  def drive
    @car.open
    @car.start_engine
  end
end

class Mechanic
  def do_stuff
    @car.change_engine
  end
end

```

**好：**
```ruby
# used by Driver only
class Car
  def open
    # ...
  end

  def start_engine
    # ...
  end
end

# used by Mechanic only
class CarInternals
  def change_engine
    # ...
  end
end

class Driver
  def drive
    @car.open
    @car.start_engine
  end
end

class Mechanic
  def do_stuff
    @car_internals.change_engine
  end
end
```

**[⬆ 回到目录](#目录)**

### Dependency Inversion Principle (DIP)
This principle states two essential things:
1. High-level modules should not depend on low-level modules. Both should
depend on abstractions.
2. Abstractions should not depend upon details. Details should depend on
abstractions.

Simply put, DIP keeps high-level
modules from knowing the details of its low-level modules and setting them up.
It can accomplish this through DI. A huge benefit of this is that it reduces
the coupling between modules. Coupling is a very bad development pattern because
it makes your code hard to refactor.

As stated previously, Ruby doesn't have interfaces so the abstractions
that are depended upon are implicit contracts. That is to say, the methods
and properties that an object/class exposes to another object/class. In the
example below, the implicit contract is that any Request module for an
`InventoryTracker` will have a `request_items` method.

**坏:**
```ruby
class InventoryRequester
  def initialize
    @req_methods = ['HTTP']
  end

  def request_item(item)
    # ...
  end
end

class InventoryTracker
  def initialize(items)
    @items = items

    # BAD: We have created a dependency on a specific request implementation.
    @requester = InventoryRequester.new
  end

  def request_items
    @items.each do |item|
      @requester.request_item(item)
    end
  end
end

inventory_tracker = InventoryTracker.new(['apples', 'bananas'])
inventory_tracker.request_items
```

**好：**
```ruby
class InventoryTracker
  def initialize(items, requester)
    @items = items
    @requester = requester
  end

  def request_items
    @items.each do |item|
      @requester.request_item(item)
    end
  end
end

class InventoryRequesterV1
  def initialize
    @req_methods = ['HTTP']
  end

  def request_item(item)
    # ...
  end
end

class InventoryRequesterV2
  def initialize
    @req_methods = ['WS']
  end

  def request_item(item)
    # ...
  end
end

# By constructing our dependencies externally and injecting them, we can easily
# substitute our request module for a fancy new one that uses WebSockets.
inventory_tracker = InventoryTracker.new(['apples', 'bananas'], InventoryRequesterV2.new)
inventory_tracker.request_items
```
**[⬆ 回到目录](#目录)**

## **Testing**
Testing is more important than shipping. If you have no tests or an
inadequate amount, then every time you ship code you won't be sure that you
didn't break anything. Deciding on what constitutes an adequate amount is up
to your team, but having 100% coverage (all statements and branches) is how
you achieve very high confidence and developer peace of mind. This means that
in addition to having a [great testing framework](http://rspec.info/), you also need to use a
[good coverage tool](https://coveralls.io/).

There's no excuse to not write tests. Aim to always write tests
for every new feature/module you introduce. If your preferred method is
Test Driven Development (TDD), that is great, but the main point is to just
make sure you are reaching your coverage goals before launching any feature,
or refactoring an existing one.

### Single expectation per test

**坏:**
```ruby
require 'rspec'

describe 'Calculator' do
  let(:calculator) { Calculator.new }

  it 'performs addition, subtraction, multiplication and division' do
    expect(calculator.calculate('1 + 2')).to eq(3)
    expect(calculator.calculate('4 - 2')).to eq(2)
    expect(calculator.calculate('2 * 3')).to eq(6)
    expect(calculator.calculate('6 / 2')).to eq(3)
  end
end
```

**好：**
```ruby
require 'rspec'

describe 'Calculator' do
  let(:calculator) { Calculator.new }

  it 'performs addition' do
    expect(calculator.calculate('1 + 2')).to eq(3)
  end

  it 'performs subtraction' do
    expect(calculator.calculate('4 - 2')).to eq(2)
  end

  it 'performs multiplication' do
    expect(calculator.calculate('2 * 3')).to eq(6)
  end

  it 'performs division' do
    expect(calculator.calculate('6 / 2')).to eq(3)
  end
end
```
**[⬆ 回到目录](#目录)**

## **Error Handling**
Thrown errors are a good thing! They mean the runtime has successfully
identified when something in your program has gone wrong and it's letting
you know by stopping method execution on the current stack, killing the
process, and notifying you in the logs with a stack trace.

### Don't ignore caught errors
Doing nothing with a caught error doesn't give you the ability to ever fix
or react to said error. Logging the error
isn't much better as often times it can get lost in a sea of other logs. If you wrap any bit of code in a `begin/rescue` it means you
think an error may occur there and therefore you should have a plan,
or create a code path, for when it occurs.

**坏:**
```ruby
require 'logger'

logger = Logger.new(STDOUT)

begin
  method_that_might_throw()
rescue StandardError => err
  logger.info(err)
end
```

**好：**
```ruby
require 'logger'

logger = Logger.new(STDOUT)
# Change the logger level to ERROR to output only logs with ERROR level and above
logger.level = Logger::ERROR

begin
  method_that_might_throw()
rescue StandardError => err
  # Option 1: Only log errors
  logger.error(err)
  # Option 2: Notify end-user via an interface
  notify_user_of_error(err)
  # Option 3: Report error to a third-party service like Honeybadger
  report_error_to_service(err)
  # OR do all three!
end
```

### Provide context with exceptions
Use a descriptive error class name and a message when you raise an error. That way you know why the error occurred and you can rescue the specific type of error.

***坏:***
```ruby
def initialize(user)
  fail unless user
  ...
end
```

***好：***
```ruby
def initialize(user)
  fail ArgumentError, 'Missing user' unless user
  ...
end
```

**[⬆ 回到目录](#目录)**


## **Formatting**
Formatting is subjective. Like many rules herein, there is no hard and fast
rule that you must follow. The main point is DO NOT ARGUE over formatting.
There are tons of tools like [RuboCop](https://github.com/bbatsov/rubocop) to automate this.
Use one! It's a waste of time and money for engineers to argue over formatting.

For things that don't fall under the purview of automatic formatting
(indentation, tabs vs. spaces, double vs. single quotes, etc.) look here
for some guidance.

### Use consistent capitalization
Ruby is dynamically typed, so capitalization tells you a lot about your variables,
methods, etc. These rules are subjective, so your team can choose whatever
they want. The point is, no matter what you all choose, just be consistent.

**坏:**
```ruby
DAYS_IN_WEEK = 7
daysInMonth = 30

songs = ['Back In Black', 'Stairway to Heaven', 'Hey Jude']
Artists = ['ACDC', 'Led Zeppelin', 'The Beatles']

def eraseDatabase; end

def restore_database; end

class ANIMAL; end
class Alpaca; end
```

**好：**
```ruby
DAYS_IN_WEEK = 7
DAYS_IN_MONTH = 30

SONGS = ['Back In Black', 'Stairway to Heaven', 'Hey Jude'].freeze
ARTISTS = ['ACDC', 'Led Zeppelin', 'The Beatles'].freeze

def erase_database; end

def restore_database; end

class Animal; end
class Alpaca; end
```
**[⬆ 回到目录](#目录)**


### Method callers and callees should be close
If a method calls another, keep those methods vertically close in the source
file. Ideally, keep the caller right above the callee. We tend to read code from
top-to-bottom, like a newspaper. Because of this, make your code read that way.

**坏:**
```ruby
class PerformanceReview
  def initialize(employee)
    @employee = employee
  end

  def lookup_peers
    db.lookup(@employee, :peers)
  end

  def lookup_manager
    db.lookup(@employee, :manager)
  end

  def peer_reviews
    peers = lookup_peers
    # ...
  end

  def perf_review
    peer_reviews
    manager_review
    self_review
  end

  def manager_review
    manager = lookup_manager
    # ...
  end

  def self_review
    # ...
  end
end

review = PerformanceReview.new(employee)
review.perf_review
```

**好：**
```ruby
class PerformanceReview
  def initialize(employee)
    @employee = employee
  end

  def perf_review
    peer_reviews
    manager_review
    self_review
  end

  def peer_reviews
    peers = lookup_peers
    # ...
  end

  def lookup_peers
    db.lookup(@employee, :peers)
  end

  def manager_review
    manager = lookup_manager
    # ...
  end

  def lookup_manager
    db.lookup(@employee, :manager)
  end

  def self_review
    # ...
  end
end

review = PerformanceReview.new(employee)
review.perf_review
```

**[⬆ 回到目录](#目录)**

## **Comments**


### Don't leave commented out code in your codebase
Version control exists for a reason. Leave old code in your history.

**坏:**
```ruby
do_stuff
# do_other_stuff
# do_some_more_stuff
# do_so_much_stuff
```

**好：**
```ruby
do_stuff
```
**[⬆ 回到目录](#目录)**

### Don't have journal comments
Remember, use version control! There's no need for dead code, commented code,
and especially journal comments. Use `git log` to get history!

**坏:**
```ruby
# 2016-12-20: Removed monads, didn't understand them (RM)
# 2016-10-01: Improved using special monads (JP)
# 2016-02-03: Removed type-checking (LI)
# 2015-03-14: Added combine with type-checking (JR)
def combine(a, b)
  a + b
end
```

**好：**
```ruby
def combine(a, b)
  a + b
end
```
**[⬆ 回到目录](#目录)**

## Translations

This is also available in other languages:

  - [Brazilian Portuguese](https://github.com/uohzxela/clean-code-ruby/blob/master/translations/pt-BR.md)

*[⬆ 回到目录](#目录)**
