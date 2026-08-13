# Aula 01 — Pilha de Execução em Java

**Curso:** Técnico em Desenvolvimento de Sistemas  
**Turma:** 3º Ano  
**Disciplina:** Programação / Java  
**Professor:** Richardson Schawarski Cruz  

---

# Objetivos da Aula

Ao final desta aula, você deverá ser capaz de:

- Compreender o que é a **pilha de execução** do Java.
- Entender como os métodos são chamados e finalizados.
- Identificar a ordem de execução dos métodos.
- Utilizar o **Debug do IntelliJ IDEA**.
- Criar e utilizar **breakpoints**.
- Utilizar **Step Over** e **Step Into**.
- Observar os métodos presentes na pilha durante a execução.
- Acompanhar o valor de uma variável durante o Debug.

---

# 1. Contextualização

Até agora, quando executamos um programa Java, normalmente observamos apenas o resultado no console.

Por exemplo:

```java
public class Main {

    public static void main(String[] args) {
        System.out.println("Olá!");
    }
}
```

Executamos e vemos:

```text
Olá!
```

Mas o Java realizou algumas operações antes de essa mensagem aparecer.

Quando nosso programa começa a possuir vários métodos, precisamos entender:

> Como o Java sabe qual método está executando e para qual método deverá retornar quando terminar?

Para organizar isso, o Java utiliza uma estrutura chamada **pilha de execução**.

---

# 2. Analogia — Uma pilha de tarefas

Imagine que você está realizando uma tarefa:

```text
Corrigir uma atividade
```

Durante a correção, você precisa consultar a nota do aluno.

Sua situação agora é:

```text
Consultar a nota
Corrigir uma atividade
```

Enquanto consulta a nota, percebe que precisa abrir uma planilha.

Agora temos:

```text
Abrir a planilha
Consultar a nota
Corrigir uma atividade
```

Você precisa terminar primeiro a tarefa que está no topo.

Depois de consultar a planilha:

```text
Consultar a nota
Corrigir uma atividade
```

Terminou a consulta:

```text
Corrigir uma atividade
```

Terminou a correção:

```text
Pilha vazia
```

O Java trabalha de maneira semelhante quando executamos métodos.

---

# 3. Criando nosso projeto

Abra o **IntelliJ IDEA**.

Crie um novo projeto Java.

Utilize o nome:

```text
java-pilha
```

Escolha o JDK disponível nos computadores do laboratório.

> **IMPORTANTE:** nesta aula não utilizaremos nenhuma biblioteca externa. Utilizaremos somente recursos da própria linguagem Java.

Dentro da pasta `src`, crie uma classe chamada:

```text
Fluxo
```

O arquivo deverá ser:

```text
Fluxo.java
```

---

# 4. Criando o primeiro método

Vamos começar pelo ponto de entrada do nosso programa.

Digite:

```java
public class Fluxo {

    public static void main(String[] args) {

        System.out.println("Início do main");

        System.out.println("Fim do main");
    }
}
```

Execute o programa.

O resultado será:

```text
Início do main
Fim do main
```

Até aqui não temos nenhuma novidade.

Nosso programa começou no método:

```java
main()
```

e terminou no mesmo método.

---

# 5. O método main()

Todo programa Java precisa possuir um ponto de início.

No nosso exemplo, esse ponto é:

```java
public static void main(String[] args)
```

Quando executamos a classe `Fluxo`, a JVM inicia a execução pelo método `main()`.

Podemos imaginar nossa pilha neste momento assim:

```text
┌──────────────┐
│    main()    │
└──────────────┘
```

O método `main()` está sendo executado.

---

# 6. Criando outro método

Agora vamos criar um método chamado `metodo1()`.

Adicione o método abaixo:

```java
private static void metodo1() {

    System.out.println("Início do metodo1");

    System.out.println("Fim do metodo1");
}
```

Nosso programa ficará:

```java
public class Fluxo {

    public static void main(String[] args) {

        System.out.println("Início do main");

        System.out.println("Fim do main");
    }

    private static void metodo1() {

        System.out.println("Início do metodo1");

        System.out.println("Fim do metodo1");
    }
}
```

Execute novamente.

Observe que nada mudou.

Por quê?

Porque **criar um método não significa executá-lo**.

Precisamos chamar esse método.

---

# 7. Chamando metodo1()

Dentro do `main()`, adicione:

```java
metodo1();
```

O código ficará:

```java
public class Fluxo {

    public static void main(String[] args) {

        System.out.println("Início do main");

        metodo1();

        System.out.println("Fim do main");
    }

    private static void metodo1() {

        System.out.println("Início do metodo1");

        System.out.println("Fim do metodo1");
    }
}
```

Execute.

Resultado:

```text
Início do main
Início do metodo1
Fim do metodo1
Fim do main
```

Observe algo importante.

O Java estava executando:

```text
main()
```

Quando encontrou:

```java
metodo1();
```

ele precisou interromper temporariamente o `main()` e executar `metodo1()`.

Nossa pilha passou a ser:

```text
┌──────────────┐
│  metodo1()   │ ← executando
├──────────────┤
│    main()    │
└──────────────┘
```

O `main()` continua existindo.

Ele apenas está esperando `metodo1()` terminar.

Quando `metodo1()` termina:

```text
┌──────────────┐
│    main()    │ ← volta a executar
└──────────────┘
```

Por isso aparece:

```text
Fim do main
```

somente depois de:

```text
Fim do metodo1
```

---

# 8. Criando metodo2()

Vamos adicionar mais um nível.

Crie:

```java
private static void metodo2() {

    System.out.println("Início do metodo2");

    System.out.println("Fim do metodo2");
}
```

Agora vamos chamar `metodo2()` dentro de `metodo1()`:

```java
private static void metodo1() {

    System.out.println("Início do metodo1");

    metodo2();

    System.out.println("Fim do metodo1");
}
```

Execute novamente.

Resultado esperado:

```text
Início do main
Início do metodo1
Início do metodo2
Fim do metodo2
Fim do metodo1
Fim do main
```

---

# 9. O que aconteceu com a pilha?

Primeiro:

```text
┌──────────────┐
│    main()    │
└──────────────┘
```

O `main()` chama `metodo1()`:

```text
┌──────────────┐
│  metodo1()   │
├──────────────┤
│    main()    │
└──────────────┘
```

Depois `metodo1()` chama `metodo2()`:

```text
┌──────────────┐
│  metodo2()   │ ← executando
├──────────────┤
│  metodo1()   │
├──────────────┤
│    main()    │
└──────────────┘
```

Agora temos três métodos na pilha.

O Java executa o método que está no **topo da pilha**.

Quando `metodo2()` termina, ele é removido:

```text
┌──────────────┐
│  metodo1()   │ ← executando
├──────────────┤
│    main()    │
└──────────────┘
```

Quando `metodo1()` termina:

```text
┌──────────────┐
│    main()    │ ← executando
└──────────────┘
```

E quando `main()` termina:

```text
Pilha vazia
```

O programa chegou ao fim.

---

# 10. Melhorando metodo2()

Vamos colocar uma repetição dentro de `metodo2()`.

Altere o método:

```java
private static void metodo2() {

    System.out.println("Início do metodo2");

    for (int i = 1; i <= 5; i++) {

        System.out.println(i);
    }

    System.out.println("Fim do metodo2");
}
```

Execute.

Resultado:

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

Observe que o `for` não cria outro método.

Portanto, continuamos dentro de:

```text
metodo2()
```

A variável `i` será interessante para nossa próxima experiência.

Vamos acompanhar seu valor enquanto o programa estiver executando.

---

# 11. Código completo até este momento

Confira se seu código está assim:

```java
// IMPORTANTE:
// O arquivo deve se chamar Fluxo.java.
//
// Não precisamos instalar nenhuma biblioteca externa.
// Tudo que estamos utilizando pertence à própria linguagem Java.

public class Fluxo {

    public static void main(String[] args) {

        // O programa começa sua execução pelo método main.
        System.out.println("Início do main");

        // Ao chamar metodo1(), ele será colocado no topo
        // da pilha de execução.
        metodo1();

        // Esta linha somente será executada depois
        // que metodo1() terminar.
        System.out.println("Fim do main");
    }

    private static void metodo1() {

        System.out.println("Início do metodo1");

        // metodo1() ficará aguardando enquanto
        // metodo2() estiver sendo executado.
        metodo2();

        // O Java retorna para esta linha
        // quando metodo2() terminar.
        System.out.println("Fim do metodo1");
    }

    private static void metodo2() {

        System.out.println("Início do metodo2");

        // Vamos utilizar esta variável durante o Debug
        // para observar seu valor mudando.
        for (int i = 1; i <= 5; i++) {

            System.out.println(i);
        }

        System.out.println("Fim do metodo2");
    }
}
```

Não apague este código ao final da aula.

> **IMPORTANTE:** continuaremos trabalhando com este projeto nas próximas aulas.

---

# 12. Debug — enxergando o programa por dentro

Até agora estamos deduzindo o que acontece.

Mas o IntelliJ permite acompanhar a execução do programa.

Para isso utilizaremos o **Debug**.

O Debug permite:

- pausar o programa;
- executar uma linha por vez;
- entrar dentro de métodos;
- observar variáveis;
- acompanhar a pilha de execução.

É como se pudéssemos executar nosso programa em **câmera lenta**.

---

# 13. Criando um Breakpoint

Localize esta linha:

```java
System.out.println("Início do main");
```

Clique na margem esquerda do editor, ao lado do número da linha.

Uma marca aparecerá.

Esse ponto é chamado de:

```text
Breakpoint
```

ou:

```text
Ponto de interrupção
```

Ele significa:

> "Quando o Java chegar nesta linha, pause a execução."

---

# 14. Executando em modo Debug

Em vez de executar normalmente com **Run**, execute utilizando **Debug**.

O programa deverá iniciar e parar no breakpoint.

Observe que o IntelliJ abrirá a área de depuração.

Procure principalmente:

```text
Variables
```

e:

```text
Frames / Call Stack
```

Os nomes podem variar ligeiramente dependendo da versão do IntelliJ instalada no laboratório.

---

# 15. Observando a pilha

Neste momento provavelmente aparecerá algo relacionado a:

```text
main()
```

Isso acontece porque estamos dentro do método `main()`.

Continue a execução lentamente até chegar nesta linha:

```java
metodo1();
```

Agora precisamos conhecer dois comandos importantes.

---

# 16. Step Over

O comando:

```text
Step Over
```

executa a linha atual sem entrar detalhadamente no método chamado.

Imagine:

```java
metodo1();
```

Se utilizarmos **Step Over**, estamos dizendo:

> "Execute `metodo1()`, mas eu não quero acompanhar o que acontece dentro dele agora."

O Java executará o método e retornará para a próxima linha do `main()`.

---

# 17. Step Into

Reinicie o Debug.

Quando chegar novamente em:

```java
metodo1();
```

utilize:

```text
Step Into
```

Agora o IntelliJ deverá entrar dentro de:

```java
private static void metodo1()
```

Observe a pilha de execução.

Devemos ter algo semelhante a:

```text
metodo1()
main()
```

Isso significa que:

```text
metodo1()
```

está sendo executado e:

```text
main()
```

está aguardando.

---

# 18. Descendo mais um nível

Continue até:

```java
metodo2();
```

Utilize novamente:

```text
Step Into
```

Agora observe a pilha.

Teremos algo semelhante a:

```text
metodo2()
metodo1()
main()
```

Compare com o desenho que fizemos anteriormente:

```text
┌──────────────┐
│  metodo2()   │
├──────────────┤
│  metodo1()   │
├──────────────┤
│    main()    │
└──────────────┘
```

Agora não estamos apenas imaginando.

Estamos vendo a pilha de execução do programa.

---

# 19. Observando uma variável

Continue a execução até entrar no `for`:

```java
for (int i = 1; i <= 5; i++)
```

Observe a área:

```text
Variables
```

Procure a variável:

```text
i
```

Avance utilizando **Step Over**.

Observe seu valor:

```text
i = 1
```

Depois:

```text
i = 2
```

Depois:

```text
i = 3
```

e assim sucessivamente.

Isso demonstra outra utilidade importante do Debug:

> Podemos observar o valor das variáveis enquanto o programa está sendo executado.

Essa ferramenta será muito útil quando nossos programas começarem a apresentar comportamentos inesperados.

---

# 20. O que aprendemos sobre a pilha?

Quando um método chama outro método, o Java precisa lembrar:

> "Quando esse método terminar, para onde devo voltar?"

Para isso existe a **pilha de execução**.

No nosso programa:

```text
main()
```

chamou:

```text
metodo1()
```

que chamou:

```text
metodo2()
```

Portanto:

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

Finalmente:

```text
Pilha vazia
```

---

# 21. Por que estamos aprendendo isso?

Talvez você esteja pensando:

> "Professor, mas o assunto não era Exceções?"

Sim.

Antes de entendermos exceções, precisamos entender a pilha.

Nas próximas aulas veremos situações em que algo acontece dentro de um método e ele **não consegue terminar normalmente**.

Quando isso acontecer, precisaremos descobrir:

```text
Onde aconteceu?
```

```text
Qual método estava sendo executado?
```

```text
Quem chamou esse método?
```

```text
Para onde o problema foi enviado?
```

A pilha de execução será fundamental para responder essas perguntas.

---

# Desafio 01 — Descobrindo a ordem

Crie uma nova classe chamada:

```text
Pedido.java
```

Crie os seguintes métodos:

```text
main()
prepararPedido()
calcularTotal()
```

A chamada deverá acontecer nesta ordem:

```text
main()
   ↓
prepararPedido()
   ↓
calcularTotal()
```

Cada método deverá mostrar uma mensagem quando **iniciar** e outra quando **terminar**.

Exemplo:

```text
Início do main
...
...
...
Fim do main
```

### Sua tarefa

Antes de executar o programa:

1. Escreva em um comentário qual você acredita que será a ordem das mensagens.
2. Execute o programa.
3. Compare sua previsão com o resultado.

> **IMPORTANTE:** este exercício é separado do projeto principal. Não altere `Fluxo.java`.

---

# Desafio 02 — Investigando com Debug

Utilizando a classe `Pedido.java` criada no desafio anterior:

1. Coloque um breakpoint no início do `main()`.
2. Execute utilizando **Debug**.
3. Utilize **Step Into** para entrar em `prepararPedido()`.
4. Utilize novamente **Step Into** para entrar em `calcularTotal()`.
5. Observe a pilha de execução.

Quando estiver dentro de `calcularTotal()`, responda em comentários no final do arquivo:

```java
/*
RESPONDA:

1. Qual método está no topo da pilha?

2. Qual método chamou calcularTotal()?

3. Qual método iniciou a execução do programa?

4. Depois que calcularTotal() terminar,
   para qual método o Java deverá retornar?
*/
```

Não é necessário entregar imagens do Debug.

O objetivo é conseguir **observar a pilha e interpretar o que está acontecendo**.

---

# Resumo da Aula

Hoje aprendemos que o Java utiliza uma **pilha de execução** para controlar os métodos que estão sendo executados.

Quando um método chama outro:

```text
novo método
     ↓
entra no topo da pilha
```

Quando ele termina:

```text
sai da pilha
     ↓
Java retorna ao método anterior
```

Também aprendemos a utilizar recursos básicos do Debug do IntelliJ:

```text
Breakpoint
     ↓
Debug
     ↓
Step Over
     ↓
Step Into
     ↓
Variables
     ↓
Call Stack / Frames
```

Na próxima aula, vamos provocar um problema dentro dessa pilha.

E veremos o que acontece quando um método **não consegue terminar sua execução normalmente**.