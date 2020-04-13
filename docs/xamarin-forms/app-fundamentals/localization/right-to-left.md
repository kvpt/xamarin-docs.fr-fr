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
ms.openlocfilehash: a6eb3167fd0880984a74245c4653642ea3979354
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "72678839"
---
# <a name="right-to-left-localization"></a>Localisation de droite à gauche

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalizedrtl)

_La localisation de droite à gauche ajoute la prise en charge de la direction de flux de droite à gauche pour les applications Xamarin.Forms._

> [!NOTE]
> La localisation de droite à gauche nécessite l’utilisation d’iOS 9 ou version ultérieure, et de l’API 17 ou version ultérieure sur Android.

La direction de flux est la direction dans laquelle les éléments d’IU sur la page sont analysés par l’œil. Dans certaines langues, par exemple l’arabe et l’hébreu, les éléments d’IU doivent être disposés de droite à gauche. Ceci peut être réalisé [`VisualElement.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) en fixant la propriété. Cette propriété obtient ou définit la direction dans laquelle les éléments d’interface utilisateur s’écoulent dans n’importe quel élément parent qui contrôle leur disposition, et doit être réglé à l’une des valeurs de [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) recensement:

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

La [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) mise [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft) en place de la propriété sur un élément définit généralement l’alignement vers la droite, l’ordre de lecture à droite à gauche, et la disposition du contrôle à couler de droite à gauche:

[![TodoItemPage en arabe avec une direction de flux de droite à gauche](rtl-images/TodoItemPage-Arabic.png "TodoItemPage en arabe avec une direction de flux de droite à gauche")](rtl-images/TodoItemPage-Arabic-Large.png#lightbox "TodoItemPage en arabe avec une direction de flux de droite à gauche")

> [!TIP]
> Vous ne devez [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) définir la propriété que sur la disposition initiale. Si vous changez cette valeur au moment de l’exécution, cela alourdit le processus de disposition et affecte le niveau de performance.

La [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) valeur de propriété par défaut [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)pour un `FlowDirection` élément sans parent [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)est, tandis que le défaut pour un élément avec un parent est . Un élément hérite donc de la valeur de propriété `FlowDirection` de son parent dans l’arborescence d’éléments visuels, et un élément peut remplacer la valeur qu’il obtient de son parent.

> [!TIP]
> Lors de la localisation d’une application pour [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) les langues de droite à gauche, définissez la propriété sur une page ou une mise en page de racine. Ainsi, tous les éléments contenus dans la page ou la disposition racine répondent correctement à la direction de flux.

## <a name="respecting-device-flow-direction"></a>Respect de la direction de flux de l’appareil

Le respect de la direction de flux de l’appareil en fonction de la langue et de la région sélectionnées est un choix explicite du développeur et n’est pas automatique. Il peut être réalisé [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) en définissant la propriété sur `static` [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection) une page, ou la mise en page des racines, à la valeur:

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

```csharp
this.FlowDirection = Device.FlowDirection;
```

Tous les éléments enfant de la page, ou [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection) mise en page des racines, hériteront par défaut de la valeur.

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

![Langues supportées Info.plist](rtl-images/ios-locales.png "Langues supportées Info.plist")

Pour plus d’informations, consultez les [Principes de base de la localisation dans iOS](https://docs.microsoft.com/xamarin/ios/app-fundamentals/localization/#localization-basics-in-ios).

Vous pouvez ensuite tester la localisation de droite à gauche en remplaçant la langue et la région de l’appareil/du simulateur par des paramètres régionaux de droite à gauche spécifiés dans **Info.plist**.

> [!WARNING]
> Veuillez noter que lorsque vous changez la langue et la région [`DatePicker`](xref:Xamarin.Forms.DatePicker) en un lieu de droite à gauche sur iOS, toute vue jettera une exception si vous n’incluez pas les ressources nécessaires pour le lieu. Par exemple, quand vous testez une application en arabe comportant `DatePicker`, vérifiez que **mideast** est sélectionné dans la section **Internationalisation** du volet **Build iOS**.

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

## <a name="limitations"></a>Limites

La localisation de droite à gauche de Xamarin.Forms présente un certain nombre de limitations :

- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)l’emplacement du bouton, l’emplacement de l’élément de la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) barre d’outils et l’animation de transition sont contrôlés par le lieu de l’appareil, plutôt que par la propriété.
- [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)glisser la direction ne se retourne pas.
- [`Image`](xref:Xamarin.Forms.Image)contenu visuel ne bascule pas.
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String))et [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) l’orientation est contrôlée par le [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) lieu de l’appareil, plutôt que par la propriété.
- [`WebView`](xref:Xamarin.Forms.WebView)ne respecte pas [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) la propriété.
- Vous devez ajouter une propriété `TextDirection` pour contrôler l’alignement du texte.

### <a name="ios"></a>iOS

- [`Stepper`](xref:Xamarin.Forms.Stepper)l’orientation est contrôlée par le [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) lieu de l’appareil, plutôt que par la propriété.
- [`EntryCell`](xref:Xamarin.Forms.EntryCell)l’alignement du texte est contrôlé par [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) le lieu de l’appareil, plutôt que par la propriété.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions)gestes et l’alignement ne sont pas inversés.

### <a name="android"></a>Android

- [`SearchBar`](xref:Xamarin.Forms.SearchBar)l’orientation est contrôlée par le [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) lieu de l’appareil, plutôt que par la propriété.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions)le placement est contrôlé par le [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) lieu de l’appareil, plutôt que par la propriété.

### <a name="uwp"></a>UWP

- [`Editor`](xref:Xamarin.Forms.Editor)l’alignement du texte est contrôlé par [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) le lieu de l’appareil, plutôt que par la propriété.
- [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)les enfants n’ont pas hérité de [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) biens.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions)l’alignement du texte est contrôlé par [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) le lieu de l’appareil, plutôt que par la propriété.

## <a name="force-right-to-left-layout"></a>Mise en page de la force de droite à gauche

Les applications Xamarin.iOS et Xamarin.Android peuvent être contraintes d’utiliser toujours une mise en page droite à gauche, quels que soient les paramètres de l’appareil, en modifiant les projets de plate-forme respectifs.

### <a name="ios"></a>iOS

Les applications Xamarin.iOS peuvent être forcées d’utiliser toujours une mise en page droite à gauche en modifiant la classe **AppDelegate** comme suit :

1. Déclarez `IntPtr_objc_msgSend` la fonction comme `AppDelegate` première ligne dans votre classe :

   ```csharp
   [System.Runtime.InteropServices.DllImport(ObjCRuntime.Constants.ObjectiveCLibrary, EntryPoint = "objc_msgSend")]
   internal extern static IntPtr IntPtr_objc_msgSend(IntPtr receiver, IntPtr selector, UISemanticContentAttribute arg1);
   ```

1. Appelez `IntPtr_objc_msgSend` la fonction `FinishedLaunching` à partir de `FinshedLaunching` la méthode, avant de revenir de la méthode:

   ```csharp
   bool result = base.FinishedLaunching(app, options);

   ObjCRuntime.Selector selector = new ObjCRuntime.Selector("setSemanticContentAttribute:");
   IntPtr_objc_msgSend(UIView.Appearance.Handle, selector.Handle, UISemanticContentAttribute.ForceRightToLeft);

   return result;
   ```

Cette approche est utile pour les applications qui nécessitent toujours une disposition de [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) droite à gauche, et supprime l’exigence de définir la propriété.

Pour plus d’informations sur la `IntrPtr_objc_msgSend` méthode, voir Les [sélecteurs Objectif-C dans Xamarin.iOS](~/ios/internals/objective-c-selectors.md).

### <a name="android"></a>Android

Les applications Xamarin.Android peuvent être obligées d’utiliser toujours une mise en page droite à gauche en modifiant la classe **MainActivity** pour inclure la ligne suivante :

```csharp
Window.DecorView.LayoutDirection = LayoutDirection.Rtl;
```

Cette approche est utile pour les applications qui nécessitent toujours une disposition de [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) droite à gauche, et supprime l’exigence de définir la propriété.

## <a name="right-to-left-language-support-with-xamarinuniversity"></a>Prise en charge linguistique de droite à gauche avec Xamarin.University

> [!VIDEO https://youtube.com/embed/f2lQ5yw3iiU]

**Vidéo sur la prise en charge linguistique de droite à gauche avec Xamarin.Forms 3.0**

## <a name="related-links"></a>Liens connexes

- [Exemple d’application TodoLocalizedRTL](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalizedrtl)
