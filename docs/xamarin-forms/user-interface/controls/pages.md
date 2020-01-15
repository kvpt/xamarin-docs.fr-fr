---
title: Pages Xamarin.Forms
description: Pages Xamarin.Forms représentent les écrans de l’application mobile multiplateforme. Cet article répertorie les pages qui sont inclus dans Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 9C8C710F-E312-420B-9324-A7A20CEDB7EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 101b7aa014064e07de69e8c6b12f0ad1000ae0dd
ms.sourcegitcommit: 211fed94fb96127a3e158ae1ff5d7eb831a203d8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75955751"
---
# <a name="xamarinforms-pages"></a>Pages Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Pages Xamarin.Forms représentent les écrans de l’application mobile multiplateforme._

Tous les types de page qui sont décrites ci-dessous dérivent de Xamarin.Forms [ `Page` ](xref:Xamarin.Forms.Page) classe. Ces éléments visuels occupent tout ou partie de l’écran. Un `Page` objet représente un `ViewController` dans iOS et un `Page` dans la plateforme Windows universelle. Sur Android, chaque page occupe l’écran comme une `Activity`, mais sont des pages Xamarin.Forms *pas* `Activity` objets.

[![](pages-images/pages-sml.png "Xamarin.Forms Page Types")](pages-images/pages.png#lightbox "Xamarin.Forms Page Types")

## <a name="pages"></a>Pages

Xamarin.Forms prend en charge les types de page suivants :

<a name="contentPage" />

### <a name="contentpage"></a>ContentPage

|     |     |
| --- | --- |
| [`ContentPage`](xref:Xamarin.Forms.ContentPage) est le type de la plus simple et la plus courante de page. Définir le [ `Content` ](xref:Xamarin.Forms.ContentPage.Content) à une seule propriété [ `View` ](views.md) objet, qui est le plus souvent un [ `Layout` ](layouts.md) comme [ `StackLayout` ](layouts.md#stackLayout), [ `Grid` ](layouts.md#grid), ou [ `ScrollView` ](layouts.md#scrollView).<br /><br />[Documentation de l’API](xref:Xamarin.Forms.ContentPage) | [![Exemple ContentPage](pages-images/ContentPage.png "Exemple ContentPage")](pages-images/ContentPage-Large.png#lightbox "Exemple ContentPage")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) |
|     |     |

### <a name="masterdetailpage"></a>MasterDetailPage

|     |     |
| --- | --- |
| Un [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) gère deux volets d’informations. Définir le [ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) propriété à une page présentant généralement une liste ou un menu. Définir le [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) propriété à une page présentant un élément sélectionné à partir de la page maître. Le [ `IsPresented` ](xref:Xamarin.Forms.MasterDetailPage.IsPresented) propriété détermine si la page maître ou détail est visible.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.MasterDetailPage) / [Guide](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md) / [exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage) | [![Exemple MasterDetailPage](pages-images/MasterDetailPage.png "Exemple MasterDetailPage")](pages-images/MasterDetailPage-Large.png#lightbox "Exemple MasterDetailPage")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs) |
|     |     |

### <a name="navigationpage"></a>NavigationPage

|     |     |
| --- | --- |
| Le [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) gère la navigation parmi les autres pages à l’aide d’une architecture basée sur la pile. Lorsque vous utilisez la navigation entre les pages dans votre application, une instance de la page d’accueil doit être passée au constructeur d’un `NavigationPage` objet.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.NavigationPage) / [Guide](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md) / [exemple 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical), [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata), et [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-loginflow)  | [![Exemple NavigationPage](pages-images/NavigationPage.png "Exemple NavigationPage")](pages-images/NavigationPage-Large.png#lightbox "Exemple NavigationPage")<br />[Code c# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs) / [XAML Page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml) avec [code = derrière](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs) |
|     |     |

### <a name="tabbedpage"></a>TabbedPage

|     |     |
| --- | --- |
| [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) dérive de la classe abstraite [ `MultiPage` ](xref:Xamarin.Forms.MultiPage`1) classe et permet la navigation parmi les enfants de pages à l’aide des onglets. Définir le [ `Children` ](xref:Xamarin.Forms.MultiPage`1.Children) propriété à une collection de pages, ou définir le [ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propriété à une collection d’objets de données et la [ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) propriété à un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) décrivant la manière dont chaque objet doit être représenté visuellement.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.TabbedPage) / [Guide](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md) / [exemple 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage) et [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage) | [![Exemple TabbedPage](pages-images/TabbedPage.png "Exemple TabbedPage")](pages-images/TabbedPage-Large.png#lightbox "Exemple TabbedPage")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) |
|     |     |

### <a name="carouselpage"></a>CarouselPage

|     |     |
| --- | --- |
| [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) dérive de la classe abstraite [ `MultiPage` ](xref:Xamarin.Forms.MultiPage`1) classe et permet la navigation parmi les enfants des pages via balayant doigt. Définir le [ `Children` ](xref:Xamarin.Forms.MultiPage`1.Children) propriété à une collection de [ `ContentPage` ](#contentPage) objets, ou définir le [ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propriété à une collection d’objets de données et le [ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) propriété à un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) décrivant la manière dont chaque objet doit être représenté visuellement.<br /><br />[Documentation de l’API](xref:Xamarin.Forms.CarouselPage) / [Guide](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md) / [exemple 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage) et [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate) | [![Exemple CarouselPage](pages-images/CarouselPage.png "Exemple CarouselPage")](pages-images/CarouselPage-Large.png#lightbox "Exemple CarouselPage")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs) / [page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) |
|     |     |

### <a name="templatedpage"></a>TemplatedPage

|     |     |
| --- | --- |
| [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage) Affiche le contenu de plein écran avec un modèle de contrôle, et est la classe de base pour [ `ContentPage` ](#contentPage).<br /><br />[Documentation de l’API](xref:Xamarin.Forms.TemplatedPage) / [Guide](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![Exemple TemplatedPage](pages-images/TemplatedPage.png "Exemple TemplatedPage")](pages-images/TemplatedPage.png "Exemple TemplatedPage") |
|     |     |

## <a name="related-links"></a>Liens associés

- [Exemple de Xamarin.Forms FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Exemples Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Documentation sur les API Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
