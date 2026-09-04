# Aula 05 — Checked e Unchecked Exceptions em Java

**Curso:** Técnico em Desenvolvimento de Sistemas  
**Turma:** 3º Ano  
**Disciplina:** Programação / Java  
**Professor:** Richardson Schawarski Cruz  

---

# Objetivos da Aula

Ao final desta aula, você deverá ser capaz de:

- Diferenciar `Error` de `Exception`.
- Compreender a diferença entre exceções **Checked** e **Unchecked**.
- Entender o papel de `RuntimeException`.
- Entender por que algumas exceções exigem tratamento no código.
- Utilizar `throws` na assinatura de métodos.
- Escolher entre **propagar** ou **tratar** uma exceção.
- Utilizar `try-catch` com exceções checked.
- Compreender o uso de `catch(Exception ex)`.
- Relacionar checked e unchecked com a hierarquia estudada anteriormente.

---

# 1. Retomando a aula anterior

Na aula anterior conhecemos a hierarquia:

```text
Throwable
│
├── Error
│
└── Exception
    │
    └── RuntimeException
```

Também criamos:

```java
public class MinhaExcecao extends RuntimeException {

    public MinhaExcecao(String mensagem) {
        super(mensagem);
    }
}
```

Neste formato, nossa exceção funcionava normalmente.

Podíamos fazer:

```java
throw new MinhaExcecao("Algo deu errado");
```

sem adicionar nada à assinatura do método.

Hoje vamos fazer uma alteração aparentemente pequena:

```java
extends RuntimeException
```

vai se transformar em:

```java
extends Exception
```

E essa mudança fará o compilador Java começar a exigir algo novo.

---

# 2. Error não é Exception

Antes de continuar, precisamos organizar os termos.

No dia a dia é comum dizer:

> "Deu um erro no programa."

Mas no Java existem conceitos diferentes.

Na hierarquia:

```text
Throwable
│
├── Error
│
└── Exception
```

`Error` normalmente está relacionado a problemas graves da execução ou da JVM.

Na aula anterior vimos:

```java
StackOverflowError
```

Já situações que nosso programa pode prever e tratar normalmente estão relacionadas a:

```java
Exception
```

Por isso, ao estudar `try`, `catch`, `throw` e `throws`, trabalharemos principalmente com **exceções**.

---

# 3. Duas categorias importantes

Dentro das exceções, podemos fazer uma divisão importante:

```text
Exception
│
├── RuntimeException
│   └── Unchecked
│
└── Outras Exception
    └── Checked
```

Temos então:

```text
Checked Exceptions
```

e:

```text
Unchecked Exceptions
```

A principal diferença entre elas aparece durante a **compilação**.

---

# 4. Unchecked Exception

Uma exceção é normalmente considerada **unchecked** quando pertence à hierarquia de:

```java
RuntimeException
```

Exemplos que já utilizamos:

```java
ArithmeticException
```

e:

```java
NullPointerException
```

Hierarquia simplificada:

```text
Exception
    ↓
RuntimeException
    ↓
ArithmeticException
```

ou:

```text
Exception
    ↓
RuntimeException
    ↓
NullPointerException
```

---

# 5. O que significa unchecked?

Podemos traduzir a ideia como:

```text
Unchecked
↓
não verificada obrigatoriamente pelo compilador
```

Isso não significa que a exceção não existe.

Significa apenas que o compilador **não obriga** o programador a:

```text
tratar com try-catch
```

ou:

```text
declarar com throws
```

Por exemplo:

```java
public static void dividir() {

    int resultado = 10 / 0;
}
```

Esse código pode ser compilado.

A `ArithmeticException` aparecerá durante a execução.

---

# 6. Nossa exceção também era unchecked

Na aula anterior tínhamos:

```java
public class MinhaExcecao extends RuntimeException {
```

Como ela estendia:

```java
RuntimeException
```

ela também era uma:

```text
Unchecked Exception
```

Podíamos escrever:

```java
private static void metodo2() {

    throw new MinhaExcecao("Algo deu errado");
}
```

sem precisar informar nada na assinatura.

---

# 7. Transformando nossa exceção

Abra:

```text
MinhaExcecao.java
```

Na aula anterior tínhamos:

```java
public class MinhaExcecao extends RuntimeException {

    public MinhaExcecao(String mensagem) {

        super(mensagem);
    }
}
```

Agora altere apenas:

```java
RuntimeException
```

para:

```java
Exception
```

Código:

```java
// IMPORTANTE:
// Arquivo: MinhaExcecao.java
//
// Nesta aula estamos alterando RuntimeException
// para Exception propositalmente.
//
// Essa mudança transforma nossa exceção
// em uma Checked Exception.

public class MinhaExcecao extends Exception {

    public MinhaExcecao(String mensagem) {

        // Envia a mensagem para a classe mãe.
        super(mensagem);
    }
}
```

---

# 8. Algo mudou

Volte para `Fluxo.java`.

Se tivermos:

```java
private static void metodo2() {

    throw new MinhaExcecao("Algo deu errado");
}
```

o IntelliJ indicará um problema.

Agora o código não compila normalmente.

Mas por quê?

A única alteração foi:

```text
RuntimeException
```

para:

```text
Exception
```

---

# 9. Checked Exception

Quando nossa classe passa a estender diretamente:

```java
Exception
```

ela passa a ser uma:

```text
Checked Exception
```

Nesse caso o compilador exige que façamos uma escolha.

Precisamos:

```text
TRATAR
```

a exceção.

Ou:

```text
DECLARAR / PROPAGAR
```

que o método pode lançá-la.

---

# 10. As duas possibilidades

Quando temos uma checked exception:

```java
MinhaExcecao
```

podemos escolher:

```text
Opção 1
↓
try-catch
↓
tratar aqui
```

ou:

```text
Opção 2
↓
throws
↓
informar que este método
pode lançar a exceção
```

Essa escolha será feita dependendo da responsabilidade de cada método.

---

# 11. Conhecendo throws

Até agora utilizamos:

```java
throw
```

Hoje conheceremos:

```java
throws
```

Os nomes são parecidos, mas possuem funções diferentes.

---

# 12. throw x throws

`throw`:

```java
throw new MinhaExcecao("Algo deu errado");
```

significa:

> Lance esta exceção agora.

Já:

```java
throws MinhaExcecao
```

na assinatura de um método significa:

> Este método pode lançar ou propagar esta exceção.

Exemplo:

```java
private static void metodo2() throws MinhaExcecao {
```

---

# 13. Corrigindo metodo2()

Vamos alterar:

```java
private static void metodo2() {
```

para:

```java
private static void metodo2() throws MinhaExcecao {
```

Código:

```java
private static void metodo2() throws MinhaExcecao {

    System.out.println("Início do metodo2");

    throw new MinhaExcecao("Algo deu errado");
}
```

Agora estamos dizendo ao compilador:

> `metodo2()` pode lançar `MinhaExcecao`.

---

# 14. Mas o problema apareceu em metodo1()

Observe:

```java
private static void metodo1() {

    metodo2();
}
```

`metodo1()` está chamando um método que pode lançar:

```java
MinhaExcecao
```

Então o compilador pergunta novamente:

> E agora? Quem vai cuidar dessa exceção?

Temos novamente as mesmas duas possibilidades:

```text
tratar
```

ou:

```text
propagar
```

---

# 15. Propagando novamente

Vamos escolher propagar.

Altere:

```java
private static void metodo1() {
```

para:

```java
private static void metodo1() throws MinhaExcecao {
```

Código:

```java
private static void metodo1() throws MinhaExcecao {

    System.out.println("Início do metodo1");

    metodo2();

    System.out.println("Fim do metodo1");
}
```

Agora:

```text
metodo2()
    ↓
throws MinhaExcecao
    ↓
metodo1()
    ↓
throws MinhaExcecao
```

A exceção está sendo propagada.

---

# 16. A exceção chegou ao main()

Temos agora:

```text
metodo2()
    ↓
throws
    ↓
metodo1()
    ↓
throws
    ↓
main()
```

No `main()` podemos continuar propagando.

Mas vamos escolher tratar.

---

# 17. Tratando no main()

Use:

```java
try {

    metodo1();

} catch (MinhaExcecao ex) {

    System.out.println(
        "Ocorreu uma exceção: " + ex.getMessage()
    );
}
```

Agora temos:

```text
metodo2()
    ↓
propaga
    ↓
metodo1()
    ↓
propaga
    ↓
main()
    ↓
catch
    ↓
tratamento
```

---

# 18. Código completo

Nosso `Fluxo.java` pode ficar assim:

```java
// IMPORTANTE:
// Arquivo: Fluxo.java
//
// MinhaExcecao agora estende Exception.
//
// Por isso precisamos tratar a exceção
// ou declarar sua propagação com throws.

public class Fluxo {

    public static void main(String[] args) {

        System.out.println("Início do main");

        try {

            metodo1();

        } catch (MinhaExcecao ex) {

            String mensagem = ex.getMessage();

            System.out.println(
                "Ocorreu uma exceção."
            );

            System.out.println(
                "Mensagem: " + mensagem
            );

            ex.printStackTrace();
        }

        System.out.println("Fim do main");
    }

    private static void metodo1()
            throws MinhaExcecao {

        System.out.println("Início do metodo1");

        // metodo2() pode lançar MinhaExcecao.
        metodo2();

        System.out.println("Fim do metodo1");
    }

    private static void metodo2()
            throws MinhaExcecao {

        System.out.println("Início do metodo2");

        // Lançamos a exceção.
        throw new MinhaExcecao(
            "Algo deu errado em metodo2"
        );
    }
}
```

---

# 19. O que significa throws?

Observe:

```java
private static void metodo2()
        throws MinhaExcecao
```

`throws` não lança a exceção.

Quem lança é:

```java
throw new MinhaExcecao(...);
```

`throws` apenas declara:

> Este método pode gerar ou propagar essa exceção.

---

# 20. Uma analogia

Imagine um funcionário recebendo um problema.

Ele pode:

```text
resolver o problema
```

ou:

```text
encaminhar para seu superior
```

No Java:

```text
try-catch
↓
resolver aqui
```

e:

```text
throws
↓
encaminhar a responsabilidade
```

---

# 21. Outra maneira: tratar imediatamente

Nós não somos obrigados a propagar.

Dentro de `metodo1()`, poderíamos fazer:

```java
private static void metodo1() {

    System.out.println("Início do metodo1");

    try {

        metodo2();

    } catch (MinhaExcecao ex) {

        System.out.println(
            "Problema tratado em metodo1."
        );
    }

    System.out.println("Fim do metodo1");
}
```

Nesse caso:

```text
metodo2()
    ↓
MinhaExcecao
    ↓
metodo1()
    ↓
catch
```

A exceção é tratada ali.

Não precisa continuar até o `main()`.

---

# 22. Regra prática

Quando uma checked exception aparece, pense:

> Este método sabe o que fazer com esse problema?

Se a resposta for:

```text
SIM
```

podemos usar:

```java
try-catch
```

Se for:

```text
NÃO
```

podemos declarar:

```java
throws
```

e deixar outro método decidir.

---

# 23. Checked x Unchecked

Agora podemos comparar.

## Unchecked

Exemplo:

```java
RuntimeException
```

ou classes que herdam dela.

O compilador:

```text
NÃO obriga
```

o uso de:

```java
try-catch
```

ou:

```java
throws
```

---

## Checked

Exemplo:

```java
Exception
```

ou uma classe que estende diretamente `Exception`.

O compilador:

```text
OBRIGA
```

o programador a:

```text
tratar
```

ou:

```text
declarar
```

---

# 24. Na execução existe diferença?

Esta parte é importante.

Quando uma exceção é lançada:

```java
throw new MinhaExcecao(...)
```

o fluxo é interrompido.

Ela percorre a pilha.

Procura um `catch`.

Isso acontece tanto com checked quanto com unchecked.

A principal diferença estudada hoje está no:

```text
COMPILADOR
```

---

# 25. Voltando para a Conta

Nossa classe `Conta` pode possuir:

```java
void deposita() throws MinhaExcecao {

    // código omitido
}
```

Agora qualquer código que chamar:

```java
conta.deposita();
```

precisará decidir:

```text
vou tratar?
```

ou:

```text
vou propagar?
```

---

# 26. Criando um teste

Crie ou utilize:

```text
TestaContaComExcecaoChecked.java
```

Código:

```java
// IMPORTANTE:
// Arquivo: TestaContaComExcecaoChecked.java
//
// Conta.deposita() declara throws MinhaExcecao.
// Por isso precisamos tratar ou propagar.

public class TestaContaComExcecaoChecked {

    public static void main(String[] args) {

        Conta conta = new Conta();

        try {

            conta.deposita();

        } catch (MinhaExcecao ex) {

            System.out.println(
                "A exceção foi tratada."
            );

            System.out.println(
                ex.getMessage()
            );
        }
    }
}
```

Aqui escolhemos:

```text
TRATAR
```

a exceção.

---

# 27. E se retirarmos o try-catch?

Tente temporariamente:

```java
public static void main(String[] args) {

    Conta conta = new Conta();

    conta.deposita();
}
```

O IntelliJ deverá indicar um problema.

Isso acontece porque:

```java
deposita()
```

declara:

```java
throws MinhaExcecao
```

e `MinhaExcecao` é checked.

O compilador quer saber:

> Quem será responsável por essa possível exceção?

---

# 28. Outra solução

Podemos também escrever:

```java
public static void main(String[] args)
        throws MinhaExcecao {

    Conta conta = new Conta();

    conta.deposita();
}
```

Agora o próprio `main()` está declarando:

```java
throws MinhaExcecao
```

Isso faz o programa compilar.

Mas se a exceção realmente acontecer e ninguém tratá-la, ela chegará até a JVM e será exibida no console.

---

# 29. Qual abordagem é melhor?

Não existe uma única resposta para todos os casos.

Se o método sabe como reagir:

```text
try-catch
```

pode fazer sentido.

Se não sabe:

```text
throws
```

pode deixar a responsabilidade para quem chamou.

O importante é compreender que checked exceptions tornam essa decisão **obrigatória para o programador**.

---

# 30. Um catch mais genérico

Até agora utilizamos coisas como:

```java
catch (ArithmeticException ex)
```

ou:

```java
catch (NullPointerException ex)
```

ou:

```java
catch (MinhaExcecao ex)
```

Também podemos utilizar uma classe superior da hierarquia:

```java
catch (Exception ex)
```

---

# 31. Por que funciona?

Observe:

```text
Exception
│
├── RuntimeException
│   ├── ArithmeticException
│   └── NullPointerException
│
└── MinhaExcecao
```

Se todas essas classes são tipos de:

```java
Exception
```

então um:

```java
catch (Exception ex)
```

pode capturar qualquer uma delas.

---

# 32. Alterando Fluxo.java

Podemos substituir:

```java
catch (ArithmeticException |
       NullPointerException |
       MinhaExcecao ex)
```

por:

```java
catch (Exception ex)
```

Exemplo:

```java
try {

    metodo1();

} catch (Exception ex) {

    System.out.println(
        "Ocorreu uma exceção."
    );

    System.out.println(
        ex.getMessage()
    );

    ex.printStackTrace();
}
```

---

# 33. O catch genérico resolve tudo?

Tecnicamente:

```java
catch (Exception ex)
```

pode capturar muitas exceções diferentes.

Mas isso não significa que devemos utilizar sempre um tratamento genérico.

Imagine:

```text
Saldo insuficiente
```

e:

```text
Arquivo não encontrado
```

Talvez nosso sistema precise reagir de maneiras diferentes.

Por isso:

```java
catch (Exception ex)
```

é útil em algumas situações, mas tratamentos específicos continuam sendo importantes.

---

# 34. Pensando na hierarquia

Podemos usar:

```java
catch (MinhaExcecao ex)
```

mais específico.

Ou:

```java
catch (Exception ex)
```

mais genérico.

É semelhante ao conceito de herança que já conhecemos.

Quanto mais alto subimos na hierarquia, mais tipos conseguimos representar.

---

# 35. Código final de MinhaExcecao.java

```java
// IMPORTANTE:
// Arquivo: MinhaExcecao.java
//
// Agora nossa exceção é CHECKED
// porque estende diretamente Exception.

public class MinhaExcecao extends Exception {

    public MinhaExcecao(String mensagem) {

        super(mensagem);
    }
}
```

---

# 36. Código final de Conta.java

```java
// IMPORTANTE:
// Arquivo: Conta.java
//
// Como MinhaExcecao agora é checked,
// precisamos declarar throws MinhaExcecao.

public class Conta {

    public void deposita()
            throws MinhaExcecao {

        // Código omitido para este exemplo.
    }
}
```

---

# 37. Código final de TestaContaComExcecaoChecked.java

```java
// IMPORTANTE:
// Arquivo: TestaContaComExcecaoChecked.java

public class TestaContaComExcecaoChecked {

    public static void main(String[] args) {

        Conta conta = new Conta();

        try {

            conta.deposita();

        } catch (MinhaExcecao ex) {

            System.out.println(
                "A exceção foi tratada."
            );
        }
    }
}
```

---

# 38. Não confunda mais throw e throws

## throw

Utilizado dentro do código:

```java
throw new MinhaExcecao(
    "Algo deu errado"
);
```

Significa:

```text
LANCE esta exceção.
```

---

## throws

Utilizado na assinatura:

```java
public void metodo()
        throws MinhaExcecao
```

Significa:

```text
Este método PODE lançar
esta exceção.
```

Uma forma simples de lembrar:

```text
throw
↓
ação

throws
↓
aviso
```

---

# Desafio 01 — Checked ou Unchecked?

Crie duas classes de exceção.

Primeira:

```text
ErroCadastroException
```

Ela deverá estender:

```java
RuntimeException
```

Segunda:

```text
ErroArquivoException
```

Ela deverá estender:

```java
Exception
```

As duas deverão possuir construtor recebendo uma mensagem e utilizando:

```java
super(mensagem);
```

Depois crie:

```text
TesteTiposExcecao.java
```

Crie dois métodos:

```java
private static void testarCadastro()
```

e:

```java
private static void testarArquivo()
```

No primeiro, lance:

```java
ErroCadastroException
```

No segundo, lance:

```java
ErroArquivoException
```

Observe o IntelliJ.

No final do arquivo, responda:

```java
/*
RESPONDA:

1. Qual das duas exceções obrigou o uso de
   try-catch ou throws?

2. Qual delas é unchecked?

3. Qual delas é checked?

4. A diferença apareceu durante a compilação
   ou apenas durante a execução?
*/
```

---

# Desafio 02 — Tratar ou propagar

Crie:

```text
ValidacaoException.java
```

Ela deverá estender:

```java
Exception
```

Crie:

```text
SistemaCadastro.java
```

Dentro dela crie:

```java
private static void validarNome(String nome)
```

Se:

```java
nome == null
```

lance:

```java
new ValidacaoException(
    "Nome não informado"
);
```

Faça `validarNome()` utilizar:

```java
throws ValidacaoException
```

Depois crie outro método:

```java
private static void cadastrar()
```

Esse método deverá chamar:

```java
validarNome(null);
```

Você deverá escolher uma das opções:

```text
tratar dentro de cadastrar()
```

ou:

```text
propagar com throws
```

No `main()`, garanta que a exceção seja tratada antes do programa terminar.

Mostre:

```text
Cadastro não realizado.
```

e a mensagem original da exceção.

No final responda:

```java
/*
RESPONDA:

1. Em qual método a exceção foi lançada?

2. Qual método propagou a exceção?

3. Em qual método ela foi tratada?

4. Para que serviu o throws neste programa?

5. Para que serviu o throw?
*/
```

---

# Resumo da Aula

Hoje aprendemos que:

```text
Exception
│
├── RuntimeException
│   └── Unchecked
│
└── Checked
```

Uma exceção que herda de:

```java
RuntimeException
```

é normalmente:

```text
Unchecked
```

O compilador não exige tratamento obrigatório.

Uma exceção que herda diretamente de:

```java
Exception
```

é:

```text
Checked
```

O compilador exige:

```text
try-catch
```

ou:

```text
throws
```

Também aprendemos:

```java
throw new MinhaExcecao();
```

para lançar uma exceção.

E:

```java
throws MinhaExcecao
```

para declarar que um método pode propagá-la.

Na execução, tanto checked quanto unchecked percorrem a pilha normalmente quando são lançadas.

A principal diferença estudada hoje está no comportamento do:

```text
COMPILADOR
```

Também conhecemos:

```java
catch (Exception ex)
```

que permite capturar diferentes tipos de exceção pertencentes à hierarquia de `Exception`.

---

# Próxima etapa

Agora já sabemos:

```text
Pilha
↓
Exceções
↓
try-catch
↓
throw
↓
Exceções personalizadas
↓
Checked e Unchecked
↓
throws
```

Nas próximas aulas podemos aplicar esses conceitos em um cenário mais completo, utilizando regras reais de um sistema e exceções específicas para cada situação.