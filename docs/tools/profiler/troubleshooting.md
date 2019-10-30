---
title: Résolution des problèmes de Xamarin Profiler
description: Ce document fournit des informations sur la résolution des problèmes liés au Xamarin Profiler. Il décrit les problèmes liés à la journalisation et aux diagnostics, à l’IDE et à d’autres rubriques.
ms.prod: xamarin
ms.assetid: 0060E9D1-C003-4E4C-ADE8-B406978FE891
author: davidortinau
ms.author: daortin
ms.date: 10/27/2017
ms.openlocfilehash: 915f7df80e3ae29ab3c598ea95fabbc054e916dd
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019211"
---
# <a name="xamarin-profiler-troubleshooting"></a>Résolution des problèmes de Xamarin Profiler

## <a name="logging-and-diagnostics"></a>Journalisation et diagnostics

L’équipe Xamarin peut faciliter le suivi des problèmes si vous nous fournissez des informations, notamment :

- Capture vidéo du problème, du blocage ou de l’échec, et de votre flux de travail.
- Journaux des sorties (voir ci-dessous).
- **. MLPD** généré pour la session de profilage (voir ci-dessous).

### <a name="getting-log-outputs"></a>Obtention des sorties de journal

Les journaux Mac sont enregistrés dans `~/Library/Logs/Xamarin.Profiler/Profiler.<date>.log`.

Sur Windows, ceux-ci sont enregistrés dans `%appdata%Local//Xamarin/Log/Xamarin.Profiler/Profiler.<date>.log` veuillez inclure le dernier journal chaque fois que vous soumettez un problème.

Nous ajoutons de la journalisation au fur et à mesure. cette sortie devrait donc croître et devenir plus utile dans le temps.

<a name="gen_mlpd" />

### <a name="generating-mlpd-files"></a>Génération de fichiers. MLPD

Un fichier **. MLPD** est la sortie compressée du profileur de Runtime mono. L’interface utilisateur graphique Xamarin Profiler lit les données à partir d’un **. MLPD** et les affiche pour l’utilisateur. les fichiers **. MLPD** sont des outils de débogage utiles pour Xamarin, car ils aident nos ingénieurs à diagnostiquer les problèmes que le profileur peut avoir avec vos données.

Le **. MLPD** pour la session active est automatiquement enregistré dans le répertoire `/tmp` de votre Mac et peut être identifié par l’horodateur. Si vous activez la journalisation, la première sortie correspond au chemin d’accès au fichier **. MLPD** . Le fichier **. MLPD** est normalement enregistré dans le répertoire à partir de ~/var/Folders...

Vous pouvez également enregistrer le fichier **. MLPD** pour une session active en choisissant **fichier > Enregistrer sous...** dans le menu du profileur :

**Visual Studio pour Mac**:

![](troubleshooting-images/image17.png "Saving .mlpd file in Visual Studio for Mac")

**Visual Studio**:

![](troubleshooting-images/image17-vs.png "Saving .mlpd file in Visual Studio")

Il est important de noter que **. MLPD** contient un grand nombre d’informations et que la taille du fichier est importante.

## <a name="troubleshooting"></a>Résolution des problèmes

La liste ci-dessous présente les pièges courants, les solutions de contournement et les trucs et astuces pour l’utilisation du profileur.

> [!NOTE]
> Vous devez être abonné à Visual Studio **Enterprise** pour déverrouiller cette fonctionnalité dans Visual Studio Enterprise sur Windows ou Visual Studio pour Mac.

#### <a name="i-cant-see-the-ios-profiler-option-or-it-is-greyed-out-visual-studio-and-visual-studio-for-mac"></a>Je ne vois pas l’option du profileur iOS, ou elle est grisée [Visual Studio et Visual Studio pour Mac]

Vérifiez les paramètres suivants pour résoudre ce qui suit :

- Vérifiez que vous utilisez la configuration Debug
- Vérifiez que vous utilisez le garbage collector SGen.
- Assurez-vous que la plateforme est [prise en charge](~/tools/profiler/index.md#Profiler_Support).
- Vérifiez que vous disposez de la licence appropriée.
- Vérifiez que vous êtes connecté et correctement authentifié.
- (Visual Studio) Vous devez utiliser [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/enterprise/) et disposer d’une licence d’entreprise valide.

#### <a name="i-get-an-error-when-i-try-to-launch-the-profiler"></a>J’obtiens une erreur lorsque j’essaie de lancer le profileur

Si vous rencontrez ce message d’erreur lors de l’utilisation du profileur dans Visual Studio :

![](troubleshooting-images/error.png "Error box when using the profiler in Visual Studio")

En règle générale, il est impossible de lancer le simulateur/émulateur. Essayez d’exécuter l’application normalement, corrigez les problèmes qu’elle fournit, puis réessayez d’utiliser le profileur.

#### <a name="to-watch-a-specific-thread"></a>Pour surveiller un thread spécifique

Si vous avez un thread que vous souhaitez surveiller spécifiquement, il est idéal de nommer le thread au tout début de sa création pour obtenir `ThreadName` au lieu de `0x0`. Par exemple, pour définir le nom du thread comme `UI`, vous pouvez utiliser le code suivant :

```csharp
RunOnUiThread (() => {
  Thread.CurrentThread.Name  = "UI";
});
```

## <a name="related-links"></a>Liens associés

- [Procédure pas à pas : utilisation de l’Xamarin Profiler](~/tools/profiler/index.md)
- [Meilleures pratiques en matière de performances et de mémoire](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Notes de publication](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/profiler/preview/index.md)
