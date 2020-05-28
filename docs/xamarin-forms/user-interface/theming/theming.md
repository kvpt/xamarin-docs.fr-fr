---
title: Thème d’une Xamarin.Forms application
description: Ils peuvent être implémentés dans les Xamarin.Forms applications en créant un ResourceDictionary pour chaque thème, puis en chargeant les ressources avec l’extension de balisage DynamicResource.
ms.prod: ''
ms.assetId: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3341ada6c5605917eeec79aac96e38cb99b40fc4
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138201"
---
# <a name="theme-a-xamarinforms-application"></a>Thème d’une Xamarin.Forms application

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-theming/)

Xamarin.Formsles applications peuvent répondre de manière dynamique aux modifications de style au moment de l’exécution à l’aide de l' `DynamicResource` extension de balisage. Cette extension de balisage est similaire à l' `StaticResource` extension de balisage, dans le sens où les deux utilisent une clé de dictionnaire pour extraire une valeur d’un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . Toutefois, si l' `StaticResource` extension de balisage effectue une recherche dans un dictionnaire unique, l' `DynamicResource` extension de balisage maintient un lien vers la clé du dictionnaire. Par conséquent, si la valeur associée à la clé est remplacée, la modification est appliquée à [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Cela permet d’implémenter les thèmes d’exécution dans les Xamarin.Forms applications.

Le processus d’implémentation du runtime dans une Xamarin.Forms application est le suivant :

1. Définissez les ressources pour chaque thème dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) .
1. Consommez les ressources de thème dans l’application, à l’aide de l' `DynamicResource` extension de balisage.
1. Définissez un thème par défaut dans le fichier **app. Xaml** de l’application.
1. Ajoutez du code pour charger un thème au moment de l’exécution.

> [!IMPORTANT]
> Utilisez l' `StaticResource` extension de balisage si vous n’avez pas besoin de modifier le thème de l’application au moment de l’exécution.

Les captures d’écran suivantes illustrent les pages à thème, avec l’application iOS utilisant un thème clair et l’application Android à l’aide d’un thème sombre :

[![Capture d’écran de la page principale d’une application à thème, sur iOS et Android](theming-images/main-page-both-themes.png "Page principale de l’application à thème")](theming-images/main-page-both-themes-large.png#lightbox "Page principale de l’application à thème") 
 [ ![Capture d’écran de la page de détails d’une application à thème, sur iOS et Android](theming-images/detail-page-both-themes.png "Page de détails de l’application à thème")](theming-images/detail-page-both-themes-large.png#lightbox "Page de détails de l’application à thème")

> [!NOTE]
> La modification d’un thème au moment de l’exécution requiert l’utilisation de styles XAML et n’est actuellement pas possible avec CSS.

## <a name="define-themes"></a>Définir des thèmes

Un thème est défini comme une collection d’objets de ressource stockés dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) .

L’exemple suivant montre le `LightTheme` à partir de l’exemple d’application :

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

L’exemple suivant montre le `DarkTheme` à partir de l’exemple d’application :

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

Chaque [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) contient des [`Color`](xref:Xamarin.Forms.Color) ressources qui définissent leurs thèmes respectifs, chacun `ResourceDictionary` utilisant des valeurs de clé identiques. Pour plus d’informations sur les dictionnaires de ressources, consultez [dictionnaires de ressources](~/xamarin-forms/xaml/resource-dictionaries.md).

> [!IMPORTANT]
> Un fichier code-behind est requis pour chaque `ResourceDictionary` , qui appelle la `InitializeComponent` méthode. Cela est nécessaire pour qu’un objet CLR représentant le thème choisi puisse être créé au moment de l’exécution.

## <a name="set-a-default-theme"></a>Définir un thème par défaut

Une application requiert un thème par défaut, afin que les contrôles aient des valeurs pour les ressources qu’elles consomment. Un thème par défaut peut être défini en fusionnant le thème du [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) au niveau de l’application `ResourceDictionary` défini dans **app. Xaml**:

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

Quand une application souhaite consommer une ressource stockée dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) qui représente un thème, elle doit le faire avec l’extension de `DynamicResource` balisage. Cela garantit que si un autre thème est sélectionné lors de l’exécution, les valeurs du nouveau thème seront appliquées.

L’exemple suivant montre trois styles de l’exemple d’application qui peuvent être appliqués aux [`Label`](xref:Xamarin.Forms.Label) objets :

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

Ces styles sont définis dans le dictionnaire de ressources de niveau application, afin qu’ils puissent être utilisés par plusieurs pages. Chaque style consomme des ressources de thème avec l' `DynamicResource` extension de balisage.

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

Quand une ressource de thème est consommée directement, elle doit être consommée avec l' `DynamicResource` extension de balisage. Toutefois, lorsqu’un style qui utilise l' `DynamicResource` extension de balisage est consommé, il doit être consommé avec l' `StaticResource` extension de balisage.

Pour plus d’informations sur les styles, consultez application d’un [style aux Xamarin.Forms applications à l’aide de styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md). Pour plus d’informations sur l' `DynamicResource` extension de balisage, consultez [styles dynamiques dans Xamarin.Forms ](~/xamarin-forms/user-interface/styles/xaml/dynamic.md).

## <a name="load-a-theme-at-runtime"></a>Charger un thème au moment de l’exécution

Quand un thème est sélectionné lors de l’exécution, l’application doit :

1. Supprimer le thème actuel de l’application. Pour ce faire, vous devez effacer la [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) propriété du niveau de l’application [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) .
2. Charge le thème sélectionné. Pour ce faire, vous devez ajouter une instance du thème sélectionné à la `MergedDictionaries` propriété du niveau de l’application `ResourceDictionary` .

Tous les [`VisualElement`](xref:Xamarin.Forms.VisualElement) objets qui définissent des propriétés avec l' `DynamicResource` extension de balisage appliquent ensuite les nouvelles valeurs de thème. Cela est dû au fait que l' `DynamicResource` extension de balisage maintient un lien vers les clés de dictionnaire. Par conséquent, lorsque les valeurs associées aux clés sont remplacées, les modifications sont appliquées aux `VisualElement` objets.

Dans l’exemple d’application, un thème est sélectionné à l’aide d’une page modale qui contient un [`Picker`](xref:Xamarin.Forms.Picker) . Le code suivant montre la `OnPickerSelectionChanged` méthode, qui est exécutée lorsque le thème sélectionné change :

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
- [Répondre aux modifications du thème du système](system-theme-changes.md)
- [Dictionnaires de ressources](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Styles dynamiques dansXamarin.Forms](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)
- [Styliser des Xamarin.Forms applications à l’aide de styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
