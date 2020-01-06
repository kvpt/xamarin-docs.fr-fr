---
title: Xamarin.Forms ScrollView
description: Cet article explique comment utiliser la classe Xamarin.Forms ScrollView pour présenter les dispositions qui ne rentrent pas sur un seul écran, et qui ont contenu pour libérer de l’espace pour le clavier.
ms.prod: xamarin
ms.assetid: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2019
ms.openlocfilehash: bb10cda7c9899f176861ceee712cc876984c56ef
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488268"
---
# <a name="xamarinforms-scrollview"></a>Xamarin.Forms ScrollView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`ScrollView`](xref:Xamarin.Forms.ScrollView) contient des dispositions et leur permet de défilement hors écran. `ScrollView` est également utilisé pour autoriser des vues déplacer automatiquement à la partie visible de l’écran lors de l’affichage de l’aide du clavier.

[![](scroll-view-images/layouts-sml.png "Xamarin.Forms Layouts")](scroll-view-images/layouts.png#lightbox "Xamarin.Forms Layouts")

## <a name="purpose"></a>Fonction

[`ScrollView`](xref:Xamarin.Forms.ScrollView) peut être utilisé pour s’assurer que les affichages les plus grands s’affichent correctement sur des téléphones plus petits. Par exemple, une disposition qui fonctionne sur un iPhone 6 s peut être tronquée sur un iPhone 4 s. À l’aide un `ScrollView` permettrait les parties ajustées de la mise en page à afficher sur l’écran plus petit.

## <a name="usage"></a>Contrôle

> [!NOTE]
> les objets [`ScrollView`](xref:Xamarin.Forms.ScrollView) ne doivent pas être imbriqués. En outre, `ScrollView`s ne doit pas être imbriqué avec d’autres contrôles qui fournissent le défilement, comme `ListView` et `WebView`.

[`ScrollView`](xref:Xamarin.Forms.ScrollView) expose une propriété `Content`, qui peut être définie sur une vue ou une disposition unique. Prenons l’exemple suivant d’une disposition avec un très grand boxView, suivie d’une `Entry`:

```xaml
<ContentPage.Content>
    <ScrollView>
        <StackLayout>
            <BoxView BackgroundColor="Red" HeightRequest="600" WidthRequest="150" />
            <Entry />
        </StackLayout>
    </ScrollView>
</ContentPage.Content>
```

En C# :

```csharp
public class ScrollingDemoCode : ContentPage
{
    public ScrollingDemoCode()
    {
        StackLayout stackLayout = new StackLayout();
        stackLayout.Children.Add(new BoxView { BackgroundColor = Color.Red, HeightRequest = 600, WidthRequest = 150 });
        stackLayout.Children.Add(new Entry());
        ScrollView scrollView = new ScrollView();
        scrollView.Content = stackLayout;
        Content = scrollView;
    }
}
```

Avant que l’utilisateur fait défiler vers le bas, seuls le `BoxView` est visible :

![](scroll-view-images/scroll-start.png "BoxView in ScrollView")

Notez que lorsque l’utilisateur commence à entrer du texte dans le `Entry`, la vue défile pour laisser visibles à l’écran :

![](scroll-view-images/scroll-end.png "Entry in ScrollView")

## <a name="properties"></a>Propriétés

[`ScrollView`](xref:Xamarin.Forms.ScrollView) définit les propriétés suivantes :

- [`ContentSize`](xref:Xamarin.Forms.ScrollView.ContentSizeProperty) Obtient un [ `Size` ](xref:Xamarin.Forms.Size) valeur qui représente la taille du contenu.
- [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) Obtient ou définit un [ `ScrollOrientation` ](xref:Xamarin.Forms.ScrollOrientation) valeur d’énumération qui représente la direction de défilement de la `ScrollView`.
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollXProperty) Obtient un `double` qui représente la position de défilement X actuel.
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollYProperty) Obtient un `double` qui représente la position de défilement Y actuelle.
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibilityProperty) Obtient ou définit un [ `ScrollBarVisibility` ](xref:Xamarin.Forms.ScrollBarVisibility) valeur représentant le moment où la barre de défilement horizontale est visible.
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.VerticalScrollBarVisibilityProperty) Obtient ou définit un [ `ScrollBarVisibility` ](xref:Xamarin.Forms.ScrollBarVisibility) valeur représentant le moment où la barre de défilement verticale est visible.

> [!NOTE]
> Le défilement peut être désactivé en affectant à la propriété [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) la valeur `Neither`.

## <a name="methods"></a>Méthodes

[`ScrollView`](xref:Xamarin.Forms.ScrollView) fournit une méthode `ScrollToAsync`, qui peut être utilisée pour faire défiler la vue soit à l’aide de coordonnées, soit en spécifiant une vue particulière qui doit être rendue visible.

Lorsque vous utilisez des coordonnées, spécifiez la `x` et `y` coordonnées, ainsi que d’une valeur booléenne indiquant si le défilement doit être animé :

```csharp
scroll.ScrollToAsync(0, 150, true); //scrolls so that the position at 150px from the top is visible

scroll.ScrollToAsync(label, ScrollToPosition.Start, true); //scrolls so that the label is at the start of the list
```

> [!IMPORTANT]
> La méthode `ScrollToAsync` n’entraîne pas de défilement lorsque la propriété [`ScrollView.Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) a la valeur `Neither`.

Lorsque vous faites défiler jusqu’à un élément particulier, l’énumération `ScrollToPosition` spécifie l’emplacement où l’élément apparaîtra dans la vue :

- **Centre** &ndash; fait défiler l’élément vers le centre de la partie visible de la vue.
- **Fin** &ndash; fait défiler l’élément à la fin de la partie visible de la vue.
- **MakeVisible** &ndash; fait défiler l’élément afin qu’il soit visible dans la vue.
- **Démarrer** &ndash; fait défiler l’élément au début de la partie visible de la vue.

Le `IsAnimated` propriété spécifie la façon dont l’affichage est défilé. Lorsqu’elle est définie sur `true`, une animation lisse est utilisée, au lieu de déplacer instantanément le contenu dans la vue.

## <a name="events"></a>Events

[`ScrollView`](xref:Xamarin.Forms.ScrollView) définit un seul événement, `Scrolled`. `Scrolled` est déclenché lorsque la vue a terminé le défilement. Le Gestionnaire d’événements pour `Scrolled` prend `ScrolledEventArgs`, qui a le `ScrollX` et `ScrollY` propriétés. Les éléments suivants montrent comment mettre à jour une étiquette avec la position de défilement actuelle d’un `ScrollView`:

```csharp
Label label = new Label { Text = "Position: " };
ScrollView scroll = new ScrollView();
scroll.Scrolled += (object sender, ScrolledEventArgs e) => {
    label.Text = "Position: " + e.ScrollX + " x " + e.ScrollY;
};
```

Notez que les positions de défilement peuvent être négatives, en raison de l’effet de rebond lors du défilement à la fin d’une liste.

## <a name="related-links"></a>Liens connexes

- [Disposition (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Exemple BusinessTumble (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
