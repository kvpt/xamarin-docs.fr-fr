---
title: Localisation de droite à gauche
description: La localisation de droite à gauche ajoute la prise en charge du sens du déroulement de droite à gauche aux Xamarin.Forms applications.
ms.prod: xamarin
ms.assetid: 90E0CB16-C42A-4CC8-A70E-0C2CFB64A429
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 212674472706a36c66436a3955ab7b988f8e246b
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137551"
---
# <a name="right-to-left-localization"></a>Localisation de droite à gauche

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalizedrtl)

_La localisation de droite à gauche ajoute la prise en charge du sens du déroulement de droite à gauche aux Xamarin.Forms applications._

> [!NOTE]
> La localisation de droite à gauche nécessite l’utilisation d’iOS 9 ou version ultérieure, et de l’API 17 ou version ultérieure sur Android.

La direction de flux est la direction dans laquelle les éléments d’IU sur la page sont analysés par l’œil. Dans certaines langues, par exemple l’arabe et l’hébreu, les éléments d’IU doivent être disposés de droite à gauche. Pour ce faire, définissez la [`VisualElement.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriété. Cette propriété obtient ou définit la direction dans laquelle les éléments d’interface utilisateur sont acheminés dans tout élément parent qui contrôle leur disposition, et doit être défini sur l’une des [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) valeurs d’énumération :

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

L’affectation de la valeur à la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriété [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft) sur un élément définit généralement l’alignement à droite, l’ordre de lecture de droite à gauche et la disposition du contrôle pour qu’il se déroule de droite à gauche :

[![TodoItemPage en arabe avec sens du déroulement de droite à gauche](rtl-images/TodoItemPage-Arabic.png "TodoItemPage en arabe avec sens du déroulement de droite à gauche")](rtl-images/TodoItemPage-Arabic-Large.png#lightbox "TodoItemPage en arabe avec sens du déroulement de droite à gauche")

> [!TIP]
> Vous devez uniquement définir la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriété sur la disposition initiale. Si vous changez cette valeur au moment de l’exécution, cela alourdit le processus de disposition et affecte le niveau de performance.

La [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) valeur de propriété par défaut pour un élément sans parent est [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight) , tandis que la valeur par défaut `FlowDirection` d’un élément avec un parent est [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent) . Un élément hérite donc de la valeur de propriété `FlowDirection` de son parent dans l’arborescence d’éléments visuels, et un élément peut remplacer la valeur qu’il obtient de son parent.

> [!TIP]
> Lors de la localisation d’une application pour les langues de droite à gauche, définissez la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriété sur une disposition de page ou racine. Ainsi, tous les éléments contenus dans la page ou la disposition racine répondent correctement à la direction de flux.

## <a name="respecting-device-flow-direction"></a>Respect de la direction de flux de l’appareil

Le respect de la direction de flux de l’appareil en fonction de la langue et de la région sélectionnées est un choix explicite du développeur et n’est pas automatique. Pour ce faire, définissez la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriété sur une page ou une disposition racine sur la `static` [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection) valeur :

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

```csharp
this.FlowDirection = Device.FlowDirection;
```

Tous les éléments enfants de la page, ou disposition racine, héritent par défaut de la [`Device.FlowDirection`](xref:Xamarin.Forms.Device.FlowDirection) valeur.

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

![Informations sur les langues prises en charge par info. plist](rtl-images/ios-locales.png "Informations sur les langues prises en charge par info. plist")

Pour plus d’informations, consultez les [Principes de base de la localisation dans iOS](https://docs.microsoft.com/xamarin/ios/app-fundamentals/localization/#localization-basics-in-ios).

Vous pouvez ensuite tester la localisation de droite à gauche en remplaçant la langue et la région de l’appareil/du simulateur par des paramètres régionaux de droite à gauche spécifiés dans **Info.plist**.

> [!WARNING]
> Notez que lorsque vous remplacez la langue et la région par des paramètres régionaux de droite à gauche sur iOS, les [`DatePicker`](xref:Xamarin.Forms.DatePicker) vues lèvent une exception si vous n’incluez pas les ressources requises pour les paramètres régionaux. Par exemple, quand vous testez une application en arabe comportant `DatePicker`, vérifiez que **mideast** est sélectionné dans la section **Internationalisation** du volet **Build iOS**.

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

Xamarin.Formsla localisation de droite à gauche présente actuellement un certain nombre de limitations :

- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)l’emplacement du bouton, l’emplacement de l’élément de barre d’outils et l’animation de transition sont contrôlés par les paramètres régionaux de l’appareil, plutôt que par la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriété.
- [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)la direction de balayage n’est pas inversée.
- [`Image`](xref:Xamarin.Forms.Image)le contenu visuel n’est pas inversé.
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String))et l' [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) orientation sont contrôlées par les paramètres régionaux de l’appareil, plutôt que par la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriété.
- [`WebView`](xref:Xamarin.Forms.WebView)le contenu ne respecte pas la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriété.
- Vous devez ajouter une propriété `TextDirection` pour contrôler l’alignement du texte.

### <a name="ios"></a>iOS

- [`Stepper`](xref:Xamarin.Forms.Stepper)l’orientation est contrôlée par les paramètres régionaux de l’appareil, plutôt que par la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriété.
- [`EntryCell`](xref:Xamarin.Forms.EntryCell)l’alignement du texte est contrôlé par les paramètres régionaux de l’appareil, plutôt que par la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriété.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions)les mouvements et l’alignement ne sont pas inversés.

### <a name="android"></a>Android

- [`SearchBar`](xref:Xamarin.Forms.SearchBar)l’orientation est contrôlée par les paramètres régionaux de l’appareil, plutôt que par la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriété.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions)le positionnement est contrôlé par les paramètres régionaux de l’appareil, plutôt que par la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriété.

### <a name="uwp"></a>UWP

- [`Editor`](xref:Xamarin.Forms.Editor)l’alignement du texte est contrôlé par les paramètres régionaux de l’appareil, plutôt que par la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriété.
- [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)la propriété n’est pas héritée par les [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) enfants.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions)l’alignement du texte est contrôlé par les paramètres régionaux de l’appareil, plutôt que par la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriété.

## <a name="force-right-to-left-layout"></a>Forcer la mise en page de droite à gauche

Les applications Xamarin. iOS et Xamarin. Android peuvent être forcées à toujours utiliser une disposition de droite à gauche, quels que soient les paramètres de l’appareil, en modifiant les projets de plateforme respectifs.

### <a name="ios"></a>iOS

Les applications Xamarin. iOS peuvent être forcées à toujours utiliser une disposition de droite à gauche en modifiant la classe **AppDelegate** comme suit :

1. Déclarez la `IntPtr_objc_msgSend` fonction en tant que première ligne de votre `AppDelegate` classe :

   ```csharp
   [System.Runtime.InteropServices.DllImport(ObjCRuntime.Constants.ObjectiveCLibrary, EntryPoint = "objc_msgSend")]
   internal extern static IntPtr IntPtr_objc_msgSend(IntPtr receiver, IntPtr selector, UISemanticContentAttribute arg1);
   ```

1. Appelez la `IntPtr_objc_msgSend` fonction à partir de la `FinishedLaunching` méthode, avant de retourner à partir de la `FinshedLaunching` méthode :

   ```csharp
   bool result = base.FinishedLaunching(app, options);

   ObjCRuntime.Selector selector = new ObjCRuntime.Selector("setSemanticContentAttribute:");
   IntPtr_objc_msgSend(UIView.Appearance.Handle, selector.Handle, UISemanticContentAttribute.ForceRightToLeft);

   return result;
   ```

Cette approche est utile pour les applications qui requièrent toujours une disposition de droite à gauche et supprime la nécessité de définir la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriété.

Pour plus d’informations sur la `IntrPtr_objc_msgSend` méthode, consultez [sélecteurs objective-C dans Xamarin. iOS](~/ios/internals/objective-c-selectors.md).

### <a name="android"></a>Android

Les applications Xamarin. Android peuvent être forcées à toujours utiliser une disposition de droite à gauche en modifiant la classe **MainActivity** pour inclure la ligne suivante :

```csharp
Window.DecorView.LayoutDirection = LayoutDirection.Rtl;
```

Cette approche est utile pour les applications qui requièrent toujours une disposition de droite à gauche et supprime la nécessité de définir la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriété.

## <a name="right-to-left-language-support-with-xamarinuniversity"></a>Prise en charge linguistique de droite à gauche avec Xamarin.University

> [!VIDEO https://youtube.com/embed/f2lQ5yw3iiU]

**Xamarin.Formsvidéo de prise en charge de droite à gauche de 3,0**

## <a name="related-links"></a>Liens connexes

- [Exemple d’application TodoLocalizedRTL](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todolocalizedrtl)
