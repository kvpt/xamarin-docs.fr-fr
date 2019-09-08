---
title: Erreur de concepteur iOS avec RegisterServicePort
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 929A0080-B126-4744-BF88-A4A1EFBB6CC2
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 815eb638b3e394dcfe4b60d716820c610b01f8a1
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769322"
---
# <a name="ios-designer-error-with-registerserviceport"></a>Erreur de concepteur iOS avec RegisterServicePort

## <a name="sample-error"></a>Exemple d’erreur
> System. AggregateException : Une ou plusieurs erreurs se sont produites---> System. SystemException : RegisterServicePort (com. xamarin. MTHosting. 2a0b1, com. Apple. PowerManagement. Control) : Le noyau a renvoyé :-308 (-308) : serveur (IPC/MIG) mort

## <a name="explanation"></a>Explication
Les erreurs `RegisterServicePort` avec et les messages d’erreur similaires comme ci-dessus sont généralement un problème avec les logiciels espions et les logiciels malveillants sur l’ordinateur. Pour plus d’informations, consultez le [commentaire sur ce rapport de bogue](https://bugzilla.xamarin.com/show_bug.cgi?id=21907#c4) , ainsi que le lien vers la [discussion du Forum Apple](https://discussions.apple.com/thread/5596008) sur la suppression d’une infection possible. 

Pour vous aider à diagnostiquer le problème, ouvrez la **console** d’application MacOS et supprimez chaque fichier dans la section [http://screencast.com/t/y9i3NKcuMy](http://screencast.com/t/y9i3NKcuMy) **rapports de diagnostic utilisateur** . Puis, démarrez Visual Studio pour Mac et essayez d’utiliser le concepteur. Si de nouveaux fichiers journaux apparaissent dans cette section après l’échec de l’initialisation du concepteur, enregistrez-les pour analyse.  

Notez que la chose la plus importante à vérifier est ce fichier : 
> /usr/lib/libimckit.dylib

Quels que soient les résultats ci-dessus, si ce fichier existe, le problème de logiciels espions/logiciels malveillants susmentionnés est présent sur votre ordinateur.  

Le lien suivant présente les étapes permettant de supprimer ce logiciel espion/programme malveillant :[http://www.thesafemac.com/arg-genieo/](http://www.thesafemac.com/arg-genieo/)  
