---
title: Page carrousel Xamarin.Forms
description: Le CarouselPage Xamarin.Forms est une page que les utilisateurs peuvent balayer pour parcourir les pages de contenu, comme une galerie. Cet article montre comment utiliser un CarouselPage pour parcourir une collection de pages.
ms.prod: xamarin
ms.assetid: 2D14FC9D-DF5F-427E-9006-2AAE61ECF8DC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 5cfaeb8589514972dfcccd11009a02c00f95bfac
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "72696442"
---
# <a name="xamarinforms-carousel-page"></a>Page carrousel Xamarin.Forms

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)

_Le CarrouselPage Xamarin.Forms est une page que les utilisateurs peuvent glisser d’un côté à l’autre pour naviguer à travers les pages de contenu, comme une galerie. Cet article montre comment utiliser un CarouselPage pour naviguer à travers une collection de pages._

> [!IMPORTANT]
> Le [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) a été remplacé par [`CarouselView`](xref:Xamarin.Forms.CarouselView)le , qui fournit une mise en page défilementable où les utilisateurs peuvent glisser pour se déplacer à travers une collection d’éléments. Pour plus d’informations sur le `CarouselView`, voir [Xamarin.Forms CarouselView](~/xamarin-forms/user-interface/carouselview/index.md).

Les captures d’écran suivantes montrent un [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) sur chaque plate-forme:

![](carousel-page-images/thirdpage.png "CarouselPage Third Item")

La disposition [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) d’a est identique sur chaque plate-forme. Les pages peuvent être parcourues en balayant de droite à gauche pour naviguer vers l’avant dans la collection, et en balayant de gauche à droite pour naviguer en arrière dans la collection. Les captures d’écran suivantes [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) montrent la première page dans un cas:

![](carousel-page-images/firstpage.png "CarouselPage First Item")

Un balayage de droite à gauche permet d’accéder à la deuxième page, comme illustré dans les captures d’écran suivantes :

![](carousel-page-images/secondpage.png "CarouselPage Second Item")

Un nouveau balayage de droite à gauche permet d’accéder à la troisième page, tandis qu’un balayage de gauche à droite permet de revenir à la page précédente.

> [!NOTE]
> Le [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) ne prend pas en charge la virtualisation de l’interface utilisateur. Par conséquent, les performances peuvent être affectées si le `CarouselPage` contient trop d’éléments enfants.

Si [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) un est [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) intégré dans [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)la [`MasterDetailPage.IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty) page d’un , la propriété doit être définie pour `false` empêcher les conflits de geste entre le `CarouselPage` et le `MasterDetailPage`.

Pour plus d’informations sur le [`CarouselPage`](xref:Xamarin.Forms.CarouselPage), voir le chapitre [25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) de Charles Petzold Xamarin.Forms livre.

## <a name="create-a-carouselpage"></a>Créer un CarouselPage

Deux approches peuvent être [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)utilisées pour créer un :

- [Remplir](#Populating_a_CarouselPage_with_a_Page_Collection) le `CarouselPage` avec une collection d’instances enfants de [`ContentPage`](xref:Xamarin.Forms.ContentPage).
- [Affecter](#Populating_a_CarouselPage_with_a_Template) une collection à la propriété [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) et affecter un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à la propriété [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) pour retourner des instances de [`ContentPage`](xref:Xamarin.Forms.ContentPage) pour les objets dans la collection.

Avec les deux approches, le `CarouselPage` affiche alors chaque page tour à tour, avec une interaction de balayage pour afficher la page suivante.

> [!NOTE]
> A [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) ne peut être [`ContentPage`](xref:Xamarin.Forms.ContentPage) peuplé `ContentPage` que d’instances, ou de dérivés.

<a name="Populating_a_CarouselPage_with_a_Page_Collection" />

### <a name="populate-a-carouselpage-with-a-page-collection"></a>Remplir un CarouselPage avec une collection de pages

L’exemple de code XAML suivant montre un [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) qui affiche trois [`ContentPage`](xref:Xamarin.Forms.ContentPage) instances :

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

Chacun [`ContentPage`](xref:Xamarin.Forms.ContentPage) affiche [`Label`](xref:Xamarin.Forms.Label) simplement un pour [`BoxView`](xref:Xamarin.Forms.BoxView) une couleur particulière et une de cette couleur.

<a name="Populating_a_CarouselPage_with_a_Template" />

### <a name="populate-a-carouselpage-with-a-template"></a>Remplir un CarouselPage avec un modèle

L’exemple de code XAML suivant montre un [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) construit en assignant un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à la propriété pour retourner des [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) pages pour les objets de la collection:

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

Le [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) est peuplé de données [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) en définissant la propriété dans le constructeur pour le fichier de code derrière:

```csharp
public MainPage ()
{
    ...
    ItemsSource = ColorsDataModel.All;
}
```

L’exemple de code [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) suivant montre l’équivalent créé dans C :

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

Chacun [`ContentPage`](xref:Xamarin.Forms.ContentPage) affiche [`Label`](xref:Xamarin.Forms.Label) simplement un pour [`BoxView`](xref:Xamarin.Forms.BoxView) une couleur particulière et une de cette couleur.

## <a name="related-links"></a>Liens connexes

- [Types de pages](~/xamarin-forms/user-interface/controls/pages.md)
- [CarouselPage (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)
- [CarouselPageTemplate (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate)
- [CarouselPage](xref:Xamarin.Forms.CarouselPage)
