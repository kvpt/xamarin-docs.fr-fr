---
ms.topic: include
author: jamesmontemagno
ms.author: jamont
ms.date: 05/11/2020
ms.openlocfilehash: b5396061de657690fa3f4cdf68e8a94382918613
ms.sourcegitcommit: 83cf2a4d99546751c6394510a463a2b2a8bf75b8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83149731"
---
Cette API utilise des autorisations d’exécution sur Android. Vérifiez que Xamarin. Essentials est entièrement initialisé et que la gestion des autorisations est configurée dans votre application.

Dans `MainLauncher` pour le projet Android ou n’importe quel `Activity` qui est lancé Xamarin.Essentials doit être initialisé dans la méthode `OnCreate` :

```csharp
protected override void OnCreate(Bundle savedInstanceState) 
{
    //...
    base.OnCreate(savedInstanceState);
    Xamarin.Essentials.Platform.Init(this, savedInstanceState); // add this line to your code, it may also be called: bundle
    //...
}    
```

Pour gérer les autorisations d’exécution sur Android, Xamarin.Essentials doit recevoir `OnRequestPermissionsResult`. Ajoutez le code suivant à toutes les classes `Activity` :

```csharp
public override void OnRequestPermissionsResult(int requestCode, string[] permissions, Android.Content.PM.Permission[] grantResults)
{
    Xamarin.Essentials.Platform.OnRequestPermissionsResult(requestCode, permissions, grantResults);

    base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
}
```
