<h3>Single responsibility principle (Princípio da responsabilidade única).</h3>
<p>O primeiro princípio do SOLID basicamente diz que uma classe deveria ter exclusivamente uma responsabilidade.</p>
<p>
Diz que quando temos uma classe que não atenda esse princípio, devemos dividi-la em mais classes até que isso ocorra.</p>
<p>
Vamos para o exemplo. Criaremos uma pequena classe para gerenciar nossos relatórios.</p>

```php

<?php
class Report
{
    public function getTitle()
    {
        return 'Report Title';
    }

    public function getDate()
    {
        return '2018-01-22';
    }

    public function getContents()
    {
        return [
            'title' => $this->getTitle(),
            'date' => $this->getDate(),
        ];
    }

    public function formatJson()
    {
        return json_encode($this->getContents());
    }
}

```

<p>Nesse nosso pequeno exemplo, a nossa classe tem duas responsabilidades:</p>
<p>
Trazer os dados do relatório.
Formatar o relatório para o formato JSON. Então estamos ferindo o princípio da responsabilidade única.
Para resolvermos isso, vamos fazer o seguinte:</p>

```php
<?php
class Report
{
    public function getTitle()
    {
        return 'Report Title';
    }

    public function getDate()
    {
        return '2018-01-22';
    }

    public function getContents()
    {
        return [
            'title' => $this->getTitle(),
            'date' => $this->getDate(),
        ];
    }
}

class JsonReportFormatter
{
    public function format(Report $report)
    {
        return json_encode($report->getContents());
    }
}


```

<p>Agora foi criado uma nova classe chamada JsonReportFormatter, responsável exclusivamente em formatar o relatório em JSON.</p>

<h3>Open/closed principle (Princípio aberto/fechado)</h3>

<p>O segundo princípio diz que você deve ser capaz de estender um comportamento de uma classe, sem modificá-lo, a classe está aberta para expansão e fechado para alteração.</p>

<p>Sempre que precisarmos criar um novo recurso, devemos criar uma nova classe que implemente esse recurso.</p>

<p>Criaremos um exemplo. Vamos imaginar a situação onde temos que criar uma pequena classe para registrar os logs da aplicação.</p>

```php
<?php
class Logger
{
    public function writeTxt($message)
    {
        //lógica
    }
}

```

<p>Neste exemplo, a nossa classe cria arquivos .txt para armazenar as mensagens do log. Agora, se quisermos criar um para criar arquivos .csv, precisaríamos modificar a classe para algo parecido com isso:</p>

```php
<?php
class Logger
{
    public function writeTxt($message)
    {
        //lógica
    }

    public function writeCsv($message)
    {
        //lógica
    }
}


```

<p>Foi preciso alterar a classe Logger, algo que fere o princípio aberto/fechado. A classe tem de estar fechada para alteração.</p>

<p>Vamos refatorar o nosso código.</p>

```php
<?php
class Logger
{
    private $writer;

    public function __construct(Writer $writer)
    {
        $this-writer = $writer;
    }

    public function write($message)
    {
        $this-writer->write($message);
    }
}

interface Writer
{
    public function write($message);
}

class Txt implements Writer
{
    public function write($message)
    {
        //lógica
    }
}

class Csv implements Writer
{
    public function write($message)
    {
        //lógica
    }
}

```

<p>Agora, sim. Implementamos os mesmos recursos que tínhamos antes, mas agora, para implementar um novo recurso, como escrever em .doc, por exemplo, basta criar uma nova classe Doc que implemente a interface Writer e pronto, não alteramos nada da classe Logger.</p>
<h3>Liskov substitution principle(Princípio da Substituição de Liskov)</h3>
<p>O terceiro princípio é o mais complicado de entender, ele foi escrito pela cientista da computação Barbara Liskov, que resumiu o seu princípio com:</p>
<p>
Se q(x) é uma propriedade demonstrável dos objetos x de tipo T. Então q(y) deve ser verdadeiro para objetos y de tipo S onde S é um subtipo de T.
</p>
<p>
Já vi algumas interpretações para o princípio da substituição de Liskov, mas o que eu mais acredito que seja o certo é que as classes derivadas podem ser substituíveis por suas classes base e classes irmãs. Resumindo, quando temos uma classe B e classe C que estende da classe A, deveríamos poder trocar a classe B pelo classe A, ou pela classe C dentro do projeto sem quebrar o código.
</p>
<p>Vamos para o exemplo.</p>

```php

<?php
class Logger
{
public function writer($message)
{
//lógica
}
}

class DatabaseLogger extends Logger
{
public function __construct(DataBase $database)
{
$this->database = $database;
}

public function writer($message)
{
    //lógica
}
}
class FileLogger extends Logger
{
public function __construct(FIleManager $fileManager)
{
$this->fileManager = $fileManager;
}

public function writer($message)
{
    //lógica
}
}

/*
Nesse exemplo nós temos a classe *Logger*, *DatabaseLogger* e a *FileLogger* o princípio da substituição de Liskov diz que podemos usar tanto a classe *FileLogger* quanto a *DatabaseLogger* na nossa aplicação deve manter o mesmo comportamento, isso vale para qualquer classe que extende da classe *Logger*.

Como mostra no nesse pequeno exemplo, podemos usar todas as classes que vamos atingir o mesmo resultado.*/

$logger = new FileLogger($fileManager);
$logger->write('teste 1');

$logger = new DatabaseLogger($database);
$logger->write('teste 2');

```
<p>
Mas ainda temos um problema, ambas as classes tem dependências para funcionar, nesse caso, o indicado é usar um pacote para injeção de dependências.</p>
<p>
Observação: Para isso, precisamos sempre cuidar o que vamos retornar nos nossos métodos para não quebrar o código.</p>

<h3>Interface segregation principle(Princípio da Segregação da Interface)</h3>
<p>
O quarto princípio diz que muitas interfaces específicas são melhores que uma única interface para não forçar uma classe a implementar um método que ela não vai usar. Precisamos criar pequenas interfaces mais específicas ao invés de termos uma única genérica.
</p>
<p>
Vamos exemplificar para entender melhor, vamos criar algumas interfaces e classes de aves para nos ajudar.</p>

```php
<?php
interface Aves
{
    public function andar();
    public function voar();
    public function nadar();
}
class Pato implements Aves
{
    public function voar()
    {
        //lógica
    }

    public function nadar()
    {
        //lógica
    }

    public function andar()
    {
        //lógica
    }
}

class Pinguim implements Aves
{
    public function voar()
    {
        //lógica
    }

    public function nadar()
    {
        //lógica
    }

    public function andar()
    {
        //lógica
    }
}

class Andorinha implements Aves
{
    public function voar()
    {
        //lógica
    }

    public function nadar()
    {
        //lógica
    }

    public function andar()
    {
        //lógica
    }
}
```

<p>Nessa estrutura estamos forçando algumas aves a implementar alguns métodos que elas não deveriam possuir. Isso porque existem aves que não voam e aves que não nadam. Como o Pinguim, que implementou o método voar já que pinguins não voam. E o mesmo para a Andorinha, que não nada.</p>

<p>Vamos ver como criar uma estrutura que siga o princípio da segregação da interface.</p>

```php
<?php
interface Aves
{
    public function andar();
}

interface AvesQueVoam extends Aves
{
    public function voar();
}

interface AvesQueNadam extends Aves
{
    public function nadar();
}

class Pato implements AvesQueVoam, AvesQueNadam
{
    public function voar()
    {
        //lógica
    }

    public function nadar()
    {
        //lógica
    }

    public function andar()
    {
        //lógica
    }
}

class Pinguim implements AvesQueNadam
{
    public function nadar()
    {
        //lógica
    }

    public function andar()
    {
        //lógica
    }
}

class Andorinha implements AvesQueVoam
{
    public function andar()
    {
        //lógica
    }

    public function voar()
    {
        //lógica
    }
}


```

<p>Agora, sim. Temos as interfaces AvesQueVoam, AvesQueNadam ao invés de somente uma interface Aves.</p>

<h3>Dependency inversion principle (Princípio da inversão da dependência)</h3>
<p>O último e quinto princípio diz para que uma classe dependa de uma abstração e não de uma implementação.</p>

<p>Vamos exemplificar.</p>

```php
<?php
class Email
{
    public function enviar($mensagem)
    {
        //lógica
    }
}

class Notificacao
{
    public __construct()
    {
        $this->mensagem = new Email;
    }

    public function enviar($mensagem)
    {
        $this->mensagem->enviar($mensagem)
    }
}
```

<p>Neste exemplo, temos o que chamamos de acoplamento e uma dependência da classe Notificação cria uma instancia da classe Email dentro dela. E o metodo enviar faz a utilização da classe Email para enviar a notificação por e-mail. Isso fere o princípio da inversão da dependência, porque não desenvolvemos a classe Notificação para uma abstração, e sim para uma implementação já que classe E-mail implementa a lógica para o envio do e-mail.</p>

<p>Vamos resolver isso.</p>

```php
<?php
interface MensagemInterface
{
    public function enviar($mensagem);
}
class Email implements MensagemInterface
{
    public function enviar($mensagem)
    {
        //lógica
    }
}

class Notificacao
{
    public __construct(MensagemInterface $mensagem)
    {
        $this->mensagem = $mensagem;
    }

    public function enviar($mensagem)
    {
        $this->mensagem->enviar($mensagem)
    }
}
```

<p>Agora desaclopamos a classe E-mail da classe Notificação estamos trabalhando com a abstração MensagemInterface, para Notificação não importa qual classe você está usando, e sim, que ela implemente a interface MensagemInterface porque sabemos que ela vai ter o método enviar que precisamos. Isso também permite que a classe Notificação use outras classes que implementem a interface MensagemInterface.</p>