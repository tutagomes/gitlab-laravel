##### Correção de um Bug

Acabamos de perceber que houve um erro na hora de configurar o cliente e ele não processa bem o DateTime! Ele pediu o retorno de data no formato DD-MM-YYYY.

Faça as correções necessárias, lembre-se de criar uma issue, um branch, alterar os arquivos, um commit com mensagem significativa e finalmente um pull request.

Não se esqueça de abrir uma nova Issue, criar um novo branch para corrigir, implementar testes e realizar o Pull Request!


```php
<?php
   
namespace App\Http\Controllers\API;
   
use Illuminate\Http\Request;
use App\Http\Controllers\Controller as Controller;
use Carbon\Carbon;

class TimeController extends Controller
{
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        $response = [
            'datetime' => Carbon::now()->format('d-m-Y')
        ];

        return response()->json($response, 200);
    }
}
```

E não se esqueça também de alterar o código dos testes!

```php
<?php

namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithFaker;
use Tests\TestCase;

class ServerTimeAPITest extends TestCase
{
    /**
     * Testing return format
     *
     * @return void
     */
    public function testRetornoServerTime() {
        $response = $this->get('/api/server-time');
        $response->assertStatus(200);
        $response->assertJsonStructure([
            'datetime'
        ]);
        $regex = '/[0-9]{2}[-|\/]{1}[0-9]{2}[-|\/]{1}[0-9]{4}/m';
        $this->assertEquals(preg_match_all($regex, $response['datetime']), 1);
    }
}


```

