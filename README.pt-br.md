# Sinatra

*Atenção: Este documento é apenas uma tradução da versão em inglês e
pode estar desatualizado.*

Alguns dos trechos de código a seguir utilizam caracteres UTF-8. Então, caso esteja utilizando uma versão de ruby inferior à `2.0.0`, adicione o encoding no início de seus arquivos:

```ruby
# encoding: utf-8
```

Sinatra é uma [DSL](https://pt.wikipedia.org/wiki/Linguagem_de_domínio_específico) para
criar aplicações web em Ruby com o mínimo de esforço e rapidez:

```ruby
# minha_app.rb
require 'sinatra'

get '/' do
  'Olá Mundo!'
end
```

Instale a gem:

```shell
gem install sinatra
```

Em seguida execute:

```shell
ruby minha_app.rb
```

Acesse em: [http://localhost:4567](http://localhost:4567)

Códigos alterados só terão efeito após você reiniciar o servidor.  
Por favor, reinicie o servidor após qualquer mudança ou use 
[sinatra/reloader](http://www.sinatrarb.com/contrib/reloader).

É recomendado também executar `gem install thin`. Caso esta gem esteja 
disponível, o Sinatra irá utilizá-la.

## Conteúdo

* [Sinatra](#sinatra)
    * [Conteúdo](#conteúdo)
    * [Rotas](#rotas)
    * [Condições](#condições)
    * [Valores Retornados](#valores-retornados)
    * [Validadores de rota personalizados](#validadores-de-rota-personalizados)
    * [Arquivos estáticos](#arquivos-estáticos)
    * [Views / Templates](#views--templates)
        * [Literal Templates](#literal-templates)
        * [Linguagens de template disponíveis](#linguagens-de-template-disponíveis)
            * [Haml Templates](#haml-templates)
            * [Erb Templates](#erb-templates)
            * [Builder Templates](#builder-templates)
            * [Nokogiri Templates](#nokogiri-templates)
            * [Sass Templates](#sass-templates)
            * [SCSS Templates](#scss-templates)
            * [Less Templates](#less-templates)
            * [Liquid Templates](#liquid-templates)
            * [Markdown Templates](#markdown-templates)
            * [Textile Templates](#textile-templates)
            * [RDoc Templates](#rdoc-templates)
            * [AsciiDoc Templates](#asciidoc-templates)
            * [Radius Templates](#radius-templates)
            * [Markaby Templates](#markaby-templates)
            * [RABL Templates](#rabl-templates)
            * [Slim Templates](#slim-templates)
            * [Creole Templates](#creole-templates)
            * [MediaWiki Templates](#mediawiki-templates)
            * [CoffeeScript Templates](#coffeescript-templates)
            * [Stylus Templates](#stylus-templates)
            * [Yajl Templates](#yajl-templates)
            * [WLang Templates](#wlang-templates)
        * [Acessando Variáveis nos Templates](#acessando-variáveis-nos-templates)
        * [Templates com `yield` e layouts aninhados](#templates-com-yield-e-layouts-aninhados)
        * [Templates Inline](#templates-inline)
        * [Templates Nomeados](#templates-nomeados)
        * [Associando extensões de arquivos](#associando-extensões-de-arquivos)
        * [Adicionando seu Próprio Engine de Template](#adicionando-seu-próprio-engine-de-template)
        * [Customizando lógica para encontrar templates](#customizando-lógica-para-encontrar-templates)
    * [Filtros](#filtros)
    * [Helpers](#helpers)
        * [Utilizando Sessões](#utilizando-sessões)
          * [Segurança Secreta da Sessão](#segurança-secreta-da-sessão)  
          * [Configuração da Sessão](#configuração-da-sessão)  
          * [Escolhande Seu Próprio Middleware de Sessão](#escolhendo-middleware-de-sessão)
        * [Halting](#halting)
        * [Passing](#passing)
        * [Desencadeando Outra Rota](#desencadeando-outra-rota)
        * [Definindo Corpo, Código de Status e Cabeçalhos]
        (#definindo-corpo-codigo-de-status-cabeçalhos)
        * [Respostas de Streaming](#respostas-de-streaming)
        * [Logando](#logando)
        * [Tipos Mime](#tipos-mime)
        * [Gerando URLS](#gerando-urls)
        * [Redirecionamento do Navegador](#redirecionamento-do-navagador)
        * [Controle de Cache](#controle-de-cache)
        * [Enviando Arquivos](#enviando-arquivos)
        * [Acessando o Objeto de Requisição](#acessando-o-objeto-de-requisição)
        * [Anexos](#anexos)
        * [Trabalhando com Data e Hora](#trabalhando-com-data-e-hora)
        * [Procurando Arquivos de Modelo](#procurando-arquivos-de-modelo)
    * [Configuração](#configuração)
        * [Configurando proteção a ataques](#configurando-proteção-a-ataques)
        * [Configurações Disponíveis](#configurações-disponíveis)  
    * [Ambientes](#ambientes)
    * [Tratamento de Erros](#tratamento-de-erros)
        * [Não Encontrado](#não-encontrado)
        * [Erro](#erro)
    * [Rack Middleware](#rack-middleware)
    * [Testando](#testando)
    * [Sinatra::Base - Middleware, Bibliotecas e aplicativos modulares](#sinatrabase---middleware-bibliotecas-e-aplicativos-modulares)  
        * [Modular vs. Estilo Clássico](#modular-vs-estilo-clássico)
        * [Servindo uma Aplicação Modular](#servindo-uma-aplicação-modular)
        * [Usando uma Aplicação de Estilo Clássico com um config.ru](#usando-uma-aplicação-de-estilo-clássico-com-um-configru)
        * [Quando usar um config.ru?](#quando-usar-um-configru)
        * [Usando Sinatra como Middleware](#usando-sinatra-como-middleware)
        * [Criação de Aplicações Dinâmicas](#criação-de-aplicações-dinamicas)  
    * [Escopos e Ligação](#escopos-e-ligação)
        * [Escopo de Aplicação/Classe](#escopo-de-aplicação-classe)
        * [Escopo de Instância/Requisição](#escopo-de-instância-requisição)
        * [Escopo de Delegação](#escopo-de-delegação)
    * [Linha de comando](#linha-de-comando)
        * [Multi-threading](#multi-threading)  
    * [Requerimentos](#requerimentos)
    * [The Bleeding Edge](#the-bleeding-edge)
        * [Com Bundler](#com-bundler)
    * [A última versão](#a-última-versão)
    * [Mais](#mais)

## Rotas

No Sinatra, uma rota é um método HTTP emparelhado com um padrão de URL.
Cada rota possui um bloco de execução:

```ruby
get '/' do
  .. mostrando alguma coisa ..
end

post '/' do
  .. criando alguma coisa ..
end

put '/' do
  .. atualizando alguma coisa ..
end

patch '/' do
  .. modificando alguma coisa ..
end

delete '/' do
  .. removendo alguma coisa ..
end

options '/' do
  .. estabelecendo alguma coisa ..
end

link '/' do
  .. associando alguma coisa ..
end

unlink '/' do
  .. separando alguma coisa ..
end
```

As rotas são interpretadas na ordem em que são definidas. A primeira
rota encontrada responde a requisição.

Rotas com barras à direita são diferentes das que não contém as barras:

```ruby
get '/foo' do
  # Não é o mesmo que "GET /foo/"
end
```

Padrões de rota podem conter parâmetros nomeados, acessíveis por meio do
hash `params`:

```ruby
get '/ola/:nome' do
  # corresponde a "GET /ola/foo" e "GET /ola/bar"
  # params['nome'] é 'foo' ou 'bar'
  "Olá #{params['nome']}!"
end
```

Você também pode acessar parâmetros nomeados por meio dos parâmetros de
um bloco:

```ruby
get '/ola/:nome' do |n|
  # corresponde a "GET /ola/foo" e "GET /ola/bar"
  # params['nome'] é 'foo' ou 'bar'
  # n guarda o valor de params['nome']
  "Olá #{n}!"
end
```

Padrões de rota também podem conter parâmetros splat (curinga),
acessível por meio do array `params['splat']`:

```ruby
get '/diga/*/para/*' do
  # corresponde a /diga/ola/para/mundo
  params['splat'] # => ["ola", "mundo"]
end

get '/download/*.*' do
  # corresponde a /download/caminho/do/arquivo.xml
  params['splat'] # => ["caminho/do/arquivo", "xml"]
end
```

Ou com parâmetros de um bloco:

```ruby
get '/download/*.*' do |caminho, ext|
  [caminho, ext] # => ["caminho/do/arquivo", "xml"]
end
```

Rotas podem casar com expressões regulares:

```ruby
get /\/ola\/([\w]+)/ do
  "Olá, #{params['captures'].first}!"
end
```

Ou com parâmetros de um bloco:

```ruby
get %r{/ola/([\w]+)} do |c|
  # corresponde a "GET /meta/ola/mundo", "GET /ola/mundo/1234" etc.
  "Olá, #{c}!"
end
```

Padrões de rota podem contar com parâmetros opcionais:

```ruby
get '/posts/:formato?' do
  # corresponde a "GET /posts/" e qualquer extensão "GET /posts/json", "GET /posts/xml", etc.
end
```

Rotas também podem utilizar query strings:

```ruby
get '/posts' do
  # corresponde a "GET /posts?titulo=foo&autor=bar"
  titulo = params['titulo']
  autor = params['autor']
  # utiliza as variaveis titulo e autor; a query é opicional para a rota /posts
end
```

A propósito, a menos que você desative a proteção contra ataques (veja
[abaixo](#configurando-proteção-a-ataques)), o caminho solicitado pode ser 
alterado antes de concluir a comparação com as suas rotas.

Você pode customizar as opções usadas do [Mustermann](https://github.com/sinatra/mustermann#readme) para uma rota passando `:mustermann_opts` num hash:

```ruby
get '\A/posts\z', :musterman_opts => { :type => regexp, :check_anchors => false } do
  # corresponde a /posts exatamente, com ancoragem explícita
  "Se você combinar um padrão ancorado bata palmas!"
end
```

Parece com uma [condição](#condições) mas não é! Essas opções serão misturadas no hash global `:mustermann_opts` descrito [abaixo](#configurações-disponíveis)

## Condições

Rotas podem incluir uma variedade de condições, tal como o `user agent`:

```ruby
get '/foo', :agent => /Songbird (\d\.\d)[\d\/]*?/ do
  "Você está usando o Songbird versão #{params['agent'][0]}"
end

get '/foo' do
  # Correspondente a navegadores que não sejam Songbird
end
```

Outras condições disponíveis são `host_name` e `provides`:

```ruby
get '/', :host_name => /^admin\./ do
  "Área administrativa. Acesso negado!"
end

get '/', :provides => 'html' do
  haml :index
end

get '/', :provides => ['rss', 'atom', 'xml'] do
  builder :feed
end
```
`provides` procura pelos Accept header das requisições

Você pode facilmente definir suas próprias condições:

```ruby
set(:probabilidade) { |valor| condition { rand <= valor } }

get '/ganha_um_carro', :probabilidade => 0.1 do
  "Você ganhou!"
end

get '/ganha_um_carro' do
  "Sinto muito, você perdeu."
end
```

Use splat, para uma condição que leva vários valores:

```ruby
set(:auth) do |*roles|   # <- observe o splat aqui
  condition do
    unless logged_in? && roles.any? {|role| current_user.in_role? role }
      redirect "/login/", 303
    end
  end
end

get "/minha/conta/", :auth => [:usuario, :administrador] do
  "Detalhes da sua conta"
end

get "/apenas/administrador/", :auth => :administrador do
  "Apenas administradores são permitidos aqui!"
end
```

## Retorno de valores

O valor de retorno do bloco de uma rota determina pelo menos o corpo da
resposta passado para o cliente HTTP, ou pelo menos o próximo middleware
na pilha Rack. Frequentemente, isto é uma `string`, tal como nos
exemplos acima. Entretanto, outros valores também são aceitos.

Você pode retornar uma resposta válida ou um objeto para o Rack, sendo
eles de qualquer tipo de objeto que queira. Além disso, é possível
retornar um código de status HTTP.

* Um array com três elementros: `[status (Integer), cabecalho (Hash),
    corpo da resposta (responde à #each)]`

* Um array com dois elementros: `[status (Integer), corpo da resposta
    (responde à #each)]`

* Um objeto que responda à `#each` sem passar nada, mas, sim, `strings`
    para um dado bloco

* Um objeto `Integer` representando o código de status

Dessa forma, podemos implementar facilmente um exemplo de streaming:

```ruby
class Stream
  def each
    100.times { |i| yield "#{i}\n" }
  end
end

get('/') { Stream.new }
```

Você também pode usar o método auxiliar `stream` ([descrito abaixo](#respostas-de-streaming)) para reduzir códigos boilerplate e para incorporar a lógica de streaming na rota.

## Validadores de Rota Personalizados

Como apresentado acima, a estrutura do Sinatra conta com suporte
embutido para uso de padrões de String e expressões regulares como
validadores de rota. No entanto, ele não pára por aí. Você pode
facilmente definir os seus próprios validadores:

```ruby
class AllButPattern
  Match = Struct.new(:captures)

  def initialize(except)
    @except   = except
    @captures = Match.new([])
  end

  def match(str)
    @captures unless @except === str
  end
end

def all_but(pattern)
  AllButPattern.new(pattern)
end

get all_but("/index") do
  # ...
end
```

Note que o exemplo acima pode ser robusto e complicado em excesso. Pode
também ser implementado como:

```ruby
get // do
  pass if request.path_info == "/index"
  # ...
end
```

Ou, usando algo mais denso à frente:

```ruby
get %r{(?!/index)} do
  # ...
end
```

## Arquivos estáticos

Arquivos estáticos são disponibilizados a partir do diretório
`./public`. Você pode especificar um local diferente pela opção
`:public_folder`

```ruby
set :public_folder, File.dirname(__FILE__) + '/estatico'
```

Note que o nome do diretório público não é incluido na URL. Um arquivo
`./public/css/style.css` é disponibilizado como
`http://exemplo.com/css/style.css`.

Use a configuração `:static_cache_control` (veja [abaixo](#controle-de-cache)) para adicionar a informação `Cache-Control` no cabeçalho.

## Views / Templates

Cada linguagem de template é exposta através de seu próprio método de renderização. Estes metodos simplesmente retornam uma string:

```ruby
get '/' do
  erb :index
end
```

Isto renderiza `views/index.rb`

Ao invés do nome do template, você também pode passar direto o conteúdo do template:

```ruby
get '/' do
  code = "<%= Time.now %>"
  erb code
end
```

Templates também aceitam um segundo argumento, um hash de opções:

```ruby
get '/' do
  erb :index, :layout => :post
end
```

Isto irá renderizar a `views/index.erb` inclusa dentro da `views/post.erb` (o padrão é a `views/layout.erb`, se existir).

Qualquer opção não reconhecida pelo Sinatra será passada adiante para o engine de template:

```ruby
get '/' do
  haml :index, :format => :html5
end
```

Você também pode definir opções padrões para um tipo de template:

```ruby
set :haml, :format => :html5

get '/' do
  haml :index
end
```

Opções passadas para o método de renderização sobrescreve as opções definitas através do método `set`.

Opções disponíveis:

<dl>
  <dt>locals</dt>
  <dd>
    Lista de locais passado para o documento. Conveniente para *partials*
    Exemplo: <tt>erb "<%= foo %>", :locals => {:foo => "bar"}</tt>
  </dd>

  <dt>default_encoding</dt>
  <dd>
    String encoding para ser utilizada em caso de incerteza. o padrão é <tt>settings.default_encoding</tt>.
  </dd>

  <dt>views</dt>
  <dd>
    Diretório de onde os templates são carregados. O padrão é <tt>settings.views</tt>.
  </dd>

  <dt>layout</dt>
  <dd>
    Para definir quando utilizar ou não um
    layout (<tt>true</tt> ou <tt>false</tt>). E se for um
    Symbol, especifica qual template usar. Exemplo:
    <tt>erb :index, :layout => !request.xhr?</tt>
  </dd>

  <dt>content_type</dt>
  <dd>
    O *Content-Type* que o template produz. O padrão depente
    da linguagem de template utilizada.
  </dd>

  <dt>scope</dt>
  <dd>
    Escopo em que o template será renderizado. Padrão é a
    instancia da aplicação. Se você mudar isto as variáveis
    de instânciae metodos auxiliares não serão
    disponibilizados.
  </dd>

  <dt>layout_engine</dt>
  <dd>
    A engine de template utilizada para renderizar seu layout.
    Útil para linguagens que não suportam templates de outra
    forma. O padrão é a engine do template utilizado. Exemplo:
    <tt>set :rdoc, :layout_engine => :erb</tt>
  </dd>

  <dt>layout_options</dt>
  <dd>
    Opções especiais utilizadas apenas para renderizar o
    layout. Exemplo:
    <tt>set :rdoc, :layout_options => { :views => 'views/layouts' }</tt>
  </dd>
</dl>

É pressuposto que os templates estarão localizados direto sob o diretório `./views`. Para usar um diretório diferente:

```ruby
set :views, settings.root + '/templates'
```

Uma coisa importante para se lembrar é que você sempre deve
referenciar os templates utilizando *symbols*, mesmo que
eles estejam em um subdiretório (neste caso use:
`:'subdir/template'` or `'subdir/template'.to_sym`). Você deve
utilizar um *symbol* porque senão o método de renderização irá
renderizar qualquer outra string que você passe diretamente
para ele

### Literal Templates

```ruby
get '/' do
  haml '%div.title Olá Mundo'
end
```

Renderiza um template string. Você pode opcionalmente especificar `path` e `:line` para um backtrace mais claro se existir um caminho do sistema de arquivos ou linha associada com aquela string.

```ruby
get '/' do
  haml '%div.title Olá Mundo', :path => 'exemplos/arquivo.haml', :line => 3
end
```

### Linguagens de template disponíveis

Algumas linguagens possuem multiplas implementações. Para especificar qual implementação deverá ser utilizada (e para ser *thread-safe*), você deve simplesmente requere-la primeiro:

```ruby
require 'rdiscount' # ou require 'bluecloth'
get('/') { markdown :index }
```

#### Haml Templates

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="http://haml.info/" title="haml">haml</a></td>
  </tr>
  <tr>
    <td>Extencao do Arquivo</td>
    <td><tt>.haml</tt></td>
  </tr>
  <tr>
    <td>Exemplo</td>
    <td><tt>haml :index, :format => :html5</tt></td>
  </tr>
</table>

#### Erb Templates

<table>
  <tr>
    <td>Dependencia</td>
    <td>
      <a href="http://www.kuwata-lab.com/erubis/" title="erubis">erubis</a>
      or erb (included in Ruby)
    </td>
  </tr>
  <tr>
    <td>Extencao do Arquivos</td>
    <td><tt>.erb</tt>, <tt>.rhtml</tt> or <tt>.erubis</tt> (Erubis only)</td>
  </tr>
  <tr>
    <td>Exemplo</td>
    <td><tt>erb :index</tt></td>
  </tr>
</table>

#### Builder Templates

<table>
  <tr>
    <td>Dependencia</td>
    <td>
      <a href="https://github.com/jimweirich/builder" title="builder">builder</a>
    </td>
  </tr>
  <tr>
    <td>Extencao do Arquivo</td>
    <td><tt>.builder</tt></td>
  </tr>
  <tr>
    <td>Exemplo</td>
    <td><tt>builder { |xml| xml.em "hi" }</tt></td>
  </tr>
</table>

It also takes a block for inline templates (see exemplo).

#### Nokogiri Templates

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="http://www.nokogiri.org/" title="nokogiri">nokogiri</a></td>
  </tr>
  <tr>
    <td>Extencao do Arquivo</td>
    <td><tt>.nokogiri</tt></td>
  </tr>
  <tr>
    <td>Exemplo</td>
    <td><tt>nokogiri { |xml| xml.em "hi" }</tt></td>
  </tr>
</table>

It also takes a block for inline templates (see exemplo).

#### Sass Templates

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="http://sass-lang.com/" title="sass">sass</a></td>
  </tr>
  <tr>
    <td>Extencao do Arquivo</td>
    <td><tt>.sass</tt></td>
  </tr>
  <tr>
    <td>Exemplo</td>
    <td><tt>sass :stylesheet, :style => :expanded</tt></td>
  </tr>
</table>

#### SCSS Templates

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="http://sass-lang.com/" title="sass">sass</a></td>
  </tr>
  <tr>
    <td>Extencao do Arquivo</td>
    <td><tt>.scss</tt></td>
  </tr>
  <tr>
    <td>Exemplo</td>
    <td><tt>scss :stylesheet, :style => :expanded</tt></td>
  </tr>
</table>

#### Less Templates

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="http://lesscss.org/" title="less">less</a></td>
  </tr>
  <tr>
    <td>Extencao do Arquivo</td>
    <td><tt>.less</tt></td>
  </tr>
  <tr>
    <td>Exemplo</td>
    <td><tt>less :stylesheet</tt></td>
  </tr>
</table>

#### Liquid Templates

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="https://shopify.github.io/liquid/" title="liquid">liquid</a></td>
  </tr>
  <tr>
    <td>Extencao do Arquivo</td>
    <td><tt>.liquid</tt></td>
  </tr>
  <tr>
    <td>Exemplo</td>
    <td><tt>liquid :index, :locals => { :key => 'value' }</tt></td>
  </tr>
</table>

Já que você não pode chamar o Ruby (exceto pelo método `yield`) pelo template Liquid,
você quase sempre precisará passar o `locals` para ele.

#### Markdown Templates

<table>
  <tr>
    <td>Dependencia</td>
    <td>
      Anyone of:
        <a href="https://github.com/davidfstr/rdiscount" title="RDiscount">RDiscount</a>,
        <a href="https://github.com/vmg/redcarpet" title="RedCarpet">RedCarpet</a>,
        <a href="https://github.com/ged/bluecloth" title="bluecloth">BlueCloth</a>,
        <a href="http://kramdown.gettalong.org/" title="kramdown">kramdown</a>,
        <a href="https://github.com/bhollis/maruku" title="maruku">maruku</a>
    </td>
  </tr>
  <tr>
    <td>Extencao do Arquivos</td>
    <td><tt>.markdown</tt>, <tt>.mkd</tt> and <tt>.md</tt></td>
  </tr>
  <tr>
    <td>Exemplo</td>
    <td><tt>markdown :index, :layout_engine => :erb</tt></td>
  </tr>
</table>

Não é possível chamar métodos por este template, nem passar *locals* para o mesmo. Portanto normalmente é utilizado junto a outra engine de renderização:

```ruby
erb :overview, :locals => { :text => markdown(:introducao) }
```

Note que vcoê também pode chamar o método `markdown` dentro de outros templates:

```ruby
%h1 Olá do Haml!
%p= markdown(:saudacoes)
```

Já que você não pode chamar o Ruby pelo Markdown, você não
pode utilizar um layout escrito em Markdown. Contudo é
possível utilizar outra engine de renderização como template,
deve-se passar a `:layout_engine` como opção.

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="http://redcloth.org/" title="RedCloth">RedCloth</a></td>
  </tr>
  <tr>
    <td>Extencao do Arquivo</td>
    <td><tt>.textile</tt></td>
  </tr>
  <tr>
    <td>Exemplo</td>
    <td><tt>textile :index, :layout_engine => :erb</tt></td>
  </tr>
</table>

Não é possível chamar métodos por este template, nem passar *locals* para o mesmo. Portanto normalmente é utilizado junto a outra engine de renderização:

```ruby
erb :overview, :locals => { :text => textile(:introducao) }
```

Note que vcoê também pode chamar o método `textile` dentro de outros templates:

```ruby
%h1 Olá do Haml!
%p= textile(:saudacoes)
```

Já que você não pode chamar o Ruby pelo Textile, você não
pode utilizar um layout escrito em Textile. Contudo é
possível utilizar outra engine de renderização como template,
deve-se passar a `:layout_engine` como opção.

#### RDoc Templates

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="http://rdoc.sourceforge.net/" title="RDoc">RDoc</a></td>
  </tr>
  <tr>
    <td>Extencao do Arquivo</td>
    <td><tt>.rdoc</tt></td>
  </tr>
  <tr>
    <td>Exemplo</td>
    <td><tt>rdoc :README, :layout_engine => :erb</tt></td>
  </tr>
</table>

Não é possível chamar métodos por este template, nem passar *locals* para o mesmo. Portanto normalmente é utilizado junto a outra engine de renderização:

```ruby
erb :overview, :locals => { :text => rdoc(:introducao) }
```

Note que vcoê também pode chamar o método `rdoc` dentro de outros templates:

```ruby
%h1 Olá do Haml!
%p= rdoc(:saudacoes)
```

Já que você não pode chamar o Ruby pelo RDoc, você não
pode utilizar um layout escrito em RDoc. Contudo é
possível utilizar outra engine de renderização como template,
deve-se passar a `:layout_engine` como opção.

#### AsciiDoc Templates

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="http://asciidoctor.org/" title="Asciidoctor">Asciidoctor</a></td>
  </tr>
  <tr>
    <td>Extencao do Arquivo</td>
    <td><tt>.asciidoc</tt>, <tt>.adoc</tt> and <tt>.ad</tt></td>
  </tr>
  <tr>
    <td>Exemplo</td>
    <td><tt>asciidoc :README, :layout_engine => :erb</tt></td>
  </tr>
</table>

Já que você não pode chamar o Ruby pelo template AsciiDoc,
você quase sempre precisará passar o `locals` para ele.

#### Radius Templates

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="https://github.com/jlong/radius" title="Radius">Radius</a></td>
  </tr>
  <tr>
    <td>Extencao do Arquivo</td>
    <td><tt>.radius</tt></td>
  </tr>
  <tr>
    <td>Exemplo</td>
    <td><tt>radius :index, :locals => { :key => 'value' }</tt></td>
  </tr>
</table>

Já que você não pode chamar o Ruby pelo template Radius,
você quase sempre precisará passar o `locals` para ele.

#### Markaby Templates

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="http://markaby.github.io/" title="Markaby">Markaby</a></td>
  </tr>
  <tr>
    <td>Extencao do Arquivo</td>
    <td><tt>.mab</tt></td>
  </tr>
  <tr>
    <td>Exemplo</td>
    <td><tt>markaby { h1 "Welcome!" }</tt></td>
  </tr>
</table>

Este também recebe um bloco para templates (veja o exemplo).

#### RABL Templates

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="https://github.com/nesquena/rabl" title="Rabl">Rabl</a></td>
  </tr>
  <tr>
    <td>Extencao do Arquivo</td>
    <td><tt>.rabl</tt></td>
  </tr>
  <tr>
    <td>Exemplo</td>
    <td><tt>rabl :index</tt></td>
  </tr>
</table>

#### Slim Templates

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="http://slim-lang.com/" title="Slim Lang">Slim Lang</a></td>
  </tr>
  <tr>
    <td>Extencao do Arquivo</td>
    <td><tt>.slim</tt></td>
  </tr>
  <tr>
    <td>Exemplo</td>
    <td><tt>slim :index</tt></td>
  </tr>
</table>

#### Creole Templates

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="https://github.com/minad/creole" title="Creole">Creole</a></td>
  </tr>
  <tr>
    <td>Extencao do Arquivo</td>
    <td><tt>.creole</tt></td>
  </tr>
  <tr>
    <td>Exemplo</td>
    <td><tt>creole :wiki, :layout_engine => :erb</tt></td>
  </tr>
</table>

Não é possível chamar métodos por este template, nem passar *locals* para o mesmo. Portanto normalmente é utilizado junto a outra engine de renderização:

```ruby
erb :overview, :locals => { :text => creole(:introduction) }
```

Note que você também pode chamar o método `creole` dentro de outros templates:

```ruby
%h1 Olá do Haml!
%p= creole(:saudacoes)
```

Já que você não pode chamar o Ruby pelo Creole, você não
pode utilizar um layout escrito em Creole. Contudo é
possível utilizar outra engine de renderização como template,
deve-se passar a `:layout_engine` como opção.

#### MediaWiki Templates

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="https://github.com/nricciar/wikicloth" title="WikiCloth">WikiCloth</a></td>
  </tr>
  <tr>
    <td>Extencao do Arquivo</td>
    <td><tt>.mediawiki</tt> and <tt>.mw</tt></td>
  </tr>
  <tr>
    <td>Exemplo</td>
    <td><tt>mediawiki :wiki, :layout_engine => :erb</tt></td>
  </tr>
</table>

It is not possible to call methods from MediaWiki markup, nor to pass locals to
it. You therefore will usually use it in combination with another rendering
engine:

```ruby
erb :overview, :locals => { :text => mediawiki(:introduction) }
```

Note that you may also call the `mediawiki` method from within other templates:

```ruby
%h1 Hello From Haml!
%p= mediawiki(:greetings)
```

Já que você não pode chamar o Ruby pelo MediaWiki, você não
pode utilizar um layout escrito em MediaWiki. Contudo é
possível utilizar outra engine de renderização como template,
deve-se passar a `:layout_engine` como opção.

#### CoffeeScript Templates

<table>
  <tr>
    <td>Dependencia</td>
    <td>
      <a href="https://github.com/josh/ruby-coffee-script" title="Ruby CoffeeScript">
        CoffeeScript
      </a> and a
      <a href="https://github.com/sstephenson/execjs/blob/master/README.md#readme" title="ExecJS">
        way to execute javascript
      </a>
    </td>
  </tr>
  <tr>
    <td>Extencao do Arquivo</td>
    <td><tt>.coffee</tt></td>
  </tr>
  <tr>
    <td>Exemplo</td>
    <td><tt>coffee :index</tt></td>
  </tr>
</table>

#### Stylus Templates

<table>
  <tr>
    <td>Dependencia</td>
    <td>
      <a href="https://github.com/forgecrafted/ruby-stylus" title="Ruby Stylus">
        Stylus
      </a> and a
      <a href="https://github.com/sstephenson/execjs/blob/master/README.md#readme" title="ExecJS">
        way to execute javascript
      </a>
    </td>
  </tr>
  <tr>
    <td>Extencao do Arquivo</td>
    <td><tt>.styl</tt></td>
  </tr>
  <tr>
    <td>Exemplo</td>
    <td><tt>stylus :index</tt></td>
  </tr>
</table>

Antes que vcoê possa utilizar o template Stylus primeiro você deve carregar `stylus` e `stylus/tilt`:

```ruby
require 'sinatra'
require 'stylus'
require 'stylus/tilt'

get '/' do
  stylus :exemplo
end
```

#### Yajl Templates

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="https://github.com/brianmario/yajl-ruby" title="yajl-ruby">yajl-ruby</a></td>
  </tr>
  <tr>
    <td>Extencao do Arquivo</td>
    <td><tt>.yajl</tt></td>
  </tr>
  <tr>
    <td>Exemplo</td>
    <td>
      <tt>
        yajl :index,
             :locals => { :key => 'qux' },
             :callback => 'present',
             :variable => 'resource'
      </tt>
    </td>
  </tr>
</table>

O código-fonte do template é executado como uma string Ruby e a variável resultante em json é convertida utilizando `#to_json`:

```ruby
json = { :foo => 'bar' }
json[:baz] = key
```

O `:callback` e `:variable` são opções que podem ser utilizadas para o objeto de renderização:

```javascript
var resource = {"foo":"bar","baz":"qux"};
present(resource);
```

#### WLang Templates

<table>
  <tr>
    <td>Dependencia</td>
    <td><a href="https://github.com/blambeau/wlang/" title="WLang">WLang</a></td>
  </tr>
  <tr>
    <td>Extencao do Arquivo</td>
    <td><tt>.wlang</tt></td>
  </tr>
  <tr>
    <td>Exemplo</td>
    <td><tt>wlang :index, :locals => { :key => 'value' }</tt></td>
  </tr>
</table>

Já que você não pode chamar o Ruby (exceto pelo método
`yield`) pelo template WLang,
você quase sempre precisará passar o `locals` para ele.

## Acessando Variáveis nos Templates

Templates são avaliados dentro do mesmo contexto como manipuladores de
rota. Variáveis de instância setadas em rotas manipuladas são
diretamente acessadas por templates:

```ruby
get '/:id' do
  @foo = Foo.find(params['id'])
  haml '%h1= @foo.nome'
end
```

Ou, especifique um hash explícito para variáveis locais:

```ruby
get '/:id' do
  foo = Foo.find(params['id'])
  haml '%h1= foo.nome', :locals => { :foo => foo }
end
```

Isso é tipicamente utilizando quando renderizamos templates como
partials dentro de outros templates.

### Templates com `yield` e layouts aninhados

O layout geralmente é apenas um template que executa `yield`.
Tal template pode ser utilizado pela opção `:template` descrita acima ou pode ser renderizado através de um bloco, como a seguir:

```ruby
erb :post, :layout => false do
  erb :index
end
```

Este código é quase equivalente a `erb :index, :layout => :post`

Passando blocos para os métodos de renderização é útil para criar layouts aninhados:

```ruby
erb :main_layout, :layout => false do
  erb :admin_layout do
    erb :user
  end
end
```

Também pode ser feito com menos linhas de código:

```ruby
erb :admin_layout, :layout => :main_layout do
  erb :user
end
```

Atualmente os métodos listados aceitam blocos: `erb`, `haml`,
`liquid`, `slim `, `wlang`. E também o método `render`.

### Templates Inline

Templates podem ser definidos no final do arquivo fonte(.rb):

```ruby
require 'sinatra'

get '/' do
  haml :index
end

__END__

@@ layout
%html
  = yield

@@ index
%div.title Olá Mundo.
```

NOTA: Templates inline definidos no arquivo fonte são automaticamente
carregados pelo sinatra. Digite \`enable :inline\_templates\` se você
tem templates inline no outro arquivo fonte.

### Templates nomeados

Templates também podem ser definidos utilizando o método top-level
`template`:

```ruby
template :layout do
  "%html\n  =yield\n"
end

template :index do
  '%div.title Olá Mundo!'
end

get '/' do
  haml :index
end
```

Se existir um template com nome “layout”, ele será utilizado toda vez
que um template for renderizado. Você pode desabilitar layouts passando
`:layout => false` ou desabilita-los por padrão via `set :haml, :layout => false`

```ruby
get '/' do
  haml :index, :layout => !request.xhr?
end
```

### Associando extensões de arquivos

Para associar uma extensão de arquivo com um engine de template use o método `Tilt.register`. Por exemplo, se você quiser usar a extensão `tt` para os templates Textile você pode fazer o seguinte:

```ruby
Tilt.register :tt, Tilt[:textile]
```

### Adicionando seu Próprio Engine de Template

Primeiro registre seu engine utilizando o Tilt, e então crie um método de renderização:

```ruby
Tilt.register :myat, MyAwesomeTemplateEngine

helpers do
  def myat(*args) render(:myat, *args) end
end

get '/' do
  myat :index
end
```

Renderize `./views/index.myat`. Veja
https://github.com/rtomayko/tilt para saber mais sobre Tilt.

### Customizando lógica para encontrar templates

Para implementar sua própria lógica para busca de templates você pode escrever seu próprio método `#find_template`

```ruby
configure do
  set :views [ './views/a', './views/b' ]
end

def find_template(views, name, engine, &block)
  Array(views).each do |v|
    super(v, name, engine, &block)
  end
end
```

## Filtros

Filtros Before são avaliados antes de cada requisição dentro do contexto
da requisição e podem modificar a requisição e a reposta. Variáveis de
instância setadas nos filtros são acessadas através de rotas e
templates:

```ruby
before do
  @nota = 'Oi!'
  request.path_info = '/foo/bar/baz'
end

get '/foo/*' do
  @nota #=> 'Oi!'
  params['splat'] #=> 'bar/baz'
end
```

Filtros After são avaliados após cada requisição dentro do contexto da
requisição e também podem modificar a requisição e a resposta. Variáveis de
instância e rotas definidas nos filtros before são acessadas através dos
filtros after:

```ruby
after do
  puts response.status
end
```

Nota: A não ser que você use o metódo `body` ao invés de apenas retornar uma String das rotas, o corpo ainda não estará disponível no filtro after, uma vez que é gerado tardiamente.

Filtros opcionalmente têm um padrão, fazendo com que sejam avaliados
somente se o caminho do pedido coincidir com esse padrão:

```ruby
before '/protected/*' do
  authenticate!
end

after '/create/:slug' do |slug|
  session[:last_slug] = slug
end
```

Como rotas, filtros também aceitam condições:

```ruby
before :agent => /Songbird/ do
  #...
end

after '/blog/*', :host_name => 'exemplo.com' do
  #...
end
```

## Helpers

Use o método de alto nível `helpers` para definir métodos auxiliares
para utilizar em manipuladores de rotas e modelos:

```ruby
helpers do
  def bar(nome)
    "#{nome}bar"
  end
end

get '/:nome' do
  bar(params['nome'])
end
```

Alternativamente, métodos Helper podem ser definidos separadamente em módulos:

```ruby
module FooUtils
  def foo(nome) "#{nome}foo" end
end

module BarUtils
  def bar(nome) "#{nome}bar" end
end

helpers FooUtils, BarUtils
```

O efeito é o mesmo que incluir os módulos na classe da aplicação.

### Utilizando Sessões

Sessões são usadas para manter um estado durante uma requisição. Se ativa, você terá disponível um hash de sessão para cada sessão de usuário:

```ruby
enable :sessions

get '/' do
  "value = " << session[:value].inspect
end

get '/:value' do
  session['value'] = params['value']
end
```
#### Segredo Seguro da Sessão

Para melhorar a segurança, os dados da sessão no cookie são assinado com uma segredo de sessão usando `HMAC-SHA1`. Esse segredo de sessão deve ser, de preferência, um valor criptograficamente randômico, seguro, de um comprimento apropriado no qual `HMAC-SHA1` é maior ou igual a 64 bytes (512 bits, 128 carecteres hexadecimais). Você será avisado para não usar uma chave secreta menor que 32 bytes de randomicidade (256 bits, 64 caracteres hexadecimais). Portanto, é **muito importante** que você não invente o segredo, mas use um gerador de números aleatórios seguro para cria-lo. Humanos são extremamente ruins em gerar números aleatórios.

Por padrão, um segredo de sessão randômico seguro de 32 bytes é gerada para você pelo Sinatra, mas ele mudará toda vez que você reiniciar sua aplicação. Se você tiver múltiplas instâncias da sua aplicação e você deixar que o Sinatra gere a chave, cada instância teria uma chave de sessão diferente, o que certamente não é o que você quer.

Para melhor segurança e usabilidade é [recomendado](https://12factor.net/config) que você gere um segredo randômico secreto e salve-o em uma variável de ambiente em cada host rodando sua aplicação, assim todas as instâncias da sua aplicação irão compartilhar o mesmo segredo. Você deve, periodicamente, mudar esse segredo de sessão para um novo valor. Abaixo, são mostrados alguns exemplos de como você pode criar um segredo de 64 bytes e usa-lo:

**Gerando Segredo de Sessão**

```text
$ ruby -e "require 'securerandom'; puts SecureRandom.hex(64)"
99ae8af...snip...ec0f262ac
```

**Gerando Segredo de Sessão (Pontos adicionais)**

Use preferencialmente a [gem sysrandom](https://github.com/cryptosphere/sysrandom#readme) para utilizar as facilidades do sistema RNG para gerar valores aleatórios ao invés do `OpenSSL` no qual o MRI Ruby padroniza para:

```text
$ gem install sysrandom
Building native extensions. This could take a while...
Sucessfully installed sysrandom-1.x
1 gem installed

$ ruby -r "require 'sysrandom/securerandom'; puts SecureRandom.hex(64)"
99ae8af...snip...ec0f262ac
```

**Segredo de Sessão numa Variável de Ambiente**

Defina uma variável de ambiente `SESSION_SECRET` para o Sinatra com o valor que você gerou. Salve esse valor entre as reinicializações do seu host. Já que a forma de fazer isso irá variar entre os sistemas operacionais, o exemplo abaixo serve apenas para fins ilustrativos:

```bash
# echo "export SESSION_SECRET = 99ae8af...snip...ec0f262ac" >> ~/.bashrc
```

**Configurando o Segredo de Sessão na Aplicação**

Configure sua aplicação para uma falha de segredo seguro aleatório se a variável de ambiente `SESSION_SECRET` não estiver disponível.

Como ponto adicional use a [gem sysrandom](https://github.com/cryptosphere/sysrandom#readme) da seguinte forma:

```ruby
require 'securerandom'
# -or- require 'sysrandom/securearandom'
set :session_secret, ENV.fecth(`SESSION_SECRET`) { SecureRandom.hex(64) }
```

#### Configuração de Sessão

Se você deseja configurar isso adicionalmente, você pode salvar um hash com opções na definição de `sessions`:

```ruby
set :sessions, :domain => 'foo.com'
```

Para compartilhar sua sessão com outras aplicações no subdomínio de foo.com, adicione um *.* antes do domínio como no exemplo abaixo:

```ruby
set :sessions, :domain => '.foo.com'
```

#### Escolhendo Seu Próprio Middleware de Sessão

Perceba que `enable :sessions` na verdade guarda todos seus dados num cookie. Isto pode não ser o que você deseja sempre (armazenar muitos dados irá aumentar seu tráfego, por exemplo). Você pode usar qualquer middleware de sessão Rack para fazer isso, um dos seguintes métodos pode ser usado:

```ruby
enable :sessions
set :session_store, Rack::Session::Pool
```

Ou definir as sessões com um hash de opções:

```ruby
set :sessions, :expire_after => 2592000
set :session_store, Rack::Session::Pool
```

Outra opção é **não** usar `enable :sessions`, mas ao invés disso trazer seu middleware escolhido como você faria com qualquer outro middleware.

É importante lembrar que usando esse método, a proteção baseada na sessão **não estará habilitada por padrão**.

O middleware Rack, para fazer isso, precisará que isso também seja adicionado:

```ruby
use Rack::Session::Pool, :expire_after => 2592000
use Rack::Protection::RemoteToken
use Rack::Protection::SessionHijacking
```
Veja '[Configurando proteção a ataques](#configurando-proteção-a-ataques)' para mais informações.

### Halting

Para parar imediatamente uma requisição com um filtro ou rota utilize:

```ruby
halt
```

Você também pode especificar o status quando parar:

```ruby
halt 410
```

Ou com corpo de texto:

```ruby
halt 'isso será o corpo do texto'
```

Ou com ambos:

```ruby
halt 401, 'vá embora!'
```

Com cabeçalhos:

```ruby
halt 402, {'Content-Type' => 'text/plain'}, 'revanche'
```

Também é obviamente possível combinar um template com o `halt`:

```ruby
halt erb(:error)
```

### Passing

Uma rota pode processar aposta para a próxima rota correspondente usando
`pass`:

```ruby
get '/adivinhar/:quem' do
  pass unless params['quem'] == 'Frank'
  'Você me pegou!'
end

get '/adivinhar/*' do
  'Você falhou!'
end
```

O bloqueio da rota é imediatamente encerrado e o controle continua com a
próxima rota de parâmetro. Se o parâmetro da rota não for encontrado, um
404 é retornado.

### Desencadeando Outra Rota

As vezes o `pass` não é o que você quer, ao invés dele talvez você queira obter o resultado chamando outra rota. Utilize o método `call` neste caso:

```ruby
get '/foo' do
  status, headers, body = call env.merge("PATH_INFO" => '/bar')
  [status, headers, body.map(&:upcase)]
end

get '/bar' do
  "bar"
end
```

Note que no exemplo acima você ganharia performance ao simplemente mover o `"bar"` em um helper usado por ambos `/foo` e `/bar`.

Se você quer que a requisição seja enviada para a mesma instancia da aplicação ao invês de uma duplicada, use `call!` no lugar de `call`.

Veja a especificação do Rack se você quer aprender mais sobre o `call`.

### Definindo Corpo, Código de Status e Cabeçalhos

É possível e recomendado definir o código de status e o corpo da resposta com o valor retornado do bloco da rota. Entretanto, em alguns cenário você pode querer definir o corpo em um ponto arbitrário do fluxo de execução. Você pode fazer isso com o metódo helper `body`. Se você fizer isso, poderá usar esse metódo de agora em diante para acessar o body:

```ruby
get '/foo' do
  body "bar"
end

after do
  puts body
end
```

Também é possivel passar um bloco para `body`, que será executado pelo manipulador Rack (isso pode ser usado para implementar streaming, [veja "Retorno de Valores"](#retorno-de-valores)).

Similar ao corpo, você pode também definir o código de status e cabeçalhos:

```ruby
get '/foo' do
  status 418
  headers \
    "Permite" => "BREW, POST, GET, PROPFIND, WHEN"
    "Atualiza" => "Refresh: 20; https://ietf.org/rfc/rfc2324.txt"
  body "Eu sou um bule de chá!"
end
```

Assim como `body`, `headers` e `status` sem argumentos podem ser usados para acessar seus valores atuais.

### Transmitindo Respostas

As vezes você quer começar a mandar dados enquanto está gerando partes do corpo da resposta. Em exemplos extremos, você quer continuar enviando dados até o cliente encerrar a conexão. Você pode usar o helper `stream` para evitar criar seu próprio empacotador:

```ruby
get '/' do
  stream do |out|
    out << "Isso será len -\n"
    sleep 0.5
    out << " Aguarde \n"
    sleep 1
    out << " dário!\n"
  end
end
```

Isso permite você implementar APIs de Transmissão, [Eventos Enviados pelo Servidor](https://w3c.github.io/eventsource/), e pode ser usado como a base para [WebSockets](https://en.wikipedia.org/wiki/WebSocket). Pode ser usado também para aumentar a taxa de transferência se algum, mas não todo, conteúdo depender de um recurso lento.

Perceba que o comportamento da transmissão, especialmente o número de requisições concorrentes, depende altamente do servidor web usado para servir a aplicação. Alguns servidores podem até mesmo não suportar transmissão de maneira alguma. Se o servidor não suportar transmissão, o corpo será enviado completamente após que o bloco passado para `stream` terminar de executar. Transmissão não funciona de nenhuma maneira com Shotun.

Se o parâmetro opcional é definido como `keep_open`, ele não chamará `close` no objeto transmitido, permitindo você a fecha-lo em algum outro ponto futuro no fluxo de execução. Isso funciona apenas em servidores orientados a eventos, como Thin e Rainbows. Outros servidores irão continuar fechando a transmissão:

```ruby
# long polling

set :server, :thin
conexoes = []

get '/assinar' do
  # registra o interesse de um cliente em servidores de eventos
  stream(:keep_open) do |saida|
    conexoes << saida
    # retire conexões mortas
    conexoes.reject!(&:closed?)
  end
end

post '/:messagem' do
  conexoes.each do |saida|
    # notifica o cliente que uma nova mensagem chegou
    saida << params['messagem'] << "\n"

    # indica ao cliente para se conectar novamente
    saida.close
  end

  # confirma
  "messagem recebida"
end
```

Também é possivel para o cliente fechar a conexão quando está tentando escrever para o socket. Devido a isso, é recomendado checar `out.closed?` antes de tentar escrever.

### Logando

No escopo da requisição, o helper `logger` expõe uma instância `Logger`:

```ruby
get '/' do
  logger.info "loading data"
  # ...
end
```

Esse logger irá por automaticamente as configurações de logging do handler do Rack na conta. Se o logging estiver desabilitado, esse método recuperará um objeto dummy, então você não terá que se preocupar com suas rotas e filtros.

Perceba que logging está habilitao apenas para `Sinatra::Application` por padrão, então se você herdar de `Sinatra::Base`, você provavelmente irá querer habilitar:

```ruby
  class MyApp < Sinatra::Base
    configure :production, :development do
      enable :logging
    end
  end
```

Para eviar que qualquer middleware de logging seja configurado, defina a configuração `logging` como `nil`. Entretanto, tenha em mente que `logger` retornará, nesse caso, `nil`. Um caso de uso comum é quanto você quer definir seu próprio logger. Sinatra irá usar qualquer um que ele achar em `env['rack.logger']`

### Tipos Mime

Quando se está usando `send_file` ou arquivos estáticos, você pode ter tipos mime que o Sinatra não entende. Use `mime_type` para registrá-los pela extensão do arquivo:

```ruby
configure do
  mime_type :foo, 'text/foo'
end
```

Você pode utilizar também com o helper `content_type`:

```ruby
get '/' do
  content-type :foo
  "foo foo foo"
end
```

### Gerando URLs

Para gerar URLs você deve usar o metódo helper `url` no Haml:

```ruby
%a{:href => url('/foo')} foo
```

Isso inclui proxies reversos e rotas Rack, se presentes.

Esse método é também apelidado para `to` (veja [abaixo](#redirecionamento-do-browser) para um exemplo).

### Redirecionamento do Browser

Você pode lançar um redirecionamento no browser com o metódo helper `redirect`:

```ruby
get '/foo' do
  redirect to('/bar')
end
```

Quaisquer paramêtros adicional são interpretados como argumentos passados ao `halt`:

```ruby
redirect to('/bar'), 303
redirect 'http://www.google.com/', 'lugar errado, amigo'
```

Você pode também facilmente redirecionar para a página da qual o usuário veio com `redirect back`:

```ruby
get '/foo' do
  "<a href='/bar'>do something</a>"
end

get '/bar' do
  do_something
  redirect back
end
```

Para passar argumentos com um redirecionamento, adicione-os a query:

```ruby
redirect to('/bar?sum=42')
```

Ou use uma sessão:

```ruby
enable :sessions

get '/foo' do
  session[:secret] = 'foo'
  redirect to('/bar')
end

get '/bar' do
  session[:secret]
end
```

### Controle de Cache

Definir sues cabeçalhos corretamente é o principal passo para uma correta cache HTTP.

Você pode facilmente definir o cabeçalho de Cache-Control como:

```ruby
get '/' do
  cache_control :public
  "guarde isso!"
end
```

Dica profissional: Configure a cache em um filtro anterior:

```ruby
before do
  cache_control :public, :must_revalidate, :max_age => 60
end
```

Se você está usando o helper `expires` para definir seu cabeçalho correspondente, `Cache-Control` irá ser definida automaticamente para você:

```ruby
before do
  expires 500, :public, :must_revalidate
end
```

Para usar propriciamente caches, você deve considerar usar `etag` ou `last_modified`. É recomendado chamar esses helpers *antes* de fazer qualquer tipo de processamento pesado, já que eles irão imediatamente retorna uma resposta se o cliente já possui a versão atual na sua cache:

```ruby
get "/artigo/:id" do
  @artigo = Artigo.find params['id']
  last_modified @artigo.updated_at
  etag @artigo.sha1
  erb :artigo
end
```

Também é possível usar uma [ETag fraca](https://en.wikipedia.org/wiki/HTTP_ETag#Strong_and_weak_validation):

```ruby
etag @article.sha1, :weak
```
Esses helpers não irão fazer nenhum processo de cache para você, mas irão alimentar as informações necessárias para sua cache. Se você está pesquisando por uma solução rápida de proxy-reverso, tente [rack-cache](https://github.com/rtomayko/rack-cache#readme):

```ruby
require "rack/cache"
require "sinatra"

use Rack::Cache

get '/' do
  cache_control :public, :max_age => 36000
  sleep 5
  "olá"
end
```

Use a configuração `:static_cache_control` (veja [acima]#(controle-de-cache)) para adicionar o cabeçalho de informação `Cache-Control` para arquivos estáticos.

De acordo com a RFC 2616, sua aplicação deve se comportar diferentemente se o cabeçalho If-Match ou If-None-Match é definido para `*`, dependendo se o recurso requisitado já existe. Sinatra assume que recursos para requisições seguras (como get) e idempotentes (como put) já existem, enquanto que para outros recursos (por exemplo requisições post) são tratados como novos recursos. Você pode mudar esse comportamento passando em uma opção `:new_resource`:

```ruby
get '/create' do
  etag '', :new_resource => true
  Artigo.create
  erb :novo_artigo
end
```

Se você quer continuar usando um ETag fraco, passe em uma opção `:kind`:

```ruby
etag '', :new_resource => true, :kind => :weak
```

### Enviando Arquivos

Para retornar os conteúdos de um arquivo como as resposta, você pode usar o metódo helper `send_file`:

```ruby
get '/' do
  send_file 'foo.png'
end
```

Também aceita opções:

```ruby
send_file 'foo.png', :type => :jpg
```

As opções são:

<dl>
  <dt>filename</dt>
    <dd>Nome do arquivo a ser usado na respota,
    o padrão é o nome do arquivo reak</dd>
  <dt>last_modified</dt>
    <dd>Valor do cabeçalho Last-Modified, o padrão corresponde ao mtime do arquivo.</dd>

  <dt>type</dt>
    <dd>Valor do cabeçalho Content-Type, extraído da extensão do arquivo se
    inexistente.</dd>

  <dt>disposition</dt>
    <dd>
      Valor do cabeçalho Content-Disposition, valores possíveis: <tt>nil</tt>
      (default), <tt>:attachment</tt> and <tt>:inline</tt>
    </dd>

  <dt>length</dt>
    <dd>Valor do cabeçalho Content-Length, o padrão corresponde ao tamanho do arquivo.</dd>

  <dt>status</dt>
    <dd>
      Status code to be sent. Useful when sending a static file as an error
      page. If supported by the Rack handler, other means than streaming
      from the Ruby process will be used. If you use this helper method,
      Sinatra will automatically handle range requests.
      Código de status a ser enviado. Útil quando está se enviando um arquivo estático
      como uma página de erro. Se suportado pelo handler do Rack, outros meios além de
      transmissão do processo do Ruby serão usados. So você usar esse metódo helper,
      o Sinatra irá automaticamente lidar com requisições de alcance.
    </dd>
</dl>

## Configuração

Rodando uma vez, na inicialização, em qualquer ambiente:

```ruby
configure do
  ...
end
```

Rodando somente quando o ambiente (`APP_ENV` environment variável) é
setado para `:production`:

```ruby
configure :production do
  ...
end
```

Rodando quando o ambiente é setado para `:production` ou `:test`:

```ruby
configure :production, :test do
  ...
end
```

## Tratamento de Erros

Tratamento de erros rodam dentro do mesmo contexto como rotas e filtros
before, o que significa que você pega todos os presentes que tem para
oferecer, como `haml`, `erb`, `halt`, etc.

### Não Encontrado

Quando um `Sinatra::NotFound` exception é levantado, ou o código de
status da reposta é 404, o manipulador `not_found` é invocado:

```ruby
not_found do
  'Isto está longe de ser encontrado'
end
```

### Erro

O manipulador `error` é invocado toda a vez que uma exceção é lançada a
partir de um bloco de rota ou um filtro. O objeto da exceção pode ser
obtido a partir da variável Rack `sinatra.error`:

```ruby
error do
  'Desculpe, houve um erro desagradável - ' + env['sinatra.error'].message
end
```

Erros customizados:

```ruby
error MeuErroCustomizado do
  'Então que aconteceu foi...' + env['sinatra.error'].message
end
```

Então, se isso acontecer:

```ruby
get '/' do
  raise MeuErroCustomizado, 'alguma coisa ruim'
end
```

Você receberá isso:

    Então que aconteceu foi... alguma coisa ruim

Alternativamente, você pode instalar manipulador de erro para um código
de status:

```ruby
error 403 do
  'Accesso negado'
end

get '/secreto' do
  403
end
```

Ou um range:

```ruby
error 400..510 do
  'Boom'
end
```

O Sinatra instala os manipuladores especiais `not_found` e `error`
quando roda sobre o ambiente de desenvolvimento.

## Rack Middleware

O Sinatra roda no [Rack](http://rack.github.io/), uma interface
padrão mínima para frameworks web em Ruby. Um das capacidades mais
interessantes do Rack para desenvolver aplicativos é suporte a
“middleware” – componentes que ficam entre o servidor e sua aplicação
monitorando e/ou manipulando o request/response do HTTP para prover
vários tipos de funcionalidades comuns.

O Sinatra faz construtores pipelines do middleware Rack facilmente em um
nível superior utilizando o método `use`:

```ruby
require 'sinatra'
require 'meu_middleware_customizado'

use Rack::Lint
use MeuMiddlewareCustomizado

get '/ola' do
  'Olá mundo'
end
```

A semântica de `use` é idêntica aquela definida para a DSL
[Rack::Builder](http://www.rubydoc.info/github/rack/rack/master/Rack/Builder)
(mais frequentemente utilizada para arquivos rackup). Por exemplo, o
método `use` aceita múltiplos argumentos/variáveis bem como blocos:

```ruby
use Rack::Auth::Basic do |usuario, senha|
  usuario == 'admin' && senha == 'secreto'
end
```

O Rack é distribuido com uma variedade de middleware padrões para logs,
debugs, rotas de URL, autenticação, e manipuladores de sessão. Sinatra
utilizada muitos desses componentes automaticamente baseando sobre
configuração, então, tipicamente você não tem `use` explicitamente.

## Testando

Testes no Sinatra podem ser escritos utilizando qualquer biblioteca ou
framework de teste baseados no Rack.
[Rack::Test](http://gitrdoc.com/brynary/rack-test) é recomendado:

```ruby
require 'minha_aplicacao_sinatra'
require 'rack/test'

class MinhaAplicacaoTeste < Minitest::Test
  include Rack::Test::Methods

  def app
    Sinatra::Application
  end

  def meu_test_default
    get '/'
    assert_equal 'Ola Mundo!', last_response.body
  end

  def teste_com_parametros
    get '/atender', :name => 'Frank'
    assert_equal 'Olá Frank!', last_response.bodymeet
  end

  def test_com_ambiente_rack
    get '/', {}, 'HTTP_USER_AGENT' => 'Songbird'
    assert_equal "Você está utilizando o Songbird!", last_response.body
  end
end
```

NOTA: Os módulos de classe embutidos `Sinatra::Test` e
`Sinatra::TestHarness` são depreciados na versão 0.9.2.

## Sinatra::Base - Middleware, Bibliotecas e aplicativos modulares

Definir sua aplicação em um nível superior de trabalho funciona bem para
micro aplicativos, mas tem consideráveis incovenientes na construção de
componentes reutilizáveis como um middleware Rack, metal Rails,
bibliotecas simples como um componente de servidor, ou mesmo extensões
Sinatra. A DSL de nível superior polui o espaço do objeto e assume um
estilo de configuração de micro aplicativos (exemplo: uma simples
arquivo de aplicação, diretórios `./public` e `./views`, logs, página de
detalhes de exceção, etc.). É onde o `Sinatra::Base` entra em jogo:

```ruby
require 'sinatra/base'

class MinhaApp < Sinatra::Base
  set :sessions, true
  set :foo, 'bar'

  get '/' do
    'Ola mundo!'
  end
end
```

A classe `MinhaApp` é um componente Rack independente que pode agir como
um middleware Rack, uma aplicação Rack, ou metal Rails. Você pode
utilizar ou executar esta classe com um arquivo rackup `config.ru`;
ou, controlar um componente de servidor fornecendo como biblioteca:

```ruby
MinhaApp.run! :host => 'localhost', :port => 9090
```

Os métodos disponíveis para subclasses `Sinatra::Base` são exatamente como
aqueles disponíveis via a DSL de nível superior. Aplicações de nível
mais alto podem ser convertidas para componentes `Sinatra::Base` com duas
modificações:

-   Seu arquivo deve requerer `sinatra/base` ao invés de `sinatra`;
    outra coisa, todos os métodos DSL do Sinatra são importados para o
    espaço principal.

-   Coloque as rotas da sua aplicação, manipuladores de erro, filtros e
    opções na subclasse de um `Sinatra::Base`.

`Sinatra::Base` é um quadro branco. Muitas opções são desabilitadas por
padrão, incluindo o servidor embutido. Veja [Opções e
Configurações](http://www.sinatrarb.com/configuration.html) para
detalhes de opções disponíveis e seus comportamentos.

SIDEBAR: A DSL de alto nível do Sinatra é implementada utilizando um simples
sistema de delegação. A classe `Sinatra::Application` – uma subclasse especial
da `Sinatra::Base` – recebe todos os `:get`, `:put`, `:post`, `:delete`,
`:before`, `:error`, `:not_found`, `:configure`, e `:set messages` enviados
para o alto nível. Dê uma olhada no código você mesmo: aqui está o
[Sinatra::Delegator
mixin](http://github.com/sinatra/sinatra/blob/ceac46f0bc129a6e994a06100aa854f606fe5992/lib/sinatra/base.rb#L1128)
sendo [incluido dentro de um espaço
principal](http://github.com/sinatra/sinatra/blob/ceac46f0bc129a6e994a06100aa854f606fe5992/lib/sinatra/main.rb#L28)

## Linha de Comando

Aplicações Sinatra podem ser executadas diretamente:

```shell
ruby minhaapp.rb [-h] [-x] [-e AMBIENTE] [-p PORTA] [-o HOST] [-s SERVIDOR]
```

As opções são:

```
-h # ajuda
-p # define a porta (padrão é 4567)
-o # define o host (padrão é 0.0.0.0)
-e # define o ambiente (padrão é development)
-s # especifica o servidor/manipulador rack (padrão é thin)
-x # ativa o bloqueio (padrão é desligado)
```

### Multi-threading

_Parafraseando [esta resposta no StackOverflow](resposta-so) por Konstantin_

Sinatra não impõe nenhum modelo de concorrencia, mas deixa isso como responsabilidade
do Rack (servidor) subjacente como o Thin, Puma ou WEBrick. Sinatra por si só é thread-safe,
então não há nenhum problema se um Rack handler usar um modelo de thread de concorrência. Isso
significaria que ao iniciar o servidor, você teria que espeficiar o método de invocação correto
para o Rack handler específico. Os seguintes exemplos é uma demonstração de como iniciar um
servidor Thin multi-thread:

```ruby
# app.rb

require 'sinatra/base'

class App < Sinatra::Base
  get '/' do
    'Olá mundo'
  end
end

App.run!
```

Para iniciar o servidor seria:

```shell
thin --threaded start
```

[resposrta-so]: http://stackoverflow.com/questions/6278817/is-sinatra-multi-threaded/6282999#6282999)

## A última versão

Se você gostaria de utilizar o código da última versão do Sinatra, crie
um clone local e execute sua aplicação com o diretório `sinatra/lib` no
`LOAD_PATH`:

```shell
cd minhaapp
git clone git://github.com/sinatra/sinatra.git
ruby -I sinatra/lib minhaapp.rb
```

Alternativamente, você pode adicionar o diretório do `sinatra/lib` no
`LOAD_PATH` do seu aplicativo:

```ruby
$LOAD_PATH.unshift File.dirname(__FILE__) + '/sinatra/lib'
require 'rubygems'
require 'sinatra'

get '/sobre' do
  "Estou rodando a versão" + Sinatra::VERSION
end
```

Para atualizar o código do Sinatra no futuro:

```shell
cd meuprojeto/sinatra
git pull
```

## Mais

* [Website do Projeto](http://www.sinatrarb.com/) - Documentação
    adicional, novidades e links para outros recursos.
* [Contribuir](http://www.sinatrarb.com/contributing) - Encontrar um
    bug? Precisa de ajuda? Tem um patch?
* [Acompanhar Questões](https://github.com/sinatra/sinatra/issues)
* [Twitter](https://twitter.com/sinatra)
* [Lista de Email](http://groups.google.com/group/sinatrarb/topics)
* [Sinatra Book](https://github.com/sinatra/sinatra-book/) Livro de Receitas
* Documentação da API para a [última release](http://www.rubydoc.info/gems/sinatra)
* [IRC: \#sinatra](irc://chat.freenode.net/#sinatra) em
    [freenode.net](http://freenode.net)
* [Servidor de CI](https://travis-ci.org/sinatra/sinatra)
