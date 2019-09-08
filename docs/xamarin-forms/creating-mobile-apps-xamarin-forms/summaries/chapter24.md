---
title: Résumé du chapitre 24. Navigation entre les pages
description: 'Création d’Mobile Apps avec Xamarin. Forms : Résumé du chapitre 24. Navigation entre les pages'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: DDCDB49C-6008-4F72-B095-463EE21D7C23
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 340aa8c7327ba75986b960d5e0f6bda66c116fc5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770925"
---
# <a name="summary-of-chapter-24-page-navigation"></a>Résumé du chapitre 24. Navigation entre les pages

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)

De nombreuses applications sont constituées de plusieurs pages parmi lesquels l’utilisateur navigue. L’application always a un *principal* page ou *domestique* page, et à partir de là, l’utilisateur navigue vers d’autres pages, qui sont conservées dans une pile de navigation vers l’arrière. Options de navigation supplémentaires sont couverts dans [ **chapitre 25. Page variétés**](chapter25.md).

## <a name="modal-pages-and-modeless-pages"></a>Pages modales et non modales

`VisualElement` définit un [ `Navigation` ](xref:Xamarin.Forms.NavigableElement.Navigation) propriété de type [ `INavigation` ](xref:Xamarin.Forms.INavigation), qui inclut les deux méthodes suivantes pour accéder à une nouvelle page :

- [`PushAsync`](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page))
- [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page))

Les deux méthodes acceptent une `Page` instance en tant qu’argument et retournent un `Task` objet. Les deux méthodes suivantes accédez à la page précédente :

- [`PopAsync`](xref:Xamarin.Forms.INavigation.PopAsync)
- [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync)

Si l’interface utilisateur possède son propre **retour** bouton (comme les téléphones Android et Windows), il n’est pas nécessaire pour l’application d’appeler ces méthodes.

Bien que ces méthodes sont disponibles à partir d’un `VisualElement`, elles sont généralement appelées à partir la `Navigation` propriété du courant `Page` instance.

Les applications utilisent généralement des pages modales quand l’utilisateur est requis pour fournir des informations sur la page avant de retourner à la page précédente. Les pages qui ne sont pas modales sont parfois appelés non modales ou *hiérarchique*. Rien dans la page elle-même distingue en tant que modale ou non modale ; Il est soumise à la place la méthode utilisée pour naviguer jusqu'à lui. Pour fonctionner sur toutes les plateformes, une page modale doit fournir sa propre interface utilisateur pour revenir à la page précédente.

Le [ **ModelessAndModal** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal) exemple vous permet d’Explorer la différence entre les pages non modales et modales. Toute application qui utilise la navigation entre les pages doive passer sa page d’accueil pour le [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) constructeur, généralement dans le programme `App` classe. Un bonus est que vous n’avez plus besoin de définir un `Padding` sur la page pour iOS.

Vous découvrirez que pour les non modales pages, la page [ `Title` ](xref:Xamarin.Forms.Page.Title) propriété s’affiche. IOS, Android et les plateformes de tablette et bureau Windows fournissent un élément d’interface utilisateur pour revenir à la page précédente. Des cours, Android et Windows, les téléphones ont une norme **retour** bouton revenir en arrière.

Pour les pages modales, la page `Title` n’est pas affichée, et aucun élément d’interface utilisateur n’est fournie pour revenir à la page précédente. Bien que vous pouvez utiliser la norme de téléphone Android et Windows **retour** bouton pour revenir à la page précédente, la page modale sur d’autres plateformes doit fournir son propre mécanisme pour revenir en arrière.

### <a name="animated-page-transitions"></a>Transitions de page animés

Autres versions des différentes méthodes de navigation sont fournies avec un deuxième argument booléen que vous définissez pour `true` si vous souhaitez que la transition de page à inclure une animation :

- [PushAsync](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page,System.Boolean))
- [PushModalAsync](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page,System.Boolean))
- [PopAsync](xref:Xamarin.Forms.INavigation.PopAsync(System.Boolean))
- [PopModalAsync](xref:Xamarin.Forms.INavigation.PopModalAsync(System.Boolean))

Toutefois, les méthodes de navigation de page standard incluent l’animation par défaut, afin qu’ils soient uniquement un outil précieux pour la navigation vers une page particulière au démarrage (comme indiqué à la fin de ce chapitre) ou lorsque vous fournissez votre propre animation d’entrée (comme indiqué dans [ **Chapter22. Animation**](chapter22.md)).

### <a name="visual-and-functional-variations"></a>Variations de visuels et fonctionnelles

`NavigationPage` inclut deux propriétés que vous pouvez définir lorsque vous instanciez la classe dans votre `App` méthode :

- [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)
- [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor)

`NavigationPage` inclut également les quatre propriétés jointes pouvant être liées qui affectent la page spécifique sur lequel elles sont définies :

- [`SetHasBackButton`](xref:Xamarin.Forms.NavigationPage.SetHasBackButton(Xamarin.Forms.Page,System.Boolean)) et [`GetHasBackButton`](xref:Xamarin.Forms.NavigationPage.GetHasBackButton(Xamarin.Forms.Page))
- [`SetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.SetHasNavigationBar(Xamarin.Forms.BindableObject,System.Boolean)) et [`GetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.GetHasNavigationBar(Xamarin.Forms.BindableObject))
- [`SetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.SetBackButtonTitle(Xamarin.Forms.BindableObject,System.String)) et [ `GetBackButtonTitle` ](xref:Xamarin.Forms.NavigationPage.GetBackButtonTitle(Xamarin.Forms.BindableObject)) Professionnel sur iOS uniquement
- [`SetTitleIcon`](xref:Xamarin.Forms.NavigationPage.SetTitleIcon(Xamarin.Forms.BindableObject,Xamarin.Forms.ImageSource)) et [ `GetTitleIcon` ](xref:Xamarin.Forms.NavigationPage.GetTitleIcon(Xamarin.Forms.BindableObject)) Professionnel sur iOS et Android uniquement

### <a name="exploring-the-mechanics"></a>Explorer la mécanique

Les méthodes de navigation de page sont toutes asynchrones et doit être utilisés avec `await`. La saisie semi-automatique n’indique pas que navigation entre les pages est terminée, mais uniquement qu’il est sécurisé examiner la pile de navigation entre les pages.

Lorsqu’une page accède à un autre, la première page obtienne généralement un appel à son [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) (méthode) et la deuxième page reçoit un appel à son [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) (méthode). De même, quand une page est retournée à l’autre, la première page reçoit un appel à son `OnDisappearing` (méthode) et la deuxième page obtient généralement un appel à son `OnAppearing` (méthode). L’ordre de ces appels (et l’achèvement des méthodes asynchrones qui appelle le volet de navigation) est dépendant de la plate-forme. L’utilisation du mot « généralement » dans les deux instructions ci-dessus est en raison de la navigation modale-page Android, dans lequel ces appels de méthode ne se produisent.

En outre, les appels à la `OnAppearing` et `OnDisappearing` méthodes n’indiquent pas nécessairement navigation entre les pages.

Le `INavigation` interface inclut deux propriétés de collection qui vous permettent d’examiner la pile de navigation :

- [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack) de type `IReadOnlyList<Page>` pour la pile non modale
- [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack) de type `IReadOnlyList<Page>` pour la pile modale

Il est plus sûr d’accéder à ces piles à partir de la `Navigation` propriété de la `NavigationPage` (qui doit être le `App` la classe [ `MainPage` ](xref:Xamarin.Forms.Application.MainPage) propriété). Il n’est plus sûr examiner ces piles une fois que les méthodes de navigation de page asynchrone est terminé. Le [ `CurrentPage` ](xref:Xamarin.Forms.NavigationPage.CurrentPage) propriété de la `NavigationPage` n’indique pas la page actuelle si la page actuelle est une page modale, mais indique à la place la dernière page non modale.

Le [ **SinglePageNavigation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation) exemple vous permet d’Explorer la navigation entre les pages et les piles et les types juridiques de navigation de page :

- Une page non modale peut naviguer vers une autre page non modale ou une page modale
- Une page modale peut uniquement naviguer vers une autre page modale

### <a name="enforcing-modality"></a>En appliquant la modalité

Une application utilise une page modale lorsqu’il est nécessaire obtenir des informations à partir de l’utilisateur. L’utilisateur doit être interdit de retourner à la page précédente jusqu'à ce que cette information est fournie. Sur iOS, il est facile de fournir un **retour** bouton et activez-la uniquement lorsque l’utilisateur a fini avec la page. Mais pour les appareils Android et Windows phone, l’application doit remplacer le [ `OnBackButtonPressed` ](xref:Xamarin.Forms.Page.OnBackButtonPressed) méthode et retournez `true` si le programme a géré le **retour** bouton lui-même, comme illustré dans le [ **ModalEnforcement** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement) exemple.

Le [ **MvvmEnforcement** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement) exemple illustre comment cela fonctionne dans un scénario MVVM.

## <a name="navigation-variations"></a>Variations de navigation

Si une page modale particulier peut être accédée à plusieurs fois, il doit conserver les informations afin que l’utilisateur peut modifier les informations au lieu de taper tous dans une nouvelle fois. Vous pouvez le gérer en conservant l’instance particulière de la page modale, mais une meilleure approche (en particulier sur iOS) conserve les informations contenues dans un modèle de vue.

### <a name="making-a-navigation-menu"></a>Rendre un menu de navigation

Le [ **ViewGalleryType** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType) exemple montre comment utiliser un `TableView` pour répertorier les éléments de menu. Chaque élément est associé un `Type` objet pour une page particulière. Lorsque cet élément est sélectionné, le programme instancie la page et navigue vers elle.

[![Capture d’écran triple de Type de la vue bibliothèque](images/ch24fg21-small.png "TableView répertoriant les éléments de Menu")](images/ch24fg21-large.png#lightbox "TableView répertoriant les éléments de Menu")

Le [ **ViewGalleryInst** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst) exemple est un peu différente car le menu contient des instances de chaque page plutôt que des types. Cela permet de conserver les informations à partir de chaque page, mais toutes les pages doivent être instanciés au démarrage du programme.

### <a name="manipulating-the-navigation-stack"></a>Manipulation de la pile de navigation

[**StackManipulation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation) montre plusieurs fonctions définies par `INavigation` qui vous permettent de manipuler la pile de navigation de manière structurée :

- [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page))
- [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page))
- [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync) et [ `PopToRootAsync` ](xref:Xamarin.Forms.INavigation.PopToRootAsync(System.Boolean)) avec animation facultative

### <a name="dynamic-page-generation"></a>Génération de page dynamique

Le [ **BuildAPage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage) exemple illustre la construction d’une page lors de l’exécution en fonction de l’entrée d’utilisateur.

## <a name="patterns-of-data-transfer"></a>Modèles de transfert de données

Il est souvent nécessaire de partager des données entre les pages &mdash; pour transférer des données vers une page de navigation et d’une page retourner des données à la page qui l’a appelée. Il existe plusieurs techniques permettant de le faire.

### <a name="constructor-arguments"></a>Arguments de constructeur

Lors de la navigation vers une nouvelle page, il est possible d’instancier la classe de page avec un argument de constructeur qui permet à la page pour s’initialiser. Le [ **SchoolAndStudents** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents) illustre cela. Il est également possible d’avoir une page naviguée son `BindingContext` définie par la page qui navigue vers elle.

### <a name="properties-and-method-calls"></a>Propriétés et appels de méthode

Les autres exemples de transfert de données d’Explorer le problème de passer des informations entre les pages quand une page navigue vers une autre page et inversement. Dans ces discussions, les *domestique* page accède à la *info* page et doit transférer des informations initialisées à la *info* page. Le *info* page obtient des informations supplémentaires à partir de l’utilisateur et transfère les informations à la *domestique* page.

Le *domestique* page permettre accéder facilement aux méthodes publiques et les propriétés dans le *info* page dès qu’il instancie cette page. Le *info* page permettre également accéder aux méthodes publiques et les propriétés dans le *domestique* page, mais que le choix d’un bon moment pour Ceci peut être difficile. Le [ **DateTransfer1** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1) exemple fait cela dans son `OnDisappearing` remplacer. L’inconvénient est que le *info* page doit connaître le type de la *domestique* page.

### <a name="messagingcenter"></a>MessagingCenter

Xamarin.Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe fournit une autre façon pour les deux pages communiquer entre eux. Les messages sont identifiés par une chaîne de texte et peuvent être accompagnés de n’importe quel objet.

Un programme qui souhaite recevoir des messages à partir d’un type particulier doit s’abonner à eux à l’aide de [ `MessagingCenter.Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*) et spécifier une fonction de rappel. Plus tard, il peut se désabonner en appelant [ `MessagingCenter.Unsubscribe` ](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*). La fonction de rappel reçoit tout message envoyé à partir du type spécifié avec le nom spécifié est envoyé via le [ `Send` ](xref:Xamarin.Forms.MessagingCenter.Send*) (méthode).

Le [ **DateTransfer2** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2) programme montre comment transférer des données à l’aide du centre de messagerie, mais là encore, ceci nécessite que le *info* page connaître le type de la *accueil* page.

### <a name="events"></a>Événements

L’événement est une approche précieuses pour une classe envoyer des informations à une autre classe sans connaître les type de cette classe. Dans le [ **DateTransfer3** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3) exemple le *info* classe définit un événement qu’il se déclenche lorsque les informations sont prêtes. Toutefois, il n’existe aucun emplacement pratique où les le *domestique* page pour détacher le Gestionnaire d’événements.

### <a name="the-app-class-intermediary"></a>L’intermédiaire de la classe application

Le [ **DateTransfer4** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4) exemple montre comment accéder aux propriétés définies dans le `App` classe à la fois par le *domestique* page et le *info*page. Il s’agit d’une bonne solution, mais la section suivante décrit une meilleure solution.

### <a name="switching-to-a-viewmodel"></a>Passage à un ViewModel

À l’aide d’un ViewModel pour les informations permettant la *domestique* page et le *info* page à partager l’instance de la classe d’informations. Cela est illustré dans le [ **DateTransfer5** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5) exemple.

### <a name="saving-and-restoring-page-state"></a>Enregistrement et restauration de l’état de la page

Le `App` intermédiaire de la classe ou l’approche ViewModel est idéale lors de l’application doit enregistrer vos informations si le programme se met en veille pendant que le *info* page est active. Le [ **DateTransfer6** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6) illustre cela.

## <a name="saving-and-restoring-the-navigation-stack"></a>Enregistrement et restauration de la pile de navigation

En règle générale, un programme de plusieurs pages qui se met en veille doit accéder à la même page lorsqu’elle est restaurée. Cela signifie qu’un tel programme doit enregistrer le contenu de la pile de navigation. Cette section montre comment automatiser ce processus dans une classe conçue à cet effet. Cette classe appelle également les pages individuelles pour les autoriser à enregistrer et restaurer leur état de la page.

Le [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) bibliothèque définit une interface nommée [ `IPersistantPage` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs) que les classes peuvent implémenter pour enregistrer et restaurer des éléments dans le `Properties`dictionnaire.

Le [ `MultiPageRestorableApp` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs) classe dans le **Xamarin.FormsBook.Toolkit** dérive de la bibliothèque `Application`. Vous pouvez ensuite dériver votre `App` classe `MultiPageRestorableApp` et effectuer un peu de ménage.

Le [ **StackRestoreDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo) illustre l’utilisation de `MultiPageRestorableApp`.

### <a name="something-like-a-real-life-app"></a>Quelque chose comme une application de la vie réelle

Le [ **NoteTaker** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker) exemple utilise également `MultiPageRestorableApp` , tout en permettant à l’entrer et de modifier des notes qui sont enregistrés dans le `Properties` dictionnaire.

## <a name="related-links"></a>Liens connexes

- [Chapitre 24 de texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [Exemples de chapitre 24](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [Navigation hiérarchique](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Pages modales](~/xamarin-forms/app-fundamentals/navigation/modal.md)
