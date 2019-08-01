---
title: Page carrousel Xamarin.Forms
description: Le CarouselPage Xamarin.Forms est une page que les utilisateurs peuvent balayer pour parcourir les pages de contenu, comme une galerie. Cet article montre comment utiliser un CarouselPage pour parcourir une collection de pages.
ms.prod: xamarin
ms.assetid: 2D14FC9D-DF5F-427E-9006-2AAE61ECF8DC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: b96877045a3f289cb4b9ba83a8159d26ed768d99
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655607"
---
# <a name="xamarinforms-carousel-page"></a>Page carrousel Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)

_Le CarouselPage Xamarin.Forms est une page que les utilisateurs peuvent balayer pour parcourir les pages de contenu, comme une galerie. Cet article montre comment utiliser un CarouselPage pour parcourir une collection de pages._

## <a name="overview"></a>Vue d'ensemble

Les captures d’écran suivantes montrent un [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) sur chaque plateforme :

![](carousel-page-images/thirdpage.png "Troisième élément CarouselPage")

La disposition d’un [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) est identique sur chaque plateforme. Les pages peuvent être parcourues en balayant de droite à gauche pour naviguer vers l’avant dans la collection, et en balayant de gauche à droite pour naviguer en arrière dans la collection. Les captures d’écran suivantes montrent la première page dans une instance de [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) :

![](carousel-page-images/firstpage.png "Premier élément CarouselPage")

Un balayage de droite à gauche permet d’accéder à la deuxième page, comme illustré dans les captures d’écran suivantes :

![](carousel-page-images/secondpage.png "Deuxième élément CarouselPage")

Un nouveau balayage de droite à gauche permet d’accéder à la troisième page, tandis qu’un balayage de gauche à droite permet de revenir à la page précédente.

<!--
> [!NOTE]
> The [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) has been deprecated, and will be removed from Xamarin.Forms in a future release. Instead, the [`CarouselView`](xref:Xamarin.Forms.CarouselView) should be used to provide a gallery-like view, where users can swipe from side to side to move through a collection of items.
-->

## <a name="creating-a-carouselpage"></a>Création d’un CarouselPage

Deux approches peuvent être adoptées pour créer un [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) :

- [Remplir](#Populating_a_CarouselPage_with_a_Page_Collection) le `CarouselPage` avec une collection d’instances enfants de [`ContentPage`](xref:Xamarin.Forms.ContentPage).
- [Affecter](#Populating_a_CarouselPage_with_a_Template) une collection à la propriété [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) et affecter un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à la propriété [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) pour retourner des instances de [`ContentPage`](xref:Xamarin.Forms.ContentPage) pour les objets dans la collection.

Avec les deux approches, le `CarouselPage` affiche alors chaque page tour à tour, avec une interaction de balayage pour afficher la page suivante.

> [!NOTE]
> Un [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) peut uniquement être rempli avec des instances de [`ContentPage`](xref:Xamarin.Forms.ContentPage), ou des dérivés de `ContentPage`.

<a name="Populating_a_CarouselPage_with_a_Page_Collection" />

### <a name="populating-a-carouselpage-with-a-page-collection"></a>Remplissage d’un CarouselPage avec une collection de pages

L’exemple de code XAML suivant montre un [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) qui affiche trois instances de [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

```xaml
<CarouselPage xmlns="http://xamarin.com/schemas/2014/forms"
              xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
              x:Class="CarouselPageNavigation.MainPage">
    <ContentPage>
        <ContentPage.Padding>
          <OnPlatform x:TypeArguments="Thickness">
              <On Platform="iOS, Android" Value="0,40,0,0" />
          </OnPlatform>
        </ContentPage.Padding>
        <StackLayout>
            <Label Text="Red" FontSize="Medium" HorizontalOptions="Center" />
            <BoxView Color="Red" WidthRequest="200" HeightRequest="200" HorizontalOptions="Center" VerticalOptions="CenterAndExpand" />
        </StackLayout>
    </ContentPage>
    <ContentPage>
        ...
    </ContentPage>
    <ContentPage>
        ...
    </ContentPage>
</CarouselPage>
```

L’exemple de code suivant montre l’interface utilisateur équivalente en C# :

```csharp
public class MainPageCS : CarouselPage
{
    public MainPageCS ()
    {
        Thickness padding;
        switch (Device.RuntimePlatform)
        {
            case Device.iOS:
            case Device.Android:
                padding = new Thickness(0, 40, 0, 0);
                break;
            default:
                padding = new Thickness();
                break;
        }

        var redContentPage = new ContentPage {
            Padding = padding,
            Content = new StackLayout {
                Children = {
                    new Label {
                        Text = "Red",
                        FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
                        HorizontalOptions = LayoutOptions.Center
                    },
                    new BoxView {
                        Color = Color.Red,
                        WidthRequest = 200,
                        HeightRequest = 200,
                        HorizontalOptions = LayoutOptions.Center,
                        VerticalOptions = LayoutOptions.CenterAndExpand
                    }
                }
            }
        };
        var greenContentPage = new ContentPage {
            Padding = padding,
            Content = new StackLayout {
                ...
            }
        };
        var blueContentPage = new ContentPage {
            Padding = padding,
            Content = new StackLayout {
                ...
            }
        };

        Children.Add (redContentPage);
        Children.Add (greenContentPage);
        Children.Add (blueContentPage);
    }
}
```

Chaque [`ContentPage`](xref:Xamarin.Forms.ContentPage) affiche simplement un [`Label`](xref:Xamarin.Forms.Label) pour une couleur particulière et un [`BoxView`](xref:Xamarin.Forms.BoxView) de cette couleur.

> [!NOTE]
> Le [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) ne prend pas en charge la virtualisation de l’interface utilisateur. Par conséquent, les performances peuvent être affectées si le `CarouselPage` contient trop d’éléments enfants.

Si un [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) est incorporé dans la page [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) d’un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage), la valeur `false` doit être affectée à la propriété [`MasterDetailPage.IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty) afin d’éviter les conflits de mouvement entre `CarouselPage` et `MasterDetailPage`.

Pour plus d’informations sur [`CarouselPage`](xref:Xamarin.Forms.CarouselPage), consultez le [chapitre 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) de l’ouvrage de Charles Petzold sur Xamarin.Forms.

<a name="Populating_a_CarouselPage_with_a_Template" />

### <a name="populating-a-carouselpage-with-a-template"></a>Remplissage d’un CarouselPage avec un modèle

L’exemple de code XAML suivant montre un [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) construit en assignant un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à la propriété [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) afin de retourner des pages pour les objets de la collection :

```xaml
<CarouselPage xmlns="http://xamarin.com/schemas/2014/forms"
              xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
              x:Class="CarouselPageNavigation.MainPage">
    <CarouselPage.ItemTemplate>
        <DataTemplate>
            <ContentPage>
                <ContentPage.Padding>
                  <OnPlatform x:TypeArguments="Thickness">
                    <On Platform="iOS, Android" Value="0,40,0,0" />
                  </OnPlatform>
                </ContentPage.Padding>
                <StackLayout>
                    <Label Text="{Binding Name}" FontSize="Medium" HorizontalOptions="Center" />
                    <BoxView Color="{Binding Color}" WidthRequest="200" HeightRequest="200" HorizontalOptions="Center" VerticalOptions="CenterAndExpand" />
                </StackLayout>
            </ContentPage>
        </DataTemplate>
    </CarouselPage.ItemTemplate>
</CarouselPage>
```

Le [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) est rempli avec des données en définissant la propriété [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) dans le constructeur pour le fichier code-behind :

```csharp
public MainPage ()
{
    ...
    ItemsSource = ColorsDataModel.All;
}
```

L’exemple de code suivant montre le [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) équivalent créé en C# :

```csharp
public class MainPageCS : CarouselPage
{
    public MainPageCS ()
    {
        Thickness padding;
        switch (Device.RuntimePlatform)
        {
            case Device.iOS:
            case Device.Android:
                padding = new Thickness(0, 40, 0, 0);
                break;
            default:
                padding = new Thickness();
                break;
        }

        ItemTemplate = new DataTemplate (() => {
            var nameLabel = new Label {
                FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
                HorizontalOptions = LayoutOptions.Center
            };
            nameLabel.SetBinding (Label.TextProperty, "Name");

            var colorBoxView = new BoxView {
                WidthRequest = 200,
                HeightRequest = 200,
                HorizontalOptions = LayoutOptions.Center,
                VerticalOptions = LayoutOptions.CenterAndExpand
            };
            colorBoxView.SetBinding (BoxView.ColorProperty, "Color");

            return new ContentPage {
                Padding = padding,
                Content = new StackLayout {
                    Children = {
                        nameLabel,
                        colorBoxView
                    }
                }
            };
        });

        ItemsSource = ColorsDataModel.All;
    }
}
```

Chaque [`ContentPage`](xref:Xamarin.Forms.ContentPage) affiche simplement un [`Label`](xref:Xamarin.Forms.Label) pour une couleur particulière et un [`BoxView`](xref:Xamarin.Forms.BoxView) de cette couleur.

> [!NOTE]
> Le [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) ne prend pas en charge la virtualisation de l’interface utilisateur. Par conséquent, les performances peuvent être affectées si le `CarouselPage` contient trop d’éléments enfants.

Si un [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) est incorporé dans la page [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) d’un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage), la valeur `false` doit être affectée à la propriété [`MasterDetailPage.IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty) afin d’éviter les conflits de mouvement entre `CarouselPage` et `MasterDetailPage`.

Pour plus d’informations sur [`CarouselPage`](xref:Xamarin.Forms.CarouselPage), consultez le [chapitre 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) de l’ouvrage de Charles Petzold sur Xamarin.Forms.

## <a name="summary"></a>Récapitulatif

Dans cet article, vous avez vu comment utiliser un [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) pour parcourir une collection de pages. Le `CarouselPage` est une page que les utilisateurs peuvent balayer pour parcourir les pages de contenu, comme une galerie.


## <a name="related-links"></a>Liens associés

- [Types de pages](~/xamarin-forms/user-interface/controls/pages.md)
- [CarouselPage (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)
- [CarouselPageTemplate (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate)
- [CarouselPage](xref:Xamarin.Forms.CarouselPage)
