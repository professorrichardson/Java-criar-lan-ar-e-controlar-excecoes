# Aula 04 — Hierarquia e Exceções Personalizadas em Java

**Curso:** Técnico em Desenvolvimento de Sistemas  
**Turma:** 3º Ano  
**Disciplina:** Programação / Java  
**Professor:** Richardson Schawarski Cruz  

---

# Objetivos da Aula

Ao final desta aula, você deverá ser capaz de:

- Compreender que as exceções fazem parte de uma hierarquia de classes.
- Conhecer a classe `Throwable`.
- Diferenciar, de forma inicial, `Exception` e `Error`.
- Entender de onde vêm métodos como `getMessage()` e `printStackTrace()`.
- Compreender que podemos criar nossas próprias exceções.
- Criar uma exceção personalizada.
- Fazer uma exceção personalizada herdar de `RuntimeException`.
- Criar um construtor para receber uma mensagem.
- Utilizar `super()` para enviar a mensagem para a classe mãe.
- Lançar uma exceção personalizada com `throw`.
- Capturar uma exceção personalizada com `catch`.
- Observar um `StackOverflowError`.

---

# 1. De onde vêm as exceções?

Nas aulas anteriores utilizamos:

```java
ArithmeticException
```

e:

```java
NullPointerException
```

Também utilizamos métodos como:

```java
ex.getMessage();
```

e:

```java
ex.printStackTrace();
```

Mas ainda não respondemos uma pergunta importante:

> De onde vêm essas classes e esses métodos?

Hoje vamos descobrir que as exceções do Java estão organizadas utilizando um conceito que já conhecemos da Orientação a Objetos:

**Herança.**

---

# 2. Relembrando herança

Imagine duas classes:

```java
public class Pessoa {

    public void falar() {
        System.out.println("Olá!");
    }
}
```

E:

```java
public class Aluno extends Pessoa {

}
```

Temos:

```text
Pessoa
  ↑
Aluno
```

A classe `Aluno` herda características da classe `Pessoa`.

Por isso, mesmo sem criar novamente o método:

```java
falar()
```

um objeto `Aluno` poderá utilizá-lo.

```java
Aluno aluno = new Aluno();

aluno.falar();
```

A mesma ideia aparece nas exceções.

---

# 3. A família das exceções

As classes relacionadas às exceções também possuem uma hierarquia.

Podemos começar observando algumas classes que já utilizamos:

```text
Throwable
    │
    └── Exception
            │
            └── RuntimeException
                    │
                    ├── ArithmeticException
                    │
                    └── NullPointerException
```

Observe que:

```java
ArithmeticException
```

é uma `RuntimeException`.

Uma `RuntimeException` é uma `Exception`.

E uma `Exception` é um `Throwable`.

---

# 4. Pensando como uma árvore de família

Podemos imaginar:

```text
Throwable
   ↓
Exception
   ↓
RuntimeException
   ↓
ArithmeticException
```

É como uma árvore de família.

`ArithmeticException` herda de `RuntimeException`.

`RuntimeException` herda de `Exception`.

`Exception` herda de `Throwable`.

Por isso, características definidas nas classes superiores podem ser utilizadas pelas classes que estão abaixo delas.

---

# 5. Throwable

No topo dessa parte da hierarquia encontramos:

```java
Throwable
```

O nome ajuda a entender sua função.

```text
Throwable
↓
algo que pode ser lançado
```

Nas aulas anteriores aprendemos:

```java
throw new ArithmeticException("Algo deu errado");
```

Para um objeto participar desse mecanismo de lançamento de exceções, sua classe precisa fazer parte da hierarquia de `Throwable`.

---

# 6. De onde vem getMessage()?

Na aula anterior utilizamos:

```java
ex.getMessage();
```

Por exemplo:

```java
catch (ArithmeticException ex) {

    System.out.println(ex.getMessage());
}
```

Mas nós nunca criamos o método:

```java
getMessage()
```

dentro de `ArithmeticException`.

Esse comportamento está disponível por causa da hierarquia.

Podemos pensar:

```text
Throwable
   │
   │ possui recursos para trabalhar
   │ com informações da exceção
   ↓
Exception
   ↓
RuntimeException
   ↓
ArithmeticException
```

Por isso podemos utilizar:

```java
ex.getMessage();
```

---

# 7. E printStackTrace()?

A mesma ideia vale para:

```java
ex.printStackTrace();
```

Quando utilizamos:

```java
catch (ArithmeticException ex) {

    ex.printStackTrace();
}
```

conseguimos visualizar o rastro da exceção.

Esses recursos estão disponíveis para as exceções porque elas fazem parte da hierarquia iniciada em `Throwable`.

---

# 8. A hierarquia é maior

Até agora observamos:

```text
Throwable
    │
    └── Exception
```

Mas `Throwable` possui outra parte importante:

```text
Throwable
    │
    ├── Error
    │
    └── Exception
```

Temos, portanto, duas grandes ramificações que precisamos conhecer:

```text
Throwable
├── Error
└── Exception
```

---

# 9. Exception

`Exception` representa a parte da hierarquia que utilizaremos para trabalhar com situações excepcionais em nossos programas.

Até agora conhecemos:

```text
Exception
    │
    └── RuntimeException
            │
            ├── ArithmeticException
            └── NullPointerException
```

Nas próximas aulas vamos aprofundar essa parte da hierarquia.

---

# 10. Error

Também existe:

```java
Error
```

`Error` representa problemas mais graves relacionados à execução da aplicação e à JVM.

Um exemplo que podemos observar é:

```java
StackOverflowError
```

O nome já dá uma pista:

```text
Stack
↓
Pilha

Overflow
↓
Estouro
```

Portanto:

```text
StackOverflowError
↓
Estouro da pilha
```

Mas como podemos estourar a pilha?

Vamos testar.

---

# 11. Criando FluxoComError.java

Dentro do mesmo projeto `java-pilha`, crie:

```text
FluxoComError.java
```

> **IMPORTANTE:** esta classe será utilizada apenas para demonstrar o funcionamento da pilha. Não substitua `Fluxo.java`.

Digite:

```java
// IMPORTANTE:
// Arquivo: FluxoComError.java
//
// Não precisamos instalar ou importar nenhuma biblioteca.
//
// Este código será utilizado propositalmente para
// provocar um StackOverflowError.

public class FluxoComError {

    public static void main(String[] args) {

        System.out.println("Início do main");

        metodo1();

        System.out.println("Fim do main");
    }

    private static void metodo1() {

        System.out.println("Início do metodo1");

        metodo2();

        System.out.println("Fim do metodo1");
    }

    private static void metodo2() {

        System.out.println("Início do metodo2");

        // ATENÇÃO:
        // metodo2() está chamando ele mesmo.
        metodo2();

        System.out.println("Fim do metodo2");
    }
}
```

Antes de executar, observe:

```java
private static void metodo2() {

    metodo2();
}
```

O método está chamando:

**ele mesmo.**

---

# 12. O que acontecerá com a pilha?

Na Aula 01 vimos:

```text
main()
```

Depois:

```text
metodo1()
main()
```

Depois:

```text
metodo2()
metodo1()
main()
```

Mas agora `metodo2()` chama novamente:

```java
metodo2();
```

Então teremos:

```text
metodo2()
metodo2()
metodo1()
main()
```

E novamente:

```text
metodo2()
metodo2()
metodo2()
metodo1()
main()
```

E novamente:

```text
metodo2()
metodo2()
metodo2()
metodo2()
metodo1()
main()
```

O processo continua.

A pilha cresce sem que `metodo2()` consiga terminar.

---

# 13. Executando o exemplo

Execute `FluxoComError`.

O console mostrará várias vezes:

```text
Início do metodo2
Início do metodo2
Início do metodo2
Início do metodo2
...
```

Até aparecer algo semelhante a:

```text
java.lang.StackOverflowError
```

Acabamos de provocar:

```java
StackOverflowError
```

A pilha não possui espaço infinito.

Chega um momento em que não é possível adicionar novas chamadas.

---

# 14. Voltando para a hierarquia

Agora podemos ampliar nosso desenho:

```text
Throwable
│
├── Error
│   │
│   └── StackOverflowError
│
└── Exception
    │
    └── RuntimeException
        │
        ├── ArithmeticException
        │
        └── NullPointerException
```

Não precisamos decorar toda a hierarquia.

O importante neste momento é compreender sua organização.

---

# 15. O foco dos nossos programas

Durante nosso curso, nosso principal interesse estará em:

```text
Exception
```

e suas subclasses.

Não vamos criar nossas regras de negócio utilizando:

```java
Error
```

Para os problemas que queremos representar em nossas aplicações, trabalharemos com exceções.

---

# 16. Um problema com nossa Conta

Na aula anterior tínhamos a classe `Conta`.

Nosso método `saca()` verificava:

```java
if (valor > saldo) {

    throw new ArithmeticException("Saldo insuficiente");
}
```

Funcionava.

Mas existe algo estranho.

Observe o tipo utilizado:

```java
ArithmeticException
```

Essa exceção normalmente representa problemas relacionados a operações aritméticas.

Mas nosso problema é:

```text
Saldo insuficiente
```

Isso é uma regra do nosso sistema.

Então surge uma pergunta:

> Precisamos obrigatoriamente utilizar somente as exceções que o Java já possui?

Não.

Podemos criar nossas próprias exceções.

---

# 17. Criando nossa primeira exceção

Crie uma nova classe chamada:

```text
MinhaExcecao.java
```

Comece com:

```java
public class MinhaExcecao {

}
```

Temos uma classe Java normal.

Mas ainda existe um problema.

Queremos fazer:

```java
throw new MinhaExcecao();
```

Será que funciona?

Vamos testar.

---

# 18. Tentando lançar uma classe comum

Volte temporariamente para `Fluxo.java`.

Dentro de `metodo2()`, tente:

```java
throw new MinhaExcecao();
```

O IntelliJ indicará um problema.

Por quê?

Porque `MinhaExcecao` é apenas uma classe comum.

Ela ainda não pertence à hierarquia:

```text
Throwable
```

Para uma classe ser utilizada dessa maneira, ela precisa fazer parte da hierarquia de exceções.

---

# 19. Fazendo nossa classe ser uma exceção

Volte para:

```text
MinhaExcecao.java
```

Altere para:

```java
// IMPORTANTE:
// Arquivo: MinhaExcecao.java
//
// Não precisamos importar RuntimeException.
// Ela pertence ao pacote java.lang, que é
// disponibilizado automaticamente pelo Java.

public class MinhaExcecao extends RuntimeException {

}
```

Observe:

```java
extends RuntimeException
```

Agora nossa classe faz parte desta hierarquia:

```text
Throwable
    ↓
Exception
    ↓
RuntimeException
    ↓
MinhaExcecao
```

Criamos nossa primeira exceção personalizada.

---

# 20. Por que RuntimeException?

Nesta aula utilizaremos:

```java
extends RuntimeException
```

Isso permitirá que continuemos trabalhando com `throw` sem introduzir ainda novas regras de compilação.

> **IMPORTANTE:** existe uma diferença importante entre criar uma exceção que estende `RuntimeException` e uma que estende diretamente `Exception`.

Essa diferença será assunto da próxima aula.

Por enquanto, mantenha:

```java
extends RuntimeException
```

---

# 21. Lançando MinhaExcecao

Agora volte para:

```text
Fluxo.java
```

Altere `metodo2()` para:

```java
private static void metodo2() {

    System.out.println("Início do metodo2");

    // Agora podemos lançar nossa própria exceção.
    throw new MinhaExcecao();
}
```

Execute.

Nossa exceção será lançada.

Mas ainda temos um problema.

Não adicionamos nenhuma mensagem.

---

# 22. Queremos fazer isso

Queremos escrever:

```java
throw new MinhaExcecao("Algo deu errado");
```

Ou, pensando em um sistema real:

```java
throw new MinhaExcecao("Saldo insuficiente");
```

Para isso, nossa classe precisa possuir um construtor que receba essa mensagem.

---

# 23. Criando o construtor

Volte para:

```text
MinhaExcecao.java
```

Adicione:

```java
public MinhaExcecao(String mensagem) {

}
```

Nossa classe ficará:

```java
public class MinhaExcecao extends RuntimeException {

    public MinhaExcecao(String mensagem) {

    }
}
```

Agora podemos receber uma `String`.

Mas precisamos fazer algo com ela.

---

# 24. Para onde enviar a mensagem?

Lembre da hierarquia:

```text
Throwable
    ↓
Exception
    ↓
RuntimeException
    ↓
MinhaExcecao
```

Nossa classe está herdando de:

```java
RuntimeException
```

Queremos aproveitar o comportamento que já existe na classe mãe para armazenar a mensagem.

Para isso utilizamos:

```java
super()
```

---

# 25. Utilizando super()

Altere o construtor:

```java
public MinhaExcecao(String mensagem) {

    super(mensagem);
}
```

Código completo:

```java
// IMPORTANTE:
// Arquivo: MinhaExcecao.java
//
// Esta exceção estende RuntimeException.
// Não altere para Exception nesta aula.
//
// Essa alteração será feita posteriormente
// para estudarmos outro comportamento do Java.

public class MinhaExcecao extends RuntimeException {

    public MinhaExcecao(String mensagem) {

        // Envia a mensagem recebida para
        // o construtor da classe mãe.
        super(mensagem);
    }
}
```

---

# 26. O que super(mensagem) está fazendo?

Podemos pensar assim:

```text
MinhaExcecao
     │
     │ recebe "Saldo insuficiente"
     ↓
super(mensagem)
     │
     ↓
RuntimeException
```

Estamos enviando a mensagem para o construtor da classe mãe.

A hierarquia do Java cuidará do armazenamento dessa informação.

Depois poderemos utilizar:

```java
ex.getMessage();
```

para recuperá-la.

---

# 27. Analogia — Herdando uma estrutura pronta

Imagine que estamos construindo um tipo específico de formulário.

A empresa já possui um formulário padrão com:

```text
Nome
Data
Descrição
```

Em vez de criar tudo novamente, criamos um formulário especializado utilizando a estrutura que já existe.

Nossa exceção personalizada segue uma ideia parecida.

Não precisamos criar novamente:

```java
getMessage()
```

nem:

```java
printStackTrace()
```

Aproveitamos a estrutura existente através da herança.

---

# 28. Lançando a exceção com mensagem

Agora volte para `Fluxo.java`.

Altere:

```java
throw new MinhaExcecao();
```

para:

```java
throw new MinhaExcecao("Algo deu errado em metodo2");
```

Nosso método ficará:

```java
private static void metodo2() {

    System.out.println("Início do metodo2");

    // Criamos e lançamos nossa própria exceção.
    throw new MinhaExcecao("Algo deu errado em metodo2");
}
```

---

# 29. Precisamos capturar nossa nova exceção

Nosso `main()` já possuía:

```java
catch (ArithmeticException | NullPointerException ex)
```

Mas agora estamos lançando:

```java
MinhaExcecao
```

Vamos adicioná-la ao multi-catch.

```java
catch (ArithmeticException | NullPointerException | MinhaExcecao ex)
```

Agora o mesmo tratamento poderá receber qualquer uma dessas três exceções.

---

# 30. Fluxo.java completo

Ao final desta etapa, `Fluxo.java` ficará:

```java
// IMPORTANTE:
// Arquivo: Fluxo.java
//
// MinhaExcecao.java deve existir no mesmo projeto.
//
// Nesta aula MinhaExcecao deve estender
// RuntimeException.
//
// Não adicione "throws MinhaExcecao" aos métodos.
// Isso será estudado posteriormente.

public class Fluxo {

    public static void main(String[] args) {

        System.out.println("Início do main");

        try {

            metodo1();

        } catch (ArithmeticException |
                 NullPointerException |
                 MinhaExcecao ex) {

            // Recuperamos a mensagem armazenada
            // no objeto da exceção.
            String mensagem = ex.getMessage();

            System.out.println("Ocorreu uma exceção.");
            System.out.println("Mensagem: " + mensagem);

            // Como MinhaExcecao faz parte da hierarquia
            // de Throwable, também podemos visualizar
            // seu stack trace.
            ex.printStackTrace();
        }

        System.out.println("Fim do main");
    }

    private static void metodo1() {

        System.out.println("Início do metodo1");

        metodo2();

        // Esta linha não será executada
        // quando metodo2() lançar a exceção.
        System.out.println("Fim do metodo1");
    }

    private static void metodo2() {

        System.out.println("Início do metodo2");

        // Criamos e lançamos nossa própria exceção.
        throw new MinhaExcecao(
                "Algo deu errado em metodo2"
        );
    }
}
```

---

# 31. Execute o programa

O resultado deverá ser semelhante a:

```text
Início do main
Início do metodo1
Início do metodo2
Ocorreu uma exceção.
Mensagem: Algo deu errado em metodo2
...
Fim do main
```

Também aparecerá o stack trace por causa de:

```java
ex.printStackTrace();
```

Observe uma coisa importante.

Agora o stack trace apresentará nossa própria classe:

```text
MinhaExcecao
```

Criamos uma exceção que participa do mesmo mecanismo utilizado pelas exceções do Java.

---

# 32. Nossa hierarquia agora

Podemos atualizar nosso desenho:

```text
Throwable
│
├── Error
│   │
│   └── StackOverflowError
│
└── Exception
    │
    └── RuntimeException
        │
        ├── ArithmeticException
        │
        ├── NullPointerException
        │
        └── MinhaExcecao
```

Nossa classe agora faz parte da hierarquia.

---

# 33. Melhorando nossa Conta

Na aula anterior tínhamos algo semelhante a:

```java
if (valor > saldo) {

    throw new ArithmeticException("Saldo insuficiente");
}
```

Agora já podemos substituir uma exceção genérica para esse contexto por nossa própria exceção.

Abra:

```text
Conta.java
```

Deixe a classe assim:

```java
// IMPORTANTE:
// Arquivo: Conta.java
//
// MinhaExcecao.java precisa existir no projeto.
//
// Nesta aula MinhaExcecao continua estendendo
// RuntimeException.

public class Conta {

    private double saldo = 500;

    public void deposita(double valor) {

        saldo = saldo + valor;
    }

    public void saca(double valor) {

        // Verificamos nossa regra de negócio.
        if (valor > saldo) {

            // Em vez de utilizar ArithmeticException,
            // utilizamos nossa própria exceção.
            throw new MinhaExcecao("Saldo insuficiente");
        }

        saldo = saldo - valor;
    }

    public double getSaldo() {

        return saldo;
    }
}
```

Agora:

```java
MinhaExcecao
```

está representando uma situação definida por nosso próprio sistema.

---

# 34. Testando nossa exceção personalizada

Utilize `TesteConta.java`:

```java
public class TesteConta {

    public static void main(String[] args) {

        Conta conta = new Conta();

        System.out.println(
                "Saldo inicial: " + conta.getSaldo()
        );

        try {

            conta.saca(800);

        } catch (MinhaExcecao ex) {

            System.out.println(
                    "Não foi possível realizar o saque."
            );

            System.out.println(
                    "Motivo: " + ex.getMessage()
            );
        }

        System.out.println(
                "Saldo final: " + conta.getSaldo()
        );
    }
}
```

Resultado esperado:

```text
Saldo inicial: 500.0
Não foi possível realizar o saque.
Motivo: Saldo insuficiente
Saldo final: 500.0
```

---

# 35. Quem faz o quê?

Agora temos três responsabilidades diferentes.

### Conta

Detecta a situação:

```java
if (valor > saldo)
```

e lança:

```java
throw new MinhaExcecao("Saldo insuficiente");
```

### MinhaExcecao

Representa o problema:

```java
public class MinhaExcecao
        extends RuntimeException
```

### TesteConta

Decide como tratar:

```java
catch (MinhaExcecao ex)
```

Podemos visualizar:

```text
Conta
  ↓
detecta o problema
  ↓
throw
  ↓
MinhaExcecao
  ↓
percorre a pilha
  ↓
TesteConta
  ↓
catch
  ↓
tratamento
```

---

# 36. Uma exceção com nome melhor

`MinhaExcecao` é um bom nome enquanto estamos aprendendo como criar a estrutura.

Mas imagine um sistema real.

Poderíamos possuir algo como:

```text
SaldoInsuficienteException
```

O próprio nome já explicaria o problema.

Por exemplo:

```java
throw new SaldoInsuficienteException(
        "Saldo insuficiente"
);
```

Nesta aula continuaremos utilizando:

```java
MinhaExcecao
```

para manter a sequência do nosso projeto.

Mais adiante poderemos criar exceções com nomes específicos para as regras do sistema.

---

# 37. Debugando nossa exceção

Coloque um breakpoint em:

```java
throw new MinhaExcecao("Saldo insuficiente");
```

Execute `TesteConta` utilizando **Debug**.

Antes do `throw`, observe:

```text
saldo = 500.0
valor = 800.0
```

Observe também os **Frames / Call Stack**.

Você deverá encontrar algo semelhante a:

```text
saca()
main()
```

Execute a linha:

```java
throw new MinhaExcecao("Saldo insuficiente");
```

O fluxo normal de `saca()` será interrompido.

A exceção chegará ao:

```java
catch (MinhaExcecao ex)
```

Depois disso, observe:

```java
ex.getMessage()
```

A mensagem disponível será:

```text
Saldo insuficiente
```

Isso acontece porque utilizamos:

```java
super(mensagem);
```

no construtor de `MinhaExcecao`.

---

# 38. O que não devemos fazer ainda

Nossa classe deve continuar:

```java
public class MinhaExcecao
        extends RuntimeException
```

Não altere para:

```java
extends Exception
```

ainda.

Também não adicione:

```java
throws MinhaExcecao
```

aos métodos.

Essas mudanças provocam um comportamento diferente no compilador Java.

Esse será justamente o assunto da próxima aula.

---

# Desafio 01 — Produto sem estoque

Crie uma classe:

```text
Produto.java
```

Ela deverá possuir:

```java
private int estoque = 5;
```

Crie o método:

```java
public void vender(int quantidade)
```

Antes de diminuir o estoque, verifique:

```java
quantidade > estoque
```

Se a quantidade solicitada for maior que o estoque, lance:

```java
MinhaExcecao
```

com a mensagem:

```text
Estoque insuficiente
```

Se houver estoque, diminua normalmente:

```java
estoque = estoque - quantidade;
```

Crie também:

```java
public int getEstoque()
```

para consultar o estoque atual.

Depois crie:

```text
TesteProduto.java
```

Tente vender:

```text
8 unidades
```

Utilize `try-catch` para tratar `MinhaExcecao`.

Mostre a mensagem utilizando:

```java
ex.getMessage();
```

> **IMPORTANTE:** utilize a `MinhaExcecao` que já existe no projeto. Não crie outra exceção.

---

# Desafio 02 — Criando sua própria exceção

Agora você deverá criar uma segunda exceção personalizada.

Crie:

```text
IdadeInvalidaException.java
```

Ela deverá:

1. herdar de `RuntimeException`;
2. possuir um construtor que receba uma `String`;
3. enviar essa mensagem para a classe mãe utilizando `super()`.

Depois crie:

```text
Cadastro.java
```

Crie um método:

```java
public static void validarIdade(int idade)
```

Quando:

```text
idade < 18
```

lance:

```java
IdadeInvalidaException
```

com a mensagem:

```text
É necessário ter 18 anos ou mais
```

No `main()`, teste:

```java
validarIdade(16);
```

Capture a exceção utilizando:

```java
catch (IdadeInvalidaException ex)
```

e mostre sua mensagem.

No final do arquivo, responda:

```java
/*
RESPONDA:

1. De qual classe IdadeInvalidaException
   está herdando?

2. Para que serve super(mensagem)?

3. Por que conseguimos utilizar
   ex.getMessage() mesmo sem criar
   getMessage() dentro da nossa classe?

4. Qual palavra utilizamos para
   lançar uma exceção?
*/
```

---

# Resumo da Aula

Hoje descobrimos que as exceções possuem uma hierarquia.

```text
Throwable
│
├── Error
│   └── StackOverflowError
│
└── Exception
    └── RuntimeException
        ├── ArithmeticException
        ├── NullPointerException
        └── MinhaExcecao
```

Vimos que:

```java
Throwable
```

está no topo da hierarquia utilizada pelos objetos que podem ser lançados.

Também entendemos por que podemos utilizar recursos como:

```java
ex.getMessage();
```

e:

```java
ex.printStackTrace();
```

Criamos nossa própria exceção:

```java
public class MinhaExcecao
        extends RuntimeException {

    public MinhaExcecao(String mensagem) {

        super(mensagem);
    }
}
```

Depois lançamos:

```java
throw new MinhaExcecao(
        "Saldo insuficiente"
);
```

E tratamos:

```java
catch (MinhaExcecao ex) {

    System.out.println(ex.getMessage());
}
```

Assim, nosso programa deixou de depender apenas das exceções prontas do Java.

Agora podemos representar problemas específicos dos nossos próprios sistemas.

---

# Próxima aula

Existe uma mudança aparentemente pequena:

```java
extends RuntimeException
```

para:

```java
extends Exception
```

Mas essa alteração fará o compilador Java começar a exigir algo que ele não exige atualmente.

Na próxima aula vamos descobrir o motivo e conhecer:

```text
Checked Exceptions
```

```text
Unchecked Exceptions
```

e a palavra-chave:

```java
throws
```

Não altere `MinhaExcecao` antes da próxima aula.