#  Orientação a objetos com caminhantes aleatórios

Inspirado na introdução à simulações físicas apresentada por Daniel Shiffman em *Nature of Code*, 
neste tutorial que se desenvolve  em etapas, serão introduz alguns conceitos de Programação Orientada a Objetos (POO): classe, atributos de dados e métodos, instâncias e encapsulamento.

Não serão abordadas as questões de herança e composição. Para poder aproveitar o exemplo sugerimos que você revise antes o seguinte vocabulário e algumas ideias de programação:

* Métodos de desenho `rect`, `line`, `ellipse`, `beginShape`, `vertex` e `endShape`;
* Controle de atributos gráficos `fill`, `stroke`, `noStroke`, `noFill`, `background`;
* Controle de fluxo de execução e laços `if`, `else`, `for`;
* Declaração de funções com e sem parâmetros;
<!-- * Controle do sistema de coordenadas `pushMatrix`, `translate`, `rotate`, `scale`, `popMatrix`. -->

## 1. Redesenhando formas e atualizando variáveis no laço principal

Para se obter o efeito de movimento (animação da particula) criamos um par de variáveis globais x e y, inicializadas no setup() com as coordenadas do meio da àrea de desenho. Note que o escopo global dessas variáveis precisa ser indicado com a palavra chave global quando pretendemos alterá-las.

O `draw()` que faz parte da infraestrutura do Processing para permitir animações, é executado repetidas vezes, continuamente, é conhecido às vezes como o "laço principal" do sketch.

Neste bloco podemos ou não limpar a tela com `background()` e invocar a função de desenho `circle()` com a posição indicada pelas variáveis x e y, atualizar as variáveis de posição e por fim checar se estas estão além de um certo limite e precisam ser alteradas (redefinindo a posição para um novo ciclo de alterações).

```pde
float x, y;

void setup() { // Código de configuração, executado no início
  size(100, 100);  // área de desenho
  // coordenadas do meio da área de desenho
  x = width / 2;
  y = height / 2;  
  background(0); // fundo preto
}
void draw() { // Laço principal de repetição
  // background(0); // Opcional, limpa a tela com a cor indicada
  circle(x, y, 5);  // desenha um círculo
  x = x + random(-5, 5);  // modifica o x
  y = y + random(-5, 5);  // modifica o y
  if (x > width + 5) x = -5;
  if (y > height + 5) y = -5;
  if (x < -5) x = width + 5;
  if (y < -5) y = height + 5;
}
```

## 2. Primeira aproximação da classe Particula

Vamos agora obter o mesmo comportamento usando um objeto da classe particula.
A classe é definida pelo bloco `class Particula{ ... }` que contém logo no começo a definição do método `Particula()`, conhecido como construtor, e que produz um objeto da classe e inicializa os atributos de dados (campos) de posição e tamanho.

Em seguida, o método `desenha()` é praticamente o desenho do círculo que escrevemos no passo inicial, usando os atributos de posição e tamanho do próprio objeto (instância) quando executado.

O método `anda()` contém o código anteriormente usado para atualizar a posição nas variáveis globais, agora atualiza os atributos de dados (campos ou variáveis de instância) de posição do objeto. 

No bloco `setup()` criamos uma instância de particula no meio da área de desenho com a linha `Particula = new Particula(width / 2, height / 2, 50)` e o `draw()` vai repetidamente limpar a tela (opcionalmente) e chamar os métodos de desenho e atualização, `Particula.desenha()` e `Particula.anda()` respectivamente.

```pde
Particula particula; // variável global para uma particula

void setup() {
  /* define área de desenho e popula lista de particulas */
  size(100, 100);  // área de desenho
  background(0);
  float meia_largura = width / 2;
  float meia_altura = height / 2;
  particula = new Particula(meia_largura, meia_altura, 5);
}

void draw() {
  /* Limpa a tela (opcional), desenha e atualiza particulas */
  // background(0);  // limpa a tela com fundo preto
  particula.desenha();
  particula.anda();
}

class Particula {
  /* Classe particula */
  float x, y, tamanho;
  Particula(float px, float py, float ptamanho) {
    x = px;
    y = py;
    tamanho = ptamanho;
  }

  void desenha() {
    circle(x, y, tamanho);  // desenha um círculo
  }

  void anda() {
    /* atualiza a posição do objeto e devolve do lado oposto se sair */
    x = x + random(-5, 5);  // modifica o x
    y = y + random(-5, 5);  // modifica o y
    if (x > width + 25) x = -25;
    if (y > height + 25) y = -25;
    if (x < -25) x = width + 25;
    if (y < -25) y = height + 25;
  }
}
```

## 3. Instanciando mais alguns objetos

A vantagem da estruturação e encapsulamento de termos um objeto Particula criado por uma classe particula pode começar a ser visto quando instanciamos mais de uma particula.

```pde
Particula particula_0, particula_1, particula_2; // lista de objetos

void setup() {
  /* define área de desenho e popula lista de particulas */
  size(500, 500);  // área de desenho
  background(0);
  float meia_largura = width / 2;
  float meia_altura = height / 2;
  particula_0 = new Particula(meia_largura, meia_altura, 5);
  particula_1 = new Particula(280, 100, 10);
  particula_2 = new Particula(200, 400, 20);
}

void draw() {
  /* Limpa a tela, desenha e atualiza particulas */
  background(0);  // atualização do desenho, fundo preto
  particula_0.desenha();
  particula_0.anda();
  particula_1.desenha();
  particula_1.anda();
  particula_2.desenha();
  particula_2.anda();
}
```

## 4. Ampliando a classe, mudando o comportamento e adicionando outras propriedades.

O passo seguinte é dado modificando o código da classe particula.

Na definição da classe `Particula`:
- É criado um atributo da classe para determinar ma velocidade de redução do tamanho (`velocidadeEncolhimento`);
- É crirada uma variável para controlar o tamanho dos "passos aleatórios" (`maxRandom`);

No método construtor `Particula()`:
- Sorteio do tamanho, caso tenha sido passado 0 na expressão construtora;
- Sorteio de uma cor.

No método `desenha()`:
- Aplicação da cor de preenchimento com `fill(cor)`.

No método `anda()`
- Uso do `maxRandom` no sorteio do deslocamento;
- Atualização do tamanho;

```pde
class Particula {
  /* Classe particula, cor sorteada, tamanho sorteado */
  float x, y, tamanho;
  float maxRandom = 3;
  float velocidadeEncolhimento = 0.1;
  color cor;
  Particula(float px, float py, float ptamanho) {
    x = px;
    y = py;
    if (ptamanho != 0) {
      tamanho = ptamanho;
    } else {
      tamanho = random(50, 100);
    }
    cor  = color(random(255), // R
      random(255), // G
      random(255), // B
      200);  // alpha
  }

  void desenha() {
    fill(cor);
    if (tamanho > 0) circle(x, y, tamanho);
  }
  void anda() {
    /* atualiza a posição do objeto e devolve do lado oposto se sair */
    x = x + random(-maxRandom, maxRandom);  // modifica o x
    y = y + random(-maxRandom, maxRandom);  // modifica o y
    if (x > width + 25) x = -25;
    if (y > height + 25) y = -25;
    if (x < -25) x = width + 25;
    if (y < -25) y = height + 25;
    // reduz o tamanho
    if (tamanho > 0) {
      tamanho = tamanho - velocidadeEncolhimento;
    }
  }
}
```

## 5. Uma lista de objetos

Neste passo criamos uma estrutura de dados, no caso uma lista dinâmica do tipo *ArrayList*, que pode conter referências para um número não previamente determinado de objetos. Vamos instanciar 50 particulas no `setup()` e , em seguida, no `draw()` iteramos por estas particulas contidas no ArrayList `particulas` com um tipo de laço conhecido como "for each" que tem a estrutura `for (Tipo objeto : lista_de_objetos){ }`. 

```pde
ArrayList<Particula> particulas; // lista de objetos

void setup() {
  /* define área de desenho e popula lista de particulas */
  size(400, 400);  // área de desenho
  background(0);  // fundo preto

  float meia_largura = width / 2;
  float meia_altura = height / 2;
  particulas = new ArrayList<Particula>();
  for (int i=0; i <50; i++) {
    particulas.add(new Particula(random(width), random(height), 0));
  }
}

void draw() {
  /* Limpa a tela, desenha e atualiza particulas */
  //background(0);  // limpa a tela, fundo preto
  for (Particula p : particulas) {
    p.desenha();
    p.anda();
  }
}
```

## 6. Acrescentando e removendo objetos

Como extra, acrescentamos exemplo dos métodos append() e remove() do ArrayList, chamados nos eventos de clique do mouse ou acionamento da barra de espaço no teclado, acrescentando ou removendo objetos respectivamente. 

```pde
void mousePressed() {
  /* Acrescenta pequena particula branca */
  particula nova_particula = new Particula(mouseX, mouseY, 25);
  nova_particula.cor = color(255);  // forçando que seja branca!
  particulas.add(nova_particula);
}

void keyPressed() {  
  /* tecla 'espaço' remove a última particula da lista */
  int num_particulas =  particulas.size();
  if (key == ' ' && num_particulas > 1) {
     particulas.remove(num_particulas - 1);
  }
}
```

### Referências:

VILLARES, Alexandre. B. A.; MOREIRA, Daniel. de C.; GOMES, Monica Rizzolli. [**Ensino de programação em um contexto de exploração gráfica com Processing modo Python.**](https://villares.github.io/mestrado/VILLARES_MOREIRA_GOMES_GRAPHICA_2017) GRAPHICA 2017: XII International Conference on Graphics Engineering for Arts and Design. 2017.

[Objects tutorial](https://processing.org/tutorials/objects/) by Daniel Shiffman @ Processing.org

