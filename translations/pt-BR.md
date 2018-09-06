# clean-code-ruby

Conceitos de Código Limpo (*Clean Code*) adaptado para Ruby.

Inspirado por [clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript).

*Observação: Isso ainda é um trabalho em progresso. Os exemplos  aqui são em sua maior parte portados da versão em JavaScript, então eles podem não ser muito idiomático. Sinta-se livre para apontar qualquer código em Ruby que não seja idiomático submetendo uma *issue* e eu irei corrigi-lo imediatamente. Além disso, *pull requests* são sempre bem-vindos!

## Sumário
  1. [Introdução](#introdução)
  2. [Variáveis](#variáveis)
  
  ## Introdução
Imagem bem humorada sobre estimativa de qualidade de software de acordo com quantos palavrões você solta enquanto está lendo código.

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
