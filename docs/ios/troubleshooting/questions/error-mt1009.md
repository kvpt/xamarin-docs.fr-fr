---
title: 'Erreur MT1009 : impossible de copier l’assembly'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F9FEDFF5-C84C-42B4-8F25-E34846E7315A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: 8973093d851cf2c38fe57bada0e9d01e9664b15b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70293007"
---
# <a name="error-mt1009-could-not-copy-the-assembly"></a>Erreur MT1009 : impossible de copier l’assembly

> [!IMPORTANT]
> Ce problème a été résolu dans les versions récentes de Xamarin. iOS. Toutefois, si le problème se produit sur la version la plus récente du logiciel, envoyez un [nouveau bogue](~/cross-platform/troubleshooting/questions/howto-file-bug.md) à vos informations de contrôle de version et à la sortie complète du journal de génération.

Comme décrit dans nos [notes de publication](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_7/xamarin.ios_7.2/index.md), il s’agit d’un problème connu dans Xamarin. iOS 7.2.6. Ce problème est dû aux autorisations de fichier nécessitant des privilèges plus élevés quand Xamarin. iOS est installé avec un compte d’utilisateur différent, puis le compte principal du développeur.

Pour contourner le problème, ouvrez l’application Terminal Server sur la station de travail Mac et exécutez la commande suivante :

`sudo chmod 0644 /Developer/MonoTouch/usr/lib/mono/2.1/monotouch.dll.mdb`
