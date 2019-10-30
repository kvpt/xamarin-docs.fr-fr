---
title: Ressources tvOS et stockage des données dans Xamarin
description: Cet article explique comment utiliser les ressources et le stockage de données persistant dans une application tvOS créée avec Xamarin. Il aborde le stockage de données iCloud et les ressources à la demande.
ms.prod: xamarin
ms.assetid: C56B5046-D2C0-4B63-9CE0-ADAA0EFD368A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: d84752afd5579216272895f750522a8f38ece34a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030806"
---
# <a name="tvos-resources-and-data-storage-in-xamarin"></a>Ressources tvOS et stockage des données dans Xamarin

_Cet article traite de l’utilisation des ressources et du stockage de données persistant dans une application Xamarin. tvOS._

<a name="tvOS-Resource-Limitations" />

## <a name="tvos-resource-limitations"></a>Limitations des ressources tvOS

Contrairement aux appareils iOS, le nouveau Apple TV offre un stockage local et permanent extrêmement limité pour les applications ou données tvOS. Pour les très petits éléments (par exemple, les préférences de l’utilisateur), votre application tvOS a toujours accès à `NSUserDefaults` avec une [limite de 500 Ko de données](https://forums.developer.apple.com/message/50696#50696). Toutefois, si votre application Xamarin. tvOS doit conserver de plus grandes quantités d’informations, elle doit stocker et récupérer ces données à partir de [icloud](#iCloud-Data-Storage).

En outre, tvOS limite la taille d’une application Apple TV à 200 Mo. Si votre application nécessite des ressources qui dépassent cette taille, elles doivent être empaquetées et chargées à l’aide [de ressources à la demande](#On-Demand-Resources) (jusqu’à 2 Go supplémentaires). Compte tenu de ces limitations, il est essentiel que vous Timez correctement le téléchargement de ressources supplémentaires pour offrir la meilleure expérience aux utilisateurs de votre application. Pour plus d’informations, consultez [le guide des ressources à la demande](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083)d’Apple.

<a name="Non-Persistent-Downloads" />

## <a name="non-persistent-downloads"></a>Téléchargements non persistants

Chaque application tvOS est fournie dans un répertoire de cache temporaire sur lequel ses ressources et ressources supplémentaires sont téléchargées. Ce répertoire est conservé tant que l’application est toujours en cours d’exécution. Toutefois, étant donné que la TV Apple doit libérer de la place pour d’autres applications ou l’utilisation du système, ce cache peut être supprimé.

Par conséquent, votre application ne peut pas compter sur la disponibilité du contenu précédemment téléchargé la prochaine fois qu’elle est lancée. Votre application Xamarin. tvOS doit toujours vérifier l’existence des ressources requises et les télécharger si nécessaire.

> [!IMPORTANT]
> Si vous avez la possibilité de télécharger d’autres ressources et ressources en fonction des besoins, Apple vous avertit de l’utilisation de tout l’espace dans le cache de votre application, car cela peut entraîner des résultats imprévisibles.

<a name="Managing-Resources" />

## <a name="managing-resources"></a>Gestion des ressources

Comme indiqué ci-dessus, en raison du stockage limité et non persistant des informations disponibles pour les applications tvOS, ces restrictions nécessitent une planification minutieuse pour créer une expérience utilisateur remarquable pour votre application Xamarin. tvOS.

<a name="iCloud-Data-Storage" />

### <a name="icloud-data-storage"></a>Stockage de données iCloud

Étant donné que le stockage sur la TV Apple est limité, non seulement un stockage local et persistant très limité, votre application n’a aucune garantie que les informations qu’elle a téléchargées précédemment seront disponibles lors de sa prochaine exécution.

Par conséquent, votre application Xamarin. tvOS doit stocker toutes les données utilisateur dans un magasin de données iCloud. Apple fournit deux options de stockage de données basées sur iCloud pour vos applications tvOS :

- **icloud key-value Storage (KVS)** : pour obtenir des informations de petite taille (moins de 1 Mo) dont votre application peut avoir besoin (comme les préférences de l’utilisateur), vous pouvez utiliser le stockage icloud KVS. les données KVS iCloud sont automatiquement synchronisées avec le Cloud et tous les appareils de l’utilisateur exécutent la même application. Pour plus d’informations, consultez la section relative au [stockage clé-valeur](~/ios/data-cloud/introduction-to-icloud.md) de notre [Introduction au document icloud](~/ios/data-cloud/introduction-to-icloud.md) ou à la conception d’Apple [pour les données de clé-valeur dans](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/iCloudDesignGuide/Chapters/DesigningForKey-ValueDataIniCloud.html#//apple_ref/doc/uid/TP40012094-CH7) la documentation iCloud.
- **CloudKit** : pour le stockage des informations plus volumineuses (supérieures à 1 Mo), utilisez l’infrastructure CloudKit d’Apple. Contrairement au stockage iCloud KVS, les données CloudKit peuvent être partagées entre tous les utilisateurs de votre application (et être privées pour un seul utilisateur). Pour plus d’informations, consultez notre [Introduction à](~/ios/data-cloud/intro-to-cloudkit.md) la documentation CloudKit ou à la [démarrage rapide CloudKit](https://developer.apple.com/library/prerelease/tvos/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987)d’Apple.

> [!IMPORTANT]
> Apple [fournit des outils](https://developer.apple.com/support/allowing-users-to-manage-data/) pour aider les développeurs à gérer correctement le Règlement général sur la protection des données (RGPD) de l’Union européenne.

<a name="On-Demand-Resources" />

### <a name="on-demand-resources"></a>Ressources à la demande

Les ressources à la demande fournissent le contenu et les ressources de l’application (séparées du bundle d’applications), qui sont hébergées sur l’App Store et téléchargées selon les besoins de votre application. Jusqu’à 2 Go de données supplémentaires peuvent être servies à l’aide de ressources à la demande. Ils permettent de réduire la taille du téléchargement de l’application initiale (les applications tvOS sont limitées à un maximum de 200 Mo), tout en fournissant des ressources riches en fonction des besoins.

Quand une application tvOS demande des ressources à la demande, le système gère automatiquement le téléchargement et le stockage de ce contenu dans le répertoire de cache de l’application. Votre application doit attendre que ce contenu soit téléchargé et mis à disposition avant de pouvoir continuer.

Ces ressources peuvent continuer à être mises en cache sur le téléviseur Apple dans plusieurs lancements de votre application, accélérant ainsi le cycle de lancement. Toutefois, votre application ne peut pas compter sur la disponibilité du contenu précédemment téléchargé la prochaine fois qu’elle est lancée. Pour plus d’informations, consultez la section [téléchargements non persistants](#Non-Persistent-Downloads) ci-dessus.

Vous utilisez Xcode pour créer des regroupements de contenu associé (par exemple, toutes les ressources pour le niveau de jeu 2) associés à une balise de ressource. Plus tard, votre application demandera une ressource à la demande en spécifiant cette balise de ressource. Votre application doit présenter à l’utilisateur une interface utilisateur indiquant que le contenu est en cours de téléchargement. Pour plus d’informations, consultez [le guide des ressources à la demande](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083)d’Apple.

> [!IMPORTANT]
> Veillez à prendre le bon équilibre entre le nombre de fois où l’application doit télécharger des ressources à la demande et la taille des téléchargements individuels. L’utilisateur peut devenir frustré avec votre application si le jeu est interrompu constamment pour télécharger un nouveau contenu ou si un téléchargement unique prend trop de temps.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a abordé les limites de taille, de ressources et de stockage des données placées sur une application Xamarin. tvOS par le système tvOS. Il a présenté des options pour contourner ces limitations et suggestions afin de créer une expérience utilisateur exceptionnelle pour votre application.

## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guides de l’interface utilisateur tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’applications pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
