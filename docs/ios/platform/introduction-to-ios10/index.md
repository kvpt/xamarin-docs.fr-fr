---
title: Introduction à iOS 10
description: Cet article présente tous les nouveaux et modifiés API et fonctionnalités disponibles dans iOS 10 pour les développeurs de Xamarin.iOS.
ms.prod: xamarin
ms.assetid: FB91DFFE-CF5E-4253-92CB-78A6371259D9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: e84f5acdd1660633521d1a99325155b614b87e1b
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67268910"
---
# <a name="introduction-to-ios-10"></a>Introduction à iOS 10

Avec la nouvelle iOS 10 SDK, Apple a inclus nouvelles API et services qui permettent au développeur de créer des catégories d’applications et de fonctionnalités. Une application iOS peut maintenant étendre les applications de Messages, Siri, téléphone et des cartes pour fournir des fonctionnalités enrichies et attrayantes à l’utilisateur final qui était auparavant pas disponible.

Pour plus d’informations sur iOS 10, consultez d’Apple [iOS + applications](https://developer.apple.com/ios/) documentation.

## <a name="whats-new-in-ios-10"></a>Quelles sont les nouveautés dans iOS 10

Apple a ajouté plusieurs nouvelles API et services dans iOS 10, ainsi que de nombreuses améliorations aux fonctionnalités existantes, y compris :

## <a name="adapting-to-the-true-tone-display"></a>Adaptation à l’affichage de ton True

Technologie d’affichage de ton True d’Apple utilise le capteur d’éclairage ambiant sur un appareil iOS pour ajuster dynamiquement la couleur et l’intensité de l’affichage pour faire correspondre les conditions d’éclairage actuelles. iOS 10 propose les nouvelles [UIWhitePointAdaptivityStyle](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW31) clé qui peut être ajouté à l’application `Info.plist` de fichiers et contrôle comment ton True applique le décalage vers la couleur standard. 

Les valeurs suivantes sont disponibles :

- `UIWhitePointAdaptivityStyleStandard` **Par défaut** -utiliser l’adaptabilité de point de blanc standard.
- `UIWhitePointAdaptivityStyleReading` -Utilisé pour les applications axées sur la lecture.
- `UIWhitePointAdaptivityStyleGame` -Utilisé pour les applications axées sur le jeu.
- `UIWhitePointAdaptivityStyleVideo` -Utilisé pour les applications axées sur la vidéo.
- `UIWhitePointAdaptivityStylePhoto` -Utilisé pour les applications axée sur la photographie où la fidélité des couleurs est plus importante que les réglages de l’environnement de point de blanc.

## <a name="app-extensions"></a>Extensions d’application

Apple a fourni plusieurs nouveaux Points d’Extension d’application dans iOS 10 :

- Répertoire de l’appel
- Intents et Intents UI
- Messages
- Contenu de la notification
- Notification Services
- Pack d’autocollant

En outre, 3e partie clavier application Extensions ont les améliorations suivantes :

- La nouvelle `DocumentInputMode` propriété de la `UITextDocumentProxy` classe peut déterminer la langue d’entrée d’un document et autoriser l’extension de clavier pour se conformer à cette langue.
- La nouvelle `HandleInputModeList` (méthode) permet d’afficher le menu du sélecteur de clavier du système en réponse à la clé du monde entier est appuyé sur l’extension de clavier.

Pour plus d’informations, consultez notre [Introduction aux Extensions](~/ios/platform/extensions.md), [intégration des applications Message](~/ios/platform/message-app-integration/index.md), [Introduction aux Suggestions proactives](~/ios/platform/search/proactive-suggestions.md), [ Présentation de SiriKit](~/ios/platform/sirikit/index.md), [Introduction aux Notifications à l’utilisateur](~/ios/platform/user-notifications/index.md) et d’Apple [Guide de programmation d’Extension d’application](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214).

## <a name="app-search-enhancements"></a>Améliorations de la recherche d’applications

Spotlight de base dans iOS 10 apporte plusieurs améliorations à la recherche d’application telles que :

- **Popularité de lien ciblé participatives (avec privacy différentielle)** -offre un moyen pour promouvoir le contenu de l’application lié à la profondeur dans les résultats de la recherche.
- **Dans l’application recherche** -utiliser le nouveau `CSSearchQuery` classe pour fournir la possibilité de recherche Spotlight dans l’application similaire au fonctionnement des applications de messagerie, les Messages et les Notes.
- **Rechercher la Continuation** - permet à un utilisateur de lancer une recherche dans Spotlight ou Safari, puis ouvrez une application et continuer cette recherche.
- **Visualisation des résultats de la Validation** -Apple [outil de Validation API application recherche](https://search.developer.apple.com/appsearch-validation-tool) affiche maintenant une représentation visuelle du balisage d’un site Web et lien profond lors de la réalisation de tests.
- **Message d’application partage d’images** -permet des images populaires dans l’application fournies pour le partage dans les Messages (via une Extension d’application Message) doit s’afficher dans les recherches d’actualités.

Pour en savoir plus, consultez notre [les améliorations de recherche application](~/ios/platform/search/app-search-enhancements.md) guide.

## <a name="apple-pay-enhancements"></a>Apple Pay améliorations

Apple a apporté plusieurs améliorations à Apple Pay dans iOS 10 qui autorise l’utilisateur à procéder à des paiements sécurisés à partir de sites Web et via l’interaction avec Siri et cartes.

Avec iOS 10, plusieurs nouvelles API ont été ajoutés qui fonctionnent avec iOS et watchOS pour prendre en charge des réseaux de paiement dynamique et un nouvel environnement de test de bac à sable.

En outre, le framework PassKit a été développé pour prendre en charge Apple Pay en dehors de `UIKit` et pour permettre des émetteurs de carte présenter leurs cartes à partir de leurs applications.

Pour en savoir plus, consultez notre [Apple payer améliorations](~/ios/platform/apple-pay.md) guide.

## <a name="alternate-app-icons"></a>Autres icônes d’applications

Apple a ajouté plusieurs améliorations à iOS 10.3 qui permettent à une application gérer son icône :

- `ApplicationIconBadgeNumber` -Obtient ou définit le badge de l’icône d’application Springboard.
- `SupportsAlternateIcons` -If `true` l’application a un autre ensemble d’icônes.
- `AlternateIconName` -Retourne le nom de l’icône de remplacement actuellement sélectionné ou `null` si vous utilisez l’icône principale.
- `SetAlternameIconName` -Utilisez cette méthode pour basculer d’icône de l’application vers l’icône de remplacement donné.

Pour en savoir plus, consultez notre [autres icônes d’application](~/ios/app-fundamentals/images-icons/alternate-app-icons.md) guide.

## <a name="introduction-to-callkit"></a>Introduction à CallKit

La nouvelle API CallKit dans iOS 10 offre un moyen pour les applications VOIP intégrer l’interface utilisateur de l’iPhone et fournir une interface familière et à l’utilisateur final. Avec cette API, les utilisateurs peuvent afficher et interagir avec les appels VOIP à partir de l’écran de verrouillage de l’appareil iOS et gérer des contacts à l’aide de l’application de téléphone **favoris** et **récents** vues.

En outre, l’API CallKit offre la possibilité de créer des Extensions d’application qui peut associer un numéro de téléphone avec un nom (ID de l’appelant), ou indiquer le système lorsqu’un nombre doit être bloqué (appel bloquant).

Pour en savoir plus, consultez notre [présentation Callkit](~/ios/platform/callkit.md) guide.

## <a name="message-app-integration"></a>Intégration de l’application Messages

iOS 10 permettant d’inclure une Extension d’application de Message dans la solution Xamarin.iOS qui s’intègre à la **Messages** application et présente de nouvelles fonctionnalités à l’utilisateur. L’extension peut envoyer des messages interactifs, texte, autocollants et fichiers multimédias. Deux types d’Extension d’application de Message sont disponibles :

- **Packs d’autocollant** -contient une collection d’autocollants de l’utilisateur peut ajouter à un message. Packs d’autocollant peuvent être créés sans écrire de code.
- **iMessage application** -peut présenter une Interface utilisateur personnalisée dans l’application Messages pour la sélection autocollants, saisie de texte, y compris les fichiers de support (avec des conversions de type facultatif) et création, modification et envoi de messages de l’interaction.

Pour en savoir plus, consultez notre [intégration des applications Message](~/ios/platform/message-app-integration/index.md) guide.

## <a name="news-publisher-enhancements"></a>Améliorations de serveur de publication d’actualités

Avec iOS 10, Apple n’importe qui dans les magazines principales et de nouveau aux organisations de produit et des éditeurs indépendants pour vous inscrire et blogueurs et de diffuser du contenu à l’application Apple News. Pour plus d’informations, consultez le site d’Apple [ressources des News](https://newsresources.apple.com/) documentation.

## <a name="providing-haptic-feedback"></a>Activation du retour haptique

Sur l’iPhone 7 et iPhone 7 en outre, Apple a inclus les nouvelles réponses HAPTIQUES qui offrent des moyens supplémentaires de physiquement solliciter l’utilisateur. Utiliser les nouvelles options de rétroaction tactile pour obtenir l’attention des utilisateurs et de renforcer leurs actions.

Plusieurs éléments d’interface utilisateur intégrés fournissent déjà retour haptique tels que les sélecteurs, les commutateurs et les curseurs. iOS 10 ajoute désormais la possibilité de déclencher par programmation HAPTIQUES à l’aide d’une sous-classe concrète de la `UIFeedbackGenerator` classe.

Pour en savoir plus, consultez notre [fournissant un retour haptique](~/ios/user-interface/ios-ui/haptic-feedback.md) guide.

## <a name="proactive-suggestions"></a>Suggestions proactives

iOS 10 présente les nouvelles façons d’engagement de conduite à une application en autorisant le système de manière proactive présenter des informations utiles automatiquement à l’utilisateur aux moments opportuns. Tout comme iOS 9 fourni la possibilité d’ajouter une recherche approfondie à l’application à l’aide de Spotlight, remise et des Suggestions de Siri, avec iOS 10, qu'une application peut exposer des fonctionnalités qui peuvent être présentées à l’utilisateur par le système à partir des emplacements suivants :

- Le sélecteur d’application
- L’écran de verrouillage
- CarPlay
- Maps
- Interactions de Siri
- Suggestions de QuickType

Une application expose cette fonctionnalité dans le système à l’aide d’une collection de technologies telles que [NSUserActivity](xref:Foundation.NSUserActivity), balisage web, Spotlight de base, MapKit, Media Player et UIKit.

Pour en savoir plus, consultez notre [Introduction aux Suggestions proactives](~/ios/platform/search/proactive-suggestions.md) guide.

## <a name="request-app-review"></a>Demander une révision d’application

Nouveau à iOS 10.3, le `RequestReview()` méthode permet à une application iOS demander à évaluer ou réviser l’utilisateur. Bien que cette méthode peut être appelée à tout moment où il est judicieux de l’expérience utilisateur, le processus de révision est régi et géré par la stratégie de l’App Store. Par conséquent, cette méthode peut ou peut ne pas affiche une alerte et ne doit jamais être appelée en réponse à une action de l’utilisateur, par exemple en appuyant sur un bouton.

Pour en savoir plus, consultez notre [demander une révision application](~/ios/platform/request-app-review.md) guide.

## <a name="security-and-privacy-enhancements"></a>Améliorations de la confidentialité et de sécurité

Apple a apporté plusieurs améliorations à la sécurité et confidentialité dans iOS 10 qui aideront les développeurs à améliorer la sécurité de leurs applications et garantir la confidentialité de l’utilisateur final.

Par conséquent, les applications qui s’exécutent sur iOS 10 (ou version ultérieure) doivent déclarer statiquement leur intention d’accéder à des fonctionnalités spécifiques ou des informations utilisateur en entrant une ou plusieurs clés spécifiques confidentialité dans leurs `Info.plist` fichiers expliquent à l’utilisateur pourquoi l’application souhaite accéder.

Pour en savoir plus, consultez notre [sécurité et confidentialité améliorations](~/ios/app-fundamentals/security-privacy.md) guide.

## <a name="sirikit"></a>SiriKit

Nouveau à iOS 10, SiriKit permet à une application Xamarin.iOS fournir des services qui sont accessibles à l’utilisateur à l’aide de Siri sur un appareil iOS. Cette fonctionnalité est fournie dans un ou plusieurs Extension d’application à l’aide de la nouvelle **intentions** et **Intents UI** frameworks.

SiriKit prend en charge les domaines de service suivants :

- Audio ou vidéo.
- Un tour de réservation.
- La gestion de sommeil.
- La messagerie.
- Recherche des photos.
- Envoyer ou recevoir des paiements.

Lorsque l’utilisateur effectue une requête de Siri impliquant un des services de l’Extension application, SiriKit envoie l’extension un **intention** objet qui décrit la demande de l’utilisateur, ainsi que les données pertinentes. l’Extension d’application génère ensuite approprié **réponse** de l’objet pour la donnée **intention**, détaillant la façon dont l’extension puisse traiter la demande.

Tandis que Siri gère généralement toutes les interactions utilisateur, l’Extension de l’application peut utiliser le **l’interface utilisateur de l’intention** framework pour présenter une riche et personnalisé l’Interface utilisateur présentant la marque de l’application et des informations supplémentaires.

Pour en savoir plus, consultez notre [présentation de SiriKit](~/ios/platform/sirikit/index.md) guide.

## <a name="speech-recognition"></a>Reconnaissance vocale

iOS 10 inclut une nouvelle API de reconnaissance vocale qui permet à l’application prendre en charge la reconnaissance vocale continue et transcription vocale (à partir de flux audio en direct ou enregistrés) dans le texte.

Étant donné que la reconnaissance vocale nécessite la transmission et le stockage temporaire des données sur les serveurs d’Apple, l’application _doit_ demander l’autorisation de l’utilisateur pour effectuer la reconnaissance en incluant le `NSSpeechRecognitionUsageDescription` clés dans son `Info.plist` fichier et en appelant le `SFSpeechRecognizer.RequestAutorization` (méthode).

Pour en savoir plus, consultez notre [Introduction à la reconnaissance vocale](~/ios/platform/speech.md) guide.

## <a name="user-notifications"></a>Notifications à l’utilisateur

Nouveau à iOS 10, la Notification utilisateur framework permet de la fourniture et la gestion des notifications locales et distantes. À l’aide de cette infrastructure, l’application ou une Extension d’application permettre planifier la remise des notifications locales en spécifiant un ensemble de conditions comme emplacement ou de l’heure.

En outre, l’application ou une extension peut s’afficher (et éventuellement modifier) notifications locales et distantes comme ils sont remis à l’appareil iOS de l’utilisateur.

La nouvelle infrastructure de l’interface utilisateur de la Notification utilisateur permet à l’application ou une Extension d’application pour personnaliser l’apparence des notifications locales et distantes quand elles sont présentées à l’utilisateur.

Pour en savoir plus, consultez notre [Framework des Notifications utilisateur](~/ios/platform/user-notifications/index.md) guide.

## <a name="video-subscriber-account"></a>Compte abonné vidéo

Nouveau pour iOS 10, le framework compte abonné à la vidéo permet aux applications cette prise en charge authentifié de diffusion en continu ou la vidéo à la demande auprès de leur fournisseur de télévision par câble ou satellite à l’aide d’une seul-expérience de connexion pour l’utilisateur final.

## <a name="wide-color"></a>Couleur à spectre large

iOS 10 étend la prise en charge pour les formats de pixel de la plage étendue et des espaces d’une gamme de couleurs dans tout le système, y compris les infrastructures telles que graphismes de base, l’Image de Core, complète et AVFoundation. Prise en charge pour les appareils avec des couleurs large est davantage facilité en fournissant ce comportement dans toute la pile de graphique entière.

En outre, [UIKit](xref:UIKit) a été modifié pour fonctionner dans la nouvelle étendue **sRVB** espace colorimétrique, facilitant ainsi de mélanger les couleurs utilisées dans les gammes de couleurs large sans perte de performances significatifs.

Apple offre les meilleures pratiques suivantes lorsque vous travaillez avec des couleurs larges :

- [UIColor](xref:UIKit.UIColor) maintenant utilise le sRVB espace de couleurs et ne seront plus fixer des valeurs pour le `0.0` à `1.0` plage. Si l’application s’appuie sur le comportement de pince précédent, il devront être modifiées pour iOS 10.
- L’environnement de dessin sera configuré pour l’espace de couleurs sRVB lors de l’exécution personnalisée `UIView` de dessin sur un iPad Pro.
- Si l’application effectue le rendu personnalisé de `UIImages`, utilisez la nouvelle [UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer) classe pour spécifier l’utilisation des formats étendus standard-gamme ou.
- Lorsque vous utilisez une API de bas niveau tels que les graphismes de base ou complète pour fournir le traitement d’image, le développeur doit utiliser un format de couleur plage étendue espace et de pixels qui prend en charge les valeurs à virgule flottante 16 bits. Le cas échéant, le développeur aura à fixer manuellement les valeurs de composant de couleur.
- Graphismes de base, Image Core et des nuanceurs de performances de métal fournissent tous des nouvelles méthodes pour la conversion entre les espaces de deux couleurs.

Pour en savoir plus, consultez notre [Introduction à large couleur](~/ios/platform/wide-color.md) guide.

## <a name="widget-enhancements"></a>Améliorations des widgets

Apple a introduit plusieurs améliorations du système de Widget pour vous assurer que les widgets belles sur n’importe quel arrière-plan qui existe sur le nouveau iOS 10 écran de verrouillage. Le [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect) propriété a été déconseillée et a été remplacée par la nouvelle [WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect) ou [WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect) propriétés. En outre, les widgets contiennent désormais un [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) propriété qui permet au développeur décrire la quantité de contenu est disponible et permet à l’utilisateur développer et réduire le contenu.

Pour en savoir plus, consultez notre [recherche et les améliorations des widgets écran d’accueil](~/ios/platform/search/widgets.md) guide.

## <a name="additional-framework-changes"></a>Modifications d’infrastructure supplémentaires

Outre les modifications de l’infrastructure principale et les ajouts répertoriés ci-dessus, Apple a effectué de nombreuses modifications de framework mineures supplémentaires dans iOS 10.

Pour en savoir plus, consultez notre [des modifications supplémentaires Framework](~/ios/platform/introduction-to-ios10/additional-framework-changes.md) guide.

## <a name="deprecated-apis"></a>API déconseillées

Les API suivantes ont été déconseillées dans iOS 10 :

- Le `CKDiscoverAllContactsOperation`, `CKDiscoveredUserInfo`, `CKDiscoverUserInfosOperation` et `CKFetchRecordChangesOperation` classes ont été déconseillées dans CloudKit pour iOS 10. Utilisez le [CKDiscoverAllUserIdentitiesOperation](xref:CloudKit.CKDiscoverUserIdentitiesOperation), [CKUserIdentity](xref:CloudKit.CKUserIdentity) et [CKFetchRecordZoneChangesOperation](xref:CloudKit.CKFetchRecordZoneChangesOperation) classes (qui prennent en charge le partage des enregistrements) à la place.
- Plusieurs [CKSubscription](https://developer.apple.com/reference/cloudkit/cksubscription) API (telles que des abonnements basés sur une requête et zone) ont été déconseillées. Utilisez le [CKRecordZoneSubscription](xref:CloudKit.CKRecordZoneSubscription) et [CKQuerySubscription](xref:CloudKit.CKQuerySubscription) API à la place.
- [NSPersistentStoreCoordinator](xref:CoreData.NSPersistentStoreCoordinator) symboles relatifs au contenu omniprésent ont été déconseillées.
- `ADBannerView`, `ADInterstitialAd` et les symboles dans le [UIViewController](xref:UIKit.UIViewController) classe ont été déconseillées.
- [SKUniform](https://developer.apple.com/reference/spritekit/skuniform) symboles relatifs aux valeurs à virgule flottante ont été déconseillées.
- Le `UILocalNotification`, `UIMutableUserNotificationAction`, `UIMutableUserNotificationCategory`, `UIUserNotificationAction`, `UIUserNotificationCategory` et `UIUserNotificationSettings` classes de UIKit ont été déconseillées. Utilisez le [Notifications utilisateur](#user-notifications) framework à la place.
- Le `HandleActionForLocalNotification`, `HandleActionForRemoteNotification`, `DidReceiveLocalNotification` et `DidReceiveRemoteNotification` WatchKit méthodes ont été déconseillées. Utilisez le `HandleActionForNotification` et `DidReceiveNotification` méthodes à la place.
- Le `DidReceiveLocalNotification` et `DidReceiveRemoteNotification` méthodes de la [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate) ont été déconseillées. Créez une instance de [UNUserNotificationCenterDelegate](https://developer.apple.com/reference/usernotifications/unusernotificationcenterdelegate) qui implémente les méthodes appropriées et assignez-la à la `Delegate` propriété de la [UNUserNotificationCenter](https://developer.apple.com/reference/usernotifications/unusernotificationcenter) objet.
- Le **Game Center application** a été déconseillé et supprimé à partir d’iOS. Si l’application utilise GameKit, il _doit_ présenter son propre interface pour afficher les fonctionnalités GameKit (leaderboards, etc.).

Consultez d’Apple [iOS 9.3 aux différences d’API iOS 10.0](https://developer.apple.com/library/prerelease/content/releasenotes/General/iOS10APIDiffs/index.html) documentation pour obtenir la liste complète des dépréciations.

## <a name="related-links"></a>Liens associés

- [Exemples iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
