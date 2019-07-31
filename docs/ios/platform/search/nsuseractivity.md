---
title: Rechercher avec NSUserActivity dans Xamarin. iOS
description: Ce document décrit comment indexer un NSUserActivity, ce qui le rend consultable dans Spotlight et Safari. Il explique comment répondre à la sélection d’un NSUserActivity dans les résultats de la recherche.
ms.prod: xamarin
ms.assetid: 0B28B284-C7C9-4C0D-A782-D471FBBC4CAE
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: f37511082dd3b81a3fba4d165c9276ac40267cbb
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656288"
---
# <a name="search-with-nsuseractivity-in-xamarinios"></a>Rechercher avec NSUserActivity dans Xamarin. iOS

`NSUserActivity`a été introduit dans iOS 8 et est utilisé pour fournir les données pour la remise.
Elle vous permet de créer des activités dans des parties spécifiques de votre application qui peuvent ensuite être transférées vers une autre instance de votre application exécutée sur un autre appareil iOS. L’appareil de réception peut ensuite poursuivre l’activité démarrée sur l’appareil précédent, en reprenant juste là où l’utilisateur s’est arrêté. Pour plus d’informations sur l’utilisation de la remise, consultez notre introduction à la documentation relative [au transfert](~/ios/platform/handoff.md) .

Nouveauté d’iOS 9, `NSUserActivity` peut être indexé (à la fois publique et privée) et recherchée dans la recherche Spotlight et Safari. En marquant un `NSUserActivity` comme pouvant faire l’objet d’une recherche et en ajoutant des métadonnées indexables, l’activité peut être listée dans les résultats de recherche sur l’appareil iOS.

[![](nsuseractivity-images/apphistory01.png "Vue d’ensemble de l’historique des applications")](nsuseractivity-images/apphistory01.png#lightbox)

Si l’utilisateur sélectionne un résultat de recherche qui appartient à une activité de votre application, l’application est lancée et l’activité décrite par `NSUserActivity` le sera redémarrée et présentée à l’utilisateur.

Les propriétés suivantes de `NSUserActivity` sont utilisées pour prendre en charge la recherche d’applications:

- `EligibleForHandoff`– Si `true`, cette activité peut être utilisée dans une opération de remise.
- `EligibleForSearch`– Si `true`la valeur est, cette activité est ajoutée à l’index sur l’appareil et présentée dans les résultats de la recherche.
- `EligibleForPublicIndexing`– Si `true`cette activité est ajoutée à l’index Cloud d’Apple et présentée aux utilisateurs (via la recherche) qui n’ont pas encore installé votre application sur leur appareil iOS. Pour plus d’informations, consultez la section [indexation de recherche publique](#public-search-indexing) ci-dessous.
- `Title`: Fournit un titre pour votre activité et s’affiche dans les résultats de la recherche. Les utilisateurs peuvent également rechercher le texte du titre lui-même.
- `Keywords`– Est un tableau de chaînes utilisé pour décrire votre activité qui sera indexée et fera l’objet d’une recherche par l’utilisateur final.
- `ContentAttributeSet`– Est un `CSSearchableItemAttributeSet` utilisé pour décrire plus en détail votre activité et fournir du contenu riche dans les résultats de la recherche.
- `ExpirationDate`: Si vous souhaitez qu’une activité s’affiche uniquement jusqu’à une date donnée, vous pouvez fournir cette date ici.
- `WebpageURL`: Si l’activité peut être affichée sur le Web ou si votre application prend en charge les liens ciblés de Safari, vous pouvez définir le lien sur visiter ici.

## <a name="nsuseractivity-quickstart"></a>Démarrage rapide de NSUserActivity

Suivez ces instructions pour implémenter une recherche `NSUserActivity` dans votre application:

- [Création d’identificateurs de type d’activité](#creatingtypeid)
- [Création d’une activité](#createactivity)
- [Répondre à une activité](#respondactivity)
- [Indexation de recherche publique](#indexing)

L’utilisation `NSUserActivity` de pour faciliter la recherche de contenu présente des [avantages supplémentaires](#benefits) .

<a name="creatingtypeid" />

## <a name="creating-activity-type-identifiers"></a>Création d’identificateurs de type d’activité

Avant de pouvoir créer une activité de recherche, vous devez créer un _identificateur de type d’activité_ pour l’identifier. L’identificateur de type d’activité est une chaîne abrégée ajoutée `NSUserActivityTypes` au tableau du fichier **info. plist** de l’application, utilisée pour identifier de manière unique un type d’activité utilisateur donné. Il y aura une entrée dans le tableau pour chaque activité que l’application prend en charge et expose à la recherche d’applications. 

Apple suggère d’utiliser une notation de style DNS inversé pour l’identificateur de type d’activité afin d’éviter les collisions. Par exemple: `com.company-name.appname.activity` pour des activités spécifiques basées sur `com.company-name.activity` une application ou pour des activités pouvant s’exécuter sur plusieurs applications.

L’identificateur de type d’activité est utilisé lors de `NSUserActivity` la création d’une instance pour identifier le type d’activité. Lorsqu’une activité est poursuivie suite à la poursuite de l’utilisateur en appuyant sur un résultat de recherche, le type d’activité (ainsi que l’ID de l’équipe de l’application) détermine l’application à lancer pour continuer l’activité.

Pour créer les identificateurs de type d’activité requis pour prendre en charge ce comportement, modifiez le fichier **info. plist** et basculez en mode **source** . Ajoutez une `NSUserActivityTypes` clé et créez des identificateurs au format suivant:

[![](nsuseractivity-images/type01.png "Clé NSUserActivityTypes et identificateurs requis dans l’éditeur plist")](nsuseractivity-images/type01.png#lightbox)

Dans l’exemple ci-dessus, nous avons créé un nouvel identificateur de type d’activité pour`com.xamarin.platform`l’activité de recherche (). Lorsque vous créez vos propres applications, remplacez le contenu du `NSUserActivityTypes` tableau par les identificateurs de type d’activité spécifiques aux activités prises en charge par votre application.

<a name="createactivity" />

## <a name="creating-an-activity"></a>Création d’une activité

Voici un exemple de création d’une activité pour une recherche hébergée sur un index sur l’appareil:

```csharp
// Create App Search Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Add App Search ability
activity.EligibleForSearch = true;
activity.BecomeCurrent();
```

Nous pouvons ajouter des détails supplémentaires en définissant la `ContentAttributeSet` propriété de notre `NSUserActivity` comme suit:

[![](nsuseractivity-images/apphistory02.png "Détails de la recherche d’addition")](nsuseractivity-images/apphistory02.png#lightbox)

En utilisant un `ContentAttributeSet` , vous pouvez créer des résultats de recherche enrichis qui incite l’utilisateur final à interagir avec lui.

<a name="respondactivity" />

## <a name="responding-to-an-activity"></a>Répondre à une activité

Pour répondre à l’utilisateur en appuyant sur un résultat`NSUserActivity`de recherche () pour notre application, modifiez le fichier **AppDelegate.cs** et `ContinueUserActivity` remplacez la méthode. Par exemple :

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    }

    return true;
}
```

Notez qu’il s’agit du même remplacement de méthode utilisé pour répondre aux demandes de remise. Maintenant, si l’utilisateur clique sur un lien de notre application dans les résultats de la recherche Spotlight, notre application est placée au premier plan (ou démarrée si elle n’est pas déjà en cours d’exécution) et le contenu, la navigation ou la fonctionnalité représentée par ce lien s’affiche:

[![](nsuseractivity-images/apphistory03.png "Restaurer l’état précédent à partir de la recherche")](nsuseractivity-images/apphistory03.png#lightbox)

<a name="indexing" />

## <a name="public-search-indexing"></a>Indexation de recherche publique

Comme nous l’avons vu plus haut, iOS 9 facilite l’accès à la recherche au contenu et aux fonctionnalités de l’application que l’utilisateur a déjà découvertes et utilisées sur un appareil iOS donné. Avec l’indexation publique, iOS 9 offre un moyen aux utilisateurs qui n’ont pas encore découvert du contenu ou des fonctionnalités (ou qui n’ont pas encore installé l’application) d’obtenir ces résultats dans leurs recherches.

L’indexation publique fonctionne de la façon suivante:

1. Lorsque vous créez une activité pour votre application, vous pouvez la marquer comme publique.
2. Les activités publiques sont envoyées à Apple et indexées dans le Cloud.
3. À mesure que d’autres utilisateurs interagissent avec votre application sur un appareil et utilisent la fonctionnalité ou le contenu spécifique représenté par l’activité, le classement augmente.
4. Les résultats publics populaires seront mis à la disposition d’autres utilisateurs, même s’ils n’ont pas l’application installée.
5. Ces résultats publics s’affichent dans la recherche Spotlight et Safari (si l’activité comprend une URL).

Nous pouvons prendre l’activité de recherche privée que nous avons créée ci-dessus et la développer pour qu’elle soit publique:

```csharp
// Create App Search Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Add App Search ability
activity.EligibleForSearch = true;
activity.EligibleForPublicIndexing = true;
activity.BecomeCurrent();
```

Juste parce qu’une activité a été définie pour l’indexation publique en `EligibleForPublicIndexing = true`définissant, cela ne signifie pas qu’elle sera automatiquement ajoutée à l’index du cloud public d’Apple. Les conditions suivantes doivent être remplies en premier:

1. Il doit apparaître dans les résultats de la recherche et être sélectionné par de nombreux utilisateurs. Les résultats restent privés jusqu’à ce qu’un seuil d’engagement d’activité soit atteint.
2. L’approvisionnement d’applications empêche toute donnée propre à l’utilisateur d’être indexée et rendue publique.

<a name="benefits" />

## <a name="additional-benefits"></a>Autres avantages

En adoptant la recherche `NSUserActivity` d’application via dans votre application, vous bénéficiez également des fonctionnalités suivantes:

- **Transfert** : étant donné que la recherche d’application expose du contenu, de la navigation et/ou des fonctionnalités`NSUserActivity`à l’aide du même mécanisme que la remise (), vous pouvez facilement autoriser les utilisateurs de votre application à démarrer une activité sur un appareil et à le poursuivre sur un autre.
- Les suggestions **Siri** , ainsi que les suggestions standard que Siri les suggestions effectuent normalement, les actifs de votre application peuvent être suggérés automatiquement.
- **Rappels intelligents Siri** : les utilisateurs peuvent demander à Siri de les rappeler des activités de votre application.



## <a name="related-links"></a>Liens associés

- [Exemples iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 pour les développeurs](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guide de programmation de recherche d’applications](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
- [Billet de blog & exemple](https://blog.xamarin.com/improve-discoverability-with-search-in-ios-9/)
