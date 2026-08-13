# Aula 03 — Lançando Exceções em Java

**Curso:** Técnico em Desenvolvimento de Sistemas  
**Turma:** 3º Ano  
**Disciplina:** Programação / Java  
**Professor:** Richardson Schawarski Cruz  

---

# Objetivos da Aula

Ao final desta aula, você deverá ser capaz de:

- Revisar o funcionamento da pilha de execução.
- Revisar como uma exceção percorre a pilha.
- Compreender que exceções são objetos.
- Entender a diferença entre uma exceção que acontece e uma exceção que é lançada pelo programador.
- Utilizar a palavra-chave `throw`.
- Criar uma exceção com `new`.
- Adicionar uma mensagem a uma exceção.
- Tratar uma exceção lançada manualmente.
- Relacionar o lançamento de exceções com regras de negócio.

---

# 1. Continuando nosso projeto

Nas aulas anteriores, utilizamos o mesmo projeto:

```text
java-pilha
```

Na Aula 01 estudamos:

```text
Pilha de execução
```

Na Aula 02 estudamos:

```text
Exceções
try
catch
ArithmeticException
NullPointerException
Stack Trace
```

Hoje continuaremos no mesmo projeto.

> **IMPORTANTE:** não crie um projeto novo. Continue utilizando `java-pilha`.

---

# 2. Revisão rápida

Nosso fluxo era:

```text
main()
   ↓
metodo1()
   ↓
metodo2()
```

Quando tudo funciona normalmente, `metodo2()` termina e o Java retorna para `metodo1()`.

Depois:

```text
metodo1()
```

termina e o Java retorna para:

```text
main()
```

Na pilha:

```text
┌──────────────┐
│  metodo2()   │
├──────────────┤
│  metodo1()   │
├──────────────┤
│    main()    │
└──────────────┘
```

Quando `metodo2()` termina:

```text
┌──────────────┐
│  metodo1()   │
├──────────────┤
│    main()    │
└──────────────┘
```

Depois:

```text
┌──────────────┐
│    main()    │
└──────────────┘
```

---

# 3. E quando acontece uma exceção?

Na aula anterior provocamos situações como:

```java
int resultado = 10 / 0;
```

Isso gerou:

```text
ArithmeticException
```

Também utilizamos:

```java
Conta conta = null;
conta.deposita();
```

Isso gerou:

```text
NullPointerException
```

Nos dois casos, a exceção aconteceu por causa de alguma operação inválida.

Mas surge uma nova pergunta:

> E se nós quisermos decidir que determinada situação deve gerar uma exceção?

É isso que veremos hoje.

---

# 4. Uma situação do mundo real

Imagine uma conta bancária.

Temos:

```text
Saldo: R$ 500
```

E alguém tenta sacar:

```text
R$ 800
```

O programa não possui um problema matemático.

Também não existe uma referência `null`.

Mas existe uma situação inválida para nossa regra de negócio:

```text
Não é permitido sacar mais dinheiro do que existe na conta.
```

Nesse caso, o próprio programador pode decidir interromper o fluxo.

Podemos dizer ao Java:

> "Essa situação não pode continuar."

Para isso utilizamos:

```java
throw
```

---

# 5. O que significa throw?

A palavra:

```java
throw
```

significa:

```text
lançar
```

No contexto de exceções, utilizamos `throw` para lançar um objeto de exceção.

A estrutura básica será:

```java
throw new ArithmeticException();
```

Podemos ler assim:

```text
throw
↓
lance

new ArithmeticException()
↓
uma nova exceção do tipo ArithmeticException
```

---

# 6. Voltando ao Fluxo.java

Vamos simplificar temporariamente nosso código.

Abra:

```text
Fluxo.java
```

Vamos manter nosso `main()` com tratamento.

Utilize:

```java
public class Fluxo {

    public static void main(String[] args) {

        System.out.println("Início do main");

        try {

            metodo1();

        } catch (ArithmeticException | NullPointerException ex) {

            String mensagem = ex.getMessage();

            System.out.println("Ocorreu uma exceção.");
            System.out.println("Mensagem: " + mensagem);

            ex.printStackTrace();
        }

        System.out.println("Fim do main");
    }

    private static void metodo1() {

        System.out.println("Início do metodo1");

        metodo2();

        System.out.println("Fim do metodo1");
    }

    private static void metodo2() {

        System.out.println("Início do metodo2");

        System.out.println("Fim do metodo2");
    }
}
```

Execute.

Resultado:

```text
Início do main
Início do metodo1
Início do metodo2
Fim do metodo2
Fim do metodo1
Fim do main
```

Tudo funciona normalmente.

---

# 7. Criando uma exceção manualmente

Agora vamos alterar apenas `metodo2()`.

Digite:

```java
private static void metodo2() {

    System.out.println("Início do metodo2");

    ArithmeticException excecao = new ArithmeticException();

    System.out.println("Fim do metodo2");
}
```

Execute.

O programa continua funcionando normalmente.

Por quê?

Porque apenas criamos um objeto.

```java
new ArithmeticException();
```

não lança automaticamente a exceção.

Criamos o objeto:

```text
ArithmeticException
```

mas ainda não fizemos nada com ele.

---

# 8. Exceções também são objetos

Observe:

```java
ArithmeticException excecao = new ArithmeticException();
```

Temos:

```text
ArithmeticException
```

como tipo.

Temos:

```text
excecao
```

como referência.

E:

```java
new ArithmeticException()
```

cria o objeto.

Isso é semelhante a:

```java
Conta conta = new Conta();
```

Em Java, exceções também são objetos.

Essa ideia será muito importante nas próximas aulas.

---

# 9. Lançando o objeto

Agora vamos adicionar:

```java
throw excecao;
```

Nosso método ficará:

```java
private static void metodo2() {

    System.out.println("Início do metodo2");

    // Criamos um objeto que representa uma exceção.
    ArithmeticException excecao = new ArithmeticException();

    // Agora lançamos esse objeto para a pilha de execução.
    throw excecao;
}
```

> **IMPORTANTE:** depois de `throw`, o fluxo normal do método é interrompido.

Por isso, não coloque agora:

```java
System.out.println("Fim do metodo2");
```

depois do `throw`.

O IntelliJ poderá inclusive indicar que essa linha nunca será alcançada.

---

# 10. Execute novamente

Agora o programa deverá mostrar algo semelhante a:

```text
Início do main
Início do metodo1
Início do metodo2
Ocorreu uma exceção.
Mensagem: null
...
Fim do main
```

Perceba que não apareceu:

```text
Fim do metodo2
```

Também não apareceu:

```text
Fim do metodo1
```

Isso aconteceu porque:

```java
throw excecao;
```

interrompeu o fluxo.

---

# 11. O que aconteceu na pilha?

Antes do `throw`:

```text
┌──────────────┐
│  metodo2()   │
├──────────────┤
│  metodo1()   │
├──────────────┤
│    main()    │
└──────────────┘
```

Em `metodo2()` executamos:

```java
throw excecao;
```

A exceção foi lançada.

`metodo2()` não possui tratamento.

Então a exceção sobe:

```text
metodo2()
    ↑
ArithmeticException
```

Chega em:

```text
metodo1()
```

`metodo1()` também não trata.

Então continua:

```text
metodo2()
   ↓
metodo1()
   ↓
main()
```

No `main()` encontramos:

```java
catch (ArithmeticException | NullPointerException ex)
```

Então a exceção é capturada.

---

# 12. Uma analogia

Imagine que um aluno detecta um problema durante uma atividade.

Ele comunica ao professor:

```text
Tem um problema aqui.
```

O professor pode resolver.

Se não resolver, o problema pode ser encaminhado para a coordenação.

Na programação, `throw` funciona como:

```text
"Existe um problema aqui.
Alguém precisa tratar."
```

O método que lança a exceção não necessariamente precisa tratá-la.

Ela pode subir pela pilha até encontrar um `catch` compatível.

---

# 13. Adicionando uma mensagem

Quando executamos:

```java
new ArithmeticException();
```

a exceção foi criada sem mensagem.

Por isso:

```java
ex.getMessage();
```

pode retornar:

```text
null
```

Vamos melhorar isso.

Altere:

```java
ArithmeticException excecao = new ArithmeticException();
```

para:

```java
ArithmeticException excecao =
        new ArithmeticException("Algo deu errado em metodo2");
```

Nosso método ficará:

```java
private static void metodo2() {

    System.out.println("Início do metodo2");

    // Criamos a exceção já com uma mensagem explicando o problema.
    ArithmeticException excecao =
            new ArithmeticException("Algo deu errado em metodo2");

    // Interrompemos o fluxo normal.
    throw excecao;
}
```

Execute novamente.

Agora teremos:

```text
Mensagem: Algo deu errado em metodo2
```

---

# 14. Forma mais comum

Criar primeiro:

```java
ArithmeticException excecao =
        new ArithmeticException("Algo deu errado");
```

e depois:

```java
throw excecao;
```

funciona perfeitamente.

Mas normalmente escrevemos tudo em uma única linha:

```java
throw new ArithmeticException("Algo deu errado");
```

Podemos ler:

```text
new ArithmeticException(...)
↓
crie uma nova exceção

throw
↓
lance essa exceção
```

---

# 15. Simplificando metodo2()

Altere para:

```java
private static void metodo2() {

    System.out.println("Início do metodo2");

    // Forma mais utilizada para criar e lançar
    // uma exceção diretamente.
    throw new ArithmeticException("Algo deu errado em metodo2");
}
```

Essa forma será bastante utilizada daqui para frente.

---

# 16. Exceção que acontece x exceção que lançamos

Na Aula 02:

```java
int resultado = 10 / 0;
```

O Java percebeu o problema e lançou:

```text
ArithmeticException
```

Agora:

```java
throw new ArithmeticException("Algo deu errado");
```

somos nós que estamos dizendo explicitamente:

> lance uma exceção.

Essa diferença é importante.

### Exceção provocada por uma operação

```java
int resultado = 10 / 0;
```

### Exceção lançada pelo programador

```java
throw new ArithmeticException("Operação inválida");
```

Nos dois casos, a exceção entra no mesmo mecanismo de propagação e tratamento.

---

# 17. Podemos decidir quando lançar

Até agora `metodo2()` sempre lança uma exceção.

Mas normalmente uma exceção é lançada quando alguma condição inválida acontece.

Vamos criar:

```java
private static void metodo2() {

    System.out.println("Início do metodo2");

    int idade = 15;

    if (idade < 18) {

        throw new ArithmeticException("Idade não permitida");
    }

    System.out.println("Fim do metodo2");
}
```

Execute.

A exceção será lançada porque:

```text
15 < 18
```

é verdadeiro.

---

# 18. Mas ArithmeticException faz sentido aqui?

Observe:

```java
throw new ArithmeticException("Idade não permitida");
```

Funciona.

Mas existe um problema de significado.

`ArithmeticException` representa normalmente um problema aritmético.

Nossa situação é:

```text
Idade não permitida
```

Portanto, estamos utilizando um tipo de exceção que não descreve muito bem nosso problema.

Por enquanto vamos aceitar isso apenas para compreender `throw`.

Nas próximas aulas poderemos criar exceções mais adequadas para as regras do nosso sistema.

---

# 19. Preparando uma situação mais próxima de um sistema real

Na aula anterior já criamos:

```text
Conta.java
```

Vamos aproveitar essa classe.

Abra `Conta.java`.

Podemos evoluí-la para:

```java
public class Conta {

    private double saldo = 500;

    public void deposita(double valor) {

        saldo = saldo + valor;
    }

    public void saca(double valor) {

        saldo = saldo - valor;
    }

    public double getSaldo() {

        return saldo;
    }
}
```

> **IMPORTANTE:** nesta aula ainda não estamos criando uma exceção própria. Vamos apenas utilizar uma exceção já existente do Java para aprender a lógica do `throw`.

---

# 20. Testando a Conta

Crie uma nova classe:

```text
TesteConta.java
```

Digite:

```java
public class TesteConta {

    public static void main(String[] args) {

        Conta conta = new Conta();

        System.out.println("Saldo inicial: " + conta.getSaldo());

        conta.saca(200);

        System.out.println("Saldo final: " + conta.getSaldo());
    }
}
```

Execute.

Resultado:

```text
Saldo inicial: 500.0
Saldo final: 300.0
```

Tudo certo.

---

# 21. Criando um problema de regra de negócio

Agora altere:

```java
conta.saca(200);
```

para:

```java
conta.saca(800);
```

Execute.

Teremos:

```text
Saldo inicial: 500.0
Saldo final: -300.0
```

O Java considera isso válido.

Matematicamente:

```text
500 - 800 = -300
```

não possui erro.

Mas para nosso sistema bancário, podemos decidir que isso é inválido.

Nossa regra será:

```text
Não permitir saque maior que o saldo.
```

---

# 22. Verificando a regra antes do saque

Volte para:

```text
Conta.java
```

Altere `saca()`:

```java
public void saca(double valor) {

    // Antes de alterar o saldo,
    // verificamos uma regra do nosso sistema.
    if (valor > saldo) {

        System.out.println("Saldo insuficiente.");

        return;
    }

    saldo = saldo - valor;
}
```

Execute novamente.

Agora o saldo não será alterado.

Mas temos outro problema.

O método simplesmente imprime:

```text
Saldo insuficiente.
```

Quem chamou `saca()` não recebeu uma informação estruturada sobre o problema.

---

# 23. Lançando uma exceção no método saca()

Vamos trocar:

```java
System.out.println("Saldo insuficiente.");

return;
```

por:

```java
throw new ArithmeticException("Saldo insuficiente");
```

O método ficará:

```java
public void saca(double valor) {

    // Verificamos se o saque viola uma regra.
    if (valor > saldo) {

        // Interrompemos o método e comunicamos
        // que ocorreu uma situação inválida.
        throw new ArithmeticException("Saldo insuficiente");
    }

    // Esta linha só será executada se
    // houver saldo suficiente.
    saldo = saldo - valor;
}
```

Agora nossa regra de negócio utiliza uma exceção.

---

# 24. Testando sem tratamento

Deixe `TesteConta.java` assim:

```java
public class TesteConta {

    public static void main(String[] args) {

        Conta conta = new Conta();

        System.out.println("Saldo inicial: " + conta.getSaldo());

        conta.saca(800);

        System.out.println("Saldo final: " + conta.getSaldo());
    }
}
```

Execute.

Você verá uma:

```text
ArithmeticException
```

com a mensagem:

```text
Saldo insuficiente
```

E esta linha:

```java
System.out.println("Saldo final: " + conta.getSaldo());
```

não será executada.

---

# 25. Tratando o problema

Agora vamos tratar.

Altere:

```java
conta.saca(800);
```

para:

```java
try {

    conta.saca(800);

} catch (ArithmeticException ex) {

    System.out.println("Não foi possível realizar o saque.");
    System.out.println("Motivo: " + ex.getMessage());
}
```

Código completo:

```java
public class TesteConta {

    public static void main(String[] args) {

        Conta conta = new Conta();

        System.out.println("Saldo inicial: " + conta.getSaldo());

        try {

            conta.saca(800);

        } catch (ArithmeticException ex) {

            System.out.println("Não foi possível realizar o saque.");
            System.out.println("Motivo: " + ex.getMessage());
        }

        System.out.println("Saldo final: " + conta.getSaldo());
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

# 26. O método que detecta e o método que trata

Observe nossa organização.

A classe `Conta` detectou o problema:

```java
if (valor > saldo) {
    throw new ArithmeticException("Saldo insuficiente");
}
```

A classe `TesteConta` decidiu como reagir:

```java
catch (ArithmeticException ex) {

    System.out.println("Não foi possível realizar o saque.");
}
```

Isso é muito importante.

Podemos separar responsabilidades:

```text
Conta
↓
detecta que a operação é inválida

TesteConta
↓
decide como tratar a situação
```

O método que detecta o problema não precisa necessariamente decidir como a interface do sistema deverá mostrar esse problema ao usuário.

---

# 27. Fluxo do saque

Quando existe saldo:

```text
saca()
  ↓
verifica saldo
  ↓
saldo suficiente
  ↓
realiza saque
  ↓
método termina normalmente
```

Quando não existe saldo:

```text
saca()
  ↓
verifica saldo
  ↓
saldo insuficiente
  ↓
throw
  ↓
fluxo interrompido
  ↓
exceção procura um catch
```

---

# 28. Código final de Fluxo.java

```java
// IMPORTANTE:
// Arquivo: Fluxo.java
//
// Não precisamos importar bibliotecas externas.
// ArithmeticException faz parte do Java.

public class Fluxo {

    public static void main(String[] args) {

        System.out.println("Início do main");

        try {

            // metodo1() chama metodo2().
            metodo1();

        } catch (ArithmeticException | NullPointerException ex) {

            // Recuperamos a mensagem da exceção.
            String mensagem = ex.getMessage();

            System.out.println("Ocorreu uma exceção.");
            System.out.println("Mensagem: " + mensagem);

            // Exibe o caminho percorrido pela exceção
            // através da pilha de execução.
            ex.printStackTrace();
        }

        System.out.println("Fim do main");
    }

    private static void metodo1() {

        System.out.println("Início do metodo1");

        metodo2();

        // Se metodo2() lançar uma exceção,
        // esta linha não será executada.
        System.out.println("Fim do metodo1");
    }

    private static void metodo2() {

        System.out.println("Início do metodo2");

        // Criamos e lançamos uma exceção manualmente.
        // O fluxo do método é interrompido nesta linha.
        throw new ArithmeticException("Algo deu errado em metodo2");
    }
}
```

---

# 29. Código final de Conta.java

```java
// IMPORTANTE:
// Arquivo: Conta.java
//
// Não utilizamos nenhuma biblioteca externa.
//
// Nesta aula utilizamos ArithmeticException
// apenas para aprender o funcionamento do throw.
// Futuramente poderemos utilizar uma exceção
// mais adequada para representar saldo insuficiente.

public class Conta {

    // Saldo inicial utilizado apenas para facilitar os testes.
    private double saldo = 500;

    public void deposita(double valor) {

        // Acrescenta o valor recebido ao saldo atual.
        saldo = saldo + valor;
    }

    public void saca(double valor) {

        // Verificamos a regra antes de modificar o saldo.
        if (valor > saldo) {

            // Criamos e lançamos uma exceção.
            // O restante deste método não será executado.
            throw new ArithmeticException("Saldo insuficiente");
        }

        // Só chegaremos aqui se existir saldo suficiente.
        saldo = saldo - valor;
    }

    public double getSaldo() {

        return saldo;
    }
}
```

---

# 30. Código final de TesteConta.java

```java
// IMPORTANTE:
// Arquivo: TesteConta.java
//
// Conta.java deve existir no mesmo projeto
// e estar acessível por esta classe.

public class TesteConta {

    public static void main(String[] args) {

        // Criamos uma nova conta.
        Conta conta = new Conta();

        System.out.println("Saldo inicial: " + conta.getSaldo());

        try {

            // Este valor é maior que o saldo inicial.
            // Conta.saca() deverá lançar uma exceção.
            conta.saca(800);

        } catch (ArithmeticException ex) {

            // O tratamento fica fora da classe Conta.
            System.out.println("Não foi possível realizar o saque.");

            // Recuperamos a mensagem definida no throw.
            System.out.println("Motivo: " + ex.getMessage());
        }

        // Como o saque não aconteceu,
        // o saldo deverá continuar igual.
        System.out.println("Saldo final: " + conta.getSaldo());
    }
}
```

---

# 31. Debugando o throw

Coloque um breakpoint dentro de:

```java
public void saca(double valor)
```

na linha:

```java
if (valor > saldo)
```

Execute `TesteConta` em modo **Debug**.

Observe as variáveis:

```text
valor
saldo
```

Você deverá encontrar algo semelhante a:

```text
valor = 800.0
saldo = 500.0
```

A condição:

```java
valor > saldo
```

será verdadeira.

Avance até:

```java
throw new ArithmeticException("Saldo insuficiente");
```

Antes de executar essa linha, observe a pilha.

Ela poderá estar semelhante a:

```text
saca()
main()
```

Execute a linha do `throw`.

Observe que o método `saca()` não continua normalmente.

O fluxo será transferido para o `catch` existente no `main()`.

---

# 32. Uma observação importante

Não confunda:

```java
throw
```

com:

```java
throws
```

Hoje aprendemos apenas:

```java
throw
```

`throw` é utilizado para **lançar uma exceção**.

Exemplo:

```java
throw new ArithmeticException("Saldo insuficiente");
```

> **IMPORTANTE:** `throws` é outro recurso da linguagem Java e será estudado posteriormente. Não precisamos utilizá-lo nesta aula.

---

# Desafio 01 — Validação de idade

Crie uma classe:

```text
ValidacaoIdade.java
```

Crie um método:

```java
private static void validarIdade(int idade)
```

O método deverá verificar:

```text
idade < 18
```

Quando isso acontecer, lance:

```java
ArithmeticException
```

com a mensagem:

```text
Idade não permitida
```

No `main()`:

1. chame `validarIdade(16)`;
2. utilize `try-catch`;
3. mostre a mensagem da exceção com `getMessage()`;
4. depois do tratamento, mostre:

```text
Fim da validação.
```

### Resultado esperado

Algo semelhante a:

```text
Não foi possível continuar.
Motivo: Idade não permitida
Fim da validação.
```

> **IMPORTANTE:** neste desafio estamos utilizando `ArithmeticException` apenas porque ainda não criamos nossas próprias exceções.

---

# Desafio 02 — Saque com dois testes

Utilizando a classe `Conta`, crie uma nova classe:

```text
TesteSaque.java
```

Crie uma conta com saldo inicial de:

```text
500
```

Realize dois testes separados.

### Teste 1

Tente sacar:

```text
200
```

Mostre o saldo depois do saque.

### Teste 2

Tente sacar:

```text
700
```

Utilize `try-catch` para tratar a exceção.

No final, mostre novamente o saldo.

### Sua tarefa

Em comentários no final do arquivo, responda:

```java
/*
RESPONDA:

1. No primeiro saque, o método saca()
   terminou normalmente?

2. No segundo saque, em qual linha o
   fluxo normal foi interrompido?

3. O saldo foi alterado quando a
   exceção foi lançada?

4. Quem detectou o problema?

5. Quem tratou o problema?
*/
```

> **IMPORTANTE:** não altere a regra de `Conta.saca()` para resolver este desafio.

---

# Resumo da Aula

Hoje aprendemos que uma exceção também é um objeto.

Podemos criar:

```java
new ArithmeticException();
```

Podemos adicionar uma mensagem:

```java
new ArithmeticException("Saldo insuficiente");
```

E podemos lançar a exceção:

```java
throw new ArithmeticException("Saldo insuficiente");
```

Quando `throw` é executado:

```text
fluxo normal
     ↓
throw
     ↓
fluxo interrompido
     ↓
exceção percorre a pilha
     ↓
procura um catch compatível
```

Também vimos que podemos usar exceções para representar situações inválidas de uma regra de negócio.

Exemplo:

```java
if (valor > saldo) {

    throw new ArithmeticException("Saldo insuficiente");
}
```

Assim, o método que detecta o problema pode lançar uma exceção, enquanto outro método decide como tratá-la.

Na próxima etapa do conteúdo, estaremos preparados para avançar para exceções mais adequadas às regras dos nossos próprios sistemas.