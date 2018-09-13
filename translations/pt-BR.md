# clean-code-ruby

Conceitos de Código Limpo (*Clean Code*) adaptados para Ruby.

Inspirado por [clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript).

*Observação: Isso ainda é um trabalho em progresso. Os exemplos  aqui são em sua maior parte portados da versão em JavaScript, então eles podem não ser muito idiomático. Sinta-se livre para apontar qualquer código em Ruby que não seja idiomático submetendo uma *issue* e eu irei corrigi-lo imediatamente. Além disso, *pull requests* são sempre bem-vindos!

## Sumário
  1. [Introdução](#introdução)
  2. [Variáveis](#variáveis)
  3. [Funções](#funções)
  4. [Objetos e Estruturas de Dado](#objetos-e-estruturas-de-dado)
  5. [Classes](#classes)
  6. [SOLID](#solid)
  7. [Testes](#testes)
  8. [Lidando com Erros](#lidando-com-erros)
  9. [Formatação](#formatação)
  10. [Comentários](#comentários)

  ## Introdução
Imagem bem-humorada sobre estimativa de qualidade de software de acordo com
quantos palavrões você solta enquanto está lendo código.

![Imagem](http://www.osnews.com/images/comics/wtfm.jpg)

Princípios de Engenharia de Software, do livro de Robert C. Martin
[*Clean Code*](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882),
adaptado para Ruby. Isso não é um guia de estilo. É um guia para produzir software
[legível, reutilizável e refatorável](https://github.com/ryanmcdermott/3rs-of-software-architecture) em Ruby.

## **Variáveis**
### Use nomes de variáveis que tenham significado e pronúncia

**Ruim:**
```ruby
yyyymmdstr = Time.now.strftime('%Y/%m/%d')
```

**Bom:**
```ruby
current_date = Time.now.strftime('%Y/%m/%d')
```
**[⬆ retornar ao topo](#sumário)**

### Use o mesmo vocabulário para o mesmo tipo de variável

Escolha uma palavra para o conceito e permaneça com ela.

**Ruim:**
```ruby
user_info
user_data
user_record

starts_at
start_at
start_time
```

**Bom:**
```ruby
user

starts_at
```
**[⬆ retornar ao topo](#sumário)**

### Use nomes pesquisáveis e use constantes

Nós iremos ler mais código do que jamais escreveremos. É importante que o
código que escrevemos seja legível e pesquisável. Ao **não** nomear variáveis
com um significado que ajude a compreender nosso programa, nós prejudicamos
nossos leitores. Faça seus nomes serem pesquisáveis.

Alem disso, ao invés de escrever valores em código e usar "números mágicos",
crie constantes.

**Ruim:**
```ruby
# Para que diabos 86400 serve?
status = Timeout::timeout(86_400) do
  # ...
end
```

**Bom:**
```ruby
# Declare-as como globais em maiúsculo.
SECONDS_IN_A_DAY = 86_400

status = Timeout::timeout(SECONDS_IN_A_DAY) do
  # ...
end
```
**[⬆ retornar ao topo](#sumário)**

### Use variáveis explicativas
**Ruim:**
```ruby
address = 'One Infinite Loop, Cupertino 95014'
city_zip_code_regex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/
save_city_zip_code(city_zip_code_regex.match(address)[1], city_zip_code_regex.match(address)[2])
```

**Bom:**
```ruby
address = 'One Infinite Loop, Cupertino 95014'
city_zip_code_regex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/
_, city, zip_code = city_zip_code_regex.match(address).to_a
save_city_zip_code(city, zip_code)
```
**[⬆ retornar ao topo](#sumário)**

### Evite Mapeamento Mental
Explícito é melhor que implícito.

**Ruim:**
```ruby
locations = ['Austin', 'New York', 'San Francisco']
locations.each do |l|
  do_stuff
  do_some_other_stuff
  # ...
  # ...
  # ...
  # Espere, para que serve `l` mesmo?
  dispatch(l)
end
```

**Bom:**
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
**[⬆ retornar ao topo](#sumário)**

### Não adicione contexto desnecessário

Se o nome da sua classe/objeto diz alguma coisa a você, não repita isso no
nome da sua variável.

**Ruim:**
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

**Bom:**
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
**[⬆ retornar ao topo](#sumário)**

### Use argumentos padrões ao invés de curto-circuito ou condicionais

Argumentos padrões frequentemente são mais limpos do que curto-circuitos.
Esteja ciente que se você usá-los, suas funções só irão fornecer valores
padrões para argumentos indefinidos. Outros valores "falsos" como `''`, `""`,
`false` and `nil` não serão substituídos por um valor padrão.

**Ruim:**
```ruby
def create_micro_brewery(name)
  brewery_name = name || 'Hipster Brew Co.'
  # ...
end
```

**Bom:**
```ruby
def create_micro_brewery(brewery_name = 'Hipster Brew Co.')
  # ...
end
```
**[⬆ retornar ao topo](#sumário)**

## **Funções**
### Argumentos de Funções (2 ou menos de preferência)
Limitar a quantidade de parâmetros de uma função é incrivelmente importante
porque isso torna o processo de testar sua função mais fácil. Ter mais do que
três leva a uma explosão combinatória, onde você precisa testar toneladas de
diferentes casos com cada argumento separadamente.

Um ou dois argumentos é o caso ideal, três deveria ser evitado se possível.
Qualquer coisa mais do que isso deveria ser estabilizado. Geralmente, se você
tem mais do que dois argumentos então a sua função está tentando fazer demais.
Em casos onde ela não está, na maioria das vezes um objeto de nível mais alto
será suficiente como um argumento. Ou você pode passa informações para a função
através de variáveis de instância.

Uma vez que Ruby permite que você crie objetos em tempo de execução, sem um
monte de código repetitivo de classe, você pode usar um objeto se você se
encontrar precisando de um monte de argumentos. O padrão predominante em Ruby
é usar um *hash* de argumentos.

Para tornar evidente quais propriedades uma função espera, você pode usar a
sintaxe de argumentos de palavra chave (introduzido no Ruby 2.1). Isso tem
algumas vantagens:

1.  Quando alguém olha para a assinatura da função, é imediatamente visível
quais propriedades estão sendo usadas.
2. Se um argumento palavra chave estiver faltando, Ruby vai retornar um erro
útil `ArgumentError` que nos diz quais argumentos necessários precisamos
incluir.

**Ruim:**
```ruby
def create_menu(title, body, button_text, cancellable)
  # ...
end
```

**Bom:**
```ruby
def create_menu(title:, body:, button_text:, cancellable:)
  # ...
end

create_menu(
  title: 'Foo',
  body: 'Bar',
  button_text: 'Baz',
  cancellable: true
)
```
**[⬆ retornar ao topo](#sumário)**


### Funções deve fazer uma coisa
Essa é de longe a regra mais importante em engenharia de software. Quando
funções fazem mais do que uma coisa, elas são mais difíceis de integrar,
testar e compreender. Quando você consegue isolar a função a apenas uma ação,
elas podem ser refatoradas facilmente e o seu código será lido de forma muito
mais clara. Se você não conseguir tirar nada mais desse guia além disso, você
já estará na frente de muitos desenvolvedores.

**Ruim:**
```ruby
def email_clients(clients)
  clients.each do |client|
    client_record = database.lookup(client)
    email(client) if client_record.active?
  end
end
```

**Bom:**
```ruby
def email_active_clients(clients)
  clients
    .select(&method(:active_client?))
    .each(&method(:email))
end

def active_client?(client)
  client_record = database.lookup(client)
  client_record.active?
end
```
**[⬆ retornar ao topo](#sumário)**

### Nomes de funções devem dizer o que elas fazem

**Ruim:**
```ruby
def add_to_date(date, month)
  # ...
end

date = DateTime.now

# É difícil dizer pelo nome da função o que é adicionado
add_to_date(date, 1)
```

**Bom:**
```ruby
def add_month_to_date(date, month)
  # ...
end

date = DateTime.now
add_month_to_date(date, 1)
```
**[⬆ retornar ao topo](#sumário)**

### Funções devem ter apenas um nível de abstração
Quando você tem mais do que um nível de abstração sua função geralmente está
fazendo muita coisa. Dividir funções leva a reusabilidade e testes mais fáceis.
Além disso, funções devem descer de acordo com o nível de abstração: uma
função muito abstrata deve chamar métodos que são menos abstratos e assim por
diante.

**Ruim:**
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

**Bom:**
```ruby
def interpet(code)
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
**[⬆ retornar ao topo](#sumário)**

### Remover código duplicado
Faça o seu melhor para evitar duplicar código. Código duplicado é ruim porque
significa que existe mais do que um lugar para alterar algo se você
precisar mudar alguma lógica.

Imagine que você tenha um restaurante e mantenha registro de seu inventário:
todos os seus tomates, cebolas, alhos, pimentas, etc. Se você tem múltiplas
listas onde você mantém essa informação, então todas elas precisarão ser
atualizadas quando você serve um prato com tomates. Se você só tem uma lista,
existe apenas um lugar para atualizar!

Muitas vezes você tem código duplicado porque você tem duas ou mais coisas
levemente diferentes, que compartilham muitas semelhanças.
As pequenas difereças forçam você a ter duas ou mais funções separadas que
fazem muito das mesmas coisas. Remover código duplicado significa criar uma
abstração que possa lidar com esse conjunto de coisas diferentes com apenas uma
função/módulo/classe.

Conseguir a abstração certa é crítico, é por ísso que você deveria seguir os
princípios SOLID explicado na seção de **Classes**. Abstrações ruins podem ser
piores do que código duplicado, então seja cauteloso! Tendo dito isso, se você
conseguir criar uma boa abstração, crie! Não se repita, caso contrário você vai
se encontrar atualizando vários lugares sempre que vocÊ quiser mudar uma coisa.

**Ruim:**
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

**Bom:**
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
**[⬆ retornar ao topo](#sumário)**

### Não use *flags* como parâmetros de funções
*Flags* (bandeiras) dizem para seu usuário que essa função faz mais do que uma
coisa. Funções devem fazer apenas uma coisa. Separe suas funções se elas seguem
diferentes caminhos de código baseado em um boleano (verdadeiro/falso).


**Ruim:**
```ruby
def create_file(name, temp)
  if temp
    fs.create("./temp/#{name}")
  else
    fs.create(name)
  end
end
```

**Bom:**
```ruby
def create_file(name)
  fs.create(name)
end

def create_temp_file(name)
  create_file("./temp/#{name}")
end
```
**[⬆ retornar ao topo](#sumário)**

### Evite Efeitos Colaterais (Parte 1)
Uma função produz um efeito colateral se ela faz algo além de receber um valor
como entrada e retornar outro valor ou valores como saída. Um efeito colateral
poderia ser escrever em um arquivo, modificar uma variável global ou
acidentalmente enviar todo o seu dinheiro para um estranho.

Você precisa ter efeitos colaterais em um programa de vez em quando. Conforme
nos exemplos anteriores, você pode precisar escrever em um arquivo. O que você
que fazer é centralizar onde você estiver fazendo isso. Não tenha diversas
funções e classes que escrevam em um arquivo em particular. Tenha um serviço
que faça isso. Um e apenas um.

O objetivo principal é evitar armadilhas comuns como compartilhar estado entre
objetos sem estrutura alguma, usar tipo de informações mutáveis que podem ser
escritas por qualquer coisa e não centralizar onde o efeito colateral ocorre. Se
você conseguir fazer isso, você será mais feliz do que a grande maioria dos
outros programadores.

**Ruim:**
```ruby
# Variável global referenciada pela seguinte função.
# Se você tivesse outra função que usasse esse nome, agora ela seria um vetor e poderia quebrar.
$name = 'Ryan McDermott'

def split_into_first_and_last_name
  $name = $name.split(' ')
end

split_into_first_and_last_name()

puts $name # ['Ryan', 'McDermott']
```

**Bom:**
```ruby
def split_into_first_and_last_name(name)
  name.split(' ')
end

name = 'Ryan McDermott'
new_name = split_into_first_and_last_name(name)

puts name # 'Ryan McDermott'
puts new_name # ['Ryan', 'McDermott']
```
**[⬆ retornar ao topo](#sumário)**

### Evite Efeitos Colaterais (Parte 2)
Em Ruby, tudo é um objeto e tudo é passado por valor, mas esses valores são
referências para objetos. No caso de objetos e vetores, se seu sua função faz
uma alteração em um vetor de um `carrinho` de compras, por exemplo, ao adicionar
um item de compra, então qualquer outra função que use o vetor desse carrinho
será afetada por essa adição. Isso pode ser ótimo, no entanto pode ser ruim
também. Vamos imaginar uma situação ruim:

O usuário clica no botão "Comprar", que chama a função `comprar` que invoca uma
requisição na rede e envia o vetor de itens `carrinho` para para o servidor.
Devido a conexão ruim com a rede, a função `comprar` tem que continuar tentando
efetuar a requisição. Agora, e se o usuário nesse intervalo de tempo
acidentalmente clicar no botão "Adicionar ao Carrinho" em um item que ele não
quer na realidade, antes da requisição ser iniciada?
Se isso acontecer e a requisição começar, então aquela função `comprar` vai
enviar o item adicionado acidentalmente porque ela tem uma referência para o
vetor de itens que a função `adicionar_item_ao_carrinho` modificou adicionando o
item indesejado.

Uma boa solução seria o `adicionar_item_ao_carrinho` sempre clonar o `carrinho`,
editá-lo e retornar o clone. Isso garante que nenhuma outra função que está
guardando uma referência para o carrinho de compras será afetada por qualquer
mudança.

Duas ressalvas que devem ser mencionada para essa abordagem:
  1. Talvez existam casos onde você realmente quer modificar o objeto de entrada,
mas quando você adota essa prática de programação você vai descobrir que esses
casos são bem raros. A maiorira das coisa podem ser refatoradas para ter
nenhum efeito colateral!
  2. Clonar objetos grandes pode ser custoso em relação a desempenho. Por sorte,
isso não é um grande problema na prática porque existem
[ótimas gems](https://github.com/hamstergem/hamster) que permitem que esse tipo
de abordagem de programação seja rápida e não muito intensiva em relação a uso
de memória como seria caso você clonasse objetos e vetores manualmente.

**Ruim:**
```ruby
def add_item_to_cart(cart, item)
  cart.push(item: item, time: Time.now)
end
```

**Bom:**
```ruby
def add_item_to_cart(cart, item)
  cart + [{ item: item, time: Time.now }]
end
```
**[⬆ retornar ao topo](#sumário)**

### Prefira programação funcional sobre programação imperativa
Ruby não é uma linguagem funcional do mesmo modo que Haskell é, mas ela tem
um aroma funcional nela. Linguagens funcionais são mais limpas e fáceis de
testar. Prefira esse estilo de programação quando puder.

**Ruim:**
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

**Bom:**
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

total_output = programmer_output
               .reduce(INITIAL_VALUE) { |acc, output| acc + output[:lines_of_code] }
```
**[⬆ retornar ao topo](#sumário)**

### Encapsule Condicionais

**Ruim:**
```ruby
if params[:message].present? && params[:recipient].present?
  # ...
end
```

**Bom:**
```ruby
def send_message?(params)
  params[:message].present? && params[:recipient].present?
end

if send_message?(params)
  # ...
end
```
**[⬆ retornar ao topo](#sumário)**

### Evite condicionais negativas

**Ruim:**
```ruby
if !genres.blank?
  # ...
end
```

**Bom:**
```ruby
unless genres.blank?
  # ...
end

# ou

if genres.present?
  # ...
end
```
**[⬆ retornar ao topo](#sumário)**

### Evite Condicionais
Isso parece uma tarefa impossível. Ao escutar isso pela primeira vez, a maioria
das pessoas dizem, "como eu deveria fazer qualquer coisa sem uma declaração `if`
?". A resposta é que você pode usar polimorfismo para alcançar o mesmo objetivo
em muitos casos. A segunda pergunta é geralmente, "bem, isso é ótimo, mas por
que eu iria querer fazer isso?". A resposta é um conceito de código limpo que
aprendemos anteriormente: uma função deveria fazer apenas uma coisa. Quando
você tem classes e funções que tem declarações do tipo `if`, você está dizendo
ao usuário daquela função que ela faz mais do que uma coisa. Lembre-se, faça
apenas uma coisa.

**Ruim:**
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

**Bom:**
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
**[⬆ retornar ao topo](#sumário)**

### Evite Verificações de Tipo (Parte 1)
Você não precisa declarar o tipo em Ruby, o que significa que suas funções
podem receber qualquer tipo de argumento. Algumas vezes vocês você tropeça
devido a essa liberdade e se torna tentador fazer verificação de tipo em suas
funções. Existem diversas maneiras de se evitar ter que fazer isso. A primeira
coisa a ser considerada é APIs consistentes.

**Ruim:**
```ruby
def travel_to_texas(vehicle)
  if vehicle.is_a?(Bicycle)
    vehicle.pedal(@current_location, Location.new('texas'))
  elsif vehicle.is_a?(Car)
    vehicle.drive(@current_location, Location.new('texas'))
  end
end
```

**Bom:**
```ruby
def travel_to_texas(vehicle)
  vehicle.move(@current_location, Location.new('texas'))
end
```
**[⬆ retornar ao topo](#sumário)**

### Evite Verificações de Tipo (Parte 1)
Se você está trabalhando com tipos básicos como strings e inteiros e você
não pode usar polimorfismo, mas ainda sente necessidade de verificar o tipo,
você deveria considerar usar [contracts.ruby](https://github.com/egonSchiele/contracts.ruby).
O problema com a verificação de tipo manual em Ruby é que ela não compensa a
perda de legibilidade. Mantenha seu código em Ruby limpo, escreva bons testes e
tenha bons *reviews* de código.

**Ruim:**
```ruby
def combine(val1, val2)
  if (val1.is_a?(Numeric) && val2.is_a?(Numeric)) ||
     (val1.is_a?(String) && va2.is_a?(String))
    val1 + val2
  end

  raise 'Must be of type String or Numeric'
end
```

**Bom:**
```ruby
def combine(val1, val2)
  val1 + val2
end
```
**[⬆ retornar ao topo](#sumário)**

### Remova código morto
Código morto é tão ruim quanto código duplicado. Não há razão para mantê-lo em
sua base de código. Se não está sendo chamado, livre-se dele! Ele ainda estará
seguro no seu histórico de versão se você ainda precisar dele.

**Ruim:**
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

**Bom:**
```ruby
def new_request_module(url)
  # ...
end

req = new_request_module(request_url)
inventory_tracker('apples', req, 'www.inventory-awesome.io')
```
**[⬆ retornar ao topo](#sumário)**

## **Objetos e Estruturas de Dado**
### Use *getters* e *setters*
Usar *getters* e *setters* para acessar informações de objetos poderia ser
melhor do que simplesmente procurar por uma propriedade em um objeto. "Por que?"
você deve perguntar. Bem, aqui está uma lista desorganizada de motivos:

* Quando você quiser fazer algo além de retornar uma propriedade de um objeto,
você não vai precisar procurar e mudar todos os acessos na sua base de código.
* Torna o processo de adicionar validação simples em uma função do tipo `set`.
* Encapsula a representação interna.
* Mais fácil adicionar registro de log e tratamento de errors.
* Você pode fazer um *lazy load* das propriedades de um objeto, por exemplo,
recuperando elas de um servidor.

**Ruim:**
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

**Bom:**
```ruby
class BankAccount
  def initialize
    # Esse é privado
    @balance = 0
  end

  # Um "getter" através de um método público de instância
  def balance
    # Faça alguma registro de log
    @balance
  end

  # Um "setter" através de um método público de instância
  def balance=(amount)
    # Faça alguma registro de log
    # Faça alguma validação
    @balance = amount
  end
end

account = BankAccount.new
account.balance = 100
account.balance # => 100
```

Alternativamente, se seus *getters* e *setters* são absolutamente triviais, você
deveria usar `attr_accessor` para definir eles. Isso é conveniente em especial
para implementar objetos para guardar informação, que expõem dados para outras
partes do sistema (ex: objetos de ActiveRecord, empacotadores de resposta para
APIs remotas).

**Bom:**
```ruby
class Toy
  attr_accessor :price
end

toy = Toy.new
toy.price = 50
toy.price # => 50
```
Contudo, você precisa estar ciente de que em algumas situações, usar
`attr_accessor` é um *code smell*, leia mais sobre
[aqui](http://solnic.eu/2012/04/04/get-rid-of-that-code-smell-attributes.html).

**[⬆ retornar ao topo](#sumário)**

## **Classes**
### Evite Interfaces Fluentes
Uma [Interface Fluente](https://en.wikipedia.org/wiki/Fluent_interface) é uma
API orientada a objetos que tem como objeto melhorar a legibilidade do código
fonte ao usar [encadeamento de métodos](https://en.wikipedia.org/wiki/Method_chaining).

Enquanto existem alguns contextos, frequentemente construtores de objetos, onde
esse padrão reduz a verbosidade do código (ex: Consultas do ActiveRecord), muitas
vezes ele tem alguns custos:

1. Quebra [Encapsulamento](https://en.wikipedia.org/wiki/Encapsulation_%28object-oriented_programming%29)
2. Breaks [Decoradores (*Decorators*)](https://en.wikipedia.org/wiki/Decorator_pattern)
3. É mais difícil de trabalhar com [*mock*](https://en.wikipedia.org/wiki/Mock_object)
em suítes de teste.
4. Faz com que diferenças (*diffs*) entre commits sejam mais difíceis de serem lidas.

Para mais informações sobre isso você pode ler a
[postagem completa](https://ocramius.github.io/blog/fluent-interfaces-are-evil/)
no blog, escrita por [Marco Pivetta](https://github.com/Ocramius) sobre esse
tópico.

**Ruim**
```ruby
class Car
  def initialize(make, model, color)
    @make = make
    @model = model
    @color = color
    # Obs: Retornando self para encadeamento
    self
  end

  def set_make(make)
    @make = make
    # Obs: Retornando self para encadeamento
    self
  end

  def set_model(model)
    @model = model
    # Obs: Retornando self para encadeamento
    self
  end

  def set_color(color)
    @color = color
    # Obs: Retornando self para encadeamento
    self
  end

  def save
    # Salva objeto...
    # Obs: Retornando self para encadeamento
    self
  end
end

car = Car.new('Ford','F-150','red')
  .set_color('pink')
  .save
```

**Bom**
```ruby
class Car
  attr_accessor :make, :model, :color

  def initialize(make, model, color)
    @make = make
    @model = model
    @color = color
  end

  def save
    # Salva objeto...
  end
end

car = Car.new('Ford', 'F-150', 'red')
car.color = 'pink'
car.save
```
**[⬆ retornar ao topo](#sumário)**

### Prefira Composição ao invés de Herança
Essa declaração ficou famosa no livro
Padrões de Design([*Design Patterns*](https://en.wikipedia.org/wiki/Design_Patterns))
escrito pelo Bando dos Quatro (*Gang of Four*). Você deveria preferir composição
ao invés de heranção onde puder. Existem muitas boas razões para usar herança
e muitas boas razões para usar composição. O ponto principal para esse ditado
é que se sua mente vai instintivamente para herança, tente pensar se composição
poderia modelar seu problema melhor. Em alguns casos, ela pode.

Você pode estar se perguntando então, "quando eu deveria usar herança?". Isso
depende no problema em questão, mas essa é uma lista decente sobre quando
usar herança faz mais sentido do que composição:

1. Sua herança representa uma relação do tipo "é uma" e não uma "tem uma"
(Humano->Animal vs. Usuario->DetalhesUsuario).
2. Você pode reutilizar código da classe base
(Humano pode ser mover como todos os animais).
3. Você quer fazer mudanças globais para classes derivadas ao trocar a classe
base (Muda o consumo calórico de todos os animais quando eles se movem).

**Ruim**
```ruby
class Employee
  def initialize(name, email)
    @name = name
    @email = email
  end

  # ...
end

# Ruim porque Employees "tem" informações sobre tax. EmployeeTaxData não é um tipo de Employee
class EmployeeTaxData < Employee
  def initialize(ssn, salary)
    super()
    @ssn = ssn
    @salary = salary
  end

  # ...
end
```

**Bom**
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
    this.name = name
    this.email = email
  end

  def set_tax_data(ssn, salary)
    @tax_data = EmployeeTaxData.new(ssn, salary)
  end
  # ...
end
```
**[⬆ retornar ao topo](#sumário)**

## **SOLID**
### Princípio de Responsabilidade Única (*SRP - Single Responsibility Principle*)
Conforme exposto no livro *Clean Code*, "Nunca deve existir mais do que uma razão
para uma classe mudar". É tentador colocar em uma classe um monte de
funcionalidades atoladas, da mesma forma de quando você só pode levar uma mala
de viagem no seu voo. O problema nisso é que sua classe não será conceitualmente
coesiva e ela terá muitas razões para mudar. Minimizar a quantidade de vezes
que você precisa mudar a classe é importante. É importante porque se muita
funcionalidade está em uma classe e você modifica uma parte dela, pode ser
difícil entender como isso irá afetar os outros módulos que dependem da sua
base de código.

**Ruim**
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

**Bom**
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
**[⬆ retornar ao topo](#sumário)**

###  Princípio Aberto/Fechado (*OCP - Open/Closed Principle*)
Conforme exposto por Bertrand Meyer, "Entidade de software (classes, módulos,
funções, etc.) deveriam estar abertas para extensão, mas fechadas para
modificação." O que isso significa? Esse princípio basicamente especifica que
você deveria permitir um usuário adicionar funcionalidades sem ter que alterar
código que já existe.

**Ruim**
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
    adapter_name = @adapter.name

    if adapter_name == 'ajaxAdapter'
      make_ajax_call(url)
    elsif adapter_name == 'httpNodeAdapter'
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

**Bom**
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
**[⬆ retornar ao topo](#sumário)**

### Princípio de Substituição de Liskov (*LSP - Liskov Substitution Principle*)
Esse é um termo assustador para um conceito muito simples. Ele é formalmente
definido como "Se S é um subtipo de T, então objetos do tipo T podem ser
substituídos por objetos do tipo S sem termos que alterar nenhuma propriedade
desejável daquele programa (exatidão, tarefa executada, etc.)".
Essa é uma definição ainda mais assustadora. Em outras palavras, objetos do tipo
S podem substituir objetos do tipo T.


A melhor explicação para isso é que se você tem uma classe pai e uma classe
filha, então a classe pai pode sempre ser substituída pela classe filha sem
termos resultados incorretos. Isso pode continuar confuso, então vamos dar uma
olhada no exemplo clássico de Quadrado e Retângulo. Matematicamente, um
quadrado é um retângulo, mas se seu modelo está uma relação "é um" através de
herança, você rapidamente se encontra em um problema.

**Ruim**
```ruby
class Rectangle
  def initialize
    @width  = 0
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
    @width  = width
    @height = width
  end

  def height=(height)
    @width  = height
    @height = height
  end
end

def render_large_rectangles(rectangles)
  rectangles.each do |rectangle|
    rectangle.width = 4
    rectangle.height = 5
    area = rectangle.area # Ruim: Retorna 25 para Square. Deveria ser 20.
    rectangle.render(area)
  end
end

rectangles = [Rectangle.new, Rectangle.new, Square.new]
render_large_rectangles(rectangles)
```

**Bom**
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
**[⬆ retornar ao topo](#sumário)**

### Princípio de Segregação de Interface (*ISP - Interface Segregation Principle*)
Ruby não tem interfaces (como em Java, por exemplo), então esse princípio não se
aplica tão rigorosamente como em outras linguagens. No entanto, é importante e
relevante mesmo com a falta de tipos em Ruby.

Esse princípio declara que "Clientes não deveriam ser forçados a dependerem de
interfaces que não usam." Interfaces são contratos implícitos em Ruby por causa
do *duck typing*.

Quando um cliente depende de uma classe que contém interfaces que ele
não usa, mas outros cliente usam, então aquele cliente será afetado por
mudanças que outros clientes forçam sobre a classe das interfaces.

O exemplo a seguir foi tirado [daqui](http://geekhmer.github.io/blog/2015/03/18/interface-segregation-principle-in-ruby/).


**Ruim**
```ruby
class Car
  # usado por Driver
  def open
    # ...
  end

  # usado por Driver
  def start_engine
    # ...
  end

  # usado por Mechanic
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

**Bom**
```ruby
# usado por Driver apenas
class Car
  def open
    # ...
  end

  def start_engine
    # ...
  end
end

# usado por Mechanic apenas
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

**[⬆ retornar ao topo](#sumário)**

### Princípio de Inversão de Dependências (*DIP - Dependency Inversion Principle*)
Esse princípio declara duas coisas essenciais:
1. Módulos de alto nível não deveriam depender de módulos de baixo nível. Ambos
deveriam depender de abstrações.
2. Abstrações não deveriam depender de detalhes. Detalhes deveriam depender de
abstrações.

Colocando de forma simples, esse princípio mantém módulos de alto nível sem
saberem de detalhes de seus módulos de baixo nível e suas configurações. Isso
pode ser alcançado através de inversão de dependências. Um grande benefício disso
é a redução de acoplamento entre módulos. Acoplamento é muito ruim em
padrões de desenvolvimento porque torna seu código difícil de ser refatorado.

Como dito anteriormente, Ruby não tem interfaces, então as abstrações tem
dependências em contratos subentendidos. Isso que dizer os métodos e
propriedades que uma classe/objeto expõem para outra classe/objeto. No exemplo
abaixo, o contrato subentendido é que qualquer módulo de *Request* para um
objeto da classe `InventoryTracker` terá um método `request_items`.

**Ruim**
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

    # Ruim: Nós criamos uma dependência em uma implementação específica de request
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

**Bom**
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

# Ao construir nossas dependências externamente e injeta-las, nós podemos
# facilmente substituir nosso módulo de request por um novo que use WebSockets.
inventory_tracker = InventoryTracker.new(['apples', 'bananas'], InventoryRequesterV2.new)
inventory_tracker.request_items
```
**[⬆ retornar ao topo](#sumário)**

## **Testes**
Testar é mais importante do que colocar em produção. Se você não tem testes ou
tem uma quantidade inadequada, então toda vez que você colocar seu código em
produção você não vai ter certeza que não quebrou algo. Decidir o que constitui
uma quantidade adequada é tarefa da sua equipe, mas ter 100% de cobertura (todas
as declarações e ramos/caminhos) é como você atingi uma confiança muito alta
e paz de espírito como desenvolvedor. Isso significa que além de ter um bom
framework de testes, você também precisa de uma
[boa ferramenta de cobertura](https://coveralls.io/).

Não existem desculpas para não escrever testes. Ruby vem com sua própria
ferramenta de teste, direto com a linguagem. Tenha como objetivo sempre escrever
testes para cada nova funcionalidade ou módulo que você introduzir. Se seu
método preferido é Desenvolvimento Guiado por Testes (TDD), isso é ótimo, mas
o ponto principal é apenas se certificar que você está alcançando suas metas de
cobertura antes de lançar qualquer funcionalidade ou refatorar uma existente.

### Uma Expectativa por Teste

**Ruim:**
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

**Bom:**
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
**[⬆ retornar ao topo](#sumário)**

## **Lidando com Erros**
Erros lançados são uma coisa boa! Eles significam que em tempo de execução foi
identificado quando algo em seu programa deu errado e está permitindo você saber
ao parar a execução de uma função na pilha atual, matando o processo e
notificando você nos logs com um rastreamento de pilha.

### Não ignore erros apanhados
Fazer nada com um erro apanhado não te permite corrigir ou reagir ao erro.
Escrever o erro no log também não é muito melhor, já que muitas vezes ele pode
se perder em um mar de outros registros no log. Se você envolveu qualquer
pedaço de código em um `begin/rescue`, isso significa que você acha que um erro
pode ocorrer ali e portanto você deveria ter um plano, ou criar um caminho com
código, para quando isso ocorrer.

**Ruim**
```ruby
require 'logger'

logger = Logger.new(STDOUT)

begin
  function_that_might_throw()
rescue StandardError => err
  logger.info(err)
end
```

**Bom**
```ruby
require 'logger'

logger = Logger.new(STDOUT)
# Mude o nível do logger para ERROR para mostrar apenas registros de nível ERROR ou superior
logger.level = Logger::ERROR

begin
  function_that_might_throw()
rescue StandardError => err
  # Opção 1: Apenas registros de erros
  logger.error(err)
  # Opção 2: Notifique o usuário final através de uma interface
  notify_user_of_error(err)
  # Opção 3: Reporte o erro para um serviço de terceiro como Honeybadger
  report_error_to_service(err)
  # OU faça todas as três!
end
```

### Forneça Contexto com Exceções
Use um nome de classe de erro descritivo e uma mensagem quando você lançar um
erro. Dessa forma você sabe porque o erro ocorreu e pode resgatar o tipo
específico de erro.

**Ruim**
```ruby
def initialize(user)
  fail unless user
  ...
end
```

**Bom**
```ruby
def initialize(user)
  fail ArgumentError, 'Missing user' unless user
  ...
end
```

**[⬆ retornar ao topo](#sumário)**

## **Formatação**
Formatação é subjetivo. Como muitas regras aqui, não existe uma regra rígida e
rápida que você deve seguir. O ponto principal é NÃO DISCUTA sobre formatação.
Existem diversas ferramentas como [RuboCop](https://github.com/bbatsov/rubocop)
para automatizar isso. Use uma! É uma perda de tempo e dinheiro engenheiros
ficarem discutindo sobre formatação.

Para coisas que não estão no alcance de formatação automática (identação,
tabulação vs. espaços, aspas simples ou duplas, etc.) olhe aqui para alguma
orientação.

### Use Capitalização Consistente
Uma vez que você não declara tipos em Ruby, a capitalização diz muito sobre suas
variáveis, funções, etc. Essas regras são subjetivas, então sua equipe pode
escolher o que ela quiser. O ponto é, não importa o que vocês escolherem, apenas
sejam consistentes.

**Ruim**
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

**Bom**
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
**[⬆ retornar ao topo](#sumário)**

### Invocadores e Invocados deveriam estar Próximos
Se uma função chama outra, mantenha essas funções verticalmente próximas no
arquivo fonte. Idealmente, mantenha o invocador logo acima do invocado. Nós
tendemos a ler código de cima para baixo, como em um jornal. Por causa disso,
faça seu código ler desse jeito.

**Ruim**
```ruby
class PerformanceReview
  def initialize(employee)
    @employee = employee
  end

  def lookup_peers
    db.lookup(@employee, 'peers')
  end

  def lookup_manager
    db.lookup(@employee, 'manager')
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

**Bom**
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
    db.lookup(@employee, 'peers')
  end

  def manager_review
    manager = lookup_manager
    # ...
  end

  def lookup_manager
    db.lookup(@employee, 'manager')
  end

  def self_review
    # ...
  end
end

review = PerformanceReview.new(employee)
review.perf_review
```

**[⬆ retornar ao topo](#sumário)**

## **Comentários**

### Não deixe código comentado na sua base de códigos
Controle de versão existe por um motivo. Deixe código velho no seu histórico.

**Ruim**
```ruby
do_stuff
# do_other_stuff
# do_some_more_stuff
# do_so_much_stuff
```

**Bom**
```ruby
do_stuff
```
**[⬆ retornar ao topo](#sumário)**

### Não tenha comentários de anotações
Lembre-se, use o controle de versão! Não há razão para código morto, código
comentado e especialmente comentários de anotações. Use `git log` para consultar
seu histórico!

**Ruim**
```ruby
# 2016-12-20: Removed monads, didn't understand them (RM)
# 2016-10-01: Improved using special monads (JP)
# 2016-02-03: Removed type-checking (LI)
# 2015-03-14: Added combine with type-checking (JR)
def combine(a, b)
  a + b
end
```

**Bom**
```ruby
def combine(a, b)
  a + b
end
```
**[⬆ retornar ao topo](#sumário)**
