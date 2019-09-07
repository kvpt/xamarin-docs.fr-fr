---
title: Styles de texte de Xamarin.Forms
description: Cet article explique comment le style du texte dans les applications Xamarin.Forms. Styles peuvent être définies une seule fois et utilisés par plusieurs vues, mais un style peut uniquement être utilisé avec vues d’un type.
ms.prod: xamarin
ms.assetid: 57C0CFD6-A568-46B8-ADA1-BF25681893CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 66d7ae722281d9034cb4cdf1501662a7de396c2d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770252"
---
# <a name="xamarinforms-text-styles"></a>Styles de texte de Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Style du texte dans Xamarin.Forms_

Styles peuvent être utilisés pour ajuster l’apparence des étiquettes, des entrées et des éditeurs. Styles peuvent être définies une seule fois et utilisés par plusieurs vues, mais un style peut uniquement être utilisé avec vues d’un type.
Styles peuvent recevoir un `Key` et appliqués de manière sélective à l’aide d’un contrôle spécifique `Style` propriété.

<a name="Built-In_Styles" />

## <a name="built-in-styles"></a>Styles intégrés

Xamarin.Forms inclut plusieurs [intégrés](xref:Xamarin.Forms.Device.Styles) styles pour les scénarios courants :

- `BodyStyle`
- `CaptionStyle`
- `ListItemDetailTextStyle`
- `ListItemTextStyle`
- `SubtitleStyle`
- `TitleStyle`

Pour appliquer des styles intégrés, utilisez le `DynamicResource` extension de balisage pour spécifier le style :

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

En c#, les styles intégrés sont sélectionnés à partir de `Device.Styles`:

```csharp
label.Style = Device.Styles.TitleStyle;
```

![Exemple de styles de périphérique](styles-images/builtinstyles.png)

<a name="Custom_Styles" />

## <a name="custom-styles"></a>Styles personnalisés

Styles se composent des accesseurs Set et accesseurs Set se composent des propriétés et les propriétés les valeurs seront définies.

Dans c#, un style personnalisé pour une étiquette avec un texte rouge de taille 30 serait défini comme suit :

```csharp
var LabelStyle = new Style (typeof(Label)) {
    Setters = {
        new Setter {Property = Label.TextColorProperty, Value = Color.Red},
        new Setter {Property = Label.FontSizeProperty, Value = 30}
    }
};

var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

Dans XAML :

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <Style x:Key="LabelStyle" TargetType="Label">
            <Setter Property="TextColor" Value="Red"/>
            <Setter Property="FontSize" Value="30"/>
        </Style>
    </ResourceDictionary>
</ContentPage.Resources>

<ContentPage.Content>
    <StackLayout>
        <Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
    </StackLayout>
</ContentPage.Content>
```

Notez que les ressources (y compris tous les styles) sont définis dans `ContentPage.Resources`, qui est un frère de plus familiers `ContentPage.Content` élément.

![Exemple de styles personnalisés](styles-images/customstyle.png)

<a name="Applying_Styles" />

## <a name="applying-styles"></a>Application de Styles

Une fois qu’un style a été créé, il peut être appliqué à toute correspondance de vue ses `TargetType`.

Dans XAML, les styles personnalisés sont appliqués aux vues en fournissant leurs `Style` propriété avec un `StaticResource` extension de balisage référençant le style souhaité :

```xaml
<Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
```

En c#, styles peuvent soit être appliqués directement à une vue ou ajoutés à et récupérées à partir d’une page `ResourceDictionary`. Pour ajouter directement :

```csharp
var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

Pour ajouter et récupérer à partir de la page `ResourceDictionary`:

```csharp
this.Resources.Add ("LabelStyle", LabelStyle);
label.Style = (Style)Resources["LabelStyle"];
```

Styles intégrés sont appliqués de manière différente, car ils doivent pouvoir répondre aux paramètres d’accessibilité. Pour appliquer des styles intégrés dans XAML, le `DynamicResource` extension de balisage est utilisée :

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

En c#, les styles intégrés sont sélectionnés à partir de `Device.Styles`:

```csharp
label.Style = Device.Styles.TitleStyle;
```

## <a name="accessibility"></a>Accessibilité

Les styles intégrés existent pour le rendre plus facile de respecter les préférences d’accessibilité. Lorsque vous utilisez un des styles intégrés, les tailles de police augmentera automatiquement si un utilisateur configure leurs préférences d’accessibilité en conséquence.

Prenons l’exemple suivant de la même page de vues mis en forme avec les styles intégrés avec les paramètres d’accessibilité activés et désactivés :

Désactivé :

![Styles d’appareil avec accessibilité désactivée](styles-images/pre-access.png)

Activé :

![Styles d’appareil avec accessibilité activée](styles-images/post-access.png)

Pour garantir l’accessibilité, assurez-vous que les styles intégrés sont utilisés comme base pour tous les styles de texte au sein de votre application, et que vous utilisez les styles de manière cohérente. Consultez [Styles](~/xamarin-forms/user-interface/styles/index.md) pour plus d’informations sur l’extension et l’utilisation des styles en général.

## <a name="related-links"></a>Liens associés

- [Création d’applications mobiles avec Xamarin.Forms, chapitre 12](https://developer.xamarin.com/r/xamarin-forms/book/chapter12.pdf)
- [Styles](~/xamarin-forms/user-interface/styles/index.md)
- [Texte (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Style](xref:Xamarin.Forms.Style)
