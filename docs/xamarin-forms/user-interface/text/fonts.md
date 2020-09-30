---
title: Polices dans Xamarin.Forms
description: Cet article explique comment spécifier des informations de police sur les contrôles qui affichent du texte dans les Xamarin.Forms applications.
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fb32d3248b1dbbe633a99afd8de14b4fb4f0ba09
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562195"
---
# <a name="fonts-in-no-locxamarinforms"></a>Polices dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)

Cet article décrit comment Xamarin.Forms vous permet de spécifier les attributs de police (y compris le poids et la taille) des contrôles qui affichent du texte. Les informations de police peuvent être [spécifiées dans le code](#set-the-font-in-code) ou [spécifiées en XAML](#set-the-font-in-xaml). Il est également possible d’utiliser une [police personnalisée](#use-a-custom-font)et d' [afficher les icônes de police](#display-font-icons).

## <a name="set-the-font-in-code"></a>Définir la police dans le code

Utilisez les trois propriétés de police de tous les contrôles qui affichent du texte :

- **FontFamily** &ndash; nom de la `string` police.
- **Police** &ndash; taille de police sous la forme d’un `double` .
- **FontAttributes** &ndash; Chaîne spécifiant des informations de style comme *Italic* et **Bold** (à l’aide `FontAttributes` de l’énumération en C#).

Ce code montre comment créer une étiquette et spécifier la taille de police et le poids à afficher :

```csharp
var about = new Label
{
    FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
    FontAttributes = FontAttributes.Bold,
    Text = "Medium Bold Font"
};
```

### <a name="font-size"></a>Taille de police

La `FontSize` propriété peut être définie sur une valeur double, par exemple :

```csharp
label.FontSize = 24;
```

La valeur de taille est mesurée en unités indépendantes du périphérique. Pour plus d’informations, consultez [unités de mesure](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement).

Xamarin.Forms définit également les champs de l' [`NamedSize`](xref:Xamarin.Forms.NamedSize) énumération qui représentent des tailles de police spécifiques. Pour plus d’informations sur les tailles de police nommées, consultez [tailles de police nommées](#named-font-sizes).

### <a name="font-attributes"></a>Attributs de police

Les styles de police tels que **gras** et *italique* peuvent être définis sur la `FontAttributes` propriété. Les valeurs suivantes sont actuellement prises en charge :

- **Aucun**
- **Gras**
- **Italique**

L' `FontAttribute` énumération peut être utilisée comme suit (vous pouvez spécifier un seul attribut ou `OR` les deux) :

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="set-font-info-per-platform"></a>Définir les informations de police par plateforme

La `Device.RuntimePlatform` propriété peut également être utilisée pour définir différents noms de polices sur chaque plateforme, comme illustré dans ce code :

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "MarkerFelt-Thin" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/ArimaMadurai-Black.ttf#Arima Madurai",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

[Iosfonts.com](http://iosfonts.com)est une bonne source d’informations sur les polices pour iOS.

## <a name="set-the-font-in-xaml"></a>Définir la police en XAML

Xamarin.Forms les contrôles qui affichent du texte ont tous une `FontSize` propriété qui peut être définie en XAML. La façon la plus simple de définir la police en XAML consiste à utiliser les valeurs d’énumération de taille nommée, comme indiqué dans cet exemple :

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

Il existe un convertisseur intégré pour la `FontSize` propriété qui permet d’exprimer tous les paramètres de police sous la forme d’une valeur de chaîne en XAML. En outre, la `FontAttributes` propriété peut être utilisée pour spécifier les attributs de police :

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

La [`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#provide-platform-specific-values) propriété peut également être utilisée en XAML pour restituer une police différente sur chaque plateforme. L’exemple ci-dessous utilise des polices différentes sur chaque plateforme :

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="MarkerFelt-Thin" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP" Value="Assets/Fonts/ArimaMadurai-Black.ttf#Arima Madurai" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

## <a name="named-font-sizes"></a>Tailles de police nommées

Xamarin.Forms définit des champs dans l' [`NamedSize`](xref:Xamarin.Forms.NamedSize) énumération qui représentent des tailles de police spécifiques. Le tableau suivant présente les `NamedSize` membres et leurs tailles par défaut sur iOS, Android et le plateforme Windows universelle (UWP) :

| Membre | iOS | Android | UWP |
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

Les tailles de police nommées peuvent être définies à l’aide de XAML et de code. En outre, la `Device.GetNamedSize` méthode peut être appelée pour retourner un `double` qui représente la taille de police nommée :

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

> [!NOTE]
> Sur iOS et Android, les tailles de police nommées seront mises à l’échelle automatiquement en fonction des options d’accessibilité du système d’exploitation. Ce comportement peut être désactivé sur iOS avec un spécifique à la plateforme. Pour plus d’informations, consultez [mise à l’échelle de l’accessibilité pour les tailles de police nommées sur iOS](~/xamarin-forms/platform/ios/named-font-size-scaling.md).

## <a name="use-a-custom-font"></a>Utiliser une police personnalisée

Des polices personnalisées peuvent être ajoutées à votre Xamarin.Forms projet partagé et utilisées par des projets de plateforme sans aucun travail supplémentaire. Pour ce faire, procédez comme suit :

1. Ajoutez la police à votre Xamarin.Forms projet partagé en tant que ressource incorporée (**action de génération : EmbeddedResource**).
1. Enregistrez le fichier de polices avec l’assembly, dans un fichier tel que **AssemblyInfo.cs**, à l’aide de l' `ExportFont` attribut. Un alias facultatif peut également être spécifié.

> [!IMPORTANT]
> Les polices incorporées requièrent l’utilisation de Xamarin.Forms 4.5.0.530 ou une version ultérieure.

L’exemple suivant illustre la police régulière de la homard inscrite avec l’assembly, ainsi qu’un alias :

```csharp
using Xamarin.Forms;

[assembly: ExportFont("Lobster-Regular.ttf", Alias = "Lobster")]
```

> [!NOTE]
> La police peut résider dans n’importe quel dossier du projet partagé, sans qu’il soit nécessaire de spécifier le nom du dossier lors de l’inscription de la police avec l’assembly.

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
> Sur Windows, le nom du fichier de police et le nom de la police peuvent être différents. Pour découvrir le nom de police sur Windows, cliquez avec le bouton droit sur le fichier. ttf, puis sélectionnez **Aperçu**. Le nom de la police peut ensuite être déterminé à partir de la fenêtre d’aperçu.

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
- [Dispositions liables](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)