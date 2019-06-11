---
title: Travaillez sur le Thread d’interface utilisateur dans Xamarin.iOS
description: Ce document décrit comment travailler avec le Thread d’interface utilisateur dans Xamarin.iOS. Il traite de l’exécution de thread de l’interface utilisateur, fournit un exemple de thread d’arrière-plan et examine async/await.
ms.prod: xamarin
ms.assetid: 98762ACA-AD5A-4E1E-A536-7AF3BE36D77E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: e4485c485b708bdec06f7f1dc22f0bf33e07e982
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827744"
---
# <a name="working-with-the-ui-thread-in-xamarinios"></a>Travaillez sur le Thread d’interface utilisateur dans Xamarin.iOS

Interfaces utilisateur d’application sont toujours monothread, même pour les périphériques multithreads : il n'existe qu’une seule représentation sous forme de l’écran et toute modification apportée à ce qui est affiché doit être coordonné via un seul « point d’accès ». Cela empêche plusieurs threads à partir de la mise à jour le même pixel en même temps (par exemple).

Votre code doit faire uniquement les threads de modifications apportées aux contrôles d’interface à partir de la main de l’utilisateur (ou l’interface utilisateur). Les mises à jour de l’interface utilisateur qui se produisent sur un thread différent (par exemple, un thread de rappel ou d’arrière-plan) ne peuvent pas obtenir rendus à l’écran, ou peuvent même provoquer un blocage.

## <a name="ui-thread-execution"></a>Exécution du Thread d’interface utilisateur

Lorsque vous créez des contrôles dans une vue, ou gère un événement initié par l’utilisateur comme une pression tactile, le code s’exécute déjà dans le contexte de thread d’interface utilisateur.

Si le code s’exécute sur un thread d’arrière-plan, dans une tâche ou d’un rappel il est probablement ne pas l’exécution sur le thread d’interface utilisateur principal. Dans ce cas, vous devez encapsuler le code dans un appel à `InvokeOnMainThread` ou `BeginInvokeOnMainThread` comme suit :

```csharp
InvokeOnMainThread ( () => {
    // manipulate UI controls
});
```

Le `InvokeOnMainThread` méthode est définie sur `NSObject` afin qu’il peut être appelée à partir de méthodes définies sur n’importe quel objet UIKit (par exemple, une vue ou d’un contrôleur d’affichage).

Pendant le débogage des applications Xamarin.iOS, une erreur est levée si votre code essaie d’accéder à un contrôle d’interface utilisateur à partir d’un thread incorrect. Cela vous aide à détecter et résoudre ces problèmes avec la méthode InvokeOnMainThread. Cela seulement se produit pendant le débogage et ne lève pas d’une erreur dans les versions release. Le message d’erreur apparaîtra comme suit :

 ![](ui-thread-images/image10.png "Exécution du Thread d’interface utilisateur")

 <a name="Background_Thread_Example" />


## <a name="background-thread-example"></a>Exemple de Thread d’arrière-plan

Voici un exemple qui tente d’accéder à un contrôle d’interface utilisateur (un `UILabel`) à partir d’un thread d’arrière-plan à l’aide d’un thread simple :

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    label1.Text = "updated in thread"; // should NOT reference UILabel on background thread!
})).Start();
```

Code lèvera le `UIKitThreadAccessException` pendant le débogage. Pour résoudre le problème (et vous assurer que le contrôle d’interface utilisateur est uniquement accessible à partir du thread d’interface utilisateur principal), encapsuler tout code qui fait référence à des contrôles d’interface utilisateur à l’intérieur d’un `InvokeOnMainThread` expression comme suit :

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    InvokeOnMainThread (() => {
        label1.Text = "updated in thread"; // this works!
    });
})).Start();
```

Vous n’aurez pas à utiliser pour le reste des exemples de ce document, mais vous devez impérativement un concept à retenir lorsque votre application effectue des demandes réseau, utilise le centre de notification ou d’autres méthodes qui requièrent un gestionnaire d’achèvement qui s’exécutera sur un autre thread.

 <a name="Async_Await_Example" />


## <a name="asyncawait-example"></a>Exemple Async/Await

Lorsque vous utilisez le C# 5 mots-clé async/await `InvokeOnMainThread` n’est pas nécessaire, car quand une tâche attendue se termine la méthode continue sur le thread appelant.

Cet exemple de code (qui attend sur un appel de méthode délai purement à des fins de démonstration) montre une méthode async qui est appelée sur le thread d’interface utilisateur (il s’agit d’un gestionnaire de TouchUpInside). Étant donné que la méthode conteneur est appelée sur le thread d’interface utilisateur, les opérations de l’interface utilisateur telles que la définition du texte sur un `UILabel` ou montrant un `UIAlertView` peut être appelée en toute sécurité après l’aboutissement des opérations asynchrones sur des threads d’arrière-plan.

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

Si une méthode async est appelée à partir d’un thread d’arrière-plan (pas le thread d’interface utilisateur principal) puis `InvokeOnMainThread` seraient nécessaires.


## <a name="related-links"></a>Liens associés

- [Contrôles (exemple)](https://developer.xamarin.com/samples/monotouch/Controls/)
- [Thread](~/ios/app-fundamentals/threading.md)
