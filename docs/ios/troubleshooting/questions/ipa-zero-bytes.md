---
title: Le fichier IPA contient 0 octet
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 376BBA27-8694-4E63-9976-BF60349D42D8
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: 3ef916dbd277544309f1803f923cde302494e4f1
ms.sourcegitcommit: 61a35d0643eb3bf5adb8f8831da54771d8dde626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033507"
---
# <a name="ipa-file-is-0-bytes"></a>Le fichier IPA contient 0 octet

> [!IMPORTANT]
> Ce problème a été résolu dans les versions récentes de Xamarin. Toutefois, si le problème se produit sur la version la plus récente du logiciel, envoyez un [nouveau bogue](~/cross-platform/troubleshooting/questions/howto-file-bug.md) à vos informations de contrôle de version et à la sortie complète du journal de génération.

Certains problèmes connus dans les versions précédentes de Xamarin pouvaient entraîner la présence de 0 octets dans le fichier de la Loi d’accès aux fichiers sur Windows. 

### <a name="fixed-in-xamarin-for-visual-studio-311584"></a>Correction dans Xamarin pour Visual Studio 3.11.584 

- [Bogue 24416-la génération de la configuration « ad hoc » à partir de la ligne de commande n’effectue pas la copie du fichier de la loi Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24416)
- [Bogue 24417-modification de « propriétés du projet-> Options de la loi iOS-> nom du package » empêche la recopier la Loi sur Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24417)
- [Bogue 29822-[XVS. iOS 3,11] la définition d’un numéro de « build » différent de celui de « version » entraîne la non-copie de la Loi sur Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=29822)

### <a name="fixed-in-xamarin-for-visual-studio-410496"></a>Correction dans Xamarin pour Visual Studio 4.1.0.496

- [Bogue 27989-échec de l’affichage du fichier de la Loi sur le serveur de builds si le nom de l’assembly ne correspond pas au nom du projet](https://bugzilla.xamarin.com/show_bug.cgi?id=27989)
