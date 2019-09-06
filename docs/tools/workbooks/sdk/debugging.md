---
title: Débogage d’intégrations
description: Ce document décrit comment déboguer les intégrations de Xamarin Workbooks, côté agent et côté client sur Windows et Mac.
ms.prod: xamarin
ms.assetid: 90143544-084D-49BF-B44D-7AF943668F6C
author: conceptdev
ms.author: crdun
ms.date: 06/19/2018
ms.openlocfilehash: fbb5673a70328ad6edde78af1b35d2801fe65ca8
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283930"
---
# <a name="debugging-integrations"></a>Débogage d’intégrations

## <a name="debugging-agent-side-integrations"></a>Débogage des intégrations côté agent

Le débogage des intégrations côté agent s’effectue mieux en utilisant les méthodes de journalisation fournies `Log` par la `Xamarin.Interactive.Logging`classe dans.

Sur macOS, les messages de journal s’affichent dans le menu de la visionneuse du journal (**fenêtre > Log Viewer**) et dans le journal du client. Sur Windows, les messages apparaissent uniquement dans le journal du client, car il n’y a aucune visionneuse du journal.

Le journal client se trouve aux emplacements suivants sur macOS et Windows :

- Macintosh`~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows : `%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

Une chose à savoir, c’est que lors du chargement des intégrations via `#r` le mécanisme habituel pendant le développement, l’assembly d’intégration est récupéré en tant que _dépendance_ du classeur et empaqueté avec lui si aucun chemin d’accès absolu n’est utilisé. Cela peut entraîner des modifications qui ne se propagent pas, comme si la reconstruction de l’intégration ne faisait rien.

## <a name="debugging-client-side-integrations"></a>Débogage des intégrations côté client

Comme les intégrations côté client sont écrites en JavaScript et chargées dans notre zone de navigateur Web (consultez la documentation sur l' [architecture](~/tools/workbooks/sdk/architecture.md) ), la meilleure façon de les déboguer consiste à utiliser les outils de développement WebKit sur Mac ou à utiliser le sélecteur F12 sur Windows.

Les deux ensembles d’outils vous permettent d’afficher la source JavaScript/de machine à écrire, de définir des points d’arrêt, d’afficher la sortie de la console et d’inspecter et de modifier le DOM.

### <a name="mac"></a>Mac

Pour activer les outils de développement pour Xamarin Workbooks sur Mac, exécutez la commande suivante dans votre terminal :

```shell
defaults write com.xamarin.Workbooks WebKitDeveloperExtras -bool true
```

puis redémarrez Xamarin Workbooks. Une fois cette opération effectuée, l' **élément inspecter** doit s’afficher dans le menu contextuel du clic droit, et un nouveau volet **développeur** sera disponible dans les préférences des classeurs. Cette option vous permet de choisir si vous souhaitez que les outils de développement soient ouverts au démarrage :

[![Volet du développeur](debugging-images/developer-pane-small.png)](debugging-images/developer-pane.png#lightbox)

Cette préférence est également Restart (redémarrer uniquement). vous devrez redémarrer le client des classeurs afin qu’il prenne effet sur les nouveaux classeurs. L’activation des outils de développement via le menu contextuel ou les préférences affiche l’interface utilisateur de Safari familière :

[![Outils de développement Safari](debugging-images/mac-dev-tools.png)](debugging-images/mac-dev-tools.png#lightbox)

Pour plus d’informations sur l’utilisation des outils de développement Safari, consultez la [documentation de WebKit Inspector][webkit-docs].

### <a name="windows"></a>Windows

Sur Windows, l’équipe IE fournit un outil connu sous le nom de « sélecteur F12 », qui est un débogueur distant pour les instances d’Internet Explorer incorporées. L’outil se trouve à l’emplacement suivant :

```shell
C:\Windows\System32\F12\F12Chooser.exe
```

Exécutez le sélecteur F12. vous devriez voir l’instance incorporée qui alimente la surface cliente des classeurs dans la liste. Choisissez-le et les outils de débogage F12 familiers d’Internet Explorer s’affichent, attachés au client :

[![Outils F12](debugging-images/windows-dev-tools.png)](debugging-images/windows-dev-tools.png#lightbox)

[webkit-docs]: https://trac.webkit.org/wiki/WebInspector
