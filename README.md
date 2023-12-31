# FilamentPHP na Prática v2

https://codeexperts.com.br/


## <a name="indice">Índice</a>

1. [1. Primeiros Passos](#parte1)
2. [01 - Seja Bem Vindo](#parte2)
3. [02 - O FilamentPHP](#parte3)
4. [03 - Ambientando Projeto](#parte4)
5. [04 - Iniciando Primeiro Recurso](#parte5)
6. [05 - Compondo Listagem](#parte6)
7. [06 - Compondo Nosso Primeiro Form](#parte7)
8. [07 - Trabalhando com Mutate na Criação](#parte8)
9. [08 - Mutate na Edição](#parte9)
10. [09 - Manipulando Slug Dinâmico](#parte10)
11. [2. Buscas, Filtros, Ordenação e Relacionamento M:M](#parte11)
12. [10 - Introdução](#parte12)
13. [11 - Ordenação na Tabela](#parte13)
14. [12 - Busca na Tabela](#parte14)
15. [13 - Filtros na Tabela](#parte15)
16. [14 - Adicionando Upload Form e Imagem na Tabela](#parte16)
17. [15 - Produtos e Categorias Relacionamento](#parte17)
18. [16 - Relation Managers](#parte18)
19. [17 - Conclusões](#parte19)
20. [3. Recurso User, ACL e Ações](#parte20)
21. [18 - Intro e Recurso User](#parte21)
22. [19 - Habilitando e Desabilitando Áreas Recurso](#parte22)
23. [20 - Acão Alterar Senha Edição](#parte23)
24. [21 - Ação Alterar Senha na Listagem](#parte24)
25. [22 - ACL: Policy e Filament](#parte25)
26. [23 - Plugin Shield Filament ACL](#parte26)
27. [24 - Add Papél Form Usuário e Conclusões](#parte27)
28. [4. Custom Pages, Menus, Form Steps e Mais](#parte28)
29. [25 - Intro e Custom Page](#parte29)
30. [26 - Modificando Menu Navegação](#parte30)
31. [27 - Falando Sobre Recurso de Pedidos](#parte31)
32. [28 - Filtro com Datepicker](#parte32)
33. [29 - Links para Outros Recursos](#parte33)
34. [30 - Custom Forms: Column, Card e Fieldset](#parte34)
35. [31 - Custom Forms: Tabs, Wizard e Section](#parte35)
36. [32 - Busca Global R](#parte36)
37. [33 - Conclusão](#parte37)
---


## <a name="parte1">1 - 1. Primeiros Passos</a>

https://filamentphp.com/docs/2.x/admin/installation

```

 sail composer require filament/filament:"^2.17.4"


```

```

```

[Voltar ao Índice](#indice)

---


## <a name="parte2">2 - 01 - Seja Bem Vindo</a>



[Voltar ao Índice](#indice)

---


## <a name="parte3">3 - 02 - O FilamentPHP</a>



[Voltar ao Índice](#indice)

---


## <a name="parte4">4 - 03 - Ambientando Projeto</a>



[Voltar ao Índice](#indice)

---


## <a name="parte5">5 - 04 - Iniciando Primeiro Recurso</a>

```
sail php artisan make:model                
 ┌ What should the model be named? ─────────────────────────────┐
 │ Product                                                      │
 └──────────────────────────────────────────────────────────────┘

 ┌ Would you like any of the following? ────────────────────────┐
 │ Factory                                                      │
 │ Migration                                                    │
 └──────────────────────────────────────────────────────────────┘

   INFO  Model [app/Models/Product.php] created successfully.

   INFO  Factory [database/factories/ProductFactory.php] created successfully.  

   INFO  Migration [database/migrations/2023_10_16_211510_create_products_table.php] created successfully.

```

```
sail php artisan make:filament-resource ProductResource

```

```php
 public function up(): void
    {
        Schema::create('products', function (Blueprint $table) {
            $table->id();

            $table->string('nome');
            $table->string('description')->nullable();
            $table->integer('price');
            $table->integer('amount');

            $table->string('slug');

            $table->timestamps();
        });
    }
```

```
 sail php artisan migrate

   INFO  Running migrations.

  2023_10_16_211510_create_products_table ................ 34ms DONE


```



[Voltar ao Índice](#indice)

---


## <a name="parte6">6 - 05 - Compondo Listagem</a>



[Voltar ao Índice](#indice)

---


## <a name="parte7">7 - 06 - Compondo Nosso Primeiro Form</a>



[Voltar ao Índice](#indice)

---


## <a name="parte8">8 - 07 - Trabalhando com Mutate na Criação</a>

```php
class CreateProduct extends CreateRecord
{
    protected static string $resource = ProductResource::class;

    protected function mutateFormDataBeforeCreate(array $data): array
    {
        $data['price'] = ((float) str_replace(['.', ','], ['', '.'], $data['price'])) * 100;
        return $data;
    }
}
```

[Voltar ao Índice](#indice)

---


## <a name="parte9">9 - 08 - Mutate na Edição</a>

```php
class EditProduct extends EditRecord
{
    protected static string $resource = ProductResource::class;

    protected function mutateFormDataBeforeFill(array $data): array
    {
        $data['price'] = number_format(($data['price'] / 100), 2, ',', '.');
        return $data;
    }

    protected function mutateFormDataBeforeSave(array $data): array
    {
        $data['price'] = ((float) str_replace(['.', ','], ['', '.'], $data['price'])) * 100;
        return $data;
    }

    protected function getActions(): array
    {
        return [
            Actions\DeleteAction::make(),
        ];
    }
}

```

[Voltar ao Índice](#indice)

---


## <a name="parte10">10 - 09 - Manipulando Slug Dinâmico</a>

```php
class ProductResource extends Resource
{
    protected static ?string $model = Product::class;

    protected static ?string $navigationIcon = 'heroicon-o-collection';

    public static function form(Form $form): Form
    {
        return $form
            ->schema([
                TextInput::make('nome')
                    ->reactive()
                    ->afterStateUpdated(function ($state, $set) {
                        $state = Str::slug($state);
                        $set('slug', $state);
                    })
                    ->label('Nome Produto'),
                TextInput::make('description')->label('Descrição do  Produto'),
                TextInput::make('price')->label('Valor'),
                TextInput::make('amount')->label('Quantidade'),
                TextInput::make('slug')->disabled(),

            ]);
    }
```

[Voltar ao Índice](#indice)

---


## <a name="parte11">11 - 2. Buscas, Filtros, Ordenação e Relacionamento M:M</a>



[Voltar ao Índice](#indice)

---


## <a name="parte12">12 - 10 - Introdução</a>

```
 sail php artisan make:migration alter_table_add_colum_photo --table=products            

   INFO  Migration [database/migrations/2023_10_17_204644_alter_table_add_colum_photo.php] create
```

```
sail php artisan make:model Category -mfs

   INFO  Model [app/Models/Category.php] created successfully.

   INFO  Factory [database/factories/CategoryFactory.php] created successfully.

   INFO  Migration [database/migrations/2023_10_17_204807_create_categories_table.php] created successfully.

   INFO  Seeder [database/seeders/CategorySeeder.php] created successfully.

```

```
 sail php artisan make:migration create_category_product_table

   INFO  Migration [database/migrations/2023_10_17_205211_create_category_product_table.php] created successfully.

```




[Voltar ao Índice](#indice)

---


## <a name="parte13">13 - 11 - Ordenação na Tabela</a>



[Voltar ao Índice](#indice)

---


## <a name="parte14">14 - 12 - Busca na Tabela</a>



[Voltar ao Índice](#indice)

---


## <a name="parte15">15 - 13 - Filtros na Tabela</a>



[Voltar ao Índice](#indice)

---


## <a name="parte16">16 - 14 - Adicionando Upload Form e Imagem na Tabela</a>



[Voltar ao Índice](#indice)

---


## <a name="parte17">17 - 15 - Produtos e Categorias Relacionamento</a>



[Voltar ao Índice](#indice)

---


## <a name="parte18">18 - 16 - Relation Managers</a>



[Voltar ao Índice](#indice)

---


## <a name="parte19">19 - 17 - Conclusões</a>



[Voltar ao Índice](#indice)

---


## <a name="parte20">20 - 3. Recurso User, ACL e Ações</a>



[Voltar ao Índice](#indice)

---


## <a name="parte21">21 - 18 - Intro e Recurso User</a>



[Voltar ao Índice](#indice)

---


## <a name="parte22">22 - 19 - Habilitando e Desabilitando Áreas Recurso</a>



[Voltar ao Índice](#indice)

---


## <a name="parte23">23 - 20 - Acão Alterar Senha Edição</a>



[Voltar ao Índice](#indice)

---


## <a name="parte24">24 - 21 - Ação Alterar Senha na Listagem</a>



[Voltar ao Índice](#indice)

---


## <a name="parte25">25 - 22 - ACL: Policy e Filament</a>



[Voltar ao Índice](#indice)

---


## <a name="parte26">26 - 23 - Plugin Shield Filament ACL</a>



[Voltar ao Índice](#indice)

---


## <a name="parte27">27 - 24 - Add Papél Form Usuário e Conclusões</a>



[Voltar ao Índice](#indice)

---


## <a name="parte28">28 - 4. Custom Pages, Menus, Form Steps e Mais</a>



[Voltar ao Índice](#indice)

---


## <a name="parte29">29 - 25 - Intro e Custom Page</a>



[Voltar ao Índice](#indice)

---


## <a name="parte30">30 - 26 - Modificando Menu Navegação</a>



[Voltar ao Índice](#indice)

---


## <a name="parte31">31 - 27 - Falando Sobre Recurso de Pedidos</a>



[Voltar ao Índice](#indice)

---


## <a name="parte32">32 - 28 - Filtro com Datepicker</a>



[Voltar ao Índice](#indice)

---


## <a name="parte33">33 - 29 - Links para Outros Recursos</a>



[Voltar ao Índice](#indice)

---


## <a name="parte34">34 - 30 - Custom Forms: Column, Card e Fieldset</a>



[Voltar ao Índice](#indice)

---


## <a name="parte35">35 - 31 - Custom Forms: Tabs, Wizard e Section</a>



[Voltar ao Índice](#indice)

---


## <a name="parte36">36 - 32 - Busca Global R</a>



[Voltar ao Índice](#indice)

---


## <a name="parte37">37 - 33 - Conclusão</a>



[Voltar ao Índice](#indice)

---

