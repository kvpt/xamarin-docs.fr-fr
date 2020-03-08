---
title: Polices dans Xamarin.Forms
description: Cet article explique comment spécifier des informations de police sur les contrôles qui affichent du texte dans les applications Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/20/2020
ms.openlocfilehash: 3798e3612547d36905dd62e6314f158958782874
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78911478"
---
# <a name="fonts-in-xamarinforms"></a>Polices dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)

Cet article décrit comment Xamarin.Forms vous permet de spécifier les attributs de police (y compris les poids et taille) sur les contrôles qui affichent du texte. Les informations de police peuvent être [spécifiées dans le code](#Setting_Font_in_Code) ou [spécifiées en XAML](#Setting_Font_in_Xaml). Il est également possible d’utiliser une [police personnalisée](#Using_a_Custom_Font)et d' [afficher les icônes de police](#display-font-icons).

<a name="Setting_Font_in_Code" />

## <a name="set-the-font-in-code"></a>Définir la police dans le code

Utilisez les trois propriétés liées à la police de tous les contrôles qui affichent du texte :

- **FontFamily** &ndash; le nom de la police `string`.
- **Fontsize** &ndash; la taille de police en tant que `double`.
- **FontAttributes** &ndash; une chaîne spécifiant des informations de style comme *Italic* et **Bold** (à l' C#aide de l’énumération `FontAttributes` dans).

Ce code montre comment créer une étiquette et de spécifier la taille de police et le poids à afficher :

```csharp
var about = new Label
{
    FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
    FontAttributes = FontAttributes.Bold,
    Text = "Medium Bold Font"
};
```

<a name="FontSize" />

### <a name="font-size"></a>Taille de la police

La propriété `FontSize` peut être définie sur une valeur double, par exemple :

```csharp
label.FontSize = 24;
```

La valeur de taille est mesurée en unités indépendantes du périphérique. Pour plus d’informations, consultez [unités de mesure](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement).

Xamarin. Forms définit également les champs de l’énumération [`NamedSize`](xref:Xamarin.Forms.NamedSize) qui représentent des tailles de police spécifiques. Pour plus d’informations sur les tailles de police nommées, consultez [tailles de police nommées](#named-font-sizes).

<a name="FontAttributes" />

### <a name="font-attributes"></a>Attributs de police

Les styles de police tels que **gras** et *italique* peuvent être définis sur la propriété `FontAttributes`. Les valeurs suivantes sont actuellement prises en charge :

- **Aucun**
- **Gras**
- **Italique**

L’énumération `FontAttribute` peut être utilisée comme suit (vous pouvez spécifier un seul attribut ou `OR` ensemble) :

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="set-font-info-per-platform"></a>Définir les informations de police par plateforme

Vous pouvez également utiliser la propriété `Device.RuntimePlatform` pour définir différents noms de polices sur chaque plateforme, comme illustré dans ce code :

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "MarkerFelt-Thin" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/ArimaMadurai-Black.ttf#Arima Madurai",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

[Iosfonts.com](http://iosfonts.com)est une bonne source d’informations sur les polices pour iOS.

<a name="Setting_Font_in_Xaml" />

## <a name="set-the-font-in-xaml"></a>Définir la police en XAML

Les contrôles Xamarin. Forms qui affichent du texte ont tous une propriété `FontSize` qui peut être définie en XAML. Le plus simple pour définir la police dans XAML consiste à utiliser les valeurs d’énumération taille nommée, comme illustré dans cet exemple :

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

Il existe un convertisseur intégré pour la propriété `FontSize` qui permet d’exprimer tous les paramètres de police sous la forme d’une valeur de chaîne en XAML. En outre, la propriété `FontAttributes` peut être utilisée pour spécifier les attributs de police :

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

La propriété [`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#providing-platform-specific-values) peut également être utilisée en XAML pour restituer une police différente sur chaque plateforme. L’exemple ci-dessous utilise des polices différentes sur chaque plateforme :

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

Xamarin. Forms définit les champs de l’énumération [`NamedSize`](xref:Xamarin.Forms.NamedSize) qui représentent des tailles de police spécifiques. Le tableau suivant présente les `NamedSize` membres et leurs tailles par défaut sur iOS, Android et les plateforme Windows universelle (UWP) :

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

Les tailles de police nommées peuvent être définies à l’aide de XAML et de code. En outre, la méthode `Device.GetNamedSize` peut être appelée pour retourner une `double` qui représente la taille de police nommée :

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

> [!NOTE]
> Sur iOS et Android, les tailles de police nommées seront mises à l’échelle automatiquement en fonction des options d’accessibilité du système d’exploitation. Ce comportement peut être désactivé sur iOS avec un spécifique à la plateforme. Pour plus d’informations, consultez [mise à l’échelle de l’accessibilité pour les tailles de police nommées sur iOS](~/xamarin-forms/platform/ios/named-font-size-scaling.md).

<a name="Using_a_Custom_Font" />

## <a name="use-a-custom-font"></a>Utiliser une police personnalisée

À l’aide d’une police autres que les polices intégrées nécessite du codage spécifique à la plateforme. Cette **capture d’écran montre les polices** personnalisées des [polices Open source de Google](https://www.google.com/fonts) rendues à l’aide de Xamarin. Forms.

 [![Police personnalisée sur iOS et Android](fonts-images/custom-sml.png "Exemple de polices personnalisées")](fonts-images/custom.png#lightbox "Exemple de polices personnalisées")

Les étapes requises pour chaque plateforme sont décrites ci-dessous. En incluant des fichiers de police personnalisée avec une application, veillez à vérifier que les licences de la police autorise pour la distribution.

### <a name="ios"></a>iOS

Il est possible d’afficher une police personnalisée en vous assurant d’abord qu’elle est chargée, puis en la faisant référence par son nom à l’aide des méthodes Xamarin. Forms `Font`.
Suivez les instructions de [ce](https://devblogs.microsoft.com/xamarin/custom-fonts-in-ios/)billet de blog :

1. Ajoutez le fichier de police avec l' **action de génération : BundleResource**, et
2. Mettez à jour le fichier **info. plist** (**polices fournies par l’application**ou `UIAppFonts`, la clé), puis
3. Reporter en nom partout où vous définissez une police dans Xamarin.Forms !

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" : null // set only for iOS
}
```

### <a name="android"></a>Android

Xamarin.Forms pour Android peut référencer une police personnalisée qui a été ajoutée au projet en suivant une norme d’affectation de noms spécifique. Ajoutez tout d’abord le fichier de police au dossier **Assets** dans le projet d’application et définissez *action de génération : AndroidAsset*. Utilisez ensuite le chemin d’accès complet et le nom de la *police* séparés par un hachage (#) comme nom de police dans Xamarin. Forms, comme le montre l’extrait de code ci-dessous :

```csharp
new Label
{
  Text = "Hello, Forms!",
  FontFamily = Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : null // set only for Android
}
```

### <a name="windows"></a>Windows

Xamarin.Forms pour les plateformes Windows peut référencer une police personnalisée qui a été ajoutée au projet en suivant une norme d’affectation de noms spécifique. Ajoutez d’abord le fichier de police au dossier **/Assets/Fonts/** dans le projet d’application et définissez l' **action de génération : contenu**. Utilisez ensuite le chemin d’accès complet et le nom de fichier de police, suivis d’un hachage (#) et du nom de la **police**, comme le montre l’extrait de code ci-dessous :

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.UWP ? "Assets/Fonts/Lobster-Regular.ttf#Lobster" : null // set only for UWP apps
}
```

> [!NOTE]
> Notez que le nom de fichier de police et le nom de police peuvent être différentes. Pour découvrir le nom de police sur Windows, cliquez avec le bouton droit sur le fichier. ttf, puis sélectionnez **Aperçu**. Le nom de la police peut alors être déterminé à partir de la fenêtre d’aperçu.

### <a name="xaml"></a>XAML

Vous pouvez également utiliser [`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#interact-with-the-ui-from-background-threads) en XAML pour restituer une police personnalisée :

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="Lobster-Regular" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP" Value="Assets/Fonts/Lobster-Regular.ttf#Lobster" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

## <a name="use-a-custom-font-preview"></a>Utiliser une police personnalisée (version préliminaire)

Des polices personnalisées peuvent être ajoutées à votre projet partagé Xamarin. Forms et utilisées par des projets de plateforme sans aucun travail supplémentaire. Pour ce faire, procédez comme suit :

1. Ajoutez la police à votre projet partagé Xamarin. Forms en tant que ressource incorporée (**action de génération : EmbeddedResource**).
1. Enregistrez le fichier de polices avec l’assembly, dans un fichier tel que **AssemblyInfo.cs**, à l’aide de l’attribut `ExportFont`.

L’exemple suivant illustre la police régulière de la homard inscrite avec l’assembly :

```csharp
using Xamarin.Forms;

[assembly: ExportFont("Lobster-Regular.ttf")]
```

> [!NOTE]
> La police peut résider dans n’importe quel dossier du projet partagé, sans qu’il soit nécessaire de spécifier le nom du dossier lors de l’inscription de la police avec l’assembly.

La police peut ensuite être consommée sur chaque plateforme en référençant son nom, sans l’extension de fichier :

```xaml
<Label Text="Hello Xamarin.Forms"
       FontFamily="Lobster-Regular" />
```

Le code C# équivalent est :

```csharp
Label label = new Label
{
    Text = "Hello Xamarin.Forms!",
    FontFamily = "Lobster-Regular"
};
```

Les captures d’écran suivantes illustrent la police personnalisée :

[![Police personnalisée sur iOS et Android](fonts-images/custom-sml.png "Exemple de polices personnalisées")](fonts-images/custom.png#lightbox "Exemple de polices personnalisées")

## <a name="display-font-icons"></a>Afficher les icônes de police

Les icônes de police peuvent être affichées par les applications Xamarin. Forms en spécifiant les données d’icône de police dans un objet `FontImageSource`. Cette classe, qui dérive de la classe [`ImageSource`](xref:Xamarin.Forms.ImageSource) , a les propriétés suivantes :

- `Glyph` : valeur de caractère Unicode de l’icône de police, spécifiée comme `string`.
- `Size` : valeur `double` qui indique la taille, en unités indépendantes du périphérique, de l’icône de police rendue. La valeur par défaut est 30. En outre, cette propriété peut être définie sur une taille de police nommée.
- `FontFamily` : `string` représentant la famille de polices à laquelle appartient l’icône de police.
- `Color` : valeur facultative [`Color`](xref:Xamarin.Forms.Color) à utiliser lors de l’affichage de l’icône de police.

Ces données sont utilisées pour créer un PNG, qui peut être affiché par n’importe quelle vue pouvant afficher une `ImageSource`. Cette approche permet d’afficher des icônes de police, telles que des emoji, par le biais de plusieurs vues, au lieu de limiter l’affichage des icônes de police à une seule vue de type présentation de texte, telle qu’une [`Label`](xref:Xamarin.Forms.Label).

> [!IMPORTANT]
> Les icônes de police ne peuvent être spécifiées que par leur représentation de caractère Unicode.

L’exemple de code XAML suivant a une icône de police unique qui est affichée par un affichage de [`Image`](xref:Xamarin.Forms.Image) :

```xaml
<Image BackgroundColor="#D1D1D1">
    <Image.Source>
        <FontImageSource Glyph="&#xf30c;"
                         FontFamily="{OnPlatform iOS=Ionicons, Android=ionicons.ttf#}"
                         Size="44" />
    </Image.Source>
</Image>
```

Ce code affiche une icône XBox, à partir de la famille de polices Ionicons, dans une vue [`Image`](xref:Xamarin.Forms.Image) . Notez que si le caractère Unicode de cette icône est `\uf30c`, il doit être placé dans une séquence d’échappement en XAML et devient donc `&#xf30c;`. Le code C# équivalent est :

```csharp
Image image = new Image { BackgroundColor = Color.FromHex("#D1D1D1") };
image.Source = new FontImageSource
{
    Glyph = "\uf30c",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Ionicons" : "ionicons.ttf#",
    Size = 44
};
```

Les captures d’écran suivantes de l’exemple de [dispositions pouvant être liées](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts) affichent plusieurs icônes de police affichées par une disposition pouvant être liée :

![Capture d’écran des icônes de police affichées, sur iOS et Android](fonts-images/font-image-source.png "Icônes de police affichées dans une vue d’image")

## <a name="related-links"></a>Liens connexes

- [FontsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)
- [Texte (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Dispositions pouvant être liées (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Dispositions liables](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
