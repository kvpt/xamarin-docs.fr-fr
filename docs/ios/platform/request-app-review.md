---
title: Demander une révision de l’application dans Xamarin. iOS
description: Cet article décrit la méthode RequestReview ajoutée par Apple à iOS 10 et explique comment l’implémenter dans Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 6408e707-b7dc-4557-b931-16a4d79b8930
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 0522e6b1bcf3e0f927a403c12a9c27bb6b3bb3ff
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031602"
---
# <a name="request-app-review-in-xamarinios"></a>Demander une révision de l’application dans Xamarin. iOS

_Cet article décrit la méthode RequestReview qu’Apple a ajoutée à iOS 10 et comment l’implémenter dans Xamarin. iOS._

Nouveauté d’iOS 10,3, la méthode `RequestReview()` permet à une application iOS de demander à l’utilisateur de la noter ou de la passer en revue. Lorsque cette méthode est appelée dans une application d’expédition que l’utilisateur a installée à partir de l’App Store, iOS 10 gère l’intégralité du processus d’évaluation et de révision pour le développeur. Étant donné que ce processus est régi par la stratégie App Store, une alerte peut être affichée ou non.

![](request-app-review-images/review01.png "A sample Review Request alert")

## <a name="requesting-a-rating-or-review"></a>Demande d’évaluation ou révision

Tandis que la méthode statique `RequestReview()` de la classe `SKStoreReviewController` peut être appelée à tout moment où il est judicieux de l’expérience utilisateur, le processus de révision est régi et géré par la stratégie App Store. Par conséquent, cette méthode peut ou ne peut pas afficher une alerte et ne doit jamais être appelée en réponse à une action de l’utilisateur, par exemple en appuyant sur un bouton.

Par exemple, une application peut demander une révision après qu’elle a été lancée un certain nombre de fois, ou un jeu peut demander une révision une fois que le joueur a terminé un niveau.

Pour demander une révision dès qu’une application Xamarin. iOS a terminé son lancement, apportez les modifications suivantes au fichier `AppDelegate.cs` :

```csharp
using Foundation;
using StoreKit;
using UIKit;

namespace iOSTenThree
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        ...

        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Request a review from the user
            SKStoreReviewController.RequestReview ();

            return true;
        }

        ...

    }
}
```

> [!NOTE]
> L’appel de `RequestReview()` dans une application de sous-développement affiche toujours la boîte de dialogue évaluation et examen pour qu’elle puisse être testée. Cela ne s’applique pas aux applications qui ont été distribuées via TestFlight, où l’appel de méthode sera ignoré.

Lorsque la méthode `RequestReview()` est appelée dans une application d’expédition que l’utilisateur a installée à partir de l’App Store, iOS 10 gère l’intégralité du processus d’évaluation et de révision pour le développeur. Là encore, étant donné que ce processus est régi par la stratégie App Store, une alerte peut être affichée ou non.

## <a name="linking-to-an-app-store-product-page"></a>Lien vers une page de produit App Store 

En plus de la nouvelle méthode de `RequestReview`, le développeur peut toujours fournir un lien détaillé vers la page de produit de l’application dans l’App Store à partir d’une application. En ajoutant `action=write-review` à la fin de l’URL de la page de produit, une page s’ouvre pour permettre à l’utilisateur d’écrire une revue de l’application automatiquement. 

## <a name="summary"></a>Récapitulatif

Cet article a abordé la méthode RequestReview qu’Apple a ajoutée à iOS 10 et comment l’implémenter dans Xamarin. iOS.

## <a name="related-links"></a>Liens associés

- [Exemple iOSTenThree](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-iostenthree/)
