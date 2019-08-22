---
title: Rechercher avec Spotlight Core dans Xamarin. iOS
description: Ce document explique comment utiliser Core Spotlight dans une application Xamarin. iOS pour fournir des liens vers du contenu dans l’application. Il explique comment créer, restaurer, mettre à jour et supprimer des éléments pouvant faire l’objet d’une recherche.
ms.prod: xamarin
ms.assetid: 1374914C-0F63-41BF-BD97-EBCEE86E57B1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: c322a329a517f9c655ce7b8dac4aaaaf8c5ef4cc
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69889776"
---
# <a name="search-with-core-spotlight-in-xamarinios"></a>Rechercher avec Spotlight Core dans Xamarin. iOS

Core Spotlight est une nouvelle infrastructure pour iOS 9 qui présente une API de type base de données qui permet d’ajouter, de modifier ou de supprimer des liens vers du contenu au sein de votre application. Les éléments qui ont été ajoutés à l’aide de Core Spotlight sont disponibles dans la recherche Spotlight sur l’appareil iOS.

Pour obtenir un exemple des types de contenu qui peuvent être indexés à l’aide de Core Spotlight, regardez les applications messages, messagerie, calendrier et notes d’Apple. Tous utilisent actuellement le projecteur principal pour fournir des résultats de recherche.

## <a name="creating-an-item"></a>Création d’un élément

L’exemple suivant illustre la création d’un élément et son indexation à l’aide de Core Spotlight:

```csharp
using CoreSpotlight;
...

// Create attributes to describe an item
var attributes = new CSSearchableItemAttributeSet();
attributes.Title = "App Center Test";
attributes.ContentDescription = "Automatically test your app on 1,000 devices in the cloud.";

// Create item
var item = new CSSearchableItem ("1", "products", attributes);

// Index item
CSSearchableIndex.DefaultSearchableIndex.Index (new CSSearchableItem[]{ item }, (error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

Ces informations se présentent comme suit dans les résultats de la recherche:

[![](corespotlight-images/corespotlight01.png "Vue d’ensemble des résultats de la recherche Spotlight Core")](corespotlight-images/corespotlight01.png#lightbox)

## <a name="restoring-an-item"></a>Restauration d’un élément

Quand l’utilisateur appuie sur un élément ajouté au résultat de la recherche via le projecteur principal pour votre application `AppDelegate` , `ContinueUserActivity` la méthode est appelée (cette méthode est également `NSUserActivity`utilisée pour). Par exemple :

```csharp
public override bool ContinueUserActivity (UIApplication application,
   NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    default:
        if (userActivity.ActivityType == CSSearchableItem.ActionType.ToString ()) {
            // Display content for searchable item...
        }
        break;
    }

    return true;
}
```

Notez que cette fois, nous vérifions l’activité ayant un `ActivityType` de `CSSearchableItem.ActionType`.

## <a name="updating-an-item"></a>Mise à jour d’un élément

Il peut arriver qu’un élément d’index créé avec Core Spotlight doive être modifié, par exemple une modification du titre ou de l’image miniature est nécessaire. Pour effectuer cette modification, nous utilisons la même méthode que celle utilisée pour la création initiale de l’index.
Nous créons un nouveau `CSSearchableItem` en utilisant le même ID que celui utilisé pour créer l’élément et attacher un `CSSearchableItemAttributeSet` nouveau contenant les attributs modifiés:

[![](corespotlight-images/corespotlight02.png "Mise à jour d’une vue d’ensemble d’un élément")](corespotlight-images/corespotlight02.png#lightbox)

Lorsque cet élément est écrit dans l’index pouvant faire l’objet d’une recherche, l’élément existant est mis à jour avec les nouvelles informations.

## <a name="deleting-an-item"></a>Suppression d’un élément

Core Spotlight offre plusieurs moyens de supprimer un élément d’index lorsqu’il n’est plus nécessaire.

Tout d’abord, vous pouvez supprimer un élément par son identificateur, par exemple:

```csharp
// Delete Items by ID
CSSearchableIndex.DefaultSearchableIndex.Delete(new string[]{"1","16"},(error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

Ensuite, vous pouvez supprimer un groupe d’éléments d’index en fonction de leur nom de domaine. Par exemple :

```csharp
// Delete by Domain Name
CSSearchableIndex.DefaultSearchableIndex.DeleteWithDomain(new string[]{"domain-name"},(error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

Enfin, vous pouvez supprimer tous les éléments d’index avec le code suivant:

```csharp
// Delete all index items
CSSearchableIndex.DefaultSearchableIndex.DeleteAll((error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

## <a name="additional-core-spotlight-features"></a>Fonctionnalités supplémentaires de Spotlight

Core Spotlight offre les fonctionnalités suivantes qui permettent de conserver l’index exact et à jour:

- **Prise en charge des mises à jour par lot** : Si votre application doit créer ou modifier un grand groupe d’index simultanément, le lot entier peut être envoyé à `Index` la méthode de `CSSearchableIndex` la classe en un seul appel.
- **Répondre aux modifications d’index** : l' `CSSearchableIndexDelegate` utilisation de votre application peut répondre aux modifications et aux notifications à partir de l’index pouvant faire l’objet d’une recherche.
- **Appliquer la protection des données** : à l’aide des classes de protection des données, vous pouvez implémenter la sécurité sur les éléments que vous ajoutez à l’index pouvant faire l’objet d’une recherche à l’aide de Core Spotlight.



## <a name="related-links"></a>Liens associés

- [Exemples iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 pour les développeurs](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guide de programmation de recherche d’applications](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
