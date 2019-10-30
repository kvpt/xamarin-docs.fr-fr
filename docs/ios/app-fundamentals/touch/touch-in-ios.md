---
title: Événements tactiles et mouvements dans Xamarin. iOS
description: Ce document explique comment utiliser des événements tactiles, des entrées tactiles tactiles, des gestes multiples et des mouvements personnalisés dans les applications Xamarin. iOS.
ms.prod: xamarin
ms.assetid: DA666DC9-446E-4CD1-B5A0-C6FFBC7E53AD
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 9dd06044ea9c4bf28e785932d316b3222c6cd16b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73009145"
---
# <a name="touch-events-and-gestures-in-xamarinios"></a>Événements tactiles et mouvements dans Xamarin. iOS

Il est important de comprendre les événements tactiles et les API tactiles dans une application iOS, car ils sont essentiels à toutes les interactions physiques avec l’appareil. Toutes les interactions tactiles impliquent un objet `UITouch`. Dans cet article, nous allons apprendre à utiliser la classe `UITouch` et ses API pour prendre en charge Touch. Plus tard, nous développerons notre connaissance pour apprendre à prendre en charge les gestes.

## <a name="enabling-touch"></a>Activation de Touch

Les contrôles de `UIKit` – ceux sous-classées à partir de Uicontrolextensions – sont tellement dépendants de l’interaction de l’utilisateur qu’ils ont des mouvements intégrés à UIKit. par conséquent, il n’est pas nécessaire d’activer Touch. Elle est déjà activée.

Toutefois, la plupart des vues de `UIKit` n’ont pas d’option tactile activée par défaut. Il existe deux façons d’activer la fonction tactile sur un contrôle. La première consiste à activer la case à cocher interaction utilisateur activée dans le panneau des propriétés du concepteur iOS, comme illustré dans la capture d’écran suivante :

 [![](touch-in-ios-images/image1.png "Check the User Interaction Enabled checkbox in the Property Pad of the iOS Designer")](touch-in-ios-images/image1.png#lightbox)

Nous pouvons également utiliser un contrôleur pour affecter à la propriété `UserInteractionEnabled` la valeur true sur une classe `UIView`. Cette condition est requise si l’interface utilisateur est créée dans le code.

La ligne de code suivante est un exemple :

```csharp
imgTouchMe.UserInteractionEnabled = true;
```

## <a name="touch-events"></a>Événements tactiles

Trois phases de saisie tactile se produisent lorsque l’utilisateur touche l’écran, déplace son doigt ou supprime son doigt. Ces méthodes sont définies dans `UIResponder`, qui est la classe de base pour UIView. iOS remplace les méthodes associées sur le `UIView` et le `UIViewController` pour gérer les fonctions tactiles :

- `TouchesBegan` : cette méthode est appelée lorsque l’écran est d’abord touché.
- `TouchesMoved` : cette méthode est appelée lorsque l’emplacement du toucher change au fur et à mesure que l’utilisateur fait glisser son doigt sur l’écran.
- `TouchesEnded` ou `TouchesCancelled` : `TouchesEnded` est appelé quand les doigts de l’utilisateur sont levés hors de l’écran.  `TouchesCancelled` est appelé si iOS annule la touche tactile, par exemple, si un utilisateur glisse son doigt en dehors d’un bouton pour annuler une pression.

Les événements tactiles se déplacent de manière récursive dans la pile de UIViews, pour vérifier si l’événement tactile se trouve dans les limites d’un objet de vue. On parle souvent _de test de positionnement_. Ils sont d’abord appelés au niveau le plus élevé `UIView` ou `UIViewController`, puis ils sont appelés sur le `UIView` et `UIViewControllers` situés sous ceux-ci dans la hiérarchie d’affichage.

Un objet `UITouch` est créé chaque fois que l’utilisateur touche l’écran. L’objet `UITouch` comprend des données sur le toucher, par exemple quand le toucher s’est produit, où il s’est produit, si le toucher était un balayage, etc. Les événements tactiles reçoivent une propriété Touch, `NSSet` contenant un ou plusieurs touches. Nous pouvons utiliser cette propriété pour obtenir une référence à une fonction tactile et déterminer la réponse de l’application.

Les classes qui substituent l’un des événements tactiles doivent d’abord appeler l’implémentation de base, puis récupérer l’objet `UITouch` associé à l’événement. Pour obtenir une référence à la première touche tactile, appelez la propriété `AnyObject` et effectuez un cast en `UITouch` comme indiqué dans l’exemple suivant :

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        //code here to handle touch
    }
}
```

iOS reconnaît automatiquement les touches rapides successives sur l’écran et les collecte dans un seul et même objet de `UITouch`. Cela facilite la vérification d’un double-clic aussi simple que la vérification de la propriété `TapCount`, comme illustré dans le code suivant :

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        if (touch.TapCount == 2)
        {
            // do something with the double touch.
        }
    }
}
```

## <a name="multi-touch"></a>Multipoint

L’interface multipoint n’est pas activée par défaut sur les contrôles. Vous pouvez activer multipoint dans le concepteur iOS, comme illustré dans la capture d’écran suivante :

 [![](touch-in-ios-images/image2.png "Multi-touch enabled in the iOS Designer")](touch-in-ios-images/image2.png#lightbox)

Il est également possible de définir des fonctions tactiles multiples par programme en définissant la propriété `MultipleTouchEnabled` comme indiqué dans la ligne de code suivante :

```csharp
imgTouchMe.MultipleTouchEnabled = true;
```

Pour déterminer le nombre de doigts touchés par l’écran, utilisez la propriété `Count` sur la propriété `UITouch` :

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    lblNumberOfFingers.Text = "Number of fingers: " + touches.Count.ToString();
}
```

## <a name="determining-touch-location"></a>Détermination de l’emplacement tactile

La méthode `UITouch.LocationInView` retourne un objet CGPoint qui contient les coordonnées de la pression tactile dans une vue donnée. En outre, nous pouvons vérifier si cet emplacement se trouve dans un contrôle en appelant la méthode `Frame.Contains`. L’extrait de code suivant en montre un exemple :

```csharp
if (this.imgTouchMe.Frame.Contains (touch.LocationInView (this.View)))
{
    // the touch event happened inside the UIView imgTouchMe.
}
```

Maintenant que nous avons une compréhension des événements tactiles dans iOS, voyons les informations relatives aux détecteurs de mouvement.

## <a name="gesture-recognizers"></a>Détecteurs de mouvement

Les détecteurs de mouvement peuvent simplifier et réduire l’effort de programmation pour prendre en charge la saisie tactile dans une application. les détecteurs de mouvements iOS agrègent une série d’événements tactiles en un seul événement tactile.

Xamarin. iOS fournit la classe `UIGestureRecognizer` comme classe de base pour les détecteurs de mouvement intégrés suivants :

- *UITapGestureRecognizer* : il s’agit d’un ou plusieurs robinets.
- *UIPinchGestureRecognizer* : pincement et étalement des doigts.
- *UIPanGestureRecognizer* – panoramique ou glissement.
- *UISwipeGestureRecognizer* : balayer dans n’importe quelle direction.
- *UIRotationGestureRecognizer* : rotation de deux doigts dans le sens des aiguilles d’une montre ou dans le sens des aiguilles d’une montre.
- *UILongPressGestureRecognizer* : maintenez la touche enfoncée, parfois appelée longue pression ou long-clic.

Le modèle de base pour l’utilisation d’un module de reconnaissance de mouvement est le suivant :

1. **Instancier la reconnaissance de mouvement** : tout d’abord, instanciez une sous-classe `UIGestureRecognizer`. L’objet qui est instancié sera associé à une vue et sera récupéré par le garbage collector quand la vue sera supprimée. Il n’est pas nécessaire de créer cette vue en tant que variable au niveau de la classe.
1. **Configurer des paramètres de mouvement** : l’étape suivante consiste à configurer le module de reconnaissance de mouvement. Consultez la documentation de Xamarin sur `UIGestureRecognizer` et ses sous-classes pour obtenir la liste des propriétés qui peuvent être définies pour contrôler le comportement d’une instance `UIGestureRecognizer`.
1. **Configurer la cible** – en raison de son patrimoine objective-C, Xamarin. iOS ne déclenche pas d’événements lorsqu’un module de reconnaissance de mouvement correspond à un geste.  `UIGestureRecognizer` a une méthode (`AddTarget`) qui peut accepter un délégué anonyme ou un sélecteur objective-C avec le code à exécuter lorsque le module de reconnaissance de mouvement fait une correspondance.
1. **Activer** le module de reconnaissance de mouvement : comme avec les événements tactiles, les mouvements sont uniquement reconnus si les interactions tactiles sont activées.
1. **Ajouter le module de reconnaissance de mouvement à la vue** : la dernière étape consiste à ajouter le geste à une vue en appelant `View.AddGestureRecognizer` et en lui passant un objet de module de reconnaissance de mouvement.

Reportez-vous aux exemples de module de [reconnaissance de mouvement](~/ios/app-fundamentals/touch/ios-touch-walkthrough.md#Gesture_Recognizer_Samples) pour plus d’informations sur la façon de les implémenter dans le code.

Lorsque la cible du mouvement est appelée, une référence au mouvement qui s’est produit lui est passée. Cela permet à la cible de mouvement d’obtenir des informations sur le mouvement qui s’est produit. L’étendue des informations disponibles dépend du type de module de reconnaissance de mouvement utilisé. Pour plus d’informations sur les données disponibles pour chaque sous-classe `UIGestureRecognizer`, consultez la documentation de Xamarin.

Il est important de se souvenir qu’une fois qu’un module de reconnaissance de mouvement a été ajouté à une vue, la vue (et les vues situées au-dessous) ne recevront aucun événement tactile. Pour autoriser les événements tactiles simultanément avec les gestes, la propriété `CancelsTouchesInView` doit avoir la valeur false, comme l’illustre le code suivant :

```csharp
_tapGesture.Recognizer.CancelsTouchesInView = false;
```

Chaque `UIGestureRecognizer` a une propriété d’État qui fournit des informations importantes sur l’état du module de reconnaissance de mouvement. Chaque fois que la valeur de cette propriété change, iOS appellera la méthode d’abonnement en lui donnant une mise à jour. Si un module de reconnaissance de mouvement personnalisé ne met jamais à jour la propriété d’État, l’abonné n’est jamais appelé, ce qui rend le module de reconnaissance de mouvement inutile.

Les gestes peuvent être résumés comme l’un des deux types suivants :

1. *Discret* : ces mouvements se déclenchent uniquement la première fois qu’ils sont reconnus.
1. *Continu* : ces mouvements continuent à se déclencher tant qu’ils sont reconnus.

Les détecteurs de mouvement existent dans l’un des États suivants :

- *Possible* : il s’agit de l’état initial de tous les module de reconnaissance de mouvement. Il s’agit de la valeur par défaut de la propriété State.
- *Commencée* : lorsqu’un mouvement continu est reconnu pour la première fois, l’État est défini sur commencé. Cela permet aux abonnés de faire la différence entre le moment où la reconnaissance de mouvement démarre et celui où elle est modifiée.
- *Modifié* : après qu’un mouvement continu a commencé, mais n’est pas terminé, l’État est défini sur modifié chaque fois qu’une pression tactile se déplace ou change, tant qu’elle est toujours dans les paramètres attendus du mouvement.
- *Annulé* : cet État est défini si le module de reconnaissance a commencé à être modifié, et que la touche a été modifiée de telle façon qu’il ne s’adapte plus au modèle du mouvement.
- *Reconnu* : l’État est défini lorsque le module de reconnaissance de mouvement correspond à un ensemble de touches et informe l’abonné que le mouvement est terminé.
- *Terminé* : il s’agit d’un alias pour l’État reconnu.
- *Échec* : lorsque le module de reconnaissance de mouvement ne peut plus correspondre aux touches qu’il écoute, l’état passe à échec.

Xamarin. iOS représente ces valeurs dans l’énumération `UIGestureRecognizerState`.

## <a name="working-with-multiple-gestures"></a>Utilisation de mouvements multiples

Par défaut, iOS n’autorise pas l’exécution simultanée des gestes par défaut. Au lieu de cela, chaque module de reconnaissance de mouvement recevra des événements tactiles dans un ordre non déterministe. L’extrait de code suivant illustre comment faire exécuter un module de reconnaissance de mouvement simultanément :

```csharp
gesture.ShouldRecognizeSimultaneously += (UIGestureRecognizer r) => { return true; };
```

Il est également possible de désactiver un mouvement dans iOS. Il existe deux propriétés de délégué qui permettent à un module de reconnaissance de mouvement d’examiner l’état d’une application et les événements tactiles actuels, afin de prendre des décisions sur la façon dont et si un mouvement doit être reconnu. Les deux événements sont les suivants :

1. *ShouldReceiveTouch* : ce délégué est appelé juste avant que le module de reconnaissance de mouvement soit passé à un événement tactile, et offre la possibilité d’examiner les touches et de décider quels sont les contacts qui seront gérés par le module de reconnaissance de mouvement.
1. *ShouldBegin* : cette méthode est appelée lorsqu’un module de reconnaissance tente de changer l’état d’un État à un autre. Si la valeur false est renvoyée, l’état du module de reconnaissance de mouvement sera remplacé par échec.

Vous pouvez substituer ces méthodes avec un `UIGestureRecognizerDelegate`fortement typé, un délégué faible ou une liaison via la syntaxe du gestionnaire d’événements, comme l’illustre l’extrait de code suivant :

```csharp
gesture.ShouldReceiveTouch += (UIGestureRecognizer r, UITouch t) => { return true; };
```

Enfin, il est possible de faire en sorte qu’un module de reconnaissance de mouvement soit mis en file d’attente pour qu’il réussisse uniquement en cas d’échec d’un autre module de reconnaissance de mouvement. Par exemple, un seul module de reconnaissance de mouvement TAP ne doit se faire qu’en cas d’échec du module de reconnaissance de mouvement double-tap. L’extrait de code suivant en fournit un exemple :

```csharp
singleTapGesture.RequireGestureRecognizerToFail(doubleTapGesture);
```

## <a name="creating-a-custom-gesture"></a>Création d’un mouvement personnalisé

Bien qu’iOS fournisse des détecteurs de mouvement par défaut, il peut être nécessaire de créer des détecteurs de mouvement personnalisés dans certains cas. La création d’un module de reconnaissance de mouvement personnalisé implique les étapes suivantes :

1. Sous-classe `UIGestureRecognizer`.
1. Substituez les méthodes d’événement tactile appropriées.
1. Propagez l’état de reconnaissance à l’aide de la propriété état de la classe de base.

Un exemple concret est abordé dans la procédure pas à pas d' [utilisation de Touch dans iOS](ios-touch-walkthrough.md) .
