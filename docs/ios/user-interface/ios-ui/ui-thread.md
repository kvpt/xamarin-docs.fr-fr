---
title: Utilisation du thread d’interface utilisateur dans Xamarin. iOS
description: Ce document décrit comment utiliser le thread d’interface utilisateur dans Xamarin. iOS. Il traite de l’exécution des threads d’interface utilisateur, fournit un exemple de thread d’arrière-plan et examine Async/await.
ms.prod: xamarin
ms.assetid: 98762ACA-AD5A-4E1E-A536-7AF3BE36D77E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: e91b7fdf99e8eb69cca240253f169ba16b0b11c4
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432000"
---
# <a name="working-with-the-ui-thread-in-xamarinios"></a>Utilisation du thread d’interface utilisateur dans Xamarin. iOS

Les interfaces utilisateur d’application sont toujours monothreads, même dans les appareils multithread : il n’existe qu’une seule représentation de l’écran et toute modification apportée à ce qui est affiché doit être coordonnée par un point d’accès unique. Cela empêche plusieurs threads d’essayer de mettre à jour le même pixel en même temps (par exemple).

Votre code doit uniquement apporter des modifications aux contrôles de l’interface utilisateur à partir du thread principal (ou de l’interface utilisateur). Les mises à jour de l’interface utilisateur qui se produisent sur un thread différent (par exemple, un rappel ou un thread d’arrière-plan) peuvent ne pas être rendues à l’écran ou peuvent même provoquer un blocage.

## <a name="ui-thread-execution"></a>Exécution du thread d’interface utilisateur

Lorsque vous créez des contrôles dans une vue ou que vous gérez un événement initié par l’utilisateur, tel qu’une fonction tactile, le code est déjà en cours d’exécution dans le contexte du thread d’interface utilisateur.

Si le code s’exécute sur un thread d’arrière-plan, dans une tâche ou un rappel, il est probable qu’il ne s’exécute pas sur le thread d’interface utilisateur principal. Dans ce cas, vous devez encapsuler le code dans un appel à `InvokeOnMainThread` ou `BeginInvokeOnMainThread` comme suit :

```csharp
InvokeOnMainThread ( () => {
    // manipulate UI controls
});
```

La `InvokeOnMainThread` méthode est définie sur de `NSObject` sorte qu’elle peut être appelée à partir de méthodes définies sur tout objet UIKit (tel qu’un contrôle View ou View Controller).

Lors du débogage des applications Xamarin. iOS, une erreur est levée si votre code tente d’accéder à un contrôle d’interface utilisateur à partir du mauvais thread. Cela vous permet d’identifier et de résoudre ces problèmes avec la méthode InvokeOnMainThread. Cela se produit uniquement lors du débogage et ne génère pas d’erreur dans les versions release. Le message d’erreur s’affiche comme suit :

 ![Exécution du thread d’interface utilisateur](ui-thread-images/image10.png)

 <a name="Background_Thread_Example"></a>

## <a name="background-thread-example"></a>Exemple de thread d’arrière-plan

Voici un exemple qui tente d’accéder à un contrôle d’interface utilisateur (a `UILabel` ) à partir d’un thread d’arrière-plan à l’aide d’un thread simple :

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    label1.Text = "updated in thread"; // should NOT reference UILabel on background thread!
})).Start();
```

Ce code lèvera le `UIKitThreadAccessException` en cours de débogage. Pour résoudre le problème (et vérifier que le contrôle d’interface utilisateur n’est accessible qu’à partir du thread d’interface utilisateur principal), encapsulez tout code qui référence des contrôles d’interface utilisateur à l’intérieur d’une `InvokeOnMainThread` expression comme suit :

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    InvokeOnMainThread (() => {
        label1.Text = "updated in thread"; // this works!
    });
})).Start();
```

Vous n’aurez pas besoin de l’utiliser pour le reste des exemples de ce document, mais il s’agit d’un concept important à retenir lorsque votre application effectue des demandes réseau, utilise le centre de notifications ou d’autres méthodes qui requièrent un gestionnaire d’achèvement qui s’exécutera sur un autre thread.

 <a name="Async_Await_Example"></a>

## <a name="asyncawait-example"></a>Exemple Async/await

L’utilisation de mots clés Async/await C# 5 `InvokeOnMainThread` n’est pas obligatoire, car lorsqu’une tâche attendue se termine, la méthode continue sur le thread appelant.

Cet exemple de code (qui attend un appel de la méthode Delay, uniquement à des fins de démonstration) illustre une méthode Async qui est appelée sur le thread d’interface utilisateur (il s’agit d’un gestionnaire TouchUpInside). Étant donné que la méthode conteneur est appelée sur le thread d’interface utilisateur, les opérations d’interface utilisateur telles que la définition du texte sur un `UILabel` ou l’indication d’un `UIAlertView` peuvent être appelées en toute sécurité une fois les opérations asynchrones terminées sur les threads d’arrière-plan

```csharp
async partial void button2_TouchUpInside (UIButton sender)
{
    textfield1.ResignFirstResponder ();
    textfield2.ResignFirstResponder ();
    textview1.ResignFirstResponder ();
    label1.Text = "async method started";
    await Task.Delay(1000); // example purpose only
    label1.Text = "1 second passed";
    await Task.Delay(2000);
    label1.Text = "2 more seconds passed";
    await Task.Delay(1000);
    new UIAlertView("Async method complete", "This method", 
               null, "Cancel", null)
        .Show();
    label1.Text = "async method completed";
}
```

Si une méthode Async est appelée à partir d’un thread d’arrière-plan (et non le thread d’interface utilisateur principal), elle est `InvokeOnMainThread` toujours requise.

## <a name="related-links"></a>Liens associés

- [Contrôles (exemple)](/samples/xamarin/ios-samples/controls)
- [Thread](~/ios/app-fundamentals/threading.md)