# Ruby 代码清洁之道

应用于Ruby语言的代码清洁之道。

受[clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript)项目启发。


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

软件工程的原则，来自于Robert C. Martin的书[*Clean Code*](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882),
应用于Ruby。这不是样式指南。它是构建[可读，可复用，可重构](https://github.com/ryanmcdermott/3rs-of-software-architecture) 工业级的Ruby软件工程指南。


不是每一个原则都必须严格的遵守，甚至有一些代码清洁原则没有达到广泛的共识。这些只是一些指导原则而已，但是他们是*清洁之道*作者的多年的经验总结。

我们的软件工程的这么手艺已经有50多年的历史了，不过我们依然可以从中学到很多。当软件架构和架构本身年纪一样大时，那时可能我们需要遵守更加严格的规则了。
不过，就当下而言，我们最好把这些原则当成你或者你的团队的Ruby代码清洁指南。


值得一提的是：知道这些原则不会立即让您成为一个更好的软件开发者，同样，一直努力去遵循这些代码清洁之道，并不意味着你就不会犯错。我们开始写的每行代码，这个过程就像瓷器从泥胚开始，最后变成瓷器一样。最后，当我们和搭档Review代码的时候，一起来把这些代码中的坏味道去掉。不要因为你的第一版需要提高的代码草稿而垂头丧气。相反，让我们打败臭代码。

## **变量**
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

### 为同样类型的变量使用同样的单词

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


### 使用可搜索的名称，使用常量

我们读的代码量会大大超过写的代码量。所以写出来可读和易搜索的代码是很重要的。所以*不*好好的给变量起名字，最终会伤害到代码的读者。

同样，使用“魔法数字”（常量），而不是硬编码变量。

**坏:**
```ruby
# 86_400究竟是什么玩意？
status = Timeout::timeout(86_400) do
  # ...
end
```

**好：**
```ruby
# 用常量来声明
SECONDS_IN_A_DAY = 86_400

status = Timeout::timeout(SECONDS_IN_A_DAY) do
  # ...
end
```
**[⬆ 回到目录](#目录)**

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

### 避免“心智图法”
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

### 不要添加不必要的上下文

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

### 使用默认参数，而不是使用异或，或是条件赋值

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

## ***方法*

### 方法参数（不多于2个）

限制方法参数是令人难以想象的重要，因为它会让你容易测试你的方法。多余三个参数的方法常常会让测试变得非常复杂。因为需要测试的组合太多了。

一个或两个参数是理想的情况，三个参数的情况应该尽量避免。超过三个参数的情况需要重构。通常，假如你的参数个数超过三个，那么意味着这个方法承担的责任太多。尽管有时候并不是这样的，多数的时候，多余的参数可以用一个高级的对象来包装一下。或者你可以通过实例变量来给方法传数据。


因为在Ruby中创建对象很方便，不像其他语言一样，假如你需要许多参数的话，你可以使用对象。在Ruby中流行的做法是使用Hash作为参数。
为了让方法想要的属性显而易见，你可以使用关键词参数的语法（在Ruby
2.1中引入）。这有几个优点：

1. 当有人查找方法的签名时，使用了那个属性一目了然。

2. 假如必要的关键词参数没有传，Ruby会raise一个有用的`ArgumentError`，它会告诉我们我们必须传入哪个参数。

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


### 方法应该仅做一件事

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

### 方法名应该说明它们做了什么

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

### 方法应该仅仅是一层抽象

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

### DRY

尽你最大的努力去实现DRY的目标。重复的代码意味着假如你需要修改一些逻辑的时候，你必须同时修改它们。

设想你开一家餐馆，你记录下你每天购买的蔬菜：所有的西红柿，洋葱，蒜，辣椒等等。假如你同时有几个单子，然后当你上了一盘西红柿以后，你必须更新所有的记录。
假如你仅有一个单子，那么你仅需要更新一个地方！

通常，你有重复的代码的情况是因为它们或多或少会有点不一样，其他大部分都一样，但是那些不同点让你不得不写一些大部分代码重复的方法。
移除重复的代码意味着创建抽象，通过抽象出一个方法/模块/类来处理不同的情况。

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

### 不要使用flag作为方法的参数

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

### 避免负效应（第一部分）

一个不仅仅会接受参数，返回值得方法会产生负效应。负效应可能入写入文件，修改全局变量，或者不小心把你的钱转给陌生人。

现在，偶尔，在一个程序里需要一些负效应。例如之前的例子，你可能需要写入文件。你想要做的是在你做这些的地方要中心化它们。不要用几个方法，或者类都去写入一个特殊的文件。
让一个服务来做这件事。一个，而且仅一个。

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

### 避免负效应（部分2）

在Ruby里，所有的都是对象，所有的都是通过值来传递，但是这些值是对象的参考。在对象和数组的例子中，假如你的方法在购物车数组里改变了，例如，通过添加一件商品，
另外一个处理`cart`数组的方法可能被这个影响。这也可能很好，也可能很坏。让我们设想一个坏的情况：

用户点击了购买按钮，这个按钮会调用一个`purchase`方法，这个方法会发生一个网络请求，然后把`cart`的值传到服务器。
因为网络环境较差，`purchase`方法不得不重新发起请求。现在，假设在网络请求开始的同时用户又不小心在一个他原本不想要的产品上点击了一下“加入购物车”按钮，
假如这两个同时发生，那么`purchase方法就会把不小心加进购物车的产品一起结算了，因为它调用的数组正好是`add_item_to_cart`方法通过添加不想要的产品而修改过的数组的参考。
译者注：（通常数组等在作为参数的时候，为了节约内存，编译器一般只会把数组的指针，即数组的地址传递给调用它的方法。因为Ruby中没有指针这样的概念，这里的reference意义和指针是一样的。）

好的方案就是`add_item_to_cart`总是克隆`cart`,
然后编辑它，然后返回克隆。这确保了没有别的方法会有购物车数组的参考，因此也不用担心它会被修改。

有两点需要说明的是：
1. 可能有一些情况，你确实希望修改输入的对象，但是当你适应这种编程实践的时候，你会发现这样的情况非常少见。大部分的情况可以在丝毫不影响的情况下进行重构。
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

### 与命令式编程相比，更喜欢函数式编程

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

### 避免条件

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

### 避免类型检查（第一部分）

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

### 避免类型检查（第二部分）

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

### 移除死代码

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

## **对象和数据结构**

### 使用getters和setters

使用getters和setters去获取一个对象的属性，可能比简单的查找对象的属性要好。为什么？你可能会问。嗯，这里有个没组织好的一些理由：

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

然而，你必须意识到在有些情况，使用`attr_accessor`也是臭代码，你可以看看[这里](http://solnic.eu/2012/04/04/get-rid-of-that-code-smell-attributes.html)
**[⬆ 回到目录](#目录)**


## **类**

### 避免流利的接口

[流利的接口](https://en.wikipedia.org/wiki/Fluent_interface)是面向对象的API，通过使用[方法链](https://en.wikipedia.org/wiki/Method_chaiing)来提高程序的可读性。

当有一些情景，需要频繁的创建对象，这种模式可以让代码看起来更简洁（如ActiveRecord的查询），其他更多的情况，这样编码会有一些代价。

1. 打破[封装](https://en.wikipedia.org/wiki/Encapsulation_%28object-oriented_programming%29)
2. 打破[装饰器](https://en.wikipedia.org/wiki/Decorator_pattern)
3. 在测试的时候，难以[mock](https://en.wikipedia.org/wiki/Mock_object)
4. 让提交代码时的diff变得难以阅读。

其他更多的信息，你可以阅读一下[Marco Pivetta](https://github.com/Ocramius)写的这篇[博客](https://ocramius.github.io/blog/fluent-interfaces-are-evil/)

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

### 偏好使用Mixin多过继承

正如在著名的[*设计模式*](https://en.wikipedia.org/wiki/Design_Patterns)这本书里提到的，你应该尽量使用Mixin，而不是继承。
有许多好的理由使用继承，也有很多好的理由使用Mixin。主要在于你直觉选择了继承，可以尝试使用Mixin来模型化你的问题，结果可能会更好。
在某些情况下，确实是这样的。

你可能犹豫，“那什么时候我应该用继承呢？”这依赖于你的问题，但是有一个公认的列表，如果属于下面列出的情况，那么使用继承更合理。

1. 你的继承代表了“是一个”的关系，而不是“有一个”的关系(Human->Animal vs. User->UserDetails)。
2. 你可以复用基类的代码（人可以向动物一样移动）。
3. 你希望可以通过改变基类影响全部的子孙类。（当动物活动的时候，改变所有动物运动时所消耗的卡路里）

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

### 单一责任原则

如同在清洁代码之道中所说，“修改类的原因不能超过一个”。给一个类添加许多功能，就好像要把它塞满一样，这种情形好像坐飞机的时候只能带一个行李箱。
这会导致类从概念上缺少凝聚力，所以就会有很多理由去修改这个类。最小化你修改类的需求很重要。因为如果一个类中包含太多的功能会让你在修改哪怕其中的一小块代码，你也难于
理解它会对其他代码造成什么样的影响。

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

### 开/闭原则（OCP）

如[Bertrand Meyer](https://en.wikipedia.org/wiki/Bertrand_Meyer) 所说,
“软件中的实体（类、模块、方法等）应该对扩展是开放的，但是对修改是关闭的。”什么意思呢？这个原则基本是在说你应该允许其他人在不修改现存代码的情况下就添加新的功能。


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

### 里氏替换原则（LSP）

这个概念本身很简单，不过它的名字却有点唬人。它正式的定义是“假如S是T的子类型，那么类型为T的对象应该都可以被类型为S的对象替换，也不会改变程序的属性（正确性、任务执行等）。”
（例如，类型S的对象可能替换类型T的对象）这个定义听起来更吓人。

对这个原则最佳的解释是假如你有一个父类和子类，那么父类即便用子类替换了，也不会引发错误。这可能依然会让人迷糊，所以我们看一下经典的Square-Rectangle例子。
从数学的角度来说，正方形是矩形的一种，但是假如你通过继承来模型化它，你很快就会遇到麻烦。

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

### 接口隔离原则(ISP)

Ruby没有接口，所以对Ruby来说这个原则不像其他语言要求那么严格。然而，它是重要的，甚至和Ruby的类型系统缺失相关。

ISP是说“客户不应该被强迫去依赖他们不使用的接口。”
因为鸭子类型，所以在Ruby中接口是隐式的合同。

当客户依赖包含接口的类，但客户不会使用这个接口，但是其他的客户使用了，那么这个客户就会在其他客户需要修改这个接口时受到影响。

下面的例子是来源于[here](http://geekhmer.github.io/blog/2015/03/18/interface-segregation-principle-in-ruby/).

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

### 依赖反转原则（DIP）

这个原则说明了两个必要的事情：
1. 高阶的模块不应该依赖于低阶的模块。它们都应该依赖于抽象。
2. 抽象不应该依赖于细节。细节应该依赖于抽象。

简单来说，DIP可以防止高阶的模块了解低阶模块的细节，这样可以模块之间的耦合。耦合是很差的开发模式，它可以让代码难以重构。

如之前所说，Ruby没有接口，所以被依赖的抽象是隐式的合同。也就是对象或者类暴露给别的对象和类的方法。在下面的例子里，隐式的合同是为`InventoryTracker`类的任意Request模块，
都有一个`request_items`方法。


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

## **测试**

测试比运行系统更重要。假如你没有测试，或者测试的比例不够，那么每次你部署的时候，你就很难确定系统会不会出错。
测试的覆盖度是否足够，取决于你的团队，但是100%的测试覆盖率（所有的声明和分支）是你获得非常高的信心和开发时内心平和的关键。这意味着你需要一个额外的[伟大的测试框架](http://rspec.info/), 你也需要使用[好的测试覆盖工具](https://coveralls.io/)

没有任何理由不写测试。为你的每个功能/模块写测试。假如你更喜欢的方法是测试驱动开发（TDD），那也挺好，但是主要的一点是，你也要在部署系统的时候确保达到你的测试覆盖目标。

### 每个测试只有一个expectation

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

## **错误处理**

抛出错误是对的！它们意味着运行时已经可以在程序出错的时候识别出错误，通过在当前的栈停止方法让你知道程序出错，结束进程，然后在日志里记录它们。

### 不要忽视捕捉错误

在捕捉错误的时候无所作为不会给你修复它的机会，当然你也意识不到出错。用日志记录错误也不太好，因为错误的信息会常常淹没于海量的日志当中。
假如你把代码用`begin/rescue`包住，这也意味着你知道可能会有错误出现，然后你也应该有一个相应的处理方案。

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

### 提供Exception的上下文

当你抛出错误的时候，使用描述性的错误类名和错误信息。然后你就可以知道为什么错误会发生，你也可以依据它提供的信息来修改错误。

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
## **代码格式**

代码格式是非常主观的。就像我们这里的其他规则，没有那条规则是你必须遵循的。要点就是不要在格式化上争论。
有许许多多的像RuboCop这样的工具来自动化处理他。随便找一个！对于工程师来说，在代码格式上进行争论是浪费时间。

对于那些自动格式化代码工具无法处理的情况，先看看社区的代码规范指南。


### 使用一致的大写规则

Ruby是动态类型，所以字母大写会告诉你很多关于变量，方法的信息。这些规则是主观的，所以你的团队可以选择任一种他们喜欢的规则。重点是保持一致性。

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


### 方法的调用者和被调用者应该尽量靠近

假如一个方法调用另一个，尽量让他们在源文件中的位置靠近一点。理想状况下，让调用方法的方法，正好位于被调用方法的上部。
我们希望自上而下的读代码，就像读报纸一样。因为这样可以让你的代码读起来的时候也有这种感觉。

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

## **评论**

### 不要把注释过的代码留在代码库

版本控制可以解决这个问题。所以大胆的把不需要的代码删除了。

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

### 不要在代码里保存日志性的评论

记住，使用版本控制！死代码是没有必要保留的，注释过的代码，尤其是日志性的注释都是没有必要保留的。使用`git log`就可以看到他们的历史！

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
  - [简体中文](https://github.com/uohzxela/clean-code-ruby/blob/master/translations/zh-CN.md)

*[⬆ 回到目录](#目录)**
