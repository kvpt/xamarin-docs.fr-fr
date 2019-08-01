---
title: Localisation de droite à gauche
description: La localisation de droite à gauche ajoute la prise en charge de la direction de flux de droite à gauche pour les applications Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 90E0CB16-C42A-4CC8-A70E-0C2CFB64A429
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 78288680a1a522b2c6c413e1f8a2cec2a07835d6
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656979"
---
# <a name="right-to-left-localization"></a>Localisation de droite à gauche

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalizedrtl)

_La localisation de droite à gauche ajoute la prise en charge de la direction de flux de droite à gauche pour les applications Xamarin.Forms._

> [!NOTE]
> La localisation de droite à gauche nécessite l’utilisation d’iOS 9 ou version ultérieure, et de l’API 17 ou version ultérieure sur Android.

La direction de flux est la direction dans laquelle les éléments d’IU sur la page sont analysés par l’œil. Dans certaines langues, par exemple l’arabe et l’hébreu, les éléments d’IU doivent être disposés de droite à gauche. Pour ce faire, définissez la propriété [`VisualElement.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection). Cette propriété obtient ou définit la direction de flux des éléments d’IU dans les éléments parents qui contrôlent leur disposition. Elle doit avoir l’une des valeurs d’énumération de [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) :

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

L’affectation à la propriété [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) de la valeur [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft) pour un élément permet généralement de définir l’alignement à droite, l’ordre de lecture de droite à gauche et la direction de flux du contrôle de droite à gauche :

[![TodoItemPage en arabe avec une direction de flux de droite à gauche](rtl-images/TodoItemPage-Arabic.png "TodoItemPage en arabe avec une direction de flux de droite à gauche")](rtl-images/TodoItemPage-Arabic-Large.png#lightbox "TodoItemPage en arabe avec une direction de flux de droite à gauche")

> [!TIP]
> Vous devez uniquement définir la propriété [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) pour la disposition initiale. Si vous changez cette valeur au moment de l’exécution, cela alourdit le processus de disposition et affecte le niveau de performance.

La valeur de propriété [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) par défaut pour un élément sans parent est [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight), alors que le `FlowDirection` par défaut d’un élément avec parent est [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent). Un élément hérite donc de la valeur de propriété `FlowDirection` de son parent dans l’arborescence d’éléments visuels, et un élément peut remplacer la valeur qu’il obtient de son parent.

> [!TIP]
> Quand vous localisez une application pour les langues lues de droite à gauche, définissez la propriété [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) dans une page ou une disposition racine. Ainsi, tous les éléments contenus dans la page ou la disposition racine répondent correctement à la direction de flux.

## <a name="respecting-device-flow-direction"></a>Respect de la direction de flux de l’appareil

Le respect de la direction de flux de l’appareil en fonction de la langue et de la région sélectionnées est un choix explicite du développeur et n’est pas automatique. Pour ce faire, affectez à la propriété [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) d’une page ou d’une disposition racine la valeur `static` [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection) :

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

```csharp
this.FlowDirection = Device.FlowDirection;
```

Tous les éléments enfants de la page, ou de la disposition racine, héritent par défaut de la valeur [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection).

## <a name="platform-setup"></a>Configuration de la plateforme

Une configuration de plateforme spécifique est obligatoire pour permettre l’activation des paramètres régionaux de droite à gauche.

### <a name="ios"></a>iOS

Les paramètres régionaux de droite à gauche nécessaires doivent être ajoutés aux éléments de tableau de la clé `CFBundleLocalizations` dans **Info.plist** en tant que langue prise en charge. L’exemple suivant montre que l’arabe a été ajouté au tableau pour la clé `CFBundleLocalizations` :

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>ar</string>
</array>
```

![Langues prises en charge par Info.plist](rtl-images/ios-locales.png "Langues prises en charge par Info.plist")

Pour plus d’informations, consultez les [Principes de base de la localisation dans iOS](https://docs.microsoft.com/xamarin/ios/app-fundamentals/localization/#localization-basics-in-ios).

Vous pouvez ensuite tester la localisation de droite à gauche en remplaçant la langue et la région de l’appareil/du simulateur par des paramètres régionaux de droite à gauche spécifiés dans **Info.plist**.

> [!WARNING]
> Notez qu’à partir du moment où vous changez la langue et la région en introduisant des paramètres régionaux de droite à gauche sur iOS, les vues [`DatePicker`](xref:Xamarin.Forms.DatePicker) lèvent une exception si vous n’incluez pas les ressources nécessaires pour ces paramètres régionaux. Par exemple, quand vous testez une application en arabe comportant `DatePicker`, vérifiez que **mideast** est sélectionné dans la section **Internationalisation** du volet **Build iOS**.

### <a name="android"></a>Android

Le fichier **AndroidManifest.xml** de l’application doit être mis à jour pour que le nœud `<uses-sdk>` affecte la valeur 17 à l’attribut `android:minSdkVersion`, et que le nœud `<application>` affecte la valeur `true` à l’attribut `android:supportsRtl` :

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest ... >
    <uses-sdk android:minSdkVersion="17" ... />
    <application ... android:supportsRtl="true">
    </application>
</manifest>
```

Vous pouvez ensuite tester la localisation de droite à gauche en changeant l’appareil/émulateur pour qu’il utilise la langue de droite à gauche, ou en activant l’option permettant de **forcer la disposition DàG (de droite à gauche)** dans **Paramètres > Options pour développeurs**.

### <a name="universal-windows-platform-uwp"></a>Plateforme Windows universelle (UWP)

Vous devez spécifier les ressources linguistiques nécessaires dans le nœud `<Resources>` du fichier **Package.appxmanifest**. L’exemple suivant montre que l’arabe a été ajouté au nœud `<Resources>` :

```xml
<Resources>
    <Resource Language="x-generate"/>
    <Resource Language="en" />
    <Resource Language="ar" />
</Resources>
```

De plus, avec UWP vous devez définir explicitement la culture par défaut de l’application dans la bibliothèque .NET Standard. Pour ce faire, affectez la culture par défaut à l’attribut `NeutralResourcesLanguage` dans `AssemblyInfo.cs` ou une autre classe :

```csharp
using System.Resources;

[assembly: NeutralResourcesLanguage("en")]
```

Vous pouvez ensuite tester la localisation de droite à gauche en remplaçant la langue et la région de l’appareil/du simulateur par les paramètres régionaux de droite à gauche appropriés.

## <a name="limitations"></a>Limitations

La localisation de droite à gauche de Xamarin.Forms présente un certain nombre de limitations :

- Pour [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), l’emplacement du bouton, l’emplacement des éléments de barre d’outils et l’animation de transition sont contrôlés par les paramètres régionaux de l’appareil, et non par la propriété [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- La direction de balayage de [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) ne se retourne pas.
- Le contenu visuel de [`Image`](xref:Xamarin.Forms.Image) ne se retourne pas.
- L’orientation de [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) et [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) est contrôlée par les paramètres régionaux de l’appareil, et non par la propriété [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- Le contenu de [`WebView`](xref:Xamarin.Forms.WebView) ne respecte pas la propriété [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- Vous devez ajouter une propriété `TextDirection` pour contrôler l’alignement du texte.

### <a name="ios"></a>iOS

- L’orientation de [`Stepper`](xref:Xamarin.Forms.Stepper) est contrôlée par les paramètres régionaux de l’appareil, et non par la propriété [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- L’alignement du texte de [`EntryCell`](xref:Xamarin.Forms.EntryCell) est contrôlé par les paramètres régionaux de l’appareil, et non par la propriété [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- Les mouvements et l’alignement de [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) ne sont pas inversés.

### <a name="android"></a>Android

- L’orientation de [`SearchBar`](xref:Xamarin.Forms.SearchBar) est contrôlée par les paramètres régionaux de l’appareil, et non par la propriété [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- Le placement de [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) est contrôlé par les paramètres régionaux de l’appareil, et non par la propriété [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).

### <a name="uwp"></a>UWP

- L’alignement du texte de [`Editor`](xref:Xamarin.Forms.Editor) est contrôlé par les paramètres régionaux de l’appareil, et non par la propriété [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).
- La propriété [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) n’est pas héritée par les enfants de [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage).
- L’alignement du texte de [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) est contrôlé par les paramètres régionaux de l’appareil, et non par la propriété [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection).

## <a name="right-to-left-language-support-with-xamarinuniversity"></a>Prise en charge linguistique de droite à gauche avec Xamarin.University

> [!VIDEO https://youtube.com/embed/f2lQ5yw3iiU]

**Vidéo sur la prise en charge linguistique de droite à gauche avec Xamarin.Forms 3.0**

## <a name="related-links"></a>Liens connexes

- [Exemple d’application TodoLocalizedRTL](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalizedrtl)
