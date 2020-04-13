---
title: Résumé du chapitre 25. Variétés de pages
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 25. Variétés de pages'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D1D348F2-6A44-4781-ADCE-A0B7BB9AEF89
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: b86f2d7216a6344b14fc4d8c538ea68871eda5ae
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760536"
---
# <a name="summary-of-chapter-25-page-varieties"></a>Résumé du chapitre 25. Variétés de pages

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)

Jusqu’à présent, vous avez `Page`vu `ContentPage` `NavigationPage`deux classes qui dérivent de : et . Ce chapitre en présente deux autres :

- [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)gère deux pages, un maître et un détail
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)gère plusieurs pages d’enfants accessibles par des onglets

Ces types de pages offrent `NavagationPage` des options de navigation plus sophistiquées que celles discutées au [chapitre 24. Page Navigation](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter24.md).

## <a name="master-and-detail"></a>Maître et détail

Le [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) définit deux propriétés [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail)de type `Page`: et . Généralement, vous définissez chacune de ces propriétés à un `ContentPage`. Les `MasterDetailPage` affichages et les commutateurs entre ces deux pages.

Il existe deux façons fondamentales de basculer entre ces deux pages :

- *diviser* où le maître et le détail sont côte à côte
- *popover* où la page de détail couvre ou couvre partiellement la page principale

Il existe plusieurs variantes de *l’approche popover* *(slide*, *chevauchement*, et *swap*), mais ceux-ci sont généralement dépendants de la plate-forme. Vous pouvez [`MasterDetailBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) définir `MasterDetailPage` la propriété d’un membre de l’énumération: [`MasterBehavior`](xref:Xamarin.Forms.MasterBehavior)

- [`Default`](xref:Xamarin.Forms.MasterBehavior.Default)
- [`Split`](xref:Xamarin.Forms.MasterBehavior.Split)
- [`SplitOnLandscape`](xref:Xamarin.Forms.MasterBehavior.SplitOnLandscape)
- [`SplitOnPortrait`](xref:Xamarin.Forms.MasterBehavior.SplitOnPortrait)
- [`Popover`](xref:Xamarin.Forms.MasterBehavior.Popover)

Cependant, cette propriété n’a aucun effet sur les téléphones. Les téléphones ont toujours un comportement popover. Seules les tablettes et les fenêtres de bureau peuvent avoir un comportement partagé.

### <a name="exploring-the-behaviors"></a>Explorer les comportements

[**L’échantillon MasterDetailBehaviors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailBehaviors) vous permet d’expérimenter le comportement par défaut sur différents appareils. Le programme contient `ContentPage` deux dérivés distincts pour `Title` le maître et le détail (avec une propriété fixée sur les deux), et une autre classe qui dérive de `MasterDetailPage` cela les combine. La page de détail `NavigationPage` est incluse dans un parce que le programme UWP ne fonctionnerait pas sans elle.

Les plates-formes Windows 8.1 et Windows Phone 8.1 `Icon` exigent qu’un bitmap soit réglé à la propriété de la page principale.

### <a name="back-to-school"></a>Retour à l'école

[**L’échantillon SchoolAndDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/SchoolAndDetail) adopte une approche quelque peu différente pour construire le programme pour exposer les élèves de la bibliothèque [**SchoolOfFineArt.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt)

Les `Master` `Detail` propriétés et les propriétés sont définies avec des arbres visuels `MasterDetailPage`dans le fichier [SchoolAndDetailPage.xaml,](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml) qui dérive de . Cet arrangement permet d’établir des liaisons de données entre les pages du maître et des détails.

Ce fichier XAML [`IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) définit `MasterDetailPage` également `True`la propriété de . Cela provoque l’affichage de la page principale au démarrage; par défaut, la page de détail est affichée. Le [fichier SchoolAndDetailPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml.cs) `IsPresented` s’établit au `false` moment `ListView` où un élément est sélectionné à partir de la page principale. La page de détail est alors affichée :

[![Triple capture d’écran de School And Detail](images/ch25fg09-small.png "Page de détail d’un MasterDetailPage")](images/ch25fg09-large.png#lightbox "Page de détail d’un MasterDetailPage")

### <a name="your-own-user-interface"></a>Votre propre interface utilisateur

Bien que Xamarin.Forms fournit une interface utilisateur pour passer entre le maître et les vues détaillées, vous pouvez fournir votre propre. Pour ce faire :

- Définissez [`IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabled) la `false` propriété pour désactiver le balayage
- Remplacez [`ShouldShowToolbarButton`](xref:Xamarin.Forms.MasterDetailPage.ShouldShowToolbarButton) la méthode `false` et retournez pour masquer les boutons de la barre d’outils sur Windows 8.1 et Windows Phone 8.1.

Vous devez ensuite fournir un moyen de basculer entre les pages de maître et de détail, comme démontré par l’échantillon [**ColorsDetail.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/ColorsDetails)

[**L’échantillon MasterDetailTaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailTaps) démontre une `TapGestureRecognizer` autre approche à l’aide d’un sur le maître et des pages de détail.

## <a name="tabbedpage"></a>TabbedPage

Il [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) s’agit d’une collection de pages que vous pouvez basculer entre l’utilisation d’onglets. Il dérive `MultiPage<Page>` et ne définit pas de propriétés publiques ou de méthodes qui lui sont propres. [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1), cependant, définit une propriété :

- [`Children`](xref:Xamarin.Forms.MultiPage`1.Children)propriété de type`IList<T>`

Vous remplissez cette `Children` collection d’objets de page.

Une autre approche vous `TabbedPage` permet de `ListView` définir les enfants un peu comme une utilisation de ces deux propriétés qui génèrent les pages tabbed automatiquement:

- [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource)de type`IEnumerable`
- [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate)de type`DataTemplate`

Cependant, cette approche ne fonctionne pas bien sur iOS lorsque la collection contient plus de quelques éléments.

`MultiPage<T>`définit deux autres propriétés qui vous permettent de garder une trace de la page qui est actuellement consultée :

- [`CurrentPage`](xref:Xamarin.Forms.MultiPage`1.CurrentPage)de `T`type , se référant à la page
- [`SelectedItem`](xref:Xamarin.Forms.MultiPage`1.SelectedItem)type `Object`, se référant `ItemsSource` à l’objet de la collection

`MultiPage<T>`définit également deux événements :

- [`PagesChanged`](xref:Xamarin.Forms.MultiPage`1.PagesChanged)lorsque `ItemsSource` la collection change
- [`CurrentPageChanged`](xref:Xamarin.Forms.MultiPage`1.CurrentPageChanged)lorsque la page consultée change

### <a name="discrete-tab-pages"></a>Pages d’onglets discrètes

[**L’échantillon DiscreteTabbedColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/DiscreteTabbedColors) se compose de trois pages tabbed qui affichent des couleurs de trois façons différentes. Chaque onglet `ContentPage` est un dérivé, puis le `TabbedPage` dérivé [DiscreteTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColorsPage.xaml) combine les trois pages.

Pour chaque page qui `TabbedPage`apparaît `Title` dans un , la propriété est tenue de spécifier le texte `Icon` dans l’onglet, et l’Apple Store exige qu’une icône soit utilisée ainsi, de sorte que la propriété est définie pour iOS:

[![Triple capture d’écran de Discrete Tabbed Colors](images/ch25fg13-small.png "TabbedPage")](images/ch25fg13-large.png#lightbox "TabbedPage")

[**L’échantillon StudentNotes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/StudentNotes) a une page d’accueil qui répertorie tous les élèves. Quand un étudiant est exploité, cela `TabbedPage` navigue vers un dérivé, [`StudentNotesDataPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/StudentNotes/StudentNotes/StudentNotes/StudentNotesDataPage.xaml), qui intègre trois `ContentPage` objets dans son arbre visuel, dont l’un permet d’entrer quelques notes pour cet étudiant.

### <a name="using-an-itemtemplate"></a>Utilisation d’un ItemTemplate

[**L’échantillon MultiTabbedColor**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MultiTabbedColors) utilise la [`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) classe dans la bibliothèque [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) Le fichier [MultiTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/MultiTabbedColors/MultiTabbedColors/MultiTabbedColors/MultiTabbedColorsPage.xaml) `DataTemplate` définit `TabbedPage` la propriété d’un arbre visuel commençant par `ContentPage` qui contient des fixations aux propriétés de `NamedColor` (y compris une liaison à la `Title` propriété).

Cependant, cela est problématique sur iOS. Seuls quelques-uns des éléments peuvent être affichés, et il n’y a aucun bon moyen de leur donner des icônes.

## <a name="related-links"></a>Liens connexes

- [Chapitre 25 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch25-Apr2016.pdf)
- [Échantillons du chapitre 25](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)
- [Master-Detail Page](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)
- [Tabbed Page](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
