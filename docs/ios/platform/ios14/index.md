---
title: Présentation d’iOS 14
description: Ce document fournit une description de haut niveau de certaines API iOS 14 pour lesquelles Xamarin fournit des liaisons C#.
ms.prod: xamarin
ms.assetid: 4953216e-472b-4484-9c1e-7263ac537f21
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/17/2020
ms.openlocfilehash: e9793617c76813fb68a57213edd8b48529f19ac7
ms.sourcegitcommit: 0c45e3f810947e3d43223aa01bf3e43a0defca65
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90843572"
---
# <a name="introduction-to-ios-14"></a>Présentation d’iOS 14

Suivez ces [instructions](~/ios/platform/ios14/get-started.md) pour commencer.

## <a name="new-control-uicolorwell"></a>Nouveau contrôle : UIColorWell

[`UIColorWell`](https://developer.apple.com/documentation/uikit/uicolorwell) est un nouveau contrôle UIKit permettant de sélectionner des couleurs dans une sélection d’échantillons, à l’aide d’un injecteur ou en entrant des valeurs manuellement. Le contrôle affiche un bouton de couleur circulaire qui lance un formulaire modal lorsqu’il est taraudé.

![UIColorWell](ios14-images/colorwell.png)

```xaml
<ios:UIColorWell
    SelectedColor="{x:Static ios:UIColor.Red}"
    ValueChanged="OnColorChanged" />
```

```csharp
private void OnColorChanged(object sender, EventArgs e)
{
    var colorWell = (UIColorWell)sender; 
    Debug.WriteLine(colorWell.SelectedColor);
}
```

## <a name="modified-controls"></a>Contrôles modifiés

Plusieurs contrôles ont reçu des mises à jour, notamment :

- [UIBarButtonItem](https://developer.apple.com/documentation/uikit/uibarbuttonitem) peut maintenant ajouter un UIMenu qui sera affiché en tant que menu segue.
- [UIDatePicker](https://developer.apple.com/documentation/uikit/uidatepicker) prend désormais en charge plusieurs styles : automatique (par défaut), compact, inline et roue.
- [UISplitViewController](https://developer.apple.com/documentation/uikit/uisplitviewcontroller) prend désormais en charge trois colonnes : principal, secondaire et supplémentaire.
 
![API de la version préliminaire](~/media/shared/preview.png)

## <a name="embedded-widgetkit-support"></a>Support WidgetKit incorporé

Cette version du kit de développement logiciel (SDK) ajoute la prise en charge de l’incorporation d’extensions WidgetKit écrites en SWIFT dans votre application Xamarin. iOS principale. Cela vous permet de créer des applications avec prise en charge de widget dès aujourd’hui.

Avec cette méthode, vous créez une application « hybride », en générant votre extension de widget avec SwiftUI et en l’incorporant dans une application Xamarin. iOS.

L’utilisation de la prise en charge de WidgetKit nécessite quelques modifications manuelles apportées à votre fichier projet.

Ajoutez une section comme celle-ci à votre projet :

```xml
<AdditionalAppExtensions Include="$(MSBuildProjectDirectory)/../../native">
     <Name>NativeTodayExtension</Name>
     <BuildOutput Condition="'$(Platform)' == 'iPhone'">build/Debug-iphoneos</BuildOutput>
     <BuildOutput Condition="'$(Platform)' == 'iPhoneSimulator'">build/Debug-iphonesimulator</BuildOutput>
</AdditionalAppExtensions>
```

Modifiez le chemin d’accès inclus sur le premier lien pour qu’il pointe vers le répertoire de build de votre extension d’interface utilisateur SWIFT.

Il peut être utile d’activer un emplacement de sortie relatif à un projet dans votre projet XCode (fichier → paramètres du projet) pour obtenir un chemin d’accès plus simple :

![Paramètres Xcode](ios14-images/xcode-settings.png)

Cet [exemple d’application](https://github.com/chamons/xamarin-ios-swift-extension/blob/master/App/TestApplication/TestApplication.csproj#L143) utilise la sérialisation JSON pour transférer des données à partir d’une application Xamarin. iOS vers un widget d’exemple à des fins d’affichage.

Ceux qui s’intéressent à WidgetKit sont invités à fournir leurs [commentaires ici](https://github.com/xamarin/xamarin-macios/issues/8933).

## <a name="related-links"></a>Liens connexes

- [Notes de publication de Xamarin. iOS 14](/xamarin/ios/release-notes/14/14.0)
- [Documentation UIColorWell](https://developer.apple.com/documentation/uikit/uicolorwell)
