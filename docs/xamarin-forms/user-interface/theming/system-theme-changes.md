---
title: Répondre aux modifications de thème du système dans les Xamarin.Forms applications
description: Xamarin.Formsles applications peuvent répondre aux modifications de thème du système d’exploitation à l’aide du type OnAppTheme et de l’extension de balisage DynamicResource.
ms.assetid: ''
ms.prod: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2bb83f4ad5c30adccfc961938df64dda9cef1f6b
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140060"
---
# <a name="respond-to-system-theme-changes-in-xamarinforms-applications"></a>Répondre aux modifications de thème du système dans les Xamarin.Forms applications

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-systemthemesdemo/)

Les appareils incluent généralement des thèmes clairs et foncés, qui font chacun référence à un large éventail de préférences d’apparence qui peuvent être définies au niveau du système d’exploitation. Les applications doivent respecter ces thèmes système et répondre immédiatement lorsque le thème du système change.

Le thème du système peut changer pour diverses raisons, en fonction de la configuration de l’appareil. Cela comprend le thème du système qui est explicitement modifié par l’utilisateur, qu’il change en raison de l’heure de la journée et qu’il change en raison de facteurs environnementaux tels que la faible luminosité.

Xamarin.Formsles applications peuvent répondre aux modifications de thème du système en définissant des ressources avec la `AppThemeColor` classe, la `OnAppTheme<T>` classe et l' `OnAppTheme` extension de balisage. Ces ressources doivent ensuite être consommées avec l' `DynamicResource` extension de balisage.

> [!IMPORTANT]
> La réponse à une modification de thème système est actuellement expérimentale et ne peut être utilisée qu’en définissant l' `AppTheme_Experimental` indicateur. Pour plus d’informations, consultez la page [indicateurs expérimentaux](~/xamarin-forms/internals/experimental-flags.md).

Les conditions suivantes doivent être remplies pour que Xamarin.Forms réponde à un changement de thème du système :

- Xamarin.Forms4,6 ou version ultérieure.
- iOS 13 ou version ultérieure.
- Android 10 (API 29) ou supérieure.
- Build UWP 14393 ou version ultérieure.

Les captures d’écran suivantes illustrent les pages à thème, pour les thèmes système clairs et sombres sur iOS et Android :

[![Capture d’écran de la page principale d’une application à thème, sur iOS et Android](system-theme-changes-images/main-page-both-themes.png "Page principale de l’application à thème")](system-theme-changes-images/main-page-both-themes-large.png#lightbox "Page principale de l’application à thème") 
 [ ![Capture d’écran de la page de détails d’une application à thème, sur iOS et Android](system-theme-changes-images/detail-page-both-themes.png "Page de détails de l’application à thème")](system-theme-changes-images/detail-page-both-themes-large.png#lightbox "Page de détails de l’application à thème")

## <a name="define-and-consume-theme-resources"></a>Définir et utiliser des ressources de thème

Les ressources pour les thèmes clairs et foncés peuvent être définies avec la `AppThemeColor` classe, la `OnAppTheme<T>` classe et l' `OnAppTheme` extension de balisage. Avec chaque approche, ces ressources sont automatiquement appliquées en fonction de la valeur du thème système actuel. En outre, les objets qui consomment ces ressources sont automatiquement mis à jour si le thème du système change alors qu’une application est en cours d’exécution.

### <a name="appthemecolor"></a>AppThemeColor

La `AppThemeColor` classe est utilisée pour définir [`Color`](xref:Xamarin.Forms.Color) des ressources pour les thèmes système clairs et sombres. `AppThemeColor`les ressources doivent être définies dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) :

```xaml
<Application ...>
    <Application.Resources>
        <AppThemeColor x:Key="PageBackgroundColor"
                       Light="White"
                       Dark="Black" />
        <AppThemeColor x:Key="NavigationBarColor"
                       Light="WhiteSmoke"
                       Dark="Teal" />
        <AppThemeColor x:Key="PrimaryColor"
                       Light="WhiteSmoke"
                       Dark="Teal" />
        <AppThemeColor x:Key="SecondaryColor"
                       Light="Black"
                       Dark="White" />
        <AppThemeColor x:Key="PrimaryTextColor"
                       Light="Black"
                       Dark="White" />
        <AppThemeColor x:Key="SecondaryTextColor"
                       Light="White"
                       Dark="White" />
        <AppThemeColor x:Key="TertiaryTextColor"
                       Light="Gray"
                       Dark="WhiteSmoke" />
        <AppThemeColor x:Key="TransparentColor"
                       Light="Transparent"
                       Dark="Transparent" />
    </Application.Resources>
</Application>
```

Chaque `AppThemeColor` ressource doit avoir un `x:Key` attribut, ce qui lui donne une clé descriptive dans le [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . La valeur des `Light` Propriétés et `Dark` doit être des [`Color`](xref:Xamarin.Forms.Color) objets. En outre, une `Default` propriété peut être définie sur un `Color` à utiliser par défaut par l’objet consommateur.

`AppThemeColor`les ressources peuvent être consommées en ligne :

```xaml
<Label Text="This monkey reacts appropriately to ridiculous assertions and actions"
       TextColor="{DynamicResource PrimaryTextColor}" />
```

Les `AppThemeColor` ressources peuvent également être consommées par des objets implicites ou explicites [`Style`](xref:Xamarin.Forms.Style) :

```xaml
<Style TargetType="NavigationPage">
    <Setter Property="BarBackgroundColor"
            Value="{DynamicResource NavigationBarColor}" />
    <Setter Property="BarTextColor"
            Value="{DynamicResource SecondaryColor}" />
</Style>
```

> [!IMPORTANT]
> `AppThemeColor`les ressources doivent être consommées avec l' `DynamicResource` extension de balisage. Cela garantit que l’apparence de l’objet consommateur est mise à jour lorsque le thème du système change.

### <a name="onappthemelttgt"></a>OnAppTheme &lt; T&gt;

La `OnAppTheme<T>` classe est utilisée pour définir des ressources de tout type pour les thèmes de système clair et sombre. `OnAppTheme<T>`les ressources doivent être définies dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , avec l' `T` argument spécifié comme valeur de l' `x:TypeArguments` attribut :

```xaml
<Application ...>
    <Application.Resources>
        <OnAppTheme x:Key="ImageLogo"
                    x:TypeArguments="FileImageSource"
                    Light="lightlogo.png"
                    Dark="darklogo.png" />
    </Application.Resources>
</Application>
```

Chaque `OnAppTheme<T>` ressource doit avoir un `x:Key` attribut, ce qui lui donne une clé descriptive dans le [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . La valeur des `Light` Propriétés et `Dark` doit être des objets du type défini en tant qu' `x:TypeArguments` attribut. En outre, une `Default` propriété peut être définie sur un objet de type `T` à utiliser par défaut par l’objet consommateur.

`OnAppTheme<T>`les ressources peuvent être consommées en ligne :

```xaml
<Image Source="{DynamicResource ImageLogo}"
       Aspect="AspectFit"
       HeightRequest="200" /
```

Les `OnAppTheme<T>` ressources peuvent également être consommées par des objets implicites ou explicites [`Style`](xref:Xamarin.Forms.Style) :

```xaml
<Style x:Key="imageLogoStyle"
       TargetType="Image">
    <Setter Property="Source"
            Value="{DynamicResource ImageLogo}" />
    <Setter Property="Aspect"
            Value="AspectFit" />
</Style>
```

> [!IMPORTANT]
> `OnAppTheme<T>`les ressources doivent être consommées avec l' `DynamicResource` extension de balisage. Cela garantit que l’apparence de l’objet consommateur est mise à jour lorsque le thème du système change.

### <a name="onapptheme-markup-extension"></a>Extension de balisage OnAppTheme

L' `OnAppTheme` extension de balisage vous permet de spécifier une ressource à consommer, telle qu’une image ou une couleur, en fonction du thème système actuel. Il fournit les mêmes fonctionnalités que la `OnAppTheme<T>` classe, mais avec une représentation plus concise :

```xaml
<ContentPage ...>
    <StackLayout Margin="20">
        <Label Text="This text is green in light mode, and red in dark mode."
               TextColor="{OnAppTheme Light=Green, Dark=Red}" />
        <Image Source="{OnAppTheme Light=lightlogo.png, Dark=darklogo.png}" />
    </StackLayout>
</ContentPage>
```

Dans cet exemple, la couleur de texte du premier [`Label`](xref:Xamarin.Forms.Label) est définie sur la couleur verte lorsque l’appareil utilise son thème clair, et est défini sur rouge lorsque l’appareil utilise son thème sombre. De même, [`Image`](xref:Xamarin.Forms.Image) affiche un fichier image différent basé sur le thème système actuel.

Pour plus d’informations sur l' `OnAppTheme` extension de balisage, consultez [OnAppTheme Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#onapptheme-markup-extension).

## <a name="detect-the-current-system-theme"></a>Détecter le thème du système actuel

Le thème système actuel peut être détecté en obtenant la valeur de la `Application.RequestedTheme` propriété :

```csharp
OSAppTheme currentTheme = Application.Current.RequestedTheme;
```

La `RequestedTheme` propriété retourne un `OSAppTheme` membre de l’énumération. L’énumération `OSAppTheme` définit les membres suivants :

- `Unspecified`, qui indique que l’appareil utilise un thème non spécifié.
- `Light`, qui indique que l’appareil utilise son thème clair.
- `Dark`, qui indique que l’appareil utilise son thème sombre.

## <a name="react-to-theme-changes"></a>Réagir aux changements de thème

Le thème du système sur un appareil peut changer pour diverses raisons, en fonction de la configuration de l’appareil. Xamarin.Formsles applications peuvent être averties lorsque le thème du système change en gérant l' `Application.RequestedThemeChanged` événement :

```csharp
Application.Current.RequestedThemeChanged += (s, a) =>
{
    // Respond to the theme change
};
```

L' `AppThemeChangedEventArgs` objet, qui accompagne l' `RequestedThemeChanged` événement, a une propriété unique nommée `RequestedTheme` , de type `OSAppTheme` . Cette propriété peut être examinée pour détecter le thème système demandé.

## <a name="related-links"></a>Liens connexes

- [SystemThemes (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-systemthemesdemo/)
- [Extension de balisage OnAppTheme](~/xamarin-forms/xaml/markup-extensions/consuming.md#onapptheme-markup-extension)
- [Dictionnaires de ressources](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Styles dynamiques dansXamarin.Forms](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)
- [Styliser des Xamarin.Forms applications à l’aide de styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
