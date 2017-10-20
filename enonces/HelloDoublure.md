# Premiers pas avec les doublures dans Mockito


Dans ce premier exercice, vous allez écrire vos premières doublures Java générées à l’aide du framework Mockito.   
Les exemples à implémenter sont ceux qui illustrent le [cours Doublures de tests]().


Commencez par [créer un projet maven](https://github.com/iblasquez/Back2Basics_Developpement/blob/master/CreerProjetMavenEclipse.md) que vous appellerez **`hellodoublure`**.  
Pour pouvoir générer des doublures, **ajoutez une dépendance vers le [framework Mockito](http://mockito.org/)** à votre **`pom.xml`**.  
Pour cela, ajoutez la dépendance **`mockito-core`** dans le bloc **`<dependencies>…</dependencies>`** de votre **`pom.xml`**


```XML  

		<dependency>
			<groupId>org.mockito</groupId>
			<artifactId>mockito-core</artifactId>
			<version>2.10.0</version>
			<scope>test</scope>
		</dependency>
```

Pour connaître la **`version en cours`**, rendez-vous sur le [site de Mockito](http://mockito.org/) et relevez le numéro de version indiquée en bleu à côté de Maven Central : ![Wizard pour créer un projet maven](https://maven-badges.herokuapp.com/maven-central/org.mockito/mockito-core/badge.svg)  
N'oubliez pas de remettre à jour votre **`pom.xml`**!!!

Vous allez maintenant faire vos premiers pas avec :

* [Stub (bouchon)](#stub)
* [Mock (objet Factice](#mock)


## 1. Le Stub (bouchon) <a id="stub"></a>

Le ***stub*** est une doublure qui fait l’objet d’une implémentation minimale.  
Son implémentation tient compte du contexte exact dans lequel la doublure sera utilisée.  
Le stub a ainsi pour but de fournir des réponses prédéfinies lorsque l’objet sous test (**SUT** : **S**ystem **U**nder **T**est) a besoin de ses services.


### Le contexte (le code source disponible)
 
Imaginez que vous êtes chargés de développer la partie concernant la ***messagerie*** d'un projet et qu’un autre développeur est chargé de développer les parties ***authentification*** et ***utilisateur*** de ce même projet.  
Le travail de votre collègue va, entre autres, consister à fournir une implémentation de la classe **`User`** qui exposera un service de type **`getLogin`** (que vous allez devoir utiliser pour implémenter la messagerie). Vous vous mettez d’accord sur la signature de cette méthode qui pour l’instant sera écrite de la manière la plus simple possible c-a-d sous la forme : **`public Object getLogin()`**.


Dans **`src/test/java`** (dans le paquetage **`fr.unilim.iut`**), créez et implémentez la classe **`User`** de la manière suivante :

```JAVA 

public class User {

	public Object getLogin() {
		// TODO Auto-generated method stub
		// Rien d’implémenter pour l’instant : 
		// votre collègue est en train de l’implémenter ;-)
		return null;
	}
}

```


#### Le problème
Au cours de votre développement, vous devez écrire un test qui fera appel à la méthode **`getLogin`** (qui n'existe pas encore réellement). 


#### La solution 
Pour simuler cela, créez et implémentez dans **`src/test/java`**  (dans le paquetage **`fr.unilim.iut`**), la classe **`TestDoublures`** et la méthode **`test_unPremierBouchon`** de la manière suivante :


```JAVA  

   import org.junit.Test;  

   public class TestDoublures { 
	
	@Test
	public void test_UnPremierStub() {

		User user = mock(User.class);
		when(user.getLogin()).thenReturn("alice");
		}
    }

```


Les deux lignes de code de ce test correspondent :

- à la **création de la doublure** qui se fait par un appel à la méthode statique **`mock`**.  
Rappelons qu’il est également possible de créer une doublure via l'annotation **`@Mock`**

- au **bouchonnage de la doublure** à l'aide de la méthode statique **`when`**.   
Le bouchonnage permet de spécifier le comportement que devra adopter la doublure lorsque la
méthode bouchonnée. Dans notre test :
**QUAND `getLogin`** est appelé sur la doublure **`user`** **ALORS** la doublure **RENVOIE** **"alice"**.

**Attention !!!** Notez bien que les méthodes **`mock`** et **`when`** sont des méthodes statiques comme l’indique la [javadoc du framework Mockito](https://static.javadoc.io/org.mockito/mockito-core/2.10.0/org/mockito/Mockito.html) et donc que ce code ne pourra compiler qu’après l’ajout des **`import static`** suivants :

```JAVA

   import static org.mockito.Mockito.mock;  
   import static org.mockito.Mockito.when;

```

**Compilez ce code !!!**  
Vous pouvez exécuter ce code (c-a-d lancer le test), mais comme pour l’instant il n’y a aucune étape d’assertion, cela ne sert pas à grand-chose.  
En effet, si vous lancez ce code de test, le test sera forcément VERT !!!


#### Utilisation du bouchonnage 

A partir de maintenant, on peut donc utiliser **`getLogin`** n'importe où dans la suite du test.  
A chaque fois que **`getLogin`** sera appelé, il renverra **"alice"**.  
Pour illustrer ce fait, ajoutez après le bouchonnage la ligne de code suivante :  
**`System.out.println(user.getLogin());`**  

**Compilez et exécutez !**  
Vous devriez voir s'afficher **`alice`** dans la console ….

Pour vérifier ce « bon » comportement, nous allons maintenant faire appel à **`getLogin`** dans l’étape d’assertion du test.  
En fin de test, ajoutez l’étape d’***Assertion*** suivante :  **`assertEquals(user.getLogin(), "alice");`**  
en n’oubliant pas d’importer :  **`import static org.junit.Assert.assertEquals;`**

**Compilez et exécutez !**  
Le test doit passer au VERT !!!

Suite à cette assertion, ajouter l’assertion suivante : **`assertEquals(user.getLogin(), "bob");`**    
**Compilez et exécutez !**  
La barre de tests est désormais AU ROUGE en raison de cette seconde assertion.  
Le test est en échec car la valeur attendue est **`bob`**  et c’est toujours **`alice`**  qui est renvoyée par **`getLogin`** !

Commentez ou supprimez cette dernière assertion qui fait échouer le test.  
**Relancez le test pour continuer sur une barre VERTE !!!**

**Remarque :** Dans cet exemple, nous avons utilisé la méthode bouchonnée dans l'étape d'assertion car nous voulions illustrer le « bon » comportement du bouchon… Dès lors qu'une méthode est bouchonnée, elle va pouvoir être utilisée n’importe où dans le test (et notamment dans l'étape **A**rrange du pattern A*A*A) dès que l’objet sous test aura besoin du service bouchonné pour implémenter correctement le comportement à tester …

## 2. Le Mock (objet Factice) <a id="mock"></a>

Du stub au mock, il n’y a qu’un pas puisqu'un mock permet en plus de bouchonner un comportement, de mettre en place une **vérification comportementale** c-a-d de vérifier quelle fonction a été appelée, avec quel(s) argument(s), combien de fois …. 



Dans la classe **`TestDoublures`**, ajoutez la méthode de test suivante :

```JAVA  

    @Test
	public void test_UnPremierMock() {

		User user = mock(User.class);
		when(user.getLogin()).thenReturn("alice");

        System.out.println(user.getLogin());
	}

```



Pour l'instant, tout comme un stub, la doublure est créée (**`mock`**), bouchonnée (**`when`**) et utilisée dans le test (utilisation simulée par un affichage).




- En fin de test, ajoutez l’instruction suivante  : **`verify(user).getLogin();`**  
Cette instruction permet de vérifier que la méthode **`getLogin`** a bien été appelée (1 fois) sur l'objet **`user`** :   
elle permet donc de mettre en place une vérification comportementale.

	**Compilez et exécutez le test !**  
Le test doit passer au VERT !!!

- Commentez l'instruction **`System.out.println`**

	**Compilez et exécutez le test !**  
	Le test passe AU ROUGE !!!  
	Et le message d’erreur indique : **`Wanted but not invoked : user.getLogin()`**

	Décommentez l’instruction **`System.out.println`**  
	**Compilez et exécutez le test !**  
	Le test doit repasser au VERT !!!


- Transformez l’instruction : **`verify(user).getLogin();`** en **`verify(user, times(2)).getLogin();`** en n'oubliant pas l’**`import static org.mockito.Mockito.times;`**

	**Compilez et exécutez le test !**  
	Le test doit être ROUGE !!!

	Ajoutez une nouvelle instruction **`System.out.println(user.getLogin());`**

	**Compilez et exécutez le test !**  
	Le test doit être VERT maintenant !!!


Ces exemple montrent que le mock est un objet simulé dont le comportement est décrit spécifiquement pour un test unitaire dans un test unitaire.
Avec un mock, l’assertion du test unitaire est réalisée, comme nous venons de le montrer, via un appel à **`verify`** 


Pour tester les différentes options de vérification comportementale possible qu’offre la méthode verifiy, vous pouvez terminer cet exercice en implémentant et testant l’exemple suivant 
(extrait de la rubrique **Verifying exact number of invocations / at least x / never** de la javadoc : [https://static.javadoc.io/org.mockito/mockito-core/2.10.0/org/mockito/Mockito.html#at_least_verification](https://static.javadoc.io/org.mockito/mockito-core/2.10.0/org/mockito/Mockito.html#at_least_verification))


```JAVA    

    @Test
	public void test_OptionsVerification() {

		LinkedList<String> mockedList = mock(LinkedList.class);

		 mockedList.add("once");

		 mockedList.add("twice");
		 mockedList.add("twice");

		 mockedList.add("three times");
		 mockedList.add("three times");
		 mockedList.add("three times");

		//following two verifications work exactly the same - times(1) is used by default
		 verify(mockedList).add("once");
		 verify(mockedList, times(1)).add("once");

		 //exact number of invocations verification
		 verify(mockedList, times(2)).add("twice");
		 verify(mockedList, times(3)).add("three times");

		 //verification using never(). never() is an alias to times(0)
		 verify(mockedList, never()).add("never happened");

		 //verification using atLeast()/atMost()
		 verify(mockedList, atLeastOnce()).add("three times");
		 verify(mockedList, atLeast(2)).add("three times");
		 verify(mockedList, atMost(5)).add("three times");
	}


```

## On en discute ?
Pour les discussions, c'est par [là](https://github.com/iblasquez/tuto_mockito/issues)  
Pour les propositions de contenu, de modification par [ici](https://github.com/iblasquez/tuto_mockito/pulls)  
Et bien sûr, n'hésitez pas à personnaliser vos messages avec des [emojis](http://www.webpagefx.com/tools/emoji-cheat-sheet/) :smile:

## Licence

Ce document est placé sous licence CC BY-NC-SA :  
[Creative Commons
Attribution - Pas d'Utilisation Commerciale - Partage dans les Mêmes Conditions](https://creativecommons.org/licenses/by-nc-sa/4.0/)

<img src="https://licensebuttons.net/l/by-nc-sa/3.0/88x31.png" width="100">

En savoir plus sur [les licences Creative Commons](https://creativecommons.org/licenses/?lang=fr-FR) ... 
 
Toutefois, toute personne enseignant au département Informatique de l'IUT du Limousin souhaitant utiliser ces documents doit demander une autorisation préalable :smile:
