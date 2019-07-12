---
title: Profiler Xamarin résolution des problèmes
description: Ce document fournit des informations de résolution des problèmes liées à la Xamarin Profiler. Il décrit les problèmes liés à la journalisation et diagnostics, l’IDE et d’autres rubriques.
ms.prod: xamarin
ms.assetid: 0060E9D1-C003-4E4C-ADE8-B406978FE891
author: lobrien
ms.author: laobri
ms.date: 10/27/2017
ms.openlocfilehash: e4a4376291ff56433c8cd9785989af2983a80c1c
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832150"
---
# <a name="xamarin-profiler-troubleshooting"></a>Profiler Xamarin résolution des problèmes

## <a name="logging-and-diagnostics"></a>Journalisation et Diagnostics

L’équipe Xamarin peut aider à effectuer le suivi des problèmes si vous nous communiquez des informations, y compris :

- Une capture vidéo du problème, incident, ou défaillance et de votre flux de travail mènent à ce dernier.
- Journal des sorties (voir ci-dessous).
- Le **.mlpd** générées pour la session de profilage (voir ci-dessous).

### <a name="getting-log-outputs"></a>Obtention des sorties de journal

Sur Mac, les journaux sont enregistrés dans `~/Library/Logs/Xamarin.Profiler/Profiler.<date>.log`.

Sur Windows, celles-ci sont enregistrées à `%appdata%Local//Xamarin/Log/Xamarin.Profiler/Profiler.<date>.log` Veuillez inclure le dernier journal chaque fois que vous soumettez un problème.

Nous ajoutons une journalisation plus mesure que nous, cette sortie doit donc développer et deviennent plus utile au fil du temps.

<a name="gen_mlpd" />

### <a name="generating-mlpd-files"></a>Générer des fichiers .mlpd

Un **.mlpd** fichier est la sortie compressée du profileur runtime mono. L’interface utilisateur graphique de Xamarin Profiler lit les données à partir d’un **.mlpd** et l’affiche pour l’utilisateur. **.mlpd** les fichiers sont utiles, outils de débogage pour Xamarin, car elles permettent à nos ingénieurs diagnostiquer les problèmes le Profiler peut se poser avec vos données.

Le **.mlpd** pour la session active est automatiquement enregistrée dans votre Mac `/tmp` directory et peuvent être identifiés par l’horodatage. Si vous activez la journalisation, la première sortie sera le chemin d’accès à la **.mlpd** fichier. Le **.mlpd** fichier sera enregistré normalement dans le répertoire ~/var/dossiers de démarrage...

Le **.mlpd** pour une session en cours peut également être enregistrée en choisissant **fichier > Enregistrer sous...** dans le menu de la Profiler :

**Visual Studio pour Mac**:

![](troubleshooting-images/image17.png "Enregistrement de fichier .mlpd dans Visual Studio pour Mac")

**Visual Studio**:

![](troubleshooting-images/image17-vs.png "L’enregistrement de fichier de .mlpd dans Visual Studio")

Il est important de noter que **.mlpd** contiennent un grand nombre d’informations, et la taille du fichier sera volumineux.

## <a name="troubleshooting"></a>Résolution de problèmes

La liste ci-dessous affiche courantes pièges, solutions de contournement, des conseils et astuces pour utiliser le Profiler.

> [!NOTE]
> Vous devez être un Visual Studio **Enterprise** abonné à cette fonctionnalité dans soit Visual Studio Enterprise sur Windows ou de Visual Studio pour Mac.

#### <a name="i-cant-see-the-ios-profiler-option-or-it-is-greyed-out-visual-studio-and-visual-studio-for-mac"></a>Je ne vois pas l’option de profileur iOS, ou elle est grisée [Visual Studio et Visual Studio pour Mac]

Vérifiez les paramètres suivants pour résoudre ce problème :

- Vérifiez que vous utilisez la configuration Debug
- Vérifiez que vous utilisez le SGen Garbage collector.
- Vérifiez que la plateforme est [pris en charge](~/tools/profiler/index.md#Profiler_Support).
- Vérifiez que vous disposez de la licence appropriée.
- Assurez-vous que vous êtes connecté en et correctement authentifiés.
- Visual Studio] Vous devez utiliser [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/enterprise/) et disposer d’une licence d’entreprise valide.

#### <a name="i-get-an-error-when-i-try-to-launch-the-profiler"></a>J’obtiens une erreur lorsque j’essaie de lancer le Générateur de profils

Si vous rencontrez cette boîte d’erreur lorsque vous utilisez le profileur dans Visual Studio :

![](troubleshooting-images/error.png "Boîte d’erreur lorsque vous utilisez le profileur dans Visual Studio")

Normalement, il s’en raison de l’impossibilité de se lancer sur le simulateur / émulateur. Essayez et exécuter l’application normalement, corrigez les problèmes qu’il offre et essayez ensuite d’utiliser le Profiler à nouveau.

#### <a name="to-watch-a-specific-thread"></a>Pour visionner un thread spécifique

Si vous avez un thread que vous souhaitez regarder plus précisément, il serait idéal pour nommer le thread au tout début de sa création pour obtenir `ThreadName` au lieu de `0x0`. Par exemple, pour définir le nom de thread comme `UI`, vous pouvez utiliser le code suivant :

```csharp
RunOnUiThread (() => {
  Thread.CurrentThread.Name  = "UI";
});
```

## <a name="related-links"></a>Liens associés

- [Procédure pas à pas - à l’aide de la Xamarin Profiler](~/tools/profiler/index.md)
- [Meilleures pratiques de mémoire et de performances](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Notes de publication](https://developer.xamarin.com/releases/profiler/preview/)
