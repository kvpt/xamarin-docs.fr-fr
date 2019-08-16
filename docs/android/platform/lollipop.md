---
title: Fonctionnalités Lollipop
description: Cet article offre une vue d’ensemble des nouvelles fonctionnalités introduites dans Android 5,0 (lollipop). Ces fonctionnalités incluent un nouveau style d’interface utilisateur appelé «thème de matériau», ainsi que de nouvelles fonctionnalités de prise en charge telles que les animations, les ombres de vue et les teintes de dessin. Android 5,0 comprend également des notifications améliorées, deux nouveaux widgets d’interface utilisateur, un nouveau planificateur de travaux et une poignée de nouvelles API pour améliorer le stockage, la mise en réseau, la connectivité et les fonctionnalités multimédias.
ms.prod: xamarin
ms.assetid: 1CE99CFE-FAAC-49FC-AEDC-1A21FC6E946E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 7cc7eeabebbfbcd4b264202235307638b88842d8
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524214"
---
# <a name="lollipop-features"></a>Fonctionnalités Lollipop

_Cet article offre une vue d’ensemble des nouvelles fonctionnalités introduites dans Android 5,0 (lollipop). Ces fonctionnalités incluent un nouveau style d’interface utilisateur appelé «thème de matériau», ainsi que de nouvelles fonctionnalités de prise en charge telles que les animations, les ombres de vue et les teintes de dessin. Android 5,0 comprend également des notifications améliorées, deux nouveaux widgets d’interface utilisateur, un nouveau planificateur de travaux et une poignée de nouvelles API pour améliorer le stockage, la mise en réseau, la connectivité et les fonctionnalités multimédias._

## <a name="lollipop-overview"></a>Vue d’ensemble du Lollipop

Android 5,0 (lollipop) introduit un nouveau langage de conception, une *conception de matériau*et, avec le service informatique, qui prend en charge de nouvelles fonctionnalités pour rendre les applications plus faciles et plus intuitives à utiliser. Avec la conception de matériaux, Android 5,0 fournit non seulement des téléphones Android rénové; Il fournit également un nouvel ensemble de règles de conception pour les tablettes Android, les ordinateurs de bureau, les montres et les téléviseurs intelligents. Ces règles de conception insistent sur la simplicité et le minimum tout en utilisant des attributs tactiles familiers (tels que des signaux de surface et de bord réalistes) pour aider les utilisateurs à comprendre rapidement et intuitivement l’interface.

Le *thème du matériau* est le incarnation de ces principes de conception d’interface utilisateur dans Android. Cet article commence par couvrir les fonctionnalités de prise en charge du thème de matériau:

- **Animations** Animations de commentaires tactiles, animations de transition d’activité, animations de transition d’état d’affichage et effet de révélation. &ndash;

- **Afficher les ombres et l’élévation** Les vues ont désormais `elevation` une propriété; &ndash;   les vues avec `elevation` des valeurs plus élevées convertissent les plus grandes ombres sur l’arrière-plan.

- **Fonctionnalités de couleur** La coloration pouvant être dessinée vous permet de réutiliser des éléments multimédias d’image en modifiant leur couleur, et l' *extraction de couleurs visible* vous permet de créer un thème dynamique de votre application en fonction des couleurs d’une image. &ndash;

De nombreuses fonctionnalités de thème matériel sont déjà intégrées à l’interface utilisateur Android 5,0, tandis que d’autres doivent être explicitement ajoutées aux applications. Par exemple, certains affichages standard (tels que les boutons) incluent déjà les animations de commentaires tactiles, tandis que les applications doivent activer la plupart des ombres de vue.

En plus des améliorations apportées à l’interface utilisateur par le biais du thème matériel, Android 5,0 intègre également plusieurs nouvelles fonctionnalités qui sont traitées dans cet article:

- **Notifications améliorées** Les notifications dans Android 5,0 ont été mises à jour de façon significative avec une nouvelle apparence, une prise en charge des notifications l’écran et un nouveau format de présentation de notifications de *niveau supérieur.* &ndash;

- **Nouveaux widgets d’interface utilisateur** Le nouveau `RecyclerView` widget permet aux applications de communiquer plus facilement des jeux de données volumineux et des informations complexes, `CardView` tandis que le nouveau widget fournit un format de présentation de type carte simplifié pour l’affichage de texte et d’images. &ndash;

- **Nouvelles API** &ndash; Android 5,0 ajoute de nouvelles API pour la prise en charge de plusieurs réseaux, une connectivité Bluetooth améliorée, une gestion du stockage plus facile et un contrôle plus flexible des lecteurs multimédias et des appareils photo. Une nouvelle fonctionnalité de planification des travaux est disponible pour exécuter des tâches de façon asynchrone à des heures planifiées. Cette fonctionnalité permet d’améliorer la durée de vie de la batterie, par exemple la planification des tâches à effectuer lorsque l’appareil est branché et en cours de chargement.


## <a name="requirements"></a>Configuration requise

Les éléments suivants sont requis pour utiliser les nouvelles fonctionnalités Android 5,0 dans les applications basées sur Xamarin:

- **Xamarin. Android** &ndash; Xamarin. Android 4,20 ou version ultérieure doit être installé et configuré à l’aide de Visual Studio ou de Visual Studio pour Mac. 

- **Android SDK** &ndash; Android 5,0 (API 21) ou version ultérieure doit être installé via le gestionnaire de Android SDK.

- **Kit de développement Java** Xamarin. Android nécessite [JDK 1,8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou une version ultérieure si vous développez pour l’API de niveau 24 ou supérieur (JDK 1,8 prend également en charge les niveaux d’API antérieurs à 24, y compris les interfaces Lollipop). &ndash; La version 64 bits de JDK 1,8 est requise si vous utilisez des contrôles personnalisés ou le générateur d’aperçu de formulaires.

Vous pouvez continuer à utiliser [JDK 1,7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) si vous développez spécifiquement pour l’API de niveau 23 ou antérieur.


## <a name="setting-up-an-android-50-project"></a>Configuration d’un projet Android 5,0

Pour créer un projet Android 5,0, vous devez installer les derniers outils et packages du kit de développement logiciel (SDK). Procédez comme suit pour configurer un projet Xamarin. Android qui cible Android 5,0:

1. Installez les outils Xamarin. Android et activez votre licence Xamarin. Pour plus d’informations sur l’installation de Xamarin. Android, consultez [installation et installation](~/android/get-started/installation/index.md) .

2. Si vous utilisez Visual Studio pour Mac, installez les dernières mises à jour Android 5,0.

3. Démarrez le gestionnaire de Android SDK (dans Visual Studio pour Mac, **Utilisez &gt; outils ouvrir Android SDK&hellip;Manager**) et installez Android SDK Tools 23.0.5 ou version ultérieure:

    [![Sélection des outils de Android SDK dans le gestionnaire de Android SDK](lollipop-images/android-l-tools-sml.png)](lollipop-images/android-l-tools.png#lightbox)

   Installez également les derniers packages du kit de développement logiciel (SDK) Android 5,0 (API 21 ou ultérieure):

    [![Installation des packages du kit de développement logiciel (SDK) Android 5,0 dans le gestionnaire de Android SDK](lollipop-images/android-l-sdk-pkgs-sml.png)](lollipop-images/android-l-sdk-pkgs.png#lightbox)

   Pour plus d’informations sur l’utilisation du gestionnaire de Android SDK, consultez [SDK Manager](https://developer.android.com/tools/help/sdk-manager.html).

4. Créez un projet Xamarin. Android. Si vous débutez avec le développement Android avec Xamarin, consultez [Hello, Android](~/android/get-started/hello-android/index.md) pour en savoir plus sur la création de projets Android. Lorsque vous créez un projet Android, veillez à configurer les paramètres de version pour Android 5,0.
   Dans Visual Studio pour Mac, accédez à **options &gt; du projet &gt; générer général** et définissez **Framework cible** sur **Android 5,0 (lollipop)** ou version ultérieure:

    ![Définition de la cible Framwework sur Android 5,0 Lollipop](lollipop-images/target-framework.png)

   Sous **options &gt; du projet &gt; , générer une application Android**, définir la version Android minimale et cible sur **automatique-utiliser la version du Framework cible**:

    ![Définition des versions minimale et cible d’Android sur automatique](lollipop-images/minimum-android-version.png)

5. Configurez un émulateur ou un appareil Android pour tester votre application. Si vous utilisez un émulateur, consultez [émulateur Android du programme d’installation](~/android/get-started/installation/android-emulator/index.md) pour savoir comment configurer un émulateur Android pour une utilisation avec Xamarin Studio ou Visual Studio. Si vous utilisez un appareil Android, consultez [la page Configuration du kit de développement logiciel (SDK) Preview](https://developer.android.com/preview/setup-sdk.html) pour découvrir comment mettre à jour votre appareil pour Android 5,0. Pour configurer votre appareil Android en vue d’exécuter et de déboguer des applications Xamarin. Android, consultez [configurer un appareil pour le développement](~/android/get-started/installation/set-up-device-for-development.md).

Remarque : Si vous mettez à jour un projet Android existant ciblant la version préliminaire d’Android L, vous devez mettre à jour le **Framework cible** et la **version Android** avec les valeurs décrites ci-dessus.

## <a name="important-changes"></a>Modifications importantes

Les applications Android précédemment publiées pouvaient être affectées par les modifications apportées à Android 5,0. En particulier, Android 5,0 utilise un nouveau Runtime et un format de notification considérablement modifié.

### <a name="android-runtime"></a>Runtime Android

Android 5,0 utilise le nouveau Runtime Android (ART) comme Runtime par défaut au lieu de Dalvik. ART met en œuvre plusieurs nouvelles fonctionnalités majeures:

- **Compilation de l’AOA (Ahead of Time)** &ndash; AOA peut améliorer les performances des applications en compilant le code de l’application avant le premier lancement de l’application. Quand une application est installée, l’ART génère un exécutable d’application compilé pour l’appareil cible.

- **Garbage collection amélioré (GC)** &ndash; Les améliorations de GC dans art peuvent également améliorer les performances de l’application. Le garbage collection utilise désormais une seule pause GC au lieu de deux, et les opérations GC simultanées se terminent plus tard.

- **Amélioration** du débogage d’applications &ndash; Art fournit plus de détails de diagnostic pour faciliter l’analyse des exceptions et des rapports d’incidents.

Les applications existantes doivent fonctionner sans modification sous &ndash; art, à l’exception des applications qui exploitent les techniques propres au runtime Dalvik précédent, qui peuvent ne pas fonctionner sous art. Pour plus d’informations sur ces modifications, consultez [vérification du comportement de l’application sur le runtime Android (art)](https://developer.android.com/guide/practices/verifying-apps-art.html).


### <a name="notification-changes"></a>Modifications de notification

Les notifications ont été considérablement modifiées dans Android 5,0:

- **Les sons et les vibrations sont gérés différemment** `Ringtone` `Notification.Builder` `Vibrator` `MediaPlayer`Les sons et les vibrations des notifications sont désormais gérés par au lieu de, et. &ndash;

- **Nouveau modèle de couleurs** &ndash; Conformément au thème du matériau, les notifications sont rendues avec du texte foncé sur les arrière-plans blancs ou très clairs. En outre, les canaux alpha dans les icônes de notification peuvent être modifiés par Android pour coordonner les jeux de couleurs système. 

- **Notifications l’écran** &ndash; Les notifications peuvent désormais apparaître sur le l’écran d’appareil.

- **Tetes** &ndash; Les notifications de haute priorité s’affichent désormais dans une petite fenêtre flottante (notification de tête vers le haut) lorsque l’appareil est déverrouillé et que l’écran est activé.

Dans la plupart des cas, le portage de la fonctionnalité de notification d’application existante vers Android 5,0 requiert les étapes suivantes:

1. Convertissez votre code pour `Notification.Builder` utiliser ( `NotificationsCompat.Builder`ou) pour créer des notifications. 

2. Vérifiez que vos ressources de notification existantes sont visibles dans le nouveau modèle de couleurs de thème de matériau.

3. Déterminez la visibilité de vos notifications lorsqu’elles sont présentées sur le l’écran. Si une notification n’est pas publique, quel contenu doit s’afficher sur le l’écran?

4. Définissez la catégorie de vos notifications afin qu’elles soient gérées correctement dans le nouveau mode de dérangement Android 5,0.

Si vos notifications présentent des contrôles de transport, affichez l' `RemoteControlClient`état de lecture `ActivityManager.GetRecentTasks`du média, utilisez ou appelez, consultez [importantes modifications du comportement](https://developer.android.com/preview/api-overview.html#Behaviors) pour plus d’informations sur la mise à jour de vos notifications pour Android 5,0.

Pour plus d’informations sur la création de notifications dans Android, consultez [notifications locales](~/android/app-fundamentals/notifications/local-notifications.md).

## <a name="material-theme"></a>Thème matériau

Le nouveau thème de matériel Android 5,0 apporte des modifications de balayage à l’apparence de l’interface utilisateur Android. Les éléments visuels utilisent désormais des surfaces tactiles qui prennent le gras, la typographie et les couleurs brillantes de la conception basée sur l’impression. Les captures d’écran suivantes décrivent des exemples de thèmes de matériau:

[![Captures d’écran de l’écran d’accueil de thème de matériau, écran d’applications et écran de paramétrage](lollipop-images/android-5-gallery-labeled-sml.png)](lollipop-images/android-5-gallery-labeled.png#lightbox)

Android 5,0 vous accueille avec l’écran d’accueil affiché sur la gauche. La capture d’écran centrale est le premier écran de la liste des applications, et la capture d’écran à droite est l’écran **paramètres** . La spécification de la [conception de matériel](https://material.io/guidelines/material-design/introduction.html) de Google explique les règles de conception sous-jacentes derrière le nouveau concept de thème de matériau.

Le thème matériel comprend trois versions intégrées que vous pouvez utiliser dans votre application: le `Theme.Material` thème sombre (par défaut), le `Theme.Material.Light` thème et le `Theme.Material.Light.DarkActionBar` thème: 

[![Captures d’écran des thèmes sombres, clairs et DarkActionBar](lollipop-images/three-material-themes-sml.png)](lollipop-images/three-material-themes.png#lightbox)

Pour plus d’informations sur l’utilisation des fonctionnalités de thème matériel dans les applications Xamarin. Android, consultez [thème matériel](~/android/user-interface/material-theme.md).


## <a name="animations"></a>Animations

Android 5,0 fournit des animations de commentaires tactiles, des animations de transition d’activité et des animations de transition d’état d’affichage pour rendre les interfaces d’application plus intuitives à utiliser. En outre, les applications Android 5,0 peuvent utiliser des animations d' *effet de révélation* pour masquer ou afficher les affichages. Vous pouvez utiliser les paramètres de *mouvement courbé* pour configurer le rendu des animations rapides ou lentes.


### <a name="touch-feedback-animations"></a>Animations de commentaires tactiles

Les animations de commentaires tactiles fournissent aux utilisateurs des commentaires visuels lorsqu’une vue a été touchée. Par exemple, les boutons affichent désormais un effet d’ondulation lorsqu' &ndash; ils sont touchés. il s’agit de l’animation de commentaires tactiles par défaut dans Android 5,0. L’animation d’ondulation est implémentée `RippleDrawable` par la nouvelle classe. L’effet de l’ondulation peut être configuré pour se terminer aux limites de la vue ou s’étendre au-delà des limites de la vue. Par exemple, la séquence de captures d’écran suivante illustre l’effet de l’ondulation dans un bouton pendant l’animation tactile:

![Captures d’écran image par image de l’animation ondulation sur un bouton](lollipop-images/touch-animation.png)

Le contact tactile initial avec le bouton se trouve dans la première image sur la gauche, tandis que la séquence restante (de gauche à droite) montre comment l’effet d’ondulation se propage au bord du bouton. Lorsque l’animation ondulation se termine, la vue retrouve son apparence d’origine. L’animation d’ondulation par défaut a lieu en fractions de seconde, mais la longueur de l’animation peut être personnalisée pour une durée plus longue ou plus longue.

Pour plus d’informations sur les animations de commentaires tactiles dans Android 5,0, consultez [personnaliser les commentaires tactiles](https://developer.android.com/training/material/animations.html#Touch).


### <a name="activity-transition-animations"></a>Animations de transition d’activité

Les animations de transition d’activité offrent aux utilisateurs un sens de la continuité visuelle lorsqu’une activité passe à une autre. Les applications peuvent spécifier trois types d’animations de transition:

- **Entrer une transition** &ndash; Quand une activité entre dans la scène.

- **Quitter la transition** &ndash; Quand une activité quitte la scène.

- **Transition d’élément partagé** &ndash; Quand une vue commune à deux activités change au fur et à mesure que la première activité passe à la suivante.

Par exemple, la séquence de captures d’écran suivante illustre une transition d’élément partagé:

[![Captures d’écran Frame by Frame d’une animation de transition d’élément partagé](lollipop-images/activity-transition-sml.png)](lollipop-images/activity-transition.png#lightbox)

Un élément partagé (une photo d’un Caterpillar) est l’un des nombreux affichages de la première activité; elle s’agrandit pour devenir la seule vue de la deuxième activité lorsque la première activité passe à la seconde.

#### <a name="enter-transition-animation-types"></a>Entrer les types d’animation de transition

Pour les transitions entre entrées, Android 5,0 fournit trois types d’animation:

- **Éclater l’animation** &ndash; Agrandit une vue à partir du centre de la scène.

- **Animation** de diapositives &ndash; Déplace une vue à partir de l’un des bords d’une scène.

- **Animation de fondu** &ndash; Atténue une vue dans la scène.

#### <a name="exit-transition-animation-types"></a>Types d’animation de transition de sortie

Pour les transitions de sortie, Android 5,0 fournit trois types d’animation:

- **Éclater l’animation** &ndash; Réduit une vue au centre de la scène.

- **Animation** de diapositives &ndash; Déplace une vue vers l’un des bords d’une scène.

- **Animation de fondu** &ndash; Atténue une vue de la scène.

#### <a name="shared-element-transition-animation-types"></a>Types d’animation de transition d’élément partagé

Les transitions d’éléments partagés prennent en charge plusieurs types d’animations, par exemple:

- Modification de la disposition ou des limites d’une vue.

- Modification de l’échelle et de la rotation d’une vue.

- Modification de la taille et du type de mise à l’échelle d’une vue.

Pour plus d’informations sur les animations de transition d’activité dans Android 5,0, consultez personnaliser les transitions d' [activité](https://developer.android.com/training/material/animations.html#Transitions).


### <a name="view-state-transition-animations"></a>Animations de transition d’état d’affichage

Android 5,0 permet aux animations de s’exécuter lorsque l’état d’une vue change. Vous pouvez animer des transitions d’état d’affichage à l’aide de l’une des techniques suivantes:

- Créer des drawables qui animent les modifications d’état associées à une vue particulière. La nouvelle `AnimatedStateListDrawable` classe vous permet de créer des drawables qui affichent des animations entre les modifications d’état d’affichage.

- Définir la fonctionnalité d’animation qui s’exécute lorsque l’état d’une vue change. La nouvelle `StateListAnimator` classe vous permet de définir un animateur qui s’exécute lorsque l’état d’une vue change.

Pour plus d’informations sur les animations de transition d’état d’affichage dans Android 5,0, consultez animer des [modifications d’état d’affichage](https://developer.android.com/training/material/animations.html#ViewState).


### <a name="reveal-effect"></a>Effet de révélation

L' *effet d’affichage* est un cercle de découpage qui modifie RADIUS pour afficher ou masquer une vue. Vous pouvez contrôler cet effet en définissant le rayon initial et le rayon final du cercle de découpage. La séquence de captures d’écran suivante illustre une animation d’effet de révélation à partir du centre de l’écran:

[![Captures d’écran image par image de révéler l’animation](lollipop-images/reveal-center-sml.png)](lollipop-images/reveal-center.png#lightbox)

La séquence suivante illustre une animation d’effet d’affichage qui se produit à partir de l’angle inférieur gauche de l’écran:

[![Captures d’écran Frame by frame de l’animation de découpage](lollipop-images/reveal-left-sml.png)](lollipop-images/reveal-left.png#lightbox)

Les animations de révélation peuvent être inversées; autrement dit, le cercle de découpage peut être réduit pour masquer la vue plutôt que l’agrandir pour la faire apparaître.

Pour plus d’informations sur l’effet d’affichage Android 5,0 dans, consultez [utiliser l’effet de révélation](https://developer.android.com/training/material/animations.html#Reveal).


### <a name="curved-motion"></a>Mouvement courbé

Outre ces fonctionnalités d’animation, Android 5,0 fournit également de nouvelles API qui vous permettent de spécifier les courbes de temps et de mouvement des animations. Android 5,0 utilise ces courbes pour interpoler le mouvement temporel et spatial pendant les animations. Trois courbes sont définies dans Android 5,0:

- **La\_sortielinéaire\_rapide\_dans** accélèrerapidementetcontinued’accélérerjusqu’à&ndash; la fin de l’animation.

- **La vitesse\_rapidelente\_dans accélère rapidement et lentement les décélérations vers la fin de l’animation.\_** &ndash;

- **\_Leralentissement\_despicsde charge dans commence par une rapidité maximale et ralentit lentement jusqu’à la fin de l’animation.\_** &ndash;

Vous pouvez utiliser la nouvelle `PathInterpolator` classe pour spécifier la façon dont l’interpolation de mouvement a lieu. `PathInterpolator`est un interpolateur qui traverse les chemins d’animation selon les points de contrôle et les courbes de mouvement spécifiés. Pour plus d’informations sur la spécification des paramètres de mouvement courbé dans Android 5,0, consultez [utiliser un mouvement courbé](https://developer.android.com/training/material/animations.html#CurvedMotion).


## <a name="view-shadows--elevation"></a>Afficher les ombres & l’élévation

Dans Android 5,0, vous pouvez spécifier l' *élévation* d’une vue en définissant une `Z` nouvelle propriété. Une valeur `Z` supérieure fait en sorte que la vue effectue un cast d’une ombre plus grande en arrière-plan, ce qui fait apparaître la vue en flotter au-dessus de l’arrière-plan. Vous pouvez définir l’élévation initiale d’une vue en configurant son `elevation` attribut dans la disposition.

L’exemple suivant illustre les ombres converties par `TextView` un contrôle vide quand son attribut Elevation a la valeur 2DP, 4DP et 6DP, respectivement:

[![Captures d’écran des ombres de vue plus grandes progessively](lollipop-images/view-shadows-sml.png)](lollipop-images/view-shadows.png#lightbox)

Les paramètres d’affichage des ombres peuvent être statiques (comme indiqué ci-dessus). ils peuvent aussi être utilisés dans des animations pour faire apparaître un affichage qui augmente temporairement au-dessus de l’arrière-plan de la vue. Vous pouvez utiliser la `ViewPropertyAnimator` classe pour animer l’élévation d’une vue. L’élévation d’une vue est la somme de son paramètre `elevation` de disposition plus `translationZ` une propriété que vous pouvez définir à `ViewPropertyAnimator` l’aide d’un appel de méthode.

Pour plus d’informations sur les ombres de vue dans Android 5,0, consultez [définition d’ombres et de vues](https://developer.android.com/training/material/shadows-clipping.html)de découpage.


## <a name="color-features"></a>Fonctionnalités de couleur

Android 5,0 fournit deux nouvelles fonctionnalités pour la gestion des couleurs dans les applications:

- La *coloration dessinable* vous permet de modifier les couleurs des ressources d’image en modifiant un attribut de disposition.

- L' *extraction de couleurs visible* vous permet de personnaliser dynamiquement le thème de couleur de votre application pour qu’elle soit coordonnée avec la palette de couleurs d’une image affichée.


### <a name="drawable-tinting"></a>Teinte dessinable

Les dispositions Android 5,0 reconnaissent un nouvel `tint` attribut que vous pouvez utiliser pour définir la couleur de drawables sans avoir à créer plusieurs versions de ces ressources pour afficher des couleurs différentes. Pour utiliser cette fonctionnalité, vous définissez une bitmap en tant que masque Alpha et vous `tint` utilisez l’attribut pour définir la couleur de l’élément multimédia. Cela vous permet de créer des ressources une fois et de les colorer dans votre disposition pour les faire correspondre à votre thème.

Dans l’exemple suivant, une seule image &ndash; un logo blanc avec un arrière-plan &ndash; transparent est utilisé pour créer des variations de teinte:

![Logo Xamarin blanc avec arrière-plan transparent](lollipop-images/xamarin-logo-white.png)

Ce logo s’affiche au-dessus d’un arrière-plan circulaire bleu, comme indiqué dans les exemples suivants. L’image à gauche correspond à la façon dont le logo apparaît `tint` sans paramètre. Dans l’image centrale, l’attribut du `tint` logo est défini sur un gris foncé. Dans l’image à droite, `tint` est défini sur un gris clair:

![Exemples du logo ci-dessus avec des paramètres de teinte différents](lollipop-images/drawable-tinting.png)

Pour plus d’informations sur les teintes dessinables dans Android 5,0, consultez [teintes dessinables](https://developer.android.com/training/material/drawables.html#DrawableTint).


### <a name="prominent-color-extraction"></a>Extraction de couleurs visible

La nouvelle classe Android `Palette` 5,0 vous permet d’extraire des couleurs d’une image afin de les appliquer dynamiquement à une palette de couleurs personnalisée. La `Palette` classe extrait six couleurs d’une image et étiquette ces couleurs en fonction de leurs niveaux relatifs de saturation et de luminosité des couleurs:

- Superbe

- Vif foncé

- Lumière éclatante

- Activé

- Noir muet

- Clair muet

Par exemple, dans les captures d’écran suivantes, une application d’affichage de photos extrait les couleurs visibles de l’image à l’écran et utilise ces couleurs pour adapter le modèle de couleurs de l’application en fonction de l’image:

[![Captures d’écran des extractions de couleurs de thème vertes, roses et bleues](lollipop-images/prominent-color-extraction-sml.png)](lollipop-images/prominent-color-extraction.png#lightbox)

Dans les captures d’écran ci-dessus, la barre d’action est définie sur la couleur «vif Light» extraite et l’arrière-plan est défini sur la couleur «vif Dark» extraite. Dans chaque exemple ci-dessus, une ligne de petits carrés de couleur est incluse pour illustrer les couleurs de la palette qui ont été extraites de l’image.

Pour plus d’informations sur l’extraction de couleurs dans Android 5,0, consultez [extraction des couleurs visibles d’une image](https://developer.android.com/training/material/drawables.html#ColorExtract).


## <a name="new-ui-widgets"></a>Nouveaux widgets d’interface utilisateur

Android 5,0 introduit deux nouveaux widgets d’interface utilisateur:

- `RecyclerView`&ndash; Un groupe d’affichage qui affiche une liste d’éléments déroulants.

- `CardView`&ndash; Disposition de base avec des angles arrondis.

Les deux widgets incluent une prise en charge intégrée des fonctionnalités de thème matériel; par exemple, `RecyclerView` utilise des animations pour ajouter et supprimer des vues `CardView` , et utilise afficher les ombres pour que chaque carte apparaisse flotter au-dessus de l’arrière-plan. Des exemples de ces nouveaux widgets sont présentés dans les captures d’écran suivantes:

[![Captures d’écran des applications générées avec RecyclerView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

La capture d’écran de gauche est un exemple `RecyclerView` de tel qu’il est utilisé dans une application de messagerie, et la capture d’écran `CardView` sur la droite est un exemple de tel qu’il est utilisé dans une application de réservation de voyage.


### <a name="recyclerview"></a>RecyclerView

`RecyclerView`est semblable à `ListView,` , mais il est mieux adapté aux grands ensembles de vues ou de listes avec des éléments qui changent de manière dynamique. Comme `ListView,` vous spécifiez un adaptateur pour accéder au jeu de données sous-jacent. Toutefois, contrairement `ListView,` à l’utilisation d’un *Gestionnaire de disposition* pour `RecyclerView`positionner des éléments dans. Le gestionnaire de disposition prend également en charge le recyclage de vue; Il gère la réutilisation des vues d’éléments qui ne sont plus visibles pour l’utilisateur.

Lorsque vous utilisez un `RecyclerView` widget, vous devez spécifier un `LayoutManager` et un adaptateur. Comme illustré dans cette figure, `LayoutManager` est l’intermédiaire entre l’adaptateur `RecyclerView`et:

![Diagramme de RecyclerView avec prise en charge de LayoutManager, d’adaptateur et de jeu de données](lollipop-images/recyclerview-diagram.png)

Les captures `ImageView` d’écran suivantes `RecyclerView` illustrent un qui contient 100 éléments (chaque élément se compose `TextView`d’un et d’un):

[![Captures d’écran d’une application RecyclerView faisant défiler les images](lollipop-images/recyclerview-scroll-sml.png)](lollipop-images/recyclerview-scroll.png#lightbox)

`RecyclerView`gère ce jeu de données volumineux et &ndash; le défilement facile du début de la liste jusqu’à la fin de la liste dans cet exemple d’application ne prend que quelques secondes. `RecyclerView`prend également en charge les animations. en fait, les animations permettant d’ajouter et de supprimer des éléments sont activées par défaut. Lorsqu’un élément est ajouté à un `RecyclerView`, il apparaît en fondu comme indiqué dans cette séquence de captures d’écran:

[![Capture d’écran Frame by Frame d’un élément photo en fondu](lollipop-images/recyclerview-animation-sml.png)](lollipop-images/recyclerview-animation.png#lightbox)

Pour plus d' `RecyclerView`informations, consultez [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).


### <a name="cardview"></a>CardView

`CardView`est une vue simple qui simule une carte flottante avec des angles arrondis. Étant `CardView` donné que contient des ombres de vue intégrées, il vous offre un moyen simple d’ajouter une profondeur visuelle à votre application. Les captures d’écran suivantes montrent trois exemples orientés `CardView`texte de:

[![Exemples de captures d’écran d’applications utilisant RecyclerView avec des éléments basés sur CardView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

Chacune des cartes de l’exemple ci-dessus contient `TextView`un; la couleur d’arrière-plan `cardBackgroundColor` est définie via l’attribut.

Pour plus d' `CardView`informations sur, consultez [CardView](~/android/user-interface/controls/card-view.md).


## <a name="enhanced-notifications"></a>Notifications améliorées

Le système de notification d’Android 5,0 a été considérablement mis à jour avec un nouveau format visuel et de nouvelles fonctionnalités. Les notifications ont une nouvelle apparence dans Android 5,0. Par exemple, les notifications dans Android 5,0 utilisent désormais du texte foncé sur un arrière-plan clair:

![Exemple de notification Android 5,0 non développée](lollipop-images/expanded-notification-contracted.png)

Quand une grande icône s’affiche dans une notification (comme indiqué dans l’exemple ci-dessus), Android 5,0 présente la petite icône sous la forme d’un badge sur la grande icône. 

Dans Android 5,0, les notifications peuvent également apparaître sur le l’écran d’appareil.
Par exemple, voici un exemple de capture d’écran d’un l’écran avec une seule notification:

[![Capture d’écran de la notification apparaissant sur l’écran de verrouillage](lollipop-images/lockscreen-notification-sml.png)](lollipop-images/lockscreen-notification.png#lightbox)

Les utilisateurs peuvent double-cliquer sur une notification sur le l’écran pour déverrouiller l’appareil et accéder à l’application à l’origine de cette notification, ou faire glisser pour fermer la notification. Les notifications ont un nouveau paramètre de *visibilité* qui détermine la quantité de contenu pouvant être affichée sur le l’écran. Les utilisateurs peuvent choisir d’autoriser ou non le contenu sensible à être affiché dans les notifications l’écran.

Android 5,0 introduit un nouveau format de présentation de notification de haute priorité appelé *TETE-up*. Les notifications de niveau supérieur s’affichent en haut de l’écran pendant quelques secondes, puis revenez à la nuance de notification en haut de l’écran. Les notifications par tête permettent à l’interface utilisateur du système de placer des informations importantes devant l’utilisateur sans interrompre l’activité en cours. L’exemple suivant illustre une simple notification d’en-tête qui s’affiche au-dessus d’une application:

[![Exemple de notification de tête](lollipop-images/heads-up-notification-sml.png)](lollipop-images/heads-up-notification.png#lightbox)

Les notifications par tête sont généralement utilisées pour les événements suivants:

- Nouveau message suivant

- Un appel téléphonique entrant

- Indication de batterie faible

- Une alarme

Android 5,0 affiche une notification au format en tête uniquement lorsqu’il a un paramètre de priorité élevée ou maximale.

Dans Android 5,0, vous pouvez fournir des métadonnées de notification pour aider Android à trier et à afficher des notifications plus intelligemment. Android 5,0 organise les notifications en fonction de la priorité, de la visibilité et de la catégorie.
Les catégories de notification sont utilisées pour filtrer les notifications qui peuvent être présentées lorsque l’appareil est en mode *ne pas* déranger.

Pour plus d’informations sur la création et le lancement de notifications avec les dernières fonctionnalités Android 5,0, consultez [notifications locales](~/android/app-fundamentals/notifications/local-notifications.md).


## <a name="new-apis"></a>Nouvelles API

Outre les nouvelles fonctionnalités de présentation décrites ci-dessus, Android 5,0 ajoute de nouvelles API qui étendent les fonctionnalités des fonctionnalités de connectivité, de stockage et sans fil existantes. En outre, Android 5,0 comprend de nouvelles API qui prennent en charge une nouvelle fonctionnalité de planificateur de travaux.

### <a name="camera"></a>Appareil photo

Android 5,0 fournit plusieurs nouvelles API pour les capacités améliorées de l’appareil photo. Le nouvel `Android.Hardware.Camera2` espace de noms comprend des fonctionnalités permettant d’accéder à des appareils photo individuels connectés à un appareil Android. En outre `Android.Hardware.Camera2` , modélise chaque appareil photo comme un pipeline: il accepte une demande de capture, capture l’image, puis génère le résultat. Cette approche permet aux applications d’effectuer la file d’attente de plusieurs demandes de capture sur un appareil photo.

Les API suivantes rendent ces nouvelles fonctionnalités possibles:

- `CameraManager.GetCameraIdList`Vous permet d’accéder par programmation à des appareils de l’appareil `CameraManager.OpenCamera` photo; vous utilisez pour vous connecter à un appareil photo spécifique. &ndash;

- `CameraCaptureSession`&ndash; Capture ou diffuse des images à partir de l’appareil photo. Vous implémentez `CameraCaptureSession.CaptureListener` une interface pour gérer de nouveaux événements de capture d’image.

- `CaptureRequest`&ndash; Définit des paramètres de capture.

- `CaptureResult`&ndash; Fournit les résultats d’une opération de capture d’image.

Pour plus d’informations sur les nouvelles API d’appareil photo dans Android 5,0, consultez [média](https://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="audio-playback"></a>Lecture audio

Android 5,0 met à jour la `AudioTrack` classe pour une meilleure lecture audio:

- `ENCODING_PCM_FLOAT`&ndash; Configurepouraccepterlesdonnéesaudioauformatàvirguleflottantepourunemeilleureplagedynamique,uneplusgrandemargeetunemeilleurequalité(grâceàune`AudioTrack` précision accrue). En outre, le format à virgule flottante permet d’éviter le découpage audio.

- `ByteBuffer`Vous pouvez désormais fournir des données audio `AudioTrack` à en tant que tableau d’octets. &ndash;

- `WRITE_NON_BLOCKING`&ndash; Cette option simplifie la mise en mémoire tampon et le multithreading pour certaines applications.

Pour plus d' `AudioTrack` informations sur les améliorations apportées à Android 5,0, consultez [média](https://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="media-playback-control"></a>Contrôle de lecture de média

Android 5,0 introduit la nouvelle `Android.Media.MediaController` classe, qui remplace `RemoteControlClient`. `Android.Media.MediaController`fournit des API de contrôle de transport simplifiées et offre un contrôle de lecture thread-safe en dehors du contexte de l’interface utilisateur. Les nouvelles API suivantes gèrent le contrôle de transport:

- `Android.Media.Session.MediaSession`&ndash; Session de contrôle multimédia qui gère plusieurs contrôleurs. Vous appelez `MediaSession.GetSessionToken` pour demander un jeton que votre application utilise pour interagir avec la session.

- `MediaController.TransportControls`Gère les commandes de transport telles que **Play**, **Stop**et **Skip.** &ndash;

En outre, vous pouvez utiliser la `Android.App.Notification.MediaStyle` nouvelle classe pour associer une session multimédia à du contenu de notification riche (par exemple, l’extraction et l’indication d’une pochette d’album).

Pour plus d’informations sur les nouvelles fonctionnalités de contrôle de lecture de média dans Android 5,0, consultez [média](https://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="storage"></a>Stockage

Android 5,0 met à jour l’infrastructure d’accès au stockage pour faciliter l’utilisation des répertoires et des documents par les applications:

- Pour sélectionner une sous-arborescence de répertoires, vous pouvez générer `Android.Intent.Action.OPEN_DOCUMENT_TREE` et envoyer une intention. Cette intention amène le système à afficher toutes les instances de fournisseur qui prennent en charge la sélection de sous-arborescences; l’utilisateur parcourt et sélectionne ensuite un répertoire.

- Pour créer et gérer de nouveaux documents ou répertoires n’importe où sous une sous-arborescence `CreateDocument`, `RenameDocument`utilisez les nouvelles méthodes `DocumentsContract`, et `DeleteDocument` de.

- Pour récupérer les chemins d’accès aux répertoires multimédias sur tous les périphériques de `Android.Content.Context.GetExternalMediaDirs` stockage partagés, vous devez appeler la nouvelle méthode.

Pour plus d’informations sur les nouvelles API de stockage dans Android 5,0, consultez [stockage](https://developer.android.com/preview/api-overview.html#Storage).

### <a name="wireless--connectivity"></a>Connectivité & sans fil

Android 5,0 ajoute les améliorations d’API suivantes pour la connectivité et les réseaux sans fil:

- Nouvelles API à *plusieurs réseaux* qui permettent aux applications de rechercher et de sélectionner des réseaux avec des fonctionnalités spécifiques avant d’établir une connexion.

- Fonctionnalité de diffusion Bluetooth qui permet à un appareil Android 5,0 d’agir en tant que périphérique Bluetooth à faible consommation d’énergie.

- Améliorations NFC qui facilitent l’utilisation de la fonctionnalité de communication en champ proche pour le partage de données avec d’autres appareils.

Pour plus d’informations sur les nouvelles API sans fil et de connectivité dans Android 5,0, consultez [Wireless and Connectivity](https://developer.android.com/preview/api-overview.html#Wireless).

### <a name="job-scheduling"></a>Planification des travaux

Android 5,0 introduit une nouvelle `JobScheduler` API qui peut aider les utilisateurs à réduire la charge de la batterie en planifiant l’exécution de certaines tâches uniquement lorsque l’appareil est branché et en cours de chargement. Cette fonctionnalité du planificateur de tâches peut également être utilisée pour planifier l’exécution d’une tâche lorsque les conditions sont plus adaptées à cette tâche, par exemple le téléchargement d’un fichier volumineux lorsque l’appareil est connecté via un réseau Wi-Fi au lieu d’un réseau limité.

Pour plus d’informations sur les nouvelles API de planification des travaux dans Android 5,0, consultez [planification des travaux](https://developer.android.com/preview/api-overview.html#JobScheduler).

## <a name="summary"></a>Récapitulatif

Cet article a fourni une vue d’ensemble des nouvelles fonctionnalités importantes dans Android 5,0 pour les développeurs d’applications Xamarin. Android:

- Thème matériau

- Animations

- Afficher les ombres et l’élévation

- Caractéristiques de couleur, telles que la coloration de dessinable et l’extraction de couleurs visible

- Les nouveaux `RecyclerView` widgets et `CardView`

- Améliorations des notifications

- Nouvelles API pour l’appareil photo, la lecture audio, le contrôle multimédia, le stockage, la connectivité sans fil et la planification des travaux

Si vous débutez avec le développement Xamarin Android, lisez [configuration et installation](~/android/get-started/installation/index.md) pour vous aider à prendre en main Xamarin. Android.
[Hello, Android](~/android/get-started/hello-android/index.md) est une excellente introduction pour apprendre à créer des projets Android.



## <a name="related-links"></a>Liens associés

- [Aperçu des développeurs Android L](https://developer.android.com/preview/index.html)
- [Obtient le Android SDK](https://developer.android.com/sdk/index.html#Other)
- [Conception de matériau](https://developer.android.com/preview/material/index.html)
- [Principes de conception de matériau](http://static.googleusercontent.com/media/www.google.com/en/us/design/material-design.pdf)
