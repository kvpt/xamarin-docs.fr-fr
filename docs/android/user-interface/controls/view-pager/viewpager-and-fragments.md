---
title: ViewPager avec des fragments
description: ViewPager est un gestionnaire de disposition qui vous permet d’implémenter la navigation Gestural. La navigation Gestural permet à l’utilisateur de balayer vers la gauche et vers la droite pour parcourir les pages de données. Ce guide explique comment implémenter une interface utilisateur balayable avec ViewPager, à l’aide de fragments comme pages de données.
ms.prod: xamarin
ms.assetid: 62B6286F-3680-48F3-B91B-453692E457E5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 90bffc2360654f571728f76810f144e702a81e57
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646104"
---
# <a name="viewpager-with-fragments"></a>ViewPager avec des fragments

_ViewPager est un gestionnaire de disposition qui vous permet d’implémenter la navigation Gestural. La navigation Gestural permet à l’utilisateur de balayer vers la gauche et vers la droite pour parcourir les pages de données. Ce guide explique comment implémenter une interface utilisateur balayable avec ViewPager, à l’aide de fragments comme pages de données._

 
## <a name="overview"></a>Présentation

`ViewPager`est souvent utilisé conjointement avec les fragments afin qu’il soit plus facile de gérer le cycle de vie de chaque `ViewPager`page dans le. Dans cette procédure pas `ViewPager` à pas, est utilisé pour créer une application appelée **FlashCardPager** qui présente une série de problèmes mathématiques sur les cartes flash. Chaque carte flash est implémentée en tant que fragment. L’utilisateur glisse vers la gauche et vers la droite sur les cartes Flash et tape sur un problème mathématique pour révéler sa réponse. Cette application crée une `Fragment` instance pour chaque carte Flash et implémente un adaptateur dérivé de `FragmentPagerAdapter`. Dans [Viewpager et les vues](~/android/user-interface/controls/view-pager/viewpager-and-views.md), la majeure partie du travail a `MainActivity` été effectuée dans les méthodes de cycle de vie. Dans **FlashCardPager**, la majeure partie du travail est effectuée par un `Fragment` dans l’une de ses méthodes de cycle de vie. 

Ce guide ne couvre pas les concepts de base des fragments &ndash; si vous n’êtes pas encore familiarisé avec les fragments dans Xamarin. Android, consultez [fragments](~/android/platform/fragments/index.md) pour vous aider à vous familiariser avec les fragments. 



## <a name="start-an-app-project"></a>Démarrer un projet d’application

Créez un nouveau projet Android appelé **FlashCardPager**. Ensuite, lancez le gestionnaire de package NuGet (pour plus d’informations sur l’installation des [packages NuGet, consultez Procédure pas à pas: Y compris un NuGet dans votre](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)projet). Recherchez et installez le package **Xamarin. Android. support. v4** comme expliqué dans [Viewpager et views](~/android/user-interface/controls/view-pager/viewpager-and-views.md). 



## <a name="add-an-example-data-source"></a>Ajouter un exemple de source de données

Dans **FlashCardPager**, la source de données est un jeu de cartes Flash représenté par `FlashCardDeck` la classe; cette source de données `ViewPager` fournit le avec le contenu de l’élément. `FlashCardDeck`contient une collection prête à l’emploi de problèmes mathématiques et de réponses. Le `FlashCardDeck` constructeur ne requiert aucun argument: 

```csharp
FlashCardDeck flashCards = new FlashCardDeck();
```

La collection de cartes Flash dans `FlashCardDeck` est organisée de telle sorte que chaque carte flash est accessible par un indexeur. Par exemple, la ligne de code suivante récupère le quatrième problème de carte Flash dans le jeu: 

```csharp
string problem = flashCardDeck[3].Problem;
```

Cette ligne de code récupère la réponse correspondante au problème précédent:

```csharp
string answer = flashCardDeck[3].Answer;
```

Étant donné que les détails `FlashCardDeck` d’implémentation de ne sont `ViewPager`pas pertinents `FlashCardDeck` pour la compréhension, le code n’est pas répertorié ici.
Le code `FlashCardDeck` source est disponible sur [FlashCardDeck.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/FlashCardPager/FlashCardPager/FlashCardDeck.cs).
Téléchargez ce fichier source (ou copiez et collez le code dans un nouveau fichier **FlashCardDeck.cs** ) et ajoutez-le à votre projet.



## <a name="create-a-viewpager-layout"></a>Créer une disposition ViewPager

Ouvrez **Resources/layout/main. AXML** et remplacez son contenu par le code XML suivant:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    </android.support.v4.view.ViewPager>
```

Ce code XML définit `ViewPager` un qui occupe tout l’écran. Notez que vous devez utiliser le nom complet **Android. support. v4. View. ViewPager** , car `ViewPager` est empaqueté dans une bibliothèque de prise en charge. `ViewPager`est disponible uniquement à partir de la [bibliothèque de prise en charge Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/); elle n’est pas disponible dans le Android SDK.


## <a name="set-up-viewpager"></a>Configurer ViewPager

Modifiez **MainActivity.cs** et ajoutez les instructions `using` suivantes:

```csharp
using Android.Support.V4.View;
using Android.Support.V4.App;
```

Modifiez la `MainActivity` déclaration de classe de sorte qu’elle soit `FragmentActivity`dérivée de:

```csharp
public class MainActivity : FragmentActivity
```

`MainActivity`est dérivée`FragmentActivity` de (plutôt `Activity`que) `FragmentActivity` , car sait comment gérer la prise en charge des fragments. Remplacez la méthode `OnCreate` par le code suivant : 

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);
    ViewPager viewPager = FindViewById<ViewPager>(Resource.Id.viewpager);
    FlashCardDeck flashCards = new FlashCardDeck();
}
```

Ce code effectue les opérations suivantes:

1.  Définit la vue à partir de la ressource de disposition **main. AXML** .

2.  Récupère une référence au `ViewPager` à partir de la disposition.

3.  Instancie un nouveau `FlashCardDeck` comme source de données.

Lorsque vous générez et exécutez ce code, vous devez voir un affichage qui ressemble à la capture d’écran suivante: 

[![Capture d’écran de l’application FlashCardPager avec ViewPager vide](viewpager-and-fragments-images/01-initial-screen-sml.png)](viewpager-and-fragments-images/01-initial-screen.png#lightbox)

À ce stade, `ViewPager` est vide, car il ne dispose pas des fragments utilisés pour remplir le `ViewPager`et il ne dispose pas d’un adaptateur pour créer ces fragments à partir des données dans **FlashCardDeck**. 

Dans les sections suivantes, un `FlashCardFragment` est créé pour implémenter les fonctionnalités de chaque carte Flash, et `FragmentPagerAdapter` un est créé pour connecter `ViewPager` le aux fragments créés à partir des données `FlashCardDeck`dans le. 



## <a name="create-the-fragment"></a>Créer le fragment

Chaque carte Flash sera gérée par un fragment d’interface `FlashCardFragment`utilisateur appelé. `FlashCardFragment`affiche les informations contenues dans une seule carte flash. Chaque instance de `FlashCardFragment` sera hébergée `ViewPager`par. 
`FlashCardFragment`la vue de est composée d' `TextView` un qui affiche le texte du problème de la carte flash. Cette vue implémente un gestionnaire d’événements qui utilise `Toast` un pour afficher la réponse lorsque l’utilisateur appuie sur la question de la carte flash. 



### <a name="create-the-flashcardfragment-layout"></a>Créer la disposition FlashCardFragment

Avant `FlashCardFragment` de pouvoir implémenter, sa disposition doit être définie. Cette disposition est une disposition de conteneur de fragments pour un fragment unique. Ajoutez une nouvelle disposition Android aux **ressources/mise en page** appelée **flashcard_layout. AXML**. Ouvrez **Resources/layout/flashcard_layout. AXML** et remplacez son contenu par le code suivant: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <TextView
        android:id="@+id/flash_card_question"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:gravity="center"
            android:textAppearance="@android:style/TextAppearance.Large"
            android:textSize="100sp"
            android:layout_centerHorizontal="true"
            android:layout_centerVertical="true"
            android:text="Question goes here" />
    </RelativeLayout>
```

Cette disposition définit un fragment de carte Flash unique. chaque fragment est constitué d’un `TextView` qui affiche un problème mathématique à l’aide d’une police de grande taille (100sp). Ce texte est centré verticalement et horizontalement sur la carte flash. 



### <a name="create-the-initial-flashcardfragment-class"></a>Créer la classe FlashCardFragment initiale

Ajoutez un nouveau fichier appelé **FlashCardFragment.cs** et remplacez son contenu par le code suivant:

```csharp
using System;
using Android.OS;
using Android.Views;
using Android.Widget;
using Android.Support.V4.App;

namespace FlashCardPager
{
    public class FlashCardFragment : Android.Support.V4.App.Fragment
    {
        public FlashCardFragment() { }

        public static FlashCardFragment newInstance(String question, String answer)
        {
            FlashCardFragment fragment = new FlashCardFragment();
            return fragment;
        }
        public override View OnCreateView (
            LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
        {
            View view = inflater.Inflate (Resource.Layout.flashcard_layout, container, false);
            TextView questionBox = (TextView)view.FindViewById (Resource.Id.flash_card_question);
            return view;
        }
    }
}
```

Ce code supprime la définition essentielle `Fragment` qui sera utilisée pour afficher une carte flash. Notez que `FlashCardFragment` est dérivé de la version de la bibliothèque `Fragment` de prise `Android.Support.V4.App.Fragment`en charge de définie dans. Le constructeur est vide afin que la `newInstance` méthode de fabrique soit utilisée pour créer un `FlashCardFragment` nouveau à la place d’un constructeur. 

La `OnCreateView` méthode Lifecycle crée et configure le `TextView`. Il gonfle la disposition du fragment `TextView` et retourne le `TextView` gonflé à l’appelant. `LayoutInflater`et `ViewGroup` sont passés à `OnCreateView` afin de pouvoir augmenter la disposition. Le `savedInstanceState` bundle contient `TextView` des données `OnCreateView` qui utilisent pour recréer à partir d’un état enregistré. 

La vue du fragment est explicitement déflatée par l’appel à `inflater.Inflate`. L' `container` argument est le parent de la vue, et `false` l’indicateur demande à l’imflateur de s’abstenir d’ajouter la vue gonflée au parent de la vue (il sera ajouté quand `ViewPager` la `GetItem` méthode de l’adaptateur sera ajoutée plus loin dans ce procédure pas à pas). 



### <a name="add-state-code-to-flashcardfragment"></a>Ajouter du code d’État à FlashCardFragment

À l’instar d’une activité, `Bundle` un fragment a un qu’il utilise pour enregistrer et récupérer son état. Dans **FlashCardPager**, cette `Bundle` valeur est utilisée pour enregistrer la question et le texte de réponse de la carte Flash associée. Dans **FlashCardFragment.cs**, ajoutez les clés `Bundle` suivantes au début de la `FlashCardFragment` définition de classe: 

```csharp
private static string FLASH_CARD_QUESTION = "card_question";
private static string FLASH_CARD_ANSWER = "card_answer";
```

Modifiez la `newInstance` méthode de fabrique afin qu’elle crée `Bundle` un objet et utilise les clés ci-dessus pour stocker la question et le texte de réponse passés dans le fragment après son instanciation: 

```csharp
public static FlashCardFragment newInstance(String question, String answer)
{
    FlashCardFragment fragment = new FlashCardFragment();

    Bundle args = new Bundle();
    args.PutString(FLASH_CARD_QUESTION, question);
    args.PutString(FLASH_CARD_ANSWER, answer);
    fragment.Arguments = args;

    return fragment;
}
```

Modifiez la méthode `OnCreateView` fragment Lifecycle pour récupérer ces informations à partir de l’offre groupée et charger le texte de la `TextBox`question dans le: 

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    string question = Arguments.GetString(FLASH_CARD_QUESTION, "");
    string answer = Arguments.GetString(FLASH_CARD_ANSWER, "");

    View view = inflater.Inflate(Resource.Layout.flashcard_layout, container, false);
    TextView questionBox = (TextView)view.FindViewById(Resource.Id.flash_card_question);
    questionBox.Text = question;

    return view;
}
```

La `answer` variable n’est pas utilisée ici, mais elle sera utilisée ultérieurement quand le code du gestionnaire d’événements est ajouté à ce fichier. 


## <a name="create-the-adapter"></a>Créer l’adaptateur

`ViewPager`utilise un objet contrôleur d’adaptateur qui se situe `ViewPager` entre le et la source de données (Voir l’illustration de l’article de l' [adaptateur](~/android/user-interface/controls/view-pager/index.md#adapter) ViewPager). Pour accéder à ces données `ViewPager` , exige que vous fournissiez un adaptateur personnalisé dérivé `PagerAdapter`de. Étant donné que cet exemple utilise des fragments, `FragmentPagerAdapter` il utilise un `PagerAdapter` &ndash; `FragmentPagerAdapter` est dérivé de. 
`FragmentPagerAdapter`représente chaque page comme une `Fragment` qui est conservée de manière permanente dans le gestionnaire de fragments tant que l’utilisateur peut revenir à la page. À mesure que l’utilisateur fait défiler `ViewPager`les pages `FragmentPagerAdapter` du, le extrait des informations de la source de données et `Fragment`les utilise pour `ViewPager` créer des s pour l’affichage. 

Lorsque vous implémentez `FragmentPagerAdapter`un, vous devez substituer les éléments suivants:

-   **Nombre** &ndash; Propriété en lecture seule qui retourne le nombre de vues (pages) disponibles.

-   **GetItem** &ndash; Retourne le fragment à afficher pour la page spécifiée.

Ajoutez un nouveau fichier appelé **FlashCardDeckAdapter.cs** et remplacez son contenu par le code suivant:

```csharp
using System;
using Android.Views;
using Android.Widget;
using Android.Support.V4.App;

namespace FlashCardPager
{
    class FlashCardDeckAdapter : FragmentPagerAdapter
    {
        public FlashCardDeckAdapter (Android.Support.V4.App.FragmentManager fm, FlashCardDeck flashCards)
            : base(fm)
        {
        }

        public override int Count
        {
            get { throw new NotImplementedException(); }
        }

        public override Android.Support.V4.App.Fragment GetItem(int position)
        {
            throw new NotImplementedException();
        }
    }
}
```

Ce code élimine l’implémentation `FragmentPagerAdapter` essentielle. Dans les sections suivantes, chacune de ces méthodes est remplacée par du code de travail. L’objectif du constructeur est de passer le gestionnaire de fragments au `FlashCardDeckAdapter`constructeur de classe de base de. 



### <a name="implement-the-adapter-constructor"></a>Implémenter le constructeur d’adaptateur

Lorsque l’application instancie le `FlashCardDeckAdapter`, elle fournit une référence au gestionnaire de fragments et une `FlashCardDeck`instance instanciée. Ajoutez la variable de membre suivante au début de la `FlashCardDeckAdapter` classe dans **FlashCardDeckAdapter.cs**: 

```csharp
public FlashCardDeck flashCardDeck;
```

Ajoutez la ligne de code suivante au `FlashCardDeckAdapter` constructeur: 

```csharp
this.flashCardDeck = flashCards;
```

Cette ligne de code stocke `FlashCardDeck` l’instance que `FlashCardDeckAdapter` le doit utiliser. 



### <a name="implement-count"></a>Nombre d’implémentations

L' `Count` implémentation est relativement simple: elle retourne le nombre de cartes Flash dans le jeu de cartes flash. Remplacez `Count` par le code suivant : 

```csharp
public override int Count
{
    get { return flashCardDeck.NumCards; }
}
```


La `NumCards` propriété de `FlashCardDeck` retourne le nombre de cartes Flash (nombre de fragments) dans le jeu de données. 



### <a name="implement-getitem"></a>Implémentez GetItem

La `GetItem` méthode retourne le fragment associé à la position donnée. Lorsque `GetItem` est appelé pour une position dans le jeu de cartes Flash, il retourne `FlashCardFragment` un configuré pour afficher le problème de carte mémoire flash à cette position. Remplacez la méthode `GetItem` par le code suivant : 

```csharp
public override Android.Support.V4.App.Fragment GetItem(int position)
{
    return (Android.Support.V4.App.Fragment)
        FlashCardFragment.newInstance (
            flashCardDeck[position].Problem, flashCardDeck[position].Answer);
}
```

Ce code effectue les opérations suivantes:

1.  Recherche la chaîne de problème mathématique dans le `FlashCardDeck` jeu de la position spécifiée. 

2.  Recherche la chaîne de réponse dans le `FlashCardDeck` jeu de la position spécifiée. 

3.  Appelle la `FlashCardFragment` méthode `newInstance`de fabrique, en passant le problème de la carte Flash et les chaînes de réponse. 

4.  Crée et retourne une nouvelle carte `Fragment` Flash qui contient la question et le texte de réponse pour cette position. 

Lorsque le `ViewPager` restitue le `Fragment` à `position`, il affiche le `TextBox` contenant la chaîne `position` de problème mathématique résidant dans le jeu de cartes flash. 



## <a name="add-the-adapter-to-the-viewpager"></a>Ajouter l’adaptateur au ViewPager

Maintenant que la `FlashCardDeckAdapter` est implémentée, il est temps de l’ajouter `ViewPager`au. Dans **MainActivity.cs**, ajoutez la ligne de code suivante à la fin de la `OnCreate` méthode:

```csharp
FlashCardDeckAdapter adapter =
    new FlashCardDeckAdapter(SupportFragmentManager, flashCards);
viewPager.Adapter = adapter;
```

Ce code instancie le `FlashCardDeckAdapter`, en passant le `SupportFragmentManager` dans le premier argument. (La `SupportFragmentManager` propriété de FragmentActivity est utilisée pour obtenir une référence &ndash; `FragmentManager` au pour plus d’informations sur le `FragmentManager`, consultez [gestion des fragments](~/android/platform/fragments/managing-fragments.md).) 

L’implémentation de base est maintenant &ndash; terminée la génération et l’exécution de l’application.
La première image du jeu de cartes Flash doit s’afficher sur l’écran, comme indiqué à gauche dans la capture d’écran suivante. Balayez vers la gauche pour voir plus de cartes Flash, puis faites défiler vers la droite pour revenir en arrière dans le jeu de cartes Flash:

[![Exemples de captures d’écran de l’application FlashCardPager sans indicateurs de radiomessagerie](viewpager-and-fragments-images/02-example-views-sml.png)](viewpager-and-fragments-images/02-example-views.png#lightbox)



## <a name="add-a-pager-indicator"></a>Ajouter un indicateur de pagineur

Cette implémentation `ViewPager` minimale affiche chaque carte Flash dans le jeu, mais elle ne fournit aucune indication quant à l’emplacement de l’utilisateur dans le jeu. L’étape suivante consiste à ajouter un `PagerTabStrip`. `PagerTabStrip` Informe l’utilisateur de l’affichage du numéro de problème et fournit un contexte de navigation en affichant un indicateur des cartes Flash précédentes et suivantes. 

Ouvrez **ressources/mise en page/main. AXML** et `PagerTabStrip` ajoutez un à la disposition:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

  <android.support.v4.view.PagerTabStrip
      android:layout_width="match_parent"
      android:layout_height="wrap_content"
      android:layout_gravity="top"
      android:paddingBottom="10dp"
      android:paddingTop="10dp"
      android:textColor="#fff" />

</android.support.v4.view.ViewPager>
```

Lorsque vous générez et exécutez l’application, vous devez voir le `PagerTabStrip` vide affiché en haut de chaque carte Flash: 

[![Gros plan de PagerTabStrip sans texte](viewpager-and-fragments-images/03-empty-pagetabstrip-sml.png)](viewpager-and-fragments-images/03-empty-pagetabstrip.png#lightbox)



### <a name="display-a-title"></a>Afficher un titre

Pour ajouter un titre à chaque onglet de page, implémentez la `GetPageTitleFormatted` méthode dans l’adaptateur. `ViewPager`appelle `GetPageTitleFormatted` (s’il est implémenté) pour obtenir la chaîne de titre qui décrit la page à la position spécifiée. Ajoutez la méthode suivante à la `FlashCardDeckAdapter` classe dans **FlashCardDeckAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String("Problem " + (position + 1));
}
```

Ce code convertit la position dans le jeu de cartes flash en un numéro de problème. La chaîne résultante est convertie `String` en Java retournée `ViewPager`à. Quand vous exécutez l’application avec cette nouvelle méthode, chaque page affiche le numéro de problème dans `PagerTabStrip`le: 

[![Captures d’écran de FlashCardPager avec le numéro de problème affiché au-dessus de chaque page](viewpager-and-fragments-images/04-pagetabstrip-sml.png)](viewpager-and-fragments-images/04-pagetabstrip.png#lightbox)

Vous pouvez revenir en arrière pour voir le numéro de problème dans le jeu de cartes Flash qui s’affiche en haut de chaque carte flash. 



## <a name="handle-user-input"></a>Gérer l’entrée d’utilisateur

**FlashCardPager** présente une série de cartes Flash basées sur des fragments `ViewPager`dans un, mais il n’a pas encore de moyen de révéler la réponse pour chaque problème. Dans cette section, un gestionnaire d’événements est ajouté au `FlashCardFragment` pour afficher la réponse lorsque l’utilisateur appuie sur le texte du problème de la carte flash. 

Ouvrez **FlashCardFragment.cs** et ajoutez le code suivant à la fin de la `OnCreateView` méthode juste avant que la vue soit renvoyée à l’appelant: 

```csharp
questionBox.Click += delegate
{
    Toast.MakeText(Activity.ApplicationContext,
            "Answer: " + answer, ToastLength.Short).Show();
};
```

Ce `Click` gestionnaire d’événements affiche la réponse dans un toast qui s’affiche lorsque l’utilisateur `TextBox`appuie sur. La `answer` variable a été initialisée précédemment lorsque les informations d’État ont été lues à partir du `OnCreateView`bundle qui a été passé à. Générez et exécutez l’application, puis appuyez sur le texte du problème sur chaque carte Flash pour voir la réponse: 

[![Captures d’écran des toasts d’application FlashCardPager lorsque le problème mathématique est frappé](viewpager-and-fragments-images/05-answer-sml.png)](viewpager-and-fragments-images/05-answer.png#lightbox)

Le **FlashCardPager** présenté dans cette procédure pas à `MainActivity` pas utilise `FragmentActivity`un dérivé de, mais vous `MainActivity` pouvez `AppCompatActivity` également dériver de (qui fournit également la prise en charge de la gestion des fragments). Pour afficher un `AppCompatActivity` exemple, consultez [FlashCardPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager) dans la Galerie d’exemples.



## <a name="summary"></a>Récapitulatif

Cette procédure pas à pas a fourni un exemple pas à pas de création d’une `ViewPager`application de base à `Fragment`l’aide de. Il a présenté un exemple de source de données contenant des questions et réponses `ViewPager` sur les cartes Flash, une mise en page `FragmentPagerAdapter` permettant d’afficher les cartes `ViewPager` Flash et une sous-classe qui connecte le à la source de données. Pour aider l’utilisateur à parcourir les cartes Flash, vous trouverez des instructions qui expliquent comment ajouter `PagerTabStrip` un pour afficher le numéro de problème en haut de chaque page. Enfin, le code de gestion des événements a été ajouté pour afficher la réponse lorsque l’utilisateur appuie sur un problème de carte mémoire flash. 



## <a name="related-links"></a>Liens associés

- [FlashCardPager (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager)
