---
title: Xamarin.FormsPage du carrousel
description: Le Xamarin.Forms CarouselPage est une page que les utilisateurs peuvent faire glisser d’un côté à l’autre pour naviguer dans les pages de contenu, comme une galerie. Cet article montre comment utiliser un CarouselPage pour parcourir une collection de pages.
ms.prod: xamarin
ms.assetid: 2D14FC9D-DF5F-427E-9006-2AAE61ECF8DC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c234af1a5d47446149c92a71e9ce592dc0366b8f
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937460"
---
# <a name="xamarinforms-carousel-page"></a>Xamarin.FormsPage du carrousel

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)

_Le Xamarin.Forms CarouselPage est une page que les utilisateurs peuvent faire glisser d’un côté à l’autre pour naviguer dans les pages de contenu, comme une galerie. Cet article montre comment utiliser un CarouselPage pour naviguer dans une collection de pages._

> [!IMPORTANT]
> Le [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) a été remplacé par le [`CarouselView`](xref:Xamarin.Forms.CarouselView) , qui fournit une disposition avec défilement dans laquelle les utilisateurs peuvent faire glisser pour se déplacer dans une collection d’éléments. Pour plus d’informations sur le `CarouselView` , consultez [ Xamarin.Forms CarouselView](~/xamarin-forms/user-interface/carouselview/index.md).

Les captures d’écran suivantes montrent une [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) sur chaque plateforme :

![Troisième élément CarouselPage](carousel-page-images/thirdpage.png)

La disposition d’un [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) est identique sur chaque plateforme. Les pages peuvent être parcourues en balayant de droite à gauche pour naviguer vers l’avant dans la collection, et en balayant de gauche à droite pour naviguer en arrière dans la collection. Les captures d’écran suivantes montrent la première page d’une [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) instance :

![Premier élément CarouselPage](carousel-page-images/firstpage.png)

Un balayage de droite à gauche permet d’accéder à la deuxième page, comme illustré dans les captures d’écran suivantes :

![Deuxième élément CarouselPage](carousel-page-images/secondpage.png)

Un nouveau balayage de droite à gauche permet d’accéder à la troisième page, tandis qu’un balayage de gauche à droite permet de revenir à la page précédente.

> [!NOTE]
> [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)Ne prend pas en charge la virtualisation de l’interface utilisateur. Par conséquent, les performances peuvent être affectées si le `CarouselPage` contient trop d’éléments enfants.

Si un [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) est incorporé dans la [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) page d’un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) , la [`MasterDetailPage.IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty) propriété doit avoir la valeur `false` pour empêcher les conflits de mouvements entre `CarouselPage` et `MasterDetailPage` .

Pour plus d’informations sur le [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) , consultez le [chapitre 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) du livre de Charles Petzold Xamarin.Forms .

## <a name="create-a-carouselpage"></a>Créer un CarouselPage

Vous pouvez utiliser deux approches pour créer un [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) :

- [Remplir](#populate-a-carouselpage-with-a-page-collection) le `CarouselPage` avec une collection d’instances enfants de [`ContentPage`](xref:Xamarin.Forms.ContentPage).
- [Affecter](#populate-a-carouselpage-with-a-template) une collection à la propriété [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) et affecter un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à la propriété [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) pour retourner des instances de [`ContentPage`](xref:Xamarin.Forms.ContentPage) pour les objets dans la collection.

Avec les deux approches, le `CarouselPage` affiche alors chaque page tour à tour, avec une interaction de balayage pour afficher la page suivante.

> [!NOTE]
> Un [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) peut uniquement être rempli avec des [`ContentPage`](xref:Xamarin.Forms.ContentPage) instances ou des `ContentPage` dérivées.

### <a name="populate-a-carouselpage-with-a-page-collection"></a>Remplir un CarouselPage avec une collection de pages

L’exemple de code XAML suivant montre un [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) qui affiche trois [`ContentPage`](xref:Xamarin.Forms.ContentPage) instances :

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

### <a name="populate-a-carouselpage-with-a-template"></a>Remplir un CarouselPage avec un modèle

L’exemple de code XAML suivant montre un [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) construit en assignant un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à la [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) propriété pour retourner des pages pour les objets de la collection :

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

Le [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) est rempli avec les données en définissant la [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propriété dans le constructeur pour le fichier code-behind :

```csharp
public MainPage ()
{
    ...
    ItemsSource = ColorsDataModel.All;
}
```

L’exemple de code suivant montre l’équivalent [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) créé en C# :

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

## <a name="related-links"></a>Liens connexes

- [Types de pages](~/xamarin-forms/user-interface/controls/pages.md)
- [CarouselPage (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)
- [CarouselPageTemplate (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate)
- [CarouselPage](xref:Xamarin.Forms.CarouselPage)
