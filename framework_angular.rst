**************
Les Frameworks
**************

Les frameworks Javascript sont de manière très grossière des librairies
de méthodes permettant de faciliter la programmation d'applications Web.

Il existe bien d'autres frameworks web avec d'autres types d'architecture
et d'applications mais ce n'est pas le propos, GIYF comme on dit.

Nous avons choisi de travailler sur AngularJS, toutefois il existe d'autres
frameworks également connus tels que Backbone ou Ember.

*******
Angular
*******

Créé en 2009, AngularJS est un framework MVC (Model View Controller). Cela
signifie qu'il faut passer par une architecture avec des contrôleurs ainsi
que des directives.


Les imports
===========

Pour faire appel aux librairies d'AngularJS deux solutions :

* Vous pouvez directement aller chercher les fichiers .js qui vous intéresse depuis le site ajax.googleapis.com

.. code-block:: html

  <!-- la base -->
  <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
  <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular-resource.min.js"></script>
  <!-- pour appeler ngRoute (voir plus bas dans le tuto) -->
  <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular-route.js"></script>

* Il existe sinon des moyens de télécharger ces fichiers et de changer la src en un path vers là où vous les avez rangés.

Le concept d'application
========================

Dans Angular une application est une entité qui englobe des contrôleurs et
des directives.

On crée l'application dans le fichier JS

.. code-block:: js

  var app = angular.module('myApp',[]);

Le deuxième paramètre de la fonction contiendra les modules non-inclus dans
la librairie de base, à conditions de bien les avoir importés.

Nous avons plusieurs cas de figure pour son utilisation dans le fichier html :

* L'application est déclarée dans la balise html :

.. code-block:: html

  <html ng-app="myApp">
    .... //Votre code utilisant les contrôleurs et les directives
  </html>

* L'application est déclarée dans la balise body :

.. code-block:: html

  <body ng-app="myApp">
    .... //Votre code utilisant les contrôleurs et les directives
  </body>

* Ou bien on peut la déclarer dans un div et ainsi avoir plusieurs applications pour un même fichier html :

.. code-block:: html

  <body>
    <div ng-app="myApp1">
      .... //Votre code utilisant les contrôleurs et les directives de myApp1
    <div>
    <div ng-app="myApp2">
      .... //Votre code utilisant les contrôleurs et les directives de myApp2
    <div>
  </body>

Les contrôleurs
===============

Incontournables dans l'utilisation d'Angular, c'est dans eux que vous écrirez
le code, le vrai.

.. code-block:: js

  var app = angular.module('myApp',[]);
  // on ajoute le contrôleur myController à l'application myApp
  app.controller('myController', function($scope) {
    // code
  });

Nous allons ensuite utiliser le "scope" pour manipuler les variables et les
fonctions du contrôleur.

Par exemple pour faire un "Hello world", nous allons procéder de la sorte :

.. code-block:: js

  app.controller('myController', function($scope) {
    // on crée une variable message qui reçoit notre Hello world
    $scope.message = "Hello world";
  });

Puis dans le html on utilise l'application myApp, le contrôleur myController
ainsi que la varible message :

.. code-block:: html

  <body ng-app="myApp">
    <div ng-controller="myController">
      <p>{{ message }}</p>
    </div>
  </body>

Ainsi les deux accolades vont aller chercher dans le code du contrôleur la
variable message et afficher sa valeur.

Les directives
==============

Moins essentiels que les contrôleurs, ce sont des attributs HTML qui permettent
de rajouter des modules au code.

La plupart (celles de base) sont précédées d'un "ng". Il en existe beaucoup de
déjà existante donc pas besoin de réinventer la roue.

Par exemple si l'on veut que dans un "input", l'envoi des infos se passe à
l'appui de la touche Entrée, nous créons une directive myEnter :

.. code-block:: js

  app.directive('myEnter', function () {
      return function (scope, element, attrs) {
          // element.bind avec "keydown keypress" assigne l'appui d'une touche à une action
          element.bind("keydown keypress", function (event) {
              // 13 est la valeur de la touche Entrée
              if(event.which === 13) {
                  scope.$apply(function (){
                      // on va déclencher l'événement my-enter
                      scope.$eval(attrs.myEnter);
                  });

                  event.preventDefault();
              }
          });
      };
  });

Puis nous l'utilisont dans l'input qui nous intéresse dans le fichier html :

.. code-block:: html

  <input ng-model="value" my-enter="doSomething()"></input>

"value" est la variable contenant ce qu'on met dans l'input et
"doSomething" est l'événement déclenché par l'appui de la touche Entrée.

Et il ne restera plus qu'à créer dans le contrôleur :

.. code-block:: js

  app.controller("myController", function($scope) {
    $scope.doSomething = function () {
      // faire quelque chose avec $scope.value
    }
  });



**************
Centrale Memes
**************

Pour vous donnez une idée de l'utilité d'AngularJS lorsqu'il s'agit d'utiliser
une quantité importante de code front end, nous allons appliquer ces méthodes
en créant un site sur les memes centraliens.

Nous nous sommes inspirés principalement d'Imgur et de Reddit, mais beaucoup
d'autres sites sont similaires à ceux_ci.

Changement de page par système de routing
=========================================

Cette fonctionnalité permet de passer d'une page à l'autre sans recharger
les parties communes aux deux pages. (dans la mesure du possible)

Lors d'un changement de page, nous garderons la bannière ainsi que le menu. De
cette manière dans les liens du menu nous mettrons des hrefs de la sorte :

.. code-block:: html

  <nav>
      <ul id="menu">
        <li><a href="#/!">Accueil</a></li>
        <li><a href="#!top">Top</a></li>
        <li><a href="#!flop">Flop</a></li>
        <li><a href="#!submit">Soumettre</a></li>
      </ul>
  </nav>

Puis nous appelons la page grâce à la directive ng-view qui n'a pas besoin
d'être modifiée pour l'occasion.

.. code-block:: html

  <div ng-view id="contenu" style="margin-bottom:50px;"></div>

Notez l'utilisation des points d'exclamation.

Ensuite nous allons utiliser le module ngRoute, attention cependant à ne pas
oublier de le mettre dans les imports tout en haut. En créant l'application
myApp il faut donc rajouter en deuxième paramètre le module :

.. code-block:: html

  var app = angular.module("myApp", ["ngRoute"]);

Ensuite on configure le routeur de l'application en chargeant des fichiers
ne contenant que les parties spécifiques à la page sélectionnée. Nous
utilisons ici des "sous-fichiers" .html mais on peut très bien envisager
d'utiliser d'autres types de fichiers tels que .htm .

.. code-block:: html

  app.config(function($routeProvider) {
    $routeProvider
    .when("/", {
      templateUrl : "recent.html",
      controller : "RecentController"
    })
    .when("/top", {
      templateUrl : "top.html",
      controller : "TopController"
    })
    .when("/flop", {
      templateUrl : "flop.html",
      controller : "FlopController"
    })
    .when("/submit", {
      templateUrl : "submit.html",
      controller : "RecentController"
    });
  });

Notez qu'en plus de l'url, on peut facultativement rattacher chaque page à
un contrôleur.

Ainsi nous n'aurons pas à recharger les fondations du site quand nous
changerons de page et ça, comme dirait Zinédine, "C'est bieng !".

Bannière "Carousel"
===================

Pour faire un carousel avec angular nous avons utilisé un fichier javascript
qui a une utilisation relativement simple :

Source : https://angularscript.com/simple-generic-angularjs-content-carousel/

Nous commençons tout d'abord par des imports :

.. code-block:: html

  <script src="http://hammerjs.github.io/dist/hammer.min.js"></script>
  <script src="angular-carousel.js"></script>

Dans le fichier "angular-carousel.js" l'objet Carousel est créé de la sorte :

.. code-block:: js

  angular.module('angular-carousel', [])
  .factory('Carousel', function() {
      var Carousel = {};
      //stuff
  };

Ce qui nous permet une fois le fichier .js importé de pouvoir directement
utilisé Carousel dans notre script, à condition d'avoir appelé le module
"angular-carousel" lors de la création de notre application.

.. code-block:: js

  var app = angular.module("myApp", ['angular-carousel']);
  app.controller("CarouselController", function($scope,Carousel){
    $scope.Carousel = Carousel;
    $scope.slides = ["banniere1.jpg","banniere2.jpg","banniere3.jpg"];
  });

Nous pouvons ensuite utiliser ng-carousel dans notre html avec ses attributs
pour pouvoir utiliser les fonctions présentes dans le .js importé :

.. code-block:: html

  <div ng-controller="CarouselController">
    <div ng-carousel ng-carousel-name="example-carousel4" ng-carousel-timer="4000">
      <slidecontainer>
        <slide ng-repeat="image in slides">
          <img id="carousel" src="{{image}}"/>
        </slide>
      </slidecontainer>
    </div>
  </div>

Parallax
========

Nous avons décidé de créer un parallax, qui consiste en une image qui va défiler
à une vitesse différente de celle à laquelle on scroll la page, ou un bloc.

En l'occurence, nous avons voulu mettre le fond en parallax lorsque nous descendons la
page. Pour ce faire, nous avons tout d'abord créé un JS qui crée le module
permettant la fonctionnalité en question.

On utilise une directive.  En effet, cette dernière permet d’utiliser le DOM.

Restrict A signifie que la méthode d’accès à l’élément est un attribut (pratique pour la
compatibilité avec d’anciennes versions d’IE notamment). Scope permet de créer un
scope isolé, qui nous permet de récupérer les données de la page (imite le DOM). Link,
quant à lui, permet ici d’utiliser ces données afin de faire des calculs (pour créer le
parallax) et ainsi de modifier la page (en l’occurrence notre image qui sera en parallax).

.. code-block:: js

    app.directive('perfectParallax', [
      '$window', function ($window) {

          return {
            restrict: 'A',
            scope: {
              parallaxCss: '@',
              parallaxInitVal: '@',
              parallaxRatio: '@'
            },
            link: function(iScope, iElem, iAttr) {
              var cssKey,
                cssValue,
                isSpecialVal,
                parallaxCssVal,
                parallaxOffset,
                parallaxRatio,
                parallaxInitVal,
                cssValArray;

              parallaxCssVal = iScope.parallaxCss ? iScope.parallaxCss : 'top';
              cssValArray = parallaxCssVal.split(':');
              cssKey = cssValArray[0];
              cssValue = cssValArray[1];

              isSpecialVal = cssValue ? true : false;
              if (!cssValue) cssValue = cssKey;

              parallaxRatio = iScope.parallaxRatio ? +iScope.parallaxRatio : 1.1;
              parallaxInitVal = iScope.parallaxInitVal ? +iScope.parallaxInitVal : 0;

              iElem.css(cssKey, parallaxInitVal + 'px');

              function _onScroll() {
                var resultVal;
                var calcVal = $window.pageYOffset * parallaxRatio + parallaxInitVal;

                if (isSpecialVal) {
                  resultVal = '' + cssValue + '(' + calcVal + 'px)';
                } else {
                  resultVal = calcVal + 'px';
                }
                iElem.css(cssKey, resultVal);
              };

              $window.addEventListener('scroll', _onScroll);

            }
          };
        }
      ]);

Ensuite, nous avons implémenté un CSS de l'image qui allait servir, en prenant
garde à mettre un z-index inférieur au reste afin de garder l'image dans le fond.

.. code-block:: css

    .parallax
    {
         background: url(fond2.jpg) repeat-y;
         width:1366px;
         height:768px;
         position: relative;
         z-index: -100;
    }

Puis nous avons incorporé cela au code html, en enveloppant le conteneur.

.. code-block:: html

    <div perfect-parallax parallax-ratio="0.1" parallax-css="transform:translateY">
    <div perfect-parallax parallax-ratio="0.5" parallax-css="background-position-y" parallax-init-val="-100" class="parallax">
    </div></div>

Ce qui nous permet d'obtenir un joli parallax !

Des boucles et des images
=========================

Pour éviter que nos fichiers html ne soient trop longs, nous allons
utiliser des boucles grâce à Angular pour parcourir des listes d'images
et alléger les sous-fichiers.

Dans le contrôleur qui a été rattaché à la page top.html, nous mettons
une liste images contenant les noms des images :

.. code-block:: js

  app.controller("TopController", function($scope) {
    $scope.images = ["top1.jpg","top2.jpg","top3.jpg","top4.jpg","top5.jpg"];
  });

Ainsi dans top.html nous n'avons plus qu'à mettre une boucle grâce à ng-repeat
afin de parcourir la liste.

image est l'index de la boucle (on aurait pu l'appeler toto) et images est bien
l'attribut du contrôleur car on a appelé top.html avec le contrôleur TopController.

.. code-block:: html

  <div ng-repeat="image in images">
    <img src="memes/{{image}}"></img>
  </div>

Une fois de plus les accolades permettent d'utiliser les variables du contrôleur
dans le fichier html.

L'avantage de procéder de la sorte est que l'on peut utiliser le même code pour
les deux autres pages recent.html et flop.html en gardant le même nom de liste
dans le contrôleur attaché à la page. Il suffit juste de modifier les noms de
fichiers dans la liste.

Système de tags
===============

En nous inspirant de celui fait sur le site de partage d'images Imgur, nous
allons mettre un système dynamique de tags.

Tout d'abord on crée un contrôleur contenant la liste de tags et nous les
affichons dans notre html (dans la boucle d'affichage d'images) :

.. code-block:: js

  app.controller("TagsController", function($scope) {
    $scope.tags = ["True Story"];
  });

Pour l'instant il ne contient que le tag "True Story" mais nous allons y
remédier bien assez tôt.

.. code-block:: html

  <div ng-repeat="image in images" ng-controller="TagsController">
    <h3>Posté en janvier 2018 par Anonyme</h3>
    <ul id="tags">
      <li ng-repeat="x in tags">{{x}}</li>
    </ul>
    <img src="memes/{{image}}"></img>
  </div>

Afin que les tags soient à la suite on utilise inline-block dans le css :

.. code-block:: css

  ul#tags li
  {
  	display:inline-block;
  }

On fera de même pour les autres éléments relatifs aux tags.

Maintenant nous allons rajouter une fonction pour ajouter de nouveaux tags.

.. code-block:: js

  $scope.addItem = function () {
    $scope.errortext = "";
    if (!$scope.addMe) {return;} // rien ne se passe si addMe est vide
    if ($scope.tags.indexOf($scope.addMe) == -1) {
        // -1 veut dire qu'addMe n'est pas déjà dans la liste
        $scope.tags.push($scope.addMe);
        $scope.addMe = null;
    } else {
        $scope.errortext = "Le tag est déjà dans la liste.";
    }
  }

.. code-block:: html

  <ul id="tags">
    <li ng-repeat="x in tags">{{x}}</li>
    <input id="newtag" ng-model="addMe" my-enter="addItem()"></input>
    <p>{{errortext}}</p>
  </ul>

Notez que l'on utilise ici my-enter grâce à la directive suivante quand l'on
a déjà expliqué plus en détails précédemment :

.. code-block:: js

  app.directive('myEnter', function () {
      return function (scope, element, attrs) {
          // element.bind avec "keydown keypress" assigne l'appui d'une touche à une action
          element.bind("keydown keypress", function (event) {
              // 13 est la valeur de la touche Entrée
              if(event.which === 13) {
                  scope.$apply(function (){
                      // on va déclencher l'événement my-enter
                      scope.$eval(attrs.myEnter);
                  });

                  event.preventDefault();
              }
          });
      };
  });

L'input a la valeur addMe et lorsque l'utilisateur utilise la touche Entrée,
la valeur addMe est rajouté à la liste tags grâce à la fonction addItem, puis
on passe addMe à zéro.

Maintenant il ne reste plus qu'à rajouter une croix pour pouvoir supprimer un
tag et le tour est joué :

.. code-block:: js

  $scope.removeItem = function (x) {
      $scope.errortext = "";
      $scope.tags.splice(x, 1);
  }

Cette fonction est appelé à l'appui d'un span :

.. code-block:: html

  <ul id="tags">
    <li ng-repeat="x in tags">
      {{x}}
      <span ng-click="removeItem($index)"><a>X</a></span>
    </li>
    <input id="newtag" ng-model="addMe" my-enter="addItem()"></input>
    <p>{{errortext}}</p>
  </ul>

Nous avons à présent un joli système de tags. Même si le tout est englobé
dans un ng-repeat pour l'affichage des images, chaque liste tags est
indépendante et une modification sur l'une n'aura pas d'effets sur l'autre.


Sources :

w3schools.com (https://www.w3schools.com/angular/)

Anthony Sendra pour les explications(https://blog.kaliop.com/blog/2014/01/13/angularjs-presentation/)

Parallax (https://angularscript.com/pure-angularjs-parallax-scrolling-effect-perfectparallax/)

Carousel (https://angularscript.com/simple-generic-angularjs-content-carousel/)
