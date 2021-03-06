# Laravel: facilitando o desenvolvimento PHP

- [Link para o curso](https://cursos.alura.com.br/course/laravel)
- **Curso iniciado em: 02/02/2018**
- **Curso concluído em: 13/02/2018**

## Aulas

1. :ok: Novo projeto com Laravel
2. :ok: MVC e conexão com banco de dados
3. :ok: Trabalhando com a View
4. :ok: Parâmetros da request e URL
5. :ok: Views mais flexíveis e poderosas
6. :ok: Request e métodos HTTP
7. :ok: Os diferentes tipos de resposta

## Anotações

### 1. Novo projeto com Laravel

- [Site oficial](https://laravel.com/)

- [Laravel Recipes](http://laravel-recipes.com/)

- [Site oficial > Configuration](https://laravel.com/docs/5.0/configuration)

- [Packagist](https://packagist.org/)

- O curso define o arquivo **app/Http/routes.php** para a definição das rotas. O Laravel agora possui uma pasta específica para isso. Mudei o arquivo **web.php** para o primeiro exercício.

- Instalação/criação de um projeto com a versão do curso: ``composer create-project laravel/laravel estoque "5.0."``

- Criação de um projeto normal: ``laravel new nome-do-projeto``

- Minha instalação: Precisa instalar a versão específica do curso. Optei para criar o projeto com a versão mais recente do Laravel e do Composer mesmo.

- Alterando o nome padrão: ``php artisan app:name estoque``

### 2. MVC e conexão com banco de dados

- Assim como vários frameworks, o Laravel utiliza o padrão MVC. No caso, a nossa primeira lógica será a de listagem de produtos. As lógicas do sistema ficam por conta dos Controllers, por isso a primeira é a **ProdutoController**.

- Quando criamos o arquivo na pasta **app/Http/Controllers**, precissamos definir seu namespace, ou seja, o seu local na nossa aplicação. Por isso, logo na abertura da tag php, no cabeçalho do arquivo, colocamos o ``namespace estoque\Http\Controllers;``

- Definindo o comportamento do Controller, precisa criar uma rota para ele. No caso, definimos que ele aportará para um método específico do nosso Controller:

    ```php
    Route::get('/produtos', 'ProdutoController@lista');
    ```

- Erro: sem key para a aplicação. Solução: ``php artisan key:generate`` antes de startar a aplicação. [@stackoverflow](https://stackoverflow.com/questions/44839648/no-application-encryption-key-has-been-specified-new-laravel-app)

### 3. Trabalhando com a View

- O controller não deve ser responsável por qualquer visualização. Esse, obviamente, é o trabalho para as views. Então, movemos nosso código HTML para um arquivo na **resources/views**.

- No Controller, faremos somente o select no banco, e retornaremos o resultado. Para retornar, utilizamos o **helper method** ``view()``. Esse método indica que deverá ser chamado uma página view. Precisamos passar o resultado do select, assim, utilizamos o método ``with()`` com a chave e valor. Existem diferentes maneiras de retornar esse valor:

```php
// Sendo listagem.php o nome do arquivo view, produtos o nome da variavel no controller e na view
view('listagem')->withProdutos($produtos);

view('listagem')->with('produtos', $produtos);

return view('listagem', ['produtos' => $produtos]); // Bom para retornar mais de um valor
```

### 4. Prâmetros da request e URL

- Criamos uma página para os detalhes de um produtos. Para isso precisamos fazer uma view, um método no controller e uma nova rota.

- Na página de listagem dos produtos, adicionamos um campo com um link para os detalhes de cada produto:

```php
<td><a href="/produtos/mostra/<?= $p->id ?>"><i class="material-icons">search</i></a></td>
```

- A view mostrará todas as informações de um produto, portanto usamos a variável **$p** para exibir as informações. Essa view é a **detalhes.php**

```php
<h1>Detalhes do produto <?= $p->nome ?></h1>
```

- O método no controller é o **mostra()**, ele fará o select com o where do id do produto passado. Ele então faz o retorn pra view detalhes:

```php
// Recebendo o id como parametro, ele já sabe que é pela rota da requisição
public function mostra($id) {
        //$id = Request::route('id');
        $produto = DB::select('select * from produtos where id = ?', [$id]);

        if (empty($produto)) {
            return "Esse produto não existe";
        }

        return view('detalhes')->with('p', $produto[0]);
    }
```

- A rota será de certa forma dinâmica, ou seja, terá como parte dela o id que especificarmos na listagem dos produtos. Especificamos a chave da rota com a variável {id}, o método no controller, além de restringir, atra´vesl de uma expressão regular, qual o tipo de dado será o id (isso evita a ambiguidade de rotas)

```php
Route::get('/produtos/mostra/{id}', 'ProdutoController@mostra')->where('id', '[0-9]+');
```

### 5. Views mais flexíveis e poderosas

- Nessa aula foi explicado o Blade, que é a template engine do Laravel. Ele ajudará na reutilização de partes importantes e repetidas da nossas views. Por exemplo, todo o cabeçalho do html é igual nas diferentes páginas que montamos até agora, e seria interessante ter somente uma "versão" desse código no nosso projeto

- Para isso, na área de views, criamos um arquivo **principal.blade.php**. Nele colocamos o html repetitido das nossas páginas. Porém, no meio da nossa página terão informações pertinentes a cada uma delas, e marcamos usse espaço com a tag **yield**: ``@yield('conteudo');``

- Depois disso, precisamos renomear as nossas views para essa mesma extensão do **blade.php**. Cada uma terá que importar a view principal, e fazemos isso com ``@extends('principal')``

- Em cada página precisamos definir onde começa e termina o conteudo especificado na view principal.Para isso utilizamos a tag **section**: ``@section('conteudo')``. Para terminar essa sessão, utilizamos o ```@stop``

- O blade também nos dá uma nova maneira de exibir as váriaveis php que estamos manipulando, e é através de duas chaves **{{ }}**. Assim, na listagem por exemplo, podemos exibir o nome do produto dessa maneira: ``<td>{{$p->nome}}</td>``

- Outra tag do blade é o @foreach, que utilizamos na listagem. O nova listagem fica dessa maneira:

```php
@foreach ($produtos as $p)
    <tr class="table-{{ $p->quantidade <=1 ? 'danger' : ''}}">
        <td>{{$p->nome}}</td>
        <td>{{$p->valor}}</td>
        <td>{{$p->descricao}}</td>
        <td>{{$p->quantidade}}</td>
        <td><a href="/produtos/mostra/{{$p->id}}"><i class="material-icons">search</i></a></td>
    </tr>
@endforeach
```

- Outros loops:

```php
@for ($i = 0; $i < 10; $i++)
    O indice atual é {{ $i }}
@endfor

@while (true)
    Entrando em looping infinito!
@endwhile

@forelse($produtos as $p)
    <li>{{ $p->nome }}</li>
@empty
    <p>Não tem nenhum produto!</p>
@endforelse
```

- Outras condições:

```php
@unless (1 == 2)
      Esse texto sempre será exibido!
@endunless
```

### 6. Request e métodos HTTP

- Precisamos agora fazer um método para adicionarmos produtos no nosso estoque. Faremos a rota (``Route::get('/produtos/novo', 'ProdutoController@novo');``) e o método no controller.

- O método retornará uma view de um formulário para os campos do novo produto ``return view('produto.formulario');``

- Faremos o cadastro pelo método post, que é mais discreto e um pouco mais seguro que o get. Nesse fomulário, o laravel precisa que haja um token de segurança, que será um campo no nosso formulário. Esse token precisa ter o nome **_token** por padrão no Laravel:

```php
<form action="/produtos/adiciona" method="post">

    <input type="hidden" name="_token" value="{{csrf_token()}}" />
```

- A rota/método **adiciona** cuidará da inserção do produto no banco:

```php
$nome = Request::input('nome');
$valor = Request::input('valor');
$quantidade = Request::input('quantidade');
$descricao = Request::input('descricao');

DB::insert('insert into produtos (nome, valor, quantidade, descricao) values (?,?,?,?)',
            array($nome, $valor, $quantidade, $descricao));

return view('produto.adicionado')->with('nome', $nome);
```

- A rota do adiciona será um pouco diferente, pois utilizamos o método **POST**

```php
Route::post('/produtos/adiciona', 'ProdutoController@adiciona');
```

- A view **adicionado** exibe uma mensagem de sucesso da inserção:

```php
<div class="alert alert-success">
    Produto {{$nome}} adicionado com sucesso!
</div>
```

### 7. Os diferentes tipos de resposta

- Nessa aula mudamos a forma como exibimos um produto adicionado. Agora a mensagem de sucesso é exibido na tela de listagem inicial da aplicação.

- Mudamos o controller, no método adiciona, para que ele redirecione para uma outra página, no casso ou outro método, mantendo na sessão o nome do produto adicionado

```php
return redirect()->action('ProdutoController@lista')->withInput(Request::only('nome'));
```

- Na view de listagem, podemos agora verificar se essa variavel nome está definida na nossa sessão, para então exibir uma mensagem:

```php
@if (old('nome'))
    <div class="alert alert-success">
        Produto {{old('nome')}} adicionado com sucesso!
    </div>
@endif
```

**Legenda:**

- :on: em andamento
- :ok: concluído