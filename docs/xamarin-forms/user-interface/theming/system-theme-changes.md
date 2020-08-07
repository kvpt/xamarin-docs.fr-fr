---
title: Répondre aux modifications de thème du système dans les Xamarin.Forms applications
description: Xamarin.Formsles applications peuvent répondre aux modifications de thème du système d’exploitation à l’aide du type OnAppTheme et de l’extension de balisage DynamicResource.
ms.assetid: D10506DD-BAA0-437F-A4AD-882D16E7B60D
ms.prod: xamarin
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/06/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: efd9cabc9504a9fa56f1f7a763013e6852be9382
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918099"
---
# <a name="respond-to-system-theme-changes-in-no-locxamarinforms-applications"></a>Répondre aux modifications de thème du système dans les Xamarin.Forms applications

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-systemthemesdemo/)

Les appareils incluent généralement des thèmes clairs et foncés, qui font chacun référence à un large éventail de préférences d’apparence qui peuvent être définies au niveau du système d’exploitation. Les applications doivent respecter ces thèmes système et répondre immédiatement lorsque le thème du système change.

Le thème du système peut changer pour diverses raisons, en fonction de la configuration de l’appareil. Cela comprend le thème du système qui est explicitement modifié par l’utilisateur, qu’il change en raison de l’heure de la journée et qu’il change en raison de facteurs environnementaux tels que la faible luminosité.

Xamarin.Formsles applications peuvent répondre aux modifications de thème du système en consommant des ressources avec l' `AppThemeBinding` extension de balisage, ainsi que les `SetAppThemeColor` méthodes d' `SetOnAppTheme<T>` extension et.

Les conditions suivantes doivent être remplies pour que Xamarin.Forms réponde à un changement de thème du système :

- Xamarin.Forms4.6.0.967 ou version ultérieure.
- iOS 13 ou version ultérieure.
- Android 10 (API 29) ou supérieure.
- Build UWP 14393 ou version ultérieure.

Les captures d’écran suivantes illustrent les pages à thème, pour les thèmes système clairs et sombres sur iOS et Android :

[![Capture d’écran de la page principale d’une application à thème, sur iOS et Android](system-theme-changes-images/main-page-both-themes.png "Page principale de l’application à thème")](system-theme-changes-images/main-page-both-themes-large.png#lightbox "Page principale de l’application à thème") 
 [ ![Capture d’écran de la page de détails d’une application à thème, sur iOS et Android](system-theme-changes-images/detail-page-both-themes.png "Page de détails de l’application à thème")](system-theme-changes-images/detail-page-both-themes-large.png#lightbox "Page de détails de l’application à thème")

## <a name="define-and-consume-theme-resources"></a>Définir et utiliser des ressources de thème

Les ressources pour les thèmes clairs et foncés peuvent être consommées avec l' `AppThemeBinding` extension de balisage, ainsi que les `SetAppThemeColor` méthodes d' `SetOnAppTheme<T>` extension et. Avec ces approches, les ressources sont automatiquement appliquées en fonction de la valeur du thème système actuel. En outre, les objets qui consomment ces ressources sont automatiquement mis à jour si le thème du système change alors qu’une application est en cours d’exécution.

### <a name="appthemebinding-markup-extension"></a>Extension de balisage AppThemeBinding

L' `AppThemeBinding` extension de balisage vous permet de consommer une ressource, telle qu’une image ou une couleur, en fonction du thème du système actuel :

```xaml
<ContentPage ...>
    <StackLayout Margin="20">
        <Label Text="This text is green in light mode, and red in dark mode."
               TextColor="{AppThemeBinding Light=Green, Dark=Red}" />
        <Image Source="{AppThemeBinding Light=lightlogo.png, Dark=darklogo.png}" />
    </StackLayout>
</ContentPage>
```

Dans cet exemple, la couleur de texte du premier [`Label`](xref:Xamarin.Forms.Label) est définie sur la couleur verte lorsque l’appareil utilise son thème clair, et est défini sur rouge lorsque l’appareil utilise son thème sombre. De même, [`Image`](xref:Xamarin.Forms.Image) affiche un fichier image différent basé sur le thème système actuel.

En outre, les ressources définies dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) peuvent être consommées avec l' `StaticResource` extension de balisage :

```xaml
<ContentPage ...>
    <ContentPage.Resources>

        <!-- Light colors -->
        <Color x:Key="LightPrimaryColor">WhiteSmoke</Color>
        <Color x:Key="LightSecondaryColor">Black</Color>

        <!-- Dark colors -->
        <Color x:Key="DarkPrimaryColor">Teal</Color>
        <Color x:Key="DarkSecondaryColor">White</Color>

        <Style x:Key="ButtonStyle"
               TargetType="Button">
            <Setter Property="BackgroundColor"
                    Value="{AppThemeBinding Light={StaticResource LightPrimaryColor}, Dark={StaticResource DarkPrimaryColor}}" />
            <Setter Property="TextColor"
                    Value="{AppThemeBinding Light={StaticResource LightSecondaryColor}, Dark={StaticResource DarkSecondaryColor}}" />
        </Style>

    </ContentPage.Resources>

    <Grid BackgroundColor="{AppThemeBinding Light={StaticResource LightPrimaryColor}, Dark={StaticResource DarkPrimaryColor}}">
      <Button Text="MORE INFO"
              Style="{StaticResource ButtonStyle}" />
    </Grid>    
</ContentPage>    
```

Dans cet exemple, la couleur d’arrière-plan du [`Grid`](xref:Xamarin.Forms.Grid) et du [`Button`](xref:Xamarin.Forms.Button) style change selon que l’appareil utilise ou non son thème clair ou le thème sombre.

Pour plus d’informations sur l' `AppThemeBinding` extension de balisage, consultez [AppThemeBinding Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#appthemebinding-markup-extension).

### <a name="extension-methods"></a>Méthodes d’extension

Xamarin.Formscomprend `SetAppThemeColor` les `SetOnAppTheme<T>` méthodes d’extension et qui permettent [`VisualElement`](xref:Xamarin.Forms.VisualElement) aux objets de répondre aux modifications apportées au thème du système.

La `SetAppThemeColor` méthode permet [`Color`](xref:Xamarin.Forms.Color) de spécifier des objets qui seront définis sur une propriété cible en fonction du thème système actuel :

```csharp
Label label = new Label();
label.SetAppThemeColor(Label.TextColorProperty, Color.Green, Color.Red);
```

Dans cet exemple, la couleur de texte du [`Label`](xref:Xamarin.Forms.Label) est définie sur Green lorsque l’appareil utilise son thème clair, et est défini sur rouge lorsque l’appareil utilise son thème sombre.

La `SetOnAppTheme<T>` méthode permet de spécifier des objets de type `T` qui seront définis sur une propriété cible en fonction du thème système actuel :

```csharp
Image image = new Image();
image.SetOnAppTheme<FileImageSource>(Image.SourceProperty, "lightlogo.png", "darklogo.png");
```

Dans cet exemple, le [`Image`](xref:Xamarin.Forms.Image) s’affiche `lightlogo.png` lorsque l’appareil utilise son thème clair et `darklogo.png` lorsque l’appareil utilise son thème sombre.

## <a name="detect-the-current-system-theme"></a>Détecter le thème du système actuel

Le thème système actuel peut être détecté en obtenant la valeur de la `Application.RequestedTheme` propriété :

```csharp
OSAppTheme currentTheme = Application.Current.RequestedTheme;
```

La `RequestedTheme` propriété retourne un `OSAppTheme` membre de l’énumération. L’énumération `OSAppTheme` définit les membres suivants :

- `Unspecified`, qui indique que l’appareil utilise un thème non spécifié.
- `Light`, qui indique que l’appareil utilise son thème clair.
- `Dark`, qui indique que l’appareil utilise son thème sombre.

## <a name="set-the-current-user-theme"></a>Définir le thème de l’utilisateur actuel

Le thème utilisé par l’application peut être défini avec la `Application.UserAppTheme` propriété, qui est de type `OSAppTheme` , quel que soit le thème système actuellement opérationnel :

```csharp
Application.Current.UserAppTheme = OSAppTheme.Dark;
```

Dans cet exemple, l’application est configurée pour utiliser le thème défini pour le mode d’obscurité du système, quel que soit le thème du système actuellement opérationnel.

> [!NOTE]
> Affectez à la `UserAppTheme` propriété la `OSAppTheme.Unspecified` valeur par défaut pour le thème du système opérationnel.

## <a name="react-to-theme-changes"></a>Réagir aux changements de thème

Le thème du système sur un appareil peut changer pour diverses raisons, en fonction de la configuration de l’appareil. Xamarin.Formsles applications peuvent être averties lorsque le thème du système change en gérant l' `Application.RequestedThemeChanged` événement :

```csharp
Application.Current.RequestedThemeChanged += (s, a) =>
{
    // Respond to the theme change
};
```

L' `AppThemeChangedEventArgs` objet, qui accompagne l' `RequestedThemeChanged` événement, a une propriété unique nommée `RequestedTheme` , de type `OSAppTheme` . Cette propriété peut être examinée pour détecter le thème système demandé.

> [!IMPORTANT]
> Pour répondre aux modifications de thème sur Android, vous devez inclure l' `ConfigChanges.UiMode` indicateur dans l' `Activity` attribut de votre `MainActivity` classe.

## <a name="related-links"></a>Liens connexes

- [SystemThemes (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-systemthemesdemo/)
- [Extension de balisage AppThemeBinding](~/xamarin-forms/xaml/markup-extensions/consuming.md#appthemebinding-markup-extension)
- [Dictionnaires de ressources](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Styliser des Xamarin.Forms applications à l’aide de styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
