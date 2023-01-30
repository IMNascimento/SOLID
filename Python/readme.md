<h3>1 - SINGLE RESPONSABILITY PRINCIPLE (SRP):</h3>
<strong>Princípio da responsabilidade única:</strong> <i>"Uma classe não pode ter mais de um motivo para ser alterada."</i>
<p>
Sendo ainda mais específico, uma classe deve ter somente um motivo para ser alterada.
</p>
<p>
Muitas vezes precisamos de algum método ou de alguma propriedade e inserimos em uma determinada classe porque simplesmente não existe nenhum outro lugar melhor para colocá-lo. Com isso, estamos alterando o comportamento de uma classe que já havia sido definida para o que deveria fazer. Além de alterar o comportamento da classe, ainda estamos tornando ela ainda mais frágil (aumentando acoplamento e diminuindo a coesão).
</p>
Por exemplo:

```python
class Quadrado:
    
    def __init__(self, comprimento_lado=0):
        self.comprimento_lado = comprimento_lado
    def calcula_area(self):
        return self.comprimento_lado ** 2
    def desenhar(self):
        # Desenha um quadrado
        pass

```

<p>
Dessa forma, podemos claramente ver que estamos atribuindo duas responsabilidades para a classe Quadrado, a de calcular a área e também desenhar o quadrado. Isso não está errado, porém se pensarmos no princípio de responsabilidade única, podemos ter duas classes, uma para calcular e outra para desenhar:
</p>

```python

class CalculaQuadrado:
    def __init__(self, comprimento_lado=0):
        self.comprimento_lado = comprimento_lado
    def calcula_area(self):
        return self.comprimento_lado * 2
class DesenhaQuadrado:
    
    def desenha(self):
        # Desenha um quadrado
        pass

```
<p>
Assim, cada classe tem um único propósito: temos uma classe que podemos utilizar para cálculos geométricos e outra classe responsável por desenhos geométricos.
</p>

<h3>2 - OPEN/CLOSED PRINCIPLE (OCP):</h3>
<strong>Princípio do aberto/fechado:</strong> <i>"O comportamento de uma classe deve estar aberta para extensão porém fechada para alterações."</i>
<p>
A principal idéia é que uma classe mãe deve ser genérica e abstrata para ser estendida e reaproveitada por classes filhas. Esse princípio rege que a classe mãe não deve ser alterada.
</p>
<p>
No Python podemos fazer uma operação conhecida como Monkey-Patching. Resumidamente, uma classe em Python é mutável e um método é apenas um atributo de uma classe. Dessa forma, é possível sobrescrever um método dinamicamente em tempo de execução. Não entrarei em mais detalhes sobre esta prática por não estar no escopo desse artigo, porém entenda que essa isso deve ser realizado com muito cuidado.
</p>
Exemplo:

```python
area = CalculaQuadrado(10)
def forma_geometrica():
    return 'Sou um quadrado.'
area.forma_geometrica = forma_geometrica
print(area.forma_geometrica()) # Imprime: Sou um quadrado.

```
<p>
Dessa forma, acabamos de adicionar uma nova função para a instância area. Você deve ter percebido que, ainda assim, forma_geometrica não está referenciando nossa instância (self). Por esse motivo, trata-se de uma função e não de um método inerente a instância. No entanto, também podemos adicionar métodos para a classe dessa mesma forma, conforme observamos abaixo:
</p>

```python
def calcula_volume(self):
    return self.comprimento_lado ** 3
CalculaQuadrado.volume = calcula_volume
quadrado = CalculaQuadrado(10)
print(quadrado.volume()) # Imprime 1000

```
<p>
Acabamos de alterar a implementação da classe para todas instâncias de CalculaQuadrado depois desse patch. Essa possibilidade é muito poderosa, mas também pode representar um risco gigantesco se usado sem cautela.

Você percebeu que podemos facilmente alterar o comportamento de uma classe em Python?

Vale ressaltar que esse princípio visa nos chamar a atenção justamente de que não devemos desrespeitar o encapsulamento de uma classe.
</p>

<h3>3 - LISKOV SUBSTITUTION PRINCIPLE (LSP):</h3>
<strong>Princípio da substituição de Liskov:</strong><i> "Uma classe filha deve poder ser substituída pela suas classe pai."</i>
<p>
Esse princípio define que uma subclasse deve poder ser substituída pela respectiva superclasse. E estas podem ser substituídas por qualquer uma das suas subclasses. Uma subclasse deve sobrescrever os métodos da superclasse de forma que a interface continue sempre a mesma.
</p>
Por exemplo:

```python
class Retangulo:
    def __init__(self, largura=0, altura=0):
        self.largura = largura
        self.altura = altura
    def calcula_area(self):
        return (self.largura * self.altura)
class Quadrado(Retangulo):
    def __init__(self, largura=0, altura=0):
        super(Quadrado, self).__init__(largura, altura)
        self.comprimento_largura = largura
        self.comprimento_altura = altura
    def calcula_area(self):
        return (self.comprimento_largura * self.comprimento_altura)
```
<p>Esse princípio nos mostra que devemos verificar se o polimorfismo faz mesmo sentido no uso da herança. Ou seja, devemos ver se realmente qualquer subclasse pode ser usada no lugar da superclasse, para evitarmos utilizar herança de forma inadequada.</p>

<p>Neste caso não faz sentido algum estender a classe Retangulo para a classe Quadrado. Pois já que todos os lados de um quadrado são iguais, não há a necessidade de termos os atributos largura e altura. Ao criarmos comprimento_largura e comprimento_altura, estamos utilizando a famosa gambiarra(contorno técnico) para resolvemos o problema. Não faça isso!</p>

<h3>4 - INTERFACE SEGREGATION PRINCIPLE (ISP):</h3>
<strong>Princípio da segregação de interfaces:</strong><i> "Várias interfaces específicas são melhores do que uma interface genérica."</i>
<p>
Este princípio define que uma classe não deve conhecer nem depender de métodos que não necessitam.
</p>
<p>
Já sabemos que para que uma classe seja coesa e reutilizável, ela não deve possuir mais de uma responsabilidade. Mas ainda assim, essa única responsabilidade pode ser quebrada em responsabilidades ainda menores, tornando a interface muito mais amigável.
</p><p>
No Python podemos fazer herança múltipla, então resolvi abstrair uma classe para calcular o volume de um polígono baseado em seu número de lados, uma para calcular a área também baseado em seu número de lados. Assim, eu conseguiria reutilizá-las para outros polígonos com quantidades de lados diferentes:
</p>
```python
class CalculaPerimetro:
    def __init__(self, numero_lados=0):
        self.numero_lados = numero_lados
    def calcula_perimetro(self):
        return 'Calcula o perímetro para um polígono de N lados'
class CalculaArea:
    def __init__(self, numero_lados=0):
        self.numero_lados = numero_lados
    def calcula_area(self):
        return 'Calcula área para determinado polígono de N lados'
class CalculaVolume:
    def __init__(self, numero_lados=0):
        self.numero_lados = numero_lados
    def calcula_volume(self):
        return 'Calcula volume para determinado polígono de N lados'
class Quadrado(CalculaArea, CalculaVolume):
    def __init__(self, numero_lados=None):
        super(Quadrado, self).__init__(numero_lados)
        self.numero_lados=numero_lados
    @property
    def area(self):
        return self.calcula_area()
    @property
    def volume(self):
        return self.calcula_volume()
```
<p>Perceba que na classe Quadrado, herdei e criei propriedades somente para área e volume. Não utilizei a classe CalculaPerimetro pois eu decidi que agora eu não iria usá-la, não tinha porque implementar uma nova propriedade(interface) para a minha classe Quadrado.</p>
<p>
Observação: Com um exemplo tão simples, talvez essa estrutura não faça muito sentido. Porém tentei aplicar o princípio do ISP de forma que o conceito fique entendível. Agora nossa classe Quadrado tem as propriedades área e volume que vou precisar acessar, que são abstraídos das superclasses herdadas.
</p>

<h3>5 - DEPENDENCY INVERSION PRINCIPLE (DIP):</h3>
<strong>Princípio da inversão de dependências:</strong><i> "Devemos depender de classes abstratas e não de classes concretas."</i>
<p>
Esse princípio define que módulos de alto nível não devem depender de módulos de baixo nível. Ambos devem depender de abstrações. Ao mesmo tempo, estas abstrações não devem depender detalhes. Os detalhes é que devem depender de abstrações.
</p>

<p>
Uma classe não deve conhecer outra classe para realizar alguma operação. Muito pelo contrário, deve existir uma interface genérica para intermediar esse acesso da forma mais abstrata possível.
</p>
<p>
Mas vamos concordar que em Python, geralmente nós não utilizamos interfaces para resolvermos problemas. No entanto, podem aparecer situações em que precisaremos definir uma interface, por exemplo em um contrato de uma API, na qual gostaríamos de estender alguma biblioteca ou framework. Para este caso, podemos contar com o módulo de Abstract Base Classes(ABC). E como o Python permite herança múltipla, as ABC's se comportam essencialmente como uma interface.</p>