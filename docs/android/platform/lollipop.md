---
title: Caractéristiques de Lollipop
description: Cet article fournit un aperçu de haut niveau des nouvelles fonctionnalités introduites dans Android 5.0 (Lollipop). Ces fonctionnalités comprennent un nouveau style d’interface utilisateur appelé Material Theme, ainsi que de nouvelles fonctionnalités de soutien telles que des animations, des ombres de vue et des teintures dessinables. Android 5.0 comprend également des notifications améliorées, deux nouveaux widgets d’interface utilisateur, un nouveau planificateur d’emploi, et une poignée de nouvelles API pour améliorer le stockage, le réseautage, la connectivité et les capacités multimédias.
ms.prod: xamarin
ms.assetid: 1CE99CFE-FAAC-49FC-AEDC-1A21FC6E946E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 297c7806ce8a880d65c38ef0e4672e41fee5acfe
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291900"
---
# <a name="lollipop-features"></a>Caractéristiques de Lollipop

_Cet article fournit un aperçu de haut niveau des nouvelles fonctionnalités introduites dans Android 5.0 (Lollipop). Ces fonctionnalités comprennent un nouveau style d’interface utilisateur appelé Material Theme, ainsi que de nouvelles fonctionnalités de soutien telles que des animations, des ombres de vue et des teintures dessinables. Android 5.0 comprend également des notifications améliorées, deux nouveaux widgets d’interface utilisateur, un nouveau planificateur d’emploi, et une poignée de nouvelles API pour améliorer le stockage, le réseautage, la connectivité et les capacités multimédias._

## <a name="lollipop-overview"></a>Aperçu de Lollipop

Android 5.0 (Lollipop) introduit un nouveau langage de conception, *Material Design*, et avec elle un casting de soutien de nouvelles fonctionnalités pour rendre les applications plus faciles et plus intuitives à utiliser. Avec Material Design, Android 5.0 donne non seulement aux téléphones Android un lifting; il fournit également un nouvel ensemble de règles de conception pour les tablettes Android, ordinateurs de bureau, montres et téléviseurs intelligents. Ces règles de conception mettent l’accent sur la simplicité et le minimalisme tout en utilisant des attributs tactiles familiers (tels que des repères réalistes de surface et de bord) pour aider les utilisateurs à comprendre rapidement et intuitivement l’interface.

*Material Theme* est l’incarnation de ces principes de conception d’interface utilisateur dans Android. Cet article commence par couvrir les fonctionnalités de support de Material Theme :

- **Animations** &ndash; *Animations Touch feedback* animations, animations de transition *d’activité,* voir les animations *de transition d’état,* et un effet *révélateur.*

- **Afficher les ombres et les vues d’élévation** &ndash; ont maintenant une `elevation` propriété ;   vues avec `elevation` des valeurs plus élevées jettent de plus grandes ombres sur l’arrière-plan.

- **Caractéristiques de** &ndash; *couleur La teinture Drawable* vous permet de réutiliser les ressources d’image en changeant leur couleur, et *l’extraction de couleur proéminente* vous aide à thème dynamiquer votre application basée sur les couleurs d’une image.

De nombreuses fonctionnalités de thème matériel sont déjà intégrées dans l’expérience Android 5.0 interface utilisateur, tandis que d’autres doivent être explicitement ajoutés aux applications. Par exemple, certaines vues standard (comme les boutons) incluent déjà des animations de rétroaction tactile, tandis que les applications doivent activer la plupart des ombres de vue.

En plus des améliorations de l’interface utilisateur apportées par Material Theme, Android 5.0 comprend également plusieurs autres nouvelles fonctionnalités qui sont couvertes dans cet article:

- **Notifications améliorées** &ndash; Notifications dans Android 5.0 ont été considérablement mis à jour avec un nouveau look, la prise en charge des notifications d’écran de verrouillage, et un nouveau format de présentation de notification *Heads-up.*

- **Widgets UI** &ndash; Le `RecyclerView` nouveau widget permet aux applications de transmettre plus facilement `CardView` de grands ensembles de données et des informations complexes, et le nouveau widget fournit un format de présentation simplifié en forme de carte pour afficher du texte et des images.

- **Les nouvelles API** &ndash; Android 5.0 ajoutent de nouvelles API pour un soutien réseau multiple, une connectivité Bluetooth améliorée, une gestion de stockage plus facile et un contrôle plus flexible des lecteurs multimédias et des appareils photo. Une nouvelle fonction de planification d’emploi est disponible pour exécuter les tâches asynchronement aux heures prévues. Cette fonctionnalité permet d’améliorer la durée de vie de la batterie en planant, par exemple, les tâches à accomplir lorsque l’appareil est branché et chargé.

## <a name="requirements"></a>Spécifications

Ce qui suit est nécessaire pour utiliser les nouvelles fonctionnalités Android 5.0 dans les applications basées sur Xamarin:

- **Xamarin.Android** &ndash; Xamarin.Android 4.20 ou plus tard doit être installé et configuré avec Visual Studio ou Visual Studio pour Mac.

- **Android SDK** &ndash; Android 5.0 (API 21) ou plus tard doit être installé via le gestionnaire Android SDK.

- **Java Developer Kit** &ndash; Xamarin.Android nécessite [JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou plus tard si vous développez pour le niveau API 24 ou plus (JDK 1.8 prend également en charge les niveaux d’API plus tôt que 24, y compris Lollipop). La version 64 bits de JDK 1.8 est requise si vous utilisez des contrôles personnalisés ou l’aperçu des formulaires.

Vous pouvez continuer à utiliser [JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) si vous développez spécifiquement pour le niveau API 23 ou plus tôt.

## <a name="setting-up-an-android-50-project"></a>Mise en place d’un projet Android 5.0

Pour créer un projet Android 5.0, vous devez installer les derniers outils et forfaits SDK. Utilisez les étapes suivantes pour mettre en place un projet Xamarin.Android qui cible Android 5.0:

1. Installez des outils Xamarin.Android et activez votre licence Xamarin. Voir [Configuration et Installation](~/android/get-started/installation/index.md) pour plus d’informations sur l’installation de Xamarin.Android.

2. Si vous utilisez Visual Studio pour Mac, installez les dernières mises à jour Android 5.0.

3. Démarrez le gestionnaire Android SDK (en studio visuel pour Mac, utilisez **Tools &gt; Open Android&hellip;SDK Manager**) et installez Android SDK Tools 23.0.5 ou plus tard :

    [![Sélection d’outils Android SDK dans le gestionnaire Android SDK](lollipop-images/android-l-tools-sml.png)](lollipop-images/android-l-tools.png#lightbox)

   Aussi, installer les derniers paquets Android 5.0 SDK (API 21 ou plus tard):

    [![Installation de forfaits Android 5.0 SDK dans le gestionnaire Android SDK](lollipop-images/android-l-sdk-pkgs-sml.png)](lollipop-images/android-l-sdk-pkgs.png#lightbox)

   Pour plus d’informations sur l’utilisation de l’Android SDK Manager, voir [SDK Manager](https://developer.android.com/tools/help/sdk-manager.html).

4. Créez un nouveau projet Xamarin.Android. Si vous êtes nouveau au développement Android avec Xamarin, voir [Bonjour, Android](~/android/get-started/hello-android/index.md) pour en savoir plus sur la création de projets Android. Lorsque vous créez un projet Android, assurez-vous de configurer les paramètres de la version pour Android 5.0.
   Dans Visual Studio for Mac, naviguez vers **Project &gt; Options Build &gt; General** et définissez le cadre **cible** sur **Android 5.0 (Lollipop)** ou plus tard :

    ![Définir la cible Framwework sur Android 5.0 Lollipop](lollipop-images/target-framework.png)

   Dans le cadre **de Project Options &gt; Build &gt; Android Application**, définir la version Android minimale et cible à automatique - utiliser la version cadre **cible:**

    ![Définir les versions Android minimales et ciblées à l’automatique](lollipop-images/minimum-android-version.png)

5. Configurez un émulateur ou un appareil Android pour tester votre application. Si vous utilisez un émulateur, consultez [Android Emulator Setup](~/android/get-started/installation/android-emulator/index.md) pour savoir configurer un émulateur Android pour une utilisation avec Xamarin Studio ou Visual Studio. Si vous utilisez un appareil Android, voir [configurer le SDK Preview](https://developer.android.com/preview/setup-sdk.html) pour savoir comment mettre à jour votre appareil pour Android 5.0. Pour configurer votre appareil Android pour l’exécution et le débogage des applications Xamarin.Android, voir [Set Up Device for Development](~/android/get-started/installation/set-up-device-for-development.md).

Remarque : Si vous mettez à jour un projet Android existant qui visait l’Aperçu Android L, vous devez mettre à jour le **cadre cible** et la **version Android** des valeurs décrites ci-dessus.

## <a name="important-changes"></a>Changements importants

Les applications Android publiées précédemment pourraient être affectées par les changements dans Android 5.0. En particulier, Android 5.0 utilise un nouveau temps d’exécution et un format de notification considérablement modifié.

### <a name="android-runtime"></a>Android Runtime

Android 5.0 utilise le nouveau Android Runtime (ART) comme le temps d’exécution par défaut au lieu de Dalvik. ART met en œuvre plusieurs nouveautés majeures :

- La compilation &ndash; AOT à **l’avance (AOT)** peut améliorer les performances de l’application en compilant le code de l’application avant le lancement de l’application. Lorsqu’une application est installée, ART génère une application compilée exécutable pour l’appareil cible.

- **L’amélioration de la collecte des ordures (GC)** &ndash; des améliorations GC dans ART peut également améliorer les performances de l’application. La collecte des ordures utilise maintenant une pause GC au lieu de deux, et les opérations concurrentes de GC complètent plus rapidement.

- **L’amélioration de la multithérapie d’application** &ndash; fournit plus de détails diagnostiques pour aider à analyser les exceptions et les rapports d’accident.

Les applications existantes devraient &ndash; fonctionner sans changement dans le cadre de LA MULTI, sauf pour les applications qui exploitent des techniques propres au temps d’exécution dalvik précédent, qui peuvent ne pas fonctionner sous ART. Pour plus d’informations sur ces changements, voir [Vérifier le comportement de l’application sur l’Android Runtime (ART)](https://developer.android.com/guide/practices/verifying-apps-art.html).

### <a name="notification-changes"></a>Modifications de notification

Notifications ont changé de manière significative dans Android 5.0:

- **Les sons et les vibrations sont manipulés** &ndash; `Notification.Builder` différemment `Ringtone`Les `MediaPlayer`sons et les vibrations de notification sont maintenant manipulés par au lieu de , , et `Vibrator`.

- **Nouveau schéma de** &ndash; couleurs Conformément au thème matériel, les notifications sont rendues avec du texte foncé sur des arrière-plans blancs ou très légers. En outre, les canaux alpha dans les icônes de notification peuvent être modifiés par Android pour coordonner avec les schémas de couleurs du système.

- **Notifications** &ndash; d’écran de verrouillage Les notifications peuvent désormais apparaître sur l’écran de verrouillage de l’appareil.

- Les notifications **Heads-up** &ndash; Haute priorité apparaissent maintenant dans une petite fenêtre flottante (notification Heads-up) lorsque l’appareil est déverrouillé et que l’écran est activé.

Dans la plupart des cas, le portage des fonctionnalités de notification d’applications existantes sur Android 5.0 nécessite les étapes suivantes :

1. Convertissez votre `Notification.Builder` code `NotificationsCompat.Builder`pour l’utiliser (ou) pour créer des notifications.

2. Vérifiez que vos actifs de notification existants sont visibles dans le nouveau schéma de couleurs material Theme.

3. Décidez de la visibilité que vos notifications devraient avoir lorsqu’elles sont présentées sur l’écran de verrouillage. Si une notification n’est pas publique, quel contenu doit apparaître sur l’écran de verrouillage ?

4. Définissez la catégorie de vos notifications afin qu’elles soient traitées correctement dans le nouveau mode Android 5.0 *Ne pas déranger.*

Si vos notifications présentent des contrôles de `RemoteControlClient`transport, `ActivityManager.GetRecentTasks`affichent l’état de lecture des médias, utilisez ou appelez, consultez [des changements de comportement importants](https://developer.android.com/preview/api-overview.html#Behaviors) pour plus d’informations sur la mise à jour de vos notifications pour Android 5.0.

Pour plus d’informations sur la création de notifications dans Android, voir [Notifications locales](~/android/app-fundamentals/notifications/local-notifications.md).

## <a name="material-theme"></a>Thème matériau

Le nouveau thème de matériel Android 5.0 apporte des changements radicaux à l’apparence et la sensation de l’interface utilisateur Android. Les éléments visuels utilisent maintenant des surfaces tactiles qui prennent sur les graphismes audacieux, la typographie, et les couleurs vives de la conception basée sur l’impression. Des exemples de thème matériel sont représentés dans les captures d’écran suivantes :

[![Captures d’écran de l’écran d’accueil thème matériel, écran d’apps, et l’écran de réglage](lollipop-images/android-5-gallery-labeled-sml.png)](lollipop-images/android-5-gallery-labeled.png#lightbox)

Android 5.0 vous accueille avec l’écran d’accueil affiché sur la gauche. La capture d’écran centrale est le premier écran de la liste d’applications, et la capture d’écran sur la droite est **l’écran Paramètres.** La spécification [Material Design](https://material.io/guidelines/material-design/introduction.html) de Google explique les règles de conception sous-jacentes derrière le nouveau concept de thème matériel.

Material Theme comprend trois saveurs intégrées que vous `Theme.Material` pouvez utiliser dans votre `Theme.Material.Light` application : `Theme.Material.Light.DarkActionBar` le thème sombre (par défaut), le thème et le thème :

[![Captures d’écran des thèmes Dark, Light et DarkActionBar](lollipop-images/three-material-themes-sml.png)](lollipop-images/three-material-themes.png#lightbox)

Pour en savoir plus sur l’utilisation des fonctionnalités material Theme dans les applications Xamarin.Android, voir [Material Theme](~/android/user-interface/material-theme.md).

## <a name="animations"></a>Animations

Android 5.0 fournit des animations de rétroaction tactile, des animations de transition d’activité, et des animations de transition d’état pour rendre les interfaces d’application plus intuitives à utiliser. En outre, les applications Android 5.0 peuvent utiliser des animations *d’effet révélateur* pour masquer ou révéler des vues. Vous pouvez utiliser des paramètres *de mouvement incurvés* pour configurer la rapidité ou la lenteur des animations.

### <a name="touch-feedback-animations"></a>Animations De rétroaction tactile

Les animations de rétroaction tactile fournissent aux utilisateurs une rétroaction visuelle lorsqu’une vue a été touchée. Par exemple, les boutons affichent maintenant &ndash; un effet d’entraînement lorsqu’ils sont touchés, il s’agit de l’animation de rétroaction tactile par défaut dans Android 5.0. L’animation Ripple est `RippleDrawable` mise en œuvre par la nouvelle classe. L’effet d’entraînement peut être configuré pour se terminer aux limites de la vue ou s’étendre au-delà des limites de la vue. Par exemple, la séquence suivante de captures d’écran illustre l’effet d’entraînement d’un bouton lors d’une animation tactile :

![Captures d’écran image par image de l’animation ondulée sur un bouton](lollipop-images/touch-animation.png)

Le contact tactile initial avec le bouton se produit dans la première image sur la gauche, tandis que la séquence restante (de gauche à droite) illustre comment l’effet d’entraînement s’étend sur le bord du bouton. Lorsque l’animation ondulée se termine, la vue revient à son aspect d’origine. L’animation d’ondulation par défaut a lieu en une fraction de seconde, mais la durée de l’animation peut être personnalisée pour des durées plus ou moins longues.

Pour plus d’animations de rétroaction tactiles dans Android 5.0, voir [Customize Touch Feedback](https://developer.android.com/training/material/animations.html#Touch).

### <a name="activity-transition-animations"></a>Animations de transition d’activité

Les animations de transition d’activité donnent aux utilisateurs un sentiment de continuité visuelle lorsqu’une activité passe à une autre. Les applications peuvent spécifier trois types d’animations de transition :

- **Entrez la transition** &ndash; Pour quand une activité entre en scène.

- **Transition de sortie** &ndash; Pour quand une activité sort de la scène.

- &ndash; **Transition d’éléments partagés** Pour quand un point de vue commun à deux activités change au fur et à mesure que la première activité passe à l’autre.

Par exemple, la séquence suivante de captures d’écran illustre une transition d’élément partagé :

[![Captures d’écran image par image d’une animation de transition d’élément partagé](lollipop-images/activity-transition-sml.png)](lollipop-images/activity-transition.png#lightbox)

Un élément partagé (une photo d’une chenille) est l’un des nombreux points de vue de la première activité; il s’élargit pour devenir le seul point de vue dans la deuxième activité que la première activité passe à la seconde.

#### <a name="enter-transition-animation-types"></a>Entrez les types d’animation de transition

Pour entrer dans les transitions, Android 5.0 propose trois types d’animations :

- **L’animation d’explosion** &ndash; agrandit une vue depuis le centre de la scène.

- **L’animation** &ndash; de diapositive déplace une vue à partir de l’un des bords d’une scène.

- **Fade animation** &ndash; Fades une vue dans la scène.

#### <a name="exit-transition-animation-types"></a>Types d’animation de transition de sortie

Pour les transitions de sortie, Android 5.0 propose trois types d’animations :

- **L’animation d’explosion** &ndash; rétrécit une vue au centre de la scène.

- **L’animation** &ndash; de diapositives déplace une vue sur l’un des bords d’une scène.

- **Fade animation** &ndash; Fades une vue hors de la scène.

#### <a name="shared-element-transition-animation-types"></a>Types d’animation de transition d’éléments partagés

Les transitions d’éléments partagés prennent en charge plusieurs types d’animations, telles que :

- Modification de la mise en page ou des limites de clip d’une vue.

- Modification de l’échelle et de la rotation d’une vue.

- Modification de la taille et du type d’échelle pour une vue.

Pour en savoir plus sur les animations de transition d’activité dans Android 5.0, voir [Personnaliser les transitions d’activité](https://developer.android.com/training/material/animations.html#Transitions).

### <a name="view-state-transition-animations"></a>Afficher les animations de transition de l’État

Android 5.0 permet aux animations de s’exécuter lorsque l’état d’une vue change. Vous pouvez animer les transitions d’état en utilisant l’une des techniques suivantes :

- Créez des tirages qui animent les changements d’état associés à une vue particulière. La `AnimatedStateListDrawable` nouvelle classe vous permet de créer des tirages qui affichent des animations entre les changements d’état de vue.

- Définissez la fonctionnalité d’animation qui s’exécute lorsque l’état d’une vue change. La `StateListAnimator` nouvelle classe vous permet de définir un animateur qui s’exécute lorsque l’état d’une vue change.

Pour en savoir plus sur les animations de transition d’état de vue dans Android 5.0, voir [Animate View State Changes](https://developer.android.com/training/material/animations.html#ViewState).

### <a name="reveal-effect"></a>Effet de révélation

*L’effet révélateur* est un cercle de coupure qui change de rayon pour révéler ou masquer une vue. Vous pouvez contrôler cet effet en définissant le rayon initial et final du cercle de coupure. La séquence suivante de captures d’écran illustre une animation d’effet révélateur du centre de l’écran :

[![Captures d’écran image par image de l’animation de révélation](lollipop-images/reveal-center-sml.png)](lollipop-images/reveal-center.png#lightbox)

La séquence suivante illustre une animation d’effet révélateur qui se déroule à partir du coin inférieur gauche de l’écran :

[![Captures d’écran cadre par image de l’animation de coupure](lollipop-images/reveal-left-sml.png)](lollipop-images/reveal-left.png#lightbox)

Révéler les animations peuvent être inversées; c’est-à-dire que le cercle de coupure peut rétrécir pour cacher la vue plutôt que d’agrandir pour révéler la vue.

Pour plus d’informations sur l’Effet de révélation Android 5.0, voir [Utilisez l’effet Reveal](https://developer.android.com/training/material/animations.html#Reveal).

### <a name="curved-motion"></a>Mouvement courbé

En plus de ces fonctionnalités d’animation, Android 5.0 fournit également de nouvelles API qui vous permettent de spécifier les courbes de temps et de mouvement des animations. Android 5.0 utilise ces courbes pour interpoler le mouvement temporel et spatial pendant les animations. Trois courbes sont définies dans Android 5.0:

- **Rapidement\_\_linéaire\_dans** &ndash; Accélérer rapidement et continue à accélérer jusqu’à la fin de l’animation.

- **Rapide\_\_dans\_** &ndash; Accélère rapidement et lentement décélère vers la fin de l’animation.

- **Linéaire\_lentement\_\_dans** &ndash; Begins avec une vitesse maximale et décélère lentement à la fin de l’animation.

Vous pouvez utiliser `PathInterpolator` la nouvelle classe pour spécifier comment l’interpolation de mouvement a lieu. `PathInterpolator`est un interpolateur qui traverse les chemins d’animation selon les points de contrôle spécifiés et les courbes de mouvement. Pour plus d’informations sur la façon de spécifier les paramètres de mouvement incurvés dans Android 5.0, voir [Use Curved Motion](https://developer.android.com/training/material/animations.html#CurvedMotion).

## <a name="view-shadows--elevation"></a>Afficher les ombres & l’élévation

Dans Android 5.0, vous pouvez spécifier `Z` *l’élévation* d’une vue en définissant une nouvelle propriété. Une `Z` plus grande valeur provoque la vue de jeter une plus grande ombre sur l’arrière-plan, ce qui rend la vue semblent flotter plus haut au-dessus de l’arrière-plan. Vous pouvez définir l’élévation initiale d’une vue en configurant son `elevation` attribut dans la mise en page.

L’exemple suivant illustre les ombres `TextView` projetée par un contrôle vide lorsque son attribut d’élévation est réglé à 2dp, 4dp et 6dp, respectivement:

[![Captures d’écran d’ombres de vue progestativement plus grandes](lollipop-images/view-shadows-sml.png)](lollipop-images/view-shadows.png#lightbox)

Afficher les paramètres de l’ombre peut être statique (comme indiqué ci-dessus) ou ils peuvent être utilisés dans les animations pour faire une vue semblent s’élever temporairement au-dessus de l’arrière-plan de la vue. Vous pouvez `ViewPropertyAnimator` utiliser la classe pour animer l’élévation d’une vue. L’élévation d’une vue est `elevation` la `translationZ` somme de son réglage `ViewPropertyAnimator` de mise en page ainsi qu’une propriété que vous pouvez définir à travers un appel de méthode.

Pour en savoir plus sur les ombres vues dans Android 5.0, voir [Defining Shadows et Clipping Views](https://developer.android.com/training/material/shadows-clipping.html).

## <a name="color-features"></a>Caractéristiques de couleur

Android 5.0 fournit deux nouvelles fonctionnalités pour gérer la couleur dans les applications:

- *La teinture drawable* vous permet de modifier les couleurs des ressources d’image en modifiant un attribut de mise en page.

- *L’extraction de couleurs proéminente* vous permet de personnaliser dynamiquement le thème couleur de votre application pour coordonner avec la palette de couleurs d’une image affichée.

### <a name="drawable-tinting"></a>Drawable Tinting

Les mises en page Android 5.0 reconnaissent un nouvel `tint` attribut que vous pouvez utiliser pour définir la couleur des drawables sans avoir à créer plusieurs versions de ces actifs pour afficher différentes couleurs. Pour utiliser cette fonctionnalité, vous définissez un bitmap comme un masque alpha et utilisez l’attribut `tint` pour définir la couleur de l’actif. Cela vous permet de créer des actifs une fois et de les colorer dans votre mise en page pour correspondre à votre thème.

Dans l’exemple suivant, &ndash; un seul actif d’image d’un logo blanc avec un fond &ndash; transparent est utilisé pour créer des variations de teinte :

![Logo white Xamarin avec fond transparent](lollipop-images/xamarin-logo-white.png)

Ce logo est affiché au-dessus d’un fond circulaire bleu comme indiqué dans les exemples suivants. L’image à gauche est la `tint` façon dont le logo apparaît sans réglage. Dans l’image centrale, `tint` l’attribut du logo est réglé sur un gris foncé. Dans l’image de `tint` droite, est réglé à un gris clair:

![Exemples du logo ci-dessus avec différents paramètres de teinte](lollipop-images/drawable-tinting.png)

Pour en savoir plus sur la teinture drawable dans Android 5.0, voir [Drawable Tinting](https://developer.android.com/training/material/drawables.html#DrawableTint).

### <a name="prominent-color-extraction"></a>Extraction de couleur proéminente

La nouvelle classe Android `Palette` 5.0 vous permet d’extraire des couleurs d’une image afin que vous puissiez dynamiquement les appliquer à une palette de couleurs personnalisée. La `Palette` classe extrait six couleurs d’une image et étiquette ces couleurs en fonction de leurs niveaux relatifs de saturation des couleurs et de luminosité:

- Dynamique

- Vibrant foncé

- Lumière vibrante

- Coupé

- Sombre sourd

- Lumière tamisée

Par exemple, dans les captures d’écran suivantes, une application de visualisation de photos extrait les couleurs proéminentes de l’image sur l’écran et utilise ces couleurs pour adapter le schéma de couleur de l’application pour correspondre à l’image:

[![Captures d’écran des extractions de couleur de thème vert, rose et bleu](lollipop-images/prominent-color-extraction-sml.png)](lollipop-images/prominent-color-extraction.png#lightbox)

Dans les captures d’écran ci-dessus, la barre d’action est réglée à la couleur extraite "lumière vibrante" et l’arrière-plan est réglé sur la couleur extraite "vibrante foncée". Dans chaque exemple ci-dessus, une rangée de petits carrés de couleur est inclus pour illustrer les couleurs de palette qui ont été extraites de l’image.

Pour en savoir plus sur l’extraction de couleur dans Android 5.0, voir [Extraire les couleurs proéminentes d’une image](https://developer.android.com/training/material/drawables.html#ColorExtract).

## <a name="new-ui-widgets"></a>Nouveaux Widgets de l’assurance-chômage

Android 5.0 présente deux nouveaux widgets de l’interface utilisateur:

- `RecyclerView`&ndash; Un groupe de vue qui affiche une liste d’éléments défilementables.

- `CardView`&ndash; Une disposition de base avec des coins arrondis.

Les deux widgets comprennent un support de soutien pour les caractéristiques du thème matériel; par exemple, `RecyclerView` utilise des animations pour `CardView` ajouter et supprimer les vues, et utilise des ombres de vue pour faire chaque carte semble flotter au-dessus de l’arrière-plan. Des exemples de ces nouveaux widgets sont affichés dans les captures d’écran suivantes :

[![Captures d’écran d’applications construites avec RecyclerView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

La capture d’écran sur `RecyclerView` la gauche est un exemple de comme utilisé `CardView` dans une application de messagerie, et la capture d’écran sur la droite est un exemple de comme utilisé dans une application de réservation de voyage.

### <a name="recyclerview"></a>RecyclerView

`RecyclerView`est similaire `ListView,` à, mais il est mieux adapté pour de grands ensembles de vues ou de listes avec des éléments qui changent dynamiquement. Comme `ListView,` si vous spécifiez un adaptateur pour accéder à l’ensemble de données sous-jacent. Cependant, `ListView,` contrairement à vous utilisez un `RecyclerView`gestionnaire de mise en *page* pour positionner les éléments à l’intérieur . Le gestionnaire de la mise en page s’occupe également du recyclage de la vue; il gère la réutilisation des vues d’objets qui ne sont plus visibles par l’utilisateur.

Lorsque vous `RecyclerView` utilisez un widget, `LayoutManager` vous devez spécifier un adaptateur et un adaptateur. Comme le montre `LayoutManager` ce chiffre, est l’intermédiaire entre l’adaptateur et le `RecyclerView`:

![Diagramme de RecyclerView avec layoutManager, Adaptateur et Dataset](lollipop-images/recyclerview-diagram.png)

Les captures d’écran suivantes illustrent un `RecyclerView` qui contient `ImageView` 100 éléments (chaque élément se compose d’un et d’un `TextView`):

[![Captures d’écran d’une application RecyclerView défilant à travers les images](lollipop-images/recyclerview-scroll-sml.png)](lollipop-images/recyclerview-scroll.png#lightbox)

`RecyclerView`gère cet ensemble de &ndash; données volumineuses avec facilité défilement du début de la liste à la fin de la liste dans cette application d’échantillon ne prend que quelques secondes. `RecyclerView`prend également en charge les animations; en fait, les animations pour l’ajout et la suppression d’éléments sont activées par défaut. Quand un élément est `RecyclerView`ajouté à un , il s’estompe comme indiqué dans cette séquence de captures d’écran:

[![Capture d’écran image par image d’un élément photo s’estompe](lollipop-images/recyclerview-animation-sml.png)](lollipop-images/recyclerview-animation.png#lightbox)

Pour en `RecyclerView`savoir plus sur , voir [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).

### <a name="cardview"></a>CardView

`CardView`est une vue simple qui simule une carte flottante avec des coins arrondis. Parce `CardView` que les ombres de vue intégrées, il vous permet d’ajouter facilement de la profondeur visuelle à votre application. Les captures d’écran suivantes `CardView`montrent trois exemples textuels de :

[![Exemple de captures d’écran d’applications utilisant RecyclerView avec des éléments basés sur CardView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

Chacune des cartes dans l’exemple ci-dessus contient un `TextView`; la couleur de fond `cardBackgroundColor` est définie via l’attribut.

Pour en `CardView`savoir plus sur , voir [CardView](~/android/user-interface/controls/card-view.md).

## <a name="enhanced-notifications"></a>Notifications améliorées

Le système de notification dans Android 5.0 a été considérablement mis à jour avec un nouveau format visuel et de nouvelles fonctionnalités. Notifications ont un nouveau look dans Android 5.0. Par exemple, les notifications dans Android 5.0 utilisent maintenant du texte sombre sur un fond lumineux :

![Exemple d’une notification Android 5.0 non élargie](lollipop-images/expanded-notification-contracted.png)

Lorsqu’une grande icône est affichée dans une notification (comme indiqué dans l’exemple ci-dessus), Android 5.0 présente la petite icône comme un badge sur la grande icône.

Dans Android 5.0, les notifications peuvent également apparaître sur l’écran de verrouillage de l’appareil.
Par exemple, voici un exemple de capture d’écran d’un écran de verrouillage avec une seule notification :

[![Capture d’écran de la notification apparaissant sur l’écran de verrouillage](lollipop-images/lockscreen-notification-sml.png)](lollipop-images/lockscreen-notification.png#lightbox)

Les utilisateurs peuvent appuyer sur une notification sur l’écran de verrouillage pour déverrouiller l’appareil et sauter vers l’application à l’origine de cette notification, ou glisser pour rejeter la notification. Les notifications ont un nouveau *paramètre* de visibilité qui détermine la quantité de contenu qui peut être affichée sur l’écran de verrouillage. Les utilisateurs peuvent choisir d’autoriser ou non l’affiche de contenu sensible dans les notifications d’écran de verrouillage.

Android 5.0 introduit un nouveau format de présentation de notification haute priorité appelé *Heads-up*. Les notifications tête haute glissent vers le bas du haut de l’écran pendant quelques secondes, puis reculent vers l’ombre de notification en haut de l’écran. Les notifications de tête-à-tête permettent à l’interface utilisateur du système de mettre des informations importantes devant l’utilisateur sans perturber l’activité en cours d’exécution.
L’exemple suivant illustre une simple notification Heads-up qui s’affiche au-dessus d’une application :

[![Exemple d’une notification heads-up](lollipop-images/heads-up-notification-sml.png)](lollipop-images/heads-up-notification.png#lightbox)

Les notifications de tête-à-tête sont généralement utilisées pour les événements suivants :

- Un nouveau prochain message

- Un appel téléphonique entrant

- Faible indication de batterie

- Une alarme

Android 5.0 affiche une notification dans le format Heads-up seulement quand il a un réglage prioritaire élevé ou max.

Dans Android 5.0, vous pouvez fournir des métadonnées de notification pour aider Android à trier et afficher les notifications plus intelligemment. Android 5.0 organise les notifications en fonction de la priorité, de la visibilité et de la catégorie.
Les catégories de notification sont utilisées pour filtrer les notifications qui peuvent être présentées lorsque l’appareil est en mode *Ne pas déranger.*

Pour obtenir des informations détaillées sur la création et le lancement de notifications avec les dernières fonctionnalités Android 5.0, voir [Notifications locales](~/android/app-fundamentals/notifications/local-notifications.md).

## <a name="new-apis"></a>Nouvelles API

En plus des nouvelles fonctionnalités de look-and-feel décrites ci-dessus, Android 5.0 ajoute de nouvelles API qui étendent les capacités des fonctionnalités multimédia, de stockage et de connectivité sans fil existantes. En outre, Android 5.0 comprend de nouvelles API qui fournissent un soutien pour une nouvelle fonction de planificateur d’emploi.

### <a name="camera"></a>Caméra

Android 5.0 fournit plusieurs nouvelles API pour des capacités améliorées d’appareil photo. Le `Android.Hardware.Camera2` nouvel espace de nom comprend des fonctionnalités pour accéder à des appareils photo individuels connectés à un appareil Android. En `Android.Hardware.Camera2` outre, les modèles de chaque appareil photo comme un pipeline: il accepte une demande de capture, capture l’image, puis les sorties du résultat. Cette approche permet aux applications de faire la queue de plusieurs demandes de capture vers un appareil photo.

Les API suivantes rendent ces nouvelles fonctionnalités possibles :

- `CameraManager.GetCameraIdList`&ndash; Vous aide à accéder de manière programmatique aux appareils photo; que `CameraManager.OpenCamera` vous utilisez pour vous connecter à un appareil photo spécifique.

- `CameraCaptureSession`&ndash; Capture ou diffuse des images à partir de l’appareil photo. Vous implémentez une `CameraCaptureSession.CaptureListener` interface pour gérer les nouveaux événements de capture d’images.

- `CaptureRequest`&ndash; Définit les paramètres de capture.

- `CaptureResult`&ndash; Fournit les résultats d’une opération de capture d’image.

Pour en savoir plus sur les nouvelles API appareil photo dans Android 5.0, voir [Médias](https://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="audio-playback"></a>Lecture audio

Android 5.0 `AudioTrack` met à jour la classe pour une meilleure lecture audio:

- `ENCODING_PCM_FLOAT`&ndash; Configure `AudioTrack` pour accepter les données audio en format de point flottant pour une meilleure plage dynamique, une plus grande marge de manœuvre et une meilleure qualité (grâce à une précision accrue). En outre, le format de point flottant aide à éviter la coupure audio.

- `ByteBuffer`&ndash; Vous pouvez maintenant fournir `AudioTrack` des données audio en tant que tableau d’or.

- `WRITE_NON_BLOCKING`&ndash; Cette option simplifie la mise en mémoire tampon et la multilecture pour certaines applications.

Pour en `AudioTrack` savoir plus sur les améliorations dans Android 5.0, voir [Médias](https://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="media-playback-control"></a>Contrôle de lecture des médias

Android 5.0 introduit `Android.Media.MediaController` la nouvelle classe, qui remplace `RemoteControlClient`. `Android.Media.MediaController`fournit des API simplifiées de contrôle des transports et offre un contrôle sans fil de la lecture en dehors du contexte de l’interface utilisateur. Les nouvelles API suivantes gèrent le contrôle des transports :

- `Android.Media.Session.MediaSession`&ndash; Une séance de contrôle des médias qui gère plusieurs contrôleurs. Vous `MediaSession.GetSessionToken` appelez pour demander un jeton que votre application utilise pour interagir avec la session.

- `MediaController.TransportControls`&ndash; Gère les commandes de transport telles que **Play**, **Stop**, et **Skip**.

En outre, vous `Android.App.Notification.MediaStyle` pouvez utiliser la nouvelle classe pour associer une session multimédia avec du contenu de notification riche (comme l’extraction et la diffusion de l’art album).

Pour en savoir plus sur les fonctionnalités de contrôle de lecture des nouveaux médias dans Android 5.0, voir [Media](https://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="storage"></a>Stockage

Android 5.0 met à jour le cadre d’accès au stockage pour faciliter le fonctionnement des applications avec les répertoires et les documents :

- Pour sélectionner un sous-arbre d’annuaire, `Android.Intent.Action.OPEN_DOCUMENT_TREE` vous pouvez construire et envoyer une intention. Cette intention amène le système à afficher tous les instances du fournisseur qui prennent en charge la sélection de sous-arbres; l’utilisateur navigue ensuite et sélectionne un répertoire.

- Pour créer et gérer de nouveaux documents ou répertoires `CreateDocument`n’importe où sous un sous-arbre, vous utilisez le nouveau , `RenameDocument`, et `DeleteDocument` les méthodes de `DocumentsContract`.

- Pour obtenir des chemins vers les annuaires de `Android.Content.Context.GetExternalMediaDirs` médias sur tous les périphériques de stockage partagés, vous appelez la nouvelle méthode.

Pour en savoir plus sur les nouvelles API de stockage dans Android 5.0, voir [Stockage](https://developer.android.com/preview/api-overview.html#Storage).

### <a name="wireless--connectivity"></a>Connectivité & sans fil

Android 5.0 ajoute les améliorations suivantes de l’API pour le sans fil et la connectivité :

- De nouvelles API *multi-réseaux* qui permettent aux applications de trouver et de sélectionner des réseaux dotés de capacités spécifiques avant de faire une connexion.

- Fonctionnalité de diffusion Bluetooth qui permet à un appareil Android 5.0 d’agir comme un périphérique Bluetooth à faible énergie.

- Améliorations NFC qui facilitent l’utilisation de fonctionnalités de communication en champ proche pour le partage de données avec d’autres appareils.

Pour en savoir plus sur les nouvelles API sans fil et de connectivité dans Android 5.0, voir [Wireless and Connectivity](https://developer.android.com/preview/api-overview.html#Wireless).

### <a name="job-scheduling"></a>Planification des travaux

Android 5.0 introduit `JobScheduler` une nouvelle API qui peut aider les utilisateurs à minimiser la vidange de la batterie en planant certaines tâches pour fonctionner uniquement lorsque l’appareil est branché et chargé. Cette fonction de planificateur d’emploi peut également être utilisée pour la planification d’une tâche à exécuter lorsque les conditions sont plus adaptées à cette tâche, comme le téléchargement d’un grand fichier lorsque l’appareil est connecté sur un réseau Wi-Fi au lieu d’un réseau mesuré.

Pour en savoir plus sur les nouvelles API de planification d’emploi dans Android 5.0, voir [Scheduling Jobs](https://developer.android.com/preview/api-overview.html#JobScheduler).

## <a name="summary"></a>Récapitulatif

Cet article a fourni un aperçu des nouvelles fonctionnalités importantes dans Android 5.0 pour les développeurs d’applications Xamarin.Android:

- Thème matériau

- Animations

- Afficher les ombres et l’élévation

- Caractéristiques de couleur, telles que teinture dessinable et extraction de couleur proéminente

- Le `RecyclerView` nouveau `CardView` et les widgets

- Améliorations de notification

- Nouvelles API pour appareil photo, lecture audio, contrôle des médias, stockage, sans fil/connectivité et planification de l’emploi

Si vous êtes nouveau au développement Android Xamarin, lisez [Configuration et installation](~/android/get-started/installation/index.md) pour vous aider à démarrer avec Xamarin.Android.
[Bonjour, Android](~/android/get-started/hello-android/index.md) est une excellente introduction pour apprendre à créer des projets Android.

## <a name="related-links"></a>Liens connexes

- [Aperçu du développeur Android L](https://developer.android.com/preview/index.html)
- [Obtenez l’Android SDK](https://developer.android.com/sdk/index.html#Other)
- [Conception de matériaux](https://developer.android.com/preview/material/index.html)
