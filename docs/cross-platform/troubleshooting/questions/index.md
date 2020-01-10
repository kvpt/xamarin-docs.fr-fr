---
title: Forum aux questions (FAQ)
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C7E6E54D-3957-407D-BB87-22B095148C6B
author: davidortinau
ms.author: daortin
ms.date: 05/08/2018
ms.openlocfilehash: 30827292e59c70395fce17bb520f48b2f19c7d1e
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728174"
---
# <a name="general-frequently-asked-questions"></a>Forum aux questions (FAQ)

## <a name="portable-class-libraries"></a>Bibliothèques de classes portables

### <a name="how-can-i-view-what-libraries-are-supported-in-a-pclpcl-support-librariesmd"></a>[Comment afficher les bibliothèques prises en charge dans une bibliothèque de classes portable ?](pcl-support-libraries.md)
Ce guide répertorie les ressources et les méthodes permettant de déterminer si votre bibliothèque existante est prise en charge par les différentes plateformes cibles PCL ou si elle peut être convertie en un profil PCL.

### <a name="pcl-reflection-apipcl-reflectionmd"></a>[API de réflexion de bibliothèque de classes portable](pcl-reflection.md)
Microsoft a développé une nouvelle API de réflexion à utiliser dans les bibliothèques de classes portables. Si vous avez un code de réflexion existant que vous souhaitez déplacer vers une bibliothèque de classes portable, il peut ne pas fonctionner.

### <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-packagepcl-case-studymd"></a>[Étude de cas de bibliothèque de classes portable : Comment résoudre des problèmes liés à System.Diagnostics.Tracing pour le package NuGet Microsoft TPL Dataflow ?](pcl-case-study.md)
Xamarin. iOS et Xamarin. Android n’implémentent pas 100% de chaque profil PCL qu’ils autorisent en tant que références. Pour des raisons pratiques dans Visual Studio pour Mac, Visual Studio et le gestionnaire de package NuGet, les projets Xamarin autorisent l’utilisation de plusieurs profils qui n’ont que des implémentations incomplètes. Par exemple, ni Xamarin. iOS ni Xamarin. Android n’incluent actuellement une implémentation complète des types dans l’espace de noms PCL `System.Diagnostics.Tracing`. Vous pouvez contourner ce contournement en basculant le projet d’application pour référencer la version portable-Net45 + WIN8 + wp8 + wpa81 de la bibliothèque de flux de données TPL.

## <a name="nuget-packages--xamarin-components"></a>Packages NuGet & composants Xamarin
### <a name="how-can-i-update-nugetnuget-updatemd"></a>[Comment mettre à jour NuGet ?](nuget-update.md)
Les mises à jour, extensions et compléments NuGet se trouvent sous l’onglet **mises à jour** du **Gestionnaire de package NuGet**. Navigation détaillée pour rechercher les mises à jour dans Visual Studio pour Mac & Visual Studio est disponible dans ce guide.

### <a name="how-do-i-downgrade-a-nuget-packagenuget-package-downgrademd"></a>[Comment mettre à niveau un package NuGet ?](nuget-package-downgrade.md)
Visual Studio pour Mac & Visual Studio possèdent des fonctionnalités permettant de sélectionner des versions antérieures des packages et de les installer automatiquement. semblable au fonctionnement de la mise à jour des packages.

### <a name="missing-packages-error-after-updating-nuget-packagesnuget-packages-missingmd"></a>[Erreur de packages manquants après la mise à jour des packages NuGet](nuget-packages-missing.md)
Ce problème a été signalé principalement dans les exemples de solutions d’application Xamarin. Forms, mais le potentiel de ce problème peut se produire sur n’importe quel projet qui utilise des packages NuGet.

### <a name="unifying-google-play-services-components-and-nugetgps-components-nugetmd"></a>[Unification des composants des services Google Play et de NuGet](gps-components-nuget.md)
Il s’agissait de plusieurs composants Google Play Services et de packages NuGet, mais pour faciliter la tâche des développeurs, nous avons maintenant unifié nos composants et les packages NuGet en deux. Dans presque tous les cas, Google Play Services doit être utilisé. La seule raison d’utiliser le package (Froyo) est si vous ciblez activement Froyo.

### <a name="where-are-the-components-stored-on-my-machinecomponent-storagemd"></a>[Où sont stockés les composants sur ma machine ?](component-storage.md)
Chaque fois que vous installez un composant Xamarin dans un projet d’application, il est placé aux deux emplacements listés dans ce guide.

## <a name="troubleshooting"></a>Résolution des problèmes
### <a name="where-can-i-find-my-version-information-and-logsversion-logsmd"></a>[Où trouver les informations sur ma version et les journaux ?](version-logs.md)
Ce guide explique comment trouver la plupart des informations de diagnostic qui peuvent être utilisées pour résoudre les problèmes liés à Xamarin.

### <a name="when-and-how-should-i-file-a-bug-reporthowto-file-bugmd"></a>[Quand et comment dois-je classer un rapport de bogues ?](howto-file-bug.md)
Ce guide fournit des conseils pour le classement des rapports de bogues de haute qualité, afin que nos ingénieurs puissent déterminer plus efficacement la cause (et les éventuelles corrections éventuelles) d’un problème.

### <a name="why-isnt-jenkins-supported-by-xamarinxamarin-jenkinsmd"></a>[Pourquoi Jenkins n’est-il pas pris en charge par Xamarin ?](xamarin-jenkins.md)
Jenkins est une suite d’intégration open source. en raison de ce nombre de problèmes directement provoqués par le Jenkins *lui-même* doit être classé comme un problème sur lequel vous avez obtenu le code ; tels que le [Jenkins principal référentiel](https://github.com/jenkinsci/jenkins)ou référentiel pour [Jenkins. app](https://github.com/stisti/jenkins-app).

### <a name="what-project-settings-are-required-for-the-debuggerdebugger-settingsmd"></a>[Quels sont les paramètres de projet requis pour le débogueur ?](debugger-settings.md)
Pour que le débogueur fonctionne comme prévu (points d’arrêt atteints, afficher les journaux de débogage, etc.), l’affichage des informations de débogage et l’instrumentation du développeur doivent être activés. Ce guide explique en détail comment rechercher et activer ces paramètres.
