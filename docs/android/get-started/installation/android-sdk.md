---
title: Configuration du kit Android SDK pour Xamarin.Android
description: Visual Studio inclut Android SDK Manager que vous utilisez pour télécharger les outils, les plateformes et d’autres composants du kit Android SDK dont vous avez besoin pour développer des applications Xamarin.Android.
ms.prod: xamarin
ms.assetid: 9A857F52-2EC1-414F-8010-CEE67B60A4B4
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/09/2018
ms.openlocfilehash: 6e80daf2be1b672dc7d0de3e303dfb9261c10472
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "76940916"
---
# <a name="setting-up-the-android-sdk-for-xamarinandroid"></a>Configuration du kit Android SDK pour Xamarin.Android

_Visual Studio inclut Android SDK Manager que vous utilisez pour télécharger les outils, les plateformes et d’autres composants du kit Android SDK dont vous avez besoin pour développer des applications Xamarin.Android._

## <a name="overview"></a>Vue d’ensemble

Ce guide explique comment utiliser Xamarin Android SDK Manager dans Visual Studio pour Mac.

> [!NOTE]
> Ce guide s’applique à Visual Studio 2019, Visual Studio 2017 et Visual Studio pour Mac.  

Xamarin Android SDK Manager (installé avec la charge de travail du**développement mobile avec .NET**) vous permet de télécharger les derniers composants Android dont vous avez besoin pour développer votre application Xamarin.Android. Il remplace le Gestionnaire du kit SDK autonome de Google, qui a été déprécié.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="requirements"></a>Spécifications

Pour utiliser le Gestionnaire Xamarin Android SDK, vous avez besoin des éléments suivants :

- Visual Studio 2019 Community, Professional ou Enterprise

- OU Visual Studio 2017 (édition Community, Professional ou Enterprise) Visual Studio 2017 version 15.7 ou version ultérieure est nécessaire.

- Visual Studio Tools pour Xamarin version 4.10.0 ou version ultérieure (installé dans le cadre de la charge de travail **Développement mobile avec .NET**). 

Le Gestionnaire Xamarin Android SDK requiert également le Kit de développement Java (qui est installé automatiquement avec Xamarin.Android). Vous pouvez choisir entre plusieurs alternatives JDK :

- Par défaut, Xamarin.Android utilise [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), qui est nécessaire si vous développez des applications pour le niveau d’API 24 ou des niveaux supérieurs (JDK 8 prend également en charge les niveaux d’API antérieurs au niveau 24).

- Vous pouvez continuer d’utiliser [JDK 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) si vous développez des applications spécifiquement pour le niveau d’API 23 ou des niveaux antérieurs.

- Si vous utilisez Visual Studio 15.8 Preview 5 ou une version ultérieure, vous pouvez essayer d’avoir recours à la [distribution Mobile OpenJDK de Microsoft](openjdk.md) au lieu du kit JDK 8.

> [!IMPORTANT]
> Xamarin.Android ne prend pas en charge JDK 9.

## <a name="sdk-manager"></a>Gestionnaire du kit SDK 

Pour démarrer le Gestionnaire du kit SDK dans Visual Studio, cliquez sur **Outils > Android  > Gestionnaire Android SDK** :

[![Emplacement de l’élément de menu Android SDK Manager](android-sdk-images/win/02-sdk-manager-menu-item-sml.png)](android-sdk-images/win/02-sdk-manager-menu-item.png#lightbox)

Le Gestionnaire Android SDK s’ouvre dans l’écran **Kits Android SDK et outils**. Cet écran comporte deux onglets &ndash; **Plateformes** et **Outils** :

[![Capture d’écran du Gestionnaire Android SDK ouvert sous l’onglet Plateformes](android-sdk-images/win/03-sdk-manager-platforms-sml.png)](android-sdk-images/win/03-sdk-manager-platforms.png#lightbox)

L’écran **Kits Android SDK et outils** est décrit plus en détail dans les sections suivantes.

### <a name="android-sdk-location"></a>Emplacement du kit Android SDK

L’emplacement du kit Android SDK est configuré en haut de l’écran **Kits Android SDK et outils**, comme illustré dans la capture précédente. Cet emplacement doit être configuré correctement pour que les onglets **Plateformes** et **Outils** fonctionnent correctement. Vous devrez peut-être définir l’emplacement du kit Android SDK pour une ou plusieurs des raisons suivantes :

1. Le Gestionnaire Android SDK n’a pas réussi à trouver le kit Android SDK. 

2. Vous avez installé le kit Android SDK à un autre emplacement (qui n’est pas l’emplacement par défaut). 

Pour définir l’emplacement du kit Android SDK, cliquez sur l’ellipse (&hellip;) situé complètement à droite du champ **Emplacement du kit Android SDK**. La boîte de dialogue **Rechercher un dossier** qui permet de naviguer vers l’emplacement du kit Android SDK s’ouvre. Dans la capture d’écran suivante, le kit Android SDK sous **Program Files (x86)\\Android** est sélectionné :

![Capture d’écran de la boîte de dialogue Windows Rechercher un dossier ayant permis de trouver sdk android](android-sdk-images/win/05-browse-for-folder.png)

Lorsque vous cliquez sur **OK**, le Gestionnaire SDK gère le kit Android SDK qui est installé à l’emplacement sélectionné.

### <a name="tools-tab"></a>Onglet Outils

L’onglet **Outils** affiche une liste d’_outils_ et de _bonus_. Utilisez cet onglet pour installer les outils du kit Android SDK, les outils de plateforme et les outils de génération.
Vous pouvez également installer l’émulateur Android, le débogueur de bas niveau (LLDB), le kit NDK, l’accélération HAXM et les bibliothèques Google Play.

Par exemple, pour télécharger le package Émulateur Android de Google, cochez la case en regard d’**Émulateur Android** et cliquez sur le bouton **Appliquer les modifications** :

[![Installation de l’émulateur Android à partir de l’onglet Outils](android-sdk-images/win/06-install-emulator-sml.png)](android-sdk-images/win/06-install-emulator.png#lightbox)

Une boîte de dialogue risque de s’afficher avec le message _Pour pouvoir installer le paquet suivant, vous devez accepter ses termes du contrat de licence_ :

![Écran Acceptation de la licence](android-sdk-images/win/07-license-acceptance.png)

Cliquez sur **Accepter** si vous acceptez les Conditions générales. En bas de la fenêtre, une barre de progression indique l’avancement du téléchargement et de l’installation. Une fois l’installation terminée, l’onglet **Outils** indique que les outils et bonus sélectionnés sont installés.

### <a name="platforms-tab"></a>Onglet Plateformes

**L’onglet Plates-formes** affiche une liste de versions SDK de plate-forme ainsi que d’autres ressources (comme les images système) pour chaque plate-forme :

[![Capture d’écran du volet Plates-formes](android-sdk-images/win/08-platforms-pane-sml.png)](android-sdk-images/win/08-platforms-pane.png#lightbox)

Cet écran liste la version Android (comme **Android 8.0**), le nom de code (**Oreo**), le niveau d’API (tel que **26**) et la taille des composants de cette plateforme (comme **1 Go**). Utilisez l’onglet **Plateformes** afin d’installer des composants pour le niveau d’API Android que vous souhaitez cibler. Pour plus d’informations sur les versions Android et les niveaux d’API, consultez [Présentation des niveaux d’API Android](~/android/app-fundamentals/android-api-levels.md).

Quand tous les composants d’une plateforme sont installés, une coche apparaît en regard du nom de la plateforme. Si tous les composants d’une plateforme ne sont pas installés, la case de cette plateforme est noircie. Vous pouvez étendre une plate-forme pour voir ses composants (et **+** quels composants sont installés) en cliquant sur la boîte à gauche de la plate-forme.
Cliquez **-** pour unxpand la liste des composants pour une plate-forme.

Pour ajouter une autre plateforme au kit SDK, cliquez sur la case en regard de la plateforme jusqu'à ce qu’une coche apparaisse afin d’installer l’ensemble de ses composants, puis cliquez sur **Appliquer les modifications** :

[![Exemple d’ajout de composants Android 7.1 Nougat à l’Android SDK](android-sdk-images/win/09-adding-a-platform-sml.png)](android-sdk-images/win/09-adding-a-platform.png#lightbox)

Pour installer uniquement des composants spécifiques, cliquez une fois sur la case en regard de la plateforme. Vous pouvez ensuite sélectionner les composants individuels dont vous avez besoin :

[![Exemple d’ajout de certains composants Android 7.1](android-sdk-images/win/10-adding-some-components-sml.png)](android-sdk-images/win/10-adding-some-components.png#lightbox)

Notez que le nombre de composants à installer s’affiche en regard du bouton **Appliquer les modifications**. Après avoir cliqué sur le bouton **Appliquer les modifications**, l’écran **Acceptation de la licence** s’affiche, comme montré plus tôt.
Cliquez sur **Accepter** si vous acceptez les Conditions générales. Cette boîte de dialogue peut s’afficher plusieurs fois si plusieurs composants sont installés. En bas de la fenêtre, une barre de progression indique l’avancement du téléchargement et de l’installation. Lorsque le processus de téléchargement et d’installation est terminé (cela peut prendre plusieurs minutes, selon le nombre de composants qui doivent être téléchargés), les composants ajoutés sont marqués avec une coche et répertoriés comme **Installé**.

### <a name="repository-selection"></a>Sélection du référentiel

Par défaut, le Gestionnaire Android SDK télécharge les composants et les outils de la plateforme à partir d’un référentiel géré par Microsoft. Si vous avez besoin d’accéder aux plateformes et outils alpha/bêta expérimentaux qui ne sont pas encore disponibles dans le référentiel Microsoft, vous pouvez basculer le Gestionnaire SDK pour que celui-ci utilise le référentiel Google. Pour effectuer ce basculement, cliquez sur l’icône d’engrenage en bas à droite et sélectionnez **Référentiel > Google (non pris en charge)**  :

[![Sélection du référentiel de Google](android-sdk-images/win/11-google-repo-w157-sml.png)](android-sdk-images/win/11-google-repo-w157.png#lightbox)

Lorsque le référentiel Google est sélectionné, des packages supplémentaires peuvent apparaître sous l’onglet **Plateformes**, qui n’étaient pas disponibles précédemment. (Dans la capture d’écran ci-dessus, **Android SDK Platform 28** a été ajouté en passant au référentiel Google.) Gardez à l’esprit que l’utilisation du référentiel Google n’est pas pris en compte et n’est donc pas recommandée pour le développement quotidien.

Pour revenir au référentiel pris en charge de plateformes et d’outils, cliquez sur **Microsoft (recommandé)**. Cette opération restaure la liste des packages et des outils à la sélection par défaut.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

## <a name="requirements"></a>Spécifications

Pour utiliser le Gestionnaire Xamarin Android SDK, vous avez besoin des éléments suivants :

- Visual Studio pour Mac 7.5 (ou ultérieur).

Le Gestionnaire Xamarin Android SDK requiert également le Kit de développement Java (qui est installé automatiquement avec Xamarin.Android). Vous pouvez choisir entre plusieurs alternatives JDK :

- Par défaut, Xamarin.Android utilise [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), qui est nécessaire si vous développez des applications pour le niveau d’API 24 ou des niveaux supérieurs (JDK 8 prend également en charge les niveaux d’API antérieurs au niveau 24).

- Vous pouvez continuer d’utiliser [JDK 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) si vous développez des applications spécifiquement pour le niveau d’API 23 ou des niveaux antérieurs.

- Si vous utilisez Visual Studio pour Mac 7.7 ou une version ultérieure, vous pouvez essayer d’avoir recours à la [distribution Mobile OpenJDK de Microsoft](openjdk.md) au lieu du kit JDK 8.

> [!IMPORTANT]
> Xamarin.Android ne prend pas en charge JDK 9.

## <a name="sdk-manager"></a>Gestionnaire du kit SDK 

Pour démarrer le Gestionnaire du kit SDK dans Visual Studio pour Mac, cliquez sur **Outils > SDK Manager** :

[![Emplacement de l’élément de menu Android SDK Manager](android-sdk-images/mac/01-sdk-manager-menu-item-m75-sml.png)](android-sdk-images/mac/01-sdk-manager-menu-item-m75.png#lightbox)

Le **Gestionnaire Android SDK** s’ouvre dans la **fenêtre Préférences**, qui contient trois onglets, **Plateformes**, **Outils**et **Emplacements** :

[![Capture d’écran du Gestionnaire Android SDK ouvert sous l’onglet Plateformes](android-sdk-images/mac/02-sdk-manager-platforms-m75-sml.png)](android-sdk-images/mac/02-sdk-manager-platforms-m75.png#lightbox)

Les onglets du Gestionnaire Android SDK sont décrits dans les sections suivantes.

### <a name="locations-tab"></a>Onglet Emplacements

L’onglet **Emplacements** comporte trois paramètres permettant de configurer l’emplacement du kit Android SDK, du kit Android NDK et du kit JDK (Java SDK). Ces emplacements doivent être configurés correctement pour que les onglets **Plateformes** et **Outils** fonctionnent correctement.

Au démarrage , le Gestionnaire du kit SDK détermine automatiquement le chemin de chaque package installé et indique qu’il a été **trouvé** en affichant une coche verte en regard du chemin :

[![Capture d’écran de l’onglet Emplacements](android-sdk-images/mac/03-locations-tab-m75-sml.png)](android-sdk-images/mac/03-locations-tab-m75.png#lightbox)

Cliquez sur le bouton **Rétablir les valeurs par défaut** pour faire en sorte que le Gestionnaire du kit SDK recherche le kit SDK, le kit NDK et le kit JDK à leur emplacement par défaut. 

En général, vous utilisez l’onglet **Emplacements** pour modifier l’emplacement du kit Android SDK et/ou du kit Java JDK. Vous n’avez pas besoin d’installer le kit NDK pour développer des applications Xamarin.Android. Le kit NDK est utilisé uniquement lorsque vous avez besoin de développer des parties de votre application à l’aide de langages en code natif, comme C et C++.

### <a name="tools-tab"></a>Onglet Outils

L’onglet **Outils** affiche une liste d’_outils_ et de _bonus_. Utilisez cet onglet pour installer les outils du kit Android SDK, les outils de plateforme et les outils de génération.
Vous pouvez également installer l’émulateur Android, le débogueur de bas niveau (LLDB), le kit NDK, l’accélération HAXM et les bibliothèques Google Play.

Par exemple, pour télécharger le package Émulateur Android de Google, cochez la case en regard d’**Émulateur Android** et cliquez sur le bouton **Appliquer les modifications** :

[![Installation de l’émulateur Android à partir de l’onglet Outils](android-sdk-images/mac/04-tools-tab-m75-sml.png)](android-sdk-images/mac/04-tools-tab-m75.png#lightbox)

Une boîte de dialogue risque de s’afficher avec le message _Pour pouvoir installer le paquet suivant, vous devez accepter ses termes du contrat de licence_ :

[![Écran Acceptation de la licence](android-sdk-images/mac/05-license-acceptance-m75-sml.png)](android-sdk-images/mac/05-license-acceptance-m75.png#lightbox)

Cliquez sur **Accepter** si vous acceptez les Conditions générales. En bas de la fenêtre, une barre de progression indique l’avancement du téléchargement et de l’installation. Une fois l’installation terminée, l’onglet **Outils** indique que les outils et bonus sélectionnés sont installés.

### <a name="platforms-tab"></a>Onglet Plateformes

**L’onglet Plates-formes** affiche une liste de versions SDK de plate-forme ainsi que d’autres ressources (comme les images système) pour chaque plate-forme :

[![Capture d’écran du volet Plates-formes](android-sdk-images/mac/06-platforms-tab-m75-sml.png)](android-sdk-images/mac/06-platforms-tab-m75.png#lightbox)

Cet écran liste la version Android (comme **Android 8.1**), le nom de code (**Oreo**), le niveau d’API (tel que **27**) et la taille des composants de cette plateforme (comme **1 Go**). Utilisez l’onglet **Plateformes** afin d’installer des composants pour le niveau d’API Android que vous souhaitez cibler. Pour plus d’informations sur les versions Android et les niveaux d’API, consultez [Présentation des niveaux d’API Android](~/android/app-fundamentals/android-api-levels.md).

Quand tous les composants d’une plateforme sont installés, une coche apparaît en regard du nom de la plateforme. Si tous les composants d’une plateforme ne sont pas installés, la case de cette plateforme est noircie. Vous pouvez développer une plateforme pour voir ses composants (et ceux qui sont installés) en cliquant sur la **flèche** à gauche de la plateforme.
Cliquez sur la **flèche vers le bas** pour réduire la liste de composants d’une plateforme.

Pour ajouter une autre plateforme au kit SDK, cliquez sur la case en regard de la plateforme jusqu'à ce qu’une coche apparaisse afin d’installer l’ensemble de ses composants, puis cliquez sur **Appliquer les modifications** :

[![Exemple d’ajout de tous les composants d’une plate-forme](android-sdk-images/mac/07-install-all-m75-sml.png)](android-sdk-images/mac/07-install-all-m75.png#lightbox)

Pour installer uniquement quelques composants, cliquez une fois sur la case en regard de la plateforme. Vous pouvez ensuite sélectionner les composants individuels dont vous avez besoin :

[![Exemple d’ajout de certains composants](android-sdk-images/mac/08-individual-components-m75-sml.png)](android-sdk-images/mac/08-individual-components-m75.png#lightbox)

Notez que le nombre de composants à installer s’affiche en regard du bouton **Appliquer les modifications**. Après avoir cliqué sur le bouton **Appliquer les modifications**, l’écran **Acceptation de la licence** s’affiche, comme montré plus tôt.
Cliquez sur **Accepter** si vous acceptez les Conditions générales. Cette boîte de dialogue peut s’afficher plusieurs fois si plusieurs composants sont installés. En bas de la fenêtre, une barre de progression indique l’avancement du téléchargement et de l’installation. Lorsque le processus de téléchargement et d’installation est terminé (cela peut prendre plusieurs minutes, selon le nombre de composants qui doivent être téléchargés), les composants ajoutés sont marqués avec une coche et répertoriés comme **Installé**.

### <a name="repository-selection"></a>Sélection du référentiel

Par défaut, le Gestionnaire Android SDK télécharge les composants et les outils de la plateforme à partir d’un référentiel géré par Microsoft. Si vous avez besoin d’accéder aux plateformes et outils alpha/bêta expérimentaux qui ne sont pas encore disponibles dans le référentiel Microsoft, vous pouvez basculer le Gestionnaire SDK pour que celui-ci utilise le référentiel Google. Pour effectuer ce basculement, cliquez sur l’icône d’engrenage en bas à droite et sélectionnez **Référentiel > Google (non pris en charge)**  :

[![Sélection du référentiel de Google](android-sdk-images/mac/09-google-repo-m75-sml.png)](android-sdk-images/mac/09-google-repo-m75.png#lightbox)

Lorsque le référentiel Google est sélectionné, des packages supplémentaires peuvent apparaître sous l’onglet **Plateformes**, qui n’étaient pas disponibles précédemment. (Dans la capture d’écran ci-dessus, **Android SDK Platform 28** a été ajouté en passant au référentiel Google.) Gardez à l’esprit que l’utilisation du référentiel Google n’est pas pris en compte et n’est donc pas recommandée pour le développement quotidien.

Pour revenir au référentiel pris en charge de plateformes et d’outils, cliquez sur **Microsoft (recommandé)**. Cette opération restaure la liste des packages et des outils à la sélection par défaut.

-----

## <a name="summary"></a>Récapitulatif

Dans ce guide, vous avez appris à installer et utiliser l’outil Xamarin Android SDK Manager pour Visual Studio et Visual Studio pour Mac.

## <a name="related-links"></a>Liens connexes

- [Présentation des niveaux d’API d’Android](~/android/app-fundamentals/android-api-levels.md)
- [Modifications des outils Android SDK](~/android/troubleshooting/sdk-cli-tooling-changes.md)
