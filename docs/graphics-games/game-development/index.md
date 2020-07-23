---
title: Présentation du développement de jeux avec Xamarin
description: Ce document fournit une vue d’ensemble du développement de jeux avec Xamarin, qui décrit à la fois comment les jeux sont effectués et un échantillon des technologies disponibles pour une utilisation avec Xamarin. iOS et Xamarin. Android.
ms.prod: xamarin
ms.assetid: 0E3CDCD2-FBE4-49F5-A70E-8A7B937BAF1D
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: 8f7ec5175895a31746710fad9ea7372052b698ff
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936485"
---
# <a name="introduction-to-game-development-with-xamarin"></a>Présentation du développement de jeux avec Xamarin

Le développement de jeux peut être très passionnant, en particulier en raison de la facilité de publication de votre travail sur les plateformes mobiles. Cet article aborde les concepts et les technologies liés au développement de jeux qui vous aideront à créer des jeux, que votre objectif soit de créer un jeu AAA de haute qualité ou simplement de programmer pour vous amuser.

Cet article aborde les thèmes suivants :

- **Concepts de programmation sans** jeu : nous allons explorer certains concepts qui sont propres au développement de jeux, ou qui sont partagés avec d’autres types de développement, mais méritent d’être mis en évidence ici en raison de leur importance.
- **Équipe de développement de jeux** : cette section présente les différents rôles d’une équipe de développeurs de jeux.
- **Création d’une idée de jeu** : cette section peut vous aider à créer une nouvelle idée de jeu, la première étape dans la création d’un nouveau jeu.
- **Technologie de développement de jeux** : ici, nous allons répertorier certaines des technologies multiplateforme disponibles qui peuvent améliorer votre productivité en tant que développeur de jeux.

## <a name="game-vs-non-game-programming-concepts"></a>Concepts de programmation de jeux et de non-jeux

Les programmeurs qui se déplacent dans le développement de jeux sont souvent confrontés à de nouveaux concepts et modèles de développement. Cette section présente un aperçu général de certains de ces concepts.

### <a name="the-game-loop"></a>La boucle du jeu

Un jeu classique nécessite un déplacement ou une modification constant sur l’écran en réponse à l’interaction de l’utilisateur et à la logique de jeu automatique. Cela est possible grâce à ce qui est généralement appelé « *boucle de jeu*». Une boucle de jeu est un type d’instruction de bouclage (par exemple une boucle while) qui s’exécute à une fréquence très élevée, telle que 30 ou 60 *images par seconde*.

Voici un diagramme d’une boucle de jeu simple :

![Il s’agit d’un diagramme d’une boucle de jeux simple](images/image1.png)

Les technologies que nous abordons ci-dessous feront abstraction de la boucle while réelle, mais en dépit de cette abstraction, le concept des mises à jour de chaque frame sera présent.

Les performances du code peuvent être prioritaires dans les jeux même les plus simples. Par exemple : une fonction qui prend 10 millisecondes pour s’exécuter peut avoir un impact significatif sur les performances d’un jeu, en particulier si elle est appelée plusieurs fois par image. Si votre jeu s’exécute à 30 images par seconde, cela signifie que chaque image doit s’exécuter en moins de 33 millisecondes. En revanche, une telle fonction peut même être perceptible si elle s’exécute uniquement en réponse à un clic de bouton dans une application non Game.

Les types de logique courants qui peuvent être exécutés pour chaque image sont les suivants :

- **Lecture** de l’entrée : le jeu peut avoir besoin de vérifier si l’utilisateur a interagi avec le jeu en vérifiant le matériel d’entrée, tel que l’écran tactile, le clavier, la souris ou le contrôleur de jeu.
- **Déplacement** : les objets qui se déplacent d’un emplacement à un autre déplacent généralement une très petite quantité chaque cadre pour offrir l’illusion de mouvement fluide.
- **Collision** : de nombreux jeux nécessitent des tests fréquents de savoir si différents objets se chevauchent ou croisent. Nous traiterons des collisions plus en détail dans une section ultérieure de cet article. Le mouvement et les collisions peuvent être gérés par un système de simulation physique dédié.
- **Vérification des conditions spécifiques aux jeux** : l’état d’un jeu peut être contrôlé par certaines conditions, par exemple si le joueur a gagné suffisamment de points ou si le temps alloué s’est écoulé.
- **Comportement ai** : logique d’image qui peut être utilisée pour contrôler le comportement des objets qui ne sont pas contrôlés par le joueur, tels que la patrouille d’un ennemi ou le mouvement des conducteurs de l’adversaire autour d’un Racetrack.
- **Rendu** : la plupart des jeux mettent à jour ce qui est affiché à l’écran dans chaque cadre. Cela peut être fait en réponse à des modifications qui ont un impact sur le jeu (par exemple, un déplacement d’un caractère à travers un niveau) ou simplement pour fournir une finition visuelle (telle que la neige ou les icônes animées).

N’oubliez pas que la plupart des activités listées ci-dessus peuvent modifier l’état de l’application entière, tandis que de nombreuses applications non Game ont tendance à changer d’État en réponse aux événements déclenchés.

### <a name="content-loading-and-unloading"></a>Chargement et déchargement de contenu

Le chargement et le déchargement (ou suppression) du contenu manuellement peuvent être nécessaires en fonction de la technologie que vous utilisez pour le développement. Le chargement et le déchargement manuels des ressources peuvent être nécessaires pour plusieurs raisons :

- Le chargement des ressources peut prendre beaucoup de temps en fonction de la longueur d’une image unique. Certaines ressources peuvent même prendre quelques secondes pour être chargées, ce qui perturberait gravement l’expérience en cas de chargement intermédiaire. Si la durée de chargement est particulièrement longue (par exemple, plus d’une seconde ou deux), vous pouvez afficher un écran de chargement animé ou une barre de progression.
- Les ressources peuvent consommer beaucoup de RAM, nécessitant une gestion active de ce qui est chargé pour tenir dans ce qui est fourni par les plateformes cibles du jeu.
- Les jeux peuvent avoir besoin d’afficher plus de ressources que la RAM ne peut en contenir. Les jeux « Open World » incluent souvent des environnements de grande taille que les lecteurs peuvent parcourir en toute transparence, c’est-à-dire sans écran de chargement. Dans ce cas, vous devrez peut-être créer un système personnalisé pour la diffusion en continu de contenu dans et la gestion de l’utilisation de la mémoire.

Les formats de fichier personnalisés peuvent nécessiter un traitement au moment du chargement, ce qui nécessite du code de chargement personnalisé.

### <a name="math"></a>Math

De nombreux jeux nécessitent des mathématiques plus avancées que les applications sans jeu. Bien entendu, le niveau de maths dépend de la complexité du jeu. Dans les jeux en 3D généraux, vous avez besoin de plus de mathématiques que de 2D. Heureusement, vous pouvez toujours commencer à utiliser des jeux simples et vous familiariser avec vos déplacements. Le développement de jeux peut être un excellent moyen d’apprendre les mathématiques !

Si vous êtes familiarisé avec le plan cartésien, c’est-à-dire en utilisant des coordonnées X et Y pour positionner les objets, vous vous en êtes suffisamment suffisamment pour commencer le développement de jeux. L’exemple suivant montre un plan cartésien avec un point positif Y pointant vers le haut :

![Cela montre un plan cartésien avec un Y positif pointant vers le haut](images/image2.png)

> [!IMPORTANT]
> Certains moteurs/API utilisent un système de coordonnées dans lequel l’extension de la valeur Y d’un objet le déplacera, tandis que d’autres systèmes utilisent un système de coordonnées dont l’ordonnée positive est. Gardez cela à l’esprit si vous passez d’un système à un autre.
Les fonctions trigonométriques (telles que le sinus et le cosinus) sont couramment utilisées dans les jeux 2D qui implémentent toute forme de rotation.

Si vous envisagez de créer un jeu 3D, vous devrez probablement être familiarisé avec les concepts de l’algèbre linéaire (pour la rotation et le déplacement dans l’espace 3D), ainsi qu’avec un calcul (pour l’implémentation de l’accélération).

### <a name="content-pipelines"></a>Pipelines de contenu

Le terme « *pipeline de contenu* » fait référence au processus qu’un fichier doit obtenir de son format lorsqu’il est créé (par exemple, un fichier image. png) dans son format final lorsqu’il est utilisé dans un jeu. Le format de fin dépend du type de contenu utilisé, ainsi que de la technologie utilisée pour présenter le contenu.

Certains pipelines de contenu peuvent être très rapides et ne nécessitent pas de travail manuel. Par exemple, la plupart des moteurs de jeu et des API peuvent charger le format de fichier. png dans son format non traité. En revanche, les formats plus compliqués (tels que les modèles 3D) peuvent devoir être traités dans un format différent avant d’être chargés, et ce traitement peut prendre un certain temps en fonction de la taille et de la complexité de la ressource.

## <a name="game-development-teams"></a>Équipes de développement de jeux

Le développement de jeux introduit de nouveaux rôles et titres pour les personnes impliquées dans le processus. La plupart des développeurs de jeux ne sont pas en mesure de répondre à l’ensemble des compétences requises pour libérer un jeu complet. il existe donc un certain nombre de disciplines. N’oubliez pas qu’il ne s’agit pas d’une liste complète des domaines de développement, mais seulement de quelques-uns des plus courants.

- **Programmeur** : la plupart des personnes lisant cet article seront classées dans cette catégorie. Le rôle d’un programmeur dans le développement de jeux est semblable au rôle d’un programmeur dans une application non Game. Les responsabilités incluent l’écriture d’une logique pour contrôler le déroulement d’un jeu, le développement de systèmes pour les tâches courantes dans le contexte d’un projet donné, l’ajout et l’affichage de contenu et, bien sûr, la résolution des bogues.
- **artiste 2D** : les artistes 2D sont responsables de la création de *ressources 2D*. Il s’agit notamment des fichiers image pour l’interface utilisateur graphique, les particules, les environnements et les caractères du jeu. Si le jeu que vous développez est 3D, alors les artistes 2D peuvent ne pas être responsables des environnements et des caractères. Vous pouvez trouver un art gratuit pour votre jeu à l’adresse [http://opengameart.org/](http://opengameart.org/) .
- **artistes 3D** : les artistes 3D sont responsables de la création de *ressources 3D*. Celles-ci incluent des modèles 3D pour les environnements, les caractères et les propriétés (mobilier, plantes et autres objets inanimés). Certaines équipes font la différence entre les artistes 3D et les animateurs 3D en fonction de la taille de l’équipe. Vous pouvez trouver un art 3D gratuit pour votre jeu à l’adresse [http://opengameart.org/](http://opengameart.org/) .
- **Concepteur de jeux** : les concepteurs de jeux sont chargés de définir la façon dont le jeu est joué. Cela peut inclure des décisions de haut niveau, telles que la définition du jeu, l’objectif global du jeu et la façon dont un joueur progresse dans le jeu. Les concepteurs de jeux peuvent également être impliqués dans des décisions très détaillées telles que le mappage d’entrées à des actions, la définition de coefficients pour le déplacement ou les niveaux et la conception de la disposition du niveau. N’oubliez pas que le *Concepteur* de termes peut faire référence à un concepteur de jeux ou à un concepteur visuel en fonction du contexte.
- **Sound Designer** : les concepteurs de sons sont responsables des ressources audio d’un jeu. Certaines équipes peuvent faire la différence entre les personnes responsables de la création d’effets sonores et de compositeurs, tandis que les petites équipes peuvent avoir une seule personne responsable de l’audio.

## <a name="creating-a-game-idea"></a>Création d’une idée de jeu

La conception d’un jeu peut paraître facile : une fois que la seule exigence est « faire un plaisir ». Malheureusement, de nombreux développeurs se trouvent en perte lorsqu’il est temps de créer une idée à partir de laquelle lancer le développement.

La discipline de la conception de jeux n’est pas facilement expliquée et nécessite une pratique pour s’améliorer de la même façon que l’art ou la programmation, mais cette section peut vous aider à démarrer le chemin.

Les nouveaux développeurs doivent démarrer petit. Il peut être difficile de résister à la tentation de recréer un jeu vidéo moderne, mais les jeux de plus petite taille peuvent être un meilleur environnement d’apprentissage et la progression plus rapide permet d’obtenir une expérience plus enrichissante.

De nombreux jeux, à la fois pour l’apprentissage et pour les jeux commerciaux, commencent à améliorer ou à modifier un jeu existant. L’une des méthodes permettant de générer des idées consiste à examiner d’autres jeux pour l’inspiration. Par exemple, vous pouvez envisager un jeu qui vous plaît et essayer d’identifier les caractéristiques du jeu pour l’amuser. Il peut s’agir d’une exploration, d’une maîtrise de la mécanique du jeu ou de la progression d’un récit. N’oubliez pas de prendre en compte les jeux « rétro » lorsque vous recherchez de nouvelles idées.

Une autre technique pour générer de nouvelles idées consiste à prendre en compte un genre spécifique, comme les jeux de puzzle, les jeux de stratégie ou les plateformes. Un genre familier au développeur peut fournir un bon point de départ.

La recréation de jeux existants est également une expérience pédagogique, bien que cela puisse limiter la viabilité commerciale du produit fini. Le processus de création d’un jeu, même un clone précis, offre une expérience pédagogique précieuse.

## <a name="game-development-technology"></a>Technologie de développement de jeux

Les développeurs qui utilisent Xamarin. Android et Xamarin. iOS ont une large gamme de technologies disponibles pour faciliter le développement de jeux. Cette section présente certaines des solutions interplateformes les plus populaires.

### <a name="monogame"></a>MonoGame

Monogame est une version open source et multiplateforme de l’API XNA de Microsoft. Vous pouvez utiliser monojeu pour créer des jeux pour iOS, Android, Mac OS X, Linux, Windows, Windows RT, PS4, PSVita, Xbox One et Switch.

Le monojeu n’est techniquement pas un moteur de jeu, mais plutôt une API de développement de jeux. Cela signifie que l’utilisation d’un monojeu requiert directement la gestion des objets de jeu, le dessin manuel d’objets et l’implémentation d’objets courants tels que des appareils photo et des *graphiques de scène* (la hiérarchie parent-enfant entre les objets de jeu).

Le monojeu n’offre pas d’environnement de développement visuel standard, et l’utilisation de monojeu nécessite des connaissances en programmation.

Les exemples notables de jeux utilisant un monojeu sont les suivants :

FEZ:

![FEZ](images/image7.png)

Bastion

![Bastion](images/image8.jpg)

Pour commencer à travailler avec monojeu, accédez à nos [guides monojeu](~/graphics-games/monogame/index.md).

### <a name="urhosharp"></a>UrhoSharp

UrhoSharp est un moteur 3D et 2D de haut niveau, qui peut être utilisé pour créer des scènes 3D et 2D animées pour vos applications à l’aide de géométries, de matériaux, d’éclairages et de caméras.

![UrhoSharp est un moteur 3D et 2D de haut niveau, qui peut être utilisé pour créer des scènes 3D et 2D animées](images/urhosharp.gif)

Consultez les [guides UrhoSharp](~/graphics-games/urhosharp/index.md) pour commencer.

### <a name="additional-technology"></a>Technologies supplémentaires

Les technologies mises en surbrillance ci-dessus ne sont qu’un exemple des technologies disponibles. Les autres technologies notables sont les suivantes :

- **Sprite Kit** – Xamarin fournit la prise en charge de l’infrastructure de jeu de sprites du Kit Apple, qui vous permet d’accéder à toutes les fonctionnalités de l’API native. Étant donné que le kit de sprites est une technologie créée par Apple, il offre une intégration profonde avec le reste de l’écosystème iOS. Bien entendu, le kit sprite n’est pas multiplateforme et ne peut donc pas être utilisé sur Android. Pour plus d’informations sur l’utilisation du kit de sprites, consultez ce billet :[https://blog.xamarin.com/make-games-with-xamarin.ios-and-sprite-kit/](https://blog.xamarin.com/make-games-with-xamarin.ios-and-sprite-kit/)
- **Scène Kit** – Xamarin fournit également la prise en charge de l’infrastructure du kit de scène d’Apple, qui simplifie l’implémentation de graphiques 3D dans les applications iOS. Scene Kit est également une technologie fournie par Apple. il a donc à la fois des considérations relatives à l’intégration et à la plate-forme, mentionnées ci-dessus pour le kit Sprite. Pour plus d’informations sur le kit de scène, consultez ce billet :[https://blog.xamarin.com/3d-in-ios-8-with-scene-kit/](https://blog.xamarin.com/3d-in-ios-8-with-scene-kit/)
- **OpenTK –** OpenTK (qui signifie Open Tool Kit) fournit un accès OpenGL de bas niveau à du matériel iOS, Apple et Mac. Pour plus d’informations sur OpenTK, consultez la page principale à l’adresse suivante :[https://opentk.net/](https://opentk.net/)

## <a name="related-links"></a>Liens associés

- [Guides monojeu](~/graphics-games/monogame/index.md)
- [Guides UrhoSharp](~/graphics-games/urhosharp/index.md)
