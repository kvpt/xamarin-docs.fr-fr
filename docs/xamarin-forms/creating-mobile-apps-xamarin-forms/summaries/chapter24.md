---
title: Résumé du chapitre 24. Navigation de page
description: 'Création d’Mobile Apps avec Xamarin.Forms : Résumé du chapitre 24. Navigation de page'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: DDCDB49C-6008-4F72-B095-463EE21D7C23
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4ab09546bb2a1dcbc221f3819bc891ce096ce569
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373965"
---
# <a name="summary-of-chapter-24-page-navigation"></a>Résumé du chapitre 24. Navigation de page

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)

> [!NOTE]
> Ce livre a été publié à la printemps de 2016 et n’a pas été mis à jour depuis. Le livre reste très utile, mais certains d’entre eux sont obsolètes et certaines rubriques ne sont plus entièrement correctes ou complètes.

De nombreuses applications se composent de plusieurs pages parmi lesquelles l’utilisateur navigue. L’application dispose toujours d’une page *principale* ou d’une page d' *Accueil* , et à partir de là, l’utilisateur accède à d’autres pages, qui sont conservées dans une pile pour naviguer vers l’arrière. Des options de navigation supplémentaires sont décrites dans le [**chapitre 25. Variétés de page**](chapter25.md).

## <a name="modal-pages-and-modeless-pages"></a>Pages modales et pages non modales

`VisualElement` définit une [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriété de type [`INavigation`](xref:Xamarin.Forms.INavigation) , qui comprend les deux méthodes suivantes pour accéder à une nouvelle page :

- [ `PushAsync` ] (XREF : Xamarin.Forms . INavigation. PushAsync ( Xamarin.Forms . Page))
- [ `PushModalAsync` ] (XREF : Xamarin.Forms . INavigation. PushModalAsync ( Xamarin.Forms . Page))

Les deux méthodes acceptent une `Page` instance comme argument et retournent un `Task` objet. Les deux méthodes suivantes permettent de revenir à la page précédente :

- [`PopAsync`](xref:Xamarin.Forms.INavigation.PopAsync)
- [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync)

Si l’interface utilisateur a son propre bouton **précédent** (comme les téléphones Android et Windows), il n’est pas nécessaire que l’application appelle ces méthodes.

Bien que ces méthodes soient disponibles à partir de n’importe laquelle `VisualElement` , elles sont généralement appelées à partir de la `Navigation` propriété de l' `Page` instance actuelle.

Les applications utilisent généralement des pages modales lorsque l’utilisateur doit fournir des informations sur la page avant de revenir à la page précédente. Les pages qui ne sont pas modales sont parfois appelées non modales ou *hiérarchiques*. Rien dans la page elle-même ne la distingue comme modale ou non modale ; elle est régie par la méthode utilisée pour y accéder. Pour fonctionner sur toutes les plateformes, une page modale doit fournir sa propre interface utilisateur pour revenir à la page précédente.

L’exemple [**ModelessAndModal**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal) vous permet d’explorer la différence entre les pages modales et les pages modales. Toute application qui utilise la navigation entre les pages doit passer sa page d’hébergement au [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) constructeur, généralement dans la classe du programme `App` . L’une des bonus est que vous n’avez plus besoin de définir un `Padding` sur la page pour iOS.

Vous découvrirez que pour les pages non modales, la propriété de la page [`Title`](xref:Xamarin.Forms.Page.Title) s’affiche. Les plateformes iOS, Android et Windows Tablet et Desktop fournissent toutes un élément d’interface utilisateur pour revenir à la page précédente. Bien entendu, les appareils Android et Windows Phone ont un bouton de **retour** standard pour revenir en arrière.

Pour les pages modales, la page `Title` n’est pas affichée et aucun élément d’interface utilisateur n’est fourni pour revenir à la page précédente. Bien que vous puissiez utiliser le bouton **précédent** Android et Windows Phone standard pour revenir à la page précédente, la page modale sur les autres plateformes doit fournir son propre mécanisme pour revenir en arrière.

### <a name="animated-page-transitions"></a>Transitions de pages animées

D’autres versions des différentes méthodes de navigation sont fournies avec un deuxième argument booléen dont vous avez défini `true` la valeur si vous souhaitez que la transition de page inclue une animation :

- PushAsync (XREF : Xamarin.Forms . INavigation. PushAsync ( Xamarin.Forms . Page, System. Boolean)
- [PushModalAsync] (XREF : Xamarin.Forms . INavigation. PushModalAsync ( Xamarin.Forms . Page, System. Boolean)
- [PopAsync](xref:Xamarin.Forms.INavigation.PopAsync(System.Boolean))
- [PopModalAsync](xref:Xamarin.Forms.INavigation.PopModalAsync(System.Boolean))

Toutefois, les méthodes de navigation de page standard incluent l’animation par défaut. elles sont donc uniquement utiles pour naviguer vers une page particulière au démarrage (comme indiqué dans la fin de ce chapitre) ou lorsque vous fournissez votre propre animation d’entrée (comme indiqué dans [**Chapter22. Animation**](chapter22.md)).

### <a name="visual-and-functional-variations"></a>Variations visuelles et fonctionnelles

`NavigationPage` comprend deux propriétés que vous pouvez définir quand vous instanciez la classe dans votre `App` méthode :

- [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)
- [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor)

`NavigationPage` comprend également quatre propriétés pouvant être liées qui affectent la page particulière sur laquelle elles sont définies :

- [ `SetHasBackButton` ] (XREF : Xamarin.Forms . NavigationPage. SetHasBackButton ( Xamarin.Forms . Page, System. Boolean)) et [ `GetHasBackButton` ] (XREF : Xamarin.Forms . NavigationPage. GetHasBackButton ( Xamarin.Forms . Page))
- [ `SetHasNavigationBar` ] (XREF : Xamarin.Forms . NavigationPage. SetHasNavigationBar ( Xamarin.Forms . BindableObject, System. Boolean)) et [ `GetHasNavigationBar` ] (XREF : Xamarin.Forms . NavigationPage. GetHasNavigationBar ( Xamarin.Forms . BindableObject))
- [ `SetBackButtonTitle` ] (XREF : Xamarin.Forms . NavigationPage. SetBackButtonTitle ( Xamarin.Forms . BindableObject, System. String)) et [ `GetBackButtonTitle` ] (XREF : Xamarin.Forms . NavigationPage. GetBackButtonTitle ( Xamarin.Forms . BindableObject)) travailler sur iOS uniquement
- [ `SetTitleIcon` ] (XREF : Xamarin.Forms . NavigationPage. SetTitleIcon ( Xamarin.Forms . BindableObject, Xamarin.Forms . FileImageSource)) et [ `GetTitleIcon` ] (XREF : Xamarin.Forms . NavigationPage. GetTitleIcon ( Xamarin.Forms . BindableObject)) travailler sur iOS et Android uniquement

### <a name="exploring-the-mechanics"></a>Exploration de la mécanique

Les méthodes de navigation entre les pages sont toutes asynchrones et doivent être utilisées avec `await` . La saisie semi-automatique n’indique pas que la navigation entre les pages est terminée, mais uniquement qu’il est possible d’examiner la pile de navigation entre les pages.

Quand une page navigue vers une autre, la première page reçoit généralement un appel à sa [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) méthode, et la deuxième page reçoit un appel à sa [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) méthode. De même, lorsqu’une page est retournée vers une autre, la première page reçoit un appel à sa `OnDisappearing` méthode, et la deuxième page reçoit généralement un appel à sa `OnAppearing` méthode. L’ordre de ces appels (et l’achèvement des méthodes asynchrones qui appellent la navigation) dépend de la plateforme. L’utilisation du mot « généralement » dans les deux instructions précédentes est due à la navigation dans les pages modales Android, dans laquelle ces appels de méthode ne se produisent pas.

En outre, les appels `OnAppearing` aux `OnDisappearing` méthodes et n’indiquent pas nécessairement la navigation entre les pages.

L' `INavigation` interface comprend deux propriétés de collection qui vous permettent d’examiner la pile de navigation :

- [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack) de type `IReadOnlyList<Page>` pour la pile non modale
- [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack) de type `IReadOnlyList<Page>` pour la pile modale

Il est plus sûr d’accéder à ces piles à partir de la `Navigation` propriété de `NavigationPage` (qui doit être la `App` propriété de la classe [`MainPage`](xref:Xamarin.Forms.Application.MainPage) ). Il est possible d’examiner ces piles uniquement en toute sécurité une fois que les méthodes de navigation de page asynchrones sont terminées. La [`CurrentPage`](xref:Xamarin.Forms.NavigationPage.CurrentPage) propriété de `NavigationPage` n’indique pas la page actuelle si la page actuelle est une page modale, mais indique à la place la dernière page non modale.

L’exemple [**SinglePageNavigation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation) vous permet d’explorer la navigation entre les pages et les piles, ainsi que les types juridiques de navigation entre les pages :

- Une page non modale peut naviguer vers une autre page non modale ou une page modale
- Une page modale peut naviguer uniquement vers une autre page modale

### <a name="enforcing-modality"></a>Application de la modalité

Une application utilise une page modale lorsqu’il est nécessaire d’obtenir des informations de l’utilisateur. L’utilisateur doit être autorisé à revenir à la page précédente jusqu’à ce que ces informations soient fournies. Sur iOS, il est facile de fournir un bouton **précédent** et de l’activer uniquement lorsque l’utilisateur a terminé la page. Toutefois, pour les appareils Android et Windows Phone, l’application doit remplacer la [`OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed) méthode et retourner `true` si le programme a géré le bouton **précédent** lui-même, comme illustré dans l’exemple [**ModalEnforcement**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement) .

L’exemple [**MvvmEnforcement**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement) montre comment cela fonctionne dans un scénario MVVM.

## <a name="navigation-variations"></a>Variantes de navigation

Si une page modale particulière peut être parcourue plusieurs fois, elle doit conserver les informations afin que l’utilisateur puisse modifier les informations au lieu de les taper à nouveau. Vous pouvez gérer cela en conservant l’instance particulière de la page modale, mais une meilleure approche (en particulier sur iOS) conserve les informations dans un modèle de vue.

### <a name="making-a-navigation-menu"></a>Création d’un menu de navigation

L’exemple [**ViewGalleryType**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType) illustre l’utilisation `TableView` d’un objet pour répertorier les éléments de menu. Chaque élément est associé à un `Type` objet pour une page particulière. Lorsque cet élément est sélectionné, le programme instancie la page et y accède.

[![Capture triple de la vue de type de la Galerie](images/ch24fg21-small.png "Éléments du menu TableView Listing")](images/ch24fg21-large.png#lightbox "Éléments du menu TableView Listing")

L’exemple [**ViewGalleryInst**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst) est un peu différent en ce que le menu contient des instances de chaque page plutôt que des types. Cela permet de conserver les informations de chaque page, mais toutes les pages doivent être instanciées au démarrage du programme.

### <a name="manipulating-the-navigation-stack"></a>Manipulation de la pile de navigation

[**StackManipulation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation) illustre plusieurs fonctions définies par `INavigation` qui vous permettent de manipuler la pile de navigation de manière structurée :

- [ `RemovePage` ] (XREF : Xamarin.Forms . INavigation. RemovePage ( Xamarin.Forms . Page))
- [ `InsertPageBefore` ] (XREF : Xamarin.Forms . INavigation. InsertPageBefore ( Xamarin.Forms . Page, Xamarin.Forms . Page))
- [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync) et [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync(System.Boolean)) avec une animation facultative

### <a name="dynamic-page-generation"></a>Génération de page dynamique

L’exemple [**BuildAPage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage) illustre la construction d’une page au moment de l’exécution en fonction de l’entrée de l’utilisateur.

## <a name="patterns-of-data-transfer"></a>Modèles de transfert de données

Il est souvent nécessaire de partager des données entre &mdash; les pages pour transférer des données vers une page parcourue, et pour qu’une page retourne des données à la page qui l’a appelée. Il existe plusieurs techniques pour y parvenir.

### <a name="constructor-arguments"></a>Arguments de constructeur

Quand vous naviguez vers une nouvelle page, il est possible d’instancier la classe page avec un argument de constructeur qui permet à la page de s’initialiser. L’exemple [**SchoolAndStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents) illustre cela. Il est également possible que la page parcourue ait sa `BindingContext` valeur définie par la page qui accède à celle-ci.

### <a name="properties-and-method-calls"></a>Propriétés et appels de méthode

Les autres exemples de transfert de données explorent le problème de passage d’informations entre les pages lorsqu’une page navigue vers une autre page et inversement. Dans ces discussions, la page d' *origine* accède *à la page d’informations et* doit transférer les informations initialisées vers *la page d’informations.* La *page d’informations permet* d’obtenir des informations supplémentaires de la part de l’utilisateur et de transférer les informations vers la page d' *hébergement* .

La page d' *hébergement* peut accéder facilement aux méthodes et aux propriétés publiques dans la page d' *informations* dès qu’elle instancie cette page. La page d' *informations* peut également accéder aux méthodes et propriétés publiques dans la page d' *hébergement* , mais le choix d’une bonne heure pour cela peut être délicat. L’exemple [**DateTransfer1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1) le fait dans sa `OnDisappearing` substitution. L’un des inconvénients est que la page d' *informations* doit connaître le type de la page d' *hébergement* .

### <a name="messagingcenter"></a>MessagingCenter

La Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe fournit une autre façon pour que deux pages communiquent entre elles. Les messages sont identifiés par une chaîne de texte et peuvent être accompagnés d’un objet.

Un programme qui souhaite recevoir des messages d’un type particulier doit s’abonner à ceux-ci à l’aide [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) de et spécifier une fonction de rappel. Plus tard, il peut se désabonner en appelant [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) . La fonction de rappel reçoit tout message envoyé à partir du type spécifié avec le nom spécifié envoyé via la [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) méthode.

Le programme [**DateTransfer2**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2) montre comment transférer des données à l’aide du centre de messagerie, mais cela requiert que la page d' *informations* connaisse le type de la page d' *hébergement* .

### <a name="events"></a>Événements

L’événement est une approche à durée respectée pour qu’une classe envoie des informations à une autre classe sans connaître le type de cette classe. Dans l’exemple [**DateTransfer3**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3) , la classe *info* définit un événement qui est déclenché lorsque les informations sont prêtes. Toutefois, il n’y a pas d’emplacement pratique pour que la page d' *Accueil* détache le gestionnaire d’événements.

### <a name="the-app-class-intermediary"></a>Classe d’application intermédiaire

L’exemple [**DateTransfer4**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4) montre comment accéder aux propriétés définies dans la `App` classe à la fois sur la page d' *hébergement* et sur la page d' *informations* . Il s’agit d’une bonne solution, mais la section suivante décrit un peu mieux.

### <a name="switching-to-a-viewmodel"></a>Basculer vers un ViewModel

L’utilisation d’un ViewModel pour les informations permet à la page d' *hébergement* et à *la page d’informations de* partager l’instance de la classe d’informations. Cela est illustré dans l’exemple [**DateTransfer5**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5) .

### <a name="saving-and-restoring-page-state"></a>Enregistrement et restauration de l’état de la page

L' `App` approche de classe intermédiaire ou ViewModel est idéale lorsque l’application doit enregistrer des informations si le programme passe en mode veille alors que la page d' *informations* est active. L’exemple [**DateTransfer6**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6) illustre cela.

## <a name="saving-and-restoring-the-navigation-stack"></a>Enregistrement et restauration de la pile de navigation

Dans le cas général, un programme multipage qui passe en mode veille doit accéder à la même page lors de sa restauration. Cela signifie qu’un tel programme doit enregistrer le contenu de la pile de navigation. Cette section montre comment automatiser ce processus dans une classe conçue à cet effet. Cette classe appelle également les pages individuelles pour leur permettre d’enregistrer et de restaurer leur état de page.

La bibliothèque [**Xamarin.Forms book. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) définit une interface nommée [`IPersistantPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs) que les classes peuvent implémenter pour enregistrer et restaurer des éléments dans le `Properties` dictionnaire.

La [`MultiPageRestorableApp`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs) classe de la bibliothèque **Xamarin.Forms book. Toolkit** dérive de `Application` . Vous pouvez ensuite dériver votre `App` classe de `MultiPageRestorableApp` et effectuer des nettoyages.

Le [**StackRestoreDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo) illustre l’utilisation de `MultiPageRestorableApp` .

### <a name="something-like-a-real-life-app"></a>Un peu comme une application de vie réelle

L’exemple [**NoteTaker**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker) utilise également `MultiPageRestorableApp` et permet d’entrer et de modifier des notes enregistrées dans le `Properties` dictionnaire.

## <a name="related-links"></a>Liens connexes

- [Texte intégral du chapitre 24 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [Chapitre 24 exemples](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [Navigation hiérarchique](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Pages modales](~/xamarin-forms/app-fundamentals/navigation/modal.md)
