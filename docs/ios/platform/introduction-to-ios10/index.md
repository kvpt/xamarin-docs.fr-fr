---
title: Introduction à iOS 10
description: Cet article présente toutes les API et fonctionnalités nouvelles et modifiées disponibles dans iOS 10 pour les développeurs Xamarin. iOS.
ms.prod: xamarin
ms.assetid: FB91DFFE-CF5E-4253-92CB-78A6371259D9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: ce262faf2d79e6a2cc969df582446fdc2ec29bde
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032225"
---
# <a name="introduction-to-ios-10"></a>Introduction à iOS 10

Avec le nouveau kit de développement logiciel (SDK) iOS 10, Apple a inclus de nouvelles API et de nouveaux services qui permettent au développeur de créer de nouvelles catégories d’applications et de fonctionnalités. Une application iOS peut maintenant étendre les applications messages, Siri, Phone et Maps pour fournir à l’utilisateur final des fonctionnalités riches et attrayantes qui étaient auparavant indisponibles.

Pour plus d’informations sur iOS 10, consultez la documentation sur [iOS + Apps](https://developer.apple.com/ios/) d’Apple.

## <a name="whats-new-in-ios-10"></a>Nouveautés d’iOS 10

Apple a ajouté plusieurs nouvelles API et services dans iOS 10, ainsi que de nombreuses améliorations aux fonctionnalités existantes, notamment :

## <a name="adapting-to-the-true-tone-display"></a>Adapter à l’affichage du ton vrai

La technologie d’affichage du vrai ton d’Apple utilise le capteur de lumière ambiante d’un appareil iOS pour ajuster dynamiquement la couleur et l’intensité de l’affichage afin qu’elles correspondent aux conditions d’éclairage actuelles. iOS 10 fournit la nouvelle clé [UIWhitePointAdaptivityStyle](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW31) qui peut être ajoutée au fichier `Info.plist` de l’application et contrôle la manière dont le ton vrai applique le décalage de couleur standard. 

Les valeurs suivantes sont disponibles :

- `UIWhitePointAdaptivityStyleStandard` **par défaut** : utilisez l’adaptabilité des points blancs standard.
- `UIWhitePointAdaptivityStyleReading` : utilisé pour les applications orientées lecture.
- `UIWhitePointAdaptivityStyleGame` : utilisé pour les applications orientées jeu.
- `UIWhitePointAdaptivityStyleVideo` : utilisé pour les applications vidéo.
- `UIWhitePointAdaptivityStylePhoto` : utilisé pour les applications axées sur la photographie, où la fidélité des couleurs est plus importante que les réglages des points blancs environnementaux.

## <a name="app-extensions"></a>Extensions d’application

Apple a fourni plusieurs nouveaux points d’extension d’application dans iOS 10 :

- Répertoire des appels
- Interface utilisateur d’intentions et d’intentions
- Messages
- Contenu de la notification
- Notification Services
- Pack d’autocollants

En outre, les extensions d’application de clavier tierces présentent les améliorations suivantes :

- La nouvelle propriété `DocumentInputMode` de la classe `UITextDocumentProxy` peut déterminer la langue d’entrée d’un document et permettre à l’extension de clavier de s’aligner avec cette langue.
- La nouvelle méthode `HandleInputModeList` permet à l’extension de clavier d’afficher le menu de sélection de clavier du système en réponse à la touche de l’Atlas qui est exploitée.

Pour plus d’informations, consultez notre [Introduction aux extensions](~/ios/platform/extensions.md), [intégration d’applications de message](~/ios/platform/message-app-integration/index.md), [Présentation des suggestions proactives](~/ios/platform/search/proactive-suggestions.md), [Présentation de SiriKit](~/ios/platform/sirikit/index.md), [Présentation des notifications utilisateur](~/ios/platform/user-notifications/index.md) et d' [Apple Guide de programmation](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)de l’extension d’application.

## <a name="app-search-enhancements"></a>Améliorations de la recherche d’applications

Core Spotlight dans iOS 10 offre plusieurs améliorations à la recherche d’applications, telles que :

- **Participatives popularité des liens approfondis (avec confidentialité différentielle)** : permet de promouvoir le contenu d’application lié de manière détaillée dans les résultats de recherche.
- **Recherche dans l’application** : utilisez la nouvelle classe `CSSearchQuery` pour fournir une fonctionnalité de recherche dans la Galerie d’applications similaire à la façon dont les applications mail, messages et notes fonctionnent.
- **Continuation** de la recherche : permet à un utilisateur de lancer une recherche dans Spotlight ou Safari, puis d’ouvrir une application et de poursuivre cette recherche.
- **Visualisation des résultats de la validation** : l’outil de validation de l' [API de recherche d’applications](https://search.developer.apple.com/appsearch-validation-tool) d’Apple affiche désormais une représentation visuelle du balisage d’un site Web et des liens détaillés lors de la préformation des tests.
- **Partage d’images d’application de message** : permet d’afficher les images courantes dans l’application fournies pour le partage des messages (via une extension de l’application de message) dans les recherches Spotlight.

Pour en savoir plus, consultez notre guide d’amélioration de la [recherche d’applications](~/ios/platform/search/app-search-enhancements.md) .

## <a name="apple-pay-enhancements"></a>Améliorations apportées à Apple Pay

Apple a apporté plusieurs améliorations à Apple Pay dans iOS 10 qui permettent à l’utilisateur d’effectuer des paiements sécurisés à partir de sites Web et par le biais de l’interaction avec Siri et Maps.

Avec iOS 10, plusieurs nouvelles API ont été ajoutées qui fonctionnent avec iOS et Watchos pour prendre en charge les réseaux de paiement dynamiques et un nouvel environnement de test bac à sable (sandbox).

En outre, PassKit Framework a été étendu pour prendre en charge les Apple Pay en dehors de `UIKit` et pour permettre aux émetteurs de cartes de présenter leurs cartes à partir de leurs applications.

Pour en savoir plus, consultez notre guide d’amélioration de l' [Apple Pay](~/ios/platform/apple-pay.md) .

## <a name="alternate-app-icons"></a>Autres icônes d’applications

Apple a ajouté plusieurs améliorations à iOS 10,3 qui permettent à une application de gérer son icône :

- `ApplicationIconBadgeNumber` : Obtient ou définit le badge de l’icône de l’application dans le Springboard.
- `SupportsAlternateIcons`-si `true` l’application possède un autre ensemble d’icônes.
- `AlternateIconName` : retourne le nom de l’autre icône actuellement sélectionnée ou `null` si l’icône principale est utilisée.
- `SetAlternameIconName` : utilisez cette méthode pour faire passer l’icône de l’application à l’autre icône donnée.

Pour en savoir plus, consultez notre guide des [autres icônes d’application](~/ios/app-fundamentals/images-icons/alternate-app-icons.md) .

## <a name="introduction-to-callkit"></a>Présentation de CallKit

La nouvelle API CallKit dans iOS 10 permet aux applications VOIP de s’intégrer à l’interface utilisateur iPhone et de fournir une interface et une expérience familières à l’utilisateur final. Avec cette API, les utilisateurs peuvent afficher et interagir avec les appels VOIP à partir de l’écran de verrouillage de l’appareil iOS et gérer les contacts à l’aide des **favoris** de l’application téléphonique et des vues **récentes** .

En outre, l’API CallKit offre la possibilité de créer des extensions d’application qui peuvent associer un numéro de téléphone à un nom (ID d’appelant) ou indiquer au système quand un nombre doit être bloqué (blocage d’appel).

Pour en savoir plus, consultez notre guide [de présentation du Callkit](~/ios/platform/callkit.md) .

## <a name="message-app-integration"></a>Intégration de l’application Messages

iOS 10 permet l’inclusion d’une extension d’application de message dans la solution Xamarin. iOS qui s’intègre à l’application **messages** et présente de nouvelles fonctionnalités à l’utilisateur. L’extension peut envoyer du texte, des autocollants, des fichiers multimédias et des messages interactifs. Deux types d’extension d’application de message sont disponibles :

- **Vignettes-packs** : contient une collection d’autocollants que l’utilisateur peut ajouter à un message. Les packs d’autocollants peuvent être créés sans écrire de code.
- **application IMessage** : peut présenter une interface utilisateur personnalisée dans l’application messages pour sélectionner des autocollants, entrer du texte, y compris des fichiers multimédias (avec conversions de type facultatives) et créer, modifier et envoyer des messages d’interaction.

Pour en savoir plus, consultez notre guide d' [intégration des applications de message](~/ios/platform/message-app-integration/index.md) .

## <a name="news-publisher-enhancements"></a>Améliorations de l’éditeur de News

Avec iOS 10, Apple autorisera les sociétés de magazines et les nouvelles organisations à blogueurs et éditeurs indépendants à s’inscrire et à fournir des produits et à fournir du contenu à l’application Apple News. Pour plus d’informations, consultez la documentation sur les [ressources](https://newsresources.apple.com/) d’Apple.

## <a name="providing-haptic-feedback"></a>Activation du retour haptique

Sur les iPhone 7 et iPhone 7 plus, Apple a inclus de nouvelles réponses haptique qui offrent des moyens supplémentaires d’engagement physique de l’utilisateur. Utilisez les nouvelles options de commentaires tactiles pour attirer l’attention de l’utilisateur et renforcer ses actions.

Plusieurs éléments d’interface utilisateur intégrés fournissent déjà des commentaires haptique tels que des sélecteurs, des commutateurs et des curseurs. iOS 10 ajoute désormais la possibilité de déclencher par programmation des haptique à l’aide d’une sous-classe concrète de la classe `UIFeedbackGenerator`.

Pour en savoir plus, consultez notre guide de [Commentaires haptique](~/ios/user-interface/ios-ui/haptic-feedback.md) .

## <a name="proactive-suggestions"></a>Suggestions proactives

iOS 10 présente de nouvelles façons de conduire des engagements à une application en permettant au système de présenter de manière proactive des informations utiles à l’utilisateur à des moments opportuns. Tout comme iOS 9 offrait la possibilité d’ajouter une recherche poussée à l’application à l’aide de suggestions Spotlight, de remise et de Siri, avec iOS 10, une application peut exposer des fonctionnalités qui peuvent être présentées à l’utilisateur par le système depuis les emplacements suivants :

- Le sélecteur d’application
- L’écran de verrouillage
- CarPlay
- Maps
- Interactions Siri
- Suggestions QuickType

Une application expose cette fonctionnalité au système à l’aide d’une collection de technologies telles que [NSUserActivity](xref:Foundation.NSUserActivity), Web Markup, Core Spotlight, MapKit, Media Player et uikit.

Pour en savoir plus, consultez notre guide [de présentation des suggestions proactives](~/ios/platform/search/proactive-suggestions.md) .

## <a name="request-app-review"></a>Demander une révision d’application

Nouveauté d’iOS 10,3, la méthode `RequestReview()` permet à une application iOS de demander à l’utilisateur de la noter ou de la passer en revue. Bien que cette méthode puisse être appelée à tout moment dans le sens de l’expérience utilisateur, le processus de révision est régi et géré par la stratégie App Store. Par conséquent, cette méthode peut ou ne peut pas afficher une alerte et ne doit jamais être appelée en réponse à une action de l’utilisateur, par exemple en appuyant sur un bouton.

Pour en savoir plus, consultez notre guide de [révision des applications de demande](~/ios/platform/request-app-review.md) .

## <a name="security-and-privacy-enhancements"></a>Améliorations en matière de sécurité et de confidentialité

Apple a apporté plusieurs améliorations à la sécurité et à la confidentialité dans iOS 10, ce qui permet au développeur d’améliorer la sécurité de ses applications et de garantir la confidentialité de l’utilisateur final.

Par conséquent, les applications qui s’exécutent sur iOS 10 (ou version ultérieure) doivent déclarer de manière statique leur intention d’accéder à des fonctionnalités ou des informations utilisateur spécifiques en entrant une ou plusieurs clés spécifiques à la confidentialité dans leurs fichiers `Info.plist` qui expliquent à l’utilisateur pourquoi l’application souhaite obtenir un accès.

Pour en savoir plus, consultez notre guide d’amélioration de la [sécurité et de la confidentialité](~/ios/app-fundamentals/security-privacy.md) .

## <a name="sirikit"></a>SiriKit

Nouveauté d’iOS 10, SiriKit permet à une application Xamarin. iOS de fournir des services qui sont accessibles à l’utilisateur à l’aide de Siri sur un appareil iOS. Cette fonctionnalité est fournie dans une ou plusieurs extensions d’application à l’aide des nouvelles infrastructures d' **interface utilisateur** **intentions** et intentions.

SiriKit prend en charge les domaines de service suivants :

- Appel audio ou vidéo.
- Réservation d’une définition.
- Gestion des entraînements.
- Messag.
- Recherche de photos.
- Envoi ou réception de paiements.

Lorsque l’utilisateur effectue une requête de Siri impliquant l’un des services de l’extension d’application, SiriKit envoie à l’extension un objet d' **intention** qui décrit la demande de l’utilisateur ainsi que toutes les données de prise en charge. L’extension d’application génère ensuite l’objet de **réponse** approprié pour l' **intention**donnée, en détaillant la manière dont l’extension peut traiter la demande.

Bien que Siri gère habituellement toutes les interactions de l’utilisateur, l’extension de l’application peut utiliser l’infrastructure d' **interface** utilisateur pour présenter une interface utilisateur personnalisée riche avec la personnalisation de l’application et des informations supplémentaires.

Pour en savoir plus, consultez notre guide [de présentation du SiriKit](~/ios/platform/sirikit/index.md) .

## <a name="speech-recognition"></a>Reconnaissance vocale

iOS 10 comprend une nouvelle API de reconnaissance vocale qui permet à l’application de prendre en charge la reconnaissance vocale continue et de transcrire la parole (des flux audio en direct ou enregistrés) en texte.

Étant donné que la reconnaissance vocale requiert la transmission et le stockage temporaire des données sur les serveurs d’Apple, l’application _doit_ demander à l’utilisateur l’autorisation d’effectuer la reconnaissance en incluant la clé de `NSSpeechRecognitionUsageDescription` dans son fichier de `Info.plist` et en appelant la `SFSpeechRecognizer.RequestAutorization` méthode.

Pour en savoir plus, consultez notre guide [de présentation de la reconnaissance vocale](~/ios/platform/speech.md) .

## <a name="user-notifications"></a>Notifications de l’utilisateur

Nouveauté d’iOS 10, l’infrastructure de notification utilisateur permet la remise et la gestion des notifications locales et distantes. À l’aide de ce Framework, l’application ou l’extension d’application peut planifier la remise de notifications locales en spécifiant un ensemble de conditions, telles que l’emplacement ou l’heure de la journée.

En outre, l’application ou l’extension peut recevoir (et éventuellement modifier) des notifications locales et distantes quand elles sont remises à l’appareil iOS de l’utilisateur.

La nouvelle infrastructure d’interface utilisateur de notification utilisateur permet à l’extension d’application ou d’application de personnaliser l’apparence des notifications locales et distantes quand elles sont présentées à l’utilisateur.

Pour en savoir plus, consultez notre guide de l' [infrastructure des notifications utilisateur](~/ios/platform/user-notifications/index.md) .

## <a name="video-subscriber-account"></a>Compte d’abonné vidéo

Nouveauté pour iOS 10, l’infrastructure de compte d’abonné vidéo permet aux applications qui prennent en charge la diffusion en continu authentifiée ou la vidéo à la demande de s’authentifier auprès de leur câble ou de votre fournisseur de télévision par satellite à l’aide d’une expérience d’authentification unique pour l’utilisateur final.

## <a name="wide-color"></a>Couleur à spectre large

iOS 10 étend la prise en charge des formats de pixel étendus et des espaces de couleurs à grande échelle dans le système, y compris les infrastructures telles que Core Graphics, Core image, Metal et AVFoundation. La prise en charge des appareils avec des affichages de couleurs larges est encore plus facilitée en fournissant ce comportement dans l’ensemble de la pile graphique.

En outre, [UIKit](xref:UIKit) a été modifié pour fonctionner dans le nouveau colorspace **sRVB** étendu, ce qui facilite le mixage des couleurs dans des gammes de couleurs larges sans perte de performances significative.

Apple offre les meilleures pratiques suivantes lorsque vous travaillez avec des couleurs larges :

- [UIColor](xref:UIKit.UIColor) utilise désormais l’espace de couleurs sRVB et ne bride plus les valeurs de la `0.0` à `1.0` plage. Si l’application s’appuie sur le comportement de verrouillage précédent, elle doit être modifiée pour iOS 10.
- L’environnement de dessin sera configuré pour l’espace de couleurs sRVB lors de l’exécution personnalisée de `UIView` dessin sur un iPad Pro.
- Si l’application effectue un rendu personnalisé de `UIImages`, utilisez la nouvelle classe [UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer) pour spécifier l’utilisation des formats de plage étendue ou standard.
- Lorsque vous utilisez une API de bas niveau, telle que des graphiques de base ou du métal, pour fournir un traitement d’image, le développeur doit utiliser un espace de couleurs de plage étendue et un format de pixel qui prend en charge les valeurs à virgule flottante 16 bits. Le cas échéant, le développeur devra fixer manuellement les valeurs des composants de couleur.
- Core Graphics, Core image et Metal performance Nuancers fournissent de nouvelles méthodes pour la conversion entre les deux espaces de couleurs.

Pour en savoir plus, consultez notre guide [de présentation des couleurs larges](~/ios/platform/wide-color.md) .

## <a name="widget-enhancements"></a>Améliorations des widgets

Apple a introduit plusieurs améliorations du système widget pour s’assurer que les widgets s’affichent parfaitement sur n’importe quel arrière-plan existant sur le nouvel écran de verrouillage iOS 10. La propriété [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect) a été dépréciée et a été remplacée par les nouvelles propriétés [WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect) ou [WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect) . En outre, les widgets contiennent désormais une propriété [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) qui permet au développeur de décrire la quantité de contenu disponible et permet à l’utilisateur de développer et de réduire le contenu.

Pour en savoir plus, consultez notre guide de [recherche et améliorations du widget d’écran d’accueil](~/ios/platform/search/widgets.md) .

## <a name="additional-framework-changes"></a>Modifications supplémentaires de l’infrastructure

Outre les principales modifications apportées à l’infrastructure et les ajouts ci-dessus, Apple a apporté de nombreuses modifications d’infrastructure mineures supplémentaires dans iOS 10.

Pour en savoir plus, consultez notre guide [supplémentaire sur les modifications](~/ios/platform/introduction-to-ios10/additional-framework-changes.md) de l’infrastructure.

## <a name="deprecated-apis"></a>API déconseillées

Les API suivantes ont été dépréciées dans iOS 10 :

- Les classes `CKDiscoverAllContactsOperation`, `CKDiscoveredUserInfo`, `CKDiscoverUserInfosOperation` et `CKFetchRecordChangesOperation` ont été dépréciées dans CloudKit pour iOS 10. Utilisez les classes [CKDiscoverAllUserIdentitiesOperation](xref:CloudKit.CKDiscoverUserIdentitiesOperation), [CKUserIdentity](xref:CloudKit.CKUserIdentity) et [CKFetchRecordZoneChangesOperation](xref:CloudKit.CKFetchRecordZoneChangesOperation) (qui prennent en charge le partage d’enregistrements) à la place.
- Plusieurs API [CKSubscription](https://developer.apple.com/reference/cloudkit/cksubscription) , telles que les abonnements basés sur une zone et les abonnements basés sur des requêtes, ont été dépréciées. Utilisez plutôt les API [CKRecordZoneSubscription](xref:CloudKit.CKRecordZoneSubscription) et [CKQuerySubscription](xref:CloudKit.CKQuerySubscription) .
- Les symboles [NSPersistentStoreCoordinator](xref:CoreData.NSPersistentStoreCoordinator) liés au contenu omniprésent sont dépréciés.
- `ADBannerView`, `ADInterstitialAd` et les symboles associés dans la classe [UIViewController](xref:UIKit.UIViewController) ont été dépréciés.
- Les symboles [SKUniform](https://developer.apple.com/reference/spritekit/skuniform) liés aux valeurs à virgule flottante ont été dépréciés.
- Les classes `UILocalNotification`, `UIMutableUserNotificationAction`, `UIMutableUserNotificationCategory`, `UIUserNotificationAction`, `UIUserNotificationCategory` et `UIUserNotificationSettings` de UIKit ont été dépréciées. Utilisez plutôt l’infrastructure de [notifications utilisateur](#user-notifications) .
- Les méthodes `HandleActionForLocalNotification`, `HandleActionForRemoteNotification`, `DidReceiveLocalNotification` et `DidReceiveRemoteNotification` WatchKit sont dépréciées. Utilisez plutôt les méthodes `HandleActionForNotification` et `DidReceiveNotification`.
- Les méthodes `DidReceiveLocalNotification` et `DidReceiveRemoteNotification` de [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate) ont été dépréciées. Créez une instance de [UNUserNotificationCenterDelegate](https://developer.apple.com/reference/usernotifications/unusernotificationcenterdelegate) qui implémente les méthodes appropriées et assignez-la à la propriété `Delegate` de l’objet [UNUserNotificationCenter](https://developer.apple.com/reference/usernotifications/unusernotificationcenter) .
- L' **application Game Center** a été dépréciée et supprimée d’iOS. Si l’application utilise GameKit, elle _doit_ présenter sa propre interface pour afficher les fonctionnalités GameKit telles que Leaderboards, etc.

Consultez la documentation sur les [différences d’API ios 9,3 pour ios 10,0](https://developer.apple.com/library/prerelease/content/releasenotes/General/iOS10APIDiffs/index.html) pour obtenir une liste complète des désapprobations.

## <a name="related-links"></a>Liens associés

- [Exemples iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
