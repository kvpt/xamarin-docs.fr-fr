---
title: watchOS Suggestions proactives dans Xamarin
description: Cet article explique comment utiliser des Suggestions proactives dans une application watchOS 3 pour favoriser l’engagement en autorisant le système de façon proactive présenter automatiquement des informations utiles à l’utilisateur.
ms.prod: xamarin
ms.assetid: 10CC9F16-963C-44F1-8B98-F09FB2310DFF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 0c0bf6058b2ec7a8e3ef606bef9f725a476abffe
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865917"
---
# <a name="watchos-proactive-suggestions-in-xamarin"></a>watchOS Suggestions proactives dans Xamarin

_Cet article explique comment utiliser des Suggestions proactives dans une application watchOS 3 pour favoriser l’engagement en autorisant le système de façon proactive présenter automatiquement des informations utiles à l’utilisateur._


Nouveau à watchOS 3, Suggestions proactives présent news façons pour les utilisateurs à s’engager avec une application Xamarin.iOS en informations utiles présentes de manière proactive automatiquement à l’utilisateur aux moments opportuns.


## <a name="about-proactive-suggestions"></a>À propos de Suggestions proactives

Nouveau à watchOS 3, `NSUserActivity` inclut un `MapItem` propriété qui permet à l’application fournir des informations d’emplacement qui peuvent être utilisées dans d’autres contextes. Par exemple, si l’hôtel application affichée, ainsi qu’un `MapItem` emplacement, si l’utilisateur a basculé vers l’application Maps, l’emplacement de l’hôtel, ils ont été seul affichage serait disponible.

L’application expose cette fonctionnalité dans le système à l’aide d’une collection de technologies telles que `NSUserActivity`, MapKit, Media Player et UIKit. En outre, en fournissant la prise en charge de Suggestion Proactive pour l’application, il obtient une intégration plus profonde Siri gratuitement.

## <a name="location-based-suggestions"></a>Emplacement en fonction des Suggestions

Nouveau à watchOS 3, le `NSUserActivity` classe inclut un `MapItem` propriété qui permet au développeur de fournir des informations d’emplacement qui peuvent être utilisées dans d’autres contextes. Par exemple, si l’application affiche des commentaires sur des restaurants, le développeur peut définir le `MapItem` propriété à l’emplacement du restaurant l’utilisateur est affiché dans l’application. Si l’utilisateur passe à l’application de cartes, les emplacement du restaurant sont automatiquement disponible.

Si l’application prend en charge la recherche de l’application, il peut utiliser les nouveaux composants de l’adresse de la `CSSearchableItemAttributesSet` classe pour spécifier des emplacements de l’utilisateur doit pouvoir à visiter. En définissant le `MapItem` propriété, les autres propriétés sont renseignés automatiquement.

En plus du paramètre le `Latitude` et `Longitude` les composant Propriétés d’adresse, il est recommandé que l’application fournisse la `NamedLocation` et `PhoneNumbers` propriétés trop, par conséquent, Siri peut initier un appel à l’emplacement.

## <a name="contextual-siri-reminders"></a>Rappels de Siri contextuelles

Permet à l’utilisateur à utiliser Siri pour effectuer rapidement un rappel pour afficher le contenu qu’ils sont actuellement affiché dans l’application à une date ultérieure. Par exemple, si elles ont été afficher une révision de restaurant dans l’application, ils pourraient appeler Siri et dire *« Me le rappeler à ce sujet lorsque j’obtiens accueil. »* Siri génèrerait le rappel avec un lien vers la révision dans l’application.

## <a name="implementing-proactive-suggestions"></a>Implémentation de Suggestions proactives

Ajout de Suggestion Proactive prise en charge à l’application Xamarin.iOS est généralement aussi simple que l’implémentation de certaines API ou le développement sur quelques API de l’application peut-être déjà être implémentés par.

Suggestions proactives fonctionnent avec les applications de trois façons :

- **`NSUserActivity`** -Aide le système qui décrit les informations que l’utilisateur travaille actuellement avec sur l’écran.
- **Suggestions d’emplacement** : si l’application offre ou consomme des informations basées sur l’emplacement, ces API extension offre de nouvelles façons de partager ces informations entre applications.

Est pris en charge dans l’application en implémentant les éléments suivants :

- **Les rappels de Siri contextuelles** - dans iOS 10, `NSUserActivity` a été étendu pour autoriser Siri à effectuer rapidement un rappel pour afficher le contenu qu’ils sont actuellement affiché dans l’application à une date ultérieure.
- **Suggestions d’emplacement** -iOS 10 améliore `NSUserActivity` pour capturer les emplacements affichés à l’intérieur de l’application et de les promouvoir à différents endroits du système.
- **Les requêtes Siri contextuelles**  -  `NSUserActivity` fournit le contexte pour les informations présentées à l’intérieur de l’application à Siri afin que l’utilisateur puisse directions ou lieux un appel appel Siri à partir de l’application.

Toutes ces fonctionnalités ont une chose en commun, ils utilisent tous `NSUserActivity` dans un formulaire ou autre pour fournir leur fonctionnalité. 

## <a name="nsuseractivity"></a>NSUserActivity

Comme indiqué ci-dessus, `NSUserActivity` aide le système qui décrit les informations que l’utilisateur travaille actuellement avec sur l’écran. `NSUserActivity` un état non activable est mise en cache mécanisme permettant de capturer l’activité de l’utilisateur quand ils naviguent via l’application. Par exemple, examinons l’application de restaurant :

[![](proactive-suggestions-images/activity02.png "L’application de restaurant")](proactive-suggestions-images/activity02.png#lightbox)

Avec les interactions suivantes :

1. Comme l’utilisateur travaille avec l’application, un `NSUserActivity` est créé pour recréer l’état de l’application plus tard.
2. Si l’utilisateur recherche un restaurant, le même modèle de création d’activités est suivi.
3. Et là encore, lorsque l’utilisateur affiche un résultat. Dans ce dernier cas, l’utilisateur consulte un emplacement et dans iOS 10, le système est plus conscient de certains concepts (tels que les interactions de communication ou d’emplacement).

Examinons plus en détail le dernier écran :

[![](proactive-suggestions-images/activity03.png "La charge utile NSUserActivity")](proactive-suggestions-images/activity03.png#lightbox)

Ici l’application consiste à créer un `NSUserActivity` et il a été rempli avec les informations pour recréer l’état ultérieurement. L’application a également inclus des métadonnées telles que le nom et l’adresse de l’emplacement. Avec cette activité est créée, l’application informe iOS qu’il représente l’état actuel de l’utilisateur.

L’application décide alors si l’activité est publiée par voie hertzienne pour la remise, enregistrée sous la forme d’une valeur temporaire pour obtenir des suggestions d’emplacement ou ajoutée à l’index de Spotlight sur l’appareil pour l’affichage des résultats de recherche.

Pour plus d’informations sur la recherche des transitions graduelles et Spotlight, veuillez consulter notre [Introduction à la procédure de transfert](~/ios/platform/handoff.md) et [iOS 9 nouvelles API de recherche](~/ios/platform/search/index.md) guides.

### <a name="creating-an-activity"></a>Création d’une activité

Avant de créer une activité, un identificateur de Type d’activité est nécessaire pour être créé pour être identifié. L’identificateur de Type d’activité est une chaîne courte est ajoutée à la `NSUserActivityTypes` tableau de l’application `Info.plist` fichier utilisé pour identifier un Type d’activité utilisateur donné. Il y aura une entrée dans le tableau pour chaque activité qui prend en charge de l’application et expose à la recherche de l’application. Consultez notre [création Type identificateurs de référence sur l’activité](~/ios/platform/search/nsuseractivity.md) pour plus d’informations.

Étudier un exemple d’une activité :

```csharp
// Create App Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Enable capabilities
activity.EligibleForSearch = true;
activity.EligibleForHandoff = true;
activity.EligibleForPublicIndexing = true;

// Inform system of Activity
activity.BecomeCurrent();
```

Une activité est créée à l’aide d’un identificateur de Type d’activité. Ensuite, certaines métadonnées qui définissent l’activité sont créée pour cet état peut être restauré à une date ultérieure. Ensuite, l’activité est un titre significatif et attachée aux informations utilisateur. Enfin, certaines fonctionnalités sont activées et que l’activité est envoyée au système.

Le code ci-dessus peut encore être amélioré pour inclure les métadonnées qui fournit le contexte à l’activité en apportant les modifications suivantes :

```csharp
...

// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
attributes.ThumbnailUrl = myThumbnailURL;
attributes.Keywords = new string [] { "software", "mobile", "language" }; 
activity.ContentAttributeSet = attributes;

// Inform system of Activity
activity.BecomeCurrent();
```

Si le développeur a un site Web qui est capable d’afficher les mêmes informations que l’application, l’application peut inclure l’URL et le contenu peut être affiché sur d’autres appareils qui n’ont pas l’application est installée (via la procédure de transfert) :

```csharp
// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");
```

### <a name="restoring-an-activity"></a>Restauration d’une activité

Pour répondre à l’utilisateur en appuyant sur un résultat de recherche (`NSUserActivity`) pour l’application, vous devez modifier le **AppDelegate.cs** de fichiers et de remplacer le `ContinueUserActivity` (méthode). Par exemple :

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

Assurez-vous qu’est le même identificateur de Type d’activité (`com.xamarin.platform`) en tant que l’activité créée ci-dessus. L’application utilise les informations stockées dans le `NSUserActivity` pour restaurer l’état dans lequel l’utilisateur s’est arrêté.

### <a name="benefits-of-creating-an-activity"></a>Avantages de la création d’une activité

Avec la quantité minimale de code présenté ci-dessus, l’application est maintenant en mesure de tirer parti des trois nouvelles fonctionnalités d’iOS 10 :

- **Handoff**
- **Recherche Spotlight**
- **Rappels de Siri contextuelles**

La section suivante sera un œil à activer les deux autres nouvelles fonctionnalités iOS 10 :

- **Suggestions d’emplacement**
- **Requêtes Siri contextuelles**

### <a name="location-based-suggestions"></a>Emplacement en fonction des Suggestions 

Prenons l’exemple de l’application de recherche de restaurant ci-dessus. Si elle a implémenté `NSUserActivity` et correctement rempli toutes les métadonnées et les attributs, l’utilisateur serait en mesure d’effectuer les opérations suivantes :

1. Trouver un restaurant dans l’application qu’ils souhaiteraient pour répondre à un ami chez.
2. Si l’utilisateur passe à l’application de cartes, les adresses du restaurant sont suggérés automatiquement en tant que destination.
3. Cela fonctionne même pour les applications tierces 3e (qui prennent en charge `NSUserActivity`), de sorte que l’utilisateur peut basculer vers une application de partage de course et les adresses du restaurant sont automatiquement suggéré en tant que destination il également.
4. Elle également fournit un contexte à Siri, l’utilisateur peut appeler Siri au sein de l’application de restaurant et demandez *« Get directions... »* et Siri fournira des instructions au restaurant l’affichage de l’utilisateur.

Toutes les fonctionnalités ci-dessus a une chose en commun, tous les indiquent où la suggestion à l’origine provient. Dans le cas de l’exemple ci-dessus, il est l’application de révision de restaurant fictive.

watchOS 3 a été amélioré pour activer cette fonctionnalité pour une application via plusieurs petites modifications et ajouts aux infrastructures existantes :

- `NSUserActivity` comporte des champs supplémentaires pour capturer les informations d’emplacement qui sont affichées à l’intérieur de l’application.
- Plusieurs ajouts ont été apportées à MapKit et CoreSpotlight pour capturer l’emplacement.
- Prenant en charge les fonctionnalités d’emplacement a été ajoutée à Siri, cartes, multitâche et autres applications au sein du système.

Pour implémenter des suggestions basées sur l’emplacement, démarrer avec le même code d’activité présenté ci-dessus :

```csharp
// Create App Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Enable capabilities
activity.EligibleForSearch = true;
activity.EligibleForHandoff = true;
activity.EligibleForPublicIndexing = true;

// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
attributes.ThumbnailUrl = myThumbnailURL;
attributes.Keywords = new string [] { "software", "mobile", "language" }; 
activity.ContentAttributeSet = attributes;

// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");

// Inform system of Activity
activity.BecomeCurrent();
```

Si l’application utilise MapKit, c’est aussi simple que l’ajout de l’objet map actuel `MKMapItem` à l’activité :

```csharp
// Save MKMapItem location
activity.MapItem = myMapItem;
```

Si l’application n’utilise pas MapKit, il peut adopter la recherche de l’application et spécifier les nouveaux attributs pour l’emplacement suivants :

```csharp
// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
...

attributes.NamedLocation = "Apple Inc.";
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

Examinons le code ci-dessus en détail. Tout d’abord, le nom de l’emplacement est requis dans chaque instance :

```csharp
attributes.NamedLocation = "Apple Inc.";
```

Ensuite, le texte de description en dans requis pour le texte en instances (par exemple, le clavier QuickType) :

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

La latitude et la longitude sont facultatifs, mais assurez-vous que l’utilisateur est acheminé vers l’emplacement exact, si vous l’application à les envoyer à :

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

En définissant les numéros de téléphone, l’application peut accéder à Siri afin de l’utilisateur peut appeler Siri à partir de l’application en disant quelque chose comme : * « Appeler ce lieu » :

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

Enfin, l’application peut indiquer si l’instance est approprié pour la navigation et les appels téléphoniques :

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```
## <a name="activities-best-practices"></a>Activités de meilleures pratiques

Apple suggère les meilleures pratiques suivantes lorsque vous travaillez avec des activités :

- Utilisez `NeedsSave` des mises à jour de la charge utile différé.
- Veillez à conserver une référence forte à l’activité en cours.
- Transférer uniquement les petites charges utiles qui incluent des informations de base pour restaurer l’état.
- Assurez-vous que les identificateurs de Type d’activité sont unique et descriptif de les spécifier à l’aide de la notation DNS inverse. 

## <a name="consuming-location-suggestions"></a>Consommation des Suggestions d’emplacement

La section suivante couvre la consommation Suggestion d’emplacement issues d’autres parties du système (par exemple, l’application Maps) ou d’autres applications tierces 3e.

## <a name="routing-apps-and-locations-suggestions"></a>Applications de routage et les Suggestions d’emplacements

Cette section sera un œil à la consommation des Suggestions d’emplacement directement à partir d’une application de routage. Pour l’application de routage ajouter cette fonctionnalité, le développeur s’appuiera existant `MKDirectionsRequest` framework comme suit :

- Pour promouvoir l’application dans le multitâche.
- Pour inscrire l’application comme une application de routage.
- Pour gérer le lancement de l’application avec un MapKit `MKDirectionsRequest` objet.
- Permettre à watchOS pour en savoir plus suggérer l’application en fonction de l’intérêt des utilisateurs.

Lorsque l’application est démarrée avec un MapKit `MKDirectionsRequest` de l’objet, il doit automatiquement démarrer en donnant les instructions de l’utilisateur à l’emplacement demandé, ou présenter une interface utilisateur qui facilite l’utilisateur commencer à obtenir des instructions. Par exemple :


```csharp
using System;
using Foundation;
using UIKit;
using MapKit;
using CoreLocation;

namespace MonkeyChat
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate, IUISplitViewControllerDelegate
    {
        ...
        
        public override bool OpenUrl (UIApplication app, NSUrl url, NSDictionary options)
        {
            if (MKDirectionsRequest.IsDirectionsRequestUrl (url)) {
                var request = new MKDirectionsRequest (url);
                var coordinate = request.Destination?.Placemark.Location?.Coordinate;
                var address = request.Destination.Placemark.AddressDictionary;
                if (coordinate.IsValid()) {
                    var geocoder = new CLGeocoder ();
                    geocoder.GeocodeAddress (address, (place, err) => {
                        // Handle the display of the address

                    });
                }
            }

            return true;
        }
    }       
}
```

Examinons ce code en détail. Il vérifie s’il s’agit d’une demande de destination valide :

```csharp
if (MKDirectionsRequest.IsDirectionsRequestUrl(url)) {
```

Si elle est, il crée un `MKDirectionsRequest` à partir de l’URL :

```csharp
var request = new MKDirectionsRequest(url);
```

Nouveau dans watchOS 3, l’application peut être envoyée une adresse qui n’a pas de coordonnées géographiques, dans qui entraînent que le développeur a besoin d’encoder l’adresse :

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address
    
});

```

## <a name="summary"></a>Récapitulatif

Cet article a couvert les Suggestions proactives et vous a montré comment le développeur peut les utiliser pour le trafic de lecteur à une application Xamarin.iOS pour watchOS. Il couvert l’étape pour implémenter des Suggestions proactives et affiche des instructions d’utilisation.


## <a name="related-links"></a>Liens associés

- [Exemples watchOS](https://developer.xamarin.com/samples/watchos/all/)
- [Guide de programmation de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
