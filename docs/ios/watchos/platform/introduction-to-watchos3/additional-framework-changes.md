---
title: Modifications supplémentaires apportées aux infrastructures Watchos 3
description: Ce document décrit les différentes modifications apportées à l’infrastructure présentées dans Watchos 3 et explique comment les utiliser dans Xamarin. Les données principales, le mouvement de base, Foundation, HealthKit, HomeKit, PassKit et UIKit sont abordés.
ms.prod: xamarin
ms.assetid: FE93796E-F699-4B14-B37D-D39F9D48E81E
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: cd4bc8dbc02a44807ec197d39349971d8f9cd6f9
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768579"
---
# <a name="additional-watchos-3-frameworks-changes"></a>Modifications supplémentaires apportées aux infrastructures Watchos 3

_Cet article traite des modifications supplémentaires ou des améliorations apportées aux infrastructures existantes pour Watchos 3._

Outre les modifications majeures apportées à iOS, Apple a apporté des modifications et des améliorations à plusieurs infrastructures existantes dans Watchos 3.

## <a name="core-data"></a>Données de base

Les améliorations suivantes ont été apportées à l’infrastructure de données de base pour la surveillance du système d’exploitation 3 :

- Les objets [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) racines prennent en charge l’erreur et l’extraction simultanés sans sérialisation.
- La classe [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) gère un pool de banques de données SQLite.
- Les objets [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) avec les banques de données sqlite dans le mode journal de Wal prennent en charge la nouvelle fonctionnalité de génération de requêtes dans laquelle les contextes d’objets managés (MOC) peuvent être épinglés à des versions de base de données spécifiques pour des transactions de récupération et de défaillance futures.
- Utilisation du niveau `NSPersistenceContainer` élevé pour référencer les `NSPersistentStoreCoordinator`ressources de configuration de données principales, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) et autres.
- Plusieurs nouvelles méthodes pratiques ont été ajoutées pour `NSManagedObject` faciliter l’exécution des extractions et la création de sous-classes.

Pour plus d’informations, consultez Référence de l' [infrastructure de données de base](https://developer.apple.com/reference/coredata)d’Apple.

## <a name="core-motion"></a>Mouvement de base

Les améliorations suivantes ont été apportées à l’infrastructure Motion Core pour le système d’exploitation Watch 3 :

- Le nouvel événement de mouvement d’appareil utilise l’accéléromètre et le gyroscope pour fournir des mises à jour d’orientation et de mouvement. L’application peut s’inscrire à cette mise à jour (à des tarifs allant jusqu’à 100Hz).
- Le nouvel événement Pedometer permet des notifications rapides et en temps réel lorsque l’utilisateur s’arrête et reprend son exécution. Utilisez [CMPedometer](https://developer.apple.com/reference/coremotion/cmpedometer) pour vous inscrire aux événements de premier plan ou d’arrière-plan Pedometer.

## <a name="foundation"></a>Pierre

Les améliorations suivantes ont été apportées à l’infrastructure de base pour la surveillance du système d’exploitation 3 :

- Utilisez la nouvelle classe [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) pour effectuer des calculs d’intervalles de date et d’heure tels que des durées, pour comparer des intervalles et tester des intersections d’intervalle.
- Plusieurs nouvelles propriétés ont été ajoutées à la classe [NSLocal](https://developer.apple.com/reference/foundation/nslocale) pour obtenir des informations locales et les formats d’affichage disponibles.
- Utilisez la nouvelle classe [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) pour effectuer une conversion entre différentes unités de mesure ou effectuer des calculs sur des valeurs dans différents UOMs.
- Utilisez la nouvelle classe [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) pour mettre en forme les mesures localisées à afficher à l’utilisateur final.
- Utilisez les nouvelles classes [NSUnit](https://developer.apple.com/reference/foundation/nsunit) et [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) pour représenter des UOMs spécifiques.

## <a name="healthkit"></a>HealthKit

Les améliorations suivantes ont été apportées à l’infrastructure HealthKit pour la surveillance du système d’exploitation 3 :

- Utilisez la nouvelle classe [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) pour spécifier le `ActivityType` et `LocationType` un entraînement.
- Le nouveau [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) et la `WheelchairUse` méthode de la classe [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) ont été ajoutés pour l’utilisation des données d’intégrité liées aux fauteuils roulants.
- De nouvelles clés de métadonnées ont été ajoutées pour les types `HKWeatherConditionClear` météo `HKWeatherConditionCloudy`(tels que et) et les `HKWorkoutActivityTypeFlexibility` types `HKWorkoutActivityTypeWheelchairRunPace`d’entraînement (tels que et) ont été ajoutés.

## <a name="homekit"></a>HomeKit

Les améliorations suivantes ont été apportées à l’infrastructure HomeKit pour la surveillance du système d’exploitation 3 :

- Ajout de la possibilité d’afficher et d’interagir avec les caméras IP connectées à HomeKit.
- Ajout de plusieurs nouveaux services et caractéristiques.
- Ajout du contexte et de la configuration supplémentaires des accessoires des services principaux et des services de liaison.

## <a name="passkit"></a>PassKit

Les améliorations suivantes ont été apportées à l’infrastructure PassKit pour la surveillance du système d’exploitation 3 :

- Développe l’infrastructure pour prendre en charge les paiements dans l’application sécurisés sur le Apple Watch des biens et services physiques.
- Les classes suivantes sont désormais disponibles : [PKPayment](https://developer.apple.com/reference/passkit/pkpayment), [PKPaymentMethod](https://developer.apple.com/reference/passkit/pkpaymentmethod), [PKPaymentRequest](https://developer.apple.com/reference/passkit/pkpaymentrequest) et [PKPaymentToken](https://developer.apple.com/reference/passkit/pkpaymenttoken)

## <a name="uikit"></a>UIKit

Les améliorations suivantes ont été apportées à l’infrastructure UIKit pour la surveillance du système d’exploitation 3 :

- Pour prendre en charge le type dynamique dans les étiquettes, les champs de texte `PreferredFontForTextStyle` et les zones `UIFont` de texte, utilisez la nouvelle méthode de la classe.
- La `ColorWithDisplayP3` méthode a été ajoutée pour prendre en charge la couleur étendue.

## <a name="related-links"></a>Liens associés

- [exemples Watchos](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS%20watchos)
- [Nouveautés de Watchos 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
