---
title: Fonts dans Xamarin.Forms
description: Cet article explique comment spécifier les informations de police sur les contrôles qui affichent le texte dans les applications Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2020
ms.openlocfilehash: 4f14d66e5321a0fb19078be4d97ae4df69f688c9
ms.sourcegitcommit: 6f3281a32017cfcebadde8a2d6e10651a277828f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80587418"
---
# <a name="fonts-in-xamarinforms"></a>Fonts dans Xamarin.Forms

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)

Cet article décrit comment Xamarin.Forms vous permet de spécifier les attributs de police (y compris le poids et la taille) sur les contrôles qui affichent le texte. Les informations de police peuvent être [spécifiées dans le code](#Setting_Font_in_Code) ou [spécifiées dans XAML](#Setting_Font_in_Xaml). Il est également possible d’utiliser une [police personnalisée](#Using_a_Custom_Font), et [afficher des icônes de police](#display-font-icons).

<a name="Setting_Font_in_Code" />

## <a name="set-the-font-in-code"></a>Définir la police dans le code

Utilisez les trois propriétés liées à la police de tous les contrôles qui affichent le texte :

- **FontFamily** &ndash; `string` le nom de police.
- **FontSize** &ndash; la taille `double`de la police comme un .
- **FontAttribute** &ndash; une chaîne spécifiant des **Bold** informations de `FontAttributes` style comme *Italic* et Bold (en utilisant le recensement dans C).

Ce code montre comment créer une étiquette et spécifier la taille et le poids de la police pour afficher :

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

La `FontSize` propriété peut être réglée à une double valeur, par exemple :

```csharp
label.FontSize = 24;
```

La valeur de taille est mesurée en unités indépendantes de l’appareil. Pour plus d’informations, voir [Unités de mesure](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement).

Xamarin.Forms définit également les [`NamedSize`](xref:Xamarin.Forms.NamedSize) champs dans le recensement qui représentent des tailles spécifiques de police. Pour plus d’informations sur les tailles de police nommées, voir [les tailles de police nommées](#named-font-sizes).

<a name="FontAttributes" />

### <a name="font-attributes"></a>Attributs de police

Des styles de police tels que **gras** `FontAttributes` et *italiques* peuvent être placés sur la propriété. Les valeurs suivantes sont actuellement prises en charge :

- **Aucun**
- **Gras**
- **Italique**

L’énumération `FontAttribute` peut être utilisée comme suit (vous `OR` pouvez spécifier un attribut unique ou eux ensemble):

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="set-font-info-per-platform"></a>Définissez des informations de police par plate-forme

Alternativement, `Device.RuntimePlatform` la propriété peut être utilisée pour définir différents noms de police sur chaque plate-forme, comme démontré dans ce code:

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "MarkerFelt-Thin" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/ArimaMadurai-Black.ttf#Arima Madurai",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

Une bonne source d’informations de police pour iOS est [iosfonts.com](http://iosfonts.com).

<a name="Setting_Font_in_Xaml" />

## <a name="set-the-font-in-xaml"></a>Réglez la police dans XAML

Xamarin.Forms contrôle que le `FontSize` texte d’affichage ont tous une propriété qui peut être définie dans XAML. La façon la plus simple de définir la police dans XAML est d’utiliser les valeurs d’énumération de taille nommées, comme le montre cet exemple :

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

Il y a un convertisseur `FontSize` intégré pour la propriété qui permet à tous les paramètres de police d’être exprimés comme une valeur de chaîne dans XAML. En outre, `FontAttributes` la propriété peut être utilisée pour spécifier les attributs de police :

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

La [`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#providing-platform-specific-values) propriété peut également être utilisée dans XAML pour rendre une police différente sur chaque plate-forme. L’exemple ci-dessous utilise différentes polices sur chaque plate-forme :

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

Xamarin.Forms définit les [`NamedSize`](xref:Xamarin.Forms.NamedSize) champs dans le recensement qui représentent des tailles spécifiques de police. Le tableau suivant `NamedSize` montre les membres, et leurs tailles par défaut sur iOS, Android, et la plate-forme Windows universelle (UWP):

| Membre | iOS | Android | UWP |
| --- | --- | --- | --- |
| `Default` | 16 | 14 | 14 |
| `Micro` | 11 | 10 | 15.667 |
| `Small` | 13 | 14 | 18.667 |
| `Medium` | 16 | 17 | 22.667 |
| `Large` | 20 | 22 | 32 |
| `Body` | 17 | 16 | 14 |
| `Header` | 17 | 96 | 46 |
| `Title` | 28 | 24 | 24 |
| `Subtitle` | 22 | 16 | 20 |
| `Caption` | 12 | 12 | 12 |

Les valeurs de taille sont mesurées en unités indépendantes des appareils. Pour plus d’informations, voir [Unités de mesure](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement).

Les tailles de police nommées peuvent être définies à la fois par XAML et le code. En outre, `Device.GetNamedSize` la méthode peut `double` être appelée pour retourner un qui représente la taille de police nommée:

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

> [!NOTE]
> Sur iOS et Android, les tailles de police nommées seront autoscale basées sur les options d’accessibilité du système d’exploitation. Ce comportement peut être désactivé sur iOS avec une plate-forme spécifique. Pour plus d’informations, voir [Accessibilité Mise à l’échelle pour les tailles de fontes nommées sur iOS](~/xamarin-forms/platform/ios/named-font-size-scaling.md).

<a name="Using_a_Custom_Font" />

## <a name="use-a-custom-font"></a>Utilisez une police personnalisée

L’utilisation d’une police autre que les polices intégrées nécessite un codage spécifique à la plate-forme. Cette capture d’écran montre la police personnalisée **Lobster** des [polices open-source de Google rendues à l’aide](https://www.google.com/fonts) de Xamarin.Forms.

 [![Police personnalisée sur iOS et Android](fonts-images/custom-sml.png "Personnalisé Polices Exemple")](fonts-images/custom.png#lightbox "Personnalisé Polices Exemple")

Les étapes requises pour chaque plate-forme sont décrites ci-dessous. Lorsque vous y trouverez des fichiers de police personnalisés avec une application, assurez-vous de vérifier que la licence de la police permet la distribution.

### <a name="ios"></a>iOS

Il est possible d’afficher une police personnalisée en s’assurant d’abord qu’elle `Font` est chargée, puis en se référant à elle par son nom en utilisant les méthodes Xamarin.Forms.
Suivez les instructions dans [ce billet de blog](https://devblogs.microsoft.com/xamarin/custom-fonts-in-ios/):

1. Ajouter le fichier de police avec **Build Action: BundleResource**, et
2. Mettre à jour le fichier **Info.plist** `UIAppFonts`**(Polices fournies par application,** ou, clé), puis
3. Faites-y par son nom partout où vous définissez une police dans Xamarin.Forms!

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" : null // set only for iOS
}
```

### <a name="android"></a>Android

Xamarin.Forms pour Android peut faire référence à une police personnalisée qui a été ajoutée au projet en suivant une norme de nommage spécifique. Ajoutez d’abord le fichier de police au dossier **Assets** dans le projet d’application et *définissez Build Action: AndroidAsset*. Ensuite, utilisez le chemin complet et *le nom de la fonte* séparé par un hachage (MD) comme nom de police dans Xamarin.Forms, comme le montre l’extrait de code ci-dessous :

```csharp
new Label
{
  Text = "Hello, Forms!",
  FontFamily = Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : null // set only for Android
}
```

### <a name="windows"></a>Windows

Xamarin.Forms pour les plates-formes Windows peut faire référence à une police personnalisée qui a été ajoutée au projet en suivant une norme de nommage spécifique. Ajoutez d’abord le fichier de police au **dossier /Assets/Fonts/** dans le projet d’application et définissez le **Contenu Build Action:Content**. Ensuite, utilisez le chemin complet et le nom de fichier de police, suivi d’un hachage (MD) et le **nom de la fonte**, comme le code extrait ci-dessous le démontre :

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.UWP ? "Assets/Fonts/Lobster-Regular.ttf#Lobster" : null // set only for UWP apps
}
```

> [!NOTE]
> Notez que le nom du fichier de police et le nom de police peut être différent. Pour découvrir le nom de police sur Windows, cliquez à droite sur le fichier .ttf et **sélectionnez Aperçu**. Le nom de la police peut alors être déterminé à partir de la fenêtre d’aperçu.

### <a name="xaml"></a>XAML

Vous pouvez [`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#interact-with-the-ui-from-background-threads) également utiliser dans XAML pour rendre une police personnalisée :

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

## <a name="use-a-custom-font-preview"></a>Utilisez une police personnalisée (PREVIEW)

Les polices personnalisées peuvent être ajoutées à votre projet partagé Xamarin.Forms et consommées par des projets de plate-forme sans aucun travail supplémentaire. Pour ce faire, procédez comme suit :

1. Ajoutez la police à votre projet partagé Xamarin.Forms en tant que ressource intégrée (**Build Action: EmbeddedResource**).
1. Enregistrez le fichier de police auprès de l’assemblage, `ExportFont` dans un fichier tel que **AssemblyInfo.cs,** en utilisant l’attribut. Un alias optionnel peut également être spécifié.

> [!NOTE]
> Les polices embarquées nécessitent l’utilisation de Xamarin.Forms 4.5.0.530 ou plus.

L’exemple suivant montre que la police Homard-Régulière est enregistrée auprès de l’assemblage, ainsi qu’un alias :

```csharp
using Xamarin.Forms;

[assembly: ExportFont("Lobster-Regular.ttf", Alias = "Lobster")]
```

> [!NOTE]
> La police peut résider dans n’importe quel dossier du projet partagé, sans avoir à spécifier le nom du dossier lors de l’enregistrement de la police avec l’assemblage.

La police peut ensuite être consommée sur chaque plate-forme en faisant référence à son nom, sans l’extension du fichier :

```xaml
<!-- Use font name -->
<Label Text="Hello Xamarin.Forms"
       FontFamily="Lobster-Regular" />
```

Alternativement, il peut être consommé sur chaque plate-forme en faisant référence à son alias :

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

Les captures d’écran suivantes montrent la police personnalisée :

[![Police personnalisée sur iOS et Android](fonts-images/custom-sml.png "Personnalisé Polices Exemple")](fonts-images/custom.png#lightbox "Personnalisé Polices Exemple")

## <a name="display-font-icons"></a>Afficher les icônes de police

Les icônes de police peuvent être affichées par les applications `FontImageSource` Xamarin.Forms en spécifiant les données de l’icône de police dans un objet. Cette classe, qui dérive [`ImageSource`](xref:Xamarin.Forms.ImageSource) de la classe, a les propriétés suivantes:

- `Glyph`la valeur de caractère unicode de l’icône de police, spécifiée comme un `string`.
- `Size`une `double` valeur qui indique la taille, dans les unités indépendantes de l’appareil, de l’icône de police rendue. La valeur par défaut est 30. En outre, cette propriété peut être réglée à une taille de police nommée.
- `FontFamily`- `string` une représentation de la famille de police à laquelle appartient l’icône de police.
- `Color`une valeur [`Color`](xref:Xamarin.Forms.Color) facultative à utiliser lors de l’affichage de l’icône de police.

Ces données sont utilisées pour créer un PNG, qui peut `ImageSource`être affiché par n’importe quelle vue qui peut afficher un . Cette approche permet aux icônes de police, telles que les emojis, d’être affichées par plusieurs [`Label`](xref:Xamarin.Forms.Label)vues, plutôt que de limiter l’affichage de l’icône de police à un seul texte présentant la vue, comme un .

> [!IMPORTANT]
> Les icônes de police ne peuvent actuellement être spécifiées que par leur représentation de caractère unicode.

L’exemple XAML suivant a une seule [`Image`](xref:Xamarin.Forms.Image) icône de police affichée par une vue :

```xaml
<Image BackgroundColor="#D1D1D1">
    <Image.Source>
        <FontImageSource Glyph="&#xf30c;"
                         FontFamily="{OnPlatform iOS=Ionicons, Android=ionicons.ttf#}"
                         Size="44" />
    </Image.Source>
</Image>
```

Ce code affiche une icône XBox, de la [`Image`](xref:Xamarin.Forms.Image) famille de polices Ionicons, dans une vue. Notez que si le caractère `\uf30c`unicode pour cette icône est , `&#xf30c;`il doit être échappé dans XAML et devient donc . Le code C# équivalent est :

```csharp
Image image = new Image { BackgroundColor = Color.FromHex("#D1D1D1") };
image.Source = new FontImageSource
{
    Glyph = "\uf30c",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Ionicons" : "ionicons.ttf#",
    Size = 44
};
```

Les captures d’écran suivantes, tirées de l’échantillon [Bindable Layouts,](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts) montrent plusieurs icônes de police affichées par une mise en page liant :

![Capture d’écran des icônes de police affichées, sur iOS et Android](fonts-images/font-image-source.png "Icônes de police affichées dans une vue d’image")

## <a name="related-links"></a>Liens connexes

- [FontsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)
- [Texte (échantillon)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Mises en page liant (échantillon)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Mises en page liants](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
