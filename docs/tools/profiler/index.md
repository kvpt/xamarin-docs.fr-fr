---
title: Xamarin Profiler
description: Ce guide présente les principales fonctionnalités du profileur Xamarin. Il permet de consulter les profileurs, le profilage, et de savoir quand les utiliser à bon escient et dans un flux de travail standard pour le profilage d’applications de Xamarin.
ms.prod: xamarin
ms.assetid: 3247fcee-6acc-470d-ab87-c1c511d67363
author: lobrien
ms.author: laobri
ms.date: 06/03/2018
ms.openlocfilehash: 4541a795d819c3738fffbb3224ef35dce5fc5521
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69529046"
---
# <a name="xamarin-profiler"></a>Xamarin Profiler

_Ce guide présente les principales fonctionnalités du profileur Xamarin. Il permet de consulter les profileurs, le profilage, et de savoir quand les utiliser à bon escient et dans un flux de travail standard pour le profilage d’applications de Xamarin._

La réussite d’une application dépend de l’expérience de l’utilisateur final. En tant que développeur, vous avez peut-être implémenté des fonctionnalités vraiment impressionnantes dans votre application, mais si l’application est lente ou saturée, l’utilisateur est susceptible de l’éliminer.

Historiquement, mono a inclus un puissant profileur de ligne de commande pour la collecte d’informations sur les programmes en cours d’exécution dans le runtime mono appelé [mono log Profiler](https://www.mono-project.com/docs/debug+profile/profile/profiler/). Le Xamarin Profiler une interface graphique pour le profileur mono log et prend en charge le profilage des applications Android, iOS, tvOS et Mac sur Mac, ainsi que des applications Android, iOS et tvOS sur Windows.

Le Xamarin Profiler a un certain nombre d’instruments disponibles pour le profilage (allocations, cycles et Time Profiler). Ce guide explore ce que ces instruments mesurent et comment ils analysent votre application, et clarifie la signification des données présentées sur chaque écran.

Ce guide examine les scénarios de profilage courants et présente le profileur en tant qu’outil pour faciliter l’analyse et l’optimisation des applications iOS et Android.

## <a name="download-and-install"></a>Télécharger et installer

> [!NOTE]
> Vous devez être un abonné [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/compare/) pour déverrouiller cette fonctionnalité dans Visual Studio Enterprise sur Windows ou Visual Studio pour Mac sur un Mac.

Le Xamarin Profiler est une application autonome et est intégré à Visual Studio pour Mac et Visual Studio pour activer le profilage à partir de l’IDE.

Téléchargez le package d’installation pour votre plateforme:

- [**macOS**](https://dl.xamarin.com/profiler/profiler-mac.pkg)
- [**Windows**](https://dl.xamarin.com/profiler/profiler-windows.msi)

Une fois le téléchargement terminé, lancez le programme d’installation pour ajouter le Xamarin Profiler à votre système.

## <a name="profilers-and-profiling"></a>Profileurs et profilage

Le profilage est une étape importante et souvent négligée dans le développement d’applications. Le profilage est une forme d'**analyse dynamique de programme** - il analyse le programme alors qu’il est en cours d’exécution et en cours d’utilisation. Un profileur est un outil d’exploration de données qui collecte des informations sur la complexité temporelle, l’utilisation de méthodes particulières et la mémoire allouée. Un profileur permet d’explorer en profondeur et d'analyser ces mesures pour identifier les zones à problème dans le code.

Lors de la conception et du développement d’une application, il est important de ne pas optimiser prématurément. autrement dit, vous consacrez du temps à développer votre code dans des domaines qui sont rarement accessibles. Il s’agit de la puissance du profilage. Un profileur fournit des informations sur les parties les plus couramment utilisées de votre base de code et vous aide à localiser les zones où vous devez consacrer du temps à apporter des améliorations. Les développeurs doivent s’occuper de savoir où la majeure partie du temps est passée dans votre application et comment la mémoire est utilisée par votre application.

Le profilage est utile dans tous les types de développement, mais il est particulièrement crucial dans le développement mobile. Le code non optimisé est bien plus perceptible sur les plateformes mobiles que sur les ordinateurs de bureau, et la réussite de votre application dépend d’un code très beau et optimisé qui s’exécute efficacement.

## <a name="xamarin-profiler"></a>Xamarin Profiler

Le Xamarin Profiler offre aux développeurs un moyen de profiler des applications depuis Visual Studio pour Mac ou Visual Studio. Le profileur collecte et affiche des informations sur l’application, qui peuvent ensuite être utilisées par le développeur pour analyser le comportement d’une application. Il existe plusieurs façons de profiler une application avec l’Xamarin Profiler, à savoir le profilage de la mémoire et l’échantillonnage statistique. Celles-ci sont effectuées par le biais des instruments allocations et Time Profiler, respectivement.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Actuellement, le Xamarin Profiler peut être utilisé pour tester des applications Xamarin. iOS, Xamarin. Android et Xamarin. Mac sur Mac (via Visual Studio pour Mac). Le profileur est un processus distinct de l’IDE, et par conséquent, en plus de lancer à partir de Visual Studio pour Mac, il peut être utilisé comme une application autonome pour `.mlpd` examiner les fichiers. exe et les fichiers qui ont été générés à partir du [profileur mono log](https://www.mono-project.com/docs/debug+profile/profile/profiler/).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Actuellement, le Xamarin Profiler peut être utilisé pour tester des applications Xamarin. Android sur Windows (par le biais de Visual Studio et Visual Studio pour Mac). Le profileur est un processus distinct de l’IDE, et donc, en plus du lancement à partir de Visual Studio, il peut être utilisé comme une application autonome pour examiner `.mlpd` les fichiers. exe et les fichiers qui ont été générés à partir du profileur de [Journal mono](https://www.mono-project.com/docs/debug+profile/profile/profiler/).

-----

<a name="Profiler_Support" />

## <a name="profiler-support"></a>Prise en charge du profileur

La prise en charge de l’Xamarin Profiler est disponible sur les plateformes suivantes:

- Visual Studio pour Mac (macOS, avec licence entreprise)
    - Android
        - Appareil et émulateur
    - iOS
        - Appareil et simulateur
    - tvOS (l’instrumentation de temps n’est pas pris en charge)
        - Appareil et simulateur
    - Mac

- Visual Studio (version **Enterprise** uniquement)
    - Android
        - Appareil et émulateur
    - iOS [expérimental]
        - Appareil et simulateur
    - tvOS
        - Appareil et simulateur

Notez que vous pouvez **uniquement** profiler les configurations de **débogage** .

## <a name="profiler-basics"></a>Notions de base du profileur

Cette section présente les différentes parties de la Xamarin Profiler et décrit ses fonctionnalités.

### <a name="allow-profiling-in-your-app"></a>Autoriser le profilage dans votre application

Avant de pouvoir Profiler correctement votre application, vous devez autoriser le profilage dans les options de projet de l’application.

- Libéré

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

  **Générer > le débogage iOS > activer le profilage**

  ![](images/ios-options-mac.png "Boîte de dialogue Options iOS dans Visual Studio pour Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  **Propriétés > Build iOS > activer le profilage**

  ![](images/ios-project-options-vs.png "Boîte de dialogue Options iOS dans Visual Studio")

-----

- Tablette

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

  **Build > Android Debug > activer l’instrumentation du développeur**

  ![Boîte de dialogue Options Android dans Visual Studio pour Mac](images/android-project-options.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  **Build > Android Debug > activer l’instrumentation du développeur**

  ![Boîte de dialogue Options Android dans Visual Studio pour Mac](images/android-project-options-vs-sml.png)

-----

### <a name="launching-the-profiler"></a>Lancement du profileur

Le profileur Xamarin peut être lancé à partir de votre IDE quand vous profilez votre application Android ou iOS. Le profileur peut aussi être lancé comme une application indépendante.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

#### <a name="launching-from-visual-studio-for-mac"></a>Lancement à partir de Visual Studio pour Mac

1. Tout d’abord, assurez-vous que votre application est chargée dans Visual Studio pour Mac et sélectionnez la configuration de débogage (par défaut).
2. Parcourez l’écran pour **exécuter > démarrer**le profilage dans Visual Studio pour Mac, ou **analyser > Xamarin Profiler** dans Visual Studio, pour ouvrir le profileur, comme illustré dans le diagramme ci-dessous:

  ![](images/start-profiling-xs.png "Lancement du profileur à partir de Visual Studio pour Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

#### <a name="launching-from-visual-studio"></a>Lancement à partir de Visual Studio

1. Tout d’abord, assurez-vous que votre application est chargée dans Visual Studio et sélectionnez la configuration de débogage (par défaut), comme indiqué ci-dessus.
2. Accédez à **analyser > Xamarin Profiler** dans Visual Studio pour ouvrir le profileur, comme illustré dans le diagramme ci-dessous:

![Lancement du profileur à partir de Visual Studio](images/start-profiling-vs.png)

-----

Si les éléments de menu ne s’affichent pas, reportez-vous au [Guide de résolution des problèmes](~/tools/profiler/troubleshooting.md).

Cela lance le profileur et démarre automatiquement le profilage de l’application.

Le profileur peut être utilisé pour mesurer la mémoire et les performances. Pour ce faire, il s’agit des instruments allocations et Time Profiler, que nous explorerons en détail dans la section suivante.

#### <a name="saving-and-loading-profiler-sessions"></a>Enregistrement et chargement des sessions du profileur

Pour enregistrer une session de profilage à tout moment, choisissez **fichier > enregistrer sous...** dans la barre de menus du profileur. Cela permet d’enregistrer le fichier au format _MLPD_ , un format spécial et hautement compressé pour le profilage des données.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Une fois l’installation terminée, le Xamarin Profiler se trouve dans votre dossier applications, comme illustré dans la capture d’écran ci-dessous:

![](images/applications.png "Ouvrir le profileur autonome à partir de Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Une fois installée, l’application Xamarin Profiler se trouve dans le répertoire de votre application:

![](images/applications-vs.png "Ouvrir le profileur autonome à partir de Windows")

-----

Vous pouvez charger des fichiers *. MLPD* dans le profileur en ouvrant l’application autonome, en sélectionnant **choisir la cible** et en chargeant le fichier.

Pour plus d’informations, consultez [génération de fichiers. MLPD](~/tools/profiler/troubleshooting.md#gen_mlpd).

## <a name="profiler-features"></a>Fonctionnalités du profileur

La Xamarin Profiler est composée de cinq sections, comme illustré ci-dessous:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](images/profiler-mac-sml.png "Sections du profileur dans Visual Studio pour Mac")](images/profiler-mac.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](images/profiler-vs.png "Sections du profileur dans Visual Studio")](images/profiler-vs.png#lightbox)

-----

- **Barre d’outils** : située en haut du profileur, il offre des options pour démarrer/arrêter le profilage, sélectionner un processus cible, afficher le temps d’exécution de l’application et sélectionner les affichages fractionnés qui composent l’application du profileur.
- **Liste** des instruments: répertorie tous les instruments chargés pour la session de profilage.
- **Graphique** à courbes: ces graphiques sont reliés horizontalement aux instruments pertinents dans la liste d’instruments. Un curseur (indiqué sous Time Profiler) peut être utilisé pour modifier l’échelle.
- **Zone de détails** de l’instrument: contient les données affichées par la vue sélectionnée de l’instrument actuel. Nous examinerons ces vues plus en détail dans la section ci-dessous.
- **Vue Inspector** : contient des sections qui peuvent être sélectionnées par le contrôle segmenté. Les sections dépendent de l’instrument sélectionné et incluent les éléments suivants: Paramètres de configuration, statistiques, informations de trace de la pile et chemin d’accès aux racines.

### <a name="allocations"></a>Allocations

Les informations relatives aux allocations fournissent des informations détaillées sur les objets dans l’application lorsqu'ils sont en cours de création et sur le garbage collector.

En haut du profileur se trouve le graphique allocations, qui affiche la quantité de mémoire allouée à intervalles réguliers pendant le profilage. Actuellement, le graphique allocations est le nombre total d’allocations et non la taille du tas à ce moment précis. Dans un sens, il ne s’arrêtera jamais. cela ne fera que croître. Cela comprend les objets alloués sur la pile. Selon la version du runtime utilisée, le graphique peut paraître différent, même pour la même application.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](images/allocations1.png "Allocation (Instrumentation)")](images/allocations1.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](images/allocations1-vs.png "Allocation (Instrumentation)")](images/allocations1-vs.png#lightbox)

-----

Il existe différentes vues de données dans l’instrument allocations, qui permettent aux développeurs d’analyser la manière dont l’application utilise et libère de la mémoire. Ces vues sont décrites ci-dessous:

- Allocations: affiche une liste de toutes les allocations et les regroupe par nom de classe. Vous bénéficiez ainsi d’une vue d’ensemble des classes et des méthodes utilisées, de leur fréquence d’utilisation et de la taille collective des classes utilisées. Double-cliquez sur une classe pour afficher la mémoire allouée: 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

  [![](images/allocations3.png "Onglet allocations")](images/allocations3.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/allocations2-vs.png "Onglet allocations")](images/allocations2-vs.png#lightbox)

-----

La vue de l’inspecteur pour les Allocations fournit les options de filtrage et regroupement d’objets, en fournissant des statistiques sur la mémoire allouée, ainsi que les allocations supérieures, ainsi que les vues pour la Trace de la pile et le chemin d’accès à la racine.

- **Arborescence des appels** : affiche la totalité de l’arborescence des appels de tous les threads de l’application et comprend des informations sur la mémoire allouée sur chaque nœud. Lorsqu’un élément est sélectionné dans la liste, tous les nœuds frères s’affichent en gris. Vous pouvez développer l’arborescence ou double-cliquer sur l’élément pour l’examiner. Lorsque vous Affichez cette vue de données, vous pouvez utiliser la vue inspecteur des paramètres d’affichage pour modifier la façon dont elle est présentée. Il existe actuellement deux options:
    1. **Inversée arborescence des appels** : prend en compte la trace de la pile de haut en bas. Il s’agit d’une option d’affichage pratique, car elle indique les méthodes les plus profondes dans lesquelles le processeur consacre son temps.
    2. **Séparer par thread** : cette option permet d’organiser l’arborescence des appels par thread.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

  [![](images/allocations2.png "Onglet arborescence des appels")](images/allocations2.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/allocations3-vs.png "Onglet arborescence des appels")](images/allocations3-vs.png#lightbox)

-----

- **Captures instantanées** : ce volet affiche des informations sur les instantanés de la mémoire. Pour les générer lors du profilage d’une application active, cliquez sur le bouton de l' _appareil photo_ dans la barre d’outils à chaque point où vous souhaitez voir la mémoire conservée et libérée. Vous pouvez ensuite cliquer sur chaque instantané pour explorer ce qui se passe en coulisses. Notez que les instantanés ne peuvent être pris que lors du profilage en temps réel d’une application. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

  [![](images/allocations4.png "Onglet instantanés")](images/allocations4.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/allocations4-vs.png "Onglet instantanés")](images/allocations4-vs.png#lightbox)

-----

### <a name="time-profiler"></a>Heure du profileur

L’instrument du profileur de temps mesure exactement le temps passé dans chaque méthode d’une application. L’application est suspendue à intervalles réguliers et une trace de la pile est exécutée sur chaque thread actif. Chaque ligne de la zone de détails de l’instrument affiche le chemin d’exécution qui a été suivi.

Le graphique en courbes, comme indiqué dans la capture d’écran ci-dessous, affiche le nombre d’exemples reçus par l’application pendant son exécution:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![Instrumenter le profileur Time](images/time1.png)](images/time1.png#lightbox) 

[![Time Profiler instrument – liste d’exemples](images/time3.png)](images/time3.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Instrumenter le profileur Time](images/time1-vs.png)](images/time1-vs.png#lightbox) 

[![Time Profiler instrument – liste d’exemples](images/time3-vs.png)](images/time3-vs.png#lightbox) 

-----

- **Arborescence des appels** : affiche la durée passée dans chaque méthode:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

  [![](images/time2.png "Instrument du profileur de temps – arborescence des appels")](images/time2.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/time2-vs.png "Instrument du profileur de temps – arborescence des appels")](images/time2-vs.png#lightbox) 

-----

### <a name="cycles"></a>Cycles

Grâce à l’utilisation C# de F# et du code managé, il peut être assez courant et, malheureusement, il est relativement facile de créer des références à des objets qui ne seront jamais supprimés. Cet instrument vous permet d’identifier ces objets et d’afficher les cycles référencés dans votre application.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![Instrument de cycles](images/cycles.m751-sml.png)](images/cycles.m751.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Instrument de cycles](images/cycles-vs-sml.png)](images/cycles-vs.png#lightbox) 

-----

## <a name="profiling-applications"></a>Profilage d’applications

Actuellement, seules les configurations de débogage par défaut peuvent être profilées.

Si vous profilez une application avec une autre configuration, la boîte de dialogue de message suivante s’affiche:


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![Boîte de dialogue d’erreur de profilage](images/image001.png)](images/image001.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](images/image1vs.png "Boîte de dialogue d’erreur de profilage")](images/image1vs.png#lightbox) 

-----

Sélectionnez **mettre à jour** pour continuer.

### <a name="sgen-garbage-collector-and-profiling"></a>Garbage collector et profilage SGen

Le garbage collector [SGen](https://www.mono-project.com/docs/advanced/garbage-collector/sgen/) est utilisé pour toutes les plateformes Xamarin.

SGen est un GC générationnel qui alloue des objets d’une application en trois tas: le tas de la pépinière, le tas principal et l’espace de Large Object. Cela permet une exécution plus rapide des garbage collection. SGen est actuellement le catalogue global par défaut pour les applications unifiées Xamarin. Android et Xamarin. iOS.

L’application Xamarin. iOS utilisant le API classique utilisé le garbage collector Boehm, un récupérateur de mémoire non générationnel et prudent. Comme il est conservateur, il est moins probable de libérer de la mémoire disponible, ce qui peut entraîner des résultats incorrects lors de l’utilisation du profileur. Pour cette raison, l’instrument allocations ne peut pas être utilisé avec le garbage collector Boehm.

Vous serez invité à entrer un message si votre application utilise le GC Boehm, Xamarin ne recommande pas de basculer l’application iOS existante qui utilise Boehm vers SGen sans recherche minutieuse et test minutieux. Xamarin ne recommande pas non plus de basculer vers SGen pour le profilage, puis de revenir en arrière, car ces résultats ne fournissent pas d’évaluation exacte de l’utilisation de la mémoire.

Pour plus d’informations sur la gestion de la mémoire, reportez-vous au Guide des [meilleures pratiques en matière de performances et de mémoire](~/cross-platform/deploy-test/memory-perf-best-practices.md) .

## <a name="summary"></a>Récapitulatif

Dans ce guide, nous avons vu ce qu’est le profilage et comment il est avantageux pour le développeur. Nous avons ensuite introduit le Xamarin Profiler, en fournissant un historique et des informations sur son fonctionnement. Enfin, nous avons parcouru les fonctionnalités de la Xamarin Profiler et exploré les allocations et les instruments du profileur de temps.

## <a name="related-links"></a>Liens associés

- [Meilleures pratiques en matière de performances et de mémoire](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Notes de publication](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/profiler/preview/index.md)
