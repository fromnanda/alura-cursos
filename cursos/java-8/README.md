# Java 8: Tire proveito dos novos recursos da linguagem

- [Link para o curso](https://cursos.alura.com.br/course/java8-lambdas)
- **Curso iniciado em: 17/11/2017**
- **Curso concluído em: 21/11/2017**

## Aulas

1. :ok: Default methods
2. :ok: Lambdas
3. :ok: Method references
4. :ok: Streams
5. :ok: Mais Streams
6. :ok: Datas

## Anotações

### 1. Default methods

- Permite que se adicione um novo método em uma interface, e esse método seja concreto (diferente das versões anteriores. Sempre existiam métodos abstratos)

- Método sort() com Comparator<>. O comparator fará a implementação de um método compare()

- Método forEach() com Consumer<>. O Consumer fará a implementação de um método accept()

### 2. Lambdas

- Para os casos onde temos uma classe que só tem um método implementado, é uma boa prática a utilização dos Lambdas. O compilador já sabe que um certo método precisa de um certo objeto, e que esse objeto precisa da implementação de um certo método. Por exemplo:

    - Para utilizar o método forEach(), é preciso de um objeto que implemente a interface Consumer<>.
    - O objeto Consumer precisa de uma implementação de seu método accept(). Um jeito de resolvermos isso é da sequinte maneira:
    
        ```palavras.forEach(s -> System.out.println(s));```

- Interface Funcional: uma interface parece muito com uma função, pois só tem um método implementado
- Para o caso da ordenação: 

  ```palavras.sort((s1, s2) -> Integer.compare(s1.length(), s2.length()));```

- Criação de Thread:
    
    ```new Thread(() -> System.out.println("executando um runnable")).start();```

### 3. Method references

- Quando vamos fazer a ordenação dentro de uma lista, precisamos identificar por qual critério iremos ordenar. Por isso, precisamos identificar um **comparador** para esses objetos. No ordenação, iremos utilizar o método comparing do Comparator, e somente ele. O método comparing, por sua vez, necessita de uma função, que determinará o critério de ordenação, por exemplo, o tamanho da string. De uma maneira mais extensão, podemos escrever da seguinte maneira:

    ```java
    Function<String, Integer> funcao = s -> s.length();
    Comparator<String> comparador = Comparator.comparing(funcao);
    palavras.sort(comparador);
    ```

    > Para ordenar a lista, precisamos de um tipo de compaação
    > Esse tipo de comparação deve especificar uma função onde irá devolver um Integer
    > No caso, a nossa função irá ordenar pelo tamanho da string

    Porém, podemos também diminuir algumas linhas desse código, pois como estamos utilizando uma interface funcional (o comparador só possui uma função, ou seja, o comparing, e o comparing só possui uma função também), não precisamos mais indicar cada objeto e função que sejam padrão, podemos então utilizar um lambda novamente:

    ```java
    palavras.sort(Comparator.comparing(s -> s.length()));
    ```

    Mas podemos diminuir ainda mais esse código, utilizando method references, onde todo o processo fica implícito:

    ```java
    palavras.sort(Comparator.comparing(String::length));
    ```

    > Dada uma string, invoca o método length, que devolverá um Integer

### 4. Streams

- Filtra os cursos que só tem mais do que 100 alunos: ```cursos.stream().filter(c -> c.getAlunos() >= 100).forEach( c -> System.out.println(c.getNome()));```

- Filtra pelos cursos que só tem mais do que 100 alunos, mas agora, faz o mapeamento, exibindo o número de alunos : ```cursos.stream().filter(c -> c.getAlunos() >= 100).map( Curso::getAlunos ).forEach(System.out::println);```

- Existem outros métodos, como por exemplo, fazer a soma depois de um filter:

    ```java
    int sum = cursos.stream()
			.filter(c -> c.getAlunos() >= 100)
			.mapToInt(Curso::getAlunos)
			.sum();
		
		System.out.println(sum);
    ```
- [Documentação da API Stream](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html)

### 5. Mais Streams

- Para evitar o erro de nullo, pro exemplo, já existe o método ifPresent: ```cursos.stream().filter(c ->c.getAlunos() >= 100).findAny().ifPresent( c -> System.out.println(c.getNome()) );```

- Para extrar a coleção do filtro criado: ```cursos = cursos.stream().filter(c ->c.getAlunos() >= 100).collect(Collectors.toList());```

- 
```java
cursos.stream()
	.filter(c ->c.getAlunos() >= 100)
	.collect(Collectors.toMap(c -> c.getNome(), c -> c.getAlunos() ))
	.forEach( (nome, alunos) -> System.out.println( nome + " tem " + alunos + " alunos" ) );
```

- parallelStream()

### 6. Datas

**Legenda:**

- :on: em andamento
- :ok: concluído
