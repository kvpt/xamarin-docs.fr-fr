---
title: Xamarin Profiler
description: Ce guide présente les principales fonctionnalités du profileur Xamarin. Il permet de consulter les profileurs, le profilage, et de savoir quand les utiliser à bon escient et dans un flux de travail standard pour le profilage d’applications de Xamarin.
ms.prod: xamarin
ms.assetid: 3247fcee-6acc-470d-ab87-c1c511d67363
author: lobrien
ms.author: laobri
ms.date: 06/03/2018
ms.openlocfilehash: d80363cd339d5d3177ae063df2a20d7938f59169
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832403"
---
# <a name="xamarin-profiler"></a>Xamarin Profiler

_Ce guide présente les principales fonctionnalités du profileur Xamarin. Il permet de consulter les profileurs, le profilage, et de savoir quand les utiliser à bon escient et dans un flux de travail standard pour le profilage d’applications de Xamarin._

Réussite de l’application dépend de l’expérience utilisateur final. En tant que développeur vous avez peut-être implémenté certaines fonctionnalités étonnantes vraiment dans votre application, mais si l’application est lente ou complète de pannes, l’utilisateur sera probablement en débarrasser.

Historiquement, Mono a présenté un profileur de ligne de commande puissant pour la collecte des informations sur les programmes en cours d’exécution dans le runtime Mono appelé le [Générateur de profils de journal Mono](https://www.mono-project.com/docs/debug+profile/profile/profiler/). Le Xamarin Profiler une interface graphique pour le Générateur de profils de journal Mono et prend en charge le profilage Android, iOS, tvOS et les applications Mac sur Mac et Android, iOS et tvOS des applications sur Windows.

Le Xamarin Profiler a un nombre d’instruments disponibles pour le profilage, Profiler de temps, les Cycles et les Allocations. Ce guide explore ce que ces instruments mesurent et comment ils analysent votre application et explique la signification des données présentées sur chaque écran.

Ce guide examine les scénarios de profilage courants et présente le profileur en tant qu’outil pour aider à analyser et optimiser des applications iOS et Android.

## <a name="download-and-install"></a>Téléchargez et installez

> [!NOTE]
> Vous devez être un [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/compare/) abonné à cette fonctionnalité dans soit Visual Studio Enterprise sur Windows ou de Visual Studio pour Mac sur un Mac.

Le Xamarin Profiler est une application autonome et est intégré à Visual Studio pour Mac et Visual Studio activer le profilage à partir de l’IDE.

Télécharger le package d’installation pour votre plateforme :

- [**macOS**](https://dl.xamarin.com/profiler/profiler-mac.pkg)
- [**Windows**](https://dl.xamarin.com/profiler/profiler-windows.msi)

Une fois téléchargé, lancez le programme d’installation pour ajouter le Profiler Xamarin à votre système.

## <a name="profilers-and-profiling"></a>Profileurs et profilage

Le profilage est une étape importante et souvent négligée en matière de développement d’applications. Le profilage est une forme d'**analyse dynamique de programme** - il analyse le programme alors qu’il est en cours d’exécution et en cours d’utilisation. Un profileur est un outil d’exploration de données qui collecte des informations sur la complexité temporelle, l’utilisation de méthodes particulières et la mémoire allouée. Un profileur permet d’explorer en profondeur et d'analyser ces mesures pour identifier les zones à problème dans le code.

Lorsque vous concevez et développez une application, il est important de ne pas optimiser prématurément ; Autrement dit, dépense de temps à développer votre code dans les domaines qui sera rarement accessible. Il s’agit de la puissance du profilage. Un profileur fournit insight dans les plus couramment utilisés parties de votre code base, et vous aide à localiser les parties où vous devez prendre les améliorations d’heure. Les développeurs doivent veiller à comprendre où la plupart du temps passé sur votre application, et comment la mémoire est utilisée par votre application.

Le profilage est utile dans tous les types de développement, mais il est surtout essentiel dans le développement mobile. Code non optimisé est beaucoup plus notable sur les plateformes mobiles que sur les ordinateurs de bureau, et la réussite de votre application dépend de magnifiques et optimisé le code qui s’exécute de manière efficace.

## <a name="xamarin-profiler"></a>Xamarin Profiler

Le Xamarin Profiler fournit aux développeurs un moyen pour profiler des applications à partir de Visual Studio pour Mac ou Visual Studio. Le profileur collecte et affiche des informations sur l’application, qui peut ensuite être utilisée par le développeur pour analyser le comportement d’une application. Il existe un certain nombre de différentes façons de profiler une application avec le Xamarin Profiler, à savoir le profilage de mémoire et échantillonnage statistique. Ceux-ci sont effectuées via les Allocations et les temps Profiler instrumente respectivement.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Actuellement, le Profiler Xamarin peuvent être utilisée pour tester des applications Xamarin.iOS, Xamarin.Android et Xamarin.Mac sur Mac (Via Visual Studio pour Mac). Le profileur est un processus distinct à partir de l’IDE, et par conséquent, en plus de la lancer à partir de Visual Studio pour Mac, il peut être utilisé comme une application autonome pour examiner .exe et `.mlpd` les fichiers qui ont été produites à partir de la [profileur mono journal](https://www.mono-project.com/docs/debug+profile/profile/profiler/).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Actuellement, le Profiler Xamarin peuvent être utilisée pour tester les applications Xamarin.Android sur Windows (par le biais de Visual Studio et Visual Studio pour Mac). Le profileur est un processus distinct à partir de l’IDE, et par conséquent, en plus de lancement à partir de Visual Studio, il peut être utilisé comme une application autonome pour examiner .exe et `.mlpd` les fichiers qui ont été produites à partir de la [Générateur de profils de journal mono](https://www.mono-project.com/docs/debug+profile/profile/profiler/) .

-----

<a name="Profiler_Support" />

## <a name="profiler-support"></a>Prise en charge de Profiler

Prise en charge pour le Xamarin Profiler est disponible sur les plateformes suivantes :

- Visual Studio pour Mac (macOS, avec une licence d’entreprise)
    - Android
        - Appareil et l’émulateur
    - iOS
        - Appareil et simulateur
    - tvOS (Instrument de temps n’est pas pris en charge)
        - Appareil et simulateur
    - Mac

- Visual Studio (uniquement **Enterprise** Version)
    - Android
        - Appareil et l’émulateur
    - iOS [expérimental]
        - Appareil et simulateur
    - tvOS
        - Appareil et simulateur

Notez que vous pouvez **uniquement** profil **déboguer** configurations.

## <a name="profiler-basics"></a>Principes fondamentaux de Profiler

Cette section présente les différentes parties de la Xamarin Profiler et voyages ses fonctionnalités.

### <a name="allow-profiling-in-your-app"></a>Autoriser le profilage dans votre application

Avant que vous pouvez profiler avec succès votre application, vous devez autoriser le profilage dans les Options de projet de l’application.

- iOS :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

  **Générer > débogage iOS > activer le profilage**

  ![](images/ios-options-mac.png "iOS boîte de dialogue Options dans Visual Studio pour Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  **Propriétés > Build iOS > activer le profilage**

  ![](images/ios-project-options-vs.png "iOS boîte de dialogue Options dans Visual Studio")

-----

- Android :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

  **Générer > débogage Android > Activer l’Instrumentation du développeur**

  ![Boîte de dialogue Options Android dans Visual Studio pour Mac](images/android-project-options.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  **Générer > débogage Android > Activer l’Instrumentation du développeur**

  ![Boîte de dialogue Options Android dans Visual Studio pour Mac](images/android-project-options-vs-sml.png)

-----

### <a name="launching-the-profiler"></a>Lancer le Profiler

Le profileur Xamarin peut être lancé à partir de votre IDE quand vous profilez votre application Android ou iOS. Le profileur peut aussi être lancé comme une application indépendante.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

#### <a name="launching-from-visual-studio-for-mac"></a>Lancement à partir de Visual Studio pour Mac

1. Tout d’abord, assurez-vous que vous disposez de votre application chargée dans Visual Studio pour Mac, sélectionnez la configuration de débogage (par défaut).
2. Accédez à **exécuter > Démarrer le profilage**dans Visual Studio pour Mac, ou **analyser > Xamarin Profiler** dans Visual Studio, pour ouvrir le Profiler, comme illustré dans le diagramme ci-dessous :

  ![](images/start-profiling-xs.png "Lancer le Profiler à partir de Visual Studio pour Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

#### <a name="launching-from-visual-studio"></a>Lancement à partir de Visual Studio

1.  Tout d’abord, assurez-vous que votre que votre application soit chargée dans Visual Studio, sélectionnez la configuration de débogage (par défaut), comme indiqué ci-dessus.
2.  Accédez à **analyser > Xamarin Profiler** dans Visual Studio, pour ouvrir le Profiler, comme illustré dans le diagramme ci-dessous :

![Lancer le Profiler à partir de Visual Studio](images/start-profiling-vs.png)

-----

Si les éléments de menu n’apparaissent pas, reportez-vous à la [guide de dépannage](~/tools/profiler/troubleshooting.md).

Cela lance le Profiler et démarre automatiquement le profilage de l’application.

Le Profiler peut être utilisé pour mesurer les performances et mémoire. Pour cela, via les Allocations et les temps Profiler instruments, nous allons examiner en détail dans la section suivante.

#### <a name="saving-and-loading-profiler-sessions"></a>Enregistrement et chargement de Sessions de Profiler

Pour enregistrer une session de profilage à tout moment, choisissez **fichier > Enregistrer sous...**  à partir de la barre de menus de Profiler. Cette opération enregistre le fichier dans _mlpd_ format, un format fortement compressé spécial pour le profilage des données.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Après que qu’il a été installé le Xamarin Profiler figurent dans votre dossier d’Applications comme illustré dans la capture d’écran ci-dessous :

![](images/applications.png "Ouvrez autonome Profiler à partir de Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Après que qu’il a été installé l’application Xamarin Profiler peut être trouvée dans votre répertoire de l’Application :

![](images/applications-vs.png "Ouvrez autonome Profiler à partir de Windows ")

-----

Vous pouvez charger *.mlpd* des fichiers dans le Profiler en ouvrant l’application autonome, en sélectionnant **choisir la cible** et du chargement du fichier.

Pour plus d’informations, consultez [génération de fichiers de .mlpd](~/tools/profiler/troubleshooting.md#gen_mlpd).

## <a name="profiler-features"></a>Fonctionnalités de Profiler

Le Xamarin Profiler est composé de cinq sections, comme illustré ci-dessous :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](images/profiler-mac-sml.png "Sections de Profiler dans Visual Studio pour Mac")](images/profiler-mac.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](images/profiler-vs.png "Sections de Profiler dans Visual Studio")](images/profiler-vs.png#lightbox)

-----

- **Barre d’outils** – située en haut du profileur, cela offre des options pour démarrer/arrêter le profilage, sélectionnez un processus cible, afficher la durée d’exécution de l’application et sélectionnez les affichages fractionnés qui composent l’application du profileur.
- **Instrumenter liste** – ce rapport répertorie tous les instruments chargés pour la session de profilage.
- **Tracer le graphique** – ces graphiques concernent horizontalement les instruments dans la liste des instruments. Un curseur (indiqué en dessous de Profiler de temps) peut être utilisé pour modifier l’échelle.
- **Instrumenter zone détails** -contient les données affichées par la vue sélectionnée de l’Instrument actuel. Nous allons examiner ces vues plus en détail dans la section ci-dessous.
- **Affichage de l’inspecteur** – contient les sections qui peuvent être sélectionnées par le contrôle segmenté. Les sections sont dépendantes de l’instrument sélectionné et comprend : Paramètres de configuration, les statistiques, les informations de Trace de la pile et chemin d’accès aux racines.

### <a name="allocations"></a>Allocations

Les informations relatives aux allocations fournissent des informations détaillées sur les objets dans l’application lorsqu'ils sont en cours de création et sur le garbage collector.

En haut du profileur est le graphique d’allocations, qui affiche la quantité de mémoire allouée à intervalles réguliers pendant le profilage. Actuellement, le graphique d’allocations est le nombre total d’allocations et pas la taille du tas à ce stade dans le temps. Dans un sens, elle n’ira jamais vers le bas, cela augmente uniquement jamais. Cela inclut les objets alloués sur la pile. Selon la version du runtime utilisée, le graphique peut différer – même pour la même application.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](images/allocations1.png "Instrumenter des allocations")](images/allocations1.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](images/allocations1-vs.png "Instrumenter des allocations")](images/allocations1-vs.png#lightbox)

-----

Il existe différentes vues de données dans l’instrument d’Allocations, qui permettent aux développeurs d’analyser la manière dont leur application est à l’aide et la libération de mémoire. Ces vues sont décrits ci-dessous :

- **Allocations** – Cela affiche une liste de toutes les allocations et les regroupe par nom de la classe. Cela constitue une excellente présentation des classes et méthodes utilisées, la fréquence à laquelle ils sont utilisés et la taille collective des classes utilisées. Double-clic sur une classe affichera la mémoire allouée : 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

  [![](images/allocations3.png "Onglet d’allocations")](images/allocations3.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/allocations2-vs.png "Onglet d’allocations")](images/allocations2-vs.png#lightbox)

-----

La vue de l’inspecteur pour les Allocations fournit les options de filtrage et regroupement d’objets, en fournissant des statistiques sur la mémoire allouée, ainsi que les allocations supérieures, ainsi que les vues pour la Trace de la pile et le chemin d’accès à la racine.

- **Arborescence des appels** – Cela affiche l’arborescence des appels entière de tous les threads dans l’application et inclut des informations sur la mémoire allouée sur chaque nœud. Lorsqu’un élément est sélectionné dans la liste, tous les nœuds frères apparaîtront gris. Vous pouvez développer l’arborescence ou double-cliquez sur l’élément à descendre dedans. Lors de l’affichage de cette vue de données, l’inspecteur de paramètres d’affichage peut être utilisé pour modifier la façon dont elle est présentée. Il existe actuellement deux options :
    1.  **Arborescence des appels d’inversé** – il considère que la trace de pile de haut en bas. C’est une option de vue pratique puisqu’il indique les méthodes plus profonde où le processeur a consacré sa durée.
    2.  **Séparé par thread** – cette option organise l’arborescence des appels par thread.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

  [![](images/allocations2.png "Onglet d’arborescence des appels")](images/allocations2.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/allocations3-vs.png "Onglet d’arborescence des appels")](images/allocations3-vs.png#lightbox)

-----

- **Captures instantanées** – ce volet affiche des informations sur les instantanés de mémoire. Pour générer ces stratégies lors du profilage d’une application en direct, cliquez sur le _caméra_ bouton dans la barre d’outils à chaque point que vous aimeriez voir quelle mémoire est conservée et publié. Vous pouvez ensuite cliquer sur chaque capture instantanée pour Explorer ce qui se passe sous le capot. Notez que des captures instantanées peuvent être prises en direct de profilage d’une application. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

  [![](images/allocations4.png "Onglet de captures instantanées")](images/allocations4.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/allocations4-vs.png "Onglet de captures instantanées")](images/allocations4-vs.png#lightbox)

-----

### <a name="time-profiler"></a>Profiler de temps

L’instrument de temps Profiler mesure exactement combien de temps vous consacrez à chaque méthode d’une application. L’application est suspendue à intervalles réguliers et une trace de pile est exécutée sur chaque thread actif. Chaque ligne dans la zone Détails de l’Instrument affiche le chemin d’exécution qui a été suivi.

Le graphique, comme illustré dans la capture d’écran ci-dessous, affiche le nombre d’échantillons reçus par l’application en cours d’exécution :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![Instrument de Profiler de temps](images/time1.png)](images/time1.png#lightbox) 

[![Temps Profiler instrumenter : liste d’exemples](images/time3.png)](images/time3.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Instrument de Profiler de temps](images/time1-vs.png)](images/time1-vs.png#lightbox) 

[![Temps Profiler instrumenter : liste d’exemples](images/time3-vs.png)](images/time3-vs.png#lightbox) 

-----

- **Arborescence des appels** – montre temps passé dans chaque méthode :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

  [![](images/time2.png "Temps Profiler instrumenter : arborescence des appels")](images/time2.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/time2-vs.png "Temps Profiler instrumenter : arborescence des appels")](images/time2-vs.png#lightbox) 

-----

### <a name="cycles"></a>Cycles

À l’aide de C# et F# du code managé, il peut être assez courant et malheureusement très facile de créer des références aux objets qui ne seront jamais supprimées. Cet instrument permet d’identifier ces objets et afficher les cycles référencés dans votre application.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![Instrumenter des cycles](images/cycles.m751-sml.png)](images/cycles.m751.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Instrumenter des cycles](images/cycles-vs-sml.png)](images/cycles-vs.png#lightbox) 

-----

## <a name="profiling-applications"></a>Profilage d’Applications

Actuellement, seuls les configurations de débogage par défaut peuvent être profilées.

Si vous profilez une application avec toutes les autres configurations, la boîte de dialogue de message suivant s’affiche :


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![Boîte de dialogue Erreur de profilage](images/image001.png)](images/image001.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](images/image1vs.png "Boîte de dialogue Erreur de profilage")](images/image1vs.png#lightbox) 

-----

Sélectionnez **mise à jour** pour continuer.

### <a name="sgen-garbage-collector-and-profiling"></a>SGen Garbage Collector et profilage

Le [SGen](https://www.mono-project.com/docs/advanced/garbage-collector/sgen/) RÉCUPÉRATEUR de mémoire est utilisé pour toutes les plateformes de Xamarin.

SGen est un GC générationnel, laquelle alloue les objets d’une application en trois segments de mémoire, Nursery, segment de mémoire principal et l’espace d’objet volumineux. Cela permet une exécution plus rapide du garbage collection. SGen est actuellement le GC par défaut pour Xamarin.Android et les applications Xamarin.iOS unifiée.

Application Xamarin.iOS à l’aide de l’API classique utilisé le GC Boehm – un RÉCUPÉRATEUR de mémoire classique, non générationnel. Comme il est classique, il est moins probable libérer la mémoire disponible, ce qui peut entraîner des résultats inexacts lorsque vous utilisez le profileur. Pour cette raison, l’instrument d’Allocations ne peut pas être utilisé avec le RÉCUPÉRATEUR de mémoire Boehm.

Pendant que vous serez invité avec une boîte de dialogue de message si votre application utilise le GC Boehm, Xamarin ne recommande pas l’application iOS existante qui utilisent Boehm à SGen sans recherche minutieuse et un test approfondi de commutation. Xamarin ne recommande également pas basculer vers SGen pour le profilage et passer ensuite, comme ces résultats ne fournira pas précis bancs d’essai d’utilisation de la mémoire.

Pour plus d’informations sur la gestion de la mémoire, reportez-vous à la [mémoire et les meilleures pratiques de performances](~/cross-platform/deploy-test/memory-perf-best-practices.md) guide.

## <a name="summary"></a>Récapitulatif

Dans ce guide, nous avons vu ce que le profilage est et comment il est avantageux pour le développeur. Ensuite, nous avons introduit le Profiler de Xamarin, en fournissant un historique et les informations dans son fonctionnement. Enfin nous animé vers les fonctionnalités de la Xamarin Profiler et exploré les Allocations et les Instruments de Profiler de temps.

## <a name="related-links"></a>Liens associés

- [Meilleures pratiques de mémoire et de performances](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Notes de publication](https://developer.xamarin.com/releases/profiler/preview/)
