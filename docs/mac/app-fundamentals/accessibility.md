---
title: Accessibilité sur macOS
description: Ce document décrit comment utiliser les fonctionnalités d’accessibilité macOS dans une application Xamarin. Mac. Il décrit la description des éléments d’interface utilisateur dans les storyboards et le code, les contrôles personnalisés et l’accessibilité des tests.
ms.prod: xamarin
ms.assetid: D7F4892B-501A-4271-A7E0-BDD1586B63AD
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: 087dcdc7024026e6a3ed3a05baca3b2648053cc8
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70769948"
---
# <a name="accessibility-on-macos"></a>Accessibilité sur macOS

Cette page explique comment utiliser les API d’accessibilité macOS pour créer des applications en fonction de la [liste de contrôle d’accessibilité](~/cross-platform/app-fundamentals/accessibility.md).
Reportez-vous aux pages accessibilité [Android](~/android/app-fundamentals/accessibility.md) et [accessibilité iOS](~/ios/app-fundamentals/accessibility.md) pour d’autres API de plateforme.

Pour comprendre le fonctionnement des API d’accessibilité dans macOS (anciennement appelé OS X), consultez tout d’abord le [modèle d’accessibilité OS x](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXmodel.html).

## <a name="describing-ui-elements"></a>Description des éléments d’interface utilisateur

AppKit utilise le protocole `NSAccessibility` pour exposer des API qui facilitent l’accès à l’interface utilisateur. Cela comprend un comportement par défaut qui tente de définir des valeurs significatives pour les propriétés d’accessibilité, telles que la définition de l' `AccessibilityLabel` d’un bouton. L’étiquette est généralement un mot unique ou une expression brève décrivant le contrôle ou la vue.

### <a name="storyboard-files"></a>Fichiers de Storyboard

Xamarin. Mac utilise le Interface Builder Xcode pour modifier les fichiers de Storyboard.
Vous pouvez modifier les informations d’accessibilité dans l' **inspecteur d’identité** lorsqu’un contrôle est sélectionné sur l’aire de conception (comme indiqué dans la capture d’écran ci-dessous) :

[![Ajout de l’accessibilité dans le Interface Builder de Xcode](accessibility-images/xcode.png "Ajout de l’accessibilité dans le Interface Builder de Xcode")](accessibility-images/xcode-large.png#lightbox)

### <a name="code"></a>Code

Xamarin. Mac n’expose actuellement pas comme `AccessibilityLabel` Setter.  Ajoutez la méthode d’assistance suivante pour définir l’étiquette d’accessibilité :

```csharp
public static class AccessibilityHelper
{
    [System.Runtime.InteropServices.DllImport (ObjCRuntime.Constants.ObjectiveCLibrary)]
    extern static void objc_msgSend (IntPtr handle, IntPtr selector, IntPtr label);

    static public void SetAccessibilityLabel (this NSView view, string value)
    {
        objc_msgSend (view.Handle, new ObjCRuntime.Selector ("setAccessibilityLabel:").Handle, new NSString (value).Handle);
    }
}
```

Cette méthode peut ensuite être utilisée dans le code, comme indiqué ci-dessous :

```csharp
AccessibilityHelper.SetAccessibilityLabel (someButton, "New Accessible Description");
```

La propriété `AccessibilityHelp` est une explication de ce que fait le contrôle ou la vue et doit être ajoutée uniquement lorsque l’étiquette peut ne pas fournir d’informations suffisantes. Le texte d’aide doit toujours être aussi petit que possible, par exemple « supprime le document ».

Certains éléments de l’interface utilisateur ne sont pas pertinents pour l’accès accessible (par exemple, une étiquette en regard d’une entrée ayant sa propre étiquette d’accessibilité et son aide).
Dans ces cas, définissez `AccessibilityElement = false` afin que ces contrôles ou vues soient ignorés par les lecteurs d’écran ou d’autres outils d’accessibilité.

Apple fournit des [instructions d’accessibilité](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/EnhancingtheAccessibilityofStandardAppKitControls.html) qui expliquent les meilleures pratiques pour les étiquettes d’accessibilité et le texte d’aide.

## <a name="custom-controls"></a>Contrôles personnalisés

Pour plus d’informations sur les étapes supplémentaires requises, reportez-vous aux instructions d’Apple [pour obtenir des contrôles personnalisés accessibles](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/ImplementingAccessibilityforCustomControls.html) .

## <a name="testing-accessibility"></a>Test de l’accessibilité

macOS fournit un **inspecteur d’accessibilité** qui permet de tester les fonctionnalités d’accessibilité. L’inspecteur est inclus dans Xcode.

La première fois qu’il est lancé, l' **inspecteur d’accessibilité** doit avoir l’autorisation de contrôler l’ordinateur via l’accessibilité :

![Inspecteur d’accessibilité demandant l’autorisation d’exécution](accessibility-images/accessibility-inspector-1.png "Inspecteur d’accessibilité demandant l’autorisation d’exécution")

Déverrouillez l’écran des paramètres (si nécessaire, en bas à gauche) et cochez l' **inspecteur d’accessibilité**:

![Écran Paramètres pour activer l’inspecteur d’accessibilité](accessibility-images/accessibility-inspector-2.png "Écran Paramètres pour activer l’inspecteur d’accessibilité")

Une fois activé, l’inspecteur s’affiche sous la forme d’une fenêtre flottante qui peut être déplacée à l’écran. La capture d’écran ci-dessous montre l’inspecteur en cours d’exécution à côté d’un exemple d’application Mac. Lorsque le curseur est déplacé au-dessus de la fenêtre, l’inspecteur affiche toutes les propriétés accessibles de chaque contrôle :

[![Exemple d’inspecteur d’accessibilité exécutant](accessibility-images/accessibility-example.png "Exemple d’inspecteur d’accessibilité exécutant")](accessibility-images/accessibility-example-large.png#lightbox)

Pour plus d’informations, consultez le [Guide test d’accessibilité pour OS X](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXTestingApps.html).

## <a name="related-links"></a>Liens associés

- [Accessibilité multiplateforme](~/cross-platform/app-fundamentals/accessibility.md)
- [Accessibilité Mac](https://www.apple.com/accessibility/mac/)
