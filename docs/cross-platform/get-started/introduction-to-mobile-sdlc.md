---
title: Cycle de vie du développement de logiciel mobile
description: Ce document décrit le cycle de vie de développement des logiciels mobiles. Il aborde la conception d’expérience utilisateur, la conception d’interface utilisateur, le développement, la stabilisation, la distribution et bien plus encore.
ms.prod: xamarin
ms.assetid: 420c5fdf-4610-4e71-9db5-fe894c961924
author: conceptdev
ms.author: crdun
ms.date: 11/22/2016
ms.openlocfilehash: 4e2af0d1746ef1321db39d97a86af2b4bd769c69
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526696"
---
# <a name="mobile-software-development-lifecycle"></a>Cycle de vie du développement de logiciel mobile

La création d’applications mobiles peut être aussi simple qu’ouvrir Visual Studio, assembler quelques petites choses, faire quelques tests rapides et soumettre le résultat à un App Store, le tout en une seule après-midi. Cela peut aussi consister en un processus extrêmement complexe impliquant une conception préalable rigoureuse, des tests d’utilisabilité, des tests d’assurance qualité sur des milliers d’appareils, un cycle de vie complet avec des versions bêta, puis un déploiement effectué de plusieurs façons différentes.

Dans ce document, nous allons examiner de façon approfondie la création d’applications mobiles, notamment :

1. **Processus** : le processus de développement de logiciels est appelé « cycle de vie de développement de logiciels ». Nous allons examiner toutes les phases du cycle de vie de développement de logiciels pour le développement d’applications mobiles, notamment : Lancement, conception, développement, stabilisation, déploiement et maintenance.
1. **Considérations** : plusieurs aspects doivent être considérés pour la création d’applications mobiles, en particulier pour ce qui diffère des applications web ou pour poste de travail traditionnelles. Nous allons examiner ces considérations et comment elles affectent le développement d’applications mobiles.

Ce document est destiné à répondre à des questions essentielles sur le développement d’applications mobiles, à la fois pour les développeurs d’application débutants et expérimentés. Il adopte une approche assez complète pour présenter la plupart des concepts que vous allez rencontrer au cours de l’ensemble du cycle de vie de développement de logiciels. Ce document peut cependant ne pas être pour tout le monde : si vous avez hâte de commencer à créer des applications, nous vous recommandons de passer directement au guide [Introduction au développement mobile](~/cross-platform/get-started/introduction-to-mobile-development.md), puis de revenir à ce document plus tard.

## <a name="mobile-development-software-lifecycle"></a>Cycle de vie des logiciels de développement mobile

Le cycle de vie du développement mobile n’est globalement pas différent de celui des applications web ou pour poste de travail. Comme pour ces applications, le processus comprend généralement 5 parties principales :

1. **Lancement** : toutes les applications commencent par une idée. Cette idée est généralement affinée jusqu’à constituer une base solide pour une application.
1. **Conception** : la phase de conception consiste à définir l’expérience utilisateur de l’application, comme la présentation générale, son fonctionnement, etc., ainsi que la conversion de cette expérience utilisateur en une interface utilisateur appropriée, généralement avec l’aide d’un infographiste.
1. **Développement** : généralement la phase la plus consommatrice de ressources, c’est la phase de création réelle de l’application.
1. **Stabilisation** : quand le développement est suffisamment avancé, l’assurance qualité commence généralement à tester l’application et les bogues sont corrigés. Le plus souvent, une application passe par une phase bêta limitée, durant laquelle un public plus large a la possibilité de l’utiliser, de fournir des commentaires et d’obtenir des modifications.
1. **Déploiement**

Souvent, la plupart de ces phases se recouvrent : par exemple, il est courant que le développement se poursuive tandis que l’interface utilisateur est en cours de finalisation, et il peut même déterminer la conception de l’interface utilisateur. En outre, une application peut entrer dans une phase de stabilisation en même temps que de nouvelles fonctionnalités sont ajoutées à une nouvelle version.

Ces phases peuvent même être utilisées dans différentes méthodologies de cycle de vie de développement de logiciels, comme Agile, Spiral, Waterfall, etc.

Chacune de ces phases est expliquée plus en détail dans les sections suivantes.

### <a name="inception"></a>Lancement

L’omniprésence et le niveau d’interaction qu’ont les gens avec les appareils mobiles signifient que presque tout le monde a une idée d’une application mobile. Les appareils mobiles ouvrent la porte à une toute nouvelle façon d’interagir avec l’informatique, avec le web et même avec l’infrastructure des entreprises.

La phase de lancement est tout ce qui concerne la définition et l’amélioration de l’idée d’une application.
Pour créer une application réussie, il est important de se poser quelques questions fondamentales. Voici quelques points à prendre en compte avant de publier une application dans un des App Stores publics :

- **Avantage concurrentiel** : des applications similaires existent-elles déjà ? Si oui, comment cette application se différencie-t-elle des autres ?

Pour les applications qui doivent être distribuées dans une entreprise :

- **Intégration à l’infrastructure** : quelle infrastructure existante doit-elle étendre ou à quelle infrastructure doit-elle s’intégrer ?

En outre, les applications doivent être évaluées dans le contexte du facteur de forme mobile :

- **Valeur** : quelle valeur cette application apporte-t-elle aux utilisateurs ? Comment vont-ils l’utiliser ?
- **Forme/mobilité** : comment cette application va-t-elle fonctionner dans un facteur de forme mobile ? Comment puis-je ajouter de la valeur avec des technologies mobiles comme le suivi de l’emplacement, l’appareil photo, etc. ?

Pour faciliter la conception des fonctionnalités d’une application, il peut être utile de définir des acteurs et des [cas d’usage](https://en.wikipedia.org/wiki/Use_case). Les acteurs sont des rôles au sein d’une application ; il s’agit souvent d’utilisateurs. Les cas d’usage sont généralement des actions ou des objectifs.

Par exemple, une application de suivi des tâches peut avoir deux acteurs : *Utilisateur* et *Ami*. Un utilisateur peut *créer une tâche* et peut aussi *partager une tâche* avec un ami. Dans ce cas, la création d’une tâche et le partage d’une tâche sont deux cas d’usage distincts qui, conjointement avec les acteurs, vont déterminer les écrans que vous devez créer, ainsi que les entités et la logique métier à développer.

Une fois qu’un nombre approprié de cas d’usage et d’acteurs ont été spécifiés, il est beaucoup plus facile de commencer à concevoir une application. Le développement peut alors se concentrer sur la création de l’application, et non pas sur ce qu’est l’application ou sur ce qu’elle devrait faire.

### <a name="designing-mobile-applications"></a>Conception des applications mobiles

Une fois que les caractéristiques et les fonctionnalités de l’application ont été déterminées, l’étape suivante consiste à commencer à résoudre la question de l’expérience utilisateur.

#### <a name="ux-design"></a>Conception de l’expérience utilisateur

L’expérience utilisateur est généralement modélisée via des maquettes en utilisant l’un des nombreux [toolkits de conception](https://docs.microsoft.com/windows/uwp/design/downloads/). Les maquettes permettent de concevoir l’expérience utilisateur sans devoir se soucier de la conception de l’interface utilisateur réelle :

 [![](introduction-to-mobile-sdlc-images/balsamiq.png "L’expérience utilisateur est généralement modélisée via des maquettes avec des outils comme Balsamiq")](introduction-to-mobile-sdlc-images/balsamiq.png#lightbox)

Lors de la création des maquettes de l’expérience utilisateur, il est important de prendre en compte les recommandations sur l’interface des différentes plateformes ciblées par l’application. L’application doit s’intégrer parfaitement sur chaque plateforme. Les recommandations de conception officielles pour chaque plateforme sont :

1. **Apple** -  [Human Interface Guidelines](https://developer.apple.com/ios/human-interface-guidelines/overview/themes/)
1. **Android** –  [Design Guidelines](https://developer.android.com/design/index.html)
1. **UWP** –  [Principes de base de la conception UWP](https://docs.microsoft.com/windows/uwp/design/basics/)

Par exemple, chaque application a une métaphore pour passer d’une section à l’autre dans une application. iOS utilise une barre d’onglets en bas de l’écran, Android utilise une barre d’onglets en haut de l’écran et UWP utilise la vue [Pivot ou onglet](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/tabs-pivot).

En outre, le matériel lui-même détermine également les décisions relatives à l’expérience utilisateur. Par exemple, les appareils iOS n’ont pas de bouton *Précédent* physique et ils introduisent donc la métaphore du contrôleur de navigation :

 ![](introduction-to-mobile-sdlc-images/01-navigation-controller.png "Les appareils iOS n’ont pas de bouton Précédent physique et ils introduisent donc la métaphore du contrôleur de navigation")

En outre, le facteur de forme influence également les décisions relatives à l’expérience utilisateur. Une tablette a une surface beaucoup plus importante et peut donc afficher plus d’informations. Ce qui nécessite souvent plusieurs écrans sur un téléphone peut être ramassé en seul écran pour une tablette :

 [![](introduction-to-mobile-sdlc-images/iphone-vs-ipad.png "Ce qui nécessite souvent plusieurs écrans sur un téléphone peut être ramassé en seul écran pour une tablette")](introduction-to-mobile-sdlc-images/iphone-vs-ipad.png#lightbox)

Et en raison de l’existence d’une multitude de facteurs de forme, il existe souvent des facteurs de forme de taille moyenne (quelque part entre un téléphone et une tablette) que vous pouvez également cibler.

#### <a name="user-interface-ui-design"></a>Conception de l’interface utilisateur

Une fois que l’expérience utilisateur est déterminée, l’étape suivante consiste à créer la conception de l’interface utilisateur. Si l’expérience utilisateur est généralement modélisée via des maquettes en noir et blanc, des couleurs, des graphiques, etc. sont introduits lors de la phase de conception de l’interface utilisateur. Il est important de passer du temps sur une bonne conception de l’interface utilisateur et, en règle générale, la conception des applications les plus répandues est de niveau professionnel.

Comme avec l’expérience utilisateur, il est important de comprendre que chaque plateforme a son propre langage de conception : ainsi, une application bien conçue peut avoir une apparence différente sur chaque plateforme :

 [![](introduction-to-mobile-sdlc-images/multiplatform-1.png "Une application bien conçue peut avoir une apparence différente sur chaque plateforme")](introduction-to-mobile-sdlc-images/multiplatform-1.png#lightbox)

### <a name="development"></a>Développement

La phase de développement commence généralement très tôt. En fait, souvent, une fois qu’une idée a atteint une certaine maturité dans la phase conceptuelle/idée, un prototype de travail est développé pour valider les fonctionnalités, les hypothèses et pour donner une bonne compréhension de l’étendue du travail.

Dans le reste des didacticiels, nous allons nous concentrer principalement sur la phase de développement.

### <a name="stabilization"></a>Stabilisation

La stabilisation est le processus qui consiste à corriger les bogues de votre application. Il ne s’agit pas seulement du point de vue fonctionnel, par exemple : « Elle plante quand je clique sur ce bouton », mais également de la facilité d’utilisation et des performances. Il est préférable de commencer la stabilisation très tôt dans le processus de développement, afin que les corrections courantes puissent être faites avant de devenir des sources de coûts. En règle générale, les applications passent par les étapes suivantes : *Prototype*, *Alpha*, *Bêta*, et *Version Release Candidate*. Les définitions de ces étapes varient selon les personnes, mais elles suivent généralement le modèle suivant :

1. **Prototype** : l’application est toujours en phase de preuve de concept, et seules les fonctionnalités principales ou des parties spécifiques de l’application fonctionnent. Des bogues majeurs y sont présents.
1. **Alpha** : les fonctionnalités principales sont généralement entièrement présentes dans le code (qui est généré, mais pas entièrement testé). Des bogues majeurs sont encore présents, des fonctionnalités périphériques peuvent ne pas encore être présentes.
1. **Bêta** : la plupart des fonctionnalités sont maintenant terminées, une partie des tests et de la correction des bogues a été effectuée. Des problèmes majeurs connus peuvent encore être présents.
1. **Version Release Candidate** : toutes les fonctionnalités sont terminées et testées. Sauf si de nouveaux bogues apparaissent, l’application est candidate à la publication.

Il n’est jamais trop tôt pour commencer à tester une application. Par exemple, si un problème majeur est trouvé à l’étape du prototypage, l’expérience utilisateur de l’application peut toujours être modifiée pour le résoudre. Si un problème de performances est trouvé à l’étape Alpha, il est suffisamment tôt pour modifier l’architecture avant qu’une grande quantité de code soit générée à partir d’hypothèses qui se révèlent fausses.

En règle générale, au fil de l’évolution d’une application à travers le cycle de vie, elle est ouverte à de plus en plus de personnes, qui peuvent l’essayer, la tester, fournir des commentaires, etc. Par exemple, les applications à l’état de prototype ne peuvent être montrées ou mises à disposition qu’aux principales parties prenantes, tandis que les applications en version Release Candidate peuvent être distribuées à des clients qui s’inscrivent pour pouvoir y accéder de façon anticipée.

Pour des premiers tests et un premier déploiement sur un nombre relativement restreint d’appareils, un déploiement direct à partir d’une machine de développement est généralement suffisant. Cependant, au fil de l’extension du public concerné, ceci peut rapidement devenir fastidieux. En l’état, il existe plusieurs options de déploiement de test qui facilitent ce processus, en vous permettant d’inviter des personnes à rejoindre un pool de test, de publier des builds sur le web et de fournir des outils qui permettent aux utilisateurs de faire des commentaires.

Pour les tests et le déploiement, vous pouvez utiliser [App Center](https://appcenter.ms/) pour générer, tester, publier et surveiller les applications en continu.

### <a name="distribution"></a>Distribution

Une fois que l’application a été stabilisée, il est temps de la publier. Il existe plusieurs options de distribution différentes, en fonction de la plateforme.

#### <a name="ios"></a>iOS

Les applications Xamarin.iOS et Objective-C sont distribuées exactement de la même manière :

1. **Apple App Store** : l’App Store d’Apple est un référentiel d’applications en ligne disponible globalement et qui est intégré à Mac OS X via iTunes. C’est de loin la méthode de distribution la plus répandue pour les applications, et elle permet aux développeurs de commercialiser et de distribuer leurs applications en ligne de façon très simple.
1. **Déploiement interne** : le déploiement interne est la distribution interne des applications d’entreprise qui ne sont pas accessibles publiquement via l’App Store.
1. **Déploiement ad- hoc** : le déploiement ad hoc est principalement destiné au développement et aux tests, et vous permet de déployer sur un nombre limité d’appareils correctement provisionnés. Quand vous déployez sur un appareil via Xcode ou Visual Studio pour Mac, cela s’appelle un déploiement ad hoc.

#### <a name="android"></a>Android

Toutes les applications Android doivent être signées avant d’être distribuées. Les développeurs signent leurs applications en utilisant leur propre certificat protégé par une clé privée. Ce certificat peut fournir une chaîne d’authenticité qui lie un développeur d’applications aux applications que le développeur a créées et publiées.
Il convient de noter que si un certificat de développement pour Android peut être signé par une autorité de certification reconnue, la plupart des développeurs choisissent de ne pas utiliser ces services et auto-signent leurs certificats. L’objectif principal pour les certificats est de faire la distinction entre différents développeurs et différentes applications.
Android utilise ces informations pour appliquer la délégation d’autorisations entre les applications et les composants qui s’exécutent dans le système d’exploitation Android.

Contrairement à d’autres plateformes mobiles répandues, Android adopte une approche très ouverte de la distribution d’applications. Les appareils ne sont pas limités à un seul App Store approuvé.
Au lieu de cela, tout le monde est libre de créer un App Store, et la plupart des téléphones Android autorisent l’installation d’applications de stores tiers.

Ceci permet aux développeurs de disposer d’un canal de distribution potentiellement plus large mais plus complexe pour leurs applications. [Google Play](https://play.google.com/store?hl=en) est l’App Store officiel de Google, mais il en existe beaucoup d’autres. En voici quelques-uns parmi les plus répandus :

1. [AppBrain](http://www.appbrain.com/)
1. [Amazon App Store pour Android](http://www.amazon.com/mobile-apps/b?ie=UTF8&amp;node=2350149011)
1. [Handango](http://www.handango.com/)
1. [GetJar](http://www.getjar.com/)

#### <a name="uwp"></a>UWP

Les applications UWP sont distribuées aux utilisateurs via le Microsoft Store. Les développeurs soumettent leurs applications pour approbation, qui apparaissent ensuite dans le Store. Pour plus d’informations sur la publication des applications Windows, consultez la documentation UWP sur la [publication](https://docs.microsoft.com/windows/uwp/publish/).

## <a name="mobile-development-considerations"></a>Considérations sur le développement mobile

Si le développement d’applications mobiles n’est pas fondamentalement différent du développement classique web/pour poste de travail en termes de processus ou d’architecture, certaines considérations spécifiques sont néanmoins indispensables.

### <a name="common-considerations"></a>Considérations générales

#### <a name="multitasking"></a>Multitâche

Il existe deux problématiques importantes pour le multitâche (avoir plusieurs applications qui s’exécutent en même temps) sur un appareil mobile. D’abord, étant donné la taille limitée des écrans, il est difficile d’afficher plusieurs applications en même temps. Ainsi, les appareils mobiles ne peuvent montrer qu’une seule application au premier plan à la fois. En second lieu, le fait d’avoir plusieurs applications ouvertes effectuant des tâches peut consommer rapidement la batterie.

Chaque plateforme gère le multitâche différemment, ce que nous allons détailler plus loin.

#### <a name="form-factor"></a>Facteur de forme

Les appareils mobiles se répartissent généralement en deux catégories, les téléphones et les tablettes, avec quelques appareils hybrides entre les deux. Le développement pour ces facteurs de forme est généralement très similaire ; la conception des applications pour ceux-ci peut cependant être très différente.
Les téléphones ont un espace très limité sur l’écran et les tablettes, bien que plus grandes, sont néanmoins des appareils mobiles avec moins d’espace sur l’écran que la plupart des ordinateurs portables. Pour cette raison, les contrôles d’interface utilisateur des plateformes mobiles ont été conçus spécifiquement pour fonctionner sur des facteurs de forme plus petits.

#### <a name="device-and-operating-system-fragmentation"></a>Fragmentation de l’appareil et du système d’exploitation

Il est important de prendre en compte différents appareils tout au long de la totalité du cycle de vie de développement des logiciels :

1. **Conceptualisation et planification** : gardez à l’esprit que le matériel et les caractéristiques varient d’un appareil à l’autre, et qu’une application qui s’appuie sur certaines caractéristiques peut ne pas fonctionner correctement sur certains appareils. Par exemple, certains appareils n’ont pas de caméra : si vous créez une application de messagerie vidéo, certains appareils peuvent être en mesure de jouer des vidéos, mais pas de les filmer.
1. **Conception** : lors de la conception de l’expérience utilisateur d’une application, faites attention aux différentes proportions et tailles d’écran sur les appareils. En outre, lors de la conception de l’interface utilisateur d’une application, vous devez considérer différentes résolutions d’écran.
1. **Développement** : si vous utilisez une fonctionnalité dans le code, la présence de cette fonctionnalité doit toujours être testée en premier. Par exemple, avant d’utiliser une fonctionnalité de l’appareil, comme une caméra, interrogez toujours le système d’exploitation pour d’abord vérifier la présence de cette fonctionnalité. Ensuite, lors de l’initialisation de la fonctionnalité ou de l’appareil, vérifiez auprès du système d’exploitation ce qui est pris en charge sur cet appareil, puis utilisez ensuite ces paramètres de configuration.
1. **Test** : il est extrêmement important de tester l’application très tôt et souvent sur des appareils réels. Même le comportement des appareils ayant les mêmes spécifications matérielles peut varier considérablement.

#### <a name="limited-resources"></a>Ressources limitées

La puissance des appareils mobiles ne cesse de croître, mais ils ont néanmoins des capacités limitées par rapport aux ordinateurs de bureau ou aux portables. Par exemple, les développeurs pour ordinateurs de bureau ne se soucient généralement pas des capacités mémoire ; ils sont habitués à disposer d’une quantité importante de mémoire physique et de mémoire virtuelle, tandis que sur les appareils mobiles, vous pouvez rapidement consommer toute la mémoire disponible en chargeant simplement quelques images en haute qualité.

En outre, les applications sollicitant beaucoup le processeur, comme les jeux ou la reconnaissance de texte, peuvent également pénaliser l’UC de l’appareil mobile et nuire à ses performances.

Pour ces raisons, il est important de coder de façon judicieuse, et de déployer très tôt et souvent sur des appareils réels pour en vérifier la réactivité.

### <a name="ios-considerations"></a>Considérations pour iOS

#### <a name="multitasking"></a>Multitâche

Le multitâche est étroitement contrôlé dans iOS, et votre application doit se conformer à un certain nombre de règles et de comportements quand une autre application passe au premier plan, sans quoi votre application sera arrêtée par iOS.

#### <a name="device-specific-resources"></a>Ressources propres à l’appareil

Pour un facteur de forme particulier, le matériel peut varier considérablement entre différents modèles. Par exemple, certains appareils ont une caméra à l’arrière, mais d’autres ont également une caméra à l’avant, alors que d’autres en sont complètement dépourvus.

Certains appareils plus anciens (les iPhone 3G et leurs prédécesseurs) n’autorisent même pas le multitâche.

En raison de ces différences entre les modèles d’appareils, il est important de vérifier la présence d’une fonctionnalité avant de tenter de l’utiliser.

#### <a name="os-specific-constraints"></a>Contraintes propres au système d’exploitation

Pour garantir que les applications sont réactives et sécurisées, iOS applique un certain nombre de règles auxquelles les applications doivent se conformer. En plus des règles relatives au multitâche, il existe un certain nombre de méthodes d’événement dont votre application doit retourner dans une quantité de temps déterminée, sinon elle est arrêtée par iOS.

Il faut aussi noter que les applications s’exécutent également dans ce qui s’appelle un bac à sable, qui est un environnement appliquant des contraintes de sécurité qui limitent ce à quoi votre application peut accéder. Par exemple, une application peut lire et écrire dans son propre répertoire, mais si elle tente d’écrire dans le répertoire d’une autre application, elle est arrêtée.

### <a name="android-considerations"></a>Considérations pour Android

#### <a name="multitasking"></a>Multitâche

Le multitâche dans Android a deux composants. Le premier est le cycle de vie des activités. Chaque écran d’une application Android est représenté par une activité, et il existe un ensemble spécifique d’événements qui se produisent quand une application est placée en arrière-plan ou passe au premier plan. Les applications doivent respecter ce cycle de vie pour créer des applications réactives et conformes. Pour plus d’informations, consultez le guide [Cycle de vie des activités](~/android/app-fundamentals/activity-lifecycle/index.md).

Le deuxième composant du multitâche dans Android est l’utilisation de services.
Les services sont des processus d’exécution longue qui existent indépendamment d’une application et qui sont utilisés pour exécuter des processus quand l’application est à l’arrière-plan. Pour plus d’informations, consultez le guide [Création de services](~/android/app-fundamentals/services/index.md).

#### <a name="many-devices-and-many-form-factors"></a>Nombreux appareils et facteurs de forme

Google n’impose aucune limite sur les appareils qui peuvent exécuter le système d’exploitation Android. Ce paradigme ouvert aboutit à environnement de produits constitué d’une multitude d’appareils différents présentant une grande hétérogénéité en termes de matériel, de résolutions et de proportions d’écran, de fonctionnalités des appareils et de capacités.

En raison de la fragmentation extrême des appareils Android, la plupart des personnes choisissent en priorité les 5 ou 6 appareils les plus populaires pour la conception et les tests.

#### <a name="security-considerations"></a>Considérations relatives à la sécurité

Dans le système d’exploitation Android, toutes les applications s’exécutent sous une identité distincte et isolée, avec des autorisations limitées. Par défaut, les applications ne peuvent faire que très peu de choses. Par exemple, sans autorisations spéciales, une application ne peut pas envoyer un message texte, déterminer l’état du téléphone ou même accéder à Internet ! Pour accéder à ces fonctionnalités, les applications doivent spécifier les autorisations souhaitées dans un fichier manifeste de l’application et, au moment de leur installation, le système d’exploitation lit ces autorisations, notifie l’utilisateur que l’application demande ces autorisations, puis permet à l’utilisateur de continuer ou d’annuler l’installation.
Ceci est une étape essentielle dans le modèle de distribution Android en raison du modèle ouvert de store d’applications, étant donné que les applications ne sont pas organisées comme elles le sont par exemple pour iOS. Pour obtenir la liste des autorisations des applications, consultez l’article de référence [Manifest.permission](https://developer.android.com/reference/android/Manifest.permission.html) dans la documentation d’Android.

### <a name="windows-considerations"></a>Considérations Windows

#### <a name="multitasking"></a>Multitâche

Le multitâche dans UWP a également deux parties : le cycle de vie pour les pages et les applications, et les processus d’arrière-plan. Chaque écran d’une application est une instance d’une classe Page, qui a des événements associés à un état actif ou inactif (avec des règles spéciales pour gérer l’état inactif ou « désactivé »).

La deuxième partie fournit des agents d’arrière-plan pour le traitement des tâches même quand l’application n’est pas en cours d’exécution au premier plan.

#### <a name="device-capabilities"></a>Fonctionnalités de l’appareil

Bien que le matériel UWP soit relativement homogène, certains composants restent néanmoins secondaires et nécessitent donc un traitement spécial lors du codage. Les fonctionnalités matérielles secondaires sont notamment l’appareil photo, la boussole et le gyroscope. Il existe également une classe spéciale de mémoire en quantité limitée (256 Mo), qui nécessite une attention particulière ; les développeurs peuvent également décider de ne pas prendre en charge la mémoire en quantité limitée.

#### <a name="security-considerations"></a>Considérations relatives à la sécurité

Pour plus d’informations sur les considérations de sécurité importantes dans UWP, reportez-vous à la documentation sur la [sécurité](https://docs.microsoft.com/windows/uwp/security/).

## <a name="summary"></a>Récapitulatif

Ce guide est une introduction au cycle de vie de développement de logiciels pour les appareils mobiles. Il contient des considérations générales pour créer des applications mobiles et passe en revue un certain nombre de considérations spécifiques aux plateformes, notamment la conception, les tests et le déploiement.

## <a name="next-steps"></a>Étapes suivantes

- [Qu’est-ce que Xamarin ?](~/cross-platform/get-started/introduction-to-mobile-development.md)
- [Bien démarrer avec Xamarin](~/get-started/index.yml)
- [Partage de code entre plateformes](~/cross-platform/app-fundamentals/index.md)
