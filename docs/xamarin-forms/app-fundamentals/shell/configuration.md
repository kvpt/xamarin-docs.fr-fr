---
title: Xamarin.FormsConfiguration de la page de Shell
description: La classe Shell définit des propriétés jointes qui peuvent être utilisées pour configurer l’apparence des pages dans les Xamarin.Forms applications Shell. Cela comprend notamment la configuration des couleurs de la page, la désactivation de la barre de navigation et de la barre d’onglet ainsi que l’apparence des affichages dans la barre de navigation.
ms.prod: xamarin
ms.assetid: 3FC2FBD1-C30B-4408-97B2-B04E3A2E4F03
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/29/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 102070fc478b42e9fbc0c7d0006197c81a49c9b8
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137499"
---
# <a name="xamarinforms-shell-page-configuration"></a>Xamarin.FormsConfiguration de la page de Shell

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

La `Shell` classe définit les propriétés jointes qui peuvent être utilisées pour configurer l’apparence des pages dans les applications de l' Xamarin.Forms interpréteur de commandes. Cela comprend la définition des couleurs des pages, la définition du mode de présentation de la page, la désactivation de la barre de navigation, la désactivation de la barre d’onglets et l’affichage des affichages dans la barre de navigation.

## <a name="set-page-colors"></a>Définir les couleurs de page

La classe `Shell` définit les propriétés jointes suivantes qui peuvent être utilisées pour définir les couleurs de page dans une application Shell :

- `BackgroundColor`, de type `Color` : définit la couleur d’arrière-plan du chrome Shell. La couleur n’apparaît pas derrière le contenu Shell.
- `DisabledColor`, de type `Color` : définit la couleur permettant d’ombrer le texte et les icônes désactivées.
- `ForegroundColor`, de type `Color` : définit la couleur permettant d’ombrer le texte et les icônes.
- `TitleColor`, de type `Color` : définit la couleur utilisée pour le titre de la page actuelle.
- `UnselectedColor`, de type `Color` : définit la couleur appliquée au texte et aux icônes non sélectionnés dans le chrome Shell.

Toutes ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données et être stylisées à l’aide de styles XAML. En outre, les propriétés peuvent être définies à l’aide de feuilles de style en cascade (CSS). Pour plus d’informations, consultez [ Xamarin.Forms propriétés spécifiques à l’interpréteur](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)de commandes.

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

Pour plus d’informations sur les styles XAML, consultez [stylisation d' Xamarin.Forms applications à l’aide de styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

## <a name="set-page-presentation-mode"></a>Définir le mode de présentation de la page

Par défaut, une petite animation de navigation se produit lorsqu’une page est parcourue à l’aide de la `GoToAsync` méthode. Toutefois, ce comportement peut être modifié en affectant `Shell.PresentationMode` à la propriété jointe un sur [`ContentPage`](xref:Xamarin.Forms.ContentPage) l’un des membres de l' `PresentationMode` énumération :

- `NotAnimated`indique que la page sera affichée sans animation de navigation.
- `Animated`indique que la page sera affichée avec une animation de navigation. Il s’agit de la valeur par défaut de la `Shell.PresentationMode` propriété jointe.
- `Modal`indique que la page s’affichera sous la forme d’une page modale.
- `ModalAnimated`indique que la page s’affichera sous la forme d’une page modale, avec une animation de navigation.
- `ModalNotAnimated`indique que la page s’affichera sous la forme d’une page modale, sans animation de navigation.

> [!IMPORTANT]
> Le `PresentationMode` type est une énumération d’indicateurs. Cela signifie qu’une combinaison de membres de l’énumération peut être appliquée dans le code. Toutefois, pour faciliter l’utilisation en XAML, le `ModalAnimated` membre est une combinaison des `Animated` membres et `Modal` , et le `ModalNotAnimated` membre est une combinaison des `NotAnimated` `Modal` membres et. Pour plus d’informations sur les énumérations d’indicateur, consultez [types énumération en tant qu’indicateurs binaires](/dotnet/csharp/language-reference/builtin-types/enum#enumeration-types-as-bit-flags).

L’exemple de code XAML suivant définit la `Shell.PresentationMode` propriété jointe sur un [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

```xaml
<ContentPage ...
             Shell.PresentationMode="Modal">
    ...             
</ContentPage>
```

Dans cet exemple, [`ContentPage`](xref:Xamarin.Forms.ContentPage) est défini pour être affiché en tant que page modale, lorsque la page est parcourue avec la `GoToAsync` méthode.

## <a name="enable-navigation-bar-shadow"></a>Activer l’ombre de la barre de navigation

La `Shell` classe définit la `NavBarHasShadow` propriété jointe, de type `bool` , qui contrôle si l’ombre de la barre de navigation est. Par défaut, la valeur de la propriété est `false` sur iOS et `true` sur Android.

Bien que cette propriété puisse être définie sur un objet sous `Shell` -classé, elle peut également être définie sur n’importe quelle page qui souhaite activer l’ombre de la barre de navigation. Par exemple, le code XAML suivant illustre l’activation de l’ombre de la barre de navigation à partir d’un [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

```xaml
<ContentPage ...
             Shell.NavBarHasShadow="true">
    ...
</ContentPage>
```

Cela entraîne l’activation de l’ombre de la barre de navigation.

## <a name="disable-the-navigation-bar"></a>Désactiver la barre de navigation

La classe `Shell` définit la propriété jointe `NavBarIsVisible`, de type `bool`, qui contrôle la visibilité de la barre de navigation lorsqu’une page s’affiche. Par défaut, la valeur de la propriété est `true`.

Bien que cette propriété puisse être définie sur un objet `Shell` sous-classé, elle est généralement définie sur toutes les pages sur lesquelles vous souhaitez masquer la barre de navigation. Par exemple, le code XAML suivant illustre la désactivation de la barre de navigation à partir d’un [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

```xaml
<ContentPage ...
             Shell.NavBarIsVisible="false">
    ...
</ContentPage>
```

Ainsi, la barre de navigation devient invisible lorsque la page s’affiche :

![Capture d’écran de la page de Shell avec une barre de navigation invisible, sur iOS et Android](configuration-images/navigationbar-invisible.png "Page de Shell avec barre de navigation invisible")

## <a name="disable-the-tab-bar"></a>Désactiver la barre d’onglets

La classe `Shell` définit la propriété jointe `TabBarIsVisible`, de type `bool`, qui contrôle la visibilité de la barre d’onglets lorsqu’une page s’affiche. Par défaut, la valeur de la propriété est `true`.

Bien que cette propriété puisse être définie sur un objet `Shell` sous-classé, elle est généralement définie sur toutes les pages sur lesquelles vous souhaitez masquer la barre d’onglets. Par exemple, le code XAML suivant illustre la désactivation de la barre d’onglets à partir d’un [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

```xaml
<ContentPage ...
             Shell.TabBarIsVisible="false">
    ...
</ContentPage>
```

Ainsi, la barre d’onglets devient invisible lorsque la page s’affiche :

![Capture d’écran de la page de Shell avec une barre d’onglet invisible, sur iOS et Android](configuration-images/tabbar-invisible.png "Page de l’interpréteur de commandes avec la barre d’onglet invisible")

## <a name="display-views-in-the-navigation-bar"></a>Affichage des vues dans la barre de navigation

La `Shell` classe définit la `TitleView` propriété jointe, de type `View` , qui permet l’affichage de n’importe où Xamarin.Forms [`View`](xref:Xamarin.Forms.View) dans la barre de navigation.

Bien que cette propriété puisse être définie sur un objet `Shell` sous-classé, elle peut également être définie sur toutes les pages devant afficher une vue dans la barre de navigation. Par exemple, le code XAML suivant illustre l’affichage d’un [`Image`](xref:Xamarin.Forms.Image) dans la barre de navigation d’un [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

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

![Capture d’écran de la page de Shell avec une vue de titre, sur iOS et Android](configuration-images/titleview.png "Page de Shell avec une vue de titre")

> [!IMPORTANT]
> Si la barre de navigation est invisible, avec la propriété jointe `NavBarIsVisible`, la vue de titre ne s’affichera pas.

De nombreuses vues ne s’affichent pas dans la barre de navigation, sauf si la taille de la vue est spécifiée avec les [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) Propriétés et, ou si l’emplacement de la vue est spécifié avec les [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) Propriétés et.

Étant donné que la [`Layout`](xref:Xamarin.Forms.Layout) classe dérive de la [`View`](xref:Xamarin.Forms.View) classe, la `TitleView` propriété jointe peut être définie pour afficher une classe de disposition qui contient plusieurs vues. De même, étant donné que la [`ContentView`](xref:Xamarin.Forms.ContentView) classe dérive finalement de la [`View`](xref:Xamarin.Forms.View) classe, la `TitleView` propriété jointe peut être définie pour afficher un `ContentView` qui contient une vue unique.

## <a name="page-visibility"></a>Visibilité de page

Le shell respecte la visibilité de la page, définie avec la [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) propriété. Par conséquent, lorsque la propriété d’une page `IsVisible` est définie sur `false` elle ne sera pas visible dans l’application Shell et il sera impossible de naviguer vers celle-ci.

## <a name="related-links"></a>Liens connexes

- [Xaminals (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Styliser des Xamarin.Forms applications à l’aide de styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [Xamarin.FormsPropriétés spécifiques de l’interpréteur de commandes CSS](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)
