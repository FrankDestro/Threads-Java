# Threads-Java
Conceitos e fundamentos de Threads em Java

### Threads Java
* Uma thread é a menor unidade de execução dentro de um processo. Ela permite que você execute múltiplas operações simultaneamente dentro de um programa.

### Processo vs Treads

* Um processo é uma instância em execução de um programa, enquanto uma thread é uma linha de execução dentro desse processo. Vários threads podem existir dentro de um processo, compartilhando recursos como memória.

![image](https://github.com/user-attachments/assets/1cbba9fb-9892-4179-b7d9-9f6adf05bc10)

```css
[Processo 1]
  ├── [Thread 1]
  ├── [Thread 2]
  └── [Thread 3]

[Processo 2]
  ├── [Thread 1]
  └── [Thread 2]

[Processo 3]
  ├── [Thread 1]
  └── [Thread 2]

```

### Thread principal e Thread secundária:

* O thread principal é o ponto de entrada do seu programa (normalmente o método main), e threads secundárias são aquelas criadas e gerenciadas pelo programa para realizar tarefas paralelas.

### Criação de uma Thread 
* Uma thread pode ser criada implementando a interface Runnable ou estendendo a classe Thread.

```js
public class MyThread implements Runnable {
    public void run() {
        System.out.println("Thread em execução");
    }
}

Thread thread = new Thread(new MyThread());
thread.start();

```

### Start, Run e Join:

* start(): Inicia a execução da thread.
* run(): Método que contém o código que a thread executará.
* join(): Faz com que uma thread espere a conclusão de outra antes de continuar sua execução.

```js 
public class TesteThreads {

    static class Tarefa implements Runnable {
        private String nomeTarefa;

        public Tarefa(String nomeTarefa) {
            this.nomeTarefa = nomeTarefa;
        }

        @Override
        public void run() {
            // Simula algum trabalho, por exemplo, dormir por alguns segundos
            System.out.println(nomeTarefa + " iniciou.");
            try {
                Thread.sleep(1000); // Simula o tempo de trabalho da thread
            } catch (InterruptedException e) {
                System.out.println(e.getMessage());
            }
            System.out.println(nomeTarefa + " terminou.");
        }
    }

    public static void main(String[] args) {
        // Criando tarefas (threads)
        Tarefa tarefa1 = new Tarefa("Tarefa 1");
        Tarefa tarefa2 = new Tarefa("Tarefa 2");
        Tarefa tarefa3 = new Tarefa("Tarefa 3");

        // Criando threads
        Thread thread1 = new Thread(tarefa1);
        Thread thread2 = new Thread(tarefa2);
        Thread thread3 = new Thread(tarefa3);

        // Iniciando as threads
        thread1.start();
        thread2.start();
        thread3.start();

        try {
            // Esperando as threads terminarem com o join
            thread1.join();
            thread2.join();
            thread3.join();
        } catch (InterruptedException e) {
            System.out.println(e.getMessage());
        }

        // Depois que todas as threads terminarem
        System.out.println("Todas as tarefas foram concluídas.");
    }
}

```

### Concorrência e Paralelismo:

 * Concorrência: Executar várias tarefas de forma intercalada (não necessariamente simultânea). Pode ocorrer em sistemas com um único processador.
 * Paralelismo: Execução simultânea de várias tarefas em múltiplos núcleos de processadores.

### Sincronização

* Sincronização é necessária quando múltiplas threads acessam recursos compartilhados, como variáveis ou objetos, para evitar condições de corrida. Pode ser feita usando a palavra-chave synchronized ou usando ferramentas como Lock e Semaphore.

```js
public class Test {

    static class CounterTask implements Runnable {

        private int counter = 0;

        @Override
        public void run() {
            synchronized (this) {
                counter++;
                System.out.println(Thread.currentThread().getName() + ":" + counter);
            }
        }
    }

    public static void main(String[] args) {

        CounterTask task = new CounterTask();

        Thread task1 = new Thread(task);
        Thread task2 = new Thread(task);
        Thread task3 = new Thread(task);
        Thread task4 = new Thread(task);

        task1.start();
        task2.start();
        task3.start();
        task4.start();

    }
}

Thread-0:1
Thread-3:2
Thread-1:3
Thread-2:4

```

### Condição de Corrida (Race Condition):

* Acontece quando duas ou mais threads tentam acessar e modificar o mesmo recurso simultaneamente, causando comportamentos inesperados.
* No exemplo abaixo sem o syncronized, as threads irão acessar o mesmo recurso, com um resultado não esperado, o contador apresentará valores aleatórios e não sequencial conforme definido o valor inicial e a sua incrementação. 


```js
public class Test {

    static class CounterTask implements Runnable {

        private int counter = 0;

        @Override
        public void run() {
                counter++;
                System.out.println(Thread.currentThread().getName() + ":" + counter);
        }
    }

    public static void main(String[] args) {

        CounterTask task = new CounterTask();

        Thread task1 = new Thread(task);
        Thread task2 = new Thread(task);
        Thread task3 = new Thread(task);
        Thread task4 = new Thread(task);

        task1.start();
        task2.start();
        task3.start();
        task4.start();

    }
}

Thread-3:4
Thread-0:1
Thread-1:2
Thread-2:3
```

### Thread Pools:
* Thread Pools são usados para gerenciar um conjunto de threads, permitindo reutilizar threads para múltiplas tarefas e reduzir o overhead da criação e destruição de threads. O Executor Framework no Java (como ExecutorService) fornece uma maneira eficiente de gerenciar pools de threads.

Analogia com o Mundo Real
Imagine um restaurante onde há vários cozinheiros (threads). Em vez de ter que contratar um novo cozinheiro toda vez que um pedido é feito (o que seria muito ineficiente), o restaurante tem um número fixo de cozinheiros disponíveis. Quando um pedido chega, o restaurante distribui o trabalho para o próximo cozinheiro disponível, sem precisar contratar novos cozinheiros o tempo todo. Se um cozinheiro terminar um pedido, ele poderá começar outro sem precisar ser "recrutado" novamente.

* Cozinheiros = Threads
* Pedidos = Tarefas
* Restaurante = Thread Pool
* Distribuição dos pedidos = ExecutorService

```js
import java.util.concurrent.*;

public class ThreadPoolExample {

    public static void main(String[] args) {
        // Criando um pool de threads com 3 threads
        ExecutorService executorService = Executors.newFixedThreadPool(3);

        // Submetendo 5 tarefas para execução
        for (int i = 1; i <= 5; i++) {
            final int taskId = i;
            executorService.submit(() -> {
                System.out.println("Tarefa " + taskId + " começou na " + Thread.currentThread().getName());
                try {
                    Thread.sleep(1000); // Simula uma tarefa que leva 1 segundo
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("Tarefa " + taskId + " terminou na " + Thread.currentThread().getName());
            });
        }

        // Finalizando o pool de threads
        executorService.shutdown();
    }
}

Tarefa 1 começou na pool-1-thread-1
Tarefa 2 começou na pool-1-thread-2
Tarefa 3 começou na pool-1-thread-3
Tarefa 1 terminou na pool-1-thread-1
Tarefa 4 começou na pool-1-thread-1
Tarefa 2 terminou na pool-1-thread-2
Tarefa 5 começou na pool-1-thread-2
Tarefa 3 terminou na pool-1-thread-3
Tarefa 4 terminou na pool-1-thread-1
Tarefa 5 terminou na pool-1-thread-2

```


### Volatile e Atomicidade:

* volatile: Palavra-chave que garante que as mudanças feitas a uma variável por uma thread sejam visíveis para todas as outras threads.

Conceito: A palavra-chave volatile em Java garante que qualquer alteração feita a uma variável em uma thread seja imediatamente visível para outras threads. Isso significa que, quando uma thread altera uma variável volatile, o valor dessa variável é atualizado diretamente na memória, sem cache, tornando-a visível para todas as outras threads que acessam a variável.

* Analogia no Mundo Real:
Imagine que você e seus amigos estão jogando um jogo de tabuleiro. Você tem uma carta (variável) que todos precisam olhar para saber qual é a próxima ação a ser tomada. Se você colocar essa carta em uma gaveta (cache), alguns amigos podem olhar para a carta na gaveta, enquanto outros podem ter uma versão desatualizada, pois o conteúdo da gaveta não é atualizado instantaneamente.

Se, ao invés disso, você deixar a carta em um local visível para todos e a atualize sempre que fizer uma mudança, todos terão a mesma visão da carta em tempo real. volatile faz com que a variável seja acessada diretamente da memória principal, sempre atualizada e visível para todos os "jogadores" (threads).

```js 
public class VolatileExample {

    private static volatile boolean flag = false; // Variável volatile

    public static void main(String[] args) throws InterruptedException {

        // Thread 1 que altera o valor da flag
        Thread thread1 = new Thread(() -> {
            try {
                Thread.sleep(1000); // Simula algum trabalho
                flag = true; // Modificando a variável volatile
                System.out.println("Thread 1 alterou o valor de flag para " + flag);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });

        // Thread 2 que monitora o valor de flag
        Thread thread2 = new Thread(() -> {
            while (!flag) {
                // A thread 2 espera até que flag seja true
            }
            System.out.println("Thread 2 percebeu que flag foi alterada para " + flag);
        });

        thread2.start();
        thread1.start();

        thread1.join();
        thread2.join();
    }
}

```

* Atomicidade: Operações atômicas são aquelas que são feitas de uma vez, sem interrupção, como os métodos da classe AtomicInteger.

Atomicidade (Operações Atômicas)
Conceito: Atomicidade significa que uma operação é executada de forma completa e indivisível, sem ser interrompida. Em outras palavras, uma operação atômica é garantida para ser concluída sem interferência de outras threads, de modo que o valor final da operação seja consistente, mesmo em um ambiente multithreaded.

Em Java, classes como AtomicInteger, AtomicLong e outras oferecem métodos que executam operações atômicas (como incremento ou decremento) de forma segura em ambientes com múltiplas threads.

Analogia no Mundo Real:
Imagine uma caixa registradora onde várias pessoas podem tentar adicionar dinheiro ao mesmo tempo. Se duas pessoas tentam adicionar dinheiro ao mesmo tempo e o processo não for controlado, o valor final na caixa pode não ser correto. Atomicidade é como se você colocasse um bloqueio na caixa registradora para garantir que apenas uma pessoa pode adicionar dinheiro de cada vez. Ou seja, mesmo que duas pessoas tentem adicionar dinheiro ao mesmo tempo, o processo será feito de maneira sequencial, garantindo que o valor final seja correto.

Em termos de programação, uma operação atômica garante que o valor de uma variável será alterado de forma segura, sem que outra thread interfira no processo, e sem que o valor intermediário seja visível para outras threads.



```js 
import java.util.concurrent.atomic.AtomicInteger;

public class AtomicExample {

    private static AtomicInteger counter = new AtomicInteger(0);

    public static void main(String[] args) throws InterruptedException {

        // Criação de 5 threads para incrementar a variável counter
        Thread thread1 = new Thread(() -> incrementCounter());
        Thread thread2 = new Thread(() -> incrementCounter());
        Thread thread3 = new Thread(() -> incrementCounter());
        Thread thread4 = new Thread(() -> incrementCounter());
        Thread thread5 = new Thread(() -> incrementCounter());

        // Iniciando as threads
        thread1.start();
        thread2.start();
        thread3.start();
        thread4.start();
        thread5.start();

        // Esperando as threads terminarem
        thread1.join();
        thread2.join();
        thread3.join();
        thread4.join();
        thread5.join();

        // Exibe o valor final de counter
        System.out.println("Valor final do counter: " + counter.get());
    }

    private static void incrementCounter() {
        for (int i = 0; i < 1000; i++) {
            counter.incrementAndGet(); // Incremento atômico
        }
    }
}

```

O que acontece:
* AtomicInteger: A variável counter é uma instância da classe AtomicInteger, que fornece métodos atômicos para operações como incrementAndGet().
* Thread-safe: Mesmo que várias threads tentem incrementar a variável ao mesmo tempo, a operação de incremento é atômica, o que significa que a operação será executada sem interferências. O valor final será correto, sem "condições de corrida".
Resultado Esperado:
* O valor final de counter será 5000, mesmo com várias threads tentando incrementar ao mesmo tempo, porque cada incremento é atômico e protegido contra interferência.




























### Esperando e Notificando Threads (Wait/Notify):

* As threads podem usar os métodos wait(), notify(), e notifyAll() para sincronizar sua execução e comunicação entre threads. Usados geralmente em blocos sincronizados para controle de concorrência.

### Executando Tarefas com CompletableFuture:

* CompletableFuture permite realizar tarefas assíncronas e combiná-las de maneira mais flexível. Ideal para quando você precisa realizar várias operações de forma não bloqueante.

### ThreadLocal:

* ThreadLocal é uma maneira de criar variáveis que são únicas para cada thread, evitando compartilhamento de dados entre elas.

### Threads em Java 8 e além:

* Em versões mais recentes do Java, há recursos como Stream.parallel() e outras abstrações que permitem executar operações em paralelo de maneira mais simples, sem precisar gerenciar diretamente as threads.

### Execução Assíncrona e Futura:

* Trabalhar com Future e Callable em vez de Thread pode facilitar o controle de tarefas assíncronas, permitindo a obtenção de resultados de threads de maneira mais controlada.



