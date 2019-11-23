---
title: Thèmes d’une application Xamarin. Forms
description: Ils peuvent être implémentés dans les applications Xamarin. Forms en créant un ResourceDictionary pour chaque thème, puis en chargeant les ressources avec l’extension de balisage DynamicResource.
ms.prod: xamarin
ms.assetId: B7B17F66-4E37-4B50-9A57-351B62BE4FED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2019
ms.openlocfilehash: 3e0f508a9c980c02681f1be581846f9f2f25e2d0
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "69529280"
---
# <a name="theming-a-xamarinforms-application"></a>Thèmes d’une application Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-theming/)

Les applications Xamarin. Forms peuvent répondre de manière dynamique aux modifications de style au moment de l’exécution à l’aide de l’extension de balisage `DynamicResource`. Cette extension de balisage est similaire à l’extension de balisage `StaticResource`, dans le sens où les deux utilisent une clé de dictionnaire pour extraire une valeur d’une [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). Toutefois, tandis que l’extension de balisage `StaticResource` effectue une recherche dans un dictionnaire unique, l’extension de balisage `DynamicResource` maintient un lien vers la clé de dictionnaire. Par conséquent, si la valeur associée à la clé est remplacée, la modification est appliquée au [`VisualElement`](xref:Xamarin.Forms.VisualElement). Cela permet d’implémenter les thèmes d’exécution dans les applications Xamarin. Forms.

Le processus d’implémentation de la mise en œuvre du runtime dans une application Xamarin. Forms se présente comme suit :

1. Définissez les ressources pour chaque thème dans une [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary).
1. Consommez les ressources de thème dans l’application, à l’aide de l’extension de balisage `DynamicResource`.
1. Définissez un thème par défaut dans le fichier **app. Xaml** de l’application.
1. Ajoutez du code pour charger un thème au moment de l’exécution.

Les captures d’écran suivantes illustrent les pages à thème, avec l’application iOS utilisant un thème clair et l’application Android à l’aide d’un thème sombre :

[![Capture d’écran de la page principale d’une application à thème, sur iOS et android](theming-images/main-page-both-themes.png "Page principale de l’application à thème")](theming-images/main-page-both-themes-large.png#lightbox "Page principale de l’application à thème")
[ ![capture d’écran de la page de détails d’une application à thème, sur iOS et Android](theming-images/detail-page-both-themes.png "Page de détails de l’application à thème")](theming-images/detail-page-both-themes-large.png#lightbox "Page de détails de l’application à thème")

## <a name="define-themes"></a>Définir des thèmes

Un thème est défini comme une collection d’objets de ressource stockés dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary).

L’exemple suivant montre l' `LightTheme` à partir de l’exemple d’application :

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                    x:Class="ThemingDemo.LightTheme">
    <Color x:Key="PageBackgroundColor">White</Color>
    <Color x:Key="NavigationBarColor">WhiteSmoke</Color>
    <Color x:Key="PrimaryColor">WhiteSmoke</Color>
    <Color x:Key="SecondaryColor">Black</Color>
    <Color x:Key="PrimaryTextColor">Black</Color>
    <Color x:Key="SecondaryTextColor">White</Color>
    <Color x:Key="TertiaryTextColor">Gray</Color>
    <Color x:Key="TransparentColor">Transparent</Color>
</ResourceDictionary>
```

L’exemple suivant montre l' `DarkTheme` à partir de l’exemple d’application :

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                    x:Class="ThemingDemo.DarkTheme">
    <Color x:Key="PageBackgroundColor">Black</Color>
    <Color x:Key="NavigationBarColor">Teal</Color>
    <Color x:Key="PrimaryColor">Teal</Color>
    <Color x:Key="SecondaryColor">White</Color>
    <Color x:Key="PrimaryTextColor">White</Color>
    <Color x:Key="SecondaryTextColor">White</Color>
    <Color x:Key="TertiaryTextColor">WhiteSmoke</Color>
    <Color x:Key="TransparentColor">Transparent</Color>
</ResourceDictionary>
```

Chaque [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) contient des ressources [`Color`](xref:Xamarin.Forms.Color) qui définissent leurs thèmes respectifs, chaque `ResourceDictionary` utilisant des valeurs de clé identiques. Pour plus d’informations sur les dictionnaires de ressources, consultez [dictionnaires de ressources](~/xamarin-forms/xaml/resource-dictionaries.md).

> [!IMPORTANT]
> Un fichier code-behind est requis pour chaque `ResourceDictionary`, qui appelle la méthode `InitializeComponent`. Cela est nécessaire pour qu’un objet CLR représentant le thème choisi puisse être créé au moment de l’exécution.

## <a name="set-a-default-theme"></a>Définir un thème par défaut

Une application requiert un thème par défaut, afin que les contrôles aient des valeurs pour les ressources qu’elles consomment. Un thème par défaut peut être défini en fusionnant le [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) du thème dans le `ResourceDictionary` de niveau application défini dans **app. Xaml**:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ThemingDemo.App">
    <Application.Resources>
        <ResourceDictionary Source="Themes/LightTheme.xaml" />
    </Application.Resources>
</Application>
```

Pour plus d’informations sur la fusion des dictionnaires de ressources, consultez [dictionnaires de ressources fusionnés](~/xamarin-forms/xaml/resource-dictionaries.md#merged-resource-dictionaries).

## <a name="consume-theme-resources"></a>Consommer des ressources de thème

Quand une application souhaite consommer une ressource stockée dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) qui représente un thème, elle doit le faire avec l’extension de balisage `DynamicResource`. Cela garantit que si un autre thème est sélectionné lors de l’exécution, les valeurs du nouveau thème seront appliquées.

L’exemple suivant montre trois styles de l’exemple d’application qui peuvent être appliqués à des objets [`Label`](xref:Xamarin.Forms.Label) :

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ThemingDemo.App">
    <Application.Resources>

        <Style x:Key="LargeLabelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{DynamicResource SecondaryTextColor}" />
            <Setter Property="FontSize"
                    Value="30" />
        </Style>

        <Style x:Key="MediumLabelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{DynamicResource PrimaryTextColor}" />
            <Setter Property="FontSize"
                    Value="25" />
        </Style>

        <Style x:Key="SmallLabelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{DynamicResource TertiaryTextColor}" />
            <Setter Property="FontSize"
                    Value="15" />
        </Style>

    </Application.Resources>
</Application>
```

Ces styles sont définis dans le dictionnaire de ressources de niveau application, afin qu’ils puissent être utilisés par plusieurs pages. Chaque style consomme des ressources de thème avec l’extension de balisage `DynamicResource`.

Ces styles sont ensuite consommés par les pages :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ThemingDemo"
             x:Class="ThemingDemo.UserSummaryPage"
             Title="User Summary"
             BackgroundColor="{DynamicResource PageBackgroundColor}">
    ...
    <ScrollView>
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="200" />
                <RowDefinition Height="120" />
                <RowDefinition Height="70" />
            </Grid.RowDefinitions>
            <Grid BackgroundColor="{DynamicResource PrimaryColor}">
                <Label Text="Face-Palm Monkey"
                       VerticalOptions="Center"
                       Margin="15"
                       Style="{StaticResource MediumLabelStyle}" />
                ...
            </Grid>
            <StackLayout Grid.Row="1"
                         Margin="10">
                <Label Text="This monkey reacts appropriately to ridiculous assertions and actions."
                       Style="{StaticResource SmallLabelStyle}" />
                <Label Text="  &#x2022; Cynical but not unfriendly."
                       Style="{StaticResource SmallLabelStyle}" />
                <Label Text="  &#x2022; Seven varieties of grimaces."
                       Style="{StaticResource SmallLabelStyle}" />
                <Label Text="  &#x2022; Doesn't laugh at your jokes."
                       Style="{StaticResource SmallLabelStyle}" />
            </StackLayout>
            ...
        </Grid>
    </ScrollView>
</ContentPage>
```

Quand une ressource de thème est consommée directement, elle doit être consommée avec l’extension de balisage `DynamicResource`. Toutefois, lorsqu’un style qui utilise l’extension de balisage `DynamicResource` est consommé, il doit être consommé avec l’extension de balisage `StaticResource`.

Pour plus d’informations sur les styles, consultez application d’un [style aux applications Xamarin. Forms à l’aide de styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md). Pour plus d’informations sur l’extension de balisage `DynamicResource`, consultez [styles dynamiques dans Xamarin. Forms](~/xamarin-forms/user-interface/styles/xaml/dynamic.md).

## <a name="load-a-theme-at-runtime"></a>Charger un thème au moment de l’exécution

Quand un thème est sélectionné lors de l’exécution, l’application doit :

1. Supprimer le thème actuel de l’application. Pour ce faire, vous devez effacer la propriété [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) du [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)au niveau de l’application.
2. Charge le thème sélectionné. Pour ce faire, vous devez ajouter une instance du thème sélectionné à la propriété `MergedDictionaries` du `ResourceDictionary`au niveau de l’application.

Les objets [`VisualElement`](xref:Xamarin.Forms.VisualElement) qui définissent des propriétés avec l’extension de balisage `DynamicResource` appliquent ensuite les nouvelles valeurs de thème. Cela est dû au fait que l’extension de balisage `DynamicResource` maintient un lien vers les clés de dictionnaire. Par conséquent, lorsque les valeurs associées aux clés sont remplacées, les modifications sont appliquées aux objets `VisualElement`.

Dans l’exemple d’application, un thème est sélectionné à l’aide d’une page modale qui contient un [`Picker`](xref:Xamarin.Forms.Picker). Le code suivant illustre la méthode `OnPickerSelectionChanged`, qui est exécutée lorsque le thème sélectionné change :

```csharp
void OnPickerSelectionChanged(object sender, EventArgs e)
{
    Picker picker = sender as Picker;
    Theme theme = (Theme)picker.SelectedItem;

    ICollection<ResourceDictionary> mergedDictionaries = Application.Current.Resources.MergedDictionaries;
    if (mergedDictionaries != null)
    {
        mergedDictionaries.Clear();

        switch (theme)
        {
            case Theme.Dark:
                mergedDictionaries.Add(new DarkTheme());
                break;
            case Theme.Light:
            default:
                mergedDictionaries.Add(new LightTheme());
                break;
        }
    }
}
```

## <a name="related-links"></a>Liens connexes

- [Thèmes (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-theming/)
- [Dictionnaires de ressources](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Styles dynamiques dans Xamarin. Forms](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)
- [Styler des applications Xamarin.Forms avec des styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
