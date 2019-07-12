---
title: 'Partie 2 : implémentation du WalkingGame'
description: Cette procédure pas à pas montre comment ajouter la logique de jeu et de contenu à un projet MonoGame vide pour créer une démonstration d’un sprite animée déplacement avec des entrée tactile.
ms.prod: xamarin
ms.assetid: F0622A01-DE7F-451A-A51F-129876AB6FFD
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: c9e0cf2f29d304f042bc56ee91029adadcaba570
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832505"
---
# <a name="part-2--implementing-the-walkinggame"></a>Partie 2 : implémentation du WalkingGame

_Cette procédure pas à pas montre comment ajouter la logique de jeu et de contenu à un projet MonoGame vide pour créer une démonstration d’un sprite animée déplacement avec des entrée tactile._

Les parties précédentes de cette procédure pas à pas vous a montré comment créer des projets MonoGame vides. Nous créerons ces parties précédents en apportant une simple démonstration de jeu. Cet article contient les sections suivantes :

- Le contenu de notre jeu lors de la décompression
- Vue d’ensemble de la classe de MonoGame
- Notre première Sprite de rendu
- Création de la CharacterEntity
- Ajout de CharacterEntity au jeu
- Création de la classe d’Animation
- Ajout de la première Animation à CharacterEntity
- Ajout de déplacement vers le caractère
- Mise en correspondance le déplacement et animation


## <a name="unzipping-our-game-content"></a>Le contenu de notre jeu lors de la décompression

Avant de commencer à écrire du code, nous souhaitons décompressez notre jeu *contenu*. Les développeurs de jeux utilisent souvent le terme *contenu* pour faire référence à des fichiers de code non qui sont généralement créés par visual artistes, des concepteurs de jeux ou des concepteurs audio. Types de contenu courants incluent les fichiers utilisés pour afficher des éléments visuels, émettre un signal sonore ou contrôler le comportement de l’intelligence artificielle (IA). À partir d’un développement de jeux contenu du point de vue de l’équipe est généralement créé par non programmeurs.

Vous pouvez trouver le contenu utilisé ici [sur github](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true). Nous avons besoin de ces fichiers téléchargés dans un emplacement auquel nous accéderons plus loin dans cette procédure pas à pas.

## <a name="monogame-class-overview"></a>Vue d’ensemble de la classe de MonoGame

Pour commencer, nous allons explorer les classes de MonoGame utilisées dans le rendu de base :

- `SpriteBatch` – utilisé pour dessiner des graphismes 2D à l’écran. *Sprites* sont des éléments visuels 2D qui servent à afficher des images à l’écran. Le `SpriteBatch` objet peut dessiner un sprite unique à la fois entre ses `Begin` et `End` méthodes ou des sprites plusieurs peuvent être regroupées, ou *par lot*.
- `Texture2D` : représente un objet image lors de l’exécution. `Texture2D` instances sont souvent créées à partir des formats de fichier tels que .png ou .bmp, bien qu’ils peuvent également être créés dynamiquement lors de l’exécution. `Texture2D` les instances sont utilisées lors du rendu avec `SpriteBatch` instances.
- `Vector2` : représente une position dans un système de coordonnées 2D qui est souvent utilisé pour le positionnement des objets visuels. Inclut également des MonoGame `Vector3` et `Vector4` , mais nous n’utilisons `Vector2` dans cette procédure pas à pas.
- `Rectangle` – une zone rectangulaire avec la position, la largeur et hauteur. Nous utilisons cela pour définir la partie de notre `Texture2D` à rendre lorsque nous commencerons à travailler avec des animations.

Notez également que MonoGame n’est pas gérée par Microsoft – en dépit de son espace de noms. Le `Microsoft.Xna` espace de noms est utilisé dans MonoGame pour faciliter la migration des projets XNA existants vers MonoGame.

## <a name="rendering-our-first-sprite"></a>Notre première Sprite de rendu

Ensuite, nous attirerons un sprite unique à l’écran de montrer comment effectuer un rendu 2D dans MonoGame.

### <a name="creating-a-texture2d"></a>Création d’une Texture2D

Nous devons créer un `Texture2D` instance à utiliser lors du rendu de notre sprite. Partie du contenu est finalement contenu dans un dossier nommé **contenu,** situé dans le projet spécifique à la plateforme. Projets de MonoGame partagé ne peut pas contenir de contenu, comme le contenu doit utiliser des actions de génération spécifique à la plateforme. Vous trouverez le dossier de contenu dans le projet iOS et dans le dossier de ressources dans le projet Android.

Pour ajouter du contenu de notre jeu, cliquez sur le **contenu** dossier et sélectionnez **Ajouter > ajouter des fichiers...** Accédez à l’emplacement où le fichier content.zip a été extrait et sélectionnez le **charactersheet.png** fichier. Si vous êtes invité sur la façon d’ajouter le fichier au dossier, nous devons sélectionner le **copie** option :

![](part2-images/image1.png "Si vous êtes invité sur la façon d’ajouter le fichier au dossier, sélectionnez l’option de copie")

Le dossier de contenu contient maintenant le fichier charactersheet.png :

![](part2-images/image2.png "Le dossier de contenu contient maintenant le fichier charactersheet.png")

Ensuite, nous allons ajouter le code pour charger le fichier charactersheet.png et créer un `Texture2D`. Pour cela, ouvrez le `Game1.cs` fichier, puis ajoutez le champ suivant à la classe Game1.cs :

```csharp
Texture2D characterSheetTexture;
```

Ensuite, nous allons créer le `characterSheetTexture` dans le `LoadContent` (méthode). Avant toute modification `LoadContent` méthode ressemble à ceci :

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    // TODO: use this.Content to load your game content here
}
```

Il nous faut remarquer que le projet par défaut déjà instancie le `spriteBatch` instance pour nous. Nous allons utiliser cela plus tard, mais nous ne sont pas ajouter de code supplémentaire pour préparer le `spriteBatch` à utiliser. En revanche, notre `spriteSheetTexture` requiert-il l’initialisation, donc nous initialisera après le `spriteBatch` est créé :

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
    {
        characterSheetTexture = Texture2D.FromStream (this.GraphicsDevice, stream);

    }
}
```

Maintenant que nous avons un `SpriteBatch` instance et un `Texture2D` instance, nous pouvons ajouter notre code de rendu pour le `Game1.Draw` méthode :

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    spriteBatch.Begin ();

    Vector2 topLeftOfSprite = new Vector2 (50, 50);
    Color tintColor = Color.White;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);

    spriteBatch.End ();

    base.Draw(gameTime);
}
```

En cours d’exécution du jeu montre un sprite unique affichant la texture créée à partir de charactersheet.png :

![](part2-images/image3.png "En cours d’exécution du jeu montre un sprite unique affichant la texture créée à partir de charactersheet.png")

## <a name="creating-the-characterentity"></a>Création de la CharacterEntity

Jusqu'à présent, nous avons ajouté le code permettant d’afficher un sprite unique à l’écran ; Toutefois, si nous devions développer un jeu complet sans créer d’autres classes, nous devons exécuter sur les problèmes d’organisation de code.

### <a name="what-is-an-entity"></a>Qu’est une entité ?

Un modèle courant pour organiser le code de jeu consiste à créer une nouvelle classe pour chaque jeu *entité* type. Une entité dans le développement de jeux est un objet qui peut contenir certaines des caractéristiques suivantes (certaines sont obligatoires) :

- Un élément visuel tel qu’un sprite, un texte ou un modèle 3D
- Physique ou chaque comportement de frame, par exemple une unité patrouiller un chemin d’accès de l’ensemble ou un caractère de lecteur répondre à l’entrée
- Peuvent être créés et détruits de façon dynamique, comme un power-up qui apparaissent et sont collectées par le lecteur

Systèmes d’entreprise entité peuvent être complexes, et de nombreux moteurs de jeu offrent des classes permettant de gérer les entités. Nous allons implémenter un système très simple d’entité, par conséquent, il est important de noter que des jeux complètes nécessite généralement une organisation plus sur le développeur.


### <a name="defining-the-characterentity"></a>Définition de la CharacterEntity

Notre entité, que nous appellerons `CharacterEntity`, a les caractéristiques suivantes :

- La capacité à charger sa propre `Texture2D`
- La capacité d’afficher lui-même, y compris contenant l’appel de méthodes pour mettre à jour de l’animation parcourue
- `X` propriétés et Y pour contrôler la position du caractère.
- La possibilité de se mettre à jour – en particulier, pour lire les valeurs de la fonction tactile d’écran et ajustent en conséquence de position.

Pour ajouter le `CharacterEntity` pour notre jeu, un clic droit ou un contrôle, cliquez sur le **WalkingGame** de projet et sélectionnez **Ajouter > nouveau fichier...** . Sélectionnez le **classe vide** option et nommez le nouveau fichier **CharacterEntity**, puis cliquez sur **New**.

Tout d’abord, nous allons ajouter la possibilité pour les `CharacterEntity` pour charger un `Texture2D` ainsi que pour se dessiner lui-même. Nous allons modifier le récemment ajoutés `CharacterEntity.cs` fichier comme suit :

```csharp
using System;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Input.Touch;


namespace WalkingGame
{
    public class CharacterEntity
    {
        static Texture2D characterSheetTexture;

        public float X
        {
            get;
            set;
        }

        public float Y
        {
            get;
            set;
        }

        public CharacterEntity (GraphicsDevice graphicsDevice)
        {
            if (characterSheetTexture == null)
            {
                using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
                {
                    characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
                }
            }
        }

        public void Draw(SpriteBatch spriteBatch)
        {
            Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
            Color tintColor = Color.White;
            spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);
        }
    }
}
```

Le code ci-dessus ajoute la responsabilité de positionnement, le rendu et du chargement du contenu à la `CharacterEntity`. Prenons un moment pour souligner quelques considérations prises dans le code ci-dessus.

### <a name="why-are-x-and-y-floats"></a>Pourquoi les X et à virgule flottante Y ?

Les développeurs qui débutent avec la programmation de jeux peut-être vous demandez-vous pourquoi les `float` est utilisée par opposition à `int` ou `double`. La raison est qu’une valeur 32 bits est plus courant pour le positionnement dans le code de rendu de bas niveau. Le type double occupe 64 bits pour la précision, ce qui est rarement nécessaire pour le positionnement des objets. Si une différence de 32 bits peut sembler non significatif, de nombreux jeux modernes incluent les graphiques qui nécessitent le traitement des dizaines de milliers de valeurs de position chaque frame (30 ou 60 fois par seconde). Couper la quantité de mémoire qui doit se déplacer via les graphiques pipeline par moitié peut avoir un impact significatif sur les performances d’un jeu.

Le `int` type de données peut être une unité de mesure pour le positionnement d’approprié, mais il peut placer des restrictions sur la façon dont les entités sont positionnées. Par exemple, à l’aide de valeurs entières rend plus difficile à implémenter les déplacements fluides pour les jeux qui prennent en charge un zoom avant ou caméras 3D (qui sont autorisé par `SpriteBatch`).

Enfin, nous verrons que la logique qui déplace notre caractère autour de l’écran sera le faire à l’aide des valeurs de minutage du jeu. Le résultat de la multiplication de rapidité en combien de temps écoulé dans un frame donné rarement entraîne un nombre entier, nous devons utiliser `float` pour `X` et `Y`.

### <a name="why-is-charactersheettexture-static"></a>Pourquoi est-characterSheetTexture statique ?

Le `characterSheetTexture` `Texture2D` instance est une représentation d’exécution du fichier charactersheet.png. En d’autres termes, il contient les valeurs de couleur pour chaque pixel tel qu’extrait à partir de la source **charactersheet.png** fichier. Si notre jeu consistait à créer deux `CharacterEntity` instances, chacun d’eux est ont besoin d’accéder aux informations à partir de charactersheet.png. Dans ce cas nous n’irons encourir le coût de performances de chargement charactersheet.png à deux reprises, ni que nous souhaitons avoir de mémoire de texture en double stockée dans la mémoire ram. À l’aide un `static` champ permet de partager le même `Texture2D` ensemble `CharacterEntity` instances.

À l’aide de `static` membres pour la représentation de l’exécution du contenu est une solution simple et il ne traite pas des problèmes rencontrés dans les jeux plus volumineux tels que le déchargement de contenu lors du déplacement d’un niveau vers un autre. Des solutions plus sophistiquées qui sortent du cadre de cette procédure pas à pas, incluent l’utilisation de pipeline de contenus de MonoGame ou de création d’un système de gestion de contenu personnalisé.

### <a name="why-is-spritebatch-passed-as-an-argument-instead-of-instantiated-by-the-entity"></a>Pourquoi est-il SpriteBatch passée comme un Argument Instead of instancié par l’entité

Le `Draw` méthode telle qu’implémentée ci-dessus prend un `SpriteBatch` argument, mais en revanche, le `characterSheetTexture` est instancié par le `CharacterEntity`.

La raison en est, car le rendu plus efficace est possible lorsque le même `SpriteBatch` instance est utilisée pour tous les `Draw` appels et lorsque tous les `Draw` appels sont effectués entre un ensemble unique de `Begin` et `End` appels. Bien sûr, notre jeu inclut uniquement une seule instance d’entité, mais les jeux plus compliqués bénéficieront de modèle qui permet à plusieurs entités à utiliser le même `SpriteBatch` instance.


## <a name="adding-characterentity-to-the-game"></a>Ajout de CharacterEntity au jeu

Maintenant que nous avons ajouté notre `CharacterEntity` avec du code pour le rendu de lui-même, nous pouvons remplacer le code dans `Game1.cs` pour utiliser une instance de cette nouvelle entité. Pour cela, nous allons remplacer le `Texture2D` champ avec un `CharacterEntity` champ `Game1`:

```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;
    SpriteBatch spriteBatch;
    // New code:
    CharacterEntity character;

    public Game1()
    {
    ...
```

Ensuite, nous allons ajouter l’instanciation de cette entité dans `Game1.Initialize`:

```csharp
protected override void Initialize()
{
    character = new CharacterEntity (this.GraphicsDevice);

    base.Initialize();
}
```

Nous devons également supprimer la `Texture2D` création à partir de `LoadContent` dans la mesure où cet aspect est désormais géré à l’intérieur de notre `CharacterEntity`. `Game1.LoadContent` doit ressembler à ceci :

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
}
```

Il est important de souligner que, malgré son nom le `LoadContent` (méthode) n’est pas le seul endroit où contenu peut être chargé : implémenter de nombreux jeux contenu chargement dans leur méthode d’initialisation, ou même dans leur code de mise à jour en tant que contenu, n’est pas nécessaire jusqu'à ce que le joueur atteigne un certains points du jeu.

Enfin, nous pouvons modifier la méthode Draw comme suit :


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    // We'll start all of our drawing here:
    spriteBatch.Begin ();

    // Now we can do any entity rendering:
    character.Draw(spriteBatch);
    // End renders all sprites to the screen:
    spriteBatch.End ();

    base.Draw(gameTime);
}
```

Si nous exécutons le jeu, nous allons maintenant voir le caractère. Dans la mesure où X et Y par défaut sur 0, le caractère est positionné par rapport à l’angle supérieur gauche de l’écran :

![](part2-images/image4.png "Dans la mesure où X et Y par défaut sur 0, le caractère est positionné par rapport à l’angle supérieur gauche de l’écran")

## <a name="creating-the-animation-class"></a>Création de la classe d’Animation

Actuellement notre `CharacterEntity` affiche la version complète **charactersheet.png** fichier. Cette disposition de plusieurs images dans un seul fichier est appelée un *feuille du sprite*. En règle générale, un sprite affichera uniquement une partie de la feuille de sprite. Nous allons modifier le `CharacterEntity` pour restituer une partie de ce **charactersheet.png**, et cette partie change au fil du temps pour afficher une animation parcourue.

Nous allons créer le `Animation` classe pour contrôler la logique et l’état de l’animation CharacterEntity. La classe d’Animation sera une classe générale qui pourrait être utilisée pour toutes les entités, pas seulement `CharacterEntity` animations. Ultimate le `Animation` classe fournira un `Rectangle` qui le `CharacterEntity` utilisera lors du dessin proprement dit. Nous allons également créer un `AnimationFrame` classe qui sera utilisé pour définir l’animation.


### <a name="defining-animationframe"></a>Définition AnimationFrame

`AnimationFrame` ne contient pas de logique lié à l’animation. Nous allons utiliser il uniquement pour stocker les données. Pour ajouter le `AnimationFrame` classe, avec le bouton droit ou en cliquant sur le **WalkingGame** partagé du projet, puis sélectionnez **Ajouter > nouveau fichier...** Entrez le nom **AnimationFrame** et cliquez sur le **New** bouton. Nous allons modifier le `AnimationFrame.cs` de fichiers pour qu’il contienne le code suivant :


```csharp
using System;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class AnimationFrame
    {
        public Rectangle SourceRectangle { get; set; }
        public TimeSpan Duration { get; set; }
    }
}
```

Le `AnimationFrame` classe contient deux informations :

- `SourceRectangle` : Définit la zone de la `Texture2D` qui sera affiché par le `AnimationFrame`. Cette valeur est mesurée en pixels, la supérieur gauche étant (0, 0).
- `Duration` : Définit la durée pendant laquelle un `AnimationFrame` s’affiche lorsqu’il est utilisé dans un `Animation`.

### <a name="defining-animation"></a>Définition d’Animation

Le `Animation` classe contiendra une `List<AnimationFrame>` , ainsi que la logique pour basculer l’image est affichée en fonction de combien de temps écoulé.

Pour ajouter le `Animation` classe, avec le bouton droit ou en cliquant sur le **WalkingGame** partagé du projet, puis sélectionnez **Ajouter > nouveau fichier...** . Entrez le nom **Animation** et cliquez sur le **New** bouton. Nous allons modifier le `Animation.cs` fichier afin qu’il contient le code suivant :


```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class Animation
    {
        List<AnimationFrame> frames = new List<AnimationFrame>();
        TimeSpan timeIntoAnimation;

        TimeSpan Duration
        {
            get
            {
                double totalSeconds = 0;
                foreach (var frame in frames)
                {
                    totalSeconds += frame.Duration.TotalSeconds;
                }

                return TimeSpan.FromSeconds (totalSeconds);
            }
        }

        public void AddFrame(Rectangle rectangle, TimeSpan duration)
        {
            AnimationFrame newFrame = new AnimationFrame()
            {
                SourceRectangle = rectangle,
                Duration = duration
            };

            frames.Add(newFrame);
        }

        public void Update(GameTime gameTime)
        {
            double secondsIntoAnimation = 
                timeIntoAnimation.TotalSeconds + gameTime.ElapsedGameTime.TotalSeconds;


            double remainder = secondsIntoAnimation % Duration.TotalSeconds;

            timeIntoAnimation = TimeSpan.FromSeconds (remainder);
        }
    }
}
```

Examinons certains détails à partir de la `Animation` classe.

### <a name="frames-list"></a>Liste des frames

Le `frames` membre est ce qui stocke les données pour notre animation. Le code qui instancie les animations ajoutera `AnimationFrame` instances pour le `frames` liste via la `AddFrame` (méthode). Une implémentation plus complète peut offrir `public` méthodes ou propriétés de modification `frames`, mais nous allons nous limiter les fonctionnalités à l’ajout d’images pour cette procédure pas à pas.

### <a name="duration"></a>Duration

Durée retourne la durée totale de la `Animation,` qui est obtenu en ajoutant la durée de tous les `AnimationFrame` instances. Cette valeur peut être mis en cache si `AnimationFrame` étaient un objet immuable, mais étant donné que nous avons implémenté AnimationFrame en tant que classe qui peut être modifiée après avoir été ajouté à l’Animation, nous devons calculer cette valeur chaque fois que la propriété est accessible.

### <a name="update"></a>Mettre à jour

Le `Update` (méthode) doit être appelée pour chaque trame (autrement dit, chaque fois le jeu entier est mis à jour). Son objectif consiste à augmenter la `timeIntoAnimation` membre qui est utilisée pour retourner l’image affichée. La logique de `Update` empêche le `timeIntoAnimation` de jamais être supérieure à la durée de la totalité de l’animation.

Étant donné que nous allons utiliser le `Animation` classe pour afficher une animation parcourue, alors il faut faire en sorte que cette animation répéter lorsqu’il a atteint la fin. Pour ce faire, nous pouvons vérifier si le `timeIntoAnimation` est supérieure à la `Duration` valeur. Cas dans ce il jusqu’au début du cycle et conserver le reste, ce qui entraîne une animation de boucle.

### <a name="obtaining-the-current-frames-rectangle"></a>Obtention Rectangle du Frame actif

L’objectif de la `Animation` classe est finalement de fournir un `Rectangle` qui peut être utilisé lors du dessin d’un sprite. Actuellement le `Animation` classe contient la logique permettant de modifier le `timeIntoAnimation` membre, que nous allons utiliser pour lequel obtenir `Rectangle` doit être affiché. Nous aborderons ce point en créant un `CurrentRectangle` propriété dans la `Animation` classe. Copiez cette propriété dans la `Animation` classe :

```csharp
public Rectangle CurrentRectangle
{
    get
    {
        AnimationFrame currentFrame = null;

        // See if we can find the frame
        TimeSpan accumulatedTime = new TimeSpan();
        foreach(var frame in frames)
        {
            if (accumulatedTime + frame.Duration >= timeIntoAnimation)
            {
                currentFrame = frame;
                break;
            }
            else
            {
                accumulatedTime += frame.Duration;
            }
        }

        // If no frame was found, then try the last frame, 
        // just in case timeIntoAnimation somehow exceeds Duration
        if (currentFrame == null)
        {
            currentFrame = frames.LastOrDefault ();
        }

        // If we found a frame, return its rectangle, otherwise
        // return an empty rectangle (one with no width or height)
        if (currentFrame != null)
        {
            return currentFrame.SourceRectangle;
        }
        else
        {
            return Rectangle.Empty;
        }
    }
}
```

## <a name="adding-the-first-animation-to-characterentity"></a>Ajout de la première Animation à CharacterEntity

Le `CharacterEntity` contiendra des animations pour le parcours et permanent, ainsi qu’une référence à l’actuel `Animation` affiché.

Tout d’abord, nous allons ajouter notre premier `Animation,` que nous allons utiliser pour tester la fonctionnalité comme écrits jusqu'à présent. Nous allons ajouter les membres suivants à la classe CharacterEntity :

```csharp
Animation walkDown;
Animation currentAnimation;
```

Ensuite, nous allons définir le `walkDown` animation. Pour effectuer cela modifier la `CharacterEntity` constructeur comme suit :

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

Comme mentionné précédemment, nous devons appeler `Animation.Update` pour les animations basées sur les temps de jouer. Vous devez également affecter la `currentAnimation`. Pour maintenant nous attribuons le `currentAnimation` à `walkDown`, mais nous allons remplacer ce code ultérieurement lorsque nous implémentons notre logique de déplacement. Nous allons ajouter le `Update` méthode `CharacterEntity` comme suit :


```csharp
public void Update(GameTime gameTime)
{
    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

Maintenant que nous avons le `currentAnimation` qui est affecté et mis à jour, nous pouvons l’utiliser pour exécuter notre dessin. Nous allons modifier `CharacterEntity.Draw` comme suit :

```csharp
public void Draw(SpriteBatch spriteBatch)
{
    Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
    Color tintColor = Color.White;
    var sourceRectangle = currentAnimation.CurrentRectangle;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, sourceRectangle, Color.White);
}
```

La dernière étape à l’obtention de la `CharacterEntity` animer consiste à appeler récemment ajouté `Update` méthode à partir de `Game1`. Modifier `Game1.Update` comme suit :

```csharp
protected override void Update(GameTime gameTime)
{
    character.Update (gameTime);
    base.Update(gameTime);
}
```

Maintenant le `CharacterEntity` tiendra sa `walkDown` animation :

![](part2-images/image5.gif "Maintenant le CharacterEntity jouera son animation walkDown")

## <a name="adding-movement-to-the-character"></a>Ajout de déplacement vers le caractère

Ensuite, nous ajouterons le déplacement à notre caractère à l’aide des contrôles tactiles. Lorsque l’utilisateur touche l’écran, le caractère déplacera vers le point où l’écran est touché. Si aucune touche n’est détectées, puis le caractère tout à fait en place.

### <a name="defining-getdesiredvelocityfrominput"></a>Définition GetDesiredVelocityFromInput

Nous allons utiliser de MonoGame `TouchPanel` (classe), qui fournit des informations sur l’état actuel de l’écran tactile. Nous allons ajouter une méthode qui vérifiera le `TouchPanel` et retourner la vitesse souhaitée de notre caractère :


```csharp
Vector2 GetDesiredVelocityFromInput()
{
    Vector2 desiredVelocity = new Vector2 ();

    TouchCollection touchCollection = TouchPanel.GetState();

    if (touchCollection.Count > 0)
    {
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;

        if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)
        {
            desiredVelocity.Normalize();
            const float desiredSpeed = 200;
            desiredVelocity *= desiredSpeed;
        }
    }

    return desiredVelocity;
}
```

Le `TouchPanel.GetState` méthode retourne un `TouchCollection` qui contient des informations sur l’endroit où l’utilisateur touche l’écran. Si l’utilisateur n’est pas toucher l’écran, puis le `TouchCollection` sera vide, auquel cas nous ne devrions pas déplacer le caractère.

Si l’utilisateur touche l’écran, nous déplacerons le caractère vers le premier contact tactile, en d’autres termes, le `TouchLocation` à l’index 0. Initialement, nous allons définir la vitesse souhaitée égales à la différence entre les emplacements du caractère et de premier contact tactile :

```csharp
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;
```

Ce qui suit est un peu de mathématiques garderont le déplacement de caractère à la même vitesse. Pour expliquer pourquoi c’est important, prenons l’exemple d’une situation où l’utilisateur touche les 500 pixels en loin où se trouve le caractère. La première ligne où `desiredVelocity.X` est ensemble faut attribuer une valeur de 500. Toutefois, si l’utilisateur ont été toucher l’écran à une distance de seulement 100 unités à partir du caractère, puis le `desiredVelocity.X` serait défini sur 100. Le résultat est alors que la vitesse de déplacement du caractère répond à la manière dont éloignés que du point tactile est à partir du caractère. Dans la mesure où nous voulons le caractère pour toujours déplacer à la même vitesse, nous devons modifier la desiredVelocity.

La `if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)` instruction vérifie si la vitesse est non-zéro-en d’autres termes, il est la vérification pour vous assurer que l’utilisateur n’est pas toucher au même endroit que la position actuelle du caractère. Si pas, puis nous devons définir la vitesse du caractère comme une constante, quel que soit l’éloignement de la pression tactile sont. Pour ce faire, nous normalisation du vecteur de rapidité, ce qui vous permet d’en cours d’une longueur de 1. Un vecteur de rapidité, de 1 signifie que le caractère est déplacés à 1 pixel par seconde. Nous allons accélérer ce processus en multipliant la valeur par la vitesse de votre choix de 200.


### <a name="applying-velocity-to-position"></a>Appliquer la rapidité à la Position

La rapidité retournée à partir de `GetDesiredVelocityFromInput` doit être appliqué pour le caractère `X` et `Y` valeurs en vigueur lors de l’exécution. Nous allons modifier le `Update` méthode comme suit :

```csharp
public void Update(GameTime gameTime)
{

    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;

    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

Ce que nous avons mis en œuvre ici est appelée *temporels* déplacement (par opposition à *en fonction du frame* déplacement). Temps de déplacement multiplie une valeur de la vitesse (en l’occurrence, les valeurs stockées dans le `velocity` variable) en combien de temps écoulé depuis la dernière mise à jour qui est stocké dans `gameTime.ElapsedGameTime.TotalSeconds`. Si le jeu s’exécute au moins d’images par seconde, augmente le temps écoulé entre les images : le résultat final est que les objets à l’aide des temps de déplacement peuvent être déplacés à la même vitesse, quel que soit la fréquence d’images.

Si nous exécutons notre jeu maintenant, nous verrons que le caractère se déplace vers l’emplacement tactile :

![](part2-images/image6.gif "Le caractère se tourne vers l’emplacement tactile")

## <a name="matching-movement-and-animation"></a>Mise en correspondance le déplacement et Animation

Une fois que nous avons notre caractère de déplacement et la lecture d’une animation unique, nous pouvons définir le reste de notre animations, puis les utiliser afin de refléter le déplacement du caractère. Lorsque terminé nous aurons huit animations au total :

- Animations pour remonter vers le bas, gauche et droite
- Animations pour soutenir toujours et orienté vers le haut, bas, gauche et droite

### <a name="defining-the-rest-of-the-animations"></a>Définir le reste des Animations

Nous allons tout d’abord ajouter le `Animation` instances pour le `CharacterEntity` classe pour toutes nos animations au même endroit où nous avons ajouté `walkDown`. Une fois que nous faisons cela, le `CharacterEntity` aura suit `Animation` membres :

```csharp
Animation walkDown;
Animation walkUp;
Animation walkLeft;
Animation walkRight;

Animation standDown;
Animation standUp;
Animation standLeft;
Animation standRight;

Animation currentAnimation;
```

Maintenant nous allons définir les animations dans la `CharacterEntity` constructeur comme suit :

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkUp = new Animation ();
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (160, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (176, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkLeft = new Animation ();
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (64, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (80, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkRight = new Animation ();
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (112, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (128, 0, 16, 16), TimeSpan.FromSeconds (.25));

    // Standing animations only have a single frame of animation:
    standDown = new Animation ();
    standDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standUp = new Animation ();
    standUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standLeft = new Animation ();
    standLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standRight = new Animation ();
    standRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

Il nous faut remarquer que le code ci-dessus a été ajouté à la `CharacterEntity` constructeur pour que cette procédure plus courte. Jeux seront généralement séparer la définition d’animations de caractère dans leurs propres classes ou charger ces informations à partir d’un format de données tels que XML ou JSON.

Ensuite, nous modifions la logique pour utiliser les animations en fonction de la direction dans laquelle le caractère correspond à un déplacement ou en fonction de la dernière animation si le caractère a simplement été arrêtés. Pour ce faire, nous allons modifier le `Update` méthode :


```csharp
public void Update(GameTime gameTime)
{
    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;


    if (velocity != Vector2.Zero)
    {
        bool movingHorizontally = Math.Abs (velocity.X) > Math.Abs (velocity.Y);
        if (movingHorizontally)
        {
            if (velocity.X > 0)
            {
                currentAnimation = walkRight;
            }
            else
            {
                currentAnimation = walkLeft;
            }
        }
        else
        {
            if (velocity.Y > 0)
            {
                currentAnimation = walkDown;
            }
            else
            {
                currentAnimation = walkUp;
            }
        }
    }
    else
    {
        // If the character was walking, we can set the standing animation
        // according to the walking animation that is playing:
        if (currentAnimation == walkRight)
        {
            currentAnimation = standRight;
        }
        else if (currentAnimation == walkLeft)
        {
            currentAnimation = standLeft;
        }
        else if (currentAnimation == walkUp)
        {
            currentAnimation = standUp;
        }
        else if (currentAnimation == walkDown)
        {
            currentAnimation = standDown;
        }
        else if (currentAnimation == null)
        {
            currentAnimation = standDown;
        }

        // if none of the above code hit then the character
        // is already standing, so no need to change the animation.
    }

    currentAnimation.Update (gameTime);
}
```

Le code pour basculer les animations est divisé en deux blocs. Le premier vérifie si la rapidité n’est pas égale à `Vector2.Zero` – indique si le caractère se déplace. Si le caractère se déplace, nous pouvons examiner la `velocity.X` et `velocity.Y` valeurs afin de déterminer l’animation parcourue à lire.

Si le caractère ne bouge pas, nous souhaitons définir le caractère `currentAnimation` à une animation debout – mais nous le faire uniquement si le `currentAnimation` est un parcours de l’animation ou si une animation n’a pas été définie. Si le `currentAnimation` n’est pas une des quatre animations parcours, puis le caractère est déjà permanent nous n’avez pas besoin de modifier `currentAnimation`.

Le résultat de ce code est que le caractère animer correctement lorsque vous parcourez et ensuite le sens dernière qu’il a été parcours lorsqu’il s’arrête :

![](part2-images/image7.gif "Le résultat de ce code est que le caractère animer correctement lorsque vous parcourez et ensuite le sens dernière qu’il a été parcours lorsqu’il s’arrête")

## <a name="summary"></a>Récapitulatif

Cette procédure pas à pas vous a montré comment travailler avec MonoGame pour créer un inter-plateformes jeu avec des sprites, déplacement d’objets, la détection d’entrée et l’animation. Il décrit la création d’une classe d’animation à usage général. Il a également montré comment créer une entité de caractère pour l’organisation logique de code.

## <a name="related-links"></a>Liens associés

- [Ressource d’Image CharacterSheet (exemple)](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)
- [Parcours de jeu complète (exemple)](https://developer.xamarin.com/samples/mobile/WalkingGameMG/)
