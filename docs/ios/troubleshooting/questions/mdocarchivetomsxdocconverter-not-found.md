---
title: MDocArchiveToMsxDocConverter.exe introuvable rver.BaseCommand.OnRequest
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5AC6AC4-0E7C-4746-A7CF-872F0E75AFF4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: af90baf6d7b94973a76bcf391f2cb28c45340602
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528152"
---
# <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequest"></a>MDocArchiveToMsxDocConverter.exe introuvable rver.BaseCommand.OnRequest

> [!IMPORTANT]
> Ce problème a été résolu dans les versions récentes de Xamarin. Toutefois, si le problème se produit sur la version la plus récente du logiciel, envoyez un [nouveau bogue](~/cross-platform/troubleshooting/questions/howto-file-bug.md) à vos informations de contrôle de version et à la sortie complète du journal de génération.


## <a name="error-message"></a>Message d’erreur

Cette erreur peut s’afficher dans le *Journal du serveur Mac* dans Visual Studio:

```
Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found
 rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context, System.Object commandRequestState) [0x00000] in <filename unknown>:0
  at Mtb.Server.Listener.OnRequest (System.Object state) [0x00000] in <filename unknown>:0
```

Il y a 2 problèmes distincts dans ce message:

1. `Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found`

    Cette erreur est sans conséquence, mais elle est également trompeuse. Elle [sera supprimée](https://bugzilla.xamarin.com/show_bug.cgi?id=21667) dans une version ultérieure.

2. `rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context …`

    Cette erreur est le vrai problème. Malheureusement, en raison d’une [limitation](https://bugzilla.xamarin.com/show_bug.cgi?id=22080) , la trace de la pile d’exception est incomplète. Si vous remarquez une trace de pile incomplète comme celle-ci dans le journal du `~/Library/Logs/Xamarin/MonoTouchVS/mtbserver.log` serveur Mac, vous pouvez vérifier le fichier sur l’hôte de build Mac pour trouver la trace de la pile complète.
