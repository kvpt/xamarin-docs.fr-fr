---
title: Demander une révision de l’application dans Xamarin. iOS
description: Cet article décrit la méthode RequestReview ajoutée par Apple à iOS 10 et explique comment l’implémenter dans Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 6408e707-b7dc-4557-b931-16a4d79b8930
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: b51eeb3594f1b41f8b8f7fdaedc7577138c4f5ba
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435669"
---
# <a name="request-app-review-in-xamarinios"></a>Demander une révision de l’application dans Xamarin. iOS

_Cet article décrit la méthode RequestReview qu’Apple a ajoutée à iOS 10 et comment l’implémenter dans Xamarin. iOS._

Nouveauté d’iOS 10,3, la `RequestReview()` méthode permet à une application iOS de demander à l’utilisateur de la noter ou de la passer en revue. Lorsque cette méthode est appelée dans une application d’expédition que l’utilisateur a installée à partir de l’App Store, iOS 10 gère l’intégralité du processus d’évaluation et de révision pour le développeur. Étant donné que ce processus est régi par la stratégie App Store, une alerte peut être affichée ou non.

![Exemple d’alerte de demande de révision](request-app-review-images/review01.png)

## <a name="requesting-a-rating-or-review"></a>Demande d’évaluation ou révision

Alors que la `RequestReview()` méthode statique de la `SKStoreReviewController` classe peut être appelée à tout moment dans l’expérience utilisateur, le processus de révision est régi et géré par la stratégie App Store. Par conséquent, cette méthode peut ou ne peut pas afficher une alerte et ne doit jamais être appelée en réponse à une action de l’utilisateur, par exemple en appuyant sur un bouton.

Par exemple, une application peut demander une révision après qu’elle a été lancée un certain nombre de fois, ou un jeu peut demander une révision une fois que le joueur a terminé un niveau.

Pour demander une révision dès qu’une application Xamarin. iOS a terminé son lancement, apportez les modifications suivantes au `AppDelegate.cs` fichier :

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
> `RequestReview()`L’appel de dans une application de sous-développement affiche toujours la boîte de dialogue évaluation et examen pour qu’elle puisse être testée. Cela ne s’applique pas aux applications qui ont été distribuées via TestFlight, où l’appel de méthode sera ignoré.

Lorsque la `RequestReview()` méthode est appelée dans une application d’expédition que l’utilisateur a installée à partir de l’App Store, iOS 10 gère l’intégralité du processus d’évaluation et de révision pour le développeur. Là encore, étant donné que ce processus est régi par la stratégie App Store, une alerte peut être affichée ou non.

## <a name="linking-to-an-app-store-product-page"></a>Lien vers une page de produit App Store 

En plus de la nouvelle `RequestReview` méthode, le développeur peut toujours fournir un lien profond vers la page de produit de l’application dans l’App Store à partir d’une application. En ajoutant `action=write-review` à la fin de l’URL de la page de produit, une page s’ouvre pour permettre à l’utilisateur d’écrire une revue de l’application automatiquement. 

## <a name="summary"></a>Résumé

Cet article a abordé la méthode RequestReview qu’Apple a ajoutée à iOS 10 et comment l’implémenter dans Xamarin. iOS.

## <a name="related-links"></a>Liens associés

- [Exemple iOSTenThree](/samples/xamarin/ios-samples/ios10-iostenthree/)