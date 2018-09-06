# clean-code-ruby

Conceitos de Código Limpo (*Clean Code*) adaptado para Ruby.

Inspirado por [clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript).

*Observação: Isso ainda é um trabalho em progresso. Os exemplos  aqui são em sua maior parte portados da versão em JavaScript, então eles podem não ser muito idiomático. Sinta-se livre para apontar qualquer código em Ruby que não seja idiomático submetendo uma *issue* e eu irei corrigi-lo imediatamente. Além disso, *pull requests* são sempre bem-vindos!

## Sumário
  1. [Introdução](#introdução)
  2. [Variáveis](#variáveis)

  ## Introdução
Imagem bem-humorada sobre estimativa de qualidade de software de acordo com
quantos palavrões você solta enquanto está lendo código.

![Imagem](http://www.osnews.com/images/comics/wtfm.jpg)

Princípios de Engenharia de Software, do livro de Robert C. Martin
[*Clean Code*](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882),
adaptado para Ruby. Isso não é um guia de estilo. É um guia para produzir software
[legível, reutilizável, e refatorável](https://github.com/ryanmcdermott/3rs-of-software-architecture) em Ruby.

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
