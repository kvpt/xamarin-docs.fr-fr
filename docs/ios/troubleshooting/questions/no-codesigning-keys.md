---
title: Pourquoi ma build iOS échoue avec le message disant qu’aucun code iPhone valide pour signer les clés n’a été trouvé dans le trousseau ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9DF24C46-D521-4112-9B21-52EA4E8D90D0
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: e10a04627b903c02140a6a2ead5c379c1e8bdcf6
ms.sourcegitcommit: 13e43f510da37ad55f1c2f5de1913fb0aede6362
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71021387"
---
# <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychain"></a>Pourquoi ma build iOS échoue avec le message disant qu’aucun code iPhone valide pour signer les clés n’a été trouvé dans le trousseau ?

## <a name="cause-of-the-error"></a>Cause de l’erreur

Ce message d’erreur se produit lorsque le projet en question recherche des informations d’identification de signature de code valides, mais ne parvient pas à les trouver. La signature de code est requise pour les tests et les déploiements sur des appareils iOS physiques ; et les builds ad hoc & App Store.

### <a name="provisioning-devices"></a>Configuration des appareils

Si vous n’avez pas déjà approvisionné un appareil iOS, le guide suivant vous guide tout au long de la procédure pas à pas : [Guide de provisionnement des appareils](~/ios/get-started/installation/device-provisioning/index.md)

## <a name="bug-when-using-ios-simulator"></a>Bogue lors de l’utilisation du simulateur iOS

> [!NOTE]
> Ce problème a été résolu dans les versions récentes de Xamarin pour Visual Studio. Toutefois, si le problème se produit sur la version la plus récente du logiciel, envoyez un [nouveau bogue](~/cross-platform/troubleshooting/questions/howto-file-bug.md) à vos informations de contrôle de version et à la sortie complète du journal de génération.

Il y avait un bogue dans Xamarin. Visual Studio 3,11 qui a provoqué l’ajout du projet iOS dans un modèle Xamarin. Forms pour ajouter les builds. plist à la Codesign. plist au simulateur ; blocage efficace des tests à l’aide du simulateur.

### <a name="how-to-fix"></a>Procédure de résolution

Vous pouvez contourner le problème en supprimant l' `<CodesignEntitlements>` indicateur des versions Debug dans le fichier. csproj. Vous pouvez procéder comme suit :

> [!WARNING]
> Les erreurs dans les fichiers. csproj peuvent rompre votre projet. il est donc judicieux de sauvegarder vos fichiers avant de tenter cette opération.

1. Cliquez avec le bouton droit sur le projet iOS dans le volet solution, puis sélectionnez **décharger le projet**
2. Cliquez à nouveau avec le bouton droit sur le projet et sélectionnez **modifier [ProjectName]. csproj**
3. Localisez les éléments PropertyGroup de débogage, ils doivent commencer par des indicateurs qui ressemblent à ceci :
   - Debug`<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">`
   - 3/05`<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhoneSimulator' ">`
4. Dans chacune des builds qui utilisent le simulateur, supprimez ou commentez la propriété suivante :`<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. Rechargez le projet et vous devez être en mesure de le déployer dans le simulateur.

### <a name="next-steps"></a>Étapes suivantes
Pour obtenir de l’aide, pour nous contacter ou, si le problème persiste même après l’utilisation des informations ci-dessus, consultez [les options de support disponibles pour Xamarin ?](~/cross-platform/troubleshooting/support-options.md) pour plus d’informations sur les options de contact, les suggestions et la façon de signaler un nouveau bogue si nécessaire. .
