---
title: Configuration de la page Shell Xamarin.Forms
description: La classe Shell définit des propriétés jointes qui peuvent être utilisées pour configurer l’apparence des pages dans les applications Shell Xamarin.Forms. Cela comprend notamment la configuration des couleurs de la page, la désactivation de la barre de navigation et de la barre d’onglet ainsi que l’apparence des affichages dans la barre de navigation.
ms.prod: xamarin
ms.assetid: 3FC2FBD1-C30B-4408-97B2-B04E3A2E4F03
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/05/2019
ms.openlocfilehash: e207949d607219393ffeb51fce818ddfb68ae344
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489906"
---
# <a name="xamarinforms-shell-page-configuration"></a>Configuration de la page Shell Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

La classe `Shell` définit des propriétés jointes qui peuvent être utilisées pour configurer l’apparence des pages dans les applications Shell Xamarin.Forms. Cela comprend notamment la configuration des couleurs de la page, la désactivation de la barre de navigation et de la barre d’onglet ainsi que l’apparence des affichages dans la barre de navigation.

## <a name="set-page-colors"></a>Définir les couleurs de page

La classe `Shell` définit les propriétés jointes suivantes qui peuvent être utilisées pour définir les couleurs de page dans une application Shell :

- `BackgroundColor`, de type `Color` : définit la couleur d’arrière-plan du chrome Shell. La couleur n’apparaît pas derrière le contenu Shell.
- `DisabledColor`, de type `Color` : définit la couleur permettant d’ombrer le texte et les icônes désactivées.
- `ForegroundColor`, de type `Color` : définit la couleur permettant d’ombrer le texte et les icônes.
- `TitleColor`, de type `Color` : définit la couleur utilisée pour le titre de la page actuelle.
- `UnselectedColor`, de type `Color` : définit la couleur appliquée au texte et aux icônes non sélectionnés dans le chrome Shell.

Toutes ces propriétés s’appuient sur des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), ce qui signifie qu’elles peuvent être des cibles de liaisons de données et des styles XAML peuvent être appliqués. En outre, les propriétés peuvent être définies à l’aide de feuilles de style en cascade (CSS). Pour plus d’informations, consultez [Propriétés spécifiques de Xamarin.Forms Shell](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties).

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

## <a name="enable-navigation-bar-shadow"></a>Activer l’ombre de la barre de navigation

La classe `Shell` définit la `NavBarHasShadow` propriété jointe, de type `bool`, qui contrôle si l’ombre de la barre de navigation est. Par défaut, la valeur de la propriété est `false`.

Bien que cette propriété puisse être définie sur un objet sous-classé `Shell`, elle peut également être définie sur n’importe quelle page qui souhaite activer l’ombre de la barre de navigation. Par exemple, le code XAML suivant illustre l’activation de l’ombre de la barre de navigation à partir d’une [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<ContentPage ...
             Shell.NavBarHasShadow="true">
    ...
</ContentPage>
```

Cela entraîne l’activation de l’ombre de la barre de navigation.

## <a name="disable-the-navigation-bar"></a>Désactiver la barre de navigation

La classe `Shell` définit la propriété jointe `NavBarIsVisible`, de type `bool`, qui contrôle la visibilité de la barre de navigation lorsqu’une page s’affiche. Par défaut, la valeur de la propriété est `true`.

Bien que cette propriété puisse être définie sur un objet `Shell` sous-classé, elle est généralement définie sur toutes les pages sur lesquelles vous souhaitez masquer la barre de navigation. Par exemple, le XAML suivant montre la désactivation de la barre de navigation sur une page [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

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

Bien que cette propriété puisse être définie sur un objet `Shell` sous-classé, elle est généralement définie sur toutes les pages sur lesquelles vous souhaitez masquer la barre d’onglets. Par exemple, le XAML suivant montre la désactivation de la barre d’onglets sur une page [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

```xaml
<ContentPage ...
             Shell.TabBarIsVisible="false">
    ...
</ContentPage>
```

Ainsi, la barre d’onglets devient invisible lorsque la page s’affiche :

![Capture d’écran de la page de Shell avec une barre d’onglet invisible, sur iOS et Android](configuration-images/tabbar-invisible.png "Page de l’interpréteur de commandes avec la barre d’onglet invisible")

## <a name="display-views-in-the-navigation-bar"></a>Affichage des vues dans la barre de navigation

La classe `Shell` définit la propriété jointe `TitleView`, de type `View`, qui permet d’afficher n’importe quel élément [`View`](xref:Xamarin.Forms.View) Xamarin.Forms dans la barre de navigation.

Bien que cette propriété puisse être définie sur un objet `Shell` sous-classé, elle peut également être définie sur toutes les pages devant afficher une vue dans la barre de navigation. Par exemple, le XAML suivant montre l’affichage d’une [`Image`](xref:Xamarin.Forms.Image) dans la barre de navigation sur une page [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

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

De nombreuses vues ne s’affichent dans la barre de navigation que si la taille de la vue est spécifiée avec les propriétés [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) et [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest), ou si l’emplacement de la vue est spécifié avec les propriétés [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) et [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions).

La classe [`Layout`](xref:Xamarin.Forms.Layout) dérive de la classe [`View`](xref:Xamarin.Forms.View), la propriété jointe `TitleView` peut donc être configurée de façon à afficher une classe de disposition qui contient plusieurs vues. De même, la classe [`ContentView`](xref:Xamarin.Forms.ContentView) dérive au final de la classe [`View`](xref:Xamarin.Forms.View), la propriété jointe `TitleView` peut donc être configurée de façon à afficher une `ContentView` qui contient plusieurs vues.

## <a name="related-links"></a>Liens connexes

- [Xaminals (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Styler des applications Xamarin.Forms avec des styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [Propriétés spécifiques du CSS Shell Xamarin.Forms](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)
