---
title: Présentation des suggestions proactives dans Xamarin. iOS
description: Cet article explique comment utiliser des suggestions proactives dans l’application Xamarin. iOS pour conduire un engagement en permettant au système de présenter de manière proactive des informations utiles à l’utilisateur.
ms.prod: xamarin
ms.assetid: 8DDD084A-0D1E-4DF7-B686-6309DCEFF5D3
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: b79f64f154dbd7dde623d13385f111d3d5a5d3f2
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769549"
---
# <a name="introduction-to-proactive-suggestions-in-xamarinios"></a>Présentation des suggestions proactives dans Xamarin. iOS

_Cet article explique comment utiliser des suggestions proactives dans l’application Xamarin. iOS pour conduire un engagement en permettant au système de présenter de manière proactive des informations utiles à l’utilisateur._

Nouveauté d’iOS 10, les suggestions proactives présentent de nouvelles méthodes permettant aux utilisateurs de s’associer à une application Xamarin. iOS en présentant de manière proactive des informations utiles automatiquement à l’utilisateur à des moments opportuns.

iOS 10 présente de nouvelles méthodes de conduite de l’engagement à l’application en permettant au système de présenter de manière proactive des informations utiles à l’utilisateur à des moments opportuns. Tout comme iOS 9 offrait la possibilité d’ajouter une recherche poussée à l’application à l’aide des suggestions Spotlight, de transfert et de Siri (voir les [nouvelles API de recherche](~/ios/platform/search/index.md)), avec iOS 10, une application peut exposer les fonctionnalités qui peuvent être présentées à l’utilisateur par le système depuis les emplacements suivants :

- Le sélecteur d’application
- L’écran de verrouillage
- CarPlay
- Cartes
- Interactions Siri
- Suggestions QuickType

L’application expose cette fonctionnalité au système à l’aide d’une collection de technologies `NSUserActivity`telles que, le balisage Web, Core Spotlight, MapKit, Media Player et uikit. En outre, en fournissant une prise en charge proactive des suggestions pour l’application, l’intégration Siri est plus poussée gratuitement.

## <a name="location-based-suggestions"></a>Suggestions basées sur l’emplacement

Nouveauté d’iOS 10, la `NSUserActivity` classe comprend une `MapItem` propriété qui permet au développeur de fournir des informations d’emplacement qui peuvent être utilisées dans d’autres contextes. Par exemple, si l’application affiche des revues de restaurant, le développeur peut `MapItem` définir la propriété sur l’emplacement du restaurant que l’utilisateur visualise dans l’application. Si l’utilisateur bascule vers l’application Maps, l’emplacement du restaurant est automatiquement disponible.

Si l’application prend en charge la recherche d’application, elle peut utiliser les nouveaux `CSSearchableItemAttributesSet` composants d’adresse de la classe pour spécifier les emplacements que l’utilisateur peut vouloir visiter. En définissant `MapItem` la propriété, les autres propriétés sont renseignées automatiquement.

En plus de définir les `Latitude` propriétés `Longitude` du composant Address et de l’adresse, il est recommandé que l’application `NamedLocation` fournisse également les propriétés et `PhoneNumbers` , de sorte que Siri puisse lancer un appel à l’emplacement.

## <a name="web-markup-based-suggestions"></a>Suggestions basées sur le balisage Web

iOS 9 a ajouté la possibilité d’inclure une balise de données structurées dans le site Web qui enrichit le contenu que les utilisateurs voient dans les résultats de recherche Spotlight et Safari (voir [recherche avec les balises Web](~/ios/platform/search/web-markup.md)). iOS 10 ajoute la possibilité d’inclure le balisage basé sur l’emplacement (par exemple, [PostalAddress](http://schema.org/PostalAddress) tel que défini par [Schema.org](http://schema.org/)) pour améliorer l’expérience de l’utilisateur. Par exemple, si un utilisateur affiche une page d’emplacement marquée sur le site Web, le système peut suggérer le même emplacement lorsqu’il ouvre Maps.

## <a name="text-based-suggestions"></a>Suggestions basées sur le texte

UIKit a été développé dans iOS 10 pour inclure la propriété [TextContentType](https://developer.apple.com/reference/uikit/uitextinputtraits/1649656-textcontenttype) de la classe [UITextInputTraits](https://developer.apple.com/reference/uikit/uitextinputtraits) afin de spécifier la signification sémantique du contenu dans une zone de texte. Une fois ces informations en place, le système peut généralement sélectionner automatiquement le type de clavier approprié, améliorer les suggestions de correction automatique et intégrer de manière proactive les informations provenant d’autres applications et sites Web.

Par exemple, si l’utilisateur entre du texte dans un champ de texte `UITextContentType.FullStreetAddress`marqué, le système peut suggérer la saisie semi-automatique du champ avec l’emplacement que l’utilisateur affichait récemment.

## <a name="media-based-suggestions"></a>Suggestions basées sur des médias

Si l’application lit le média à l’aide de l’API [MPPlayableContentManager](xref:MediaPlayer.MPPlayableContentManager) , iOS 10 permet aux utilisateurs d’afficher l’image de l’album et de lire le média par le biais de l’application sur l’écran de verrouillage.

## <a name="contextual-siri-reminders"></a>Rappels Siri contextuels

Permet à l’utilisateur d’utiliser Siri pour créer rapidement un rappel afin d’afficher le contenu actuellement affiché dans l’application à une date ultérieure. Par exemple, s’ils visualisaient un avis de restaurant dans l’application, ils pouvaient appeler Siri et *« me le rappeler lorsque je suis à la page. »* Siri génère le rappel avec un lien vers la revue dans l’application.

## <a name="contact-based-suggestions"></a>Suggestions basées sur les contacts

Permet aux contacts de l’application (et aux informations associées de contacter) de s’afficher dans l’application de **contact** sur l’appareil iOS avec tous les contacts enregistrés dans le système.

## <a name="ride-sharing-based-suggestions"></a>Ignorer les suggestions basées sur le partage

Si une application de partage de la casse utilise l’API [MKDirectionsRequest](xref:MapKit.MKDirectionsRequest) , iOS 10 la présente comme option dans le sélecteur d’applications à des moments où l’utilisateur est susceptible de vouloir effectuer une opération de remplacement. L’application doit également être inscrite en tant qu’application de partage de code `MKDirectionsModeRideShare` en spécifiant pour la clé `Info.plist` [MKDirectionsApplicationSupportedModes](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html) dans son fichier.

Si l’application prend uniquement en charge le partage d’une seule fois, la suggestion du système commence par *« obtenir une valeur... »* , si d’autres types de direction du routage (tels que le parcours ou le vélo) sont pris en charge, le système utilise *« obtenir des directions vers... ».*

> [!IMPORTANT]
> L’objet [MKMapItem](xref:MapKit.MKMapItem) que l’application reçoit peut ne pas inclure d’informations de longitude et de latitude et nécessitera le géocodage.

## <a name="implementing-proactive-suggestions"></a>Implémentation de suggestions proactives

L’ajout de la prise en charge de suggestions proactives à une application Xamarin. iOS est généralement aussi simple que d’implémenter quelques API ou de s’étendre sur quelques API que l’application peut déjà implémenter.

Les suggestions proactives fonctionnent avec les applications de trois manières principales :

- **`NSUserActivity`et**  -  Schema.org`NSUserActivity` aide le système à comprendre quelles informations l’utilisateur travaille actuellement sur l’écran. Schema.org ajoute des fonctionnalités similaires aux pages Web.
- **Suggestions d’emplacement** : si l’application offre ou consomme des informations sur l’emplacement, ces extensions d’API offrent de nouvelles façons de partager ces informations entre les applications.
- **Suggestions d’applications multimédias** : le système peut promouvoir l’application et son contenu multimédia en fonction du contexte de l’interaction de l’utilisateur avec l’appareil iOS.

Et est pris en charge dans l’application en implémentant les éléments suivants :

- **La remise** - `NSUserActivity` a été ajoutée dans iOS 8 pour prendre en charge le transfert, ce qui permet au développeur de démarrer une activité sur un appareil, puis de le poursuivre sur un autre (voir [présentation du transfert](~/ios/platform/handoff.md)).
- **Recherche Spotlight** -iOS 9 a ajouté la possibilité de promouvoir le contenu de l’application dans les résultats `NSUserActivity` de la recherche Spotlight à l’aide de (consultez la page [Rechercher avec un projecteur principal](~/ios/platform/search/corespotlight.md)).
- Les **rappels Siri contextuels** -dans iOS `NSUserActivity` 10, ont été développés pour permettre à Siri de créer rapidement un rappel pour afficher le contenu actuellement affiché par l’utilisateur dans l’application à une date ultérieure.
- **Suggestions d’emplacement** : iOS 10 `NSUserActivity` s’améliore pour capturer les emplacements affichés dans l’application et les promouvoir à de nombreux endroits dans le système.
- **Les requêtes**  -  Siricontextuellesfournissentuncontexteauxinformationsprésentéesàl’intérieurdel’applicationàSiriafinquel’utilisateurpuisseobtenirdesinstructionsoupasserunappelenappelantSiriàpartirdel’application.`NSUserActivity`
- **Interactions de contact** -nouveauté dans iOS 10 `NSUserActivity` , permet de promouvoir les applications de communication à partir d’une carte de visite (dans l’application contacts) en tant que méthode de communication alternative.

Toutes ces fonctionnalités ont une chose en commun, elles sont toutes utilisées `NSUserActivity` dans un formulaire ou une autre pour fournir leurs fonctionnalités. 

## <a name="nsuseractivity"></a>NSUserActivity

Comme indiqué ci- `NSUserActivity` dessus, le système aide le système à comprendre quelles informations l’utilisateur travaille actuellement sur l’écran. `NSUserActivity`est un mécanisme de mise en cache d’État léger qui permet de capturer l’activité de l’utilisateur au fur et à mesure qu’il navigue dans l’application. Par exemple, en examinant une application de restaurant :

[![](proactive-suggestions-images/activity02.png "Mécanisme de mise en cache de l’État léger NSUserActivity")](proactive-suggestions-images/activity02.png#lightbox)

Avec les interactions suivantes :

1. Lorsque l’utilisateur travaille avec l’application, un `NSUserActivity` est créé pour recréer l’état de l’application ultérieurement.
2. Si l’utilisateur recherche un restaurant, le même modèle de création d’activités est suivi.
3. Là encore, lorsque l’utilisateur affiche un résultat. Dans ce dernier cas, l’utilisateur visualise un emplacement et dans iOS 10, le système est plus conscient de certains concepts (tels que l’emplacement ou les interactions de communication).

Examinez de plus près le dernier écran :

[![](proactive-suggestions-images/activity03.png "Détails du NSUserActivity")](proactive-suggestions-images/activity03.png#lightbox)

Ici, l’application crée un `NSUserActivity` et contient des informations pour recréer l’État ultérieurement. L’application a également inclus des métadonnées telles que le nom et l’adresse de l’emplacement. Une fois cette activité créée, l’application permet à iOS de savoir qu’elle représente l’état actuel de l’utilisateur.

L’application décide ensuite si l’activité sera publiée par voie hertzienne pour le transfert, enregistrée comme valeur temporaire pour les suggestions d’emplacement ou ajoutée à l’index Spotlight sur l’appareil pour l’affichage dans les résultats de la recherche.

Pour plus d’informations sur le transfert et la recherche Spotlight, consultez notre [Introduction à la livraison](~/ios/platform/handoff.md) et les [nouveaux guides des API de recherche iOS 9](~/ios/platform/search/index.md) .

### <a name="creating-an-activity"></a>Création d’une activité

Avant de créer une activité, vous devez créer un identificateur de type d’activité pour l’identifier. L’identificateur de type d’activité est une chaîne abrégée ajoutée `NSUserActivityTypes` au tableau du `Info.plist` fichier de l’application utilisée pour identifier de manière unique un type d’activité utilisateur donné. Il y aura une entrée dans le tableau pour chaque activité que l’application prend en charge et expose à la recherche d’applications. Pour plus d’informations, consultez notre référence sur la [création d’identificateurs de type d’activité](~/ios/platform/search/nsuseractivity.md) .

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

Pour répondre à l’utilisateur en appuyant sur un résultat`NSUserActivity`de recherche () pour l’application, modifiez le fichier **AppDelegate.cs** et `ContinueUserActivity` remplacez la méthode. Par exemple :

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

Le développeur doit s’assurer qu’il s’agit du même identificateur de type d'`com.xamarin.platform`activité () que l’activité créée ci-dessus. L’application utilise les informations stockées dans le `NSUserActivity` pour restaurer l’État à l’emplacement où l’utilisateur s’est arrêté.

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
2. Lorsque l’utilisateur quitte l’application à l’aide du sélecteur d’applications multitâche, le système affiche automatiquement une suggestion (en bas de l’écran) pour obtenir des instructions sur le restaurant à l’aide de son application de navigation préférée.
3. Si l’utilisateur bascule vers l’application messages et commence à taper *« allons-y répondre »* , le clavier QuickType suggère automatiquement le collage dans l’adresse du restaurant.
4. Si l’utilisateur bascule vers l’application Maps, l’adresse du restaurant est automatiquement suggérée comme destination.
5. Cela fonctionne même pour les applications tierces (qui `NSUserActivity`prennent en charge). l’utilisateur peut donc basculer vers une application de partage d’emplacement et l’adresse du restaurant est également suggérée ici comme destination.
6. Il fournit également le contexte à Siri, de sorte que l’utilisateur peut appeler Siri au sein de l’application restaurant et demander *« obtenir des directions... »* et Siri fournit des instructions au restaurant que l’utilisateur visualise.

Toutes les fonctionnalités ci-dessus ont une chose en commun, elles indiquent toutes l’emplacement d’origine de la suggestion. Dans le cas de l’exemple ci-dessus, il s’agit de l’application fictive de revue de restaurant.

iOS 10 a été amélioré pour activer cette fonctionnalité pour une application via plusieurs petites modifications et ajouts aux Frameworks existants :

- `NSUserActivity`contient des champs supplémentaires pour la capture des informations d’emplacement affichées à l’intérieur de l’application.
- Plusieurs ajouts ont été apportés à MapKit et CoreSpotlight pour capturer l’emplacement.
- La fonctionnalité de prise en charge de l’emplacement a été ajoutée à Siri, Maps, claviers, multitâche et à d’autres applications dans le système.

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

Si l’application utilise MapKit, il est aussi simple d’ajouter la carte `MKMapItem` actuelle à l’activité :

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

Ensuite, la description textuelle de l’emplacement est requise pour les instances de type texte (par exemple, le clavier QuickType) :

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

La latitude et la longitude sont facultatives, mais assurez-vous que l’utilisateur est acheminé vers l’emplacement exact auquel l’application souhaite l’envoyer. il doit donc être inclus :

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

En définissant les numéros de téléphone, l’application peut accéder à Siri pour que l’utilisateur puisse appeler Siri à partir de l’application en disant *« appeler ce lieu »* :

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

Enfin, l’application peut indiquer si l’instance est appropriée pour la navigation et les appels téléphoniques :

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

### <a name="implementing-contact-interactions"></a>Implémentation des interactions entre les contacts

Nouveauté d’iOS 10, les applications de communication sont profondément intégrées à l’application contacts à partir de la carte de visite. Pour les applications qui ont implémenté des interactions de contact, l’utilisateur peut ajouter les informations de l’application donnée à des personnes spécifiques dans leurs contacts. Et si, par exemple, ils cliquent sur le bouton d’action rapide en haut d’une carte pour envoyer un message, l’application jointe est indiquée comme une option à partir de laquelle envoyer le message.

Si une application tierce est sélectionnée, elle peut être mémorisée et présentée comme méthode par défaut pour envoyer un message à la personne donnée la prochaine fois que l’utilisateur souhaite les contacter.

Les interactions entre les contacts sont implémentées `NSUserActivity` dans l’application à l’aide de et de la nouvelle infrastructure d’intentions introduite dans iOS 10. Pour plus d’informations sur l’utilisation des intentions, consultez notre [compréhension des concepts SiriKit](~/ios/platform/sirikit/understanding-sirikit.md) et [implémentation](~/ios/platform/sirikit/implementing-sirikit.md) des guides SiriKit.

#### <a name="donating-interactions"></a>Don d’interactions

Jetez un coup d’œil sur la façon dont l’application peut faire don d’interactions :

[![](proactive-suggestions-images/activity04.png "Présentation du don d’interactions")](proactive-suggestions-images/activity04.png#lightbox)

L’application crée un `INInteraction` objet qui contient une **intention** (`INIntent`), des **participants** et des **métadonnées**. L' **intention** représente une action de l’utilisateur, telle que la création d’un appel vidéo ou l’envoi d’un SMS. Les **participants** incluent les personnes qui reçoivent la communication. Les **métadonnées** définissent des informations supplémentaires, telles que l’envoi réussi du message, etc.

Le développeur ne crée jamais directement une instance `INIntent` de `INIntentResponse`ou, il utilise l’une des classes enfants spécifiques (en fonction de la tâche que l’application accomplit pour le compte de l’utilisateur) qui héritent de ces classes parentes. Par exemple, `INSendMessageIntent` et `INSendMessageIntentResponse` pour l’envoi d’un message texte. 

Une fois que l’interaction est entièrement remplie, `DonateInteraction` appelez la méthode pour informer le système que l’interaction peut être utilisée.

Lorsque l’utilisateur interagit avec l’application à partir de la carte de visite, l’interaction est regroupée avec un `NSUserActivity`, qui est ensuite utilisé pour lancer l’application :

[![](proactive-suggestions-images/activity05.png "L’interaction est regroupée avec un NSUserActivity qui est utilisé pour lancer l’application")](proactive-suggestions-images/activity05.png#lightbox)

Jetez un coup d’œil à l’exemple suivant d’une intention d’envoi de message :

```csharp
using System;
using Foundation;
using UIKit;
using Intents;

namespace MonkeyNotification
{
  public class DonateInteraction
  {
    #region Constructors
    public DonateInteraction ()
    {
    }
    #endregion

    #region Public Methods
    public void SendMessageIntent (string text, INPerson from, INPerson[] to)
    {

      // Create App Activity
      var activity = new NSUserActivity ("com.xamarin.message");

      // Define details
      var info = new NSMutableDictionary ();
      info.Add (new NSString ("message"), new NSString (text));

      // Populate Activity
      activity.Title = "Sent MonkeyChat Message";
      activity.UserInfo = info;

      // Enable capabilities
      activity.EligibleForSearch = true;
      activity.EligibleForHandoff = true;
      activity.EligibleForPublicIndexing = true;

      // Inform system of Activity
      activity.BecomeCurrent ();

      // Create message Intent
      var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);

      // Create Intent Reaction
      var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);

      // Create interaction
      var interaction = new INInteraction (intent, response);

      // Donate interaction to the system
      interaction.DonateInteraction ((err) => {
        // Handle donation error
        ...
      });
    }
    #endregion
  }
}
```

En examinant ce code en détail, il crée et remplit une instance de `NSUserActivity` (comme indiqué dans la section [création d’une activité](#creating-an-activity) ci-dessus). Ensuite, il crée une instance de `INSendMessageIntent` (qui hérite de `INIntent`) et la remplit avec les détails du message envoyé :

```csharp
var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);
```

Un `INSendMessageIntentResponse` est créé et reçoit le `NSUserActivity` créé ci-dessus :

```csharp
var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);
```

Un `INInteraction` est construit à partir de l’intention du`INSendMessageIntent`message Send ()`INSendMessageIntentResponse`et de la réponse () que vous venez de créer :

```csharp
var interaction = new INInteraction (intent, response);
```

Enfin, le système est averti de l’interaction :

```csharp
// Donate interaction to the system
interaction.DonateInteraction ((err) => {
  // Handle donation error
  ...
});
```

Un gestionnaire d’achèvement est passé là où l’application peut répondre à la réussite ou à l’échec de la donation.

### <a name="activities-best-practices"></a>Meilleures pratiques relatives aux activités

Apple suggère les meilleures pratiques suivantes lorsque vous travaillez avec des activités :

- Utilisez `NeedsSave` pour les mises à jour de charge utile tardive.
- Veillez à conserver une référence forte à l’activité en cours.
- Transférez uniquement les petites charges utiles qui incluent juste assez d’informations pour restaurer l’État.
- Vérifiez que les identificateurs de type d’activité sont uniques et descriptifs en utilisant la notation inversée DNS pour les spécifier. 

## <a name="schemaorg"></a>Schema.org

Comme indiqué ci- `NSUserActivity` dessus, le système aide le système à comprendre quelles informations l’utilisateur travaille actuellement sur l’écran. Schema.org ajoute des fonctionnalités similaires aux pages Web.

Schema.org peut fournir les mêmes types d’interactions basées sur l’emplacement au site Web. Apple a conçu les nouvelles suggestions d’emplacement pour fonctionner tout aussi bien lorsqu’il est affiché dans Safari comme c’est le cas dans une application native.

En arrière-plan Schema.org :

- Il fournit une norme Open Web Markup vocabulaire standard.
- Elle fonctionne en incluant des métadonnées structurées sur des pages Web.
- Il existe plus de 500 schémas représentant les différents concepts disponibles.
- En l’implémentant sur le site Web, le développeur peut acquérir certains des avantages de `NSUserActivity` l’utilisation de dans une application native.

Les schémas sont organisés dans une structure de type arborescence, où des types spécifiques tels que le *restaurant*, héritent d’autres types génériques tels que *les activités locales*. Pour plus d’informations, consultez [Schema.org](http://schema.org).

Par exemple, si la page Web inclut les données suivantes :

```html
<script type="application/ld+json">
{
  "@context":"http://schema.org",
  "@type":"Restaurant",
  "telephone":"(415) 781-1111",
  "url":"https://www.yanksing.com",
  "address":{
    "@type":"PostalAddress",
    "streetAddress":"101 Spear St",
    "addressLocality":"San Francisco",
    "postalCode":"94105",
    "addressRegion":"CA"
  },
  "aggregateRating":{
    "@type":"AggregateRating",
    "ratingValue":"3.5",
    "reviewCount":"2022"
  }
}
</script>
```

Si l’utilisateur a visité cette page dans Safari, puis a basculé vers une autre application, les informations d’emplacement de la page sont capturées et proposées comme suggestion d’emplacement dans d’autres parties du système (comme indiqué dans les activités ci-dessus).

Safari extraira tout ce qui se trouve sur une page Web conforme à l’une des propriétés de schéma suivantes :

- **Property**
- **GeoCoordinates**
- Propriété de téléphone.

Pour plus d’informations, consultez notre guide de [recherche avec les balises Web](~/ios/platform/search/web-markup.md) .

## <a name="consuming-location-suggestions"></a>Utilisation de suggestions d’emplacement

La section suivante aborde la consommation de suggestions d’emplacement provenant d’autres parties du système (telles que l’application Maps) ou d’autres applications tierces.

L’application peut utiliser des suggestions d’emplacement de deux manières principales :

- Via le clavier QuickType.
- Directement en consommant les informations d’emplacement dans les applications de routage.

### <a name="location-suggestions-and-the-quicktype-keyboard"></a>Suggestions d’emplacement et le clavier QuickType

Si l’application traite des adresses dans des formats textuels, l’application peut tirer parti des suggestions d’emplacement via l’interface utilisateur QuickType. iOS 10 développe l’interface utilisateur QuickType avec les fonctionnalités suivantes :

- L’application peut ajouter des indications sur l’intention sémantique des champs de texte dans l’interface utilisateur.
- L’application peut obtenir des suggestions proactives dans l’application.
- L’application peut tirer parti de la correction automatique améliorée.

La nouvelle `TextContentType` propriété des contrôles de champ de texte dans iOS 10 permet au développeur de définir l’intention sémantique de la valeur que l’utilisateur va entrer dans un champ donné. Par exemple :

```csharp
var textField = new UITextField();
textField.TextContentType = UITextContentType.FullStreetAddress;
```

Indique au système que l’application s’attend à ce que l’utilisateur entre une adresse postale complète dans le champ donné. Cela permettra au clavier QuickType de fournir automatiquement des suggestions d’emplacement sur le clavier lorsque l’utilisateur entre une valeur dans ce champ.

Voici quelques-uns des types les plus courants disponibles pour le développeur dans la `UITextContentType` classe statique :

- `Name`
- `GivenName`
- `FamilyName`
- `Location`
- `FullStreetAddress`
- `AddressCityAndState`
- `TelephoneNumber`
- `EmailAddress`

### <a name="routing-apps-and-locations-suggestions"></a>Suggestions relatives aux applications et aux emplacements de routage

Cette section examine l’utilisation des suggestions d’emplacement directement dans une application de routage. Pour que l’application de routage ajoute cette fonctionnalité, le développeur doit tirer parti de `MKDirectionsRequest` l’infrastructure existante comme suit :

- Pour promouvoir l’application dans le multitâche.
- Pour inscrire l’application en tant qu’application de routage.
- Pour gérer le lancement de l’application avec `MKDirectionsRequest` un objet MapKit.
- Pour donner à iOS la possibilité d’apprendre à suggérer l’application à l’utilisateur à des moments opportuns, en fonction de l’engagement de l’utilisateur.

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

Si c’est le cas, il crée `MKDirectionsRequest` un à partir de l’URL :

```csharp
var request = new MKDirectionsRequest(url);
```

Nouveauté dans iOS 10, l’application peut recevoir une adresse qui n’a pas de coordonnées géographiques, dans ce cas, le développeur doit encoder l’adresse :

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
  // Handle the display of the address
  
});

```

## <a name="media-app-suggestions"></a>Suggestions d’applications multimédias

Si l’application gère un type de support comme une application de podcast ou un contenu multimédia en continu, tel qu’un fichier audio ou vidéo, avec iOS 10, il peut tirer parti des suggestions de médias dans le système.

Pour les applications qui gèrent des médias, iOS prend en charge les comportements suivants :

- iOS encourage les applications que l’utilisateur est susceptible d’utiliser en fonction de son comportement précédent.
- Les suggestions relatives à l’application sont présentées dans Spotlight et dans la vue aujourd’hui.
- Si l’application répond à l’un des déclencheurs suivants, elle peut être élevée à une suggestion d’écran de verrouillage :
  - Une fois le casque branché ou un périphérique Bluetooth établit une connexion.
  - Après avoir obtenu une voiture.
  - Après avoir arrivé à chez vous ou au travail. 

En incluant un appel d’API simple dans iOS 10, le développeur peut créer une expérience d’écran de verrouillage plus attrayante pour les utilisateurs de l’application multimédia. En utilisant la `MPPlayableContentManager` classe pour gérer la lecture du média, les contrôles multimédias complets (comme ceux présentés par l’application musique) sont présentés sur l’écran de verrouillage de l’application.

```csharp
using System;
using MediaPlayer;
using UIKit;

namespace MonkeyPlayer
{
  public class PlayableContentDelegate : MPPlayableContentDelegate
  {
    #region Constructors
    public PlayableContentDelegate ()
    {
    }
    #endregion

    #region Override methods
    public override void InitiatePlaybackOfContentItem (MPPlayableContentManager contentManager, Foundation.NSIndexPath indexPath, Action<Foundation.NSError> completionHandler)
    {
      // Access the media item to play
      var item = LoadMediaItem (indexPath);

      // Populate the lock screen
      PopulateNowPlayingItem (item);

      // Prep item to be played
      var status = PreparePlayback (item);

      // Call completion handler
      completionHandler (null);
    }
    #endregion

    #region Public Methods
    public MPMediaItem LoadMediaItem (Foundation.NSIndexPath indexPath)
    {
      var item = new MPMediaItem ();

      // Load item from media store
      ...

      return item;
    }

    public void PopulateNowPlayingItem (MPMediaItem item)
    {
      // Get Info Center and album art
      var infoCenter = MPNowPlayingInfoCenter.DefaultCenter;
      var albumArt = (item.Artwork == null) ? new MPMediaItemArtwork (UIImage.FromFile ("MissingAlbumArt.png")) : item.Artwork;

      // Populate Info Center
      infoCenter.NowPlaying.Title = item.Title;
      infoCenter.NowPlaying.Artist = item.Artist;
      infoCenter.NowPlaying.AlbumTitle = item.AlbumTitle;
      infoCenter.NowPlaying.PlaybackDuration = item.PlaybackDuration;
      infoCenter.NowPlaying.Artwork = albumArt;
    }

    public bool PreparePlayback (MPMediaItem item)
    {
      // Prepare media item for playback
      ...

      // Return results
      return true;
    }
    #endregion
  }
}
```

## <a name="summary"></a>Récapitulatif

Cet article a présenté des suggestions proactives et a montré comment le développeur peut les utiliser pour diriger le trafic vers l’application Xamarin. iOS. Il a abordé la marche à suivre pour implémenter des suggestions proactives et présenter des instructions d’utilisation.

## <a name="related-links"></a>Liens associés

- [Exemples iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
- [Guide de programmation SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
