---
title: Entités dans CocosSharp
description: Le modèle d’entité est un moyen puissant d’organiser le code de jeu. Il améliore la lisibilité, rend le code plus facile à gérer et tire parti des fonctionnalités intégrées de parent/enfant.
ms.prod: xamarin
ms.assetid: 1D3261CE-AC96-4296-8A53-A76A42B927A8
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: 42034261c374183346c8072eb42014f43a4fe22c
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667605"
---
# <a name="entities-in-cocossharp"></a>Entités dans CocosSharp

_Le modèle d’entité est un moyen puissant d’organiser le code de jeu. Il améliore la lisibilité, rend le code plus facile à gérer et tire parti des fonctionnalités intégrées de parent/enfant._

Le modèle d’entité peut améliorer les efforts de développement avec CocosSharp via l’organisation du code améliorée. Cette procédure pas à pas est un exemple pratique montrant comment créer deux entités : une entité de livraison et d’une entité de la liste à puces. Ces entités seront des objets autonomes, ce qui signifie qu’instancié une fois qu’ils s’affichera automatiquement et effectuera la logique de déplacement en fonction de leur type. 

Ce guide couvre les rubriques suivantes :

 - Introduction aux entités de jeu
 - Générale et les types d’entités spécifique
 - Programme d’installation de projet
 - Création des classes d’entité
 - Ajout d’instances d’entité de la `GameLayer`
 - Réagir à la logique de l’entité dans le `GameLayer`

Le jeu terminé doit ressembler à ceci :

![](entities-images/image1.png "Le jeu terminé ressemblera à ceci")


## <a name="introduction-to-game-entities"></a>Introduction aux entités de jeu

Entités de jeux sont des classes qui définissent des objets nécessitant la logique de rendu, collision, physique ou l’intelligence artificielle. Heureusement, les entités présentes dans la base de code d’un jeu correspondent souvent aux objets conceptuels dans un jeu. Lorsque cela est vrai, identifiant les entités nécessaires dans un jeu est plus facilement possible. 

Par exemple, un espace à thème [em filmer jeu](https://en.wikipedia.org/wiki/Shoot_%27em_up) peut inclure les entités suivantes :

 - `PlayerShip`
 - `EnemyShip`
 - `PlayerBullet`
 - `EnemyBullet`
 - `CollectableItem`
 - `HUD`
 - `Environment`

Ces entités serait leurs propres classes dans le jeu, et chaque instance nécessiterait peu ou pas le programme d’installation au-delà de l’instanciation.


## <a name="general-vs-specific-entity-types"></a>Générale et les types d’entités spécifique

Une des premières questions auxquels sont confrontées les développeurs de jeux à l’aide d’un système de l’entité est la quantité afin de généraliser leurs entités. Le plus spécifique des implémentations définirait classes pour chaque type d’entité, même si elles diffèrent par certaines caractéristiques. Les systèmes plus générales vous combiner des groupes d’entités dans une classe et instances être personnalisées.

Par exemple, nous pouvons imaginer un jeu d’espace qui définit les classes suivantes :

 - `PlayerDogfighter`
 - `PlayerBomber`
 - `EnemyMissileShip`
 - `EnemyLaserShip`

Une approche plus généralisé consisterait à créer une classe unique pour le lecteur fourni et une seule classe d’ennemis est fourni, ce qui pouvait être configuré pour prendre en charge les types de livraison différentes. Personnalisation peut-être inclure l’image pour charger, le type d’entités de puce à créer lors de la résolution des coefficients de mouvement, et logique d’intelligence artificielle pour l’ennemi est fourni. Dans ce cas, la liste des entités peut être réduite à :

 - `PlayerShip`
 - `EnemyShip`

Bien sûr, ces types d’entité peuvent être plus généralisées en autorisant la personnalisation par instance pour contrôler le mouvement. Instances d’expédier le lecteur lit à partir de l’entrée alors que ship ennemis instances peuvent exécuter la logique de l’intelligence artificielle. Cela signifie que les entités peuvent être généralisées encore plus en une seule classe :

 - `Ship`

La généralisation peut continuer encore plus loin – un jeu peut utiliser une seule classe de base pour toutes les entités. Cette classe unique, ce qui peut être appelée `GameEntity`, serait la classe utilisée pour chaque instance d’entité dans le jeu entier, y compris les entités qui ne sont pas est fourni comme puces et les éléments pouvant être collectés (power-ups).

Cela général de la plupart des systèmes est souvent appelé un *système du composant*. Dans un tel système, entités jeu peuvent avoir des composants individuels tels que physique, IA, ou rendu composants ajoutés pour personnaliser l’apparence et le comportement. Systèmes composant pures activent maximum de souplesse et peuvent éviter des problèmes provoqués par l’utilisation de l’héritage telles que des chaînes d’héritage complexe. Comme avec d’autres généralisations, les systèmes de composant effective peuvent être difficiles de configurer et peuvent réduire l’expressivité du code.

Le niveau de la généralisation utilisé dépend de plusieurs considérations, y compris :

 - Taille du jeu – jeux plus petits peut-elle se permettre à créer des classes spécifiques, tandis que les jeux plus volumineux peuvent être difficiles à gérer avec un grand nombre de classes.
 - Data driven development-les jeux qui s’appuient sur les données (images, des modèles 3D et les fichiers de données tels que JSON ou XML) peuvent tirer parti de l’avoir généralisé des types d’entités et les caractéristiques en fonction des données de configuration. Il s’agit notamment d’importation pour les jeux qui s’attendent à ajouter du contenu pendant le développement ou après que le jeu a été publié.
 - Modèles de moteur de jeu : certains moteurs de jeu encourageons vivement l’utilisation des composants système alors que d’autres permettent aux développeurs de décider comment organiser les entités. CocosSharp ne nécessite pas l’utilisation d’un système de composant, par conséquent, les développeurs sont libres d’implémenter n’importe quel type d’entité. 

Par souci de simplicité, nous allons utiliser une approche basée sur une classe spécifique avec une seule entité de livraison et de liste à puces pour ce didacticiel.


## <a name="project-setup"></a>Programme d’installation de projet

Avant de commencer l’implémentation de nos entités, nous devons créer un projet. Nous allons utiliser les modèles de projet CocosSharp pour simplifier la création du projet. [Consultez ce billet](http://forums.xamarin.com/discussion/26822/cocossharp-project-templates-for-xamarin-studio) pour plus d’informations sur la création d’un projet CocosSharp à partir de Visual Studio pour les modèles de Mac. Le reste de ce guide utilise le nom du projet **EntityProject**.

Une fois notre projet est créé, nous allons définir la résolution de notre jeu pour s’exécuter à 480 x 320. Pour ce faire, appelez `CCScene.SetDefaultDesignResolution` dans le `GameAppDelegate.ApplicationDidFinishLaunching` méthode comme suit :


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    ...

    // New code for resolution setting:
    CCScene.SetDefaultDesignResolution(480, 320, CCSceneResolutionPolicy.ShowAll);
    
    CCScene scene = new CCScene (mainWindow);
    GameLayer gameLayer = new GameLayer ();

    scene.AddChild (gameLayer);
    mainWindow.RunWithScene (scene);
} 
```

Pour plus d’informations sur le traitement des résolutions de CocosSharp, consultez notre [guide sur la gestion de résolutions multiples dans CocosSharp](~/graphics-games/cocossharp/resolutions.md).


## <a name="adding-content-to-the-project"></a>Ajout de contenu au projet

Une fois notre projet a été créé, nous allons ajouter les fichiers contenus dans [ce fichier zip contenu](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Entities.zip?raw=true). Pour ce faire, téléchargez le fichier zip et décompressez-le. Ajouter à la fois **ship.png** et **bullet.png** à la **contenu** dossier. Le **contenu** dossier se trouve dans le **actifs** dossier sur Android et sera à la racine du projet sur iOS. Une fois ajoutée, nous devrions voir les deux fichiers dans le **contenu** dossier :

![](entities-images/image2.png "Une fois ajoutée, les deux fichiers doivent être dans le dossier de contenu")


## <a name="creating-the-ship-entity"></a>Création de l’entité de livraison

Le `Ship` classe sera la première entité de notre jeu. Pour ajouter un `Ship` class, commencez par créer un dossier appelé **entités** à la racine du projet. Ajoutez une nouvelle classe dans le **entités** dossier appelé `Ship`:

![](entities-images/image3.png "Ajoutez une nouvelle classe dans le dossier d’entités expédition")

La première modification nous veillerons à notre `Ship` classe consiste à laisser héritent la `CCNode` classe. `CCNode` sert de classe de base pour les classes de CocosSharp communes telles que `CCSprite` et `CCLayer`et nous offre les fonctionnalités suivantes :

 - `Position` propriété pour déplacer le navire autour de l’écran.
 - `Children` propriété pour l’ajout d’un `CCSprite.`
 - `Parent` propriété, qui peut être utilisée pour attacher `Ship` instances à d’autres `CCNodes`. Nous ne l’utiliserons cette fonctionnalité dans ce didacticiel. les jeux plus volumineux généralement profiter de l’attachement d’entités à d’autres `CCNodes`. 
 - `AddEventListener` méthode pour répondre à l’entrée pour le déplacement de l’expédition.
 - `Schedule` méthode de résolution des puces.

Nous allons également ajouter un `CCSprite` afin que notre ship sont consultables à l’écran de l’instance :


```csharp
using System;
using CocosSharp;

namespace EntityProject
{
    public class Ship : CCNode
    {
        CCSprite sprite;

        public Ship () : base()
        {
            sprite = new CCSprite ("ship.png");
            // Center the Sprite in this entity to simplify
            // centering the Ship when it is instantiated
            sprite.AnchorPoint = CCPoint.AnchorMiddle;
            this.AddChild(sprite);
        }
    }
}
```

Ensuite, nous allons ajouter le destinataire à notre `GameLayer` pour le voir apparaître dans le jeu :


```csharp
public class GameLayer : CCLayer
{
    Ship ship;

    public GameLayer ()
    {
        ship = new Ship ();
        ship.PositionX = 240;
        ship.PositionY = 50;
        this.AddChild (ship);
    } 
    ...
```

Si nous exécutons notre jeu, nous verrons maintenant notre entité de livraison :

![](entities-images/image4.png "Lorsque vous exécutez le jeu, l’entité de livraison s’affiche.")


### <a name="why-inherit-from-ccnode-instead-of-ccsprite"></a>Pourquoi héritent CCNode au lieu de CCSprite ?

À ce stade notre `Ship` classe est un wrapper simple pour un `CCSprite` instance. Dans la mesure où `CCSprite` hérite également de `CCNode`, nous pourrions avoir héritée directement `CCSprite`, ce qui serait ont réduit le code dans `Ship.cs`. En outre, héritant directement `CCSprite` réduit le nombre d’objets en mémoire et peut améliorer les performances en réduisant la taille de l’arborescence des dépendances.

En dépit de ces avantages, nous héritée de `CCNode` pour masquer une partie de la `CCSprite` propriétés à partir de chaque instance. Par exemple, le `Texture` propriété ne doit pas être modifiée en dehors de la `Ship` classe et héritant de `CCNode` nous permet de masquer cette propriété. Les membres publics de nos entités devient particulièrement importants, comme un jeu s’accroît et que les développeurs supplémentaires sont ajoutées à une équipe.


## <a name="adding-input-to-the-ship"></a>Ajout d’une entrée pour l’expédition

Maintenant que notre expédition est visible sur l’écran sera enrichie d’entrée. Notre approche sera semblable à l’approche adoptée le [BouncingGame guide](~/graphics-games/cocossharp/bouncing-game.md), à ceci près que nous allez placer le code pour le déplacement dans le `Ship` classe plutôt que dans le contenant `CCLayer` ou `CCScene`.

Ajoutez le code à `Ship` pour prendre en charge le déplacement vers, là où l’utilisateur touche l’écran :


```csharp
public class Ship : CCNode
{
    CCSprite sprite;

    CCEventListenerTouchAllAtOnce touchListener;

    public Ship () : base()
    {
        sprite = new CCSprite ("ship.png");
        // Center the Sprite in this entity to simplify
        // centering the Ship on screen
        sprite.AnchorPoint = CCPoint.AnchorMiddle;
        this.AddChild(sprite);

        touchListener = new CCEventListenerTouchAllAtOnce();
        touchListener.OnTouchesMoved = HandleInput;
        AddEventListener(touchListener, this);

    }

    private void HandleInput(System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
    {
        if(touches.Count > 0)
        {
            CCTouch firstTouch = touches[0];

            this.PositionX = firstTouch.Location.X;
            this.PositionY = firstTouch.Location.Y;
        } 
    }
} 
```

Nombreux filmer em des jeux implémentent une rapidité maximale, imiter contrôleur traditionnel de déplacement. Ceci dit, nous allons implémenter simplement transfert immédiat pour que notre code plus court.


## <a name="creating-the-bullet-entity"></a>Création de l’entité de la liste à puces

La deuxième entité dans notre jeu simple est une entité pour l’affichage des puces. Tout comme le `Ship` entité, le `Bullet` entité contiendra un `CCSprite` afin qu’il apparaisse sur l’écran. La logique pour le déplacement des diffère dans la mesure où il ne dépend pas des entrées d’utilisateur pour le déplacement des ; au lieu de cela, `Bullet` déplacent des instances dans une ligne droite à l’aide des propriétés de rapidité.

Tout d’abord, nous allons ajouter un nouveau fichier de classe à notre **entités** dossier et appelez-le **puce**:

![](entities-images/image5.png "Ajoutez un nouveau fichier de classe dans le dossier d’entités et appelez-le à puces")

Une fois ajoutée, nous allons modifier le `Bullet.cs` code comme suit :


```csharp
using System;
using CocosSharp;

namespace EntityProject
{
    public class Bullet : CCNode
    {
        CCSprite sprite;

        public float VelocityX
        {
            get;
            set;
        }

        public float VelocityY
        {
            get;
            set;
        }

        public Bullet () : base()
        {
            sprite = new CCSprite ("bullet.png");
            // Making the Sprite be centered makes
            // positioning easier.
            sprite.AnchorPoint = CCPoint.AnchorMiddle;
            this.AddChild(sprite);

            this.Schedule (ApplyVelocity);
        }

        void ApplyVelocity(float time)
        {
            PositionX += VelocityX * time;
            PositionY += VelocityY * time;
        }
    }
} 
```

Outre la modification du fichier utilisé pour le `CCSprite` à `bullet.png`, le code dans `ApplyVelocity` inclut la logique de déplacement qui repose sur deux coefficients : `VelocityX` et `VelocityY`.

Le `Schedule` méthode permet d’ajouter des délégués à être appelé pour chaque trame. Dans ce cas, nous ajoutons la `ApplyVelocity` méthode afin que notre liste à puces se déplace en fonction de ses valeurs de vitesse. Le `Schedule` méthode prend un `Action<float>`, où le paramètre de type float spécifie la quantité de temps (en secondes) depuis la dernière image, ce qui nous permet d’implémenter des temps de déplacement. Depuis la valeur est mesurée en secondes, puis nos valeurs de rapidités représentent le mouvement dans *pixels par seconde*.


## <a name="adding-bullets-to-gamelayer"></a>Ajout de puces à GameLayer

Avant d’ajouter les `Bullet` instances pour notre jeu, nous apporterons un conteneur, en particulier un `List<Bullet>`. Modifier le `GameLayer` afin qu’il comporte une liste à puces :


```csharp
    public class GameLayer : CCLayer
    {
        Ship ship;
        List<Bullet> bullets;

        public GameLayer ()
        {
            ship = new Ship ();
            ship.PositionX = 240;
            ship.PositionY = 50;
            this.AddChild (ship);

            bullets = new List<Bullet> ();
        }
        ... 
```

Ensuite, nous devrons remplir le `Bullet` liste. La logique pour le moment auquel créer un `Bullet` doit être contenu dans le `Ship` entité, mais le `GameLayer` est chargé de stocker la liste à puces. Nous allons utiliser le modèle de fabrique pour autoriser le `Ship` entité à créer `Bullet` instances. Cette fabrique expose un événement qui le `GameLayer` peut gérer. 

Pour ce faire tout d’abord, nous ajouterons un dossier à notre projet nommé **fabriques**, puis ajoutez une nouvelle classe appelée `BulletFactory`:

![](entities-images/image6.png "Ajouter un dossier au projet nommé fabriques, et puis ajoutez une nouvelle classe appelée BulletFactory")

Ensuite, nous allons implémenter le `BulletFactory` classe singleton :


```csharp
using System;

namespace EntityProject
{
    public class BulletFactory
    {
        static Lazy<BulletFactory> self = 
            new Lazy<BulletFactory>(()=>new BulletFactory());

        // simple singleton implementation
        public static BulletFactory Self
        {
            get
            {
                return self.Value;
            }
        }

        public event Action<Bullet> BulletCreated;

        private BulletFactory()
        {

        }

        public Bullet CreateNew()
        {
            Bullet newBullet = new Bullet ();

            if (BulletCreated != null)
            {
                BulletCreated (newBullet);
            }

            return newBullet;
        }
    }
} 
```

Le `Ship` entité gérera création `Bullet` instances – en particulier, il gère la fréquence à laquelle `Bullet` instances doivent être créées (par exemple, la fréquence à laquelle la puce est déclenchée), leur position et leur rapidité.

Modifier le `Ship` constructeur de l’entité pour ajouter un nouveau `Schedule` appeler et implémentez cette méthode comme suit :


```csharp
...
public Ship () : base()
{
    sprite = new CCSprite ("ship.png");
    // Center the Sprite in this entity to simplify
    // centering the Ship on screen
    sprite.AnchorPoint = CCPoint.AnchorMiddle;
    this.AddChild(sprite);

    touchListener = new CCEventListenerTouchAllAtOnce();
    touchListener.OnTouchesMoved = HandleInput;
    AddEventListener(touchListener, this);

    Schedule (FireBullet, interval: 0.5f);

}

void FireBullet(float unusedValue)
{
    Bullet newBullet = BulletFactory.Self.CreateNew ();
    newBullet.Position = this.Position;
    newBullet.VelocityY = 100;
} 
...
```

La dernière étape consiste à gérer la création de nouveaux `Bullet` instances dans le `GameLayer` code. Ajouter un gestionnaire d’événements pour le `BulletCreated` événement qui ajoute nouvellement créé `Bullet` pour les listes appropriées :


```csharp
...
public GameLayer ()
{
    ship = new Ship ();
    ship.PositionX = 240;
    ship.PositionY = 50;
    this.AddChild (ship);

    bullets = new List<Bullet> ();
    BulletFactory.Self.BulletCreated += HandleBulletCreated;
}

void HandleBulletCreated(Bullet newBullet)
{
    AddChild (newBullet);
    bullets.Add (newBullet);
}
... 
```

Maintenant nous pouvons exécuter le jeu et consultez le `Ship` TIR `Bullet` instances :

![](entities-images/image1.png "Exécuter le jeu et le destinataire sera être liés aux instances de liste à puces")


## <a name="why-gamelayer-has-ship-and-bullets-members"></a>Pourquoi GameLayer a des membres d’expédition et de puces

Notre `GameLayer` classe définit deux champs pour contenir des références à des instances d’entité (`ship` et `bullets`), mais ne fait rien avec eux. En outre, les entités sont responsables de leur propre comportement telles que le déplacement et le tir. Pourquoi nous ajoutons donc `ship` et `bullets` champs `GameLayer`?

La raison pour laquelle nous avons ajouté ces membres est, car une implémentation complète de jeu nécessiterait une logique dans le `GameLayer` pour l’interaction entre les différentes entités. Par exemple, ce jeu peut être plus développé pour inclure des ennemis qui peuvent être détruits par le lecteur. Ces ennemis seraient trouve dans un `List` dans le `GameLayer`et la logique pour tester si `Bullet` instances entre en conflit avec les ennemis seraient exécutées dans le `GameLayer` également. En d’autres termes, le `GameLayer` est la racine *propriétaire* d’entité de toutes les instances et il est responsable des interactions entre les instances d’entité.


## <a name="bullet-destruction-considerations"></a>Considérations relatives à la destruction puce

Notre jeu ne dispose pas actuellement le code de destruction `Bullet` instances. Chaque `Bullet` instance possède une logique de déplacement sur l’écran, mais nous n’avons pas ajouté de code pour les détruire hors écran `Bullet` instances.

En outre, la destruction de `Bullet` instances ne peuvent pas appartenir dans `GameLayer`. Par exemple, plutôt que d’en cours de destruction lorsque hors écran, le `Bullet` entité peut avoir une logique pour détruire lui-même après un certain laps de temps. Dans ce cas, le `Bullet` a besoin d’un moyen de communiquer qu’il doit être détruit à la `GameLayer`, similaire à la `Ship` entité communiquées à la `GameLayer` qui un nouveau `Bullet` a été créé via le `BulletFactory`.

La solution la plus simple consiste à développer la responsabilité de la classe de fabrique pour prendre en charge de destruction. Puis la fabrique peut être avertie d’une instance d’entité en cours de destruction, qui peut être gérée par d’autres objets, tels que le `GameLayer` suppression de l’instance d’entité à partir de ses listes. 

## <a name="summary"></a>Récapitulatif

Ce guide montre comment créer des entités de CocosSharp en héritant de la `CCNode` classe. Ces entités sont des objets autonomes, gestion de la création de leurs propres éléments visuels et une logique personnalisée. Ce guide désigne le code qui appartient à l’intérieur d’une entité (le déplacement et la création d’autres entités) à partir du code qui appartienne dans le conteneur d’entités racine (collision et toute autre logique d’interaction entité).

## <a name="related-links"></a>Liens connexes

- [Documentation de l’API de CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/)
- [Contenu zip](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Entities.zip?raw=true)
