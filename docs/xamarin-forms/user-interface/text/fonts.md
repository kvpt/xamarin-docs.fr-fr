---
title: Polices dans Xamarin.Forms
description: Cet article explique comment spécifier des informations de police sur les contrôles qui affichent du texte dans les applications Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/28/2019
ms.openlocfilehash: c18c4e63831a03cbe28accfe10f4c7da31130803
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69529310"
---
# <a name="fonts-in-xamarinforms"></a>Polices dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)

Cet article décrit comment Xamarin.Forms vous permet de spécifier les attributs de police (y compris les poids et taille) sur les contrôles qui affichent du texte. Informations de police peuvent être [spécifiée dans le code](#Setting_Font_in_Code) ou [spécifié dans XAML](#Setting_Font_in_Xaml). Il est également possible d’utiliser une [police personnalisée](#Using_a_Custom_Font)et d' [afficher les icônes de police](#display-font-icons).

<a name="Setting_Font_in_Code" />

## <a name="set-the-font-in-code"></a>Définir la police dans le code

Utilisez les trois propriétés liées à la police de tous les contrôles qui affichent du texte :

- **FontFamily** &ndash; le `string` nom de la police.
- **FontSize** &ndash; la taille de police comme un `double`.
- **Attributs de la police** &ndash; une chaîne qui spécifie les informations de style comme *italique* et **gras** (à l’aide de la `FontAttributes` énumération en C#).

Ce code montre comment créer une étiquette et de spécifier la taille de police et le poids à afficher :

```csharp
var about = new Label {
    FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
    FontAttributes = FontAttributes.Bold,
    Text = "Medium Bold Font"
};
```

<a name="FontSize" />

### <a name="font-size"></a>Taille de police

Le `FontSize` propriété peut être définie sur une valeur double, par exemple :

```csharp
label.FontSize = 24;
```

Xamarin. Forms définit également les champs [`NamedSize`](xref:Xamarin.Forms.NamedSize) de l’énumération qui représentent des tailles de police spécifiques. Pour plus d’informations sur les tailles de police nommées, consultez [tailles de police nommées](#named-font-sizes).

<a name="FontAttributes" />

### <a name="font-attributes"></a>Attributs de police

Styles de police comme **gras** et *italique* peuvent être définies sur le `FontAttributes` propriété. Les valeurs suivantes sont actuellement prises en charge :

- **Aucun**
- **Gras**
- **Italique**

Le `FontAttribute` énumération peut être utilisée comme suit (vous pouvez spécifier un seul attribut ou `OR` utilisez-les) :

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="set-font-info-per-platform"></a>Définir les informations de police par plateforme

Vous pouvez également le `Device.RuntimePlatform` propriété peut être utilisée pour définir des noms de polices différentes sur chaque plateforme, comme illustré dans ce code :

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/Lobster-Regular.ttf#Lobster",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

Est une bonne source d’informations de police pour iOS [iosfonts.com](http://iosfonts.com).

<a name="Setting_Font_in_Xaml" />

## <a name="set-the-font-in-xaml"></a>Définir la police en XAML

Xamarin.Forms contrôle ce texte d’affichage ont toutes un `FontSize` propriété qui peut être définie dans XAML. Le plus simple pour définir la police dans XAML consiste à utiliser les valeurs d’énumération taille nommée, comme illustré dans cet exemple :

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

Il existe un convertisseur intégré pour le `FontSize` propriété qui permet à tous les paramètres de police à être exprimé comme une valeur de chaîne dans XAML. En outre, la `FontAttributes` propriété peut être utilisée pour spécifier les attributs de police:

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

[`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#providing-platform-specific-values) peut également être utilisé dans XAML pour restituer une police différente sur chaque plateforme. L’exemple ci-dessous utilise un type de police personnalisé sur iOS (MarkerFelt-Thin) et spécifie uniquement la taille/les attributs sur les autres plateformes:

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="MarkerFelt-Thin" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP" Value="Assets/Fonts/Lobster-Regular.ttf#Lobster" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

Lorsque vous spécifiez un type de police personnalisée, il est toujours judicieux d’utiliser `OnPlatform`, comme il est difficile de trouver une police qui n’est disponible sur toutes les plateformes.

## <a name="named-font-sizes"></a>Tailles de police nommées

Xamarin. Forms définit les champs [`NamedSize`](xref:Xamarin.Forms.NamedSize) de l’énumération qui représentent des tailles de police spécifiques. Le tableau suivant présente les `NamedSize` membres et leurs tailles par défaut sur iOS, Android et le plateforme Windows universelle (UWP):

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

Les tailles de police nommées peuvent être définies à l’aide de XAML et de code. En outre, la `Device.GetNamedSize` méthode peut être appelée pour retourner un `double` qui représente la taille de police nommée:

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

> [!NOTE]
> Sur iOS et Android, les tailles de police nommées seront mises à l’échelle automatiquement en fonction des options d’accessibilité du système d’exploitation. Ce comportement peut être désactivé sur iOS avec un spécifique à la plateforme. Pour plus d’informations, consultez [mise à l’échelle de l’accessibilité pour les tailles de police nommées sur iOS](~/xamarin-forms/platform/ios/named-font-size-scaling.md).

<a name="Using_a_Custom_Font" />

## <a name="use-a-custom-font"></a>Utiliser une police personnalisée

À l’aide d’une police autres que les polices intégrées nécessite du codage spécifique à la plateforme. Cette capture d’écran montre la police personnalisée **Lobster** de [polices d’open source de Google](https://www.google.com/fonts) restitué à l’aide de Xamarin.Forms.

 [![Une police personnalisée sur iOS et Android](fonts-images/custom-sml.png "personnalisé polices exemple")](fonts-images/custom.png#lightbox "exemple des polices personnalisées")

Les étapes requises pour chaque plateforme sont décrites ci-dessous. En incluant des fichiers de police personnalisée avec une application, veillez à vérifier que les licences de la police autorise pour la distribution.

### <a name="ios"></a>iOS

Il est possible d’afficher une police personnalisée, tout d’abord s’assurer qu’elle est chargée, puis référence par nom à l’aide de Xamarin.Forms `Font` méthodes.
Suivez les instructions de [ce billet de blog](https://blog.xamarin.com/custom-fonts-in-ios/):

1. Ajoutez le fichier de police **avec l’action de génération: BundleResource**et
2. Mise à jour le **Info.plist** fichier (**polices fournies par l’application**, ou `UIAppFonts`, key), puis
3. Reporter en nom partout où vous définissez une police dans Xamarin.Forms !

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" : null // set only for iOS
}
```

### <a name="android"></a>Android

Xamarin.Forms pour Android peut référencer une police personnalisée qui a été ajoutée au projet en suivant une norme d’affectation de noms spécifique. Ajoutez d’abord le fichier de police au dossier **composants** dans le projet d’application *et définissez action de génération: AndroidAsset*. Puis utilisez le chemin d’accès complet et *nom de la police* séparés par un signe dièse (#) comme nom de police dans Xamarin.Forms, comme le montre l’extrait de code ci-dessous :

```csharp
new Label
{
  Text = "Hello, Forms!",
  FontFamily = Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : null // set only for Android
}
```

### <a name="windows"></a>Windows

Xamarin.Forms pour les plateformes Windows peut référencer une police personnalisée qui a été ajoutée au projet en suivant une norme d’affectation de noms spécifique. Tout d’abord ajouter le fichier de police pour le **/Assets/polices/** dossier dans le projet d’application et le jeu le **Build Action : contenu**. Puis utilisez le nom de chemin d’accès et de la police complète, suivi d’un signe dièse (#) et le **nom de la police**, comme le montre l’extrait de code ci-dessous :

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.UWP ? "Assets/Fonts/Lobster-Regular.ttf#Lobster" : null // set only for UWP apps
}
```

> [!NOTE]
> Notez que le nom de fichier de police et le nom de police peuvent être différentes. Pour découvrir le nom de la police sur Windows, cliquez sur le fichier .ttf et sélectionnez **aperçu**. Le nom de la police peut alors être déterminé à partir de la fenêtre d’aperçu.

Le code commun pour l’application est maintenant complet. Le code du numéroteur téléphonique spécifique à la plateforme sera désormais implémenté comme [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

### <a name="xaml"></a>XAML

Vous pouvez également utiliser [ `Device.RuntimePlatform` ](~/xamarin-forms/platform/device.md#interact-with-the-ui-from-background-threads) dans XAML pour restituer une police personnalisée :

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

## <a name="display-font-icons"></a>Afficher les icônes de police

Les icônes de police peuvent être affichées par les applications Xamarin. Forms en spécifiant les `FontImageSource` données d’icône de police dans un objet. Cette classe, qui dérive de la [`ImageSource`](xref:Xamarin.Forms.ImageSource) classe, a les propriétés suivantes:

- `Glyph`: valeur de caractère Unicode de l’icône de police, spécifiée comme `string`.
- `Size``double` : valeur qui indique la taille, en unités indépendantes du périphérique, de l’icône de police rendue. La valeur par défaut est 30.
- `FontFamily``string` : représentant la famille de polices à laquelle l’icône de police appartient.
- `Color`: valeur facultative [`Color`](xref:Xamarin.Forms.Color) à utiliser lors de l’affichage de l’icône de police.

Ces données sont utilisées pour créer un PNG, qui peut être affiché par n’importe quelle vue pouvant afficher `ImageSource`un. Cette approche permet d’afficher des icônes de police, telles que des emoji, par le biais de plusieurs vues, au lieu de limiter l’affichage des icônes de police à un seul [`Label`](xref:Xamarin.Forms.Label)affichage de texte, tel qu’un.

> [!IMPORTANT]
> Les icônes de police ne peuvent être spécifiées que par leur représentation de caractère Unicode.

L’exemple XAML suivant a une icône de police unique qui est affichée [`Image`](xref:Xamarin.Forms.Image) par une vue:

```xaml
<Image BackgroundColor="#D1D1D1">
    <Image.Source>
        <FontImageSource Glyph="&#xf30c;"
                         FontFamily="{OnPlatform iOS=Ionicons, Android=ionicons.ttf#}"
                         Size="44" />
    </Image.Source>
</Image>
```

Ce code affiche une icône Xbox, à partir de la famille de polices Ionicons [`Image`](xref:Xamarin.Forms.Image) , dans une vue. Notez que, si le caractère Unicode de cette icône `\uf30c`est, il doit être placé dans une séquence d’échappement en `&#xf30c;`XAML et devient donc. Le code C# équivalent est :

```csharp
Image image = new Image { BackgroundColor = Color.FromHex("#D1D1D1") };
image.Source = new FontImageSource
{
    Glyph = "\uf30c",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Ionicons" : "ionicons.ttf#",
    Size = 44
};
```

Les captures d’écran suivantes de l’exemple de [dispositions pouvant être liées](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts) affichent plusieurs icônes de police affichées par une disposition pouvant être liée:

![Capture d’écran des icônes de police affichées, sur iOS et Android](fonts-images/font-image-source.png "Icônes de police affichées dans une vue d’image")

## <a name="related-links"></a>Liens connexes

- [FontsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)
- [Texte (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Dispositions pouvant être liées (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Dispositions liables](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
