---
title: Xamarin.FormsPages
description: Xamarin.FormsLes pages représentent des écrans d’application mobile multiplateforme. Cet article répertorie les pages incluses dans Xamarin.Forms .
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c576186dcfd598cb4fcfecd6d36edf04f73eee64
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84132819"
---
# <a name="xamarinforms-pages"></a>Xamarin.FormsPages

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Les pages Xamarin. Forms représentent des écrans d’application mobile multiplateforme._

Tous les types de page qui sont décrits ci-dessous dérivent de la Xamarin.Forms [`Page`](xref:Xamarin.Forms.Page) classe. Ces éléments visuels occupent tout ou partie de l’écran. Un `Page` objet représente un `ViewController` dans iOS et un `Page` dans le plateforme Windows universelle. Sur Android, chaque page occupe l’écran comme un `Activity` , mais les Xamarin.Forms pages ne sont *pas* des `Activity` objets.

[![](pages-images/pages-sml.png "Xamarin.Forms Page Types")](pages-images/pages.png#lightbox "Xamarin.Forms Page Types")

## <a name="pages"></a>Pages

Xamarin.Formsprend en charge les types de page suivants :

<a name="contentPage" />

### <a name="contentpage"></a>ContentPage

|     |     |
| --- | --- |
| [`ContentPage`](xref:Xamarin.Forms.ContentPage)est le type de page le plus simple et le plus courant. Affectez [`Content`](xref:Xamarin.Forms.ContentPage.Content) à la propriété un [`View`](views.md) objet unique, qui est le plus souvent un [`Layout`](layouts.md) tel que [`StackLayout`](layouts.md#stackLayout) , [`Grid`](layouts.md#grid) ou [`ScrollView`](layouts.md#scrollView) .<br /><br />[Documentation sur les API](xref:Xamarin.Forms.ContentPage) | [![Exemple ContentPage](pages-images/ContentPage.png "Exemple ContentPage")](pages-images/ContentPage-Large.png#lightbox "Exemple ContentPage")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) |
|     |     |

### <a name="masterdetailpage"></a>MasterDetailPage

|     |     |
| --- | --- |
| Un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) gère deux volets d’informations. Affectez [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) à la propriété une page qui présente généralement une liste ou un menu. Affectez [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) à la propriété une page présentant un élément sélectionné à partir de la page maître. La [`IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) propriété détermine si la page maître ou de détails est visible.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.MasterDetailPage)  /  [Guide](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)  /  [Exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage) | [![Exemple MasterDetailPage](pages-images/MasterDetailPage.png "Exemple MasterDetailPage")](pages-images/MasterDetailPage-Large.png#lightbox "Exemple MasterDetailPage")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml) avec [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs) |
|     |     |

### <a name="navigationpage"></a>NavigationPage

|     |     |
| --- | --- |
| Le [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) gère la navigation entre les autres pages à l’aide d’une architecture basée sur la pile. Lors de l’utilisation de la navigation entre les pages dans votre application, une instance de la page d’hébergement doit être passée au constructeur d’un `NavigationPage` objet.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.NavigationPage)  /  [Guide](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)  /  [Exemples 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical), [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata)et [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-loginflow)  | [![Exemple NavigationPage](pages-images/NavigationPage.png "Exemple NavigationPage")](pages-images/NavigationPage-Large.png#lightbox "Exemple NavigationPage")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml) avec [code = derrière](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs) |
|     |     |

### <a name="tabbedpage"></a>TabbedPage

|     |     |
| --- | --- |
| [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)dérive de la classe abstraite [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) et permet la navigation entre les pages enfants à l’aide d’onglets. Affectez [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) à la propriété une collection de pages ou affectez à la [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propriété une collection d’objets de données et la [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) propriété pour [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) décrire comment chaque objet doit être représenté visuellement.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.TabbedPage)  /  [Guide](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)  /  [Exemple 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage) et [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage) | [![Exemple TabbedPage](pages-images/TabbedPage.png "Exemple de TabbedPage")](pages-images/TabbedPage-Large.png#lightbox "Exemple de TabbedPage")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) |
|     |     |

### <a name="carouselpage"></a>CarouselPage

|     |     |
| --- | --- |
| [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)dérive de la classe abstraite [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) et permet la navigation entre les pages enfants par le biais du balayage par doigt. Affectez [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) à la propriété une collection d' [`ContentPage`](#contentPage) objets, ou affectez à la [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propriété une collection d’objets de données et la [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) propriété pour [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) décrire comment chaque objet doit être représenté visuellement.<br /><br />Documentation sur les [API](xref:Xamarin.Forms.CarouselPage)  /  [Guide](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md)  /  [Exemple 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage) et [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate) | [![Exemple CarouselPage](pages-images/CarouselPage.png "Exemple CarouselPage")](pages-images/CarouselPage-Large.png#lightbox "Exemple CarouselPage")<br />[Code C# pour cette page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs)  /  [Page XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) |
|     |     |

### <a name="templatedpage"></a>TemplatedPage

|     |     |
| --- | --- |
| [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)affiche le contenu plein écran avec un modèle de contrôle et constitue la classe de base de [`ContentPage`](#contentPage) .<br /><br />Documentation sur les [API](xref:Xamarin.Forms.TemplatedPage)  /  [Guide](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![Exemple TemplatedPage](pages-images/TemplatedPage.png "Exemple TemplatedPage")](pages-images/TemplatedPage.png "Exemple TemplatedPage") |
|     |     |

## <a name="related-links"></a>Liens connexes

- [Xamarin.FormsExemple FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.FormsExtraits](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.FormsDocumentation sur les API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
