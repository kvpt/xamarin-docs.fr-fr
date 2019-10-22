---
title: Pages Xamarin. Forms
description: Les pages Xamarin. Forms représentent des écrans d’application mobile multiplateforme. Cet article répertorie les pages incluses dans Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 9C8C710F-E312-420B-9324-A7A20CEDB7EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 278256f75d94fe47510ae4d15f12a3ff3a6a2b19
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "69976446"
---
# <a name="xamarinforms-pages"></a>Pages Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Les pages Xamarin. Forms représentent des écrans d’application mobile multiplateforme._

Tous les types de page décrits ci-dessous dérivent de la classe Xamarin. Forms [`Page`](xref:Xamarin.Forms.Page) . Ces éléments visuels occupent tout ou partie de l’écran. Un objet `Page` représente une `ViewController` dans iOS et une `Page` dans le plateforme Windows universelle. Sur Android, chaque page occupe l’écran comme un `Activity`, mais les pages Xamarin. Forms ne sont *pas* des objets `Activity`.

[![](pages-images/pages-sml.png "Xamarin.Forms Page Types")](pages-images/pages.png#lightbox "Xamarin.Forms Page Types")

## <a name="pages"></a>Pages

Xamarin. Forms prend en charge les types de page suivants :

<a name="contentPage" />

### <a name="contentpage"></a>ContentPage

|     |     |
| --- | --- |
| [`ContentPage`](xref:Xamarin.Forms.ContentPage) est le type de page le plus simple et le plus courant. Définissez la [propriété `Content`](xref:Xamarin.Forms.ContentPage.Content) sur un seul objet [`View`](views.md) , qui est le plus souvent un [`Layout`](layouts.md) tel que [`StackLayout`](layouts.md#stackLayout), [`Grid`](layouts.md#grid)ou [1](layouts.md#scrollView).<br /><br />[Documentation de l’API](xref:Xamarin.Forms.ContentPage) | [![Exemple ContentPage](pages-images/ContentPage.png "Exemple ContentPage")](pages-images/ContentPage-Large.png#lightbox "Exemple ContentPage")<br />code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs) |
|     |     |

### <a name="masterdetailpage"></a>MasterDetailPage

|     |     |
| --- | --- |
| Un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) gère deux volets d’informations. Définissez la propriété [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) sur une page qui présente généralement une liste ou un menu. Définissez la propriété [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) sur une page présentant un élément sélectionné à partir de la page maître. La propriété [`IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) détermine si la page maître ou de détails est visible.<br /><br />[Documentation](xref:Xamarin.Forms.MasterDetailPage) sur l’API  / [Guide](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)  / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage) | [![Exemple MasterDetailPage](pages-images/MasterDetailPage.png "Exemple MasterDetailPage")](pages-images/MasterDetailPage-Large.png#lightbox "Exemple MasterDetailPage")<br />code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs) |
|     |     |

### <a name="navigationpage"></a>NavigationPage

|     |     |
| --- | --- |
| Le [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) gère la navigation entre les autres pages à l’aide d’une architecture basée sur la pile. Lors de l’utilisation de la navigation entre les pages dans votre application, une instance de la page d’hébergement doit être passée au constructeur d’un objet `NavigationPage`.<br /><br />[Documentation](xref:Xamarin.Forms.NavigationPage) sur l’API  / [Guide](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)  /  les[exemples 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical), [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata)et [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-loginflow)  | [![Exemple NavigationPage](pages-images/NavigationPage.png "Exemple NavigationPage")](pages-images/NavigationPage-Large.png#lightbox "Exemple NavigationPage")<br />code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml) avec [code = derrière](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs) |
|     |     |

### <a name="tabbedpage"></a>TabbedPage

|     |     |
| --- | --- |
| [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) dérive de la classe abstraite [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) et permet la navigation entre les pages enfants à l’aide d’onglets. Affectez à la propriété [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) la valeur d’une collection de pages ou affectez à la propriété [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) la valeur d’une collection d’objets de données et la propriété [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) à un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) décrivant la manière dont chaque objet doit être représenté visuellement.<br /><br />[Documentation](xref:Xamarin.Forms.TabbedPage) sur l’API  / [Guide](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)  /  les[exemples 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage) et [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage) | [![Exemple TabbedPage](pages-images/TabbedPage.png "Exemple TabbedPage")](pages-images/TabbedPage-Large.png#lightbox "Exemple TabbedPage")<br />code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs) |
|     |     |

### <a name="carouselpage"></a>CarouselPage

|     |     |
| --- | --- |
| [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) dérive de la classe abstraite [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) et permet la navigation entre les pages enfants par le biais du balayage par doigt. Affectez à la propriété [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) la valeur d’une collection d’objets [`ContentPage`](#contentPage) , ou affectez à la propriété [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) une collection d’objets de données et la propriété [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) à une [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) qui décrit comment chaque objet doit être visuel représenté.<br /><br />[Documentation](xref:Xamarin.Forms.CarouselPage) sur l’API  / [Guide](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md)  /  les[exemples 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage) et [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate) | [![Exemple CarouselPage](pages-images/CarouselPage.png "Exemple CarouselPage")](pages-images/CarouselPage-Large.png#lightbox "Exemple CarouselPage")<br />code pour cette page  / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs) |
|     |     |

### <a name="templatedpage"></a>TemplatedPage

|     |     |
| --- | --- |
| [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage) affiche le contenu plein écran avec un modèle de contrôle et constitue la classe de base de [`ContentPage`](#contentPage).<br /><br />[Documentation](xref:Xamarin.Forms.TemplatedPage) de l’API  / [Guide](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![Exemple TemplatedPage](pages-images/TemplatedPage.png "Exemple TemplatedPage")](pages-images/TemplatedPage.png "Exemple TemplatedPage") |
|     |     |

## <a name="related-links"></a>Liens associés

- [Xamarin. Forms FormsGallery, exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Exemples Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Documentation sur les API Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
