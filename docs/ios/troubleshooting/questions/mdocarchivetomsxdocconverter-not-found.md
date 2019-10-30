---
title: MDocArchiveToMsxDocConverter.exe introuvable rver.BaseCommand.OnRequest
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5AC6AC4-0E7C-4746-A7CF-872F0E75AFF4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 3d21dfdbf6c9be00fe6851bb288268faccd74308
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030968"
---
# <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequest"></a>MDocArchiveToMsxDocConverter.exe introuvable rver.BaseCommand.OnRequest

> [!IMPORTANT]
> Ce problème a été résolu dans les versions récentes de Xamarin. Toutefois, si le problème se produit sur la version la plus récente du logiciel, envoyez un [nouveau bogue](~/cross-platform/troubleshooting/questions/howto-file-bug.md) à vos informations de contrôle de version et à la sortie complète du journal de génération.

## <a name="error-message"></a>Message d'erreur

Cette erreur peut s’afficher dans le *Journal du serveur Mac* dans Visual Studio :

```
Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found
 rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context, System.Object commandRequestState) [0x00000] in <filename unknown>:0
  at Mtb.Server.Listener.OnRequest (System.Object state) [0x00000] in <filename unknown>:0
```

Il y a 2 problèmes distincts dans ce message :

1. `Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found`

    Cette erreur est sans conséquence, mais elle est également trompeuse. Elle [sera supprimée](https://bugzilla.xamarin.com/show_bug.cgi?id=21667) dans une version ultérieure.

2. `rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context …`

    Cette erreur est le vrai problème. Malheureusement, en raison d’une [limitation](https://bugzilla.xamarin.com/show_bug.cgi?id=22080) , la trace de la pile d’exception est *incomplète*. Si vous remarquez une trace de pile incomplète comme celle-ci dans le journal du serveur Mac, vous pouvez vérifier le fichier `~/Library/Logs/Xamarin/MonoTouchVS/mtbserver.log` sur l’hôte de build Mac pour trouver la trace de la pile complète.
