---
title: Introduction à Android Wear
description: Avec l’introduction de Google Android Wear, vous n’êtes plus limité à seulement les téléphones et tablettes lorsqu’il s’agit de développer des applications Android exceptionnelles. Prise en charge de Xamarin.Android pour Android Wear rend possible d’exécuter C# code sur votre poignet ! Cette présentation fournit une vue d’ensemble de Android Wear, décrit ses principales fonctionnalités et offre une vue d’ensemble des fonctionnalités disponibles dans Android Wear de 2.0. Il dresse la liste des appareils Android Wear plus populaires, et il fournit des liens vers la documentation de Google Android Wear essentielle pour plus d’informations.
ms.prod: xamarin
ms.assetid: EAEF99F0-8FBE-47E4-8644-E7244CFAF464
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 57a521df5fdede2bd92a6b1432f698c822a2b06f
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674569"
---
# <a name="introduction-to-android-wear"></a>Introduction à Android Wear

_Avec l’introduction de Google Android Wear, vous n’êtes plus limité à seulement les téléphones et tablettes lorsqu’il s’agit de développer des applications Android exceptionnelles. Prise en charge de Xamarin.Android pour Android Wear rend possible d’exécuter C# code sur votre poignet ! Cette présentation fournit une vue d’ensemble de Android Wear, décrit ses principales fonctionnalités et offre une vue d’ensemble des fonctionnalités disponibles dans Android Wear de 2.0. Il dresse la liste des appareils Android Wear plus populaires, et il fournit des liens vers la documentation de Google Android Wear essentielle pour plus d’informations._


## <a name="overview"></a>Vue d'ensemble

Android Wear s’exécute sur divers appareils, y compris la première génération 360 Motorola, G espion de LG et la vie de l’engrenage de Samsung. Une deuxième génération, y compris 3 de SmartWatch de Sony, a également été publiée avec des fonctionnalités supplémentaires, notamment le GPS intégrés et la lecture de musique en mode hors connexion. Pour Android Wear version 2.0, Google a collaboré avec LG pour deux observations nouvelle : le Sport espion LG et le Style d’espion LG.

![Les appareils Android Wear de 2.0](intro-to-wear-images/hero-image.png "exemple Wear 2.0 les appareils Android")

Xamarin.Android 5.0 et versions ultérieures prend en charge Android Wear via notre 4.4W Android (API 20) prennent en charge et contrôle de l’interface utilisateur de l’usure spécifiques de package NuGet qui ajoute supplémentaires. Xamarin.Android 5.0 et versions ultérieures inclut également des fonctionnalités pour l’empaquetage de vos applications de l’usure. Les packages NuGet sont également disponibles pour Android Wear 2.0 comme décrit plus loin dans ce guide.


## <a name="android-wear-basics"></a>Principes de base Android Wear

Android Wear a un paradigme d’interface utilisateur qui diffère de celui des applications portables Android. La première vague d’usure applications ont été conçus pour étendre un Compagnon app poche dans certains moyen, mais le début avec Android Wear 2.0, applications d’usure peut être autonome utilisé. Lorsque vous déployez une application Wear, il est fourni avec une application de poche Compagnon. Étant donné que la plupart Wear applications dépendent d’une application Compagnon de poche, dont ils ont besoin d’un moyen de communiquer avec les applications portables. Les sections suivantes décrivent ces scénarios d’utilisation et présentent les fonctionnalités essentielles Android Wear. 



### <a name="usage-scenarios"></a>Scénarios d'utilisation

La première version d’Android Wear portait principalement sur l’extension d’applications de poche actuelles avec les notifications améliorées et la synchronisation des données entre l’application de poche et l’application et portable. Par conséquent, ces scénarios sont relativement faciles à implémenter.


#### <a name="wearable-notifications"></a>Notifications et portable

Pour prendre en charge Android Wear, le plus simple consiste à tirer parti de la nature partagée de notifications entre l’ordinateur de poche et le périphérique portable. À l’aide de l’API de notification v4 prise en charge et la `WearableExtender` classe (disponible dans le [bibliothèque de prise en charge Android Xamarin](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)), vous pouvez exploiter les fonctionnalités natives de la plateforme, tels que des cartes de style de boîte de réception ou vocales d’entrée. Le [RecipeAssistant](https://developer.xamarin.com/samples/monodroid/wear/RecipeAssistant/) exemple fournit des exemples de code qui montre comment envoyer une liste de notifications sur un appareil Android Wear. 



#### <a name="companion-applications"></a>Le Guide des Applications

Une autre stratégie consiste à créer une application complète qui s’exécute en mode natif sur l’appareil portable et paires avec une application de poche Compagnon. Un bon exemple de cette approche est la [questionnaire](https://developer.xamarin.com/samples/monodroid/wear/Quiz/) exemple d’application, qui montre comment créer un questionnaire qui s’exécute sur un périphérique portable et pose des questions de questionnaire sur l’appareil portable. 



### <a name="user-interface"></a>Interface utilisateur

Le modèle de navigation principal pour une usure est une série de cartes disposées verticalement. Chacune de ces cartes permettre avoir des actions associées qui constituent une couche sur la même ligne. Le `GridViewPager` classe fournit cette fonctionnalité ; il respecte le même concept de carte en tant que `ListView`. En général, vous associez le `GridViewPager` avec un `FragmentGridPagerAdaptor` (ou `GridPagerAdaptor`) qui vous permet de représenter chaque ligne et colonne de cellules qu’un `Fragment`: 

[![Navigation d’usure](intro-to-wear-images/2d-picker-sml.png "Wear Navigation")](intro-to-wear-images/2d-picker.png#lightbox)

Wear également facilite l’utilisation des boutons d’action qui se composent d’une grande coloré cercle avec le texte de description de petites situé en dessous (comme illustré ci-dessus).  Le [GridViewPager](https://developer.xamarin.com/samples/monodroid/wear/GridViewPager/) exemple montre comment utiliser `GridViewPager` et `GridPagerAdapter` dans une application de l’usure.

Android Wear 2.0 ajoute un tiroir de navigation, un tiroir d’action et les boutons d’action inline à l’interface utilisateur de l’usure. Pour plus d’informations sur les éléments d’interface utilisateur Android Wear de 2.0, consultez le Android [Anatomie](https://www.google.com/design/spec-wear/system-overview/anatomy.html) rubrique. 



### <a name="communications"></a>Communications

Android Wear fournit deux API pour faciliter les communications entre les applications portable et portable Compagnon de communication différents : 

**Données API** &ndash; cette API est similaire à un magasin de données synchronisées entre l’appareil portable et l’appareil de poche. Android s’occupe de la propagation des modifications entre portable et portable lorsqu’il est optimale pour ce faire. Lorsque le portable est hors limites, il files d’attente de synchronisation pour une date ultérieure. Le point d’entrée principal pour cette API est `WearableClass.DataApi`. Pour plus d’informations sur cette API, consultez le Android [la synchronisation des éléments de données](https://developer.android.com/training/wearables/data-layer/data-items.html) rubrique. 

**API de message** &ndash; cette API permet d’utiliser un chemin d’accès de communications de niveau inférieur : une petite charge utile est envoyée à sens unique sans synchronisation entre les applications portables et portable.
Le point d’entrée principal pour cette API est `WearableClass.MessageApi`.
Pour plus d’informations sur cette API, consultez le Android [envoi et réception de Messages](https://developer.android.com/training/wearables/data-layer/messages.html) rubrique.

Vous pouvez choisir d’enregistrer des rappels pour la réception de ces messages par le biais de chacune des interfaces API écouteur ou, vous pouvez également implémenter un service dans votre application qui dérive de `WearableListenerService`.
Ce service sera automatiquement instancié par Android Wear.
Le [FindMyPhone](https://developer.xamarin.com/samples/monodroid/wear/FindMyPhoneSample/) exemple illustre comment implémenter un `WearableListenerService`.



### <a name="deployment"></a>Déploiement

Chaque application portable est déployée avec son propre fichier APK incorporé à l’intérieur de l’application principale APK. Ce package est géré automatiquement dans Xamarin.Android 5.0 et versions ultérieures, mais doit être effectuée manuellement pour les versions antérieures à la version 5.0 de Xamarin.Android. 
[Utilisation avec l’empaquetage](~/android/wear/deploy-test/packaging.md) explique le déploiement plus en détail. 



## <a name="going-further"></a>Pour aller plus loin 

La meilleure façon de vous familiariser avec Android Wear consiste à générer et tester votre première application. La liste suivante fournit un ordre de lecture recommandée pour vous aider à être rapidement opérationnel :

1.  [Configuration et Installation](~/android/wear/get-started/installation.md) fournit des instructions détaillées pour installer et configurer votre environnement de développement pour la création d’applications Xamarin.Android usure. 

2.  Après avoir installé les packages requis et configuré un appareil ou un émulateur, consultez [Hello, l’usure](~/android/wear/get-started/hello-wear.md) pour obtenir des instructions pas à pas qui expliquent comment créer un petit projet Android Wear ce bouton handles clique sur et affiche un Cliquez sur le compteur sur l’appareil de l’usure. 

3.  [Déploiement et test](~/android/wear/deploy-test/index.md) fournit plus d’informations sur la configuration et de déploiement pour les émulateurs et appareils, notamment des instructions sur la façon de déployer votre application sur un appareil Wear via Bluetooth.

4.  [Utilisation de tailles d’écran](~/android/wear/screen-sizes.md) explique comment afficher un aperçu et optimiser votre interface utilisateur pour les différentes tailles d’écran disponible sur les appareils de l’usure. 

5.  [Utilisation avec l’empaquetage](~/android/wear/deploy-test/packaging.md) décrit les étapes de l’empaquetage manuel d’usure des applications pour la distribution sur Google Play.

Une fois que vous avez créé votre première application Wear, voulez-vous essayer de créer un personnalisé cadran de montre pour Android Wear. 
[Création d’un cadran de montre](~/android/wear/platform/creating-a-watchface.md) fournit des instructions détaillées et des exemples de code pour le développement d’un élément supprimé vers le bas de service de visage espion numérique, suivie de davantage de code qui améliore l’à une face de style analogique Espion avec des fonctionnalités supplémentaires. 



## <a name="android-wear-20"></a>Android Wear 2.0

Android 2.0 Wear propose de nouvelles fonctionnalités et fonctions, telles que *complications*, courbe dispositions, les tiroirs de navigation et l’action et les notifications étendues. 2\.0 Wear rend également possible de générer des applications autonomes qui fonctionnent indépendamment des applications portables. La nouvelle *mouvements de poignet* fonctionnalité permet une interaction avec votre application. Les sections suivantes mettent en évidence ces fonctionnalités et fournissent des liens pour vous aider à commencer à les utiliser dans votre application.



### <a name="install-wear-20-packages"></a>Installation d’usure 2.0 Packages

Pour créer une application 2.0 Wear avec Xamarin.Android, vous devez ajouter le **Xamarin.Android.Wear v2.0** package à votre projet (cliquez sur le **onglet Parcourir**) :

[![Xamarin.Android.Wear v2.0](intro-to-wear-images/wear-nuget-2.0-sml.png "installer NuGet Xamarin.Android.Wear v2.0")](intro-to-wear-images/wear-nuget-2.0.png#lightbox)

Ce package NuGet contient des liaisons pour les bibliothèques de la prise en charge Android et portable et Compat Wear.

En plus de **Xamarin.Android.Wear**, nous vous recommandons d’installer le **Xamarin.GooglePlayServices.Wearable** NuGet : 

[![Xamarin.GooglePlayServices.Wearable](intro-to-wear-images/gpsw-nuget-sml.png "installer Xamarin.GooglePlayServices.Wearable NuGet")](intro-to-wear-images/gpsw-nuget.png#lightbox)


### <a name="key-features-of-wear-20"></a>Principales fonctionnalités d’usure 2.0

Android Wear de 2.0 est la plus grande mise à jour pour Android Wear depuis son lancement initial en 2014. Les sections suivantes illustrent les principales fonctionnalités d’Android 2.0 Wear, et des liens sont fournis pour aider à vous familiariser concrètement avec ces nouvelles fonctionnalités dans votre application. 


#### <a name="complications"></a>Complications

*Complications* est espion petits widgets face que vous pouvez voir en un clin de œil, sans avoir à présenter le cadran de montre. Complications sont similaires aux widgets de tableau de bord de style de bureau ; elles affichent des informations telles que la météo, autonomie, événements de calendrier et statistiques d’application de Fitness (pertinence) : 

![Exemple de complications](intro-to-wear-images/complications.png "exemple de Complications")

Pour plus d’informations sur les complications, consultez le Android [espion Face Complications](https://developer.android.com/wear/preview/features/complications.html) rubrique. 



#### <a name="navigation-and-action-drawers"></a>Navigation et tiroirs de l’Action 

Deux nouveaux bacs sont inclus dans 2.0 Wear. Le *le tiroir de navigation*, qui apparaît en haut de l’écran, permet aux utilisateurs de naviguer entre les vues de l’application (comme illustré ci-dessous à gauche). Le *tiroir d’action*, qui apparaît en bas de l’écran (comme indiqué sur la droite), permet aux utilisateurs de choisir à partir d’une liste d’actions. 

![Navigation et Action tiroirs](intro-to-wear-images/drawers.png "tiroirs de l’Action et de Navigation")

Pour plus d’informations sur ces deux bacs interactives de nouveau, consultez l’Android [Wear la Navigation et les Actions](https://developer.android.com/wear/preview/features/ui-nav-actions.html) rubrique. 



#### <a name="curved-layouts"></a>Dispositions en arc 

Usure 2.0 introduit de nouvelles fonctionnalités pour l’affichage des dispositions de courbes sur les appareils usure round. Plus précisément, la nouvelle `WearableRecyclerView` classe est optimisée pour afficher une liste d’éléments verticales sur les écrans round : 

![Exemple de disposition de courbe](intro-to-wear-images/curved-layout.png "exemple de disposition en arc")

`WearableRecyclerView` étend la `RecyclerView` classe pour prendre en charge les dispositions de courbes et des mouvements de défilement circulaires. Pour plus d’informations, consultez le Android [WearableRecyclerView](https://developer.android.com/reference/android/support/wearable/view/WearableRecyclerView.html) documentation de l’API. 



#### <a name="standalone-apps"></a>Applications autonomes 

Les applications Android Wear de 2.0 peuvent travailler indépendamment des applications portables. Cela signifie que, par exemple, un espion actif peut continuer offrir des fonctionnalités complètes même si l’appareil de poche Compagnon est désactivé ou éloigné de l’appareil portable. Pour plus d’informations sur cette fonctionnalité, consultez le Android [applications autonomes](https://developer.android.com/wear/preview/features/standalone-apps.html) rubrique.



#### <a name="wrist-gestures"></a>Mouvements de poignet 

Les mouvements de poignet permettent aux utilisateurs d’interagir avec votre application sans l’aide de l’écran tactile &ndash; les utilisateurs puissent répondre à l’application avec une seule main. Deux mouvements de poignet sont prises en charge : 

-   Poignet Flick out
-   Poignet Flick dans

Pour plus d’informations, consultez le Android [poignet mouvements](https://developer.android.com/wear/preview/features/gestures.html) rubrique. 


Il existe beaucoup d’autres fonctionnalités 2.0 Wear, accompagnées d’actions réponse intelligente, saisie à distance, notifications étendues et un nouveau mode de pontage pour les notifications. Pour plus d’informations sur les nouvelles fonctionnalités 2.0 Wear, consultez le Android [vue d’ensemble de l’API](https://developer.android.com/wear/preview/api-overview.html). 



## <a name="devices"></a>Appareils

Voici quelques exemples des appareils qui peuvent s’exécuter Android Wear :

* [Motorola 360](https://moto360.motorola.com/)
* [LG G espion](http://www.lg.com/us/smart-watches/lg-W100-g-watch)
* [LG G espion R](http://www.lg.com/us/smartwatch/g-watch-r)
* [Samsung ENGRENAGE en direct](http://www.samsung.com/global/microsite/gear/gearlive_design.html)
* [Sony SmartWatch 3](http://www.sonymobile.com/global-en/products/smartwear/smartwatch-3-swr50/)
* [ASUS ZenWatch](http://www.asus.com/us/Phones/ASUS_ZenWatch_WI500Q/)



## <a name="further-reading"></a>informations supplémentaires

Découvrez la documentation d’Android Wear de Google :

* [Sur Android Wear](http://www.android.com/wear/)
* [Conception de l’application Android Wear](https://developer.android.com/design/wear/index.html)
* [bibliothèque de Android.support.wearable](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
* [Android Wear 2.0](https://developer.android.com/wear/preview/index.html)



## <a name="summary"></a>Récapitulatif

Cette introduction fourni une vue d’ensemble de Android Wear. Il décrit les fonctionnalités de base de Android Wear et inclus une vue d’ensemble des fonctionnalités introduites dans Android Wear de 2.0. Il fourni des liens vers la lecture essentielles pour aider les développeurs à bien démarrer avec le développement de l’usure de Xamarin.Android, et il répertorié les exemples de certains appareils Android Wear actuellement sur le marché.


## <a name="related-links"></a>Liens associés

- [Installation et configuration](~/android/wear/get-started/installation.md)
- [Prise en main](~/android/wear/get-started/index.md)
