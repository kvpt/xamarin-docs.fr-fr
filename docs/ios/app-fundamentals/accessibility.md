---
title: Accessibilité sur iOS
description: Ce document décrit l’accessibilité dans iOS, en discutant des diverses propriétés et fonctionnalités qui peuvent être utilisées pour rendre votre application utilisable par autant d’utilisateurs que possible.
ms.prod: xamarin
ms.assetid: 88D59B36-05A3-4356-AE29-EC2B69CE7162
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/18/2016
ms.openlocfilehash: 2259566fc6342a40a8c0a94bacd1c146b6509d52
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574156"
---
# <a name="accessibility-on-ios"></a>Accessibilité sur iOS

Cette page explique comment utiliser les API d’accessibilité iOS pour créer des applications en fonction de la [liste de contrôle d’accessibilité](~/cross-platform/app-fundamentals/accessibility.md).
Reportez-vous aux pages d’accessibilité [Android Accessibility](~/android/app-fundamentals/accessibility.md) et [OS X](~/mac/app-fundamentals/accessibility.md) pour d’autres API de plateforme.

## <a name="describing-ui-elements"></a>Description des éléments d’interface utilisateur

iOS fournit aux `AccessibilityLabel` développeurs les propriétés et pour `AccessibilityHint` Ajouter du texte descriptif qui peut être utilisé par le lecteur d’écran VoiceOver pour rendre les contrôles plus accessibles. Les contrôles peuvent également être marqués avec une ou plusieurs caractéristiques qui fournissent un contexte supplémentaire dans les modes accessibles.

Certains contrôles peuvent ne pas avoir besoin d’être accessibles (par exemple, une étiquette sur une entrée de texte ou une image qui est purement décorative) `IsAccessibilityElement` . le est fourni pour désactiver l’accessibilité dans ces cas-là.

**Concepteur d’interface utilisateur**

L' **panneau Propriétés** contient une section d’accessibilité qui permet de modifier ces paramètres lorsqu’un contrôle est sélectionné dans le concepteur d’interface utilisateur iOS :

![](accessibility-images/ios-designer-sml.png "Accessibility Settings")

**C#**

Ces propriétés peuvent également être définies directement dans le code :

```csharp
usernameInput.AccessibilityLabel = "Search";
usernameInput.Hint = "Press Enter after typing to search employee list";
someLabel.IsAccessibilityElement = false;
displayOnlyText.AccessibilityTraits = UIAccessibilityTrait.Header | UIAccessibilityTrait.Selected;
```

### <a name="what-is-accessibilityidentifier"></a>Qu’est-ce que AccessibilityIdentifier ?

`AccessibilityIdentifier`Est utilisé pour définir une clé unique qui peut être utilisée pour faire référence à des éléments de l’interface utilisateur via l’API uiautomation.

La valeur de `AccessibilityIdentifier` n’est jamais parlée ou présentée à l’utilisateur.

<a name="postnotification"></a>

## <a name="postnotification"></a>PostNotification

La `UIAccessibility.PostNotification` méthode permet aux événements d’être déclenchés à l’utilisateur en dehors de l’interaction directe (par exemple, lorsqu’ils interagissent avec un contrôle spécifique).

### <a name="announcement"></a>Annonce

Une annonce peut être envoyée à partir du code pour informer l’utilisateur que certains États ont changé (par exemple, une opération d’arrière-plan est terminée). Cela peut être accompagné d’une indication visuelle dans l’interface utilisateur :

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.Announcement,
    new NSString(@"Item was saved"));
```

### <a name="layoutchanged"></a>LayoutChanged

L' `LayoutChanged` annonce est utilisée lorsque la disposition de l’écran :

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.LayoutChanged,
    someControl);  // someControl gets focus
```

## <a name="accessibility-and-localization"></a>Accessibilité et localisation

Les propriétés d’accessibilité telles que l’étiquette et l’indicateur peuvent être localisées comme tout autre texte dans l’interface utilisateur.

**Fichier mainstoryboard. Strings**

Si l’interface utilisateur est présentée dans une table de montage séquentiel, vous pouvez fournir des traductions pour les propriétés d’accessibilité de la même façon que les autres propriétés. Dans l’exemple ci-dessous, un `UITextField` a un **ID de localisation** de `Pqa-aa-ury` et deux propriétés d’accessibilité sont définies en espagnol :

```csharp
/* Accessibility */
"Pqa-aa-ury.accessibilityLabel" = "Notas input";
"Pqa-aa-ury.accessibilityHint" = "escriba más información";
```

Ce fichier est placé dans le répertoire **es. lproj** pour le contenu espagnol.

**Localisable. Strings**

Vous pouvez également ajouter les traductions au fichier **localisable. Strings** dans le répertoire de contenu localisé (par exemple, **es. lproj** pour l’espagnol) :

```csharp
/* Accessibility */
"Notes" = "Notas input";
"Provide more information" = "escriba más información";
```

Ces traductions peuvent être utilisées en C# par le biais de la `LocalizedString` méthode :

```csharp
notesText.AccessibilityLabel = NSBundle.MainBundle.LocalizedString ("Notes", "");
notesText.AccessibilityHint = NSBundle.MainBundle.LocalizedString ("Provide more information", "");
```

Reportez-vous au [Guide de localisation iOS](~/ios/app-fundamentals/localization/index.md) pour plus d’informations sur la localisation du contenu.

<a name="testing"></a>

## <a name="testing-accessibility"></a>Test d’accessibilité

VoiceOver est activé dans l’application **paramètres** en accédant à **général > accessibilité > VoiceOver**:

![](accessibility-images/settings-sml.png "Setting the speaking rate")

L’écran **accessibilité** fournit également des paramètres pour les options zoom, taille du texte, couleur & contraste, paramètres vocaux et autres options de configuration.

Suivez ces [instructions VoiceOver](https://developer.apple.com/library/ios/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html) pour tester l’accessibilité sur des appareils iOS.

## <a name="simulator-testing"></a>Test du simulateur

Lors du test dans le simulateur, l' **inspecteur d’accessibilité** est disponible pour vous aider à vérifier que les propriétés et les événements d’accessibilité sont correctement configurés. Activez l’inspecteur dans l’application **paramètres** en accédant à **général > accessibilité > l’inspecteur d’accessibilité**:

![](accessibility-images/settings-inspector-sml.png "Enable Accessibility Inspector")

Une fois activée, la fenêtre de l’inspecteur passe à tout moment sur l’écran iOS.
Voici un exemple de sortie lorsqu’une ligne de la vue table est sélectionnée : Notez que l' **étiquette** contient une phrase qui donne le contenu de la ligne et qu’elle est « done » (c’est-à-dire que le battement est visible) :

![](accessibility-images/tableview-a11y-sml.png "Using Accessibility Inspector")

Lorsque l’inspecteur est visible, utilisez l’icône « X » en haut à gauche pour afficher et masquer temporairement la superposition et activer/désactiver les paramètres d’accessibilité.

## <a name="related-links"></a>Liens connexes

- [Accessibilité multiplateforme](~/cross-platform/app-fundamentals/accessibility.md)
- [Accessibilité iOS (Apple)](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/iPhoneAccessibility/Accessibility_on_iPhone/Accessibility_on_iPhone.html)
- [VoiceOver iOS](https://www.apple.com/accessibility/ios/voiceover/)
