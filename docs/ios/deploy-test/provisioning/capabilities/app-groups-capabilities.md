---
title: Fonctionnalités App Group dans Xamarin.iOS
description: L’ajout de fonctionnalités à une application nécessite souvent une configuration supplémentaire du provisionnement. Ce guide explique la configuration requise pour les fonctionnalités App Group.
ms.prod: xamarin
ms.assetid: 0A61220B-BBAC-492B-9D3B-578986E64064
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: 0dcc0c9499a1f4085e489fe6d8dcfa3fbf2fa3e0
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832416"
---
# <a name="app-group-capabilities-in-xamarinios"></a>Fonctionnalités App Group dans Xamarin.iOS

_L’ajout de fonctionnalités à une application nécessite souvent une configuration plus poussée de l’approvisionnement. Ce guide explique la configuration requise pour les fonctionnalités App Group._

Un App Group est un groupe qui permet à différentes applications (ou à une application et ses extensions) d’accéder à un emplacement partagé du stockage de fichiers. Vous pouvez utiliser des App Groups pour les données suivantes, par exemple :

*   [Paramètres Apple Watch](~/ios/watchos/app-fundamentals/settings.md)
*   [Valeurs NSUserDefaults partagées](~/ios/app-fundamentals/user-defaults.md)
*   [Fichiers partagés](~/ios/watchos/app-fundamentals/parent-app.md#files)

## <a name="configure-a-new-app-group"></a>Configurer un nouvel App Group

Configurez l’emplacement partagé à l’aide d’un [App Group](https://developer.apple.com/library/content/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19), qui est lui-même configuré dans la section **Certificates, Identifiers & Profiles** du [developer center d’Apple](https://developer.apple.com/account/). Cette valeur doit également être référencée dans le fichier Entitlements.plist de chaque projet.

Le groupe d’applications a un identificateur, qui correspond généralement à l’ID de bundle avec un groupe. « group. ». Par exemple, l’ID de bundle `com.xamarin.WatchSettings` a pour groupe d’applications  `group.com.xamarin.WatchSettings`.

Pour créer un App Group, effectuez les étapes suivantes :

1. Accédez au  [iOS Developer Center](https://developer.apple.com/account/) d’Apple et ouvrez une session avec votre **compte**.
2. Sélectionnez **Certificates, Identifiers & Profiles**.
3. Sous **Identifiers**, sélectionnez **App Groups** et cliquez sur le bouton **+** pour créer un groupe.
4. Entrez un **nom** et un **identificateur** pour le nouveau groupe, puis cliquez sur le bouton **Continue** : 
   
    ![Détails de l’ajout d’un App Group](app-groups-capabilities-images/image52.png)

5. Cliquez sur le bouton **Register** pour créer le groupe et sur **Done** pour revenir à la liste des App Groups inscrits.

## <a name="configure-an-app-to-use-app-groups"></a>Configurer une application pour utiliser des App Groups

Une fois que vous avez créé l’App Group, configurez les ID d’application pour permettre aux applications de l’utiliser.

Effectuez ce qui suit :

1. Accédez au  [Centre des développeurs iOS](https://developer.apple.com/account/) d’Apple, puis connectez-vous à l’aide d’un compte de développeur Apple.
2. Dans le menu **Program Resources**, sélectionnez **Certificates, Identifiers & Profiles**.
3. Sous **Identifiers**, sélectionnez **App IDs** et cliquez sur le bouton **+** pour créer un ID d’application.
4. Entrez un nom pour l’ID d’application et attribuez-lui un ID d’application explicite.
5. Sous **App Services**, activez **App Groups**, puis cliquez sur le bouton Continue :

    ![Ajouter un App Group (App Services)](app-groups-capabilities-images/image53.png)

6. Vérifiez les paramètres, puis cliquez sur le bouton **Register** pour créer l’ID d’application.
7. Cliquez sur le bouton **Done** pour revenir à la liste des ID d’application inscrits.
8. Sélectionnez le nouvel ID d’application dans la liste et cliquez sur le bouton **Edit** :

    ![Sélectionner l’ID d’application dans la liste](app-groups-capabilities-images/image54.png)

9. Sous le service **App Groups**, cliquez sur le bouton **Edit** :

    ![Sélectionner l’ID d’application dans la liste](app-groups-capabilities-images/image55.png)

10. Sélectionnez l’App Group créé plus haut, puis cliquez sur le bouton **Continue** :

    ![Ajouter un App Group](app-groups-capabilities-images/image56.png)

11. Cliquez sur le bouton **Assign**, puis sur le bouton **Done** pour revenir à la liste des ID d’application inscrits.
12. Répétez ces étapes pour chaque application (ou extension) devant utiliser l’App Group.

## <a name="next-steps"></a>Étapes suivantes
 
S’il y a lieu, effectuez les étapes supplémentaires décrites ci-après :

* Utilisez l’espace de noms de framework dans votre application.
* Ajoutez les droits nécessaires à votre application. Pour obtenir des informations détaillées sur les droits nécessaires et leur ajout, consultez le guide [Utilisation de droits](~/ios/deploy-test/provisioning/entitlements.md).
* Assurez-vous que, dans la section **Signature du bundle iOS** de l’application, le paramètre **Droits personnalisés** est défini sur **Entitlements.plist**. Il ne s’agit  _pas_ du paramètre par défaut pour les builds Debug et iOS Simulator.

Si vous rencontrez des problèmes avec les services d’application, consultez la section [Dépannage](~/ios/deploy-test/provisioning/capabilities/index.md) du guide principal.
