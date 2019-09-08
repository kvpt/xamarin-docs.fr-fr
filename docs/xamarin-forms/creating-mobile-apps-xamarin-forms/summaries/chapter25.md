---
title: Résumé du chapitre 25. Types de page
description: 'Création d’Mobile Apps avec Xamarin. Forms : Résumé du chapitre 25. Types de page'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D1D348F2-6A44-4781-ADCE-A0B7BB9AEF89
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: b86f2d7216a6344b14fc4d8c538ea68871eda5ae
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760536"
---
# <a name="summary-of-chapter-25-page-varieties"></a>Résumé du chapitre 25. Types de page

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)

Jusqu'à présent, vous avez vu deux classes qui dérivent de `Page`: `ContentPage` et `NavigationPage`. Ce chapitre présente deux autres :

- [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) gère les deux pages, un serveur maître et un détail
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) gère plusieurs pages enfants accédés via les onglets

Ces types de page fournissent les options de navigation plus sophistiquées que la `NavagationPage` abordés dans [chapitre 24. Navigation entre les pages](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter24.md).

## <a name="master-and-detail"></a>Maître / détail

Le [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) définit deux propriétés de type `Page`: [ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) et [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail). En règle générale, vous définissez chacune de ces propriétés pour un `ContentPage`. Le `MasterDetailPage` affiche et permet de basculer entre ces deux pages.

Il existe deux méthodes fondamentales pour basculer entre ces deux pages :

- *Fractionner* où maître et détail sont côte à côte
- *menu* où la page de détails couvre ou couvre partiellement le contrôleur de page

Il existe plusieurs variantes de la *menu* approche (*diapositive*, *se chevauchent*, et *échange*), mais il s’agit généralement de plateforme dépendants. Vous pouvez définir le [ `MasterDetailBehavior` ](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) propriété du `MasterDetailPage` à un membre de la [ `MasterBehavior` ](xref:Xamarin.Forms.MasterBehavior) énumération :

- [`Default`](xref:Xamarin.Forms.MasterBehavior.Default)
- [`Split`](xref:Xamarin.Forms.MasterBehavior.Split)
- [`SplitOnLandscape`](xref:Xamarin.Forms.MasterBehavior.SplitOnLandscape)
- [`SplitOnPortrait`](xref:Xamarin.Forms.MasterBehavior.SplitOnPortrait)
- [`Popover`](xref:Xamarin.Forms.MasterBehavior.Popover)

Toutefois, cette propriété n’a aucun effet sur les téléphones. Les téléphones ont toujours un comportement de menu. Seuls les tablettes et les postes de travail windows peuvent avoir un comportement de fractionnement.

### <a name="exploring-the-behaviors"></a>Analyse des comportements

Le [ **MasterDetailBehaviors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailBehaviors) exemple vous permet de faire des essais avec le comportement par défaut sur différents appareils. Le programme contient deux distinct `ContentPage` dérivés pour maître et détail (avec un `Title` propriété définie sur les deux) et une autre classe qui dérive de `MasterDetailPage` qui combine les. La page de détails est placée dans un `NavigationPage` , car le programme UWP ne fonctionnerait pas sans lui.

Les plateformes Windows 8.1 et Windows Phone 8.1 exigent qu’une image bitmap soit définie sur le `Icon` propriété de la page maître.

### <a name="back-to-school"></a>Retour à l’école

Le [ **SchoolAndDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/SchoolAndDetail) exemple adopte une approche un peu différente à construire le programme affiche les étudiants à partir de la [ **SchoolOfFineArt** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) bibliothèque.

Le `Master` et `Detail` propriétés sont définies avec des arborescences d’éléments visuels dans le [SchoolAndDetailPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml) fichier, qui dérive de `MasterDetailPage`. Ceci permet des liaisons de données à définir entre les pages maître et détail.

Que les fichiers XAML définit également la [ `IsPresented` ](xref:Xamarin.Forms.MasterDetailPage.IsPresented) propriété du `MasterDetailPage` à `True`. Cela entraîne la page maître à afficher au démarrage. par défaut, la page de détails s’affiche. Le [SchoolAndDetailPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml.cs) fichier définit `IsPresented` à `false` lorsqu’un élément est sélectionné à partir de la `ListView` dans la page maître. La page de détails s’affiche ensuite :

[![Capture d’écran triple de détail et de l’école](images/ch25fg09-small.png "Page à partir d’un MasterDetailPage")](images/ch25fg09-large.png#lightbox "Page à partir d’un MasterDetailPage")

### <a name="your-own-user-interface"></a>Votre propre interface utilisateur

Bien que Xamarin.Forms fournit une interface utilisateur pour basculer entre les vues maître et détail, vous pouvez fournir votre propre. Pour ce faire :

- Définir le [ `IsGestureEnabled` ](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabled) propriété `false` pour désactiver le balayage
- Remplacer le [ `ShouldShowToolbarButton` ](xref:Xamarin.Forms.MasterDetailPage.ShouldShowToolbarButton) méthode et retournez `false` pour masquer les boutons de barre d’outils de Windows 8.1 et Windows Phone 8.1.

Vous devez ensuite fournir un moyen de basculer entre les pages maître et détail, comme illustré par la [ **ColorsDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/ColorsDetails) exemple.

Le [ **MasterDetailTaps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailTaps) exemple montre comment utiliser une autre approche un `TapGestureRecognizer` sur les pages maître et détail.

## <a name="tabbedpage"></a>TabbedPage

Le [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) est une collection de pages que vous pouvez passer à l’aide des onglets. Il dérive `MultiPage<Page>` et ne définit aucun propriétés ou méthodes publiques qui lui sont propres. [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1), toutefois, définit une propriété :

- [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) propriété de type `IList<T>`

Vous renseignez ceci `Children` collection avec des objets de la page.

Une autre approche vous permet de définir le `TabbedPage` enfants fonctionne comme un `ListView` à l’aide de ces deux propriétés qui génèrent automatiquement des pages à onglets :

- [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) de type `IEnumerable`
- [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) de type `DataTemplate`

Toutefois, cette approche ne fonctionne pas correctement sur iOS lors de la collection contient plusieurs éléments.

`MultiPage<T>` définit les propriétés vous permettent d’effectuer le suivi de page qui est actuellement affichée :

- [`CurrentPage`](xref:Xamarin.Forms.MultiPage`1.CurrentPage) de type `T`, qui fait référence à la page
- [`SelectedItem`](xref:Xamarin.Forms.MultiPage`1.SelectedItem) de type `Object`, qui fait référence à l’objet dans le `ItemsSource` collection

`MultiPage<T>` définit également deux événements :

- [`PagesChanged`](xref:Xamarin.Forms.MultiPage`1.PagesChanged) Lorsque le `ItemsSource` modifications de la collection
- [`CurrentPageChanged`](xref:Xamarin.Forms.MultiPage`1.CurrentPageChanged) Lorsque la page affichée change

### <a name="discrete-tab-pages"></a>Pages d’onglets discrètes

Le [ **DiscreteTabbedColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/DiscreteTabbedColors) exemple se compose de trois pages à onglets qui affichent les couleurs de trois façons différentes. Chaque onglet est un `ContentPage` dérivé, puis le `TabbedPage` dérivé [DiscreteTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColorsPage.xaml) combine les trois pages.

Pour chaque page qui s’affiche dans un `TabbedPage`, le `Title` propriété est requise pour spécifier le texte dans l’onglet, et le Store d’Apple nécessite qu’une icône également être utilisé, par conséquent, le `Icon` propriété est définie pour iOS :

[![Capture d’écran triple de couleurs avec onglet discrètes](images/ch25fg13-small.png "TabbedPage")](images/ch25fg13-large.png#lightbox "TabbedPage")

Le [ **StudentNotes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/StudentNotes) exemple comporte une page d’accueil qui répertorie tous les étudiants. Lors de l’appui sur un étudiant, il accède à un `TabbedPage` dérivé, [ `StudentNotesDataPage` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/StudentNotes/StudentNotes/StudentNotes/StudentNotesDataPage.xaml), qui intègre trois `ContentPage` objets dans son arborescence d’éléments visuels, un d'entre eux permet d’entrer des remarques pour que les élèves.

### <a name="using-an-itemtemplate"></a>À l’aide d’un modèle ItemTemplate

Le [ **MultiTabbedColor** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MultiTabbedColors) exemple utilise le [ `NamedColor` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) classe dans le [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) bibliothèque. Le [MultiTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/MultiTabbedColors/MultiTabbedColors/MultiTabbedColors/MultiTabbedColorsPage.xaml) fichier définit les `DataTemplate` propriété de `TabbedPage` vers un début de l’arborescence d’éléments visuels avec `ContentPage` qui contient des liaisons aux propriétés de `NamedColor` (y compris une liaison à la `Title` propriété).

Toutefois, cela peut être problématique sur iOS. Quelques-uns des éléments peuvent être affichées, et il n’existe aucun bon moyen de leur donner des icônes.

## <a name="related-links"></a>Liens connexes

- [Chapitre 25 de texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch25-Apr2016.pdf)
- [Exemples du chapitre 25](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)
- [Page maître / détails](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)
- [Page à onglets](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
