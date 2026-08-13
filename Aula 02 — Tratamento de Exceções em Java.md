# Aula 02 — Tratamento de Exceções em Java

**Curso:** Técnico em Desenvolvimento de Sistemas  
**Turma:** 3º Ano  
**Disciplina:** Programação / Java  
**Professor:** Richardson Schawarski Cruz  

---

# Objetivos da Aula

Ao final desta aula, você deverá ser capaz de:

- Compreender o que é uma **exceção**.
- Perceber como uma exceção altera o fluxo normal do programa.
- Identificar uma `ArithmeticException`.
- Identificar uma `NullPointerException`.
- Interpretar informações básicas de um **stack trace**.
- Relacionar uma exceção com a **pilha de execução** estudada na aula anterior.
- Utilizar `try-catch` para tratar exceções.
- Utilizar `getMessage()` para obter a mensagem de uma exceção.
- Utilizar `printStackTrace()` para visualizar o rastro da exceção.
- Tratar mais de um tipo de exceção utilizando **multi-catch**.

---

# 1. Continuando nosso projeto

Na aula anterior criamos o projeto:

```text
java-pilha
```

E dentro dele criamos:

```text
Fluxo.java
```

Nosso programa possuía três métodos:

```text
main()
   ↓
metodo1()
   ↓
metodo2()
```

Também vimos que, durante a execução de `metodo2()`, nossa pilha ficava semelhante a:

```text
┌──────────────┐
│  metodo2()   │
├──────────────┤
│  metodo1()   │
├──────────────┤
│    main()    │
└──────────────┘
```

Quando `metodo2()` terminava normalmente, o Java retornava para `metodo1()`.

Depois retornava para `main()`.

Hoje vamos responder uma nova pergunta:

> O que acontece com essa pilha quando alguma coisa dá errado durante a execução de um método?

---

# 2. Preparando o código da aula anterior

Abra o projeto:

```text
java-pilha
```

Abra:

```text
Fluxo.java
```

Confira se o código está semelhante a este:

```java
// IMPORTANTE:
// Continuaremos utilizando o mesmo projeto da Aula 01.
//
// Não é necessário instalar nenhuma biblioteca.
// ArithmeticException e NullPointerException fazem parte do Java.

public class Fluxo {

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

        for (int i = 1; i <= 5; i++) {

            System.out.println(i);
        }

        System.out.println("Fim do metodo2");
    }
}
```

Execute uma vez antes de continuar.

O resultado deverá ser:

```text
Início do main
Início do metodo1
Início do metodo2
1
2
3
4
5
Fim do metodo2
Fim do metodo1
Fim do main
```

Neste momento, todos os métodos conseguem terminar normalmente.

---

# 3. Criando um problema

Vamos modificar `metodo2()`.

Dentro do `for`, depois de:

```java
System.out.println(i);
```

adicione:

```java
int resultado = 10 / 0;
```

Nosso método ficará:

```java
private static void metodo2() {

    System.out.println("Início do metodo2");

    for (int i = 1; i <= 5; i++) {

        System.out.println(i);

        // Esta operação provocará uma exceção.
        int resultado = 10 / 0;
    }

    System.out.println("Fim do metodo2");
}
```

Antes de executar, pense:

> O programa vai compilar?

Execute.

---

# 4. Erro de compilação ou problema durante a execução?

O código:

```java
int resultado = 10 / 0;
```

pode ser compilado.

O problema aparece quando a JVM tenta executar essa operação.

Não podemos realizar uma divisão inteira por zero.

O Java interromperá o fluxo normal e apresentará algo semelhante a:

```text
Exception in thread "main" java.lang.ArithmeticException: / by zero
    at Fluxo.metodo2(Fluxo.java:...)
    at Fluxo.metodo1(Fluxo.java:...)
    at Fluxo.main(Fluxo.java:...)
```

> **IMPORTANTE:** os números das linhas podem ser diferentes no seu computador. Isso é normal. Eles dependem da posição das instruções dentro do arquivo.

---

# 5. Nossa primeira exceção

Observe esta parte:

```text
java.lang.ArithmeticException
```

O Java está informando o tipo do problema.

`ArithmeticException` é uma exceção relacionada a uma operação aritmética inválida.

Neste caso:

```java
10 / 0
```

provocou:

```text
ArithmeticException
```

Podemos pensar na exceção como um **aviso de emergência durante a execução**.

O programa estava seguindo seu caminho normalmente.

Então ocorreu uma situação que impediu aquele fluxo de continuar.

---

# 6. O fluxo mudou

Antes do problema, esperávamos:

```text
Início do main
Início do metodo1
Início do metodo2
1
2
3
4
5
Fim do metodo2
Fim do metodo1
Fim do main
```

Mas isso não aconteceu.

A exceção ocorreu logo na primeira repetição.

Portanto, algumas linhas nunca foram executadas.

Por exemplo:

```java
System.out.println("Fim do metodo2");
```

não foi executada.

Também não chegamos em:

```java
System.out.println("Fim do metodo1");
```

nem em:

```java
System.out.println("Fim do main");
```

A exceção **alterou o fluxo normal do programa**.

---

# 7. O que aconteceu com nossa pilha?

Quando o problema aconteceu, estávamos em:

```text
┌──────────────┐
│  metodo2()   │ ← EXCEÇÃO
├──────────────┤
│  metodo1()   │
├──────────────┤
│    main()    │
└──────────────┘
```

O Java verifica:

> `metodo2()` sabe tratar essa exceção?

Neste momento:

```text
NÃO
```

Então `metodo2()` é interrompido e a exceção sobe para quem chamou esse método.

```text
┌──────────────┐
│  metodo1()   │ ← exceção chegou aqui
├──────────────┤
│    main()    │
└──────────────┘
```

O Java verifica novamente:

> `metodo1()` sabe tratar essa exceção?

Também não.

Então ela continua:

```text
┌──────────────┐
│    main()    │ ← exceção chegou aqui
└──────────────┘
```

O `main()` também não possui tratamento.

A exceção não foi tratada e o programa termina.

---

# 8. Stack Trace — O rastro da exceção

O Java não mostra aquelas linhas apenas para assustar o programador.

Observe:

```text
java.lang.ArithmeticException: / by zero
    at Fluxo.metodo2(Fluxo.java:...)
    at Fluxo.metodo1(Fluxo.java:...)
    at Fluxo.main(Fluxo.java:...)
```

Isso é chamado de:

```text
Stack Trace
```

ou:

```text
Rastro da pilha
```

Ele nos ajuda a descobrir por onde o programa passou até chegar ao problema.

Podemos interpretar assim:

```text
main()
   ↓
chamou metodo1()
   ↓
chamou metodo2()
   ↓
ArithmeticException
```

Observe como o assunto da Aula 01 aparece novamente.

Para entender corretamente o stack trace, precisamos entender a **pilha de execução**.

---

# 9. Analogia — O problema subindo pela equipe

Imagine uma situação na escola.

Um aluno encontra um problema e procura o professor.

O professor não consegue resolver e encaminha para a coordenação.

A coordenação também não consegue resolver e encaminha para a direção.

Podemos imaginar:

```text
Aluno
  ↓
Professor
  ↓
Coordenação
  ↓
Direção
```

Com uma exceção acontece algo semelhante.

```text
metodo2()
   ↓
metodo1()
   ↓
main()
```

Se um método não trata o problema, a exceção é propagada para o método que o chamou.

---

# 10. Precisamos tratar o problema

Sabemos que essa operação pode provocar:

```text
ArithmeticException
```

Queremos impedir que essa exceção encerre nosso programa de maneira não controlada.

Para isso, Java possui:

```java
try
```

e:

```java
catch
```

---

# 11. O bloco try

`try` significa que queremos executar um trecho de código e estar preparados caso uma exceção esperada seja lançada dentro dele.

A estrutura básica é:

```java
try {

    // Código que será executado.

}
```

Mas apenas `try` não é suficiente.

Precisamos informar o que fazer caso uma determinada exceção aconteça.

Para isso temos:

```java
catch
```

---

# 12. O bloco catch

A estrutura será:

```java
try {

    // Código que será executado.

} catch (ArithmeticException ex) {

    // O que fazer se uma ArithmeticException acontecer.

}
```

Podemos pensar:

```text
try
 ↓
Tente executar isso.

catch
 ↓
Se esta exceção acontecer,
trate-a desta maneira.
```

---

# 13. Tratando nossa exceção

Vamos colocar o tratamento no `main()`.

Altere:

```java
metodo1();
```

para:

```java
try {

    metodo1();

} catch (ArithmeticException ex) {

    System.out.println("Ocorreu um problema matemático.");
}
```

Nosso `main()` ficará:

```java
public static void main(String[] args) {

    System.out.println("Início do main");

    try {

        // Tentamos executar normalmente metodo1().
        metodo1();

    } catch (ArithmeticException ex) {

        // Este bloco será executado somente se uma
        // ArithmeticException chegar até este ponto.
        System.out.println("Ocorreu um problema matemático.");
    }

    System.out.println("Fim do main");
}
```

Execute novamente.

---

# 14. Observe a diferença

Agora teremos algo semelhante a:

```text
Início do main
Início do metodo1
Início do metodo2
1
Ocorreu um problema matemático.
Fim do main
```

Observe atentamente.

Continuamos sem:

```text
Fim do metodo2
```

e sem:

```text
Fim do metodo1
```

Por quê?

Porque a exceção interrompeu esses métodos.

Mas agora temos:

```text
Fim do main
```

Isso acontece porque a exceção chegou ao `main()` e foi capturada pelo `catch`.

Depois do tratamento, o programa pôde continuar após o bloco `try-catch`.

---

# 15. A exceção é um objeto

Observe:

```java
catch (ArithmeticException ex)
```

Temos:

```text
ArithmeticException
```

que representa o tipo da exceção.

E:

```text
ex
```

é uma referência para o objeto que representa aquela exceção.

Isso significa que podemos obter informações sobre o problema.

---

# 16. Descobrindo a mensagem da exceção

Dentro do `catch`, adicione:

```java
String mensagem = ex.getMessage();
```

Depois:

```java
System.out.println("Mensagem: " + mensagem);
```

Ficará:

```java
catch (ArithmeticException ex) {

    // getMessage() recupera a mensagem associada à exceção.
    String mensagem = ex.getMessage();

    System.out.println("Mensagem: " + mensagem);
}
```

Execute.

Você deverá visualizar uma mensagem semelhante a:

```text
Mensagem: / by zero
```

O método:

```java
getMessage()
```

permite consultar a mensagem armazenada na exceção.

---

# 17. Exibindo o Stack Trace manualmente

Também podemos pedir que a própria exceção mostre seu stack trace.

Adicione:

```java
ex.printStackTrace();
```

Nosso `catch` ficará:

```java
catch (ArithmeticException ex) {

    String mensagem = ex.getMessage();

    System.out.println("Mensagem: " + mensagem);

    // Mostra o rastro completo da exceção.
    ex.printStackTrace();
}
```

Execute novamente.

Agora o programa trata a exceção, mas também apresenta seu rastro para análise.

> **IMPORTANTE:** `printStackTrace()` é muito útil durante o estudo e a depuração. Em sistemas reais, normalmente utilizamos mecanismos apropriados de registro de erros, conhecidos como logs.

---

# 18. Outro tipo de exceção

Até agora conhecemos:

```text
ArithmeticException
```

Mas existem vários tipos de exceções.

Vamos provocar outra.

Primeiro, remova esta linha de `metodo2()`:

```java
int resultado = 10 / 0;
```

Nosso `metodo2()` voltará temporariamente a funcionar normalmente.

Agora vamos criar uma classe simples chamada:

```text
Conta.java
```

> **IMPORTANTE:** crie `Conta.java` dentro do mesmo projeto e no mesmo pacote de `Fluxo.java`.

---

# 19. Criando a classe Conta

Digite:

```java
// IMPORTANTE:
// Esta classe deve estar no arquivo Conta.java.
//
// Não precisamos importar nenhuma biblioteca.

public class Conta {

    public void deposita() {

        System.out.println("Depósito realizado.");
    }
}
```

Temos agora uma classe `Conta` com o método:

```java
deposita()
```

---

# 20. Utilizando Conta em metodo2()

Volte para:

```text
Fluxo.java
```

Dentro do `for`, depois de:

```java
System.out.println(i);
```

adicione:

```java
Conta conta = null;

conta.deposita();
```

Nosso método ficará:

```java
private static void metodo2() {

    System.out.println("Início do metodo2");

    for (int i = 1; i <= 5; i++) {

        System.out.println(i);

        // Criamos uma referência do tipo Conta.
        // Porém ela não aponta para nenhum objeto.
        Conta conta = null;

        // Estamos tentando utilizar uma referência nula.
        conta.deposita();
    }

    System.out.println("Fim do metodo2");
}
```

Execute.

---

# 21. NullPointerException

Agora nosso programa apresentará:

```text
NullPointerException
```

Isso aconteceu porque:

```java
Conta conta = null;
```

não criou um objeto `Conta`.

A variável `conta` possui:

```text
null
```

Podemos pensar em `null` como:

> "Esta referência não aponta para nenhum objeto."

Depois tentamos:

```java
conta.deposita();
```

Estamos pedindo:

> Execute `deposita()` no objeto apontado por `conta`.

Mas não existe objeto.

Por isso ocorre:

```text
NullPointerException
```

---

# 22. Por que nosso catch não resolveu?

Temos no `main()`:

```java
catch (ArithmeticException ex)
```

Mas agora aconteceu:

```text
NullPointerException
```

São tipos diferentes.

Nosso `catch` foi preparado para:

```text
ArithmeticException
```

e não para:

```text
NullPointerException
```

Portanto, essa nova exceção não será capturada por esse `catch`.

---

# 23. Tratando tipos diferentes

Uma possibilidade é utilizar dois blocos `catch`.

```java
try {

    metodo1();

} catch (ArithmeticException ex) {

    System.out.println("Problema matemático.");

} catch (NullPointerException ex) {

    System.out.println("Tentativa de utilizar uma referência nula.");
}
```

Agora temos tratamentos diferentes para problemas diferentes.

Isso é útil quando queremos reagir de maneiras diferentes dependendo do tipo da exceção.

---

# 24. Multi-Catch

E se quisermos fazer exatamente a mesma coisa para os dois tipos?

Não precisamos repetir código.

Java permite utilizar:

```text
|
```

O caractere `|` é chamado de:

```text
pipe
```

Podemos escrever:

```java
catch (ArithmeticException | NullPointerException ex)
```

Nosso código ficará:

```java
try {

    metodo1();

} catch (ArithmeticException | NullPointerException ex) {

    String mensagem = ex.getMessage();

    System.out.println("Ocorreu uma exceção.");
    System.out.println("Mensagem: " + mensagem);

    ex.printStackTrace();
}
```

Agora o mesmo `catch` poderá capturar:

```text
ArithmeticException
```

ou:

```text
NullPointerException
```

> **IMPORTANTE:** o multi-catch com `|` é utilizado quando queremos aplicar o mesmo tratamento para diferentes tipos de exceção.

---

# 25. Código final de Fluxo.java

Ao final da aula, nosso arquivo deverá ficar semelhante a este:

```java
// IMPORTANTE:
// Arquivo: Fluxo.java
//
// Não é necessário importar bibliotecas.
// ArithmeticException e NullPointerException
// fazem parte da própria linguagem Java.

public class Fluxo {

    public static void main(String[] args) {

        System.out.println("Início do main");

        try {

            // Tentamos executar normalmente metodo1().
            metodo1();

        } catch (ArithmeticException | NullPointerException ex) {

            // ex representa o objeto da exceção capturada.

            // Recuperamos a mensagem original da exceção.
            String mensagem = ex.getMessage();

            System.out.println("Ocorreu uma exceção.");
            System.out.println("Mensagem: " + mensagem);

            // Exibe o caminho percorrido pela exceção
            // através da pilha de execução.
            ex.printStackTrace();
        }

        // Se a exceção for tratada pelo catch,
        // a execução poderá continuar a partir daqui.
        System.out.println("Fim do main");
    }

    private static void metodo1() {

        System.out.println("Início do metodo1");

        // Se metodo2() lançar uma exceção e não tratá-la,
        // ela será propagada para este método.
        metodo2();

        // Esta linha não será executada caso metodo2()
        // seja interrompido por uma exceção.
        System.out.println("Fim do metodo1");
    }

    private static void metodo2() {

        System.out.println("Início do metodo2");

        for (int i = 1; i <= 5; i++) {

            System.out.println(i);

            // A variável existe, mas não aponta para
            // nenhum objeto Conta.
            Conta conta = null;

            // Esta chamada provoca uma NullPointerException,
            // pois conta possui o valor null.
            conta.deposita();
        }

        // Esta linha não será alcançada enquanto
        // a exceção ocorrer dentro do for.
        System.out.println("Fim do metodo2");
    }
}
```

---

# 26. Código final de Conta.java

```java
// IMPORTANTE:
// Arquivo: Conta.java
//
// Mantenha esta classe no mesmo projeto e pacote
// utilizado pela classe Fluxo.

public class Conta {

    public void deposita() {

        System.out.println("Depósito realizado.");
    }
}
```

---

# 27. Entendendo o caminho completo

Agora conseguimos juntar tudo que estudamos.

O programa começa:

```text
main()
```

Depois:

```text
main()
   ↓
metodo1()
```

Depois:

```text
main()
   ↓
metodo1()
   ↓
metodo2()
```

Dentro de `metodo2()`:

```java
conta.deposita();
```

provoca:

```text
NullPointerException
```

`metodo2()` não trata:

```text
NullPointerException
      ↑
   metodo2()
```

Ela é propagada para `metodo1()`:

```text
NullPointerException
      ↑
   metodo1()
      ↑
   metodo2()
```

`metodo1()` também não trata.

Então ela chega ao `main()`.

No `main()` existe:

```java
catch (ArithmeticException | NullPointerException ex)
```

Agora encontramos alguém preparado para tratar o problema.

A exceção é capturada.

---

# 28. Uma diferença muito importante

Observe:

```java
try {
    metodo1();
} catch (ArithmeticException | NullPointerException ex) {
    System.out.println("Ocorreu uma exceção.");
}

System.out.println("Fim do main");
```

Quando a exceção acontece dentro de `metodo2()`, o Java **não retorna para continuar de onde parou em `metodo2()`**.

Ele também não volta para executar:

```text
Fim do metodo1
```

O fluxo foi interrompido.

Quando o `catch` trata a exceção, a execução continua **depois do bloco `try-catch`**.

Por isso:

```text
Fim do main
```

ainda poderá aparecer.

---

# 29. Debugando uma exceção

Vamos observar isso no IntelliJ.

Coloque um breakpoint nesta linha de `metodo2()`:

```java
conta.deposita();
```

Execute utilizando **Debug**.

Antes de executar essa linha, observe a pilha.

Você deverá encontrar algo semelhante a:

```text
metodo2()
metodo1()
main()
```

Agora avance.

A `NullPointerException` será lançada.

Observe como o fluxo deixa `metodo2()` e `metodo1()` e chega ao tratamento existente no `main()`.

Essa é a mesma pilha que estudamos na Aula 01.

A diferença é que agora ela está sendo afetada por uma **exceção**.

---

# Desafio 01 — Divisão segura

Crie uma nova classe:

```text
Divisao.java
```

O programa deverá possuir duas variáveis:

```java
int numero1 = 20;
int numero2 = 0;
```

Tente realizar:

```text
numero1 / numero2
```

Utilize `try-catch` para impedir que o programa termine de maneira não controlada.

Quando ocorrer uma `ArithmeticException`, mostre:

```text
Não foi possível realizar a divisão.
```

Depois do `try-catch`, o programa deverá obrigatoriamente mostrar:

```text
Programa finalizado.
```

### Resultado esperado

```text
Não foi possível realizar a divisão.
Programa finalizado.
```

> **IMPORTANTE:** este desafio é independente do projeto principal. Não altere `Fluxo.java` para resolvê-lo.

---

# Desafio 02 — Qual catch será executado?

Crie uma classe:

```text
TesteExcecoes.java
```

Crie uma variável:

```java
int opcao = 1;
```

Dentro de um `try`, faça o programa funcionar da seguinte maneira:

Quando:

```text
opcao = 1
```

provoque uma:

```text
ArithmeticException
```

Quando:

```text
opcao = 2
```

crie uma referência nula de `Conta` e tente chamar:

```java
deposita()
```

Utilize **dois blocos `catch` separados**:

```java
catch (ArithmeticException ex)
```

e:

```java
catch (NullPointerException ex)
```

Cada `catch` deverá mostrar uma mensagem diferente.

Teste o programa primeiro com:

```java
int opcao = 1;
```

Depois altere para:

```java
int opcao = 2;
```

No final do arquivo, responda:

```java
/*
RESPONDA:

1. Qual catch foi executado quando opcao = 1?

2. Qual catch foi executado quando opcao = 2?

3. Por que um catch de ArithmeticException
   não trata uma NullPointerException?

4. O programa continuou executando depois
   que a exceção foi tratada?
*/
```

> **IMPORTANTE:** não utilize multi-catch neste desafio. O objetivo é observar que tipos diferentes de exceções podem possuir tratamentos diferentes.

---

# Resumo da Aula

Hoje nosso programa deixou de possuir apenas um fluxo normal.

Conhecemos:

```text
ArithmeticException
```

Exemplo:

```java
10 / 0;
```

Conhecemos também:

```text
NullPointerException
```

Exemplo:

```java
Conta conta = null;
conta.deposita();
```

Vimos que uma exceção pode percorrer a pilha:

```text
metodo2()
    ↓
metodo1()
    ↓
main()
```

Também aprendemos:

```java
try {
    // Código executado normalmente.
} catch (ArithmeticException ex) {
    // Tratamento da exceção.
}
```

Para obter a mensagem:

```java
ex.getMessage();
```

Para visualizar o rastro da pilha:

```java
ex.printStackTrace();
```

Para tratar diferentes exceções da mesma maneira:

```java
catch (ArithmeticException | NullPointerException ex)
```

Agora já sabemos **observar**, **identificar** e **tratar** algumas exceções.

Na próxima aula, faremos algo diferente.

Até agora, esperamos uma situação problemática acontecer para o Java gerar uma exceção.

Na próxima aula, aprenderemos a dizer explicitamente ao Java:

> **"Esta situação não pode continuar. Lance uma exceção."**

Para isso conheceremos a palavra-chave:

```java
throw
```