---
title: Ajout d’une mise en forme spécifique à iOS
description: Cet article explique comment définir l’apparence spécifique à iOS sans utiliser de Xamarin.Forms convertisseur personnalisé.
ms.prod: xamarin
ms.assetid: CE50E207-D092-4D88-8439-1B51F178E7ED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/29/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 062dd77f1dc7b82f16bfbc6d46e44d4adeb73350
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84564704"
---
# <a name="adding-ios-specific-formatting"></a>Ajout d’une mise en forme spécifique à iOS

Une façon de définir une mise en forme spécifique à iOS consiste à créer un [convertisseur personnalisé](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) pour un contrôle et à définir des couleurs et des styles spécifiques à la plateforme pour chaque plateforme.

D’autres options pour contrôler l' Xamarin.Forms apparence de votre application IOS sont les suivantes :

- Configuration des options d’affichage dans [ **info. plist**](#customizing-infoplist)
- Définition de styles de contrôle via l' [ `UIAppearance` API](#uiappearance-api)

Ces alternatives sont présentées ci-dessous.

## <a name="customizing-infoplist"></a>Personnalisation d’info. plist

Le fichier **info. plist** vous permet de configurer certains aspects du renderering d’une application iOS, par exemple comment (et si) la barre d’État est affichée.

Par exemple, l' [exemple todo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo) utilise le code suivant pour définir la couleur de la barre de navigation et la couleur du texte sur toutes les plateformes :

```csharp
var nav = new NavigationPage (new TodoListPage ());
nav.BarBackgroundColor = Color.FromHex("91CA47");
nav.BarTextColor = Color.White;
```

Le résultat est affiché dans l’extrait de code d’écran ci-dessous. Notez que les éléments de la barre d’État sont noirs (cela ne peut pas être défini dans Xamarin.Forms , car il s’agit d’une fonctionnalité spécifique à la plateforme).

![](theme-images/status-default-sml.png "iOS Theming")

Dans l’idéal, la barre d’État est également blanche, ce que nous pouvons effectuer directement dans le projet iOS. Ajoutez les entrées suivantes au fichier **info. plist** pour forcer la barre d’État à être blanche :

![](theme-images/info-plist.png "iOS Info.plist Entries")

ou modifiez le fichier **info. plist** directement pour inclure :

```xml
<key>UIStatusBarStyle</key>
<string>UIStatusBarStyleLightContent</string>
<key>UIViewControllerBasedStatusBarAppearance</key>
<false/>
```

Désormais, lorsque l’application est exécutée, la barre de navigation est verte et son texte est blanc (en raison de Xamarin.Forms la mise en forme) *et* le texte de la barre d’État est également blanc grâce à la configuration spécifique à IOS :

![](theme-images/status-white-sml.png "iOS Theming")

## <a name="uiappearance-api"></a>API UIAppearance

L' [ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) peut être utilisée pour définir des propriétés visuelles sur de nombreux contrôles iOS *sans* avoir à créer un [convertisseur personnalisé](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

L’ajout d’une seule ligne de code à la méthode **AppDelegate.cs** `FinishedLaunching` peut affecter un style à tous les contrôles d’un type donné à l’aide de leur `Appearance` propriété. Le code suivant contient deux exemples : styles globaux de la barre d’onglets et du contrôle de commutateur :

**AppDelegate.cs** dans le projet iOS

```csharp
public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
  // tab bar
    UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
  // switch
    UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
    // required Xamarin.Forms code
    Forms.Init ();
    LoadApplication (new App ());
    return base.FinishedLaunching (app, options);
}
```

### <a name="uitabbar"></a>UITabBar

Par défaut, l’icône de la barre d’onglets sélectionnée dans un[`TabbedPage`](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
serait bleu :

![](theme-images/tabbar-default.png "Default iOS Tab Bar Icon in TabbedPage")

Pour modifier ce comportement, définissez la `UITabBar.Appearance` propriété :

```csharp
UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

L’onglet sélectionné est alors vert :

![](theme-images/tabbar-custom.png "Green iOS Tab Bar Icon in TabbedPage")

L’utilisation de cette API vous permet de personnaliser l’apparence duXamarin.Forms
`TabbedPage`sur iOS avec très peu de code. Reportez-vous à la [recette personnaliser les onglets](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs) pour plus d’informations sur l’utilisation d’un convertisseur personnalisé pour définir une police spécifique pour l’onglet.

### <a name="uiswitch"></a>UISwitch

Le `Switch` contrôle est un autre exemple qui peut être facilement stylisé :

```csharp
UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

Ces deux captures d’écran affichent le contrôle par défaut `UISwitch` sur la gauche et la version personnalisée (paramètre `Appearance` ) à droite dans l' [exemple todo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo):

![](theme-images/switch-default.png "Couleur UISwitch par défaut") ![](theme-images/switch-custom.png "Couleur UISwitch personnalisée")

### <a name="other-controls"></a>Autres contrôles

De nombreux contrôles d’interface utilisateur iOS peuvent avoir leurs couleurs par défaut et d’autres attributs définis à l’aide de l' [ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md).

## <a name="related-links"></a>Liens connexes

- [UIAppearance](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)
- [Personnaliser les onglets](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs)
