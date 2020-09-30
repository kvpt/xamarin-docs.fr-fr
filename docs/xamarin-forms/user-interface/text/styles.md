---
title: Xamarin.Forms Styles de texte
description: Cet article explique comment styliser du texte dans des Xamarin.Forms applications. Les styles peuvent être définis une fois et utilisés par de nombreuses vues, mais un style peut être utilisé uniquement avec les vues d’un seul type.
ms.prod: xamarin
ms.assetid: 57C0CFD6-A568-46B8-ADA1-BF25681893CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e2c6898439da5869d79bc500643ca3cfc52c2e3f
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557554"
---
# <a name="no-locxamarinforms-text-styles"></a>Xamarin.Forms Styles de texte

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Styliser du texte dans Xamarin.Forms_

Les styles peuvent être utilisés pour ajuster l’apparence des étiquettes, des entrées et des éditeurs. Les styles peuvent être définis une fois et utilisés par de nombreuses vues, mais un style peut être utilisé uniquement avec les vues d’un seul type.
Les styles peuvent être attribués `Key` et appliqués de manière sélective à l’aide de la propriété d’un contrôle spécifique `Style` .

## <a name="built-in-styles"></a>Styles intégrés

Xamarin.Forms comprend plusieurs styles [intégrés](xref:Xamarin.Forms.Device.Styles) pour les scénarios courants :

- `BodyStyle`
- `CaptionStyle`
- `ListItemDetailTextStyle`
- `ListItemTextStyle`
- `SubtitleStyle`
- `TitleStyle`

Pour appliquer l’un des styles intégrés, utilisez l' `DynamicResource` extension de balisage pour spécifier le style :

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

En C#, les styles intégrés sont sélectionnés dans `Device.Styles` :

```csharp
label.Style = Device.Styles.TitleStyle;
```

![Exemple de styles de périphérique](styles-images/builtinstyles.png)

## <a name="custom-styles"></a>Styles personnalisés

Les styles se composent de setters et de Setters consistent en des propriétés et des valeurs auxquelles les propriétés seront définies.

En C#, un style personnalisé pour une étiquette avec un texte rouge de taille 30 est défini comme suit :

```csharp
var LabelStyle = new Style (typeof(Label)) {
    Setters = {
        new Setter {Property = Label.TextColorProperty, Value = Color.Red},
        new Setter {Property = Label.FontSizeProperty, Value = 30}
    }
};

var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

En XAML :

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

Notez que les ressources (y compris tous les styles) sont définies dans `ContentPage.Resources` , qui est un frère de l’élément plus familier `ContentPage.Content` .

![Exemple de styles personnalisés](styles-images/customstyle.png)

## <a name="applying-styles"></a>Appliquer des styles

Une fois qu’un style a été créé, il peut être appliqué à n’importe quelle vue correspondant à son `TargetType` .

En XAML, les styles personnalisés sont appliqués aux vues en fournissant leur `Style` propriété avec une `StaticResource` extension de balisage référençant le style souhaité :

```xaml
<Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
```

En C#, les styles peuvent être appliqués directement à une vue ou ajoutés et récupérés à partir d’une page `ResourceDictionary` . Pour ajouter directement :

```csharp
var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

Pour ajouter et récupérer à partir de la page `ResourceDictionary` :

```csharp
this.Resources.Add ("LabelStyle", LabelStyle);
label.Style = (Style)Resources["LabelStyle"];
```

Les styles intégrés sont appliqués différemment, car ils doivent répondre aux paramètres d’accessibilité. Pour appliquer des styles intégrés en XAML, l' `DynamicResource` extension de balisage est utilisée :

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

En C#, les styles intégrés sont sélectionnés dans `Device.Styles` :

```csharp
label.Style = Device.Styles.TitleStyle;
```

## <a name="accessibility"></a>Accessibilité

Les styles intégrés permettent de mieux respecter les préférences d’accessibilité. Lorsque vous utilisez l’un des styles intégrés, les tailles de police augmentent automatiquement si un utilisateur définit ses préférences d’accessibilité en conséquence.

Prenons l’exemple suivant de la même page d’affichages avec les styles intégrés avec les paramètres d’accessibilité activés et désactivés :

Désactivé :

![Styles d’appareil avec accessibilité désactivée](styles-images/pre-access.png)

Activé :

![Styles d’appareil avec accessibilité activée](styles-images/post-access.png)

Pour garantir l’accessibilité, assurez-vous que les styles intégrés sont utilisés comme base pour tous les styles liés au texte dans votre application, et que vous utilisez les styles de manière cohérente. Pour plus d’informations sur l’extension et l’utilisation des styles en général, consultez [styles](~/xamarin-forms/user-interface/styles/index.md) .

## <a name="related-links"></a>Liens associés

- [Création d’Mobile Apps avec Xamarin.Forms , chapitre 12](https://developer.xamarin.com/r/xamarin-forms/book/chapter12.pdf)
- [Styles](~/xamarin-forms/user-interface/styles/index.md)
- [Texte (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Style](xref:Xamarin.Forms.Style)