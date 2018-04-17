# Laravel II: Mais técnicas e webapps poderosas

- [Curso na Alura](https://cursos.alura.com.br/course/laravel-2)
- **Iniciado em 15/02/2018**

## Aulas

1. :ok: Eloquent ORM
2. :ok: Trabalhando com Migrations
3. Validando os dados de entrada
4. Autenticação e segurança
5. Relacionamentos com Eloquent
6. Preenchendo dados com Seeds
7. Mais produtividade com Artisan

## Anotações

### 1. Eloquent ORM

O Eloquent é um framework ORM (Object Relational Mapping) que faz o mapeamento dos objetos para o modelo relacional.
Para começar, é preciso ter uma classe Model de uma tabela do banco de dados. No caso, foi utilizadaa tabela de **produtos**. Na linha de comando, basta digitar ``php artisan make:model Produto``. A classe produto será criada na pasta app. O eloquent consegue fazer todo esse processo de mapeamento pois **ele levará em conta o nome da tabela como sendo o plural da sua classe**. Assim, a tabela que ele reconhece como **produtos** faz referência a classe **Produto**, sempre minúsculo e com o **s** no final. É possível fazer sua própria classe e customizá-la do zero, mas o Eloquent ajuda bastante o processo se seguirmos suas práticas. No caso, é só adiciona a propriedade ``protected $table = 'produtos';`` para definir a tabela.

O Eloquent também assume por padrão que sua tabela tem essas duas colunas **updated_at** e **created_at**. Mas, para customizar esse padrão, é só definir ``public $timestamps = false;``.

- Métodos herdados de Model
  - all() -> **select *** -> ``$produtos = Produto::all();``
  - find() -> **select * from where id = [id]** -> ``$produto = Produto::find($id);``

- Inserção

Agora que temos uma classe representando a tabela, podemos associar os valores as propriedades de um objeto dessa classe

```php
    $produto = new Produto();
    $produto->nome = Request::input('nome');
```

Podemos também indicar que vamos precisar de todos os valores do Request ``$params = Request::all();``, mas não é recomendado fazer isso sem definir na classe a propriedade **fillable** (erro de MassAssignment). Essa propriedade indica quais campos serão populados (como definir as propriedades de uma classe) ``protected $fillable = array('nome', 'descricao', 'valor', 'quantidade');``. O contrário desse atributo é o **guarded**, definimos quais campos não podem ser populados.

Para a inserção, podemos então utilizar o método **save()** do objeto, ficando assim:

```php
    $params = Request::all();
    $produto = new Produto($params);
    $produto->save();
```

A maneira mais curta de fazer esse processo de inserção no banco é com o **factory method create()** ``Produto::create(Request::all());``

- Remoção

A remoção será bem mais fácil com as possibilidades do Eloquent:

```php
    $produto = Produto::find($id);
    $produto->delete();
```

### 2. Trabalhando com Migrations

É comum trabalharmos com um projeto que já possui um banco criado, e nesse processo de mudança para um framework é comum criamos um campo novo em uma tabela existente, ou até uma tabela nova. Com as migrations ensinam o Laravel como criar, atualizar ou recuperar o estado anterior do esquema de seu banco de dados, como um controle de versão.

A migration sempre possui dois métodos **up** e **down**, um para quando criamos um atualizarmos uma tabela e outro para quando quisermos desfazer essa alteração.

Para criar uma migração, podemos executar: ``php artisan make:migration adiciona_tamanho_no_produto``
No arquivo criado em **database/migrations**, no método up, utilizaremos o método table do Schema. No caso, iremos criar uma coluna na table de produtos, do tipo text/string, com no máximo 100 caracteres:

```php
Schema::table('produtos', function($table) {
        $table->string('tamanho', 100);
    });
```

No método down, faremos o drop dessa coluna:

```php
Schema::table('produtos', function($table) {
        $table->dropColumn('tamanho');
    });
```

- Para executar a migração: ``php artisan migrate``
- Para reverter a última migração: ``php artisan migrate:rollback``

**Atenção:**

- Quando criar um campo, lembre-se de alterar todas as utilizações que esse campo terá, como por exemplo, na propriedade fillable, precisamos adicionar tamanho como um campo para se popular

### 3. Validando os dados de entrada

### 4. Autenticação e segurança

### 5. Relacionamentos com Eloquent

### 6. Preenchendo dados com Seeds

### 7. Mais produtividade com Artisan

**Legenda:**

- :on: em andamento
- :ok: concluído