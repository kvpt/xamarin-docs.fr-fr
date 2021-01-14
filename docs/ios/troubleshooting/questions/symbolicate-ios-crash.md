---
title: Où trouver le fichier .dSYM pour générer les symboles des journaux de plantage iOS ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/09/2018
ms.openlocfilehash: 480ec0d4a815d62422e31a4110af52ca1628d1f7
ms.sourcegitcommit: d4d293174a8324ce82b8f961ae6eadce294cafd7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98194898"
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>Où trouver le fichier .dSYM pour générer les symboles des journaux de plantage iOS ?

Lors de la génération d’une application iOS avec Visual Studio pour Mac ou Visual Studio 2017, le fichier. dSYM nécessaire pour désigner les rapports d’incidents est placé dans la même hiérarchie de répertoires que le fichier projet de votre application (. csproj). L’emplacement exact dépend des paramètres de génération de votre projet :

- Si vous avez activé les builds spécifiques à l’appareil, le. dSYM se trouve dans le répertoire suivant :

    **&lt;répertoire &gt; du projet &lt; /bin/ &gt; / &lt; configuration &gt; de la plateforme/Device-builds/ &lt; de l’appareil &gt; - &lt; -version&gt;/**

    Par exemple :
  
    **TestApp/bin/iPhone/Release/Device-builds/iPhone 8.4-11.3.1/**

- Si vous n’avez pas activé les builds spécifiques à l’appareil, le. dSYM se trouve dans le répertoire suivant :

    **&lt;configuration de &gt; la &lt; plateforme &gt; /bin/ / &lt; Directory Project&gt;/**

    Par exemple :

    **TestApp/bin/iPhone/mise en version/**

> [!NOTE]
> Dans le cadre du processus de génération, Visual Studio 2017 copie le fichier. dSYM à partir de l’hôte de build Mac vers Windows. Si vous ne voyez pas de fichier. dSYM sur Windows, assurez-vous que vous avez configuré les paramètres de build de votre application pour [créer un fichier. Loi](~/ios/deploy-test/app-distribution/ipa-support.md).

## <a name="see-also"></a>Voir aussi

- [Fichiers d’incident Symbolicating iOS (Xamarin. iOS)](https://www.jmillerdev.com/symbolicating-ios-crash-files-xamarin-ios/)
- [Démystification des journaux de panne des applications iOS](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)
