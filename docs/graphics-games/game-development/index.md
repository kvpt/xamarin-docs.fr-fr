---
title: Introduction au développement de jeux avec Xamarin
description: Ce document fournit une vue d’ensemble du développement de jeux avec Xamarin, qui décrivent comment les jeux sont effectuées et un échantillonnage des technologies disponibles pour une utilisation avec Xamarin.iOS et Xamarin.Android.
ms.prod: xamarin
ms.assetid: 0E3CDCD2-FBE4-49F5-A70E-8A7B937BAF1D
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: 0fa4dbbf256e005fe17841f06acf1338197784b0
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832283"
---
# <a name="introduction-to-game-development-with-xamarin"></a>Introduction au développement de jeux avec Xamarin

Développement de jeux peut être très intéressant, étant donné notamment comment il est facile de publier votre travail sur des plateformes mobiles. Cet article aborde les concepts et technologies liées au développement de jeux qui vous aideront à créer des jeux, si votre objectif est de créer un AAA de haute qualité jeu ou simplement au programme pour le plaisir.

Cet article aborde les rubriques suivantes :

- **Jeux et les concepts de programmation hors jeu** – nous explorerons certains concepts qui sont soit unique au développement de jeux, ou sont partagés avec d’autres types de développement mais méritent d’importance ici en raison de leur importance.
- **Équipe de développement de jeux** – cette section présente les différents rôles dans une équipe de développeurs de jeux.
- **Création d’une idée de jeu** – cette section permettre vous aider à créer une nouvelle idée de jeu : la première étape dans la création d’un nouveau jeu.
- **Technologie de développement de jeux** – ici, nous allons répertorier quelques-unes des technologies inter-plateformes disponibles qui peuvent améliorer votre productivité en tant que développeur de jeux.

## <a name="game-vs-non-game-programming-concepts"></a>Visual Studio de jeu. Concepts de programmation hors jeu

Les programmeurs de déplacement dans le développement de jeux sont souvent confrontés à des nouveaux concepts et modèles de développement. Cette section présente une vue d’ensemble de certaines de ces concepts.

### <a name="the-game-loop"></a>La boucle du jeu

Un jeu standard requiert le déplacement des constantes ou modification de se produire sur l’écran en réponse à l’interaction de l’utilisateur et une logique de jeu automatique. Ceci se fait via ce qui est généralement appelé un *boucle game*. Une boucle de jeu est un type d’instruction (par exemple, une boucle while) qui s’exécute très très fréquemment, telles que 30 ou 60 de bouclage *images par seconde*.

Voici un diagramme d’une simple boucle de jeu :

![](images/image1.png "Il s’agit d’un diagramme d’une simple boucle de jeu")

Les technologies que nous abordons ci-dessous seront clarifient la boucle while réelle, mais en dépit de cette abstraction le concept des mises à jour de chaque image sera présent.

Performances du code peut prendre la priorité dans la même la plus simple de jeux. Par exemple : une fonction qui prend de 10 millisecondes pour exécuter peut avoir un impact significatif sur les performances d’un jeu – en particulier si elle est appelée plusieurs fois par trame. Si votre jeu s’exécute à 30 images par seconde puis cela signifie que chaque image doit s’exécuter en millisecondes sous 33. En revanche, une telle fonction même peut-être pas perceptible si elle s’exécute uniquement en réponse à un clic de bouton dans une application hors jeu.

Types de logique qui peut être effectuée sur chaque-image courants sont les suivantes :

- **Lecture d’entrée** – le jeu peut-être vérifier si l’utilisateur interagisse avec le jeu en vérifiant le matériel d’entrée, tels que l’écran tactile, clavier, souris ou contrôleur de jeu.
- **Le déplacement des** – objets quels déplacement depuis un seul endroit à un autre déplacera généralement un très petit montant chaque trame pour donner une impression de mouvement fluide.
- **Collision** : de nombreux jeux requièrent le test fréquentes de différents objets sont chevauchement ou intersection. Nous aborderons la collision de façon plus approfondie dans une section plus loin dans cet article. Le déplacement et la collision peuvent être gérées par un système de simulation physique dédié.
- **La vérification des conditions de jeu spécifiques** : l’état d’un jeu peut être contrôlé à certaines conditions, telles que si le joueur a gagné suffisamment de points ou si le délai imparti a exécuté.
- **Comportements d’intelligence artificielle** – logique de chaque image qui peut être utilisée pour contrôler le comportement des objets qui ne sont pas contrôlées par le lecteur, telles que le patrouiller d’un ennemi ou le déplacement des pilotes adversaire autour d’un ovale.
- **Rendu** – la plupart des jeux met à jour ce qui est affiché dans l’écran chaque trame. Cela peut être effectuée en réponse aux modifications qui ont l’impact sur le jeu (par exemple, le déplacement d’un niveau d’un caractère) ou simplement à peaufinage visual (par exemple, des chutes de neige ou des icônes animées).

N’oubliez pas que de nombreuses activités répertoriées ci-dessus peuvent modifier l’état de l’application entière, tandis que de nombreuses applications hors jeu ont tendance à changer d’état en réponse aux événements déclenchés.

### <a name="content-loading-and-unloading"></a>Chargement de contenu et de déchargement

Contenu manuellement le chargement et déchargement (ou en cours de suppression) peut-être être nécessaires selon la technologie que vous utilisez dans le développement. Le chargement et déchargement des ressources manuellement peuvent être nécessaires pour plusieurs raisons :

- Ressources peuvent prendre beaucoup de temps à charger par rapport à la longueur d’une seule image. Certaines ressources peuvent même avoir secondes à charger, ce qui risque de perturber sérieusement l’expérience si chargé mid jeu. Si le temps de chargement est particulièrement long (par exemple, plus d’une ou deux secondes), vous souhaiterez afficher un texte animé le chargement d’écran de barre de progression.
- Ressources peuvent consommer beaucoup de RAM, nécessitant une gestion active de ce qui est chargé à la taille de ce qui est fourni par les plateformes de cible du jeu.
- Jeux deviez afficher plus de ressources ne tient pas dans la mémoire RAM. Jeux de « Ouvrir le monde » incluent souvent des environnements de grande taille quels lecteurs peuvent naviguer en toute transparence : c’est avec aucun écran de chargement. Dans ce cas, vous devrez peut-être créer un système personnalisé pour le contenu de diffusion en continu dans et l’utilisation de la gestion de la mémoire.

Formats de fichiers personnalisés peut-être le traitement au moment du chargement, nécessitant le code de chargement personnalisé.

### <a name="math"></a>Math

De nombreux jeux requièrent des mathématiques plus avancées que les applications hors jeu. Bien entendu, le niveau de mathématiques dépend de la complexité du jeu. En général, des jeux en 3D nécessitent mathématiques plus que 2D. Heureusement, vous pourrez toujours prise en main des jeux simples et découvrez comme vous accédez. Développement de jeux peut être un moyen efficace d’apprendre les mathématiques !

Si vous êtes familiarisé avec le plan cartésien : qui est à l’aide de coordonnées X et Y pour positionner les objets –, puis vous en savez suffisamment prise en main le développement de jeux. L’exemple suivant montre un plan cartésien avec positive Y pointant vers le haut :

![](images/image2.png "Cet exemple montre un plan cartésien avec positive Y pointant vers le haut")

> [!IMPORTANT]
> Certains moteurs/API utilisent un système de coordonnées d’où augmentant la valeur de Y d’un objet sera déplacez-le vers le bas, tandis que les autres systèmes utilisent un système de coordonnées dans lequel les Y positif. Gardez cela à l’esprit si vous déplacez entre les systèmes.
Fonctions trigonométriques (par exemple, le sinus et le cosinus) sont couramment utilisées dans des jeux 2D qui implémentent toutes les formes de rotation.

Si vous envisagez de créer un jeu 3D vous aurez probablement besoin de se familiariser avec les concepts d’algèbre linéaire (de rotation et de déplacement dans l’espace 3D), ainsi que certains calcul (pour l’implémentation d’accélération).

### <a name="content-pipelines"></a>Pipelines de contenu

Le terme *pipeline de contenu* fait référence au processus d’un fichier nécessaire pour obtenir à partir de son format lorsque créé (par exemple, un fichier image .png) à son format final lorsqu’il est utilisé dans un jeu. Le format fin varie sur lequel le type de contenu est utilisé, ainsi que de laquelle technologie est utilisée pour présenter le contenu.

Contenu pipelines peuvent être très rapide et ne nécessiter aucun effort manuel. Par exemple, la plupart des moteurs de jeux et des API peut charger le format de fichier .png dans son format non traité. En revanche, des formats plus complexes (tels que les modèles 3D) devront peut-être être traités dans un autre format avant leur chargement, et ce traitement peut prendre un certain temps selon la taille et la complexité de la ressource.

## <a name="game-development-teams"></a>Équipes de développement de jeux

Développement de jeux introduit les nouveaux rôles et des titres pour les personnes impliquées dans le processus. La plupart des développeurs de jeux ne sont pas en mesure de satisfaire un large éventail de compétences nécessaires pour libérer un jeu complet, donc un nombre de disciplines existe. N’oubliez pas qu’il ne s’agit pas d’une liste complète des domaines de développement – quelques-uns des plus courants.

- **Programmeur** : la plupart des gens lisant cet article appartiennent à cette catégorie. Le rôle d’un programmeur dans le développement de jeux est semblable au rôle d’un programmeur dans une application hors jeu. Responsabilités incluent écrire une logique pour contrôler le flux d’un jeu, développement de systèmes pour les tâches courantes dans le contexte d’un projet donné, ajout et affichage du contenu et – évidemment – résolution des bogues.
- **Artiste 2D** – artistes 2D sont chargés de créer *ressources 2D*. Vous recourrez notamment des fichiers image pour l’interface graphique utilisateur, les particules, les environnements et les caractères du jeu. Si le jeu que vous développiez est 3D, artistes 2D ne peuvent pas être responsables pour les environnements et les caractères. Vous pouvez trouver gratuit art pour votre jeu à [ http://opengameart.org/ ](http://opengameart.org/) .
- **Artistes 3D** – artistes 3D sont chargés de créer *composants 3D*. Ceux-ci incluent des modèles 3D pour les environnements, de caractères et de propriétés (meubles plantes et autres objets inanimés). Certaines équipes de faire la distinction entre la 3D et des animations 3D selon la taille de l’équipe. Vous pouvez trouver gratuit art 3D pour votre jeu à [ http://opengameart.org/ ](http://opengameart.org/) .
- **Concepteur de jeux** – concepteurs de jeux sont chargés de déterminer comment le jeu est lu. Cela peut inclure des décisions de haut niveau tels que le paramètre de jeu, l’objectif global de jeu, et comment faire évoluer un lecteur par le biais du jeu. Concepteurs de jeux peuvent également être impliqués dans les décisions très détaillées comme entrée de mappage à des actions, définition des coefficients de mouvement ou au niveau des sauvegardes et la conception de disposition de niveau. N’oubliez pas que le terme *concepteur* peuvent faire référence à un concepteur de jeux ou un concepteur visuel en fonction du contexte.
- **Son concepteur** – concepteurs audio sont responsables de ressources audio d’un jeu. Certaines équipes peuvent faire la distinction entre les personnes chargées de la création d’effets sonores et compositeurs, tandis que les équipes plus petites peuvent avoir un seul individu responsable de tous les fichiers audio.

## <a name="creating-a-game-idea"></a>Création d’une idée de jeu

Conception d’un jeu peut apparaître pour être facile à réaliser – après tout constitue la seule exigence est « faire quelque chose d’amusant. » Malheureusement, de nombreux développeurs se retrouvent à perte lorsqu’il s’agit pour créer une idée de lancer le développement.

La discipline de conception de jeux n’est pas facilement expliquée et nécessite des exercices pratiques pour améliorer tout comme l’art ou est de programmation, mais cette section permettre vous aider à commencer le chemin d’accès.

Les nouveaux développeurs doivent commencer petits. Il peut être difficile de résister à la tentation de refaire un jeu vidéo volumineux, modern, mais les jeux plus petits peuvent être un meilleur environnement de formation et rend de la progression plus rapide pour une expérience plus enrichissante.

De nombreux jeux, à la fois à des fins de jeux d’apprentissage ainsi que la commerciales, commencent en tant qu’amélioration ou modification d’un jeu existant. Permet de générer des idées consiste à examiner d’autres jeux d’inspiration. Par exemple, vous pouvez envisager un jeu que vous aimez personnelle et essayez d’identifier les caractéristiques de l’exécution du jeu rendent amusant. Il peut être l’exploration, la maîtrise des mécanismes du jeu, ou progresse via un récit. N’oubliez pas de prendre en compte également les jeux « rétro » lorsque vous recherchez de nouvelles idées.

Une autre technique pour la génération de nouvelles idées consiste à prendre en compte un genre spécifique, telles que les jeux de puzzle, jeux de stratégie ou platformers. Un genre familier pour le développeur peut fournir un bon point de départ.

Renouveler les jeux existants est également une expérience de formation, bien que cela peut limiter la viabilité commerciale du produit fini. Le processus de création d’un jeu, même celui qui est un clone précis, offre une expérience de formation précieuse.

## <a name="game-development-technology"></a>Technologie de développement de jeux

Les développeurs à l’aide de Xamarin.Android et Xamarin.iOS ont un large éventail de technologies disponibles pour faciliter le développement de jeux. Cette section décrit certaines des solutions inter-plateformes populaires.

### <a name="monogame"></a>MonoGame

MonoGame est un open source, entre les versions de plateforme de l’API de Microsoft XNA. MonoGame peut être utilisé pour créer des jeux pour iOS, Android, Mac OS X, Linux, Windows, Windows RT, PS4, PSVita, Xbox One et commutateur.

MonoGame est techniquement pas un moteur de jeu, mais plutôt un développement de jeux API. Cela signifie que le travail avec MonoGame nécessite directement la gestion des objets de jeu, manuellement dessiner des objets et l’implémentation des objets communs tels que des caméras et *graphiques de scène* (la hiérarchie parent-enfant entre les objets de jeu).

MonoGame n’offre pas d’un environnement de développement visual standard afin de travailler avec MonoGame nécessite de connaissances en programmation.

Voici quelques exemples importants de jeux à l’aide de MonoGame :

CARTE FEZ :

![](images/image7.png "FEZ")

Bastion :

![](images/image8.jpg "Bastion")

Pour commencer à travailler avec MonoGame, rendez-vous sur notre [MonoGame Guides](~/graphics-games/monogame/index.md).

### <a name="urhosharp"></a>UrhoSharp

UrhoSharp est un moteur 2D et 3D du haut niveau des inter-plateformes qui peut être utilisé pour créer des scènes 3D et 2D animés pour vos applications à l’aide des géométries, des documents, des lumières et des caméras.

![](images/urhosharp.gif "UrhoSharp est un moteur 2D et 3D du haut niveau des inter-plateformes qui peut être utilisé pour créer des scènes 2D et 3D animés")

Découvrez le [UrhoSharp Guides](~/graphics-games/urhosharp/index.md) pour commencer.

### <a name="additional-technology"></a>Technologie supplémentaire

Les technologies de mise en surbrillance ci-dessus n'est qu’un échantillon des technologies disponibles. Autres technologies notables sont les suivantes :

- **Spritekit** : Xamarin prend en charge pour le framework d’Apple Spritekit jeu, ce qui vous donne accès à toutes les fonctionnalités de l’API native. Spritekit étant une technologie créée par Apple, il fournit une intégration approfondie avec le reste de l’écosystème d’iOS. Bien sûr, Spritekit n’est pas multiplateforme afin qu’il ne peut pas être utilisé sur Android. Pour plus d’informations sur l’utilisation de Spritekit, consultez ce billet :  [https://blog.xamarin.com/make-games-with-xamarin.ios-and-sprite-kit/](https://blog.xamarin.com/make-games-with-xamarin.ios-and-sprite-kit/)
- **Kit de scène** – Xamarin prend également en charge pour le framework d’Apple Scenekit, ce qui simplifie l’implémentation des graphiques 3D dans des applications iOS. Scène Kit est également la technologie fournie par Apple, par conséquent, il a l’intégration et les considérations spécifiques à la plateforme mentionnées ci-dessus pour Spritekit. Pour plus d’informations sur le Kit de scène, consultez ce billet : [https://blog.xamarin.com/3d-in-ios-8-with-scene-kit/](https://blog.xamarin.com/3d-in-ios-8-with-scene-kit/)
- **OpenTK –** OpenTK (ce qui signifie Open Tool Kit) fournit un bas niveau accès OpenGL pour iOS, Apple et Mac matériel. Pour plus d’informations sur OpenTK, consultez la page principale à :  [http://www.opentk.com/](http://www.opentk.com/)

## <a name="related-links"></a>Liens associés

- [Guides de MonoGame](~/graphics-games/monogame/index.md)
- [Guides de UrhoSharp](~/graphics-games/urhosharp/index.md)
