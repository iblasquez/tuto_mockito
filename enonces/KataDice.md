# Kata dice


*Ce tutoriel reprend le [Kata Designing a Game of Dices](https://github.com/polytechnice-si/3A-GL-DiceGame) de [Sébastien Mosser](https://twitter.com/petitroll) et Simon Urli.*

Ce kata consiste à implémenter **un jeu de dé** à deux joueurs. 
Chaque joueur aura droit à deux lancers.  
Le vainqueur est le joueur qui aura obtenu la plus grande valeur lors d’un de ses lancers.  
En cas d’égalité les joueurs rejouent (en relançant deux fois le dé).   
Après 5 égalités, le jeu se termine sans vainqueur.

Ce kata va être découpé en plusieurs **fonctionnalités** de manière à ce qu’il puisse être implémenté petit pas par petit pas. 

Nous n’adopterons pas une approche TDD (Test Driven Development) durant ce kata (pas de test first).  
Les **tests unitaires** seront écrits **après le code**. Des **doublures de test** pourront être utilisées.  
Une fois les tests écrits, une phase de **refactoring** pourra être envisagée pour améliorer la qualité du code.


### Spécifications fonctionnelles :

Les différentes fonctionnalités à implémenter pour mener à bien ce kata sont donc :

* **[1. Etre capable de lancer un dé](#Fonctionnalite1)**  
***Critère d’acceptation :*** *le dé a 6 faces et retourne un nombre aléatoire entre 1 et 6.*

* **[2. Ajouter un joueur](#Fonctionnalite2)**   
Associer un lancer de dé à un joueur.  
***Critère d’acceptation :*** *un joueur a un nom et expose la valeur obtenue par son propre dé.*

* **[3. Garder la valeur maximale entre de deux lancers](#Fonctionnalite3)**   
Le joueur lance deux fois le dé et la valeur maximale est conservée.  
***Critère d’acceptation :*** *le dé est lancé juste deux fois et seule la valeur maximale est conservée.*


* **[4 .Jouer aux dés : Notre jeu de dés se joue à deux](#Fonctionnalite4)**  
Le vainqueur est celui qui obtient la valeur maximale après avoir lancé ses deux dés.   
En cas d’égalité les joueurs rejouent (en relançant deux fois le dé).   
Après 5 égalités, le jeu se termine sans vainqueur.  
***Critère d’acceptation :*** *le jeu donne le vainqueur en tenant compte des règles précédentes.*


### Mise en place du projet :

[Créer un nouveau projet maven](https://github.com/iblasquez/Back2Basics_Developpement/blob/master/CreerProjetMavenEclipse.md)  que vous appellerez **`gameofdices`**.  
Configurer le **`pom.xml`** comme indiqué [ici](https://github.com/iblasquez/Back2Basics_Developpement/blob/master/CreerProjetMavenEclipse.md) afin d'utiliser les dernières versions de **Java** et **[JUnit](http://junit.org/junit4/)** et ajouter le framework **[Mockito](http://site.mockito.org/)** comme dépendance à ce projet.


 
## Fonctionnalité n°1 : Lancer un dé <a id="Fonctionnalite1"></a>

### 1.a Code de production (`src/main/java`)

Créer une classe **`Dice`** dans le paquetage **`gameofdices`** de **`src/main/java`**.  
Cette classe, responsable de lancer le dé, propose ce service au travers de la méthode **`roll`**.  
Implémentez, dans un premier temps, la classe **`Dice`** de la manière suivante :

```JAVA   

	public class Dice {

		private final static int FACES = 6;
		private Random rand;

		public Dice(Random rand) { this.rand = rand; }

		public int roll() {
			int result = rand.nextInt(FACES) + 1;
			if (result < 1 || result > FACES)
				throw new RuntimeException("Dice returns an incompatible value");
				return result;
			}
		}

```

***Quelques remarques sur ce code :***  

* dans le cadre de ce kata, nous considérons que **seulement un dé à 6 faces** sera utilisé.  
* un objet de type **`Random`** sera fourni à la création du dé pour prendre en compte le côté reproductible du lancer.
* une exception de type **`RuntimeException`** est utilisée. Une **`RuntimeException`** soulève normalement une erreur de programmation. Ici, ce devrait donc plutôt être une classe d'exception personnalisée montrant l’intention métier qui devrait être levée. Comme ce kata se focalise sur les tests et les mocks (plutôt que sur les exceptions), nous nous contenterons pour le moment d'une **`RuntimeException`**, même si cette dernière fait apparaître une certaine dette technique :smile:


### 1.b Code de test (`src/test/java`)

Pour vérifier le critère d’acceptation ***le dé a 6 faces et retourne un nombre aléatoire entre 1 et 6***, plusieurs scénarios de tests doivent être envisagés pour valider ce ***bon*** comportement :
   
* pour tester que la méthode **`roll`** **fonctionne *normalement*** et que chaque lancer de dé renvoie bien une valeur valide entre 1 et 6, la méthode de test suivante peut être implémentée :

```JAVA    

	@Test
	public void rollReturnsAValue() {
		theDice = new Dice(new Random());
		for (int i = 0; i < 100; i++) {
			int result = theDice.roll();
			assertTrue(result >= 1);
			assertTrue(result <= 6);
		}
	}

```


* pour tester que la méthode **`roll`** **est capable de renvoyer une exception** si la valeur tirée au sort se trouve être en dehors de [1-6],  deux méthodes scénarios doivent alors être envisagés :

	* un premier scénario pour tester le bon comportement en cas de **valeur trop grande**

	```JAVA  

		@Test(expected = RuntimeException.class)
		public void identifyBadValuesGreaterThanNumberOfFaces() {
			???
			theDice = new Dice(???);
			theDice.roll();
		}  

	```  

	* un second scénario pour tester le bon comportement en cas de **valeur trop petite**

	```JAVA  

    	@Test(expected = RuntimeException.class)
    	public void identifyBadValuesLesserThanOne() {
        	???
        	theDice = new Dice(???);
        	theDice.roll();
    	}

	```  

	**Que mettre dans les ??? :**  
	Dans ces ceux derniers tests, on ne peut pas se contenter pour l’initialisation du dé (**`???`**) d’un simple tirage aléatoire (objet de type **`Random`**). Il est indispensable de s’assurer que le tirage pourra répondre aux besoins du test c-a-d forcer l’objet aléatoire à retourner une valeur cohérente avec ce que l’on souhaite tester (une valeur >= 7 pour le premier test est une valeur <=-1 pour le second) pour bien pouvoir ***tester*** le déclenchement des exceptions.


#### Travail à faire : 

* Ecrire une classe de test **`DiceTest`** qui reprend l’implémentation de ces trois tests.

```JAVA   

	public class DiceTest {

		Dice theDice;

		@Test
		public void rollReturnsAValue() {
			//…à compléter avec l’implémentation donnée ci-dessus
		}

		@Test(expected = RuntimeException.class)
		public void identifyBadValuesGreaterThanNumberOfFaces() {
			//…à compléter avec l’implémentation donnée ci-dessus
		}

		@Test(expected = RuntimeException.class)
		public void identifyBadValuesLesserThanOne() {
			//…à compléter avec l’implémentation donnée ci-dessus
		}
	}
```  

* Dans les méthodes **`identifyBadValuesGreaterThanNumberOfFaces`** et **`identifyBadValuesLesserThanOne`**, complétez les **`???`** par des doublures de **`Random`** qui bouchonne la méthode **`nextInt`** (pour n’importe quelle valeur entière) avec une valeur propice au déclenchement des exceptions.

* **Compilez et exécutez les tests :** ils doivent passer AU VERT !!! 

<!-- 	

public class DiceTest {

	Dice theDice;

	@Test
	public void rollReturnsAValue() {
		theDice = new Dice(new Random());
		for (int i = 0; i < 100; i++) {
			int result = theDice.roll();
			assertTrue(result >= 1);
			assertTrue(result <= 6);
		}
	}

	@Test(expected = RuntimeException.class)
	public void identifyBadValuesGreaterThanNumberOfFaces() {
		Random tooMuch = mock(Random.class);
		when(tooMuch.nextInt(anyInt())).thenReturn(7);
		theDice = new Dice(tooMuch);
		theDice.roll();
	}

	@Test(expected = RuntimeException.class)
	public void identifyBadValuesLesserThanOne() {
		Random notEnough = mock(Random.class);
		when(notEnough.nextInt(anyInt())).thenReturn(-1);
		theDice = new Dice(notEnough);
		theDice.roll();
	}

}

-->


## Fonctionnalité n°2 : Ajouter un joueur <a id="Fonctionnalite2"></a>

### 2.a Code de production (`src/main/java`)

Cette fonctionnalité vise à faire apparaître un joueur (classe **`Player`**) dans notre implémentation.   
La classe **`Player`** devra, au minimum, être composée :  
-	d’un constructeur avec le **nom** du joueur et le **dé** associé au joueur.  
-	d’un attribut permettant de connaître la dernière valeur mémorisée suite à un lancer  de dé (**`lastValue`**), valeur initialisée par défaut à **-1** (tant que le dé n’a pas encore été lancé).  
-	d’une méthode pour de jouer (**`play`**) permettant de lancer le dé et mémoriser la valeur obtenue suite à ce lancer.


Implémentez dans **`src/main/java`** la classe **`Player`** de la manière suivante :

```JAVA  


	public class Player {

		private String name;
		private Dice dice;
		private int lastValue = -1;

		public Player(String name, Dice dice) {
			this.name = name;
			this.dice = dice;
		}

		public void play() {
			this.lastValue = dice.roll();
		}

		public int getLastValue() {
			return lastValue;
		}

	}  

```



### 2.b Code de test (`src/test/java`)

Pour vérifier le critère d’acceptation : ***un joueur a un nom et expose la valeur obtenue par son propre dé***, deux scénarios doivent être envisagés pour valider l’exposition de la ***bonne*** valeur de dé :

* lors de l’initialisation du joueur, la dernière valeur doit être la valeur par défaut (-1)
* après avoir joué, la dernière valeur doit être différente de la valeur par défaut (c-a-d différente de -1).

 
Tester le code de classe **`Player`** revient donc à écrire la classe **`PlayerTest`** suivante :


```JAVA  

	import java.util.Random;  
	import org.junit.Test;
	import static org.junit.Assert.*;

	public class PlayerTest {

		Player player;

		@Test
		public void lastValueNotInitialized() {
			player = new Player("John Doe", new Dice(new Random()));
			assertEquals(player.getLastValue(), -1);
		}

		@Test
		public void lastValueInitialized() {
			player = new Player("John Doe", new Dice(new Random()));
			player.play();
			assertNotEquals(player.getLastValue(), -1);
		}

	}

```

Implémentez cette classe dans **`src/test/java`**.  
**Compilez et exécutez les tests :** ils doivent passer AU VERT !!!


### 2.c Une petite phase de refactoring ?

Mais au fait, comment a été choisie la valeur **`-1`** ? … de manière arbitraire !  
**`-1`** est donc un [code smell](https://fr.wikipedia.org/wiki/Code_smell) de type [Magic Number](https://fr.wikipedia.org/wiki/Nombre_magique_(programmation)) qui contribue à la dette technique…  
Un petit refactoring s’impose !...

En effet, savez-vous que Java8 a introduit la classe [Optional](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html) qui permet d’encapsuler un objet dont la valeur peut être **définie ou `null`** ?

Pour pouvoir utiliser cette classe assurez-vous que votre **`pom.xml`** soit bien configuré avec (au minimum) la version **`1.8`** de Java :

```XML  

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<maven.compiler.source>1.8</maven.compiler.source>
		<maven.compiler.target>1.8</maven.compiler.target>
	</properties>

```  

Une fois cette configuration effectuée, il est possible d’utiliser la classe [**Optional**](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html) pour supprimer le [**Magic Number**](https://fr.wikipedia.org/wiki/Nombre_magique_(programmation)) **`-1`** et permettre à l’attribut **`lastValue`** de contenir une valeur ou pas…   
Pour ce faire, il suffit de de transformer le type primitif **`int`** en **`Optional<Integer>`**.

Modifiez la classe **`Player`** afin de faire apparaître **`Optional`** :

```JAVA   

	public class Player {

		private String name;
		private Dice dice;
		private Optional<Integer> lastValue = Optional.empty();

		public Player(String name, Dice dice) {
			this.name = name;
			this.dice = dice;
		}

		public void play() {
			this.lastValue = Optional.of(dice.roll());
		}

		public Optional<Integer> getLastValue() {
			return lastValue;
		}

	}  

```

**Compilez ce code.**

**Exécutez les tests :** la barre de tests est désormais AU ROUGE !!!  
Normal, la valeur **`-1`** n’a plus de raison d’être dans les assertions des tests.  
Le but des assertions est de savoir si le dé a déjà été lancé ou pas, c-a-d si une valeur est connue ou pas c-a-d **si une valeur est bien présente ou non dans `lastValue`**.  
D’après la [javadoc d'Optional](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html), c’est la méthode [**`isPresent`**](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html#isPresent--) qui : *Return true if there is a value present, otherwise false.*  

Modifiez la classe **`PlayerTest`** en faisant apparaître **`isPresent`**.  

```JAVA  

	public class PlayerTest {
		Player player;

		@Test
		public void lastValueNotInitialized() {
			player = new Player("John Doe", new Dice(new Random()));
			assertFalse(player.getLastValue().isPresent());
		}

		@Test
		public void lastValueInitialized() {
			player = new Player("John Doe", new Dice(new Random()));
			player.play();
			assertTrue(player.getLastValue().isPresent());
		}
	}   

```

***Exécutez les tests : ils doivent maintenant passer AU VERT !!!***  
Attention à bien avoir remplacé les **`assertEquals`** par des **`assertTrue`** et **`assertFalse`** :smile:


**Remarque :** Pour en savoir un peu plus sur **`Optional`**, vous pouvez, à l’occasion, consulter : 
 
* la [**javadoc sur Optionnal**](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html) sur https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html    
* Cet article [**apprendre à utiliser la classe Optional<T> pour éviter d'utiliser explicitement null**](https://www.developpez.com/actu/134958/Java-apprendre-a-utiliser-la-classe-Optional-lt-T-gt-pour-eviter-d-utiliser-explicitement-null-par-Gugelhupf) ou celui-là [**Optional en Java 8**](http://www.touilleur-express.fr/2014/11/07/optional-en-java-8/) par exemple.



## Fonctionnalité n°3 :  Garder la valeur maximale entre deux lancers <a id="Fonctionnalite3"></a>


### 3.a Code de production (`src/main/java`)

Via cette fonctionnalité, le joueur doit pouvoir lancer deux fois le dé et la valeur maximale doit pouvoir être conservée.  
Pour implémenter ce comportement, il suffit juste de modifier la méthode **`play`** en faisant apparaître 2 appels à **`roll`** et une recherche de valeur maximale.

Modifiez la méthode **`play`** de la classe **`Player`** de la manière suivante :


```JAVA  
	
	public void play() {
		int firstRoll = dice.roll();
		int secondRoll = dice.roll();
		this.lastValue = Optional.of(Math.max(firstRoll, secondRoll));
	}

```  

**Compilez !**


### 3.b Code de test (`src/test/java`)

Pour vérifier le critère d’acceptation ***le dé est lancé juste deux fois et seule la valeur maximale est conservée***, deux scénarios doivent être envisagés : 
 
* le premier devra vérifier que le dé est lancé juste deux fois (**`throwDiceOnlyTwice`**)   
* le second devra vérifier que c’est bien la valeur maximale qui est conservée (**`keepTheMaximum`**)  


#### 3.b.1 Pour vérifier que le dé est lancé juste deux fois(`throwDiceOnlyTwice`),
 
il faut mettre en place une **vérification comportementale** afin de comptabiliser le nombre d’appels à la méthode à la méthode **`roll`** lors de l’exécution de ce scénario de test.  
Ce type de vérification peut facilement être mis en place sur une doublure à l’aide de la méthode **`verify`** du framework [Mockito](http://site.mockito.org/).   
La méthode **`roll`** appartenant à la classe Dice, le scénario devra être écrit à partir d’un mock de **`Dice`**.

Implémentez avec un mock la méthode de test **`throwDiceOnlyTwice`** dans la classe **`PlayerTest`**.

```JAVA   

	@Test
	public void throwDiceOnlyTwice() {
		Dice dice = mock(Dice.class);
		player = new Player("John Doe", dice);
		player.play();
		verify(dice, times(2)).roll();
	}  

```

**Exécutez les tests : **ils doivent passer AU VERT !!! 


#### 3.b.2 Pour vérifier que c’est bien la valeur maximale qui est conservée(`keepTheMaximum`),

il faut mettre en place un **bouchonnage** afin de pouvoir contrôler les valeurs renvoyées par la méthode **`roll`** et s'assurer que seule la valeur maximale est conservée.  
Un tel test pourrait s’écrire de la manière suivante.

```JAVA  

	@Test
	public void keepTheMaximum() {
		Dice dice = mock(Dice.class);
		player = new Player("John Doe", dice);

		when(dice.roll()).thenReturn(2).thenReturn(5);
		player.play();
		assertEquals(player.getLastValue().get(), new Integer(5));

		when(dice.roll()).thenReturn(6).thenReturn(1);
		player.play();
		assertEquals(player.getLastValue().get(), new Integer(6));
	}


```  

**Remarque :** Deux assertions sont écrites dans ce test :  

* la première pour vérifier un scénario où la valeur issue du second lancer (**`5`**) est la valeur maximale.  
* la seconde pour vérifier un scénario où la valeur issue du premier lancer (**`6`**) est la valeur maximale.

Implémentez ce test dans la classe **`PlayerTest`**

**Exécutez les tests :** ils doivent passer AU VERT !!!



## Fonctionnalité n°4 : Jouer aux dés <a id="Fonctionnalite4"></a>

### 4.a Code de production (`src/main/java`)

Il ne reste plus qu’à implémenter le **jeu** de dés (**`Game`**) à proprement parler.  
Rappelons que :  
  
* ce jeu se joue à ***deux joueurs***
* ce jeu ***désigne le vainqueur*** d'une partie en tenant compte des règles suivantes :   
Le vainqueur est celui qui obtient la valeur maximale après avoir lancé ses deux dés.  
En cas d’égalité les joueurs rejouent (en relançant deux fois le dé).  
Après 5 égalités, le jeu se termine sans vainqueur.

Un tel comportement peut être implémentée dans une classe **`Game`** de la manière suivante :

```JAVA   

	import java.util.Optional;

	public class Game {

		private Player left;
		private Player right;

		public Game(Player left, Player right) {
			this.left = left;
			this.right = right;
		}

		public Optional<Player> play() {
			int counter = 0;
			while (counter < 5) {
				left.play();
				int leftValue = left.getLastValue().get();
				right.play();
				int rightValue = right.getLastValue().get();

				if (leftValue > rightValue) {
					return Optional.of(left);
				} else if (rightValue > rightValue) {
					return Optional.of(right);
				}

				counter++;
			}
			return Optional.empty();
		}
	}

```

***Remarque :*** Comme le jeu peut se terminer avec ou sans vainqueur, un **`Optional<Player>`** est utilisé comme type de retour de la méthode **`play`**.

Implémentez la classe **`Game`** dans **`src/main/java`**

**Compilez !** .. et faites en sorte que ce code compile… Il ne reste plus qu’à le tester…



### 4.b Code de test (`src/test/java`)

Pour vérifier le critère d’acceptation : ***le jeu donne le vainqueur en tenant compte des règles***, deux scénarios doivent être envisagés :

* un premier scénario où **un vainqueur peut être désigné**
* un second scénario où, après 5 égalités, il n’y aura **aucun vainqueur**.



#### 4.b.1 Pour vérifier que le jeu est bien capable de désigner un vainqueur, 
il faut mettre en place un **bouchonnage** sur la classe **`Player`** afin de pouvoir contrôler les valeurs renvoyées par la méthode **`getLastValue`** et s’assurer que le ***bon*** joueur est renvoyé.

Ecrire dans **`src/test/main`** une classe **`GameTest`** qui décrit dans le comportement d’un tel test : 

```JAVA   

	import org.junit.Test;
	import static org.junit.Assert.*;
	import static org.mockito.Mockito.*;
	import java.util.Optional;

	public class GameTest {

		Game game;

		@Test
		public void andTheWinnerIs() {

			Player player1 = mock(Player.class);
			when(player1.getLastValue()).thenReturn(Optional.of(new Integer(5)));

			Player player2 = mock(Player.class);
			when(player2.getLastValue()).thenReturn(Optional.of(new Integer(2)));

			game = new Game(player1, player2);
			assertEquals(player1, game.play().get());
		}
	}

```

Après avoir implémenté **`GameTest`**, **exécutez les tests :** ils doivent passer AU VERT !!!


#### 4.b.2 Pour vérifier que le jeu est bien capable de ne désigner aucun vainqueur après avoir pris en compte 5 égalités,

il faut :

* commencer par mettre en place un **bouchonnage** sur la classe **`Dice`** afin que le dé renvoie toujours une seule et même valeur utilisée par le premier ET le second joueur, ce qui revient à ce que les joueurs utilisent le même dé bouchonné :smile: 

```JAVA   

	Dice single = mock(Dice.class);
	when(single.roll()).thenReturn(1);

	Player player1 =  new Player("John", single);
	Player player2 =  new Player("Jane", single);  

```

* s’assurer que chaque joueur va jouer 5 fois c-a-d que la méthode **`play`** va être appelée 5 fois pour chaque joueur (pour simuler les 5 égalités), ce qui revient à mettre en place une vérification comportementale via la méthode **`verify`** sur une doublure de **`Player`**.     
Dans ce test, nous ne souhaitons pas bouchonner (forcer) le comportement d'un joueur, mais ***seulement l’espionner*** pour juste savoir si la méthode **`play`** est bien appelée 5 fois.   
Pour (d)écrire ce scénario, un objet de type **`Spy`** semble donc plus adapté à cette situation qu’un objet de type **`Mock`**, ce qui revient à implémenter la méthode de test de la manière suivante :

```JAVA  

	@Test
	public void noWinnerAfter5Attempts() {
		Dice single = mock(Dice.class);
		when(single.roll()).thenReturn(1);

		Player player1 =  spy(new Player("John", single));
		Player player2 =  spy(new Player("Jane", single));

		game = new Game(player1,player2);
		assertFalse(game.play().isPresent());
		verify(player1, times(5)).play();
		verify(player2, times(5)).play();
	}

```  

Implémentez le test **`noWinnerAfter5Attempts`** dans la classe **`GameTest`**.  
**Exécutez les tests :** ils doivent passer AU VERT !!!


## On en discute ?
Pour les discussions, c'est par [là](https://github.com/iblasquez/tuto_mockito/issues)  
Pour les propositions de contenu, de modification par [ici](https://github.com/iblasquez/tuto_mockito/pulls)  
Et bien sûr, n'hésitez pas à personnaliser vos messages avec des [emojis](http://www.webpagefx.com/tools/emoji-cheat-sheet/) :smile:


