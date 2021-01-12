---
title: Résumé du chapitre 25. Variétés de page
description: 'Création d’Mobile Apps avec Xamarin.Forms : Résumé du chapitre 25. Variétés de page'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D1D348F2-6A44-4781-ADCE-A0B7BB9AEF89
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d9d0d75a393cc30ffaf698625cd49cf0ce5b5741
ms.sourcegitcommit: 1decf2c65dc4c36513f7dd459a5df01e170a036f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98115260"
---
# <a name="summary-of-chapter-25-page-varieties"></a>Résumé du chapitre 25. Variétés de page

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)

> [!NOTE]
> Ce livre a été publié à la printemps de 2016 et n’a pas été mis à jour depuis. Le livre reste très utile, mais certains d’entre eux sont obsolètes et certaines rubriques ne sont plus entièrement correctes ou complètes.

Jusqu’à présent, vous avez vu deux classes qui dérivent de `Page` : `ContentPage` et `NavigationPage` . Ce chapitre présente deux autres :

- [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) gère deux pages, un maître et un détail
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) gère plusieurs pages enfants accessibles via les onglets

Ces types de pages fournissent des options de navigation plus sophistiquées que celles `NavagationPage` présentées au [chapitre 24. Navigation entre les pages](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter24.md).

## <a name="master-and-detail"></a>Maître et détail

[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)Définit deux propriétés de type `Page` : [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) et `Detail` . En général, vous définissez chacune de ces propriétés sur un `ContentPage` . Le `MasterDetailPage` s’affiche et passe d’une page à l’autre.

Il existe deux façons fondamentales de basculer entre ces deux pages :

- *fractionnement* où le maître et le détail sont côte à côte
- *menu segue* où la page de détails couvre ou partiellement la page maître

Il existe plusieurs variantes de l’approche *menu segue* (*diapositive*, *chevauchement* et *échange*), mais celles-ci dépendent généralement de la plateforme. Vous pouvez affecter [`MasterDetailBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) à la propriété de `MasterDetailPage` la valeur un membre de l' [`MasterBehavior`](xref:Xamarin.Forms.MasterBehavior) énumération :

- [`Default`](xref:Xamarin.Forms.MasterBehavior.Default)
- [`Split`](xref:Xamarin.Forms.MasterBehavior.Split)
- [`SplitOnLandscape`](xref:Xamarin.Forms.MasterBehavior.SplitOnLandscape)
- [`SplitOnPortrait`](xref:Xamarin.Forms.MasterBehavior.SplitOnPortrait)
- [`Popover`](xref:Xamarin.Forms.MasterBehavior.Popover)

Toutefois, cette propriété n’a aucun effet sur les téléphones. Les téléphones ont toujours un comportement menu segue. Seuls les tablettes et les fenêtres de bureau peuvent avoir un comportement de fractionnement.

### <a name="exploring-the-behaviors"></a>Exploration des comportements

L’exemple [**MasterDetailBehaviors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailBehaviors) vous permet d’expérimenter le comportement par défaut sur différents appareils. Le programme contient deux `ContentPage` dérivées distinctes pour le maître et le détail (avec une `Title` propriété définie sur les deux), et une autre classe qui dérive de `MasterDetailPage` qui les combine. La page de détails est comprise dans `NavigationPage` , car le programme UWP ne fonctionnerait pas sans lui.

Les plateformes Windows 8.1 et Windows Phone 8,1 requièrent qu’une image bitmap soit définie sur la `Icon` propriété de la page maître.

### <a name="back-to-school"></a>Retour à l'école

L’exemple [**SchoolAndDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/SchoolAndDetail) adopte une approche quelque peu différente pour créer le programme et afficher les élèves à partir de la bibliothèque [**SchoolOfFineArt**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) .

Les `Master` `Detail` Propriétés et sont définies avec des arborescences d’éléments visuels dans le fichier [SchoolAndDetailPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml) , qui dérive de `MasterDetailPage` . Cette organisation permet de définir des liaisons de données entre les pages maître et de détails.

Ce fichier XAML affecte également `IsPresented` à la propriété de la `MasterDetailPage` valeur `True` . La page maître est alors affichée au démarrage ; par défaut, la page de détails s’affiche. Le fichier [SchoolAndDetailPage.Xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml.cs) `IsPresented` est défini sur `false` lorsqu’un élément est sélectionné dans la `ListView` page maître. La page de détails s’affiche alors :

[![Capture triple de l’école et des détails](images/ch25fg09-small.png "Page de détails à partir d’un MasterDetailPage")](images/ch25fg09-large.png#lightbox "Page de détails à partir d’un MasterDetailPage")

### <a name="your-own-user-interface"></a>Votre propre interface utilisateur

Bien que Xamarin.Forms offre une interface utilisateur pour basculer entre les vues maître et détail, vous pouvez fournir les vôtres. Pour ce faire :

- Affectez à la propriété la valeur `IsGestureEnabled` `false` pour désactiver le balayage
- Substituez la `ShouldShowToolbarButton` méthode et retournez `false` pour masquer les boutons de la barre d’outils sur Windows 8.1 et Windows Phone 8,1.

Vous devez ensuite fournir un moyen de basculer entre les pages maître et de détails, comme illustré par l’exemple [**ColorsDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/ColorsDetails) .

L’exemple [**MasterDetailTaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailTaps) illustre une autre approche qui utilise un `TapGestureRecognizer` sur les pages maître et détail.

## <a name="tabbedpage"></a>TabbedPage

L [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 'est une collection de pages que vous pouvez basculer entre les onglets. Il dérive de `MultiPage<Page>` et ne définit aucune propriété ou méthode publique propre. [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1)Toutefois, définit une propriété :

- [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) propriété de type `IList<T>`

Vous remplissez cette `Children` collection avec des objets page.

Une autre approche vous permet de définir les enfants de la même `TabbedPage` manière `ListView` qu’un à l’aide de ces deux propriétés qui génèrent automatiquement les pages à onglets :

- [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) de type `IEnumerable`
- [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) de type `DataTemplate`

Toutefois, cette approche ne fonctionne pas correctement sur iOS lorsque la collection contient plus de quelques éléments.

`MultiPage<T>` définit deux propriétés supplémentaires qui vous permettent d’effectuer le suivi de la page actuellement affichée :

- [`CurrentPage`](xref:Xamarin.Forms.MultiPage`1.CurrentPage) de type `T` , qui fait référence à la page
- [`SelectedItem`](xref:Xamarin.Forms.MultiPage`1.SelectedItem) de type `Object` , qui fait référence à l’objet dans la `ItemsSource` collection

`MultiPage<T>` définit également deux événements :

- [`PagesChanged`](xref:Xamarin.Forms.MultiPage`1.PagesChanged) Quand la `ItemsSource` collection est modifiée
- [`CurrentPageChanged`](xref:Xamarin.Forms.MultiPage`1.CurrentPageChanged) Lorsque la page affichée change

### <a name="discrete-tab-pages"></a>Pages d’onglet discrètes

L’exemple [**DiscreteTabbedColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/DiscreteTabbedColors) se compose de trois pages avec onglets qui affichent des couleurs de trois façons différentes. Chaque onglet est un `ContentPage` dérivé, puis le `TabbedPage` [DiscreteTabbedColorsPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColorsPage.xaml) dérivé combine les trois pages.

Pour chaque page qui s’affiche dans un `TabbedPage` , la `Title` propriété est requise pour spécifier le texte dans l’onglet, et le magasin Apple exige qu’une icône soit également utilisée, donc la `Icon` propriété est définie pour iOS :

[![Capture d’écran triple des couleurs discrètes avec onglet](images/ch25fg13-small.png "TabbedPage")](images/ch25fg13-large.png#lightbox "TabbedPage")

L’exemple [**StudentNotes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/StudentNotes) contient une page d’hébergement qui répertorie tous les élèves. Lorsqu’un étudiant est frappé, il accède à une `TabbedPage` dérivée de, [`StudentNotesDataPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/StudentNotes/StudentNotes/StudentNotes/StudentNotesDataPage.xaml) , qui incorpore trois `ContentPage` objets dans son arborescence d’éléments visuels, l’un d’entre eux permettant d’entrer des notes pour cet étudiant.

### <a name="using-an-itemtemplate"></a>Utilisation d’un ItemTemplate

L’exemple [**MultiTabbedColor**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MultiTabbedColors) utilise la [`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) classe de la bibliothèque [**Xamarin.Forms book. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . Le fichier [MultiTabbedColorsPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/MultiTabbedColors/MultiTabbedColors/MultiTabbedColors/MultiTabbedColorsPage.xaml) affecte `DataTemplate` à la propriété de la valeur d’une arborescence d’éléments visuels à partir de `TabbedPage` `ContentPage` qui contient des liaisons aux propriétés de `NamedColor` (y compris une liaison à la `Title` propriété).

Toutefois, cela pose problème sur iOS. Seuls quelques éléments peuvent être affichés, et il n’existe pas de bonne méthode pour leur attribuer des icônes.

## <a name="related-links"></a>Liens connexes

- [Chapitre 25 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch25-Apr2016.pdf)
- [Chapitre 25 exemples](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)
- [Page maître-détail](~/xamarin-forms/app-fundamentals/navigation/flyoutpage.md)
- [Page à onglets](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
