# clean-code-ruby

Conceitos de Código Limpo (*Clean Code*) adaptados para Ruby.

Inspirado por [clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript).

*Observação: Isso ainda é um trabalho em progresso. Os exemplos  aqui são em sua maior parte portados da versão em JavaScript, então eles podem não ser muito idiomático. Sinta-se livre para apontar qualquer código em Ruby que não seja idiomático submetendo uma *issue* e eu irei corrigi-lo imediatamente. Além disso, *pull requests* são sempre bem-vindos!

## Sumário
  1. [Introdução](#introdução)
  2. [Variáveis](#variáveis)
  3. [Funções](#funções)

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
