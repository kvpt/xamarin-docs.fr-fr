---
title: Utilisation de mtouch pour grouper des applications Xamarin.iOS
description: Ce document décrit mtouch, un outil qui facilite la plupart des étapes nécessaires pour convertir une application Xamarin.iOS en bundle, pour la lancer dans le simulateur et la déployer sur un appareil physique.
ms.prod: xamarin
ms.assetid: BCA491DA-E4C1-8689-3EC9-E4C72495A798
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2017
ms.openlocfilehash: 154cb6004bad5a12c98a22fa7522a9cc7a7c757a
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84567474"
---
# <a name="using-mtouch-to-bundle-xamarinios-apps"></a>Utilisation de mtouch pour grouper des applications Xamarin.iOS

Les applications iPhone sont livrées en tant qu’offres groupées d’applications. Il s’agit de répertoires avec l’extension `.app` qui contiennent votre code, données, fichiers de configuration et un manifeste utilisé par l’iPhone pour en savoir plus sur votre application.

Le processus de transformation d’un fichier exécutable .NET dans une application est principalement piloté par la commande `mtouch`, un outil qui intègre plusieurs étapes requises pour activer l’application dans une offre groupée. Cet outil est également utilisé pour lancer votre application dans le simulateur et déployer le logiciel sur un appareil de tactiles iPhone ou iPod Touch réel.

## <a name="detailed-instructions"></a>Instructions détaillées

Vérifiez la page de votre manuel [mtouch(1)](http://docs.go-mono.com/?link=man%3amtouch(1)) avec toutes les utilisations possibles de l’outil mtouch.

## <a name="installation"></a>Installation

Sur un Mac, `mtouch` est fourni avec Xamarin.iOS. Cet outil se trouve dans le répertoire suivant :

**/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin**

Pour rendre `mtouch` pratique à utiliser, ajoutez son répertoire parent à la variable d’environnement `PATH` de votre système.  

Par exemple, pour ce faire dans Bash, ajoutez la ligne suivante à la fin de votre fichier **~/.bash_profile** :

```bash
export PATH=$PATH:/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin
```

> [!WARNING]
> Pour utiliser `mtouch`, ne comptez pas sur l’existence de **/Developer/MonoTouch/usr/bin**, un lien symbolique qui pointe vers **/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin**. Ce lien symbolique existe uniquement pour maintenir la compatibilité avec les anciennes mises en production MonoTouch qui n’étaient pas installées dans **/Library/Frameworks/... ** et il peut disparaître dans une mise en production ultérieure.

## <a name="building"></a>Génération

La commande `mtouch` peut compiler votre code de trois façons différentes :

- compilation pour le test du simulateur
- compilation pour le déploiement de l’appareil
- déploiement de votre fichier exécutable sur l’appareil.

### <a name="building-for-the-simulator"></a>Compilation pour le simulateur

Lors du démarrage, le scénario le plus fréquemment utilisé sera d’essayer l’application dans le simulateur, afin d’utiliser `mtouch -sim` pour compiler le code dans un package du simulateur. Cette opération s’effectue comme suit :

```bash
$ mtouch -sim Hello.app hello.exe
```

### <a name="building-for-the-device"></a>Compilation pour l’appareil

Pour générer des logiciels pour l’appareil, générez votre application à l’aide de l’option `mtouch -dev`, puis fournissez le nom du certificat utilisé pour vous connecter à votre application. L’exemple suivant montre comment l’application est générée pour l’appareil :

```bash
$ mtouch -dev -c "iPhone Developer: Miguel de Icaza" foo.exe
```

Dans ce cas particulier, nous utilisons le certificat « Développeur iPhone : Miguel de Icaza » pour nous connecter à l’application. Cette étape est très importante, sinon l’unité physique refuse de charger l’application.

 <a name="Running_your_Application"></a>

## <a name="running-your-application"></a>Exécution de votre application

### <a name="launching-on-the-simulator"></a>Lancement sur le simulateur

Le lancement sur le simulateur est très simple une fois que vous disposez d’une offre groupée d’applications :

```bash
$ mtouch --sdkroot /Applications/Xcode.app -launchsim Hello.app 
```

Si l’indicateur `--sdkroot` n’est pas défini, il s’affiche par défaut sur xcode-sélectionner le chemin d’accès et devient l’avertissement suivant :

> par exemple : avertissement MT0061 : aucun Xcode.app spécifié (utilisation de --sdkroot), à l’aide du système Xcode comme indiqué par ''xcode-select --print-path': /Applications/Xcode.app/Contents/Developer 

La ligne de commande ci-dessus génère une sortie comme suit :

```bash
Launching application
Application launched
PID: 98460
Press enter to terminate the application
```

Il est fortement recommandé de conserver également un journal des fichiers de sortie et d’erreur standard pour faciliter le débogage. La sortie de `Console.WriteLine` va à `stdout` et la sortie depuis `Console.Error.WriteLine` et tout autre message d’erreur runtime va à `stderr`.

Pour ce faire, utilisez les indicateurs `--stdout` et `--stderr` :

```bash
../../tools/mtouch/mtouch --launchsim=Hello.app --stdout=output --stderr=error
```

Si votre application échoue, vous pouvez voir la sortie et l’erreur pour diagnostiquer le problème.

### <a name="deploying-to-a-device"></a>Déploiement sur un appareil

Pour déployer sur votre appareil, vous devez configurer votre appareil comme décrit dans le document [Gestion des appareils](https://developer.apple.com/library/ios/#documentation/Xcode/Conceptual/ios_development_workflow/00-About_the_iOS_Application_Development_Workflow/introduction.html) d’Apple. Une fois votre appareil correctement configuré, vous pouvez utiliser la commande mtouch pour déployer un fichier « .app » compilé dans votre appareil. Pour cela, utilisez la commande :

```bash
$ mtouch —sdkroot /Applications/Xcode.app -installdev=MyApp.app
```

Si l’indicateur `--sdkroot` n’est pas défini, il s’affiche par défaut sur xcode-sélectionner le chemin d’accès et devient l’avertissement suivant :

> par exemple : avertissement MT0061 : aucun Xcode.app spécifié (utilisation de --sdkroot), à l’aide du système Xcode comme indiqué par ''xcode-select --print-path': /Applications/Xcode.app/Contents/Developer 

Ces étapes sont généralement effectuées par Visual Studio pour Mac.

## <a name="reference"></a>Informations de référence

Consultez la page du manuel [mtouch(1)](http://docs.go-mono.com/?link=man%3amtouch(1)) pour plus d’informations sur les autres options de ligne de commande.

