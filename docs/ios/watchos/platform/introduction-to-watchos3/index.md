---
title: Introduction à watchOS 3
description: Cet article présente toutes les API et fonctionnalités nouvelles et modifiées disponibles dans Watchos 3 pour les développeurs Xamarin.
ms.prod: xamarin
ms.assetid: B8ABE1E1-8688-4262-BE66-A16813C2D671
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/07/2017
ms.openlocfilehash: abe9c95c889aed5258ea3a5367e05368ddb7c77f
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574169"
---
# <a name="introduction-to-watchos-3"></a>Introduction à watchOS 3

_Cet article présente toutes les API et fonctionnalités nouvelles et modifiées disponibles dans Watchos 3 pour les développeurs Xamarin._

Ce document couvre les sujets suivants :

- [Nouveautés de Watchos 3](#Whats-New-in-watchOS-3)
  - [Apple Pay améliorations](#Apple-Pay-Enhancements) ajoute la prise en charge des paiements dans l’application sur le Apple Watch.
  - Les [tâches en arrière-plan](#Background-Tasks) donnent à l’application la possibilité de mettre à jour ses informations en arrière-plan afin qu’elles soient prêtes lorsque l’utilisateur en a besoin.
  - Des [améliorations](#Complications-Enhancements) ont été apportées à Watchos 3, qui fournissent de nouvelles fonctionnalités pour les applications.
  - Les [nouvelles infrastructures disponibles](#Newly-Available-Frameworks) ont été exposées pour les applications Watchos.
  - Les suggestions proactives permettent à l’application d’afficher de manière [proactive](#Proactive-Suggestions) des informations à l’utilisateur.
  - Plusieurs [améliorations en matière de sécurité et de confidentialité](#Security-and-Privacy-Enhancements) ont été apportées à Watchos 3.
  - Les [instantanés et la station d’accueil](#Snapshots-and-Dock) offrent à l’utilisateur un accès rapide aux applications Watchos.
  - Les [notifications](#User-Notifications) de l’utilisateur fournissent à l’utilisateur des notifications locales et distantes.
  - Plusieurs [améliorations de l’infrastructure de connexion Watch](#Watch-Connectivity-Framework-Enhancements) ont été apportées dans Watchos 3.
  - Plusieurs améliorations apportées à [WatchKit Framework](#WatchKit-Framework-Enhancements) ont été apportées dans Watchos 3.
  - Les [améliorations apportées aux applications](#Workout-App-Enhancements) de l’entraînement offrent de nouvelles fonctionnalités à l’entraînement Apple Watch applications.
- [Des modifications d’infrastructure supplémentaires](#Additional-Framework-Changes) ont été apportées dans Watchos 3.
- [API déconseillées](#Deprecated-APIs) dans Watchos 3.

<a name="Whats-New-in-watchOS-3"></a>

## <a name="whats-new-in-watchos-3"></a>Nouveautés de Watchos 3

Apple a ajouté plusieurs nouvelles API et services dans Watchos 3, ainsi que de nombreuses améliorations apportées aux fonctionnalités existantes, notamment :

<a name="Apple-Pay-Enhancements"></a>

## <a name="apple-pay-enhancements"></a>Améliorations apportées à Apple Pay

Dans Watchos 3, l’infrastructure PassKit a été développée pour permettre la prise en charge des paiements dans l’application sécurisés (des biens et services physiques) pour les applications qui s’exécutent sur le Apple Watch.

Utilisez les nouvelles classes [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) et [PKPaymentAuthorizationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate) pour présenter et répondre à une interface dans laquelle l’utilisateur peut autoriser les demandes de paiement.

Pour en savoir plus, consultez notre guide d’amélioration de l' [Apple Pay](~/ios/watchos/platform/apple-pay.md) .

<a name="Background-Tasks"></a>

## <a name="background-tasks"></a>Tâches d’arrière-plan

Watchos 3 introduit plusieurs tâches en arrière-plan qu’une application peut utiliser pour mettre à jour ses informations afin de s’assurer qu’elle dispose du contenu dont l’utilisateur a besoin avant de l’ouvrir.

Les nouvelles tâches en arrière-plan suivantes sont disponibles :

- **Actualisation** de l’application en arrière-plan : la tâche [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) permet à l’application de mettre à jour son état en arrière-plan. Cela inclut généralement une autre tâche, par exemple le téléchargement d’un nouveau contenu à partir d’Internet à l’aide d’un [passer](https://developer.apple.com/reference/foundation/nsurlsession).
- **Actualisation des instantanés d’arrière-plan** : la tâche [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) permet à l’application de mettre à jour son contenu et son interface utilisateur avant que le système prenne un instantané qui sera utilisé pour remplir l’ancre.
- **Connectivité de surveillance en arrière-plan** : la tâche [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) est démarrée pour l’application lorsqu’elle reçoit des données d’arrière-plan de l’iPhone couplé.
- **Session d’URL d’arrière-plan** : la tâche [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) est démarrée pour l’application lorsqu’un transfert en arrière-plan requiert une autorisation ou se termine (avec succès ou par erreur).

Pour en savoir plus, consultez notre guide des [tâches en arrière-plan](~/ios/watchos/platform/background-tasks.md) .

<a name="Complications-Enhancements"></a>

## <a name="complications-enhancements"></a>Améliorations des complications

Les complications sont de petits éléments visuels qui fournissent des informations utiles en un clin d’œil. Selon la facette de montre sélectionnée, l’utilisateur a la possibilité de personnaliser un visage de montre avec une ou plusieurs complications.

Watchos 3 donne à l’application la possibilité de créer une ou plusieurs complications pour l’application Watch afin que l’utilisateur puisse accéder à ses informations en un clin d’œil à partir d’un visage de montre.

En outre, les complications offrent les avantages suivants :

- L’utilisateur peut rapidement lancer l’application en appuyant sur la complication directement à partir d’une face de montre.
- Le fait de disposer de l’une des complications de l’application sur la face de la montre amène le système à conserver l’application dans un état prêt à être lancé, où elle tente de lancer l’application en arrière-plan, de la conserver en mémoire et de la mettre à jour plus longtemps.
- Les complications sont garanties au moins 50 mises à jour push par jour.
- Lorsque l’application comporte des complications, elle est proposée dans la Galerie de visages Apple Watch.

Dans Watchos 3, l’infrastructure ClockKit comprend désormais plusieurs nouveaux modèles pour les complications importantes telles que [CLKComplicationTemplateExtraLargeColumnsText](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargecolumnstext) et [CLKComplicationTemplateExtraLargeRingImage](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargeringimage). En outre, pour créer du texte localisable, utilisez les nouvelles méthodes de la classe [CLKTextProvider](https://developer.apple.com/reference/clockkit/clktextprovider) .

Pour en savoir plus, consultez notre guide [des techniques d’interaction rapide pour Watchos 3](~/ios/watchos/platform/quick-interaction-techniques.md) .

<a name="Newly-Available-Frameworks"></a>

## <a name="newly-available-frameworks"></a>Nouvelles infrastructures disponibles

Watchos 3 comprend plusieurs frameworks Apple existants qui étaient auparavant indisponibles, tels que :

- **SceneKit** : utilisez SceneKit pour inclure des modèles 3D dans l’interface utilisateur de l’application espion, y compris la plupart des fonctionnalités disponibles sur d’autres plateformes, telles que l’éclairage, l’ombrage, l’animation, la physique et les systèmes de particules. l’audio spatial 3D, les nuanceurs métalliques personnalisés ou OpenGL, les filtres d’image de base et les matériaux basés sur physiquement ne sont pas pris en charge.
- **SpriteKit** : utilisez SpriteKit pour afficher et animer des sprites dans l’interface utilisateur de l’application espion d’application, y compris la plupart des fonctionnalités disponibles sur d’autres plateformes, telles que les actions, la physique, l’éclairage et les systèmes de particule. les filtres audio spatial 3D, de lecture vidéo et d’image principale ne sont pas pris en charge.
- **AVFoundation** : pour gérer et lire des données audio.
- **CloudKit** : permet de déplacer des données entre l’application espion et les conteneurs iCloud.
- **Audio de base** : pour gérer des types de données pour représenter des flux audio, des mémoires tampons et des valeurs d’heure complexes.
- **GameKit** : pour créer des jeux de réseaux sociaux.

<a name="Proactive-Suggestions"></a>

## <a name="proactive-suggestions"></a>Suggestions proactives

Watchos 3 permet à l’application de présenter de manière proactive des informations à l’utilisateur dans des contextes donnés. Pour prendre en charge cette fonctionnalité, [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) comprend désormais la `MapItem` propriété qui permet à l’application de fournir des informations d’emplacement pour une utilisation ultérieure par d’autres applications.

Pour en savoir plus, consultez notre guide [de présentation des suggestions proactives](~/ios/watchos/platform/proactive-suggestions.md) .

<a name="Security-and-Privacy-Enhancements"></a>

## <a name="security-and-privacy-enhancements"></a>Améliorations en matière de sécurité et de confidentialité

Apple a apporté plusieurs améliorations à la sécurité et à la confidentialité dans Watchos 3, ce qui permet au développeur d’améliorer la sécurité de ses applications et de garantir la confidentialité de l’utilisateur final.

Par conséquent, les applications qui s’exécutent sur Watchos 3 (ou version ultérieure) doivent déclarer de manière statique leur intention d’accéder à des fonctionnalités ou des informations utilisateur spécifiques en entrant une ou plusieurs clés spécifiques à la confidentialité dans leurs `Info.plist` fichiers qui expliquent à l’utilisateur pourquoi l’application souhaite obtenir un accès.

Étant donné que Watchos 3 partage ces modifications avec iOS 10, consultez notre guide d’amélioration de la [sécurité et](~/ios/app-fundamentals/security-privacy.md) de la confidentialité d’iOS 10 pour plus d’informations.

<a name="Snapshots-and-Dock"></a>

## <a name="snapshots-and-dock"></a>Captures instantanées et Dock

Dans Watchos 3, Apple a ajouté la station d’accueil dans laquelle les utilisateurs peuvent épingler leurs applications favorites et y accéder rapidement. Quand l’utilisateur appuie sur le bouton latéral sur la Apple Watch, une galerie d’instantanés d’application épinglés s’affiche. L’utilisateur peut balayer vers la gauche ou la droite pour trouver l’application souhaitée, puis appuyer sur l’application pour la lancer et remplacer l’instantané par l’interface de l’application en cours d’exécution.

Le système prend régulièrement des instantanés de l’interface utilisateur de l’application et utilise ces instantanés pour remplir les documents. Watchos donne à l’application la possibilité de mettre à jour son contenu et son interface utilisateur avant que cet instantané ne soit pris.

Pour plus d’informations, consultez notre guide des [tâches en arrière-plan](~/ios/watchos/platform/background-tasks.md) et [référence WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) d’Apple.

<a name="User-Notifications"></a>

## <a name="user-notifications"></a>Notifications de l’utilisateur

L’infrastructure de notification utilisateur introduite dans Watchos 3 prend en charge la remise des notifications locales et distantes au Apple Watch. Utilisez cette infrastructure pour planifier des notifications en fonction de conditions spécifiques, telles que l’heure de la journée ou de l’emplacement, et pour recevoir et gérer les notifications.

Pour en savoir plus, consultez notre guide [des techniques d’interaction rapide pour Watchos 3](~/ios/watchos/platform/quick-interaction-techniques.md) .

<a name="Watch-Connectivity-Framework-Enhancements"></a>

## <a name="watch-connectivity-framework-enhancements"></a>Surveiller les améliorations de l’infrastructure de connectivité

La nouvelle `HasContentPending` propriété de la classe [WCSession](https://developer.apple.com/reference/watchconnectivity/wcsession) indique que la session a reçu des données en arrière-plan qui doivent être traitées. Et la `RemainingComplicationUserInfoTransfers` propriété retourne les heures restantes pendant lesquelles l’application iOS peut mettre à jour sa complication Watchos.

Pour en savoir plus, consultez notre guide des [tâches en arrière-plan](~/ios/watchos/platform/background-tasks.md) .

<a name="WatchKit-Framework-Enhancements"></a>

## <a name="watchkit-framework-enhancements"></a>Améliorations apportées à WatchKit Framework

Watchos 3 inclut plusieurs améliorations de l’infrastructure WatchKit, notamment les suivantes :

- L’application peut obtenir l’état de la Digital Crown à l’aide de la nouvelle classe [WKCrownSequencer](https://developer.apple.com/reference/watchkit/wkcrownsequencer) et recevoir des mises à jour lorsque l’utilisateur fait tourner la Couronne à l’aide de la classe [WKCrownDelegate](https://developer.apple.com/reference/watchkit/wkcrowndelegate) .
- La classe [WKExtension](https://developer.apple.com/reference/watchkit/wkextension) comprend désormais la `ApplicationState` méthode et la constante [WKApplicationState](https://developer.apple.com/reference/watchkit/wkapplicationstate) que l’application peut utiliser pour suivre l’état d’exécution de l’application. `WKExtension`fournit également deux nouvelles méthodes qui peuvent être utilisées pour planifier des tâches en arrière-plan.
- Le [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate) comprend maintenant les nouvelles `ApplicationWillEnterForeground` `ApplicationDidEnterBackground` méthodes, et `HandleBackgroundTasks` pour surveiller les modifications de l’état de l’application et gérer les mises à jour des tâches en arrière-plan.
- Une nouvelle classe [WKGestureRecognizer](https://developer.apple.com/reference/watchkit/wkgesturerecognizer) a été ajoutée pour fournir les types de reconnaissance de mouvement suivants aux applications Watch : [WKLongPressGestureRecognizer](https://developer.apple.com/reference/watchkit/wklongpressgesturerecognizer), [WKPanGestureRecognizer](https://developer.apple.com/reference/watchkit/wkpangesturerecognizer), [WKSwipeGestureRecognizer](https://developer.apple.com/reference/watchkit/wkswipegesturerecognizer) et [WKTapGestureRecognizer](https://developer.apple.com/reference/watchkit/wktapgesturerecognizer).
- La nouvelle classe [WKinterfaceHMCamera](https://developer.apple.com/reference/watchkit/wkinterfacehmcamera) fournit une interface pour tout appareil photo IP attaché à HomeKit.
- La nouvelle classe [WKInterfaceInlineMovie](https://developer.apple.com/reference/watchkit/wkinterfaceinlinemovie) permet à l’application d’afficher une « affiche » de film qui est remplacée par le film en cours d’exécution lorsque l’utilisateur le fait glisser.
- La nouvelle classe [WKInterfacePaymentButton](https://developer.apple.com/reference/watchkit/wkinterfacepaymentbutton) permet à l’application de présenter un bouton Apple Pay dans son interface utilisateur qui lance une demande de paiement lorsqu’elle est exploitée.
- La nouvelle classe [WKInterfaceSCNScene](https://developer.apple.com/reference/watchkit/wkinterfacescnscene) présente une interface permettant d’afficher une scène SceneKit sur le Apple Watch.
- La nouvelle classe [WKInterfaceSKScene](https://developer.apple.com/reference/watchkit/wkinterfaceskscene) présente une interface permettant d’afficher une scène SpriteKit sur le Apple Watch.

Pour en savoir plus, consultez notre guide [des techniques d’interaction rapide pour Watchos 3](~/ios/watchos/platform/quick-interaction-techniques.md) .

<a name="Workout-App-Enhancements"></a>

## <a name="workout-app-enhancements"></a>Améliorations apportées à l’application Exercice

Nouveauté de Watchos 3, les applications associées à l’entraînement ont la possibilité de s’exécuter en arrière-plan sur le Apple Watch. Pour activer cette fonctionnalité (et accéder aux données HealthKit), l’application doit inclure la `WKBackgroundModes` clé dans le `Info.plist` fichier avec la valeur `workout-processing` .

En outre, le développeur a désormais la possibilité de lancer l’application Watchos dans la version de l’application iOS sur l’iPhone couplé.

Pour en savoir plus, consultez notre guide d’amélioration de l' [application d’entraînement](~/ios/watchos/platform/workout-apps.md) .

<a name="Additional-Framework-Changes"></a>

## <a name="additional-framework-changes"></a>Modifications supplémentaires de l’infrastructure

Outre les principales modifications apportées à l’infrastructure et les ajouts ci-dessus, Apple a apporté de nombreuses modifications d’infrastructure mineures supplémentaires dans Watchos 3.

Pour en savoir plus, consultez notre guide [supplémentaire sur les modifications](~/ios/watchos/platform/introduction-to-watchos3/additional-framework-changes.md) de l’infrastructure.

<a name="Deprecated-APIs"></a>

## <a name="deprecated-apis"></a>API déconseillées

Les API suivantes ont été dépréciées dans Watchos 3 :

- La `UILocalNotification` classe de UIKit est dépréciée et doit être remplacée par l’infrastructure de notification de l’utilisateur.

Pour obtenir la liste complète des désapprobations et modifications, consultez la documentation des différences d’Appleos [2,2 à watchos 3,0](https://developer.apple.com/library/prerelease/content/releasenotes/General/watchOS30APIDiffs/index.html) .

## <a name="related-links"></a>Liens connexes

- [Exemples watchOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+watchOS)
- [Nouveautés de Watchos 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
