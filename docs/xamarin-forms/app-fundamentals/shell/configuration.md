---
title: Configuration de la page Shell Xamarin.Forms
description: La classe Shell définit des propriétés jointes qui peuvent être utilisées pour configurer l’apparence des pages dans les applications Shell Xamarin.Forms. Cela comprend notamment la configuration des couleurs de la page, la désactivation de la barre de navigation et de la barre d’onglet ainsi que l’apparence des affichages dans la barre de navigation.
ms.prod: xamarin
ms.assetid: 3FC2FBD1-C30B-4408-97B2-B04E3A2E4F03
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/29/2020
ms.openlocfilehash: 411c87c25701521bf27fbb863b02a90f8e523574
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79305051"
---
# <a name="xamarinforms-shell-page-configuration"></a>Configuration de la page Shell Xamarin.Forms

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

La classe `Shell` définit des propriétés jointes qui peuvent être utilisées pour configurer l’apparence des pages dans les applications Shell Xamarin.Forms. Cela comprend la définition des couleurs de la page, la définition du mode de présentation de la page, la désactivation de la barre de navigation, la désactivation de la barre d’onglet et l’affichage des vues dans la barre de navigation.

## <a name="set-page-colors"></a>Définir les couleurs de page

La classe `Shell` définit les propriétés jointes suivantes qui peuvent être utilisées pour définir les couleurs de page dans une application Shell :

- `BackgroundColor`, de type `Color` : définit la couleur d’arrière-plan du chrome Shell. La couleur n’apparaît pas derrière le contenu Shell.
- `DisabledColor`, de type `Color` : définit la couleur permettant d’ombrer le texte et les icônes désactivées.
- `ForegroundColor`, de type `Color` : définit la couleur permettant d’ombrer le texte et les icônes.
- `TitleColor`, de type `Color` : définit la couleur utilisée pour le titre de la page actuelle.
- `UnselectedColor`, de type `Color` : définit la couleur appliquée au texte et aux icônes non sélectionnés dans le chrome Shell.

Toutes ces propriétés [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) sont sauvegardées par des objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données, et stylées à l’aide de styles XAML. En outre, les propriétés peuvent être définies à l’aide de feuilles de style en cascade (CSS). Pour plus d’informations, consultez [Propriétés spécifiques de Xamarin.Forms Shell](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties).

> [!NOTE]
> Certaines propriétés permettent de définir les couleurs d’onglet. Pour plus d’informations, consultez [Apparence d’onglet](tabs.md#tab-appearance).

Le XAML suivant montre la configuration de propriétés de couleur dans une classe `Shell` sous-classée :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       x:Class="Xaminals.AppShell"
       BackgroundColor="#455A64"
       ForegroundColor="White"
       TitleColor="White"
       DisabledColor="#B4FFFFFF"
       UnselectedColor="#95FFFFFF">

</Shell>
```

Dans cet exemple, les valeurs de couleur seront appliquées à toutes les pages dans l’application Shell à moins d’être substituées au niveau de la page.

Étant donné que les propriétés de couleur sont des propriétés jointes, elles peuvent également être définies sur des pages individuelles afin de définir les couleurs sur cette page :

```xaml
<ContentPage ...
             Shell.BackgroundColor="Gray"
             Shell.ForegroundColor="White"
             Shell.TitleColor="Blue"
             Shell.DisabledColor="#95FFFFFF"
             Shell.UnselectedColor="#B4FFFFFF">

</ContentPage>
```

Vous pouvez également définir les propriétés de couleur avec un style XAML :

```xaml
<Style x:Key="DomesticShell"
       TargetType="Element" >
    <Setter Property="Shell.BackgroundColor"
            Value="#039BE6" />
    <Setter Property="Shell.ForegroundColor"
            Value="White" />
    <Setter Property="Shell.TitleColor"
            Value="White" />
    <Setter Property="Shell.DisabledColor"
            Value="#B4FFFFFF" />
    <Setter Property="Shell.UnselectedColor"
            Value="#95FFFFFF" />
</Style>
```

Pour plus d’informations sur les styles XAML, consultez [Styler des applications Xamarin.Forms avec des styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

## <a name="set-page-presentation-mode"></a>Mode de présentation de page définis

Par défaut, une petite animation de navigation se `GoToAsync` produit lorsqu’une page est consultée avec la méthode. Cependant, ce comportement peut être `Shell.PresentationMode` modifié en [`ContentPage`](xref:Xamarin.Forms.ContentPage) fixant `PresentationMode` la propriété ci-jointe sur un à l’un des membres de recensement:

- `NotAnimated`indique que la page sera affichée sans animation de navigation.
- `Animated`indique que la page sera affichée avec une animation de navigation. Il s’agit de `Shell.PresentationMode` la valeur par défaut de la propriété ci-jointe.
- `Modal`indique que la page sera affichée sous forme de page modale.
- `ModalAnimated`indique que la page sera affichée sous forme de page modale, avec une animation de navigation.
- `ModalNotAnimated`indique que la page sera affichée sous forme de page modale, sans animation de navigation.

> [!IMPORTANT]
> Le `PresentationMode` type est un recensement des drapeaux. Cela signifie qu’une combinaison de membres de recensement peut être appliquée dans le code. Cependant, pour faciliter l’utilisation dans `ModalAnimated` XAML, `Animated` le `Modal` membre est `ModalNotAnimated` une combinaison de `NotAnimated` `Modal` la et les membres, et le membre est une combinaison de la et les membres. Pour plus d’informations sur les énumérations de drapeau, voir [les types d’énumération comme des drapeaux bit](/dotnet/csharp/language-reference/builtin-types/enum#enumeration-types-as-bit-flags).

L’exemple XAML `Shell.PresentationMode` suivant place [`ContentPage`](xref:Xamarin.Forms.ContentPage)la propriété ci-jointe sur un :

```xaml
<ContentPage ...
             Shell.PresentationMode="Modal">
    ...             
</ContentPage>
```

Dans cet exemple, le [`ContentPage`](xref:Xamarin.Forms.ContentPage) est configuré pour être affiché comme une `GoToAsync` page modale, lorsque la page est naviguée avec la méthode.

## <a name="enable-navigation-bar-shadow"></a>Activer l’ombre de la barre de navigation

La `Shell` classe définit `NavBarHasShadow` la propriété `bool`ci-jointe, de type, qui contrôle si la barre de navigation a une ombre. Par défaut, la valeur `false` de la `true` propriété est sur iOS, et sur Android.

Bien que cette propriété puisse être `Shell` placée sur un objet sous-classé, elle peut également être définie sur toutes les pages qui veulent activer l’ombre de barre de navigation. Par exemple, le XAML suivant montre permettant [`ContentPage`](xref:Xamarin.Forms.ContentPage)l’ombre de barre de navigation à partir d’un :

```xaml
<ContentPage ...
             Shell.NavBarHasShadow="true">
    ...
</ContentPage>
```

Il en résulte que l’ombre de la barre de navigation est activée.

## <a name="disable-the-navigation-bar"></a>Désactiver la barre de navigation

La classe `Shell` définit la propriété jointe `NavBarIsVisible`, de type `bool`, qui contrôle la visibilité de la barre de navigation lorsqu’une page s’affiche. Par défaut, la valeur de la propriété est `true`.

Bien que cette propriété puisse être définie sur un objet `Shell` sous-classé, elle est généralement définie sur toutes les pages sur lesquelles vous souhaitez masquer la barre de navigation. Par exemple, le XAML suivant montre désactivant la barre de navigation d’un [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<ContentPage ...
             Shell.NavBarIsVisible="false">
    ...
</ContentPage>
```

Ainsi, la barre de navigation devient invisible lorsque la page s’affiche :

![Capture d’écran de la page Shell avec une barre de navigation invisible, sur iOS et Android](configuration-images/navigationbar-invisible.png "Page Shell avec barre de navigation invisible")

## <a name="disable-the-tab-bar"></a>Désactiver la barre d’onglets

La classe `Shell` définit la propriété jointe `TabBarIsVisible`, de type `bool`, qui contrôle la visibilité de la barre d’onglets lorsqu’une page s’affiche. Par défaut, la valeur de la propriété est `true`.

Bien que cette propriété puisse être définie sur un objet `Shell` sous-classé, elle est généralement définie sur toutes les pages sur lesquelles vous souhaitez masquer la barre d’onglets. Par exemple, le XAML suivant montre désactivant la barre d’onglet d’un [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<ContentPage ...
             Shell.TabBarIsVisible="false">
    ...
</ContentPage>
```

Ainsi, la barre d’onglets devient invisible lorsque la page s’affiche :

![Capture d’écran de la page Shell avec une barre d’onglet invisible, sur iOS et Android](configuration-images/tabbar-invisible.png "Page Shell avec barre d’onglet invisible")

## <a name="display-views-in-the-navigation-bar"></a>Affichage des vues dans la barre de navigation

La `Shell` classe définit `TitleView` la propriété `View`ci-jointe, de type [`View`](xref:Xamarin.Forms.View) , qui permet à n’importe quel Xamarin.Forms d’être affiché dans la barre de navigation.

Bien que cette propriété puisse être définie sur un objet `Shell` sous-classé, elle peut également être définie sur toutes les pages devant afficher une vue dans la barre de navigation. Par exemple, le XAML [`Image`](xref:Xamarin.Forms.Image) suivant montre affichant [`ContentPage`](xref:Xamarin.Forms.ContentPage)un dans la barre de navigation d’un :

```xaml
<ContentPage ...>
    <Shell.TitleView>
        <Image Source="xamarin_logo.png"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
    </Shell.TitleView>
    ...
</ContentPage>
```

Ainsi, une image s’affiche dans la barre de navigation de la page :

![Capture d’écran de la page Shell avec une vue de titre, sur iOS et Android](configuration-images/titleview.png "Page Shell avec une vue de titre")

> [!IMPORTANT]
> Si la barre de navigation est invisible, avec la propriété jointe `NavBarIsVisible`, la vue de titre ne s’affichera pas.

De nombreuses vues n’apparaîtront pas dans la barre de [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) navigation à moins que la taille [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) de [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) la vue ne soit spécifiée avec les propriétés et l’emplacement de la vue.

Étant [`Layout`](xref:Xamarin.Forms.Layout) donné que la [`View`](xref:Xamarin.Forms.View) classe `TitleView` dérive de la classe, la propriété ci-jointe peut être définie pour afficher une classe de mise en page qui contient plusieurs vues. De même, [`ContentView`](xref:Xamarin.Forms.ContentView) parce que la [`View`](xref:Xamarin.Forms.View) classe `TitleView` dérive finalement de la `ContentView` classe, la propriété ci-jointe peut être définie pour afficher un qui contient une vue unique.

## <a name="page-visibility"></a>Visibilité de la page

Shell respecte la visibilité de [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) la page, définie avec la propriété. Par conséquent, lorsque `IsVisible` la propriété `false` d’une page est configuré à elle ne sera pas visible dans l’application Shell et il ne sera pas possible de naviguer vers elle.

## <a name="related-links"></a>Liens connexes

- [Xaminals (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Styler des applications Xamarin.Forms avec des styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [Propriétés spécifiques du CSS Shell Xamarin.Forms](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)
