---
title: Introduction à Android Wear
description: Avec l’introduction de l’usure de Google, vous n’êtes plus limité aux téléphones et tablettes lorsqu’il s’agit de développer de superbes applications Android. La prise en charge d’Android par Xamarin. Android vous permet d’exécuter C# du code sur votre poignet ! Cette introduction fournit une vue d’ensemble de base de l’usure d’Android, décrit ses principales fonctionnalités et offre une vue d’ensemble des fonctionnalités disponibles dans Android usure 2,0. Il répertorie certains des appareils Android d’usure les plus populaires et fournit des liens vers la documentation de Google Android usure essentielle pour une meilleure lecture.
ms.prod: xamarin
ms.assetid: EAEF99F0-8FBE-47E4-8644-E7244CFAF464
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 3b1d27b1489cb71d4bd1922c2de993567ddf36bd
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028618"
---
# <a name="introduction-to-android-wear"></a>Introduction à Android Wear

_Avec l’introduction de l’usure de Google, vous n’êtes plus limité aux téléphones et tablettes lorsqu’il s’agit de développer de superbes applications Android. La prise en charge d’Android par Xamarin. Android vous permet d’exécuter C# du code sur votre poignet ! Cette introduction fournit une vue d’ensemble de base de l’usure d’Android, décrit ses principales fonctionnalités et offre une vue d’ensemble des fonctionnalités disponibles dans Android usure 2,0. Il répertorie certains des appareils Android d’usure les plus populaires et fournit des liens vers la documentation de Google Android usure essentielle pour une meilleure lecture._

## <a name="overview"></a>Vue d'ensemble

Android s’exécute sur un large éventail d’appareils, notamment la première génération de Motorola 360, le espion G de LG et le rapport Samsung en direct. Une deuxième génération, y compris la SmartWatch 3 de Sony, a également été publiée avec des fonctionnalités supplémentaires, notamment le GPS intégré et la lecture de musique hors connexion. Pour Android usure 2,0, Google s’est associé à LG pour deux nouvelles observations : the LG Watch Sports and the LG Watch style.

![Appareils Android usure 2,0](intro-to-wear-images/hero-image.png "Exemple d’appareil Android d’usure 2,0")

Xamarin. Android 5,0 et versions ultérieures prennent en charge l’usure Android via notre prise en charge d’Android 4.4 W (API 20) et un package NuGet qui ajoute des contrôles d’interface utilisateur supplémentaires spécifiques à l’usure. Xamarin. Android 5,0 et versions ultérieures incluent également des fonctionnalités d’empaquetage de vos applications d’usure. Les packages NuGet sont également disponibles pour Android usure 2,0, comme décrit plus loin dans ce guide.

## <a name="android-wear-basics"></a>Notions de base sur l’usure Android

Android usure présente un paradigme d’interface utilisateur qui diffère de celui des applications de poche Android. La première vague de l’usure des applications a été conçue pour étendre une application de poche auxiliaire d’une certaine façon, mais à partir d’Android usure 2,0, les applications d’usure peuvent être utilisées de manière autonome. Lorsque vous déployez une application d’usure, elle est empaquetée avec une application de poche auxiliaire. Étant donné que la plupart des applications d’usure dépendent d’une application de poche, elles ont besoin d’un moyen de communiquer avec les applications de poche. Les sections suivantes décrivent ces scénarios d’utilisation et présentent les fonctionnalités d’usure Android essentielles. 

### <a name="usage-scenarios"></a>Scénarios d'utilisation

La première version de Android usure était principalement axée sur l’extension d’applications de poche actuelles avec des notifications améliorées et la synchronisation des données entre l’application de poche et l’application portable. Par conséquent, ces scénarios sont relativement simples à implémenter.

#### <a name="wearable-notifications"></a>Notifications à porter

La façon la plus simple de prendre en charge l’usure Android est de tirer parti de la nature partagée des notifications entre le portable et l’appareil portable. À l’aide de l’API de notification v4 de prise en charge et de la classe `WearableExtender` (disponible dans la [bibliothèque de prise en charge Xamarin Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)), vous pouvez exploiter les fonctionnalités natives de la plateforme, telles que les cartes de style de la boîte de réception ou l’entrée vocale. L’exemple [RecipeAssistant](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-recipeassistant) fournit un exemple de code qui montre comment envoyer une liste de notifications à un appareil Android d’usure. 

#### <a name="companion-applications"></a>Applications auxiliaires

Une autre stratégie consiste à créer une application complète qui s’exécute en mode natif sur l’appareil portable et les paires avec une application de poche auxiliaire. L’exemple d’application de [test](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-quiz) , qui montre comment créer un quiz qui s’exécute sur un appareil mobile et vous pose des questions sur l’appareil portable, est un bon exemple de cette approche. 

### <a name="user-interface"></a>Interface utilisateur

Le modèle de navigation principal pour l’usure est une série de cartes organisées verticalement. Chacune de ces cartes peut avoir des actions associées qui sont superposées sur la même ligne. La classe `GridViewPager` fournit cette fonctionnalité. il adhère au même concept d’adaptateur que `ListView`. En général, vous associez le `GridViewPager` à un `FragmentGridPagerAdaptor` (ou `GridPagerAdaptor`) qui vous permet de représenter chaque cellule de ligne et de colonne sous la forme d’un `Fragment` : 

[![Navigation usure](intro-to-wear-images/2d-picker-sml.png "Navigation usure")](intro-to-wear-images/2d-picker.png#lightbox)

L’usure utilise également des boutons d’action qui se composent d’un petit cercle de couleur, avec un texte de description de petite taille en dessous (comme illustré ci-dessus).  L’exemple [GridViewPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-gridviewpager) montre comment utiliser `GridViewPager` et `GridPagerAdapter` dans une application d’usure.

Android usure 2,0 ajoute un tiroir de navigation, un tiroir d’action et des boutons d’action en ligne à l’interface utilisateur d’usure. Pour plus d’informations sur les éléments d’interface utilisateur Android usure 2,0, consultez la rubrique [anatomie](https://www.google.com/design/spec-wear/system-overview/anatomy.html) Android. 

### <a name="communications"></a>Communications

Android usure fournit deux API de communication différentes pour faciliter la communication entre les applications mobiles et les applications de poche auxiliaires : 

L' **API de données** &ndash; cette API est similaire à une banque de données synchronisée entre l’appareil portable et l’appareil mobile. Android s’occupe de la propagation des modifications entre les ordinateurs portables et les périphériques de poche lorsqu’il est optimal de le faire. Lorsque le portable est hors limites, il met la synchronisation en file d’attente pour une date ultérieure. Le point d’entrée principal pour cette API est `WearableClass.DataApi`. Pour plus d’informations sur cette API, consultez la rubrique [éléments de données de synchronisation](https://developer.android.com/training/wearables/data-layer/data-items.html) Android. 

L' **API de Message** &ndash; cette API vous permet d’utiliser un chemin de communication de niveau inférieur : une petite charge utile est envoyée à sens unique sans synchronisation entre les applications de poche et portable.
Le point d’entrée principal pour cette API est `WearableClass.MessageApi`.
Pour plus d’informations sur cette API, consultez la rubrique [messages d’envoi et de réception](https://developer.android.com/training/wearables/data-layer/messages.html) Android.

Vous pouvez choisir d’enregistrer les rappels pour recevoir ces messages via chacune des interfaces d’écouteur d’API ou, également, implémenter un service dans votre application qui dérive de `WearableListenerService`.
Ce service sera automatiquement instancié par Android.
L’exemple [FindMyPhone](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-findmyphonesample) montre comment implémenter un `WearableListenerService`.

### <a name="deployment"></a>Déploiement

Chaque application portable est déployée avec son propre fichier APK incorporé dans l’application principale APK. Cet empaquetage est géré automatiquement dans Xamarin. Android 5,0 et versions ultérieures, mais doit être effectué manuellement pour les versions de Xamarin. Android antérieures à la version 5,0. 
L’utilisation de l' [empaquetage](~/android/wear/deploy-test/packaging.md) explique le déploiement plus en détail. 

## <a name="going-further"></a>Aller plus loin 

La meilleure façon de se familiariser avec Android est de générer et tester votre première application. La liste suivante fournit un ordre de lecture recommandé pour vous permettre d’être rapidement opérationnel :

1. [Le programme d'](~/android/wear/get-started/installation.md) installation de & fournit des instructions détaillées pour l’installation et la configuration de votre environnement de développement afin de générer des applications d’usure Xamarin. Android. 

2. Une fois que vous avez installé les packages requis et configuré un émulateur ou un appareil, consultez [Hello, usure](~/android/wear/get-started/hello-wear.md) pour obtenir des instructions pas à pas qui expliquent comment créer un petit projet Android d’usure qui gère les clics de bouton et affiche un compteur sur l’usure passerelle. 

3. Le [test de & de déploiement](~/android/wear/deploy-test/index.md) fournit des informations plus détaillées sur la configuration et le déploiement des émulateurs et des appareils, y compris des instructions sur la façon de déployer votre application sur un appareil d’usure via Bluetooth.

4. [Utilisation des tailles d’écran](~/android/wear/screen-sizes.md) explique comment afficher un aperçu et optimiser votre interface utilisateur pour les différentes tailles d’écran disponibles sur les appareils d’usure. 

5. [Utilisation de l’empaquetage](~/android/wear/deploy-test/packaging.md) décrit les étapes à suivre pour empaqueter manuellement des applications d’usure pour la distribution sur Google Play.

Une fois que vous avez créé votre première application d’usure, vous souhaiterez peut-être essayer de créer une facette personnalisée pour l’usure Android. 
[La création d’une face de montre](~/android/wear/platform/creating-a-watchface.md) fournit des instructions pas à pas et des exemples de code pour le développement d’un service de visage de montre numérique supprimé, suivi d’un plus grand code qui l’améliore à un visage de type analogique avec des fonctionnalités supplémentaires. 

## <a name="android-wear-20"></a>Android usure 2,0

Android usure 2,0 introduit un grand nombre de nouvelles fonctionnalités, telles que les *complications*, les dispositions incurvées, les tiroirs de navigation et d’action, ainsi que les notifications étendues. En outre, l’usure de 2,0 vous permet de créer des applications autonomes qui fonctionnent indépendamment des applications de poche. La nouvelle fonctionnalité de *gestes de poignets* permet des interactions entre les mains avec votre application. Les sections suivantes mettent en évidence ces fonctionnalités et fournissent des liens pour vous aider à commencer à les utiliser dans votre application.

### <a name="install-wear-20-packages"></a>Installer l’usure des packages 2,0

Pour créer une application 2,0 avec l’usure avec Xamarin. Android, vous devez ajouter le package **Xamarin. Android. usure v 2.0** à votre projet (cliquez sur l' **onglet Parcourir**) :

[![Xamarin. Android. usure v 2.0](intro-to-wear-images/wear-nuget-2.0-sml.png "Installer Xamarin. Android. usure v 2.0 NuGet")](intro-to-wear-images/wear-nuget-2.0.png#lightbox)

Ce package NuGet contient des liaisons pour les bibliothèques de compatibilité avec le support Android et l’usure.

En plus de **Xamarin. Android. usure**, nous vous recommandons d’installer le **Xamarin. GooglePlayServices. portable** NuGet : 

[![Xamarin. GooglePlayServices. portable](intro-to-wear-images/gpsw-nuget-sml.png "Installez le NuGet Xamarin. GooglePlayServices. portable.")](intro-to-wear-images/gpsw-nuget.png#lightbox)

### <a name="key-features-of-wear-20"></a>Principales caractéristiques de l’usure de 2,0

Android usure 2,0 est la plus grande mise à jour de l’usure Android depuis son lancement initial dans 2014. Les sections suivantes mettent en évidence les principales fonctionnalités d’Android usure 2,0, et des liens sont fournis pour vous aider à commencer à utiliser ces nouvelles fonctionnalités dans votre application. 

#### <a name="complications"></a>Complications

Les *complications* sont de petits widgets de visage de montre que vous pouvez voir d’un coup d’œil sans avoir à faire glisser la face de la montre. Les complications sont similaires aux widgets de tableau de bord de type bureau. ils affichent des informations telles que la météo, la durée de vie de la batterie, les événements de calendrier et les statistiques d’application de pertinence : 

![Exemple de complications](intro-to-wear-images/complications.png "Exemple de complications")

Pour plus d’informations sur les complications, consultez la rubrique sur les [complications du visage](https://developer.android.com/wear/preview/features/complications.html) de la montre Android. 

#### <a name="navigation-and-action-drawers"></a>Navigation et tiroirs d’action 

Deux nouveaux tiroirs sont inclus dans l’usure 2,0. Le *tiroir de navigation*, qui apparaît en haut de l’écran, permet aux utilisateurs de naviguer entre les vues de l’application (comme indiqué à gauche ci-dessous). Le *tiroir action*, qui apparaît en bas de l’écran (comme indiqué à droite), permet aux utilisateurs de choisir dans une liste d’actions. 

![Navigation et tiroirs d’action](intro-to-wear-images/drawers.png "Navigation et tiroirs d’action")

Pour plus d’informations sur ces deux nouveaux tiroirs interactifs, consultez la rubrique consacrée à la [navigation et aux actions](https://developer.android.com/wear/preview/features/ui-nav-actions.html) Android. 

#### <a name="curved-layouts"></a>Dispositions courbées 

L’usure 2,0 introduit de nouvelles fonctionnalités permettant d’afficher des dispositions courbées sur des appareils à usure ronde. Plus précisément, la nouvelle classe `WearableRecyclerView` est optimisée pour afficher une liste d’éléments verticaux sur les affichages arrondis : 

![Exemple de disposition courbée](intro-to-wear-images/curved-layout.png "Exemple de disposition courbée")

`WearableRecyclerView` étend la classe `RecyclerView` pour prendre en charge les dispositions courbées et les gestes de défilement circulaires. Pour plus d’informations, consultez la documentation de l’API [WearableRecyclerView](https://developer.android.com/reference/android/support/wearable/view/WearableRecyclerView.html) Android. 

#### <a name="standalone-apps"></a>Applications autonomes 

Android usure 2,0 les applications peuvent fonctionner indépendamment des applications de poche. Cela signifie que, par exemple, un espion intelligent peut continuer à offrir des fonctionnalités complètes même si l’appareil mobile compagnon est éteint ou éloigné de l’appareil portable. Pour plus d’informations sur cette fonctionnalité, consultez la rubrique [applications autonomes](https://developer.android.com/wear/preview/features/standalone-apps.html) Android.

#### <a name="wrist-gestures"></a>Gestes de poignet 

Les gestes de poignet permettent aux utilisateurs d’interagir avec votre application sans utiliser l’écran tactile &ndash; les utilisateurs peuvent répondre à l’application à l’aide d’une seule main. Deux gestes de poignet sont pris en charge : 

- Poignet de scintillement
- Poignet de scintillement

Pour plus d’informations, consultez la rubrique [gestes du poignet](https://developer.android.com/wear/preview/features/gestures.html) Android. 

Il existe de nombreuses fonctionnalités 2,0, telles que les actions en ligne, la réponse intelligente, l’entrée à distance, les notifications étendues et un nouveau mode de pontage pour les notifications. Pour plus d’informations sur les nouvelles fonctionnalités 2,0 de l’usure, consultez la [vue d’ensemble](https://developer.android.com/wear/preview/api-overview.html)de l’API Android. 

## <a name="devices"></a>Appareils

Voici quelques exemples des appareils qui peuvent exécuter Android :

- [Motorola 360](https://moto360.motorola.com/)
- [LG G Watch](https://www.lg.com/us/smart-watches/lg-W100-g-watch)
- [LG G Watch R](https://www.lg.com/us/smartwatch/g-watch-r)
- [Samsung Gear en direct](https://www.samsung.com/global/microsite/gear/gearlive_design.html)
- [Sony SmartWatch 3](https://www.sonymobile.com/global-en/products/smartwear/smartwatch-3-swr50/)
- [ASUS ZenWatch](https://www.asus.com/us/Phones/ASUS_ZenWatch_WI500Q/)

## <a name="further-reading"></a>informations supplémentaires

Consultez la documentation relative à l’usure d’Android de Google :

- [À propos de l’usure Android](https://www.android.com/wear/)
- [Conception d’applications Android usure](https://developer.android.com/design/wear/index.html)
- [Android. support. Bibliothèque portable](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [Android usure 2,0](https://developer.android.com/wear/preview/index.html)

## <a name="summary"></a>Récapitulatif

Cette introduction a fourni une vue d’ensemble de l’usure d’Android. Il a décrit les fonctionnalités de base de Android usure et a inclus une vue d’ensemble des fonctionnalités introduites dans Android usure 2,0. Il fournit des liens vers la documentation essentielle pour aider les développeurs à se familiariser avec le développement de Xamarin. Android, et il a présenté des exemples de certains appareils Android qui sont actuellement sur le marché.

## <a name="related-links"></a>Liens associés

- [Installation et configuration](~/android/wear/get-started/installation.md)
- [Bien démarrer](~/android/wear/get-started/index.md)
