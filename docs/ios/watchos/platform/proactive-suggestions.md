---
title: Suggestions proactives Watchos dans Xamarin
description: Cet article explique comment utiliser des suggestions proactives dans une application Watchos 3 pour conduire un engagement en permettant au système de présenter de manière proactive des informations utiles à l’utilisateur.
ms.prod: xamarin
ms.assetid: 10CC9F16-963C-44F1-8B98-F09FB2310DFF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 4235e6049b9700edbb3974f1e4cbaf7c405f6e83
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937473"
---
# <a name="watchos-proactive-suggestions-in-xamarin"></a>Suggestions proactives Watchos dans Xamarin

_Cet article explique comment utiliser des suggestions proactives dans une application Watchos 3 pour conduire un engagement en permettant au système de présenter de manière proactive des informations utiles à l’utilisateur._

Nouveauté de Watchos 3, les suggestions proactives présentent de nouvelles méthodes permettant aux utilisateurs de s’associer à une application Xamarin. iOS en présentant de manière proactive les informations utiles automatiquement à l’utilisateur à des moments opportuns.

## <a name="about-proactive-suggestions"></a>À propos des suggestions proactives

Nouveauté de Watchos 3, `NSUserActivity` comprend une `MapItem` propriété qui permet à l’application de fournir des informations d’emplacement qui peuvent être utilisées dans d’autres contextes. Par exemple, si l’application a affiché des revues d’hôtel et fournit un `MapItem` emplacement, si l’utilisateur a basculé vers l’application Maps, l’emplacement de l’hôtel qu’elle affichait est disponible.

L’application expose cette fonctionnalité au système à l’aide d’une collection de technologies telles que `NSUserActivity` , MapKit, Media Player et uikit. En outre, en fournissant une prise en charge proactive des suggestions pour l’application, l’intégration Siri est plus poussée gratuitement.

## <a name="location-based-suggestions"></a>Suggestions basées sur l’emplacement

Nouveauté de Watchos 3, la `NSUserActivity` classe comprend une `MapItem` propriété qui permet au développeur de fournir des informations d’emplacement qui peuvent être utilisées dans d’autres contextes. Par exemple, si l’application affiche des revues de restaurant, le développeur peut définir la `MapItem` propriété sur l’emplacement du restaurant que l’utilisateur visualise dans l’application. Si l’utilisateur bascule vers l’application Maps, l’emplacement du restaurant est automatiquement disponible.

Si l’application prend en charge la recherche d’application, elle peut utiliser les nouveaux composants d’adresse de la `CSSearchableItemAttributesSet` classe pour spécifier les emplacements que l’utilisateur peut vouloir visiter. En définissant la `MapItem` propriété, les autres propriétés sont renseignées automatiquement.

En plus de définir les `Latitude` `Longitude` Propriétés du composant Address et de l’adresse, il est recommandé que l’application fournisse `NamedLocation` également les propriétés et, de sorte que `PhoneNumbers` Siri puisse lancer un appel à l’emplacement.

## <a name="contextual-siri-reminders"></a>Rappels Siri contextuels

Permet à l’utilisateur d’utiliser Siri pour créer rapidement un rappel afin d’afficher le contenu actuellement affiché dans l’application à une date ultérieure. Par exemple, s’ils visualisaient un avis de restaurant dans l’application, ils pouvaient appeler Siri et *« me le rappeler lorsque je suis à la page. »* Siri génère le rappel avec un lien vers la revue dans l’application.

## <a name="implementing-proactive-suggestions"></a>Implémentation de suggestions proactives

L’ajout de la prise en charge de suggestions proactives à l’application Xamarin. iOS est généralement aussi simple que d’implémenter quelques API ou de s’étendre sur quelques API que l’application peut déjà implémenter.

Les suggestions proactives fonctionnent avec les applications de trois manières principales :

- **`NSUserActivity`**-Aide le système à comprendre quelles informations l’utilisateur travaille actuellement sur l’écran.
- **Suggestions d’emplacement** : si l’application offre ou consomme des informations sur l’emplacement, ces extensions d’API offrent de nouvelles façons de partager ces informations entre les applications.

Et est pris en charge dans l’application en implémentant les éléments suivants :

- Les **rappels Siri contextuels** -dans iOS 10, `NSUserActivity` ont été développés pour permettre à Siri de créer rapidement un rappel pour afficher le contenu qu’ils visualisent actuellement dans l’application à une date ultérieure.
- **Suggestions d’emplacement** : iOS 10 `NSUserActivity` s’améliore pour capturer les emplacements affichés dans l’application et les promouvoir à de nombreux endroits dans le système.
- **Contextual Siri Requests**  -  Requêtes `NSUserActivity` Siri contextuelles fournit le contexte aux informations présentées à l’intérieur de l’application à Siri afin que l’utilisateur puisse obtenir des instructions ou passer un appel en appelant Siri à partir de l’application.

Toutes ces fonctionnalités ont une chose en commun, elles sont toutes utilisées `NSUserActivity` dans un formulaire ou une autre pour fournir leurs fonctionnalités. 

## <a name="nsuseractivity"></a>NSUserActivity

Comme indiqué ci-dessus, `NSUserActivity` le système aide le système à comprendre quelles informations l’utilisateur travaille actuellement sur l’écran. `NSUserActivity`est un mécanisme de mise en cache d’État léger qui permet de capturer l’activité de l’utilisateur au fur et à mesure qu’il navigue dans l’application. Par exemple, en examinant l’application restaurant :

[![L’application restaurant](proactive-suggestions-images/activity02.png)](proactive-suggestions-images/activity02.png#lightbox)

Avec les interactions suivantes :

1. Lorsque l’utilisateur travaille avec l’application, un `NSUserActivity` est créé pour recréer l’état de l’application ultérieurement.
2. Si l’utilisateur recherche un restaurant, le même modèle de création d’activités est suivi.
3. Là encore, lorsque l’utilisateur affiche un résultat. Dans ce dernier cas, l’utilisateur visualise un emplacement et dans iOS 10, le système est plus conscient de certains concepts (tels que l’emplacement ou les interactions de communication).

Examinez de plus près le dernier écran :

[![Charge utile NSUserActivity](proactive-suggestions-images/activity03.png)](proactive-suggestions-images/activity03.png#lightbox)

Ici, l’application crée un `NSUserActivity` et contient des informations pour recréer l’État ultérieurement. L’application a également inclus des métadonnées telles que le nom et l’adresse de l’emplacement. Une fois cette activité créée, l’application permet à iOS de savoir qu’elle représente l’état actuel de l’utilisateur.

L’application décide ensuite si l’activité sera publiée par voie hertzienne pour le transfert, enregistrée comme valeur temporaire pour les suggestions d’emplacement ou ajoutée à l’index Spotlight sur l’appareil pour l’affichage dans les résultats de la recherche.

Pour plus d’informations sur le transfert et la recherche Spotlight, consultez notre [Introduction à la livraison](~/ios/platform/handoff.md) et les [nouveaux guides des API de recherche iOS 9](~/ios/platform/search/index.md) .

### <a name="creating-an-activity"></a>Création d’une activité

Avant de créer une activité, vous devez créer un identificateur de type d’activité pour l’identifier. L’identificateur de type d’activité est une chaîne abrégée ajoutée au `NSUserActivityTypes` tableau du fichier de l’application `Info.plist` utilisée pour identifier de manière unique un type d’activité utilisateur donné. Il y aura une entrée dans le tableau pour chaque activité que l’application prend en charge et expose à la recherche d’applications. Pour plus d’informations, consultez notre référence sur la [création d’identificateurs de type d’activité](~/ios/platform/search/nsuseractivity.md) .

Examinez un exemple d’activité :

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

Une activité est créée à l’aide d’un identificateur de type d’activité. Ensuite, certaines métadonnées qui définissent l’activité sont créées afin que cet État puisse être restauré à une date ultérieure. Ensuite, l’activité reçoit un titre explicite et est attachée aux informations utilisateur. Enfin, certaines fonctionnalités sont activées et l’activité est envoyée au système.

Le code ci-dessus peut être amélioré pour inclure les métadonnées qui fournissent le contexte à l’activité en effectuant les modifications suivantes :

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

Si le développeur dispose d’un site Web capable d’afficher les mêmes informations que l’application, l’application peut inclure l’URL et le contenu peut être affiché sur d’autres appareils sur lesquels l’application n’est pas installée (par le biais du transfert) :

```csharp
// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");
```

### <a name="restoring-an-activity"></a>Restauration d’une activité

Pour répondre à l’utilisateur en appuyant sur un résultat de recherche ( `NSUserActivity` ) pour l’application, modifiez le fichier **AppDelegate.cs** et remplacez la `ContinueUserActivity` méthode. Par exemple :

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

Vérifiez qu’il s’agit du même identificateur de type d’activité ( `com.xamarin.platform` ) que l’activité créée ci-dessus. L’application utilise les informations stockées dans le `NSUserActivity` pour restaurer l’État à l’emplacement où l’utilisateur s’est arrêté.

### <a name="benefits-of-creating-an-activity"></a>Avantages de la création d’une activité

Avec la quantité minimale de code présentée ci-dessus, l’application peut désormais tirer parti de trois nouvelles fonctionnalités iOS 10 :

- **Handoff**
- **Recherche Spotlight**
- **Rappels Siri contextuels**

La section suivante présente l’activation de deux autres nouvelles fonctionnalités iOS 10 :

- **Suggestions d’emplacement**
- **Requêtes Siri contextuelles**

### <a name="location-based-suggestions"></a>Suggestions basées sur l’emplacement 

Prenons l’exemple de l’application restaurant Search ci-dessus. S’il a implémenté `NSUserActivity` et correctement rempli tous les métadonnées et attributs, l’utilisateur peut effectuer les opérations suivantes :

1. Trouvez un restaurant dans l’application à laquelle il souhaite atteindre un ami.
2. Si l’utilisateur bascule vers l’application Maps, l’adresse du restaurant est automatiquement suggérée comme destination.
3. Cela fonctionne même pour les applications tierces (qui prennent en charge `NSUserActivity` ). l’utilisateur peut donc basculer vers une application de partage d’emplacement et l’adresse du restaurant est également suggérée ici comme destination.
4. Il fournit également le contexte à Siri, de sorte que l’utilisateur peut appeler Siri au sein de l’application restaurant et demander *« obtenir des directions... »* et Siri fournit des instructions au restaurant que l’utilisateur visualise.

Toutes les fonctionnalités ci-dessus ont une chose en commun, elles indiquent toutes l’emplacement d’origine de la suggestion. Dans le cas de l’exemple ci-dessus, il s’agit de l’application fictive de revue de restaurant.

Watchos 3 a été amélioré pour activer cette fonctionnalité pour une application via plusieurs petites modifications et ajouts aux Frameworks existants :

- `NSUserActivity`contient des champs supplémentaires pour la capture des informations d’emplacement affichées à l’intérieur de l’application.
- Plusieurs ajouts ont été apportés à MapKit et CoreSpotlight pour capturer l’emplacement.
- La fonctionnalité de prise en charge de l’emplacement a été ajoutée à Siri, Maps, multitâche et à d’autres applications dans le système.

Pour implémenter des suggestions basées sur l’emplacement, commencez avec le même code d’activité que celui présenté ci-dessus :

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

Si l’application utilise MapKit, il est aussi simple d’ajouter la carte actuelle `MKMapItem` à l’activité :

```csharp
// Save MKMapItem location
activity.MapItem = myMapItem;
```

Si l’application n’utilise pas MapKit, elle peut adopter la recherche d’application et spécifier les nouveaux attributs suivants pour l’emplacement :

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

Jetez un coup d’œil au code ci-dessus. Tout d’abord, le nom de l’emplacement est requis dans chaque instance :

```csharp
attributes.NamedLocation = "Apple Inc.";
```

Ensuite, la description textuelle dans requise pour les instances basées sur le texte (par exemple, le clavier QuickType) :

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

La latitude et la longitude sont facultatives, mais assurez-vous que l’utilisateur est acheminé vers l’emplacement exact auquel l’application souhaite l’envoyer :

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

En définissant les numéros de téléphone, l’application peut accéder à Siri, de sorte que l’utilisateur peut appeler Siri à partir de l’application en disant « appeler ce lieu » :

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

Enfin, l’application peut indiquer si l’instance est appropriée pour la navigation et les appels téléphoniques :

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

## <a name="activities-best-practices"></a>Meilleures pratiques relatives aux activités

Apple suggère les meilleures pratiques suivantes lorsque vous travaillez avec des activités :

- Utilisez `NeedsSave` pour les mises à jour de charge utile tardive.
- Veillez à conserver une référence forte à l’activité en cours.
- Transférez uniquement les petites charges utiles qui incluent juste assez d’informations pour restaurer l’État.
- Vérifiez que les identificateurs de type d’activité sont uniques et descriptifs en utilisant la notation inversée DNS pour les spécifier. 

## <a name="consuming-location-suggestions"></a>Utilisation de suggestions d’emplacement

La section suivante aborde la consommation de suggestions d’emplacement provenant d’autres parties du système (telles que l’application Maps) ou d’autres applications tierces.

## <a name="routing-apps-and-locations-suggestions"></a>Suggestions relatives aux applications et aux emplacements de routage

Cette section examine l’utilisation des suggestions d’emplacement directement dans une application de routage. Pour que l’application de routage ajoute cette fonctionnalité, le développeur doit tirer parti de l' `MKDirectionsRequest` infrastructure existante comme suit :

- Pour promouvoir l’application dans le multitâche.
- Pour inscrire l’application en tant qu’application de routage.
- Pour gérer le lancement de l’application avec un `MKDirectionsRequest` objet MapKit.
- Donnez à Watchos la possibilité d’apprendre à suggérer l’application en fonction de l’engagement de l’utilisateur.

Lorsque l’application est démarrée avec un `MKDirectionsRequest` objet MapKit, elle doit automatiquement commencer à donner à l’utilisateur le sens de l’emplacement demandé ou présenter une interface utilisateur qui permet à l’utilisateur de commencer à obtenir des instructions plus facilement. Par exemple :

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

Jetez un coup d’œil sur ce code en détail. Il effectue un test pour déterminer s’il s’agit d’une demande de destination valide :

```csharp
if (MKDirectionsRequest.IsDirectionsRequestUrl(url)) {
```

Si c’est le cas, il crée un `MKDirectionsRequest` à partir de l’URL :

```csharp
var request = new MKDirectionsRequest(url);
```

Nouveauté dans Watchos 3, l’application peut recevoir une adresse qui n’a pas de coordonnées géographiques, dans ce cas, le développeur doit encoder l’adresse :

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address

});

```

## <a name="summary"></a>Résumé

Cet article a présenté des suggestions proactives et a montré comment le développeur peut les utiliser pour diriger le trafic vers une application Xamarin. iOS pour Watchos. Il a abordé la marche à suivre pour implémenter des suggestions proactives et présenter des instructions d’utilisation.

## <a name="related-links"></a>Liens associés

- [Exemples watchOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+watchOS)
- [Guide de programmation SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
