% La programmation fonctionnelle n'est pas juste les fonctions lambda&#160;!
% David Sferruzza
% 27/10/2016


# À propos de moi

- [\@d_sferruzza](https://twitter.com/d\_sferruzza)
- [github.com/dsferruzza](https://github.com/dsferruzza)
- Responsable R&D chez [Startup Palace](http://www.startup-palace.com)
- Doctorant en génie logiciel à l'Université de Nantes

<figure class="stretch"><img src="img/sp.gif" alt=""></figure>


# It all starts with a question

> Qu'est-ce que la programmation fonctionnelle ?

<figure class="stretch"><img src="img/suspicious.gif" alt=""></figure>


# FP is for Functional Programming

> [C'est] un **paradigme** de programmation qui considère le calcul en tant qu'**évaluation** de fonctions mathématiques.

> It is a **declarative** programming paradigm, which means programming is done with **expressions**.

<small>Sources : [Wikipedia FR](https://fr.wikipedia.org/wiki/Programmation_fonctionnelle) / [Wikipedia EN](https://en.wikipedia.org/wiki/Functional_programming)</small>


# Lambda calcul (&#955; calculus)

- Alonzo Church (1930s)
- système formel équivalent à la machine de Turing

<figure class="stretch"><img src="img/gordon.png" alt=""></figure>

<small>Slides sympa : [The Lambda Calculus and The JavaScript](http://fr.slideshare.net/normanrichards/the-lambda-calculus-and-the-javascript)</small>


# Déclaratif vs. imperatif

> **Programmation impérative**: utiliser des instructions qui changent l'état du programme
>
> **Programmation déclarative**: exprimer la logique du programme sans décrire son flot d'exécution

- impératif : instructions de shopping
- déclaratif : liste de shopping


# Pourquoi apprendre FP ?

- prendre du recul sur la programmation
- agrandir **et** consolider sa zone de confort
- réutiliser les concepts intéressants pour faire de **meilleurs programmes**

> Apprendre Haskell permet d'écrire de meilleurs programmes, même dans d'autres langages.

<figure class="stretch"><img src="img/promise.gif" alt=""></figure>


# Concepts

Aujourd'hui on va explorer des concepts vraiment cools issus de la programmation fonctionnelle moderne :

- transparence référentielle
- fonctions d’ordre supérieur
- évaluation paresseuse
- immuabilité


# Fonction impure

```scala
def diviserParDeux(nombre: Double): Double = {
  val missile = lancerUnMissileNucleaire()
  fairePorterLeChapeauAuDrManhattan(missile)
  nombre / 2
}
```

Cette fonction a des **effets de bord** *observables* qu'on ne peut pas deviner en regardant sa valeur de retour.
Il faut donc faire constamment attention lorsqu'on la manipule !

<figure class="stretch"><img src="img/fail.gif" alt=""></figure>


# Fonction pure

```scala
def ajouterCinq(nombre: Double): Double = {
  nombre + 5
}
```

Cette fonction n'a pas d'effets de bord. Elle renvoie **toujours** le même résultat lorsqu'on l'appelle avec les mêmes arguments.

```scala
ajouterCinq(-4) == ajouterCinq(-4)
```

<figure class="stretch"><img src="img/excellent.gif" alt=""></figure>


# Transparence référentielle

> Le résultat du programme ne change pas si on remplace une expression par une expression de valeur équivalente.

Utiliser des fonctions pures permet de **raisonner** sur son programme comme sur une équation.

<figure class="stretch"><img src="img/mind_blown.gif" alt=""></figure>


# Recommandations

## Construire son programme avec un maximum de fonctions pures

La logique métier est fiable, sans surprise et aisément testable.

## Isoler les fonctions qui ont des effets de bord

On sait quelles fonctions ont des effets de bord, ce qui permet d’être prudent lorsqu’on les manipule.


# Concepts

- ~~transparence référentielle~~
- fonctions d’ordre supérieur
- évaluation paresseuse
- immuabilité


# Fonction d'ordre supérieur

En Scala, les fonctions sont des objets de première classe (*first-class citizen*).

Une fonction d’ordre supérieur est une fonction qui possède au moins l'une des propriétés suivantes :

- elle accepte au moins une autre fonction en paramètre
- elle retourne une fonction en résultat

<figure class="stretch"><img src="img/first_class.gif" alt=""></figure>


# Exemple de fonction d'ordre supérieur

```scala
type Nb = Double
```

```scala
def appliquer2Fois(f: Nb => Nb, x: Nb): Nb = {
  f(f(x))
}
// ^ accepte une fonction en paramètre

def foisTrois(x: Nb): Nb = x * 3

appliquer2Fois(foisTrois, 7)
// foisTrois(foisTrois(7))
// foisTrois(21)
// 63
```


# Intérêt des fonctions d'ordre supérieur

- bien séparer les différentes tâches effectuées par nos fonctions
- écrire des fonctions composables, modulables, paramétrables

<figure class="stretch"><img src="img/inception.gif" alt=""></figure>


# Exemple !!

On va voir un exemple concret avec l'API des collections de Scala.

<figure class="stretch"><img src="img/party.gif" alt=""></figure>


# Situation

```scala
case class Ville(
  nom: String,
  dep: Int,
  mer: Boolean)
// On disait que le departement est
// représentable par un entier

type LV = List[Ville]

val villes: LV = List(
  Ville("Nantes", 44, false),
  Ville("Dunkerque", 59, true),
  Ville("Paris", 75, false)
)
```


# Situation

On veut obtenir une liste de chaines de type `ville (dep)`.

On va écrire une fonction `voirVilles` telle que :

```scala
def voirVilles(villes: LV): List[String]

voirVilles(villes)
// --> List("Nantes (44)", "Dunkerque (59)",
//      "Paris (75)")
```


# Solution impérative

Par exemple, en JS :

```javascript
function voirVilles(villes) {
  for (var i = 0; i < villes.length; i++) {
    villes[i] = villes[i].nom +
                  " (" + villes[i].dep + ")";
  }
  return villes;
}
```

On mélange 2 comportements :

- parcourir le tableau
- transformer les données


# List.map

```scala
def voirVilles(villes: LV): List[String] = {

  def transformation(ville: Ville): String = {
    ville.nom + " (" + ville.dep + ")"
  }

  villes.map(transformation)
}
```

- `List.map` gère le parcours du tableau \\o/
- on gère la transformation


# Syntax upgrade

Avec les *lambdas* et les *chaines interpolées* c'est encore plus clair !

```scala
villes.map(v => s"${v.nom} (${v.dep})")
// --> List("Nantes (44)", "Dunkerque (59)",
//      "Paris (75)")
```

<figure class="stretch"><img src="img/reindeer.gif" alt=""></figure>


# List.filter

```scala
villes.filter(v => {
  // Si la ville est près de la mer,
  // on renvoie true, sinon false
  v.mer
})
// --> List(Ville("Dunkerque", 59, true))
```


# Chainons !

```scala
villes
  .filter(v => !v.mer)
  .map(v => s"${v.nom} (${v.dep})")
// --> List("Nantes (44)", "Paris (75)")
```

*Rappel : `List.map` et `List.filter` sont des fonctions d'ordre supérieur.*


# List.foldLeft

```scala
villes
  .foldLeft(List.empty[String])((acc, cur) => {
    val plage = if (cur.mer) {
      List(s"${cur.nom} plage")
    } else {
      List.empty
    }
    acc ++ List(cur.nom) ++ plage
  })
```


# List.foldLeft

```scala
def map[A, B](
  list: List[A],
  tr: (A => B)): List[B] = {

  list.foldLeft(List.empty[B])((acc, cur) => {
    acc ++ List(tr(cur))
  })
}
def filter[A](
  list: List[A],
  pred: (A => Boolean)): List[A] = {

  list.foldLeft(List.empty[A])((acc, cur) => {
    if (pred(cur)) acc ++ List(cur) else acc
  })
}
```


# Recommandations

Explorez l'API des collections !

## Antisèche

Si vous avez une liste (ou autre) et que vous voulez :

- appliquer une transformation sur chacune de ses cases (en conservant leur ordre/nombre) : **map**
- supprimer certaines cases (en conservant l’ordre et le contenu des autres) : **filter**
- le parcourir pour construire une nouvelle structure de données : **fold/reduce**


# Concepts

- ~~transparence référentielle~~
- ~~fonctions d’ordre supérieur~~
- évaluation paresseuse
- immuabilité


# Stratégie d'évaluation

- **quand** évaluer les arguments d'un appel de fonction
- **quel type de valeur** passer à la fonction

```scala
maFonction(lower("WHATEVER"))
//         ^ Quand évaluer ça ?
// Que transmettre au corps de maFonction ?
```

<figure class="stretch"><img src="img/bean.gif" alt=""></figure>


# Évaluation stricte

*strict evaluation, eager evaluation, greedy evaluation*

- **quand :** dès que l'expression peut être liée à une variable
- **quel type de valeurs :**
	- *call by value*
	- *call by reference*
	- *call by sharing*
	- ...


# Évaluation non stricte

*non-strict evaluation, lazy evaluation*

- **call by name :** les arguments sont substitués dans le corps de la fonction
- **call by need :** idem, avec *mémoïsation* (≈ mise en cache du résultat de l'évaluation des arguments)
- ...


# Évaluation paresseuse

L'exécution d'un bout de code ne se fait pas avant que les résultats de ce bout de code ne soient réellement nécessaires.

<figure class="stretch"><img src="img/clean.gif" alt=""></figure>


# À quoi ça sert ?

- **optimisation :** on peut éviter des calculs inutiles
- **maintenabilité :**
	- on peut exprimer des structures de données infinies
	- on peut définir des structures de contrôle comme des abstractions, au lieu de primitives

```scala
val a      = { println("a"); 1 + 1 }
lazy val b = { println("b"); 1 + 1 }
def c      = { println("c"); 1 + 1 }
```

- `a` calculé immédiatement et stocké
- `b` calculé au 1<sup>er</sup> appel et stocké
- `c` calculé à chaque appel


# Exemple

```scala
val l = List(0, 1, 2, 3, 4)

def plusUn(nb: Int): Int = {
  println(s"$nb + 1")
  if (nb > 2) println("Traitement long")
  nb + 1
}

def petit(nb: Int): Boolean = {
  println(s"$nb plus petit que 3 ?")
  nb < 3
}
```


# Version stricte

```scala
l.map(plusUn).filter(petit).take(2)
```

```text
0 + 1
1 + 1
2 + 1
3 + 1
Traitement long
4 + 1
Traitement long
1 plus petit que 3 ?
2 plus petit que 3 ?
3 plus petit que 3 ?
4 plus petit que 3 ?
5 plus petit que 3 ?
res0: List[Int] = List(1, 2)
```


# Version paresseuse

```scala
Stream(l: _*)
  .map(plusUn)
  .filter(petit)
  .take(2)
  .toList
```

```text
0 + 1
1 plus petit que 3 ?
1 + 1
2 plus petit que 3 ?
res0: List[Int] = List(1, 2)
```


# Version stricte

<figure class="stretch"><img src="img/without_lodash.gif" alt=""></figure>


# Version paresseuse

<figure class="stretch"><img src="img/with_lodash.gif" alt=""></figure>


# Évaluation paresseuse

- séparation
  - du Calcul, de la **génération**<br>*→ où le calcul d'une valeur est-il défini ?*
  - du Contrôle, de la **condition d'arrêt**<br>*→ où le calcul d'une valeur se produit-il ?*
- *colle* qui permet d'assembler efficacement des (bouts de) programmes : facilite l'approche *diviser pour régner*

**Avantages :** peut augmenter la maintenabilité *et* les performances

**Inconvénients :** peut introduire de l'*overhead* (dépend pas mal de la techno)


# Concepts

- ~~transparence référentielle~~
- ~~fonctions d’ordre supérieur~~
- ~~évaluation paresseuse~~
- immuabilité


# Immuabilité

> Un objet immuable est un objet dont l'état ne peut pas être modifié après sa création.

<figure class="stretch"><img src="img/good.gif" alt=""></figure>


# Constantes

```scala
val a = 1
a = 2 // error: reassignment to val
```

Par défaut, on utilise `val` et donc on ne peux pas réassigner une nouvelle valeur.

```scala
var a = 1
a = 2 // ok
```

On peut utiliser `var` pour faire ça.
Mais c'est **dangereux** : ça complique le raisonnement sur code.

> En pratique, c'est très rare d'en avoir besoin.


# Objets muables

```scala
case class Personne(var nom: String, ville: String)

val p = Personne("Batman", "Gotham")
println(p)
// --> Personne("Batman", "Gotham")

p = Personne("Robin", "Gotham")
// error: reassignment to val

p.nom = "Robin"
println(p)
// --> Personne("Robin", "Gotham")
```


# Objets immuables

```scala
case class Personne(nom: String, ville: String)

val p1 = Personne("Batman", "Gotham")
println(p1)
// --> Personne("Batman", "Gotham")

val p2 = p1.copy(nom = "Robin")
println(p2)
// --> Personne("Robin", "Gotham")
println(p1)
// --> Personne("Batman", "Gotham")
```


# Avantages

- lisibilité/maintenabilité : 1 référence pour 1 valeur
- pas d'effets de bord
- *thread safe*

Et les perfs ?

<figure class="stretch"><img src="img/suspicious2.gif" alt=""></figure>


# Performances

Introduit de l'*overhead*, mais souvent le compromis maintenabilité/performances est bon

<figure class="stretch"><img src="img/tree.png" alt=""></figure>


# Concepts

- ~~transparence référentielle~~
- ~~fonctions d’ordre supérieur~~
- ~~évaluation paresseuse~~
- ~~immuabilité~~

<figure class="stretch"><img src="img/drop.gif" alt=""></figure>


# Ressources

- [Functional Programming in Scala](https://www.manning.com/books/functionaal-programming-in-scala) : super livre sur FP & Scala
- [Learn You A Haskell For Great Good](http://learnyouahaskell.com/) : livre très accessible pour débuter Haskell ([traduction FR non officielle](http://lyah.haskell.fr/))
- [Why Functional Programming Matters](http://www.cs.kent.ac.uk/people/staff/dat/miranda/whyfp90.pdf) : pourquoi les fonctions d'ordre supérieur et l'évaluation paresseuse sont parmi les meilleurs outils pour écrire des programmes modulaires


# Ressources

- [John Hughes on Why Functional Programming Matters!](http://www.infoq.com/interviews/john-hughes-fp)
- [Is your programming language unreasonable?](http://fsharpforfunandprofit.com/posts/is-your-language-unreasonable/) *or, why predictability is important*


# Questions ?

<figure class="stretch"><img src="img/question.gif" alt=""></figure>

Twitter : [\@d_sferruzza](https://twitter.com/d\_sferruzza)

Slides sur GitHub :

[dsferruzza/conf-programmation-fonctionnelle-en-scala](http://github.com/dsferruzza/conf-programmation-fonctionnelle-en-scala)
