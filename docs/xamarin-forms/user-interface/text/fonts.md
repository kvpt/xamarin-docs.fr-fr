---
title: Polices dans Xamarin.Forms
description: Cet article explique comment spécifier des informations de police sur les contrôles qui affichent du texte dans les Xamarin.Forms applications.
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/04/2020
ms.custom: contperfq2
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 271c6c5e510a892919b5d87c4dbc38ad8e9d657d
ms.sourcegitcommit: 342cfbd2502ad92cadada4fa9aec669b99d7830a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2020
ms.locfileid: "96604571"
---
# <a name="fonts-in-no-locxamarinforms"></a>Polices dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/workingwithfonts)

Par défaut, Xamarin.Forms utilise une police système définie par chaque plateforme. Toutefois, les contrôles qui affichent du texte définissent des propriétés que vous pouvez utiliser pour modifier cette police :

- `FontAttributes`, de type `FontAttributes` , qui est une énumération avec trois membres : `None` , `Bold` et `Italic` . La valeur par défaut de cette propriété est `None`.
- `FontSize`, de type `double`.
- `FontFamily`, de type `string`.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

## <a name="set-font-attributes"></a>Définir les attributs de police

Les contrôles qui affichent du texte peuvent définir la `FontAttributes` propriété pour spécifier des attributs de police :

```xaml
<Label Text="Italics"
       FontAttributes="Italic" />
<Label Text="Bold and italics"
       FontAttributes="Bold, Italic" />
```

Le code C# équivalent est :

```csharp
Label label1 = new Label
{
    Text = "Italics",
    FontAttributes = FontAttributes.Italic
};

Label label2 = new Label
{
    Text = "Bold and italics",
    FontAttributes = FontAttributes.Bold | FontAttributes.Italic
};    
```

## <a name="set-the-font-size"></a>Définir la taille de la police

Les contrôles qui affichent du texte peuvent définir la `FontSize` propriété pour spécifier la taille de police. La `FontSize` propriété peut être définie sur une `double` valeur directement ou par une [`NamedSize`](xref:Xamarin.Forms.NamedSize) valeur d’énumération :

```xaml
<Label Text="Font size 24"
       FontSize="24" />
<Label Text="Large font size"
       FontSize="Large" />
```

Le code C# équivalent est :

```csharp
Label label1 = new Label
{
    Text = "Font size 24",
    FontSize = 24
};

Label label2 = new Label
{
    Text = "Large font size",
    FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label))
};
```

La `Device.GetNamedSize` méthode a également une substitution qui spécifie le deuxième argument comme [`Element`](xref:Xamarin.Forms.Element) :

```csharp
Label myLabel = new Label
{
    Text = "Large font size",
};
myLabel.FontSize = Device.GetNamedSize(NamedSize.Large, myLabel);
```

> [!NOTE]
> La `FontSize` valeur, lorsqu’elle est spécifiée en tant que `double` , est mesurée en unités indépendantes du périphérique. Pour plus d’informations, consultez [unités de mesure](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement).

Pour plus d’informations sur les tailles de police nommées, consultez [comprendre les tailles de police nommées](#understand-named-font-sizes).

## <a name="set-the-font-family"></a>Définir la famille de polices

Les contrôles qui affichent du texte peuvent affecter `FontFamily` à la propriété un nom de famille de polices, tel que « Times Roman ». Toutefois, cela ne fonctionnera que si cette famille de polices est prise en charge sur la plate-forme particulière.

Il existe plusieurs techniques qui peuvent être utilisées pour tenter de dériver les polices disponibles sur une plateforme. Toutefois, la présence d’un fichier de police TTF (format de type true) n’implique pas nécessairement une famille de polices, et les TTFs sont souvent inclus et ne sont pas destinés à être utilisés dans les applications. En outre, les polices installées sur une plateforme peuvent changer avec la version de la plateforme. Par conséquent, l’approche la plus fiable pour spécifier une famille de polices consiste à utiliser une police personnalisée.

Des polices personnalisées peuvent être ajoutées à votre Xamarin.Forms projet partagé et utilisées par des projets de plateforme sans aucun travail supplémentaire. Pour ce faire, procédez comme suit :

1. Ajoutez la police à votre Xamarin.Forms projet partagé en tant que ressource incorporée (**action de génération : EmbeddedResource**).
1. Enregistrez le fichier de polices avec l’assembly, dans un fichier tel que **AssemblyInfo.cs**, à l’aide de l' `ExportFont` attribut. Un alias facultatif peut également être spécifié.

L’exemple suivant montre le Lobster-Regular police en cours d’inscription avec l’assembly, ainsi qu’un alias :

```csharp
using Xamarin.Forms;

[assembly: ExportFont("Lobster-Regular.ttf", Alias = "Lobster")]
```

> [!NOTE]
> La police peut résider dans n’importe quel dossier du projet partagé, sans qu’il soit nécessaire de spécifier le nom du dossier lors de l’inscription de la police avec l’assembly.
>
> Sur Windows, le nom du fichier de police et le nom de la police peuvent être différents. Pour découvrir le nom de police sur Windows, cliquez avec le bouton droit sur le fichier. ttf, puis sélectionnez **Aperçu**. Le nom de la police peut ensuite être déterminé à partir de la fenêtre d’aperçu.

La police peut ensuite être consommée sur chaque plateforme en référençant son nom, sans l’extension de fichier :

```xaml
<!-- Use font name -->
<Label Text="Hello Xamarin.Forms"
       FontFamily="Lobster-Regular" />
```

Il peut également être consommé sur chaque plateforme en référençant son alias :

```xaml
<!-- Use font alias -->
<Label Text="Hello Xamarin.Forms"
       FontFamily="Lobster" />
```

Le code C# équivalent est :

```csharp
// Use font name
Label label1 = new Label
{
    Text = "Hello Xamarin.Forms!",
    FontFamily = "Lobster-Regular"
};

// Use font alias
Label label2 = new Label
{
    Text = "Hello Xamarin.Forms!",
    FontFamily = "Lobster"
};
```

Les captures d’écran suivantes illustrent la police personnalisée :

[![Police personnalisée sur iOS et Android](fonts-images/custom-sml.png "Exemple de polices personnalisées")](fonts-images/custom.png#lightbox "Exemple de polices personnalisées")

> [!IMPORTANT]
> Pour les versions release sur Windows, assurez-vous que l’assembly contenant la police personnalisée est passé comme argument dans l' `Forms.Init` appel de méthode. Pour plus d’informations, consultez la page [Dépannage](~/xamarin-forms/platform/windows/installation/index.md#troubleshooting).

## <a name="set-font-properties-per-platform"></a>Définir les propriétés de police par plateforme

Les [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) [`On`](xref:Xamarin.Forms.On) classes et peuvent être utilisées en XAML pour définir les propriétés de police par plateforme. L’exemple ci-dessous définit des familles de polices et des tailles différentes sur chaque plateforme :

```xaml
<Label Text="Different font properties on different platforms"
       FontSize="{OnPlatform iOS=20, Android=Medium, UWP=24}">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
            <On Platform="iOS" Value="MarkerFelt-Thin" />
            <On Platform="Android" Value="Lobster-Regular" />
            <On Platform="UWP" Value="ArimaMadurai-Black" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

La [`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#provide-platform-specific-values) propriété peut être utilisée dans le code pour définir les propriétés de police par plateforme

```csharp
Label label = new Label
{
    Text = "Different font properties on different platforms"
};

label.FontSize = Device.RuntimePlatform == Device.iOS ? 20 :
    Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : 24;
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "MarkerFelt-Thin" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular" : "ArimaMadurai-Black";
```

Pour plus d’informations sur la façon de fournir des valeurs spécifiques à la plateforme, consultez [fournir des valeurs spécifiques à la plateforme](~/xamarin-forms/platform/device.md#provide-platform-specific-values). Pour plus d’informations sur l' `OnPlatform` extension de balisage, consultez [OnPlatform Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

## <a name="understand-named-font-sizes"></a>Comprendre les tailles de police nommées

Xamarin.Forms définit des champs dans l' [`NamedSize`](xref:Xamarin.Forms.NamedSize) énumération qui représentent des tailles de police spécifiques. Le tableau suivant présente les `NamedSize` membres et leurs tailles par défaut sur iOS, Android et le plateforme Windows universelle (UWP) :

| Membre | iOS | Téléphone | UWP |
| --- | --- | --- | --- |
| `Default` | 16 | 14 | 14 |
| `Micro` | 11 | 10 | 15,667 |
| `Small` | 13 | 14 | 18,667 |
| `Medium` | 16 | 17 | 22,667 |
| `Large` | 20 | 22 | 32 |
| `Body` | 17 | 16 | 14 |
| `Header` | 17 | 96 | 46 |
| `Title` | 28 | 24 | 24 |
| `Subtitle` | 22 | 16 | 20 |
| `Caption` | 12 | 12 | 12 |

Les valeurs de taille sont mesurées en unités indépendantes du périphérique. Pour plus d’informations, consultez [unités de mesure](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement).

> [!NOTE]
> Sur iOS et Android, les tailles de police nommées seront mises à l’échelle automatiquement en fonction des options d’accessibilité du système d’exploitation. Ce comportement peut être désactivé sur iOS avec un spécifique à la plateforme. Pour plus d’informations, consultez [mise à l’échelle de l’accessibilité pour les tailles de police nommées sur iOS](~/xamarin-forms/platform/ios/named-font-size-scaling.md).

## <a name="display-font-icons"></a>Afficher les icônes de police

Les icônes de police peuvent être affichées par Xamarin.Forms les applications en spécifiant les données d’icône de police dans un `FontImageSource` objet. Cette classe, qui dérive de la [`ImageSource`](xref:Xamarin.Forms.ImageSource) classe, a les propriétés suivantes :

- `Glyph` : valeur de caractère Unicode de l’icône de police, spécifiée comme `string` .
- `Size` : `double` valeur qui indique la taille, en unités indépendantes du périphérique, de l’icône de police rendue. La valeur par défaut est 30. En outre, cette propriété peut être définie sur une taille de police nommée.
- `FontFamily` : `string` représentant la famille de polices à laquelle l’icône de police appartient.
- `Color` : valeur facultative [`Color`](xref:Xamarin.Forms.Color) à utiliser lors de l’affichage de l’icône de police.

Ces données sont utilisées pour créer un PNG, qui peut être affiché par n’importe quelle vue pouvant afficher un `ImageSource` . Cette approche permet d’afficher des icônes de police, telles que des emoji, par le biais de plusieurs vues, au lieu de limiter l’affichage des icônes de police à un seul affichage de texte, tel qu’un [`Label`](xref:Xamarin.Forms.Label) .

> [!IMPORTANT]
> Les icônes de police ne peuvent être spécifiées que par leur représentation de caractère Unicode.

L’exemple XAML suivant a une icône de police unique qui est affichée par une [`Image`](xref:Xamarin.Forms.Image) vue :

```xaml
<Image BackgroundColor="#D1D1D1">
    <Image.Source>
        <FontImageSource Glyph="&#xf30c;"
                         FontFamily="{OnPlatform iOS=Ionicons, Android=ionicons.ttf#}"
                         Size="44" />
    </Image.Source>
</Image>
```

Ce code affiche une icône XBox, à partir de la famille de polices Ionicons, dans une [`Image`](xref:Xamarin.Forms.Image) vue. Notez que, si le caractère Unicode de cette icône est `\uf30c` , il doit être placé dans une séquence d’échappement en XAML et devient donc `&#xf30c;` . Le code C# équivalent est :

```csharp
Image image = new Image { BackgroundColor = Color.FromHex("#D1D1D1") };
image.Source = new FontImageSource
{
    Glyph = "\uf30c",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Ionicons" : "ionicons.ttf#",
    Size = 44
};
```

Les captures d’écran suivantes de l’exemple de [dispositions pouvant être liées](/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts) affichent plusieurs icônes de police affichées par une disposition pouvant être liée :

![Capture d’écran des icônes de police affichées, sur iOS et Android](fonts-images/font-image-source.png "Icônes de police affichées dans une vue d’image")

## <a name="related-links"></a>Liens connexes

- [FontsSample](/samples/xamarin/xamarin-forms-samples/workingwithfonts)
- [Texte (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Dispositions pouvant être liées (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Fournir des valeurs spécifiques à la plateforme](~/xamarin-forms/platform/device.md#provide-platform-specific-values)
- [Extension de balisage OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)
- [Dispositions liables](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
