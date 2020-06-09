---
title: Fournir des commentaires haptique dans Xamarin. iOS
description: Ce document décrit comment fournir des commentaires haptique dans une application Xamarin. iOS. Il aborde UIImpactFeedbackGenerator, UINotificationFeedbackGenerator et UISelectionFeedbackGenerator.
ms.prod: xamarin
ms.assetid: 888106D1-58F4-453F-BACC-91D51FA39C80
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 86c7c726465257d71b6b407686153650dddbcbca
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574468"
---
# <a name="providing-haptic-feedback-in-xamarinios"></a>Fournir des commentaires haptique dans Xamarin. iOS

<a name="Overview"></a>

## <a name="overview"></a>Vue d’ensemble

Sur les iPhone 7 et iPhone 7 plus, Apple a inclus de nouvelles réponses haptique qui offrent des moyens supplémentaires pour l’engagement physique de l’utilisateur. Les commentaires haptique (souvent appelés « haptiques ») utilisent le sens du toucher (par force, vibration ou motion) dans la conception de l’interface utilisateur. Utilisez ces nouvelles options de commentaires tactiles pour attirer l’attention de l’utilisateur et renforcer ses actions.

Les rubriques suivantes sont traitées en détail :

- [À propos des commentaires haptique](#About-Haptic-Feedback)
- [UIImpactFeedbackGenerator](#UIImpactFeedbackGenerator)
- [UINotificationFeedbackGenerator](#UINotificationFeedbackGenerator)
- [UISelectionFeedbackGenerator](#UISelectionFeedbackGenerator)

<a name="About-Haptic-Feedback"></a>

## <a name="about-haptic-feedback"></a>À propos des commentaires haptique

Plusieurs éléments d’interface utilisateur intégrés fournissent déjà des commentaires haptique tels que des sélecteurs, des commutateurs et des curseurs. iOS 10 ajoute désormais la possibilité de déclencher par programmation des haptique à l’aide d’une sous-classe concrète de la `UIFeedbackGenerator` classe.

Le développeur peut utiliser l’une des `UIFeedbackGenerator` sous-classes suivantes pour déclencher par programmation des commentaires haptique :

- `UIImpactFeedbackGenerator`-Utilisez ce générateur de commentaires pour compléter une action ou une tâche telle que la présentation d’un « Thud » lorsqu’une vue est en place ou si deux objets à l’écran sont en conflit.
- `UINotificationFeedbackGenerator`-Utilisez ce générateur de commentaires pour les notifications telles qu’une action qui se termine, échoue ou tout autre type d’avertissement.
- `UISelectionFeedbackGenerator`-Utilisez ce générateur de commentaires pour une sélection qui change activement, par exemple le choix d’un élément dans une liste.

<a name="UIImpactFeedbackGenerator"></a>

### <a name="uiimpactfeedbackgenerator"></a>UIImpactFeedbackGenerator

Utilisez ce générateur de commentaires pour compléter une action ou une tâche telle que la présentation d’un « Thud » lorsqu’une vue est en place ou si deux objets à l’écran sont en conflit.

Utilisez le code suivant pour déclencher des commentaires sur l’impact :

```csharp
using UIKit;
...

// Initialize feedback
var impact = new UIImpactFeedbackGenerator (UIImpactFeedbackStyle.Heavy);
impact.Prepare ();

// Trigger feedback
impact.ImpactOccurred ();
```

Lorsque le développeur crée une nouvelle instance de la `UIImpactFeedbackGenerator` classe, il fournit une `UIImpactFeedbackStyle` spécification de la force des commentaires comme suit :

- `Heavy`
- `Medium`
- `Light`

La `Prepare` méthode de `UIImpactFeedbackGenerator` est appelée pour informer le système que les commentaires haptique sont sur le lieu de se produire pour réduire la latence.

La `ImpactOccurred` méthode déclenche ensuite des commentaires haptique.

<a name="UINotificationFeedbackGenerator"></a>

### <a name="uinotificationfeedbackgenerator"></a>UINotificationFeedbackGenerator

Utilisez ce générateur de commentaires pour les notifications telles qu’une action qui se termine, échoue ou tout autre type d’avertissement.

Utilisez le code suivant pour déclencher des commentaires sur les notifications :

```csharp
using UIKit;
...

// Initialize feedback
var notification = new UINotificationFeedbackGenerator ();
notification.Prepare ();

// Trigger feedback
notification.NotificationOccurred (UINotificationFeedbackType.Error);
```

Une nouvelle instance de la `UINotificationFeedbackGenerator` classe est créée et sa `Prepare` méthode est appelée pour informer le système que les commentaires haptique sont sur le lieu de se produire pour réduire la latence.

La `NotificationOccurred` méthode est appelée pour déclencher un retour haptique avec un donné `UINotificationFeedbackType` de :

- `Success`
- `Warning`
- `Error`

<a name="UISelectionFeedbackGenerator"></a>

### <a name="uiselectionfeedbackgenerator"></a>UISelectionFeedbackGenerator

Utilisez ce générateur de commentaires pour une sélection qui change activement, par exemple le choix d’un élément dans une liste.

Utilisez le code suivant pour déclencher les commentaires de sélection :

```csharp
using UIKit;
...

// Initialize feedback
var selection = new UISelectionFeedbackGenerator ();
selection.Prepare ();

// Trigger feedback
selection.SelectionChanged ();
```

Une nouvelle instance de la `UISelectionFeedbackGenerator` classe est créée et sa `Prepare` méthode est appelée pour informer le système que les commentaires haptique sont sur le lieu de se produire pour réduire la latence.

La `SelectionChanged` méthode déclenche ensuite des commentaires haptique.

## <a name="summary"></a>Résumé

Cet article a abordé les nouveaux types de commentaires haptique disponibles dans iOS 10 et comment les implémenter dans Xamarin. iOS.

## <a name="related-links"></a>Liens connexes

- [Exemples iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
