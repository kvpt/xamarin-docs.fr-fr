---
title: Xamarin. Forms ScrollView
description: Cet article explique comment utiliser la classe Xamarin. Forms ScrollView pour présenter des mises en page qui ne tiennent pas sur un seul écran, et dont le contenu fait de la place pour le clavier.
ms.prod: xamarin
ms.assetid: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/17/2019
ms.openlocfilehash: 8d523c6da6ca7feaf6894123822f789f37455865
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696854"
---
# <a name="xamarinforms-scrollview"></a>Xamarin. Forms ScrollView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`ScrollView`](xref:Xamarin.Forms.ScrollView) contient des dispositions et leur permet de faire défiler l’écran. `ScrollView` est également utilisé pour permettre aux affichages de se déplacer automatiquement vers la partie visible de l’écran lorsque le clavier est affiché.

[![](scroll-view-images/layouts-sml.png "Xamarin.Forms Layouts")](scroll-view-images/layouts.png#lightbox "Xamarin.Forms Layouts")

## <a name="purpose"></a>Fonction

[`ScrollView`](xref:Xamarin.Forms.ScrollView) peut être utilisé pour s’assurer que les affichages les plus grands s’affichent correctement sur des téléphones plus petits. Par exemple, une disposition qui fonctionne sur un iPhone 6S peut être coupée sur un iPhone. L’utilisation d’un `ScrollView` permet d’afficher les portions découpées de la disposition sur l’écran plus petit.

## <a name="usage"></a>Utilisation

> [!NOTE]
> les objets [`ScrollView`](xref:Xamarin.Forms.ScrollView) ne doivent pas être imbriqués. En outre, les `ScrollView`s ne doivent pas être imbriqués avec d’autres contrôles qui permettent de faire défiler, comme `ListView` et `WebView`.

[`ScrollView`](xref:Xamarin.Forms.ScrollView) expose une propriété `Content`, qui peut être définie sur une vue ou une disposition unique. Prenons l’exemple d’une disposition avec un très grand boxView, suivi d’un `Entry` :

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
var scroll = new ScrollView();
Content = scroll;
var stack = new StackLayout();
stack.Children.Add(new BoxView { BackgroundColor = Color.Red,    HeightRequest = 600, WidthRequest = 600 });
stack.Children.Add(new Entry());
```

Avant de faire défiler l’utilisateur, seul le `BoxView` est visible :

![](scroll-view-images/scroll-start.png "BoxView in ScrollView")

Notez que lorsque l’utilisateur commence à entrer du texte dans le `Entry`, la vue défile pour rester visible à l’écran :

![](scroll-view-images/scroll-end.png "Entry in ScrollView")

## <a name="properties"></a>Propriétés

[`ScrollView`](xref:Xamarin.Forms.ScrollView) définit les propriétés suivantes :

- [`ContentSize`](xref:Xamarin.Forms.ScrollView.ContentSizeProperty) obtient une valeur [`Size`](xref:Xamarin.Forms.Size) qui représente la taille du contenu.
- [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) obtient ou définit une valeur d’énumération [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) qui représente la direction de défilement du `ScrollView`.
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollXProperty) obtient un `double` qui représente la position de défilement X actuelle.
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollYProperty) obtient un `double` qui représente la position de défilement Y actuelle.
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibilityProperty) obtient ou définit une valeur [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) qui représente le moment où la barre de défilement horizontale est visible.
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.VerticalScrollBarVisibilityProperty) obtient ou définit une valeur [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) qui représente le moment où la barre de défilement verticale est visible.

> [!NOTE]
> Le défilement peut être désactivé en affectant à la propriété [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) la valeur `Neither`.

## <a name="methods"></a>Méthodes

[`ScrollView`](xref:Xamarin.Forms.ScrollView) fournit une méthode `ScrollToAsync`, qui peut être utilisée pour faire défiler la vue soit à l’aide de coordonnées, soit en spécifiant une vue particulière qui doit être rendue visible.

Lorsque vous utilisez des coordonnées, spécifiez les coordonnées `x` et `y`, ainsi qu’une valeur booléenne indiquant si le défilement doit être animé :

```csharp
scroll.ScrollToAsync(0, 150, true); //scrolls so that the position at 150px from the top is visible

scroll.ScrollToAsync(label, ScrollToPosition.Start, true); //scrolls so that the label is at the start of the list
```

> [!IMPORTANT]
> La méthode `ScrollToAsync` n’entraîne pas de défilement lorsque la propriété [`ScrollView.Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) a la valeur `Neither`.

Lorsque vous faites défiler jusqu’à un élément particulier, l’énumération `ScrollToPosition` spécifie l’emplacement où l’élément apparaîtra dans la vue :

- **Center** &ndash; fait défiler l’élément jusqu’au centre de la partie visible de la vue.
- **End** &ndash; fait défiler l’élément jusqu’à la fin de la partie visible de la vue.
- **MakeVisible** &ndash; fait défiler l’élément pour qu’il soit visible dans la vue.
- **Start** &ndash; fait défiler l’élément jusqu’au début de la partie visible de la vue.

La propriété `IsAnimated` spécifie le mode de défilement de la vue. Lorsqu’elle est définie sur `true`, une animation lisse est utilisée, au lieu de déplacer instantanément le contenu dans la vue.

## <a name="events"></a>événements

[`ScrollView`](xref:Xamarin.Forms.ScrollView) définit un seul événement, `Scrolled`. `Scrolled` est déclenché lorsque la vue a fini de faire défiler. Le gestionnaire d’événements pour `Scrolled` prend `ScrolledEventArgs`, qui a les propriétés `ScrollX` et `ScrollY`. L’exemple suivant montre comment mettre à jour une étiquette avec la position de défilement actuelle d’un `ScrollView` :

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
