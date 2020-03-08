---
title: Couleurs dans Xamarin.Forms
description: Xamarin.Forms fournit une classe de couleur inter-plateformes flexible. Cet article décrit les fonctionnalités fournies par la classe de couleur et comment l’utiliser.
ms.prod: xamarin
ms.assetid: 22288ABF-57BE-47A9-ACC3-AC604D787C46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2019
ms.openlocfilehash: 2a17b037803d1ca6e54000ea7ba3f05c8ce6034f
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78912979"
---
# <a name="colors-in-xamarinforms"></a>Couleurs dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithcolors)

_Xamarin. Forms fournit une classe de couleur multiplateforme flexible._

Cet article présente les différentes façons dont la classe [`Color`](xref:Xamarin.Forms.Color) peut être utilisée dans Xamarin. Forms.

La classe [`Color`](xref:Xamarin.Forms.Color) fournit un certain nombre de méthodes pour générer une instance de couleur :

- **Couleurs nommées** : collection de couleurs nommées communes, y compris `Red`, `Green`et `Blue`.
- **FromHex** : valeur de chaîne similaire à la syntaxe utilisée en HTML, par exemple « 00FF00 ». Alpha peut éventuellement être spécifié en tant que première paire de caractères (« CC00FF00 »).
- **FromHsla** -Hue, saturation et luminance `double` valeurs, avec la valeur alpha facultative (0.0-1.0).
- **FromRgb** : valeurs de `int` rouge, vert et bleu (0-255).
- **FromRgba** : valeurs de `int` rouge, vert, bleu et alpha (0-255).
- **FromUint** : définit une valeur de `double` unique représentant **ARGB**.

Voici quelques exemples de couleurs, assignées au `BackgroundColor` de certaines étiquettes en utilisant différentes variantes de la syntaxe autorisée :

```csharp
var red    = new Label { Text = "Red",   BackgroundColor = Color.Red };
var orange = new Label { Text = "Orange",BackgroundColor = Color.FromHex("FF6A00") };
var yellow = new Label { Text = "Yellow",BackgroundColor = Color.FromHsla(0.167, 1.0, 0.5, 1.0) };
var green  = new Label { Text = "Green", BackgroundColor = Color.FromRgb (38, 127, 0) };
var blue   = new Label { Text = "Blue",  BackgroundColor = Color.FromRgba(0, 38, 255, 255) };
var indigo = new Label { Text = "Indigo",BackgroundColor = Color.FromRgb (0, 72, 255) };
var violet = new Label { Text = "Violet",BackgroundColor = Color.FromHsla(0.82, 1, 0.25, 1) };

var transparent = new Label { Text = "Transparent",BackgroundColor = Color.Transparent };
var @default = new Label    { Text = "Default",    BackgroundColor = Color.Default };
var accent = new Label      { Text = "Accent",     BackgroundColor = Color.Accent };
```

Ces couleurs sont affichées sur chaque plateforme ci-dessous. Notez le `Accent` de couleur finale-est une couleur bleu-longues pour iOS et Android. Cette valeur est définie par Xamarin. Forms.

 [![Démonstration de couleur](colors-images/colors-sml.png "Démonstration de couleur")](colors-images/colors.png#lightbox "Démonstration de couleur")

## <a name="colordefault"></a>Color.Default

Utilisez la `Default` pour définir (ou redéfinir) une valeur de couleur sur la plateforme par défaut (en sachant que cela représente une couleur sous-jacente différente sur chaque plateforme pour chaque propriété).

Les développeurs peuvent utiliser cette valeur pour définir une propriété de `Color`, mais **ne doivent pas** interroger cette instance pour ses valeurs RVB de composant (elles ont toutes la valeur-1).

## <a name="colortransparent"></a>Color.Transparent

Définissez la couleur à effacer.

## <a name="coloraccent"></a>Color.Accent

Sur iOS et Android, cette instance est définie sur une couleur contrastée qui est visible sur l’arrière-plan par défaut, mais n’est pas identique à la couleur du texte par défaut.

## <a name="additional-methods"></a>Méthodes supplémentaires

les instances de [`Color`](xref:Xamarin.Forms.Color) incluent les méthodes supplémentaires suivantes :

- **AddLuminosity** -retourne une `Color` en modifiant la luminosité par le delta fourni.
- **MultiplyAlpha** -retourne une `Color` en modifiant l’alpha, en la multipliant par la valeur alpha fournie.
- **ToHex** : retourne une représentation hexadécimale `string` d’un `Color`.
- **WithHue** -retourne un `Color`, en remplaçant la teinte par la valeur fournie.
- **WithLuminosity** -retourne un `Color`, en remplaçant la luminosité par la valeur fournie.
- **WithSaturation** -retourne un `Color`, en remplaçant la saturation par la valeur fournie.

## <a name="implicit-conversions"></a>Conversions implicites

La conversion implicite entre les types `Xamarin.Forms.Color` et `System.Drawing.Color` peut être effectuée :

```csharp
Xamarin.Forms.Color xfColor = Xamarin.Forms.Color.FromRgb(0, 72, 255);
System.Drawing.Color sdColor = System.Drawing.Color.FromArgb(38, 127, 0);

// Implicity convert from a Xamarin.Forms.Color to a System.Drawing.Color
System.Drawing.Color sdColor2 = xfColor;

// Implicitly convert from a System.Drawing.Color to a Xamarin.Forms.Color
Xamarin.Forms.Color xfColor2 = sdColor;
```

## <a name="deviceruntimeplatform"></a>Device.RuntimePlatform

Cet extrait de code utilise la propriété `Device.RuntimePlatform` pour définir de manière sélective la couleur d’un `ActivityIndicator`:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator
{
    Color = Device.RuntimePlatform == Device.iOS ? Color.Black : Color.Default,
    IsRunning = true
};
```

## <a name="using-from-xaml"></a>À l’aide de XAML

Les couleurs peuvent également être référencées en XAML à l’aide des noms de couleurs définis ou des représentations hexadécimales indiquées ici :

```xaml
<Label Text="Sea color" BackgroundColor="Aqua" />
<Label Text="RGB" BackgroundColor="#00FF00" />
<Label Text="Alpha plus RGB" BackgroundColor="#CC00FF00" />
<Label Text="Tiny RGB" BackgroundColor="#0F0" />
<Label Text="Tiny Alpha plus RGB" BackgroundColor="#C0F0" />
```

> [!NOTE]
> Lorsque vous utilisez la compilation de XAML, les noms de couleur respectent la casse et par conséquent, peuvent être écrits en minuscules. Pour plus d’informations sur la compilation XAML, consultez [Compilation XAML](~/xamarin-forms/xaml/xamlc.md).

## <a name="related-links"></a>Liens connexes

- [ColorsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithcolors)
- [Sélecteur pouvant être lié (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablepicker)
