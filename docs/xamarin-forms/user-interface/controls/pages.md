---
title: Xamarin.Forms Pages
description: Xamarin.Forms Les pages représentent des écrans d’application mobile multiplateforme. Cet article répertorie les pages incluses dans Xamarin.Forms .
ms.prod: xamarin
ms.assetid: 9C8C710F-E312-420B-9324-A7A20CEDB7EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2a3540773aec977c6d17592ff6a0ea1e16acc2b9
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372169"
---
# <a name="no-locxamarinforms-pages"></a>Xamarin.Forms Pages

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Xamarin.Forms Les pages représentent des écrans d’application mobile multiplateforme._

Tous les types de page qui sont décrits ci-dessous dérivent de la Xamarin.Forms [`Page`](xref:Xamarin.Forms.Page) classe. Ces éléments visuels occupent tout ou partie de l’écran. Un `Page` objet représente un `ViewController` dans iOS et un `Page` dans le plateforme Windows universelle. Sur Android, chaque page occupe l’écran comme un `Activity` , mais les Xamarin.Forms pages ne sont *pas* des `Activity` objets.

[![::: No-Loc (Xamarin. Forms) ::: types de page](pages-images/pages-sml.png)](pages-images/pages.png#lightbox "::: No-Loc (Xamarin. Forms) ::: types de page")

## <a name="pages"></a>Pages

Xamarin.Forms prend en charge les types de page suivants :

| Type | Description | Apparence |
| --- | --- | --- |
| `ContentPage` | [`ContentPage`](xref:Xamarin.Forms.ContentPage) est le type de page le plus simple et le plus courant. Affectez [`Content`](xref:Xamarin.Forms.ContentPage.Content) à la propriété un [`View`](views.md) objet unique, qui est le plus souvent un [`Layout`](layouts.md) tel que [`StackLayout`](xref:Xamarin.Forms.StackLayout) , [`Grid`](xref:Xamarin.Forms.Grid) ou [`ScrollView`](xref:Xamarin.Forms.ScrollView) .<br /><br />[Documentation sur les API](xref:Xamarin.Forms.ContentPage) | [![Exemple ContentPage](pages-images/ContentPage.png "Exemple ContentPage")](pages-images/ContentPage-Large.png#lightbox "Exemple ContentPage")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) |
| `MasterDetailPage` | Un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) gère deux volets d’informations. Affectez [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) à la propriété une page qui présente généralement une liste ou un menu. Affectez [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) à la propriété une page présentant un élément sélectionné à partir de la page maître. La [`IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) propriété détermine si la page maître ou de détails est visible.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.MasterDetailPage)  /  [Guide](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)  /  [Exemple](/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage) | [![Exemple MasterDetailPage](pages-images/MasterDetailPage.png "Exemple MasterDetailPage")](pages-images/MasterDetailPage-Large.png#lightbox "Exemple MasterDetailPage")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs) |
| `NavigationPage` | Le [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) gère la navigation entre les autres pages à l’aide d’une architecture basée sur la pile. Lors de l’utilisation de la navigation entre les pages dans votre application, une instance de la page d’hébergement doit être passée au constructeur d’un `NavigationPage` objet.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.NavigationPage)  /  [Guide](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)  /  [Exemples 1](/samples/xamarin/xamarin-forms-samples/navigation-hierarchical), [2](/samples/xamarin/xamarin-forms-samples/navigation-passingdata)et [3](/samples/xamarin/xamarin-forms-samples/navigation-loginflow)  | [![Exemple NavigationPage](pages-images/NavigationPage.png "Exemple NavigationPage")](pages-images/NavigationPage-Large.png#lightbox "Exemple NavigationPage")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml) avec [code = derrière](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs) |
| `TabbedPage` | [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) dérive de la classe abstraite [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) et permet la navigation entre les pages enfants à l’aide d’onglets. Affectez [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) à la propriété une collection de pages ou affectez à la [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propriété une collection d’objets de données et la [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) propriété pour [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) décrire comment chaque objet doit être représenté visuellement.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.TabbedPage)  /  [Guide](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)  /  [Exemple 1](/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage) et [2](/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage) | [![Exemple TabbedPage](pages-images/TabbedPage.png "Exemple de TabbedPage")](pages-images/TabbedPage-Large.png#lightbox "Exemple de TabbedPage")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) |
| `CarouselPage` | [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) dérive de la classe abstraite [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) et permet la navigation entre les pages enfants par le biais du balayage par doigt. Affectez [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) à la propriété une collection d' [`ContentPage`](xref:Xamarin.Forms.ContentPage) objets, ou affectez à la [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propriété une collection d’objets de données et la [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) propriété pour [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) décrire comment chaque objet doit être représenté visuellement.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.CarouselPage)  /  [Guide](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md)  /  [Exemple 1](/samples/xamarin/xamarin-forms-samples/navigation-carouselpage) et [2](/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate) | [![Exemple CarouselPage](pages-images/CarouselPage.png "Exemple CarouselPage")](pages-images/CarouselPage-Large.png#lightbox "Exemple CarouselPage")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) |
| `TemplatedPage` | [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage) affiche le contenu plein écran avec un modèle de contrôle et constitue la classe de base de [`ContentPage`](xref:Xamarin.Forms.ContentPage) .<br /><br />Documentation sur les [API](xref:Xamarin.Forms.TemplatedPage)  /  [Guide](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![Exemple TemplatedPage](pages-images/TemplatedPage.png "Exemple TemplatedPage")](pages-images/TemplatedPage.png "Exemple TemplatedPage") |
|     |     |     |

## <a name="related-links"></a>Liens connexes

- [Xamarin.Forms Exemple FormsGallery](/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms Extraits](/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.Forms Documentation sur les API](/dotnet/api/xamarin.forms?view=xamarin-forms)