<h1>Sass Fundaments</h1>

<h2>Algumas anotações sobre SASS:</h2>


#
- Import e Use
    
    Quando queremos importar um arquivo sass, usamos @import ‘./pasta/nome’
    
    OBS: se o nome do arquivo tiver um underline antes do nome, ao compilar ele não gerará um arquivo css
    
- Nesting
    
    Quando queremos alterar um elemento novamente, como em uma media query, é necessário replicar toda a árvore.
    
    ```scss
    @media screen and (min-width: 360px) {
        nav {
            width: 100vw;
            height: 80px;
    
            a {
                color: blue;
                text-decoration: none;
                font-size: 18px;
                -moz-transform: scale(1.2);
    
                &:hover { /*maneira de reconhecer o hover como psseudoclasse*/
                    color: red;
                }
    
                span {
                    font-size: 16px;
                    color: green;
                }
    
            }
        }
    }
    
    @media screen and (min-width: 1260px) { /*necessario replicat toda a árvore para mudar um elemento que está no fundo da hierarquia*/
        nav {
            a {
                span {
                    color: purple;
                }
            }
        }
    }
    ```
    
- Variables
    
    Para se declarar uma variável no sass, basta utilizar $nome-tipo
    
    ```scss
    /*Cores*/
    $primary-color: red;
    
    /*Espaçamento*/
    $spacer-1: 8px;
    $spacer-2: 16px;
    ```
    
    Interessante criar um arquivo (theme) somente para variáveis e no arquivo principal usa-lo no primeiro import
    
- Mixins
    
    Mixins são trechos de códigos que permitem criar componentes, que também podem receber argumentos, basta usar @mixin nomedocomponente e colocar as propriedades dele.
    
    ```scss
    @mixin card($bgcolor, $txtcolor, $icon) {//argumentos
        width: 20%;
        display: table;
        background-color: $bgcolor;
        background-image: url($icon);
        background-size: 20%;
        background-position: bottom right;
        background-repeat: no-repeat;
        border-radius: 8px;
        margin-right: 32px;
        margin-top: 32px;
        height: 230px;
        padding: 32px;
        color: $txtcolor
    }
    
    //Chamando o mixin
    
    .card-positive {
        @include card(#04e404, black, 'img/up.png'); //passando os argumentos
    }
    
    .card-negative {
        @include card(#cc0606, black, 'img/down.png');
    }
    
    	//Além disso podemos deixar um valor padrão
    @mixin btn-large($height: 60px, $fontsize: 28px) {
        height: $height;
        font-size: $fontsize;
    }
    
    //chamando o default
    .btn-large {
        @include btn-large(); //não precisa de argumento aqui
    }
    ```
    
- Expressions e Operators
    
    ```scss
    &:after {
            content: 'status: ' + $badge;
            position: absolute;
            top: 16px;
            right: 16px;
            background-color: black;
            color: #fff;
            padding: 6px;
            padding: 6px 10px;
            border-radius: 3px;
        }
    }
    
    .card-positive {
        @include card(#04e404, black, 'img/up.png', 'Opa');
    }
    ```
    
- Functions
    
    Site com diversas funções: 
    
    [Sass Functions Cheat Sheet](https://cheatography.com/hamidyfine/cheat-sheets/sass-functions/)
    
    ```scss
    background-color: lighten($bgcolor, 10%); //a cor será clareada em 10%
    background-color: darken($bgcolor, 15%); // vai escurecer o bg em 15%
    background-color: grayscale($bgcolor); // tons de cinza
    background-color: complement($bgcolor); // vai pegar a cor oposta
    ```
    
    Além dessas funções, podemos criar uma função própria, como uma função que pode clarear, escurecer ou inverter a cor.
    
    ```scss
    @function changeColor($type, $color) { //essa função recebe dois argumentos
    
        //função para escurecer, clarear ou inverter a cor
        @if $type ==light {
            $newColor: lighten($color, 10%);
            @return $newColor;
        }
    
        @else if $type ==dark {
            $newColor: darken($color, 30%);
            @return $newColor;
        }
    
        @else if $type ==comp {
            $newColor: complement($color);
            @return $newColor;
        }
    
        @else {
            $newColor: $color;
            @return $newColor;
        }
    }
    ```
    
    O return sempre é necessário no SASS e devemos terminar com else.
    
- Condicionais e If Else
    
    Função com if else para alterar font size
    
    ```scss
    @mixin title($size) {
        @if $size==small {
            font-size: small;
        }
    
        @else if $size ==large {
            font-size: large;
        }
    
        @else {
            font-size: 24px;
        }
    }
    
    //incluindo no titulo
    h1 {
        @include title(large);
    }
    ```
    
- Each Loop
    
    Podemos otimizar o trabalho definindo uma loop similar ao For Of no JavaScript
    
    ```scss
    //Método 1
    $networks: youtube, twitter, facebook, linkedin; //similar a um arrau
    
    @each $icon in $networks { //cria a váriavel icon
        .icon-#{$icon} { //.icon é a tag no html + o nome a ser gerado
            background-image: url('img/#{$icon}.svg'); //trocado somente o nome para carregar imagens distintas
        }
    }
    
    // Método 2 - usando como se fosse um objeto
    $networks2: (
        youtube: red,
        twitter: skyblue,
        facebook: royalblue,
        linkedin: steelblue
    );
    
    @each $key, $value in $networks2 { //usando duas chaves nome e cor
        .icon-#{$key} {
            background-image: url('img/#{$key}.svg');
            background-color: $value;
        }
    }
    
    //Método 3 -desestruturação
    $networks3: 
        youtube red 64px,
        twitter skyblue 64px,
        facebook royalblue 64px,
        linkedin steelblue 64px;
    
    //similar ao for of no javascript
    @each $name, $color, $size in $networks3 { //usando 3 chaves nome, cor e tamanho
        .icon-#{$name} {
            background-image: url('img/#{$name}.svg');
            background-color: $color;
            font-size: $size;
        }
    }
    ```
    
    Depois de ser executado pelo Sass, no arquivo style.css ficará:
    
    ```css
    /*Método 1*/
    .icon-youtube {
      background-image: url("img/youtube.svg");
    }
    
    .icon-twitter {
      background-image: url("img/twitter.svg");
    }
    
    .icon-facebook {
      background-image: url("img/facebook.svg");
    }
    
    .icon-linkedin {
      background-image: url("img/linkedin.svg");
    }
    
    /*Método 2*/
    
    .icon-youtube {
      background-image: url("img/youtube.svg");
      background-color: red;
    }
    
    .icon-twitter {
      background-image: url("img/twitter.svg");
      background-color: skyblue;
    }
    
    .icon-facebook {
      background-image: url("img/facebook.svg");
      background-color: royalblue;
    }
    
    .icon-linkedin {
      background-image: url("img/linkedin.svg");
      background-color: steelblue;
    }
    
    /*Método 3*/
    
    .icon-youtube {
      background-image: url("img/youtube.svg");
      background-color: red;
      font-size: 64px;
    }
    
    .icon-twitter {
      background-image: url("img/twitter.svg");
      background-color: skyblue;
      font-size: 64px;
    }
    
    .icon-facebook {
      background-image: url("img/facebook.svg");
      background-color: royalblue;
      font-size: 64px;
    }
    
    .icon-linkedin {
      background-image: url("img/linkedin.svg");
      background-color: steelblue;
      font-size: 64px;
    }/*# sourceMappingURL=style.css.map */
    ```
    
- For Loop
    
    For loop nos sass é parecido com o for do JavaScript.
    
    segue abaixo a iteração para mudança de cores em uma lista.
    
    ```scss
    ul {
        width: 800px;
        list-style: none;
        display: table;
        margin: 60px auto;
        font-family: Arial, Helvetica, sans-serif;
        font-size: 16px;
        text-align: center;
        background: #fff;
        border-radius: 8px;
        padding: 32px;
    
        @for $item from 1 to 8 {
            @if $item % 2==0 {
    
                //só ira aplicar em números impares do 1 ao 6
                li:nth-child(#{$item}) {
                    width: 100%;
                    height: 40px;
                    line-height: 40px;
                    background-color: blue;
                    color: #fff;
                }
            }
    
            @for $item from 8 to 12 { //i´ra aplicar vermelho do 8 ao 12
                li:nth-child(#{$item}) {
                    width: 100%;
                    height: 40px;
                    line-height: 40px;
                    background-color: rgb(255, 13, 0);
                    color: #fff;
                    text-shadow: 2px 2px black;
                }
            }
    
            @for $item from 1 to 8 {
                @if $item % 2 !=0 {
    
                    //só ira aplicar em números impares do 1 ao 6
                    li:nth-child(#{$item}) {
                        width: 100%;
                        height: 40px;
                        line-height: 40px;
                        background-color: rgb(47, 255, 0);
                        color: #000000;
                    }
                }
    
            }
        }
    
        li {
            width: 100%;
            height: 40px;
            line-height: 40px;
        }
    }
    ```
    
    Resultado: 
    
    ![Captura de tela_20230208_142311.png](./Captura%20de%20tela_20230208_142311.png)
    
    Além do uso para setar cores, também pode ser usado para gerar nome de classes entre outros.
    
- Content e Media queries

Diferente do css comum, as media queries do sass podem ser criadas dentro de mixins, e colocadas dentro de cada elemento. Ex:

@mixin responsive($screen) {
    @if $screen ==mobile {
        @media screen and (min-width:320px) and (max-width: 767px) {
            @content; //todas as propriedas do scss
        }
    }

    @else if $screen ==tablet {
        @media screen and (min-width:768px) and (max-width: 1199px) {
            @content; //todas as propriedas do scss
        }
    }

    @else if $screen ==desktop {
        @media screen and (min-width:1200px) {
            @content; //todas as propriedas do scss
        }
    }
}

//E então basta aplicar em cada elemento que deseja modificar

ul {
    width: 800px;
    list-style: none;
    display: table;
    margin: 60px auto;
    font-family: Arial, Helvetica, sans-serif;
    font-size: 16px;
    text-align: center;
    background: #fff;
    border-radius: 8px;
    padding: 32px;

    @include responsive (mobile) {
        background-color: greenyellow;
        width: 300px;
    }

    @include responsive (tablet) {
        background-color: #ff00ea;
        width: 700px;
    }

    @include responsive (desktop) {
        background-color: #00ffd9;
        width: 1000px;
    }

}