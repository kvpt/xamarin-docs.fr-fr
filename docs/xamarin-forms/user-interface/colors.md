---
title: Couleurs dans Xamarin. Forms
description: Xamarin. Forms fournit une classe de couleur multiplateforme flexible. Cet article explique la fonctionnalité fournie par la classe Color et comment l’utiliser.
ms.prod: xamarin
ms.assetid: 22288ABF-57BE-47A9-ACC3-AC604D787C46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2020
ms.openlocfilehash: 42b532b8565d2d8e0289b8fd446e1dd7762a09ac
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517032"
---
# <a name="colors-in-xamarinforms"></a>Couleurs dans Xamarin. Forms

[![Télécharger l'](~/media/shared/download.png) exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithcolors)

_Xamarin. Forms fournit une classe de couleur multiplateforme flexible._

Cet article présente les différentes façons dont [`Color`](xref:Xamarin.Forms.Color) la classe peut être utilisée dans Xamarin. Forms.

La [`Color`](xref:Xamarin.Forms.Color) classe fournit un certain nombre de méthodes pour générer `Color` une instance :

- **Couleurs nommées** : collection de couleurs nommées communes, y `Red`compris `Green`, et `Blue`.
- `FromHex`valeur de chaîne similaire à la syntaxe utilisée en HTML, par exemple « 00FF00 ». Alpha peut éventuellement être spécifié en tant que première paire de caractères (« CC00FF00 »).
- `FromHsla`Valeurs de teinte, de `double` saturation et de luminosité, avec une valeur alpha facultative (0.0-1.0).
- `FromHsv`-Teinte, saturation et valeur `int` ou `double` valeurs.
- `FromHsva`-Teinte, saturation et valeur `int` ou `double` valeurs.
- `FromRgb`-Les valeurs de rouge, de `int` vert et de bleu (0-255).
- `FromRgba`-Valeurs rouge, vert, bleu et alpha `int` (0-255).
- `FromUint`-définir une valeur `double` unique représentant **ARGB**.

Voici quelques exemples de couleurs, assignées `BackgroundColor` au de certaines étiquettes en utilisant différentes variantes de la syntaxe autorisée :

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

Ces couleurs sont affichées sur chaque plateforme ci-dessous. Notez la couleur finale- `Accent` -est une couleur bleu-longues pour iOS et Android. Cette valeur est définie par Xamarin. Forms.

 [![Démonstration de couleur](colors-images/colors-sml.png "Démonstration de couleur")](colors-images/colors.png#lightbox "Démonstration de couleur")

## <a name="colordefault"></a>Color. default

Utilisez `Default` pour définir (ou redéfinir) une valeur de couleur à la valeur par défaut de la plateforme (en sachant que cela représente une couleur sous-jacente différente sur chaque plateforme pour chaque propriété).

Les développeurs peuvent utiliser cette valeur pour définir `Color` une propriété, mais **ne doivent pas** interroger cette instance pour ses valeurs RVB de composant (elles ont toutes la valeur-1).

## <a name="colortransparent"></a>Color. transparent

Définissez la couleur sur Effacer.

## <a name="coloraccent"></a>Color. accent

Sur iOS et Android, cette instance est définie sur une couleur de contraste visible sur l’arrière-plan par défaut, mais elle n’est pas la même que la couleur de texte par défaut.

## <a name="additional-methods"></a>Méthodes supplémentaires

[`Color`](xref:Xamarin.Forms.Color)les instances de incluent les méthodes supplémentaires suivantes :

- `AddLuminosity`-retourne un `Color` en modifiant la luminosité par le delta fourni.
- `MultiplyAlpha`-retourne un `Color` en modifiant le alpha, en le multipliant par la valeur alpha fournie.
- `ToHex`-retourne une représentation `string` hexadécimale d' `Color`un.
- `WithHue`-retourne un `Color`, en remplaçant la teinte par la valeur fournie.
- `WithLuminosity`-retourne un `Color`, en remplaçant la luminosité par la valeur fournie.
- `WithSaturation`-retourne un `Color`, en remplaçant la saturation par la valeur fournie.

## <a name="implicit-conversions"></a>Conversions implicites

La conversion implicite `Xamarin.Forms.Color` entre `System.Drawing.Color` les types et peut être effectuée :

```csharp
Xamarin.Forms.Color xfColor = Xamarin.Forms.Color.FromRgb(0, 72, 255);
System.Drawing.Color sdColor = System.Drawing.Color.FromArgb(38, 127, 0);

// Implicity convert from a Xamarin.Forms.Color to a System.Drawing.Color
System.Drawing.Color sdColor2 = xfColor;

// Implicitly convert from a System.Drawing.Color to a Xamarin.Forms.Color
Xamarin.Forms.Color xfColor2 = sdColor;
```

## <a name="deviceruntimeplatform"></a>Appareil. RuntimePlatform

Cet extrait de code utilise `Device.RuntimePlatform` la propriété pour définir de manière sélective la couleur `ActivityIndicator`d’un :

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator
{
    Color = Device.RuntimePlatform == Device.iOS ? Color.Black : Color.Default,
    IsRunning = true
};
```

## <a name="use-from-xaml"></a>Utiliser à partir de XAML

Les couleurs peuvent également être référencées en XAML à l’aide des noms de couleurs définis ou des représentations hexadécimales indiquées ici :

```xaml
<Label Text="Sea color" BackgroundColor="Aqua" />
<Label Text="RGB" BackgroundColor="#00FF00" />
<Label Text="Alpha plus RGB" BackgroundColor="#CC00FF00" />
<Label Text="Tiny RGB" BackgroundColor="#0F0" />
<Label Text="Tiny Alpha plus RGB" BackgroundColor="#C0F0" />
```

> [!NOTE]
> Lors de l’utilisation de la compilation XAML, les noms de couleurs ne respectent pas la casse et peuvent donc être écrits en minuscules. Pour plus d’informations sur la compilation XAML, consultez [Compilation XAML](~/xamarin-forms/xaml/xamlc.md).

## <a name="related-links"></a>Liens connexes

- [ColorsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithcolors)
- [Sélecteur pouvant être lié (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablepicker)
