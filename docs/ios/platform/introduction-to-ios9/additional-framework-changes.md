---
title: Modifications supplémentaires des frameworks iOS 9
description: Ce document décrit les modifications d’infrastructure supplémentaires introduites dans iOS 9. Il aborde AVFoundation, AVKit et CloudKit.
ms.prod: xamarin
ms.assetid: CFDE1FC4-9327-402B-95A0-581D4AA0E9D5
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: cb16ad3889453f866f3dc9e66f5bcc8860bd094a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70751982"
---
# <a name="additional-ios-9-frameworks-changes"></a>Modifications supplémentaires des frameworks iOS 9

_Cet article traite des modifications supplémentaires ou des améliorations apportées aux infrastructures existantes pour iOS 9._

[![](additional-framework-changes-images/ios9-sml.png "Logo iOS 9")](additional-framework-changes-images/ios9.png#lightbox)

Outre les modifications majeures apportées à iOS, Apple a apporté des modifications et des améliorations à plusieurs infrastructures existantes dans iOS 9.

## <a name="avfoundation-framework-additions"></a>Ajouts du Framework AVFoundation

Dans l’infrastructure AVFoundation, la classe [AVSpeechSynthesisVoice](xref:AVFoundation.AVSpeechSynthesisVoice) vous permet désormais de spécifier une voix par identificateur en plus de la langue.

Par exemple, le code suivant obtient une liste de toutes les voix disponibles :

```csharp
var voices = AVSpeechSynthesisVoice.GetSpeechVoices ();
```

Vous pouvez ensuite utiliser l’une des voix de la liste en la définissant comme `Voice` la propriété d’une instance de la classe [AVSpeachUtterance](xref:AVFoundation.AVSpeechUtterance) .

La classe [AVQueuePlayer](xref:AVFoundation.AVQueuePlayer) prend désormais en charge un mélange de diffusion Internet et de média basé sur des fichiers dans la file d’attente. Les versions précédentes pouvaient uniquement faire une file d’attente du même type.

Pour plus d’informations, consultez la [référence AVSpeechSynthesisVoice](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVSpeechSynthesisVoice_Ref/index.html#//apple_ref/occ/cl/AVSpeechSynthesisVoice)d’Apple.

## <a name="avkit-framework-additions"></a>Ajouts du Framework AVKit

Pour utiliser la nouvelle fonctionnalité d’incrustation d’image (PIP), l’infrastructure AVKit comprend les nouvelles `AVPictureInPictureController` classes et [AVPlayerViewController](xref:AVKit.AVPlayerViewController) :

- **AVPictureInPictureController** : cette classe permet à une application iOS 9 de répondre à l’utilisateur qui lance la lecture d’une vidéo dans une fenêtre PIP flottante et redimensionnable sur un iPad.
- **AVPlayerViewController** : gère un `AVPlayer` contrôleur utilisé pour présenter une vidéo dans une fenêtre PIP flottante et redimensionnable sur un iPad.

Pour plus d’informations, consultez notre documentation [sur les multitâches pour iPad](~/ios/platform/introduction-to-ios9/index.md#multitasking) et la [référence AVPictureInPictureController](https://developer.apple.com/library/prerelease/ios/documentation/AVKit/Reference/AVPictureInPictureController_Class/index.html#//apple_ref/occ/cl/AVPictureInPictureController) et [AVPlayerViewController](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVPlayerViewController_Class/index.html#//apple_ref/occ/cl/AVPlayerViewController)de référence d’Apple.

## <a name="introducing-cloudkit-web-services"></a>Présentation des services Web CloudKit

L’infrastructure CloudKit simplifie le développement d’applications qui accèdent à iCloud. Cela comprend la récupération des données d’application et des droits d’actifs, ainsi que la possibilité de stocker des informations d’application en toute sécurité. Ce kit offre aux utilisateurs une couche d’anonymat en autorisant l’accès aux applications avec leurs ID iCloud sans partager les informations personnelles.

La nouvelle infrastructure de _services Web CloudKit_ fournit une bibliothèque JavaScript (CloudKit js) qui peut être incorporée dans votre site Web pour fournir un accès aux mêmes données et contenus CloudKit que votre application Xamarin. iOS.

> [!IMPORTANT]
> Avant de pouvoir accéder au contenu, le présenter ou le mettre à jour à partir d’une base de données CloudKit à l’aide de CloudKit JS, vous devez avoir préalablement défini le schéma de la base de données.

Pour plus d’informations, consultez les documents suivants :

- [Présentation de CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) : notre introduction à l’utilisation de CloudKit dans une application Xamarin. iOS.
- [CloudKit démarrage rapide](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987) -présentation d’Apple à CloudKit.
- [Référence CLOUDKIT js](https://developer.apple.com/library/prerelease/ios/documentation/CloudKitJS/Reference/CloudKitJavaScriptReference/index.html#//apple_ref/doc/uid/TP40015359) -documentation CloudKit JS d’Apple.
- [Référence des services Web CloudKit](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloutKitWebServicesReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40015240) -référence d’Apple qui décrit l’interface http à CloudKit.
- [Catalogue CloudKit : Présentation de CloudKit (cacao et JavaScript)](https://developer.apple.com/library/prerelease/ios/samplecode/CloudAtlas/Introduction/Intro.html#//apple_ref/doc/uid/TP40014599) : exemple d’application Apple utilisant CloudKit et CloudKit js.

> [!IMPORTANT]
> Apple [fournit des outils](https://developer.apple.com/support/allowing-users-to-manage-data/) pour aider les développeurs à gérer correctement le Règlement général sur la protection des données (RGPD) de l’Union européenne.

## <a name="foundation-framework-additions"></a>Ajouts de Foundation Framework

Apple a inclus les modifications suivantes à l’infrastructure de Fondation dans iOS 9 :

### <a name="changes-to-nsbundle"></a>Modifications apportées à NSBundle

Les modifications suivantes ont été apportées à la classe [NSBundle](xref:Foundation.NSBundle) pour iOS 9 :

- `GetPreservationPriorityForTag (NSString tag)`-Obtient la priorité de conservation actuelle pour les ressources avec la balise donnée. Les valeurs valides sont comprises dans la plage `0.0` de `1.0`, les ressources dont la priorité est la plus basse seront supprimées en premier.
- `SetPreservationPriorityForTag (double priority, NSSet tags)`: Définit la priorité de conservation actuelle pour les ressources avec les balises spécifiées. Les valeurs valides sont comprises dans la plage `0.0` de `1.0`, les ressources dont la priorité est la plus basse seront supprimées en premier.

Pour plus d’informations, consultez la [référence NSBundle](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/index.html#//apple_ref/occ/cl/NSBundle)d’Apple.

### <a name="changes-to-nsprocessinfo"></a>Modifications apportées à NSProcessInfo

Chaque processus en cours d’exécution sur un appareil iOS possède un assembly PIA ( _Process information agent_ ) unique. Utilisez la classe [NSProcessInfo](xref:Foundation.NSProcessInfo) pour fournir des informations sur l’assembly PIA actuel et contrôler la gestion de l’alimentation et de la chaleur pour un processus donné.

Par exemple, pour contrôler l’arrêt automatique d’un processus, vous pouvez utiliser le code suivant :

```csharp
// Disable automatic termination
var activity = NSProcessInfo.ProcessInfo.BeginActivity(NSActivityOptions.AutomaticTerminationDisabled, "Define reason for change here...");

// Perform the required task
...

// Return to normal operation
NSProcessInfo.ProcessInfo.EndActivity(activity);
```

Pour plus d’informations, consultez la [référence NSProcessInfo](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSProcessInfo_Class/index.html#//apple_ref/occ/cl/NSProcessInfo)d’Apple.

### <a name="reacting-to-low-power-mode"></a>Réagit en mode faible puissance

Utilisez la `LowPowerModeEnabled` propriété de la classe [NSProcessInfo](xref:Foundation.NSProcessInfo) pour déterminer si le mode Low Power a été activé sur le périphérique iOS sur lequel l’application s’exécute. Par exemple :

```csharp
// Is the device in low power mode?
if (NSProcessInfo.ProcessInfo.LowPowerModeEnabled) {
    // Reduce activity to conserve energy...
} else {
    // Return to normal activity...
}
```

## <a name="healthkit-framework-changes"></a>Modifications de l’infrastructure HealthKit

Apple a inclus les modifications suivantes à l’infrastructure [HealthKit](xref:HealthKit) dans iOS 9 :

- Prise en charge du suivi de suppression et de suppression en bloc des entrées dans la base de données HealthKit. Pour plus d’informations, consultez Référence de la classe [HKDeletedObject](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKDeletedObject_ClassReference/index.html#//apple_ref/occ/cl/HKDeletedObject), [HKAnchoredObjectQuery](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKAnchoredObjectQuery_Class/index.html#//apple_ref/occ/cl/HKAnchoredObjectQuery) et [HKHealthStore](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKHealthStore_Class/index.html#//apple_ref/doc/uid/TP40014708) d’Apple.
- De nouvelles catégories et caractéristiques de suivi ont été ajoutées `HKQuantityTypeIdentifier` à la classe ( `UVExposure`telle que) et `HKCategoryTypeIdentifier` à la classe ( `OvulationTestResult`telle que). Pour plus d’informations, consultez [référence des constantes HealthKit](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HealthKit_Constants/index.html#//apple_ref/doc/uid/TP40014710) d’Apple.

Pour plus d’informations sur l’utilisation de HealthKit dans Xamarin. iOS, consultez notre [Introduction à](~/ios/platform/healthkit.md) la documentation de HealthKit.

## <a name="local-authentication-framework-changes"></a>Modifications de l’infrastructure d’authentification locale

Apple a inclus les modifications suivantes à l’infrastructure [d’authentification locale](xref:LocalAuthentication) dans iOS 9 :

- À l' `EvaluateAccessControl` aide `EvaluatePolicy` des méthodes et de la classe [LAContext](xref:LocalAuthentication.LAContext) , vous pouvez désormais réutiliser les correspondances d’ID tactiles à partir des tentatives de déverrouillage réussies précédentes.
- La possibilité d’obtenir la liste des doigts actuellement inscrits.
- Prise en charge du suivi lorsqu’un doigt est ajouté ou supprimé de l’authentification.
- La possibilité d’utiliser le _contexte d’authentification_ dans les appels de trousseau et la prise en charge de l’évaluation des listes de contrôle d’accès au trousseau.
- La possibilité d’annuler une invite utilisateur à partir du code.

Pour plus d’informations sur l’utilisation de Touch ID dans Xamarin. iOS, consultez la documentation de notre [Introduction à Touch ID](~/ios/platform/touchid.md) .

### <a name="lacontext-changes"></a>Modifications de LAContext

Les modifications suivantes ont été apportées à la classe [LAContext](xref:LocalAuthentication.LAContext) pour iOS 9 :

- **TouchIdAuthenticationMaximumAllowableReuseDuration** : retourne la durée maximale pendant laquelle une authentification Touch ID peut être réutilisée.
- **EvaluatedPolicyDomainState** : Obtient ou définit l’état d’une stratégie évaluée.
- **MaxBiometryFailures** -a été amorti dans iOS 9.
- **TouchIdAuthenticationAllowableReuseDuration** Obtient ou définit la durée pendant laquelle une authentification Touch ID peut être réutilisée.
- **EvaluateAccessControl** : évalue de manière asynchrone une stratégie d’authentification.
- **Invalidate** : invalide une authentification Touch ID donnée.
- **IsCredentialSet** -retourne `true` si les informations d’identification sont actuellement définies.
- **SetCredentialType** Définit le type d’informations d’identification donné.

Pour plus d’informations, consultez la [référence LAContext](https://developer.apple.com/library/prerelease/ios/documentation/LocalAuthentication/Reference/LAContext_Class/index.html#//apple_ref/occ/instm/LAContext/evaluatePolicy:localizedReason:reply:) d’Apple.

## <a name="mapkit-framework-changes"></a>Modifications de l’infrastructure MapKit

Apple a inclus les modifications suivantes à l’infrastructure [MapKit](xref:MapKit) dans iOS 9 :

- MapKit prend désormais en charge le lancement de l’application Map directement dans les directions de transit et l’interrogation de l’heure d’arrivée estimée de transit (ETA) à l’aide des classes [MKLaunchOptions](xref:MapKit.MKLaunchOptions) et [MKDirections](xref:MapKit.MKLaunchOptions) .
- Les résultats de la recherche retournés par MapKit et la classe [CLGeocoder](xref:CoreLocation.CLGeocoder) peuvent également fournir le fuseau horaire du résultat.
- Vous pouvez désormais personnaliser entièrement les annotations de carte présentées par votre application `DetailCalloutAccessoryView` iOS à l’aide de la propriété de la classe [MKAnnotationView](xref:MapKit.MKAnnotationView) .

Pour plus d’informations sur l’utilisation des mappages et des annotations dans Xamarin. iOS et la [référence CLGeocoder](https://developer.apple.com/library/prerelease/ios/documentation/CoreLocation/Reference/CLGeocoder_class/index.html#//apple_ref/occ/cl/CLGeocoder) d’Apple, consultez nos [cartes iOS](~/ios/user-interface/controls/ios-maps/index.md) et la [procédure pas à pas : exploration des annotations et des superpositions dans](~/ios/user-interface/controls/ios-maps/ios-maps-walkthrough.md) la documentation de MapKit.

## <a name="passkit-framework-additions"></a>Ajouts du Framework PassKit

Apple a inclus les modifications suivantes à l’infrastructure [PassKit](xref:PassKit) dans iOS 9 :

- Apple Pay prend désormais en charge les cartes de débit et de crédit stockées, ainsi que les cartes Discover. Pour plus d’informations, consultez la section sur les **réseaux de paiement** de la [référence de classe PKPaymentRequest](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKPaymentRequest_Ref/index.html#//apple_ref/doc/uid/TP40014832) d’Apple.
- Directement au sein d’une application Xamarin. iOS, vous pouvez désormais ajouter des réseaux de paiement et des émetteurs de cartes à Apple Pay. Pour plus d’informations, consultez Référence de la [classe PKAddPaymentPassViewController](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKAddPaymentPassViewController_Class/index.html#//apple_ref/doc/uid/TP40016116) d’Apple.

Pour plus d’informations sur l’utilisation de PassKit dans Xamarin. iOS, consultez notre [Introduction à](~/ios/platform/passkit.md) la documentation de PassKit.

## <a name="safari-services-framework-additions"></a>Ajouts de l’infrastructure des services Safari

Apple a inclus les modifications suivantes à l’infrastructure des [services Safari](xref:SafariServices) dans iOS 9 :

- Vous pouvez maintenant utiliser la nouvelle classe [SFSafariViewController](xref:SafariServices.SFSafariViewController) pour afficher le contenu Web dans une application Xamarin. iOS. Il offre la possibilité de partager des données de site Web et des cookies avec l’application Safari et inclut plusieurs fonctionnalités de Safari (telles que le lecteur et le remplissage automatique). [SFSafariViewController](xref:SafariServices.SFSafariViewController) propose un bouton **terminé** qui renverra les utilisateurs à votre application lorsqu’ils auront terminé de consulter le contenu Web.

Étant donné que la classe [SFSafariViewController](xref:SafariServices.SFSafariViewController) est personnalisée pour afficher une seule page de contenu Web, vous devez envisager de l’utiliser pour remplacer les contrôles [WKWebKit](xref:WebKit.WKWebView) ou [UIWebView](xref:UIKit.UIWebView) dans vos applications Xamarin. iOS existantes.

### <a name="displaying-a-website"></a>Affichage d’un site Web

Le code ci-dessous est un exemple d’appel d’un [SFSafariViewController](xref:SafariServices.SFSafariViewController) à partir d’un autre contrôleur d’affichage :

```csharp
// Create an instance of the Safari Services View Controller
var controller = new SFSafariViewController(new NSUrl("http://www.xamarin.com"));

// Display website
PresentViewController(controller, true, null);
```

## <a name="uikit-framework-changes"></a>Modifications de l’infrastructure UIKit

Apple a inclus de nombreuses améliorations à plusieurs éléments de l’infrastructure [UIKit](xref:UIKit) pour iOS 9. Les sections suivantes décrivent ces modifications.

### <a name="3d-touch-events"></a>Événements tactiles 3D

Nouveauté d’iOS 9 et de iPhone 6S et iPhone 6S plus, le toucher 3D ajoute des gestes sensibles à la pression à vos applications iOS. Par conséquent, si votre application s’exécute sur iOS 9 (ou une version ultérieure) et que l’appareil iOS est en mesure de prendre en charge la fonction tactile 3D `TouchesMoved` , les modifications de pression entraînent le déclenchement de l’événement.

En raison de ce changement de comportement, vos applications iOS doivent être préparées `TouchesMoved` pour que l’événement soit appelé plus souvent, même si les coordonnées X/Y n’ont pas changé.

Pour plus d’informations, consultez notre [Introduction au Guide tactile 3D](~/ios/platform/3d-touch.md) .

### <a name="document-open-in-place-functionality"></a>Fonctionnalités Open-in-place du document

À l’aide `FinishedLaunching (application, launchOptions)` des méthodes `WillFinishLaunching (Application, launchOptions)` ou de la classe [UIApplicationDelegate](xref:UIKit.UIApplicationDelegate) , vous pouvez maintenant ouvrir un document et le modifier sur place (au lieu de travailler sur une copie).

Pour prendre en charge la nouvelle fonctionnalité ouverte, ajoutez la `LSSupportsOpeningDocumentsInPlace` clé au fichier **info. plist** de votre application Xamarin. iOS, `YES`avec la valeur.

Pour plus d’informations, consultez la [référence UIApplicationDelegate](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate) d’Apple.

### <a name="enhanced-touch-events"></a>Événements tactiles améliorés

Apple a fourni plusieurs améliorations apportées aux événements tactiles dans iOS 9. Celles-ci incluent la possibilité d’utiliser la prédiction tactile et d’obtenir l’accès aux touches intermédiaires entre les actualisations d’affichage.

Pour plus d’informations, consultez le [Guide de gestion des événements Apple pour iOS](https://developer.apple.com/library/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/Introduction/Introduction.html) .

### <a name="fetching-tailored-content"></a>Récupération du contenu personnalisé

La nouvelle `NSDataAsset` classe permet à une application Xamarin. iOS d’extraire du contenu adapté aux fonctionnalités de mémoire et de graphique du périphérique iOS sur lequel il est en cours d’exécution.

### <a name="new-layout-anchors"></a>Nouvelles ancres de disposition

Les nouvelles `NSLayoutAnchor` classes `NSLayoutDimension` d’ancre et de mise en page fonctionnent avec les nouvelles propriétés d’ancrage de la `LeadingAnchor` classe `WidthAnchor` [UIView](xref:UIKit.UIView) (telles que et) pour faciliter la mise en page dans iOS 9.

Pour plus d'informations sur l’[utilisation](~/ios/user-interface/storyboards/unified-storyboards.md) des classes de mise en page automatique et de taille dans une application Xamarin.iOS et la [référence NSLayoutAnchor](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutAnchor_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutAnchor) d’Apple, consultez la [référence NSLayoutDimension](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutDimension_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutDimension) et pour plus d’informations, consultez la [référence UIView](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/cl/UIView).

### <a name="new-readable-content-margins"></a>Nouvelles marges de contenu lisibles

La nouvelle `UILayoutGuide` classe peut être utilisée pour fournir des marges de contenu lisibles et définir les zones de dessin pour le contenu à l’intérieur d’une vue. Pour plus d’informations, consultez [référence UILayoutGuide](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UILayoutGuide_Class_Reference/index.html#//apple_ref/occ/cl/UILayoutGuide) d’Apple.

### <a name="text-input-in-notifications-modifications"></a>Entrée de texte dans les modifications de notifications

La classe [UIUserNotificationAction](xref:UIKit.UIUserNotificationAction) a une nouvelle `Behavior` propriété qui peut être utilisée pour prendre en charge l’entrée de texte à partir des notifications.

### <a name="uiapplicationdelegate-changes"></a>Modifications de UIApplicationDelegate

Bien qu’il ne soit pas explicitement déconseillé par Apple, ils suggèrent de `FinishedLaunching (UIApplication application)` remplacer tous les appels à la méthode de `FinishedLaunching (UIApplication application, NSDictionary launchOptions)` la `WillFinishLaunching (UIApplication application, NSDictionary launchOptions)` classe [UIApplicationDelegate](xref:UIKit.UIApplicationDelegate) par les méthodes ou.

Pour plus d’informations, consultez la [référence UIApplicationDelegate](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate) d’Apple.

### <a name="uikit-dynamics-changes"></a>Modifications de Dynamics UIKit

Apple a inclus les modifications suivantes dans UIKit Dynamics dans iOS 9 :

- Dynamics prend désormais en charge les limites de collision non rectangulaires.
- La nouvelle `UIFieldBehavior` classe personnalisable est utilisée pour prendre en charge différents types de champs.
- D’autres types de pièces jointes ont `UIAttachmentBehavior` été ajoutés à la classe.

Pour plus d’informations, consultez la [référence UIAttachment](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIAttachmentBehavior_Class/index.html#//apple_ref/occ/cl/UIAttachmentBehavior) d’Apple.

### <a name="uipickerview-and-uidatepicker-changes"></a>Modifications apportées à UIPickerView et UIDatePicker

Avant iOS 9, les contrôles [UIPickerView](xref:UIKit.UIPickerView) et [UIDatePicker](xref:UIKit.UIDatePicker) étaient non redimensionnables et redimensionnent automatiquement pour remplir la largeur de leur conteneur (généralement la largeur du périphérique iOS sur lequel l’application était exécutée).

Dans iOS 9, ce redimensionnement automatique ne se produit plus et les contrôles sont rendus à une largeur de 320 points sur tous les appareils iOS, quelle que soit la taille et l’orientation de l’écran.

Pour corriger cette situation, utilisez les classes disposition automatique et taille pour épingler la largeur du contrôle aux bords du conteneur parent (vue) et spécifiez la hauteur requise. Pour plus d’informations sur l’utilisation des classes de disposition et de taille automatiques dans une application Xamarin. iOS, consultez notre introduction à la documentation sur les [storyboards unifiées](~/ios/user-interface/storyboards/unified-storyboards.md) .

### <a name="new-uitextinputassistantitem-class"></a>Nouvelle classe UITextInputAssistantItem

Utilisez la nouvelle `UITextInputAssistantItem` classe pour les groupes de boutons de la barre de disposition dans une _barre de raccourcis_. La barre de raccourcis est une nouvelle zone disponible dans le clavier logiciel pour fournir des raccourcis de saisie.

## <a name="related-links"></a>Liens associés

- [Exemples iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [Introduction à iOS 9](~/ios/platform/introduction-to-ios9/index.md)
- [iOS 9 pour les développeurs](https://developer.apple.com/ios/pre-release/)
- [Nouveautés d’iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
