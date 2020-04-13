---
title: Résumé du chapitre 24. Navigation entre les pages
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 24. Navigation entre les pages'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: DDCDB49C-6008-4F72-B095-463EE21D7C23
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: fd8e4fc77917fcba9bc61e59ced714ac1cd6fbe9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292820"
---
# <a name="summary-of-chapter-24-page-navigation"></a>Résumé du chapitre 24. Navigation entre les pages

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)

De nombreuses applications se composent de plusieurs pages parmi lesquelles l’utilisateur navigue. L’application a toujours une page *principale* ou une page *d’accueil,* et de là l’utilisateur navigue vers d’autres pages, qui sont maintenues dans une pile pour naviguer en arrière. D’autres options de navigation sont couvertes dans [**le chapitre 25. Variétés de page**](chapter25.md).

## <a name="modal-pages-and-modeless-pages"></a>Pages modales et pages sans mode

`VisualElement`définit une [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriété [`INavigation`](xref:Xamarin.Forms.INavigation)de type , qui comprend les deux méthodes suivantes pour naviguer vers une nouvelle page :

- [`PushAsync`](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page))
- [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page))

Les deux `Page` méthodes acceptent une instance `Task` comme argument et renvoient un objet. Les deux méthodes suivantes reviennent à la page précédente :

- [`PopAsync`](xref:Xamarin.Forms.INavigation.PopAsync)
- [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync)

Si l’interface utilisateur a son propre bouton **Back** (comme le font les téléphones Android et Windows), il n’est pas nécessaire que l’application appelle ces méthodes.

Bien que ces méthodes `VisualElement`soient disponibles à `Navigation` partir de `Page` n’importe quel , généralement elles sont appelées à partir de la propriété de l’instance actuelle.

Les applications utilisent généralement des pages modales lorsque l’utilisateur est tenu de fournir certaines informations sur la page avant de revenir à la page précédente. Les pages qui ne sont pas modales sont parfois appelées modeless ou *hiérarchique.* Rien dans la page elle-même ne le distingue comme modal ou sans mode; il est plutôt régi par la méthode utilisée pour y naviguer. Pour travailler sur toutes les plateformes, une page modale doit fournir sa propre interface utilisateur pour naviguer vers la page précédente.

[**L’échantillon ModelessAndModal**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal) vous permet d’explorer la différence entre les pages sans mode et les pages modales. Toute application qui utilise la [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) navigation de page doit transmettre sa page `App` d’accueil au constructeur, généralement dans la classe du programme. Un bonus est que vous n’avez plus besoin de définir un `Padding` sur la page pour iOS.

Vous découvrirez que pour les pages [`Title`](xref:Xamarin.Forms.Page.Title) sans mode, la propriété de la page est affichée. L’iOS, Android, et les tablettes Et plates-formes de bureau Windows fournissent tous un élément d’interface utilisateur pour naviguer vers la page précédente. Bien sûr, Android, et les appareils de téléphone Windows ont un bouton **De retour** standard pour revenir en arrière.

Pour les pages modales, la page `Title` n’est pas affichée, et aucun élément utilisateur-interface n’est fourni pour revenir à la page précédente. Bien que vous puissiez utiliser le bouton Android et Windows Phone Standard **Back** pour revenir à la page précédente, la page modale sur les autres plates-formes doit fournir son propre mécanisme pour revenir en arrière.

### <a name="animated-page-transitions"></a>Transitions de pages animées

Les versions alternatives des différentes méthodes de navigation sont fournies `true` avec un deuxième argument Boolean que vous définissez si vous voulez la transition de page pour inclure une animation :

- [PushAsync](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page,System.Boolean))
- [PushModalAsync](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page,System.Boolean))
- [PopAsync PopAsync](xref:Xamarin.Forms.INavigation.PopAsync(System.Boolean))
- [PopModalAsync](xref:Xamarin.Forms.INavigation.PopModalAsync(System.Boolean))

Cependant, les méthodes standard de navigation de page incluent l’animation par défaut, ainsi elles ne sont valables que pour naviguer à une page particulière sur le démarrage (comme discuté vers la fin de ce chapitre) ou lors de la fourniture de votre propre animation d’entrée (comme discuté dans [**le chapitre22. Animation**](chapter22.md)).

### <a name="visual-and-functional-variations"></a>Variations visuelles et fonctionnelles

`NavigationPage`comprend deux propriétés que vous pouvez définir lorsque `App` vous instantanéez la classe dans votre méthode :

- [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)
- [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor)

`NavigationPage`comprend également quatre propriétés liantes attachées qui affectent la page particulière sur laquelle elles sont définies :

- [`SetHasBackButton`](xref:Xamarin.Forms.NavigationPage.SetHasBackButton(Xamarin.Forms.Page,System.Boolean))Et[`GetHasBackButton`](xref:Xamarin.Forms.NavigationPage.GetHasBackButton(Xamarin.Forms.Page))
- [`SetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.SetHasNavigationBar(Xamarin.Forms.BindableObject,System.Boolean))Et[`GetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.GetHasNavigationBar(Xamarin.Forms.BindableObject))
- [`SetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.SetBackButtonTitle(Xamarin.Forms.BindableObject,System.String))et [`GetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.GetBackButtonTitle(Xamarin.Forms.BindableObject)) travailler sur iOS seulement
- [`SetTitleIcon`](xref:Xamarin.Forms.NavigationPage.SetTitleIcon(Xamarin.Forms.BindableObject,Xamarin.Forms.FileImageSource))et [`GetTitleIcon`](xref:Xamarin.Forms.NavigationPage.GetTitleIcon(Xamarin.Forms.BindableObject)) travailler sur iOS et Android seulement

### <a name="exploring-the-mechanics"></a>Explorer la mécanique

Les méthodes de navigation de page sont toutes `await`asynchrones et doivent être utilisées avec . L’achèvement n’indique pas que la navigation de page est terminée, mais seulement qu’il est sûr d’examiner la pile de navigation de page.

Lorsqu’une page navigue vers une autre, la [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) première page reçoit généralement un [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) appel à sa méthode, et la deuxième page reçoit un appel à sa méthode. De même, lorsqu’une page revient à une `OnDisappearing` autre, la première page reçoit `OnAppearing` un appel à sa méthode, et la deuxième page reçoit généralement un appel à sa méthode. L’ordre de ces appels (et l’achèvement des méthodes asynchrones qui invoque la navigation) dépend de la plate-forme. L’utilisation du mot "généralement" dans les deux instructions précédentes est due à la navigation de page modale Android, dans laquelle ces appels de méthode ne se produisent pas.

En outre, `OnAppearing` les `OnDisappearing` appels vers le et les méthodes n’indiquent pas nécessairement la navigation de page.

L’interface `INavigation` comprend deux propriétés de collecte qui vous permettent d’examiner la pile de navigation :

- [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack)de `IReadOnlyList<Page>` type pour la pile sans mode
- [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack)de `IReadOnlyList<Page>` type pour la pile modale

Il est plus sûr d’accéder `Navigation` à `NavigationPage` ces piles à `App` partir [`MainPage`](xref:Xamarin.Forms.Application.MainPage) de la propriété de la (qui devrait être la propriété de la classe). Il n’est sûr d’examiner ces piles qu’une fois les méthodes asynchrones de navigation de page terminées. La [`CurrentPage`](xref:Xamarin.Forms.NavigationPage.CurrentPage) propriété `NavigationPage` de la n’indique pas la page actuelle si la page actuelle est une page modale, mais indique plutôt la dernière page sans mode.

[**L’échantillon SinglePageNavigation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation) vous permet d’explorer la navigation de page et les piles, ainsi que les types légaux de navigations de pages :

- Une page sans mode peut naviguer vers une autre page sans mode ou une page modale
- Une page modale ne peut naviguer que vers une autre page modale

### <a name="enforcing-modality"></a>Faire respecter la modalité

Une application utilise une page modale lorsqu’il est nécessaire d’obtenir des informations de l’utilisateur. Il devrait être interdit à l’utilisateur de revenir à la page précédente jusqu’à ce que ces informations soient fournies. Sur iOS, il est facile de fournir un bouton **Back** et de l’activer uniquement lorsque l’utilisateur a terminé la page. Mais pour les appareils Android et Windows, l’application doit [`OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed) remplacer la méthode et le retour `true` si le programme a manipulé le bouton **Back** lui-même, comme démontré dans l’échantillon [**ModalEnforcement.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement)

[**L’échantillon MvvmEnforcement**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement) montre comment cela fonctionne dans un scénario MVVM.

## <a name="navigation-variations"></a>Variations de navigation

Si une page modale particulière peut être naviguée à plusieurs reprises, elle doit conserver des informations afin que l’utilisateur puisse modifier les informations plutôt que de taper le tout à nouveau. Vous pouvez gérer cela en conservant l’exemple particulier de la page modale, mais une meilleure approche (en particulier sur iOS) est de préserver les informations dans un modèle de vue.

### <a name="making-a-navigation-menu"></a>Faire un menu de navigation

[**L’échantillon ViewGalleryType**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType) démontre `TableView` l’utilisation d’un menu pour énumérer les éléments de menu. Chaque élément est `Type` associé à un objet pour une page particulière. Lorsque cet élément est sélectionné, le programme instantané la page et navigue à elle.

[![Triple capture d’écran de View Gallery Type](images/ch24fg21-small.png "Articles de menu de liste TableView")](images/ch24fg21-large.png#lightbox "Articles de menu de liste TableView")

[**L’échantillon ViewGalleryInst**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst) est un peu différent en ce que le menu contient des instances de chaque page plutôt que des types. Cela permet de conserver les informations de chaque page, mais toutes les pages doivent être instantanées au démarrage du programme.

### <a name="manipulating-the-navigation-stack"></a>Manipulation de la pile de navigation

[**StackManipulation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation) démontre plusieurs fonctions `INavigation` définies par celle vous permettent de manipuler la pile de navigation d’une manière structurée :

- [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page))
- [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page))
- [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync)et [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync(System.Boolean)) avec animation facultative

### <a name="dynamic-page-generation"></a>Génération dynamique de page

[**L’échantillon BuildAPage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage) démontre la construction d’une page à l’heure d’exécution en fonction de l’entrée de l’utilisateur.

## <a name="patterns-of-data-transfer"></a>Modèles de transfert de données

Il est souvent nécessaire de &mdash; partager des données entre les pages pour transférer des données vers une page parcourue, et pour qu’une page retourne les données à la page qui les invoquait. Il existe plusieurs techniques pour ce faire.

### <a name="constructor-arguments"></a>Arguments constructeurs

Lorsque vous naviguez vers une nouvelle page, il est possible d’instantanéiser la classe de page avec un argument constructeur qui permet à la page de se parasifier. [**L’échantillon SchoolAndStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents) le démontre. Il est également possible pour la page `BindingContext` parcourue d’avoir son ensemble par la page qui navigue à elle.

### <a name="properties-and-method-calls"></a>Propriétés et appels de méthode

Les autres exemples de transfert de données explorent le problème de la transmission d’informations entre les pages lorsqu’une page navigue vers une autre page et retour. Au cours de ces discussions, la page *d’accueil* se dirige vers la page *d’information* et doit transférer des informations initiales à la page *d’information.* La page *d’information* obtient des informations supplémentaires de l’utilisateur et transfère les informations à la page *d’accueil.*

La page *d’accueil* peut facilement accéder aux méthodes et aux propriétés publiques dans la page *d’information* dès qu’elle s’adaux. La page *d’information* peut également accéder aux méthodes et aux propriétés publiques de la page *d’accueil,* mais le choix d’un bon moment pour cela peut être difficile. [**L’échantillon DateTransfer1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1) le `OnDisappearing` fait dans son remplacement. Un inconvénient est que la page *d’information* doit connaître le type de la page *d’accueil.*

### <a name="messagingcenter"></a>MessagingCenter

La classe Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) offre un autre moyen pour deux pages de communiquer entre elles. Les messages sont identifiés par une chaîne de texte et peuvent être accompagnés de n’importe quel objet.

Un programme qui souhaite recevoir des messages d’un type particulier doit s’y abonner en utilisant [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) et spécifier une fonction de rappel. Plus tard, il peut [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*)se désabonner en appelant . La fonction de rappel reçoit n’importe quel message envoyé [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) à partir du type spécifié avec le nom spécifié envoyé par la méthode.

Le programme [**DateTransfer2**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2) montre comment transférer des données à l’aide du centre de messagerie, mais encore une fois cela exige que la page *d’information* connaître le type de page *d’accueil.*

### <a name="events"></a>Événements

L’événement est une approche séculaire pour une classe d’envoyer des informations à une autre classe sans connaître le type de cette classe. Dans l’échantillon [**DateTransfer3,**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3) la classe *d’information* définit un événement qu’il déclenche lorsque l’information est prête. Cependant, il n’y a pas d’endroit pratique pour la page *d’accueil* pour détacher le gestionnaire d’événement.

### <a name="the-app-class-intermediary"></a>L’intermédiaire de la classe App

[**L’échantillon DateTransfer4**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4) montre comment accéder `App` aux propriétés définies dans la classe par la page *d’accueil* et la page *d’information.* C’est une bonne solution, mais la section suivante décrit quelque chose de mieux.

### <a name="switching-to-a-viewmodel"></a>Passer à un ViewModel

L’utilisation d’un ViewModel pour les informations permet à la page *d’accueil* et à la page *d’information* de partager l’exemple de la classe d’information. Ceci est démontré dans l’échantillon [**DateTransfer5.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5)

### <a name="saving-and-restoring-page-state"></a>Enregistrement et restauration de l’état de la page

L’intermédiaire `App` de la classe ou l’approche ViewModel est idéal lorsque l’application doit enregistrer des informations si le programme s’endort pendant que la page *d’information* est active. [**L’échantillon DateTransfer6**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6) le démontre.

## <a name="saving-and-restoring-the-navigation-stack"></a>Enregistrement et restauration de la pile de navigation

Dans le cas général, un programme multipage qui s’endort devrait naviguer jusqu’à la même page lorsqu’il est restauré. Cela signifie qu’un tel programme devrait enregistrer le contenu de la pile de navigation. Cette section montre comment automatiser ce processus dans une classe conçue à cet effet. Cette classe appelle également les pages individuelles pour leur permettre d’enregistrer et de restaurer leur état de page.

La bibliothèque [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) définit [`IPersistantPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs) une interface nommée que les classes `Properties` peuvent mettre en œuvre pour enregistrer et restaurer les éléments du dictionnaire.

La [`MultiPageRestorableApp`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs) classe de la bibliothèque **Xamarin.FormsBook.Toolkit** dérive de `Application`. Vous pouvez alors `App` tirer `MultiPageRestorableApp` votre classe et effectuer un peu d’entretien ménager.

Le [**StackRestoreDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo) démontre l’utilisation de `MultiPageRestorableApp`.

### <a name="something-like-a-real-life-app"></a>Quelque chose comme une application de la vie réelle

[**L’échantillon NoteTaker**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker) utilise `MultiPageRestorableApp` également et permet l’entrée et l’édition de notes qui sont enregistrées dans le `Properties` dictionnaire.

## <a name="related-links"></a>Liens connexes

- [Chapitre 24 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [Échantillons du chapitre 24](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [Navigation hiérarchique](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Pages modales](~/xamarin-forms/app-fundamentals/navigation/modal.md)
