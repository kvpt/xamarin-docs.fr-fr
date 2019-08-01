---
title: 'Partie 2: implémentation du WalkingGame'
description: Cette procédure pas à pas montre comment ajouter une logique de jeu et du contenu à un projet monogame vide pour créer une démonstration d’un sprite animé qui se déplace avec une entrée tactile.
ms.prod: xamarin
ms.assetid: F0622A01-DE7F-451A-A51F-129876AB6FFD
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 7c7b58266b4f5168fdb231258390fa64278963f8
ms.sourcegitcommit: f255aa286bd52e8a80ffa620c2e93c97f069f8ec
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68680952"
---
# <a name="part-2--implementing-the-walkinggame"></a>Partie 2: implémentation du WalkingGame

_Cette procédure pas à pas montre comment ajouter une logique de jeu et du contenu à un projet monogame vide pour créer une démonstration d’un sprite animé qui se déplace avec une entrée tactile._

Les parties précédentes de cette procédure pas à pas ont montré comment créer des projets monogame vides. Nous allons nous appuyer sur ces parties précédentes en effectuant une démonstration de jeu simple. Cet article contient les sections suivantes :

- Décompression de notre contenu de jeu
- Vue d’ensemble de la classe monogame
- Rendu de notre premier Sprite
- Création du CharacterEntity
- Ajout de CharacterEntity au jeu
- Création de la classe d’animation
- Ajout de la première animation à CharacterEntity
- Ajout d’un déplacement au caractère
- Mouvement et animation correspondants


## <a name="unzipping-our-game-content"></a>Décompression de notre contenu de jeu

Avant de commencer à écrire du code, nous souhaitons décompresser le *contenu*de votre jeu. Les développeurs de jeux utilisent souvent le terme *content* pour faire référence à des fichiers non-code qui sont généralement créés par des artistes visuels, des concepteurs de jeux ou des concepteurs audio. Les types de contenu courants incluent les fichiers utilisés pour afficher des visuels, lire un son ou contrôler le comportement de l’intelligence artificielle. Du contenu de perspective d’une équipe de développement de jeux est généralement créé par des non-programmeurs.

Le contenu utilisé ici se trouve [sur GitHub](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true). Nous aurons besoin de ces fichiers téléchargés vers un emplacement auquel nous aurons accès plus tard dans cette procédure pas à pas.

## <a name="monogame-class-overview"></a>Vue d’ensemble de la classe monogame

Pour commencer, nous explorerons les classes de monojeu utilisées dans le rendu de base:

- `SpriteBatch`: utilisé pour dessiner des graphiques 2D à l’écran. Les sprites sont des éléments visuels 2D qui servent à afficher des images à l’écran. L' `SpriteBatch` objet peut dessiner un seul sprite à la fois entre ses `Begin` méthodes `End` et, ou plusieurs sprites peuvent être regroupés ou regroupés *par lots*.
- `Texture2D`– représente un objet image au moment de l’exécution. `Texture2D`les instances sont souvent créées à partir de formats de fichiers tels que. png ou. bmp, bien qu’elles puissent également être créées dynamiquement au moment de l’exécution. `Texture2D`les instances sont utilisées lors du `SpriteBatch` rendu avec des instances.
- `Vector2`: représente une position dans un système de coordonnées 2D qui est souvent utilisé pour positionner des objets visuels. Le monojeu comprend `Vector3` également `Vector4` et, mais nous n' `Vector2` utiliserons cette procédure pas à pas.
- `Rectangle`: zone à quatre côtés avec la position, la largeur et la hauteur. Nous allons l’utiliser pour définir la partie de notre `Texture2D` à afficher lorsque nous commençons à travailler avec des animations.

Nous devons également noter que le monojeu n’est pas géré par Microsoft, en dépit de son espace de noms. L' `Microsoft.Xna` espace de noms est utilisé dans un monojeu pour faciliter la migration de projets XNA existants vers un monojeu.

## <a name="rendering-our-first-sprite"></a>Rendu de notre premier Sprite

Ensuite, nous allons dessiner un seul sprite à l’écran pour montrer comment effectuer un rendu 2D en monojeu.

### <a name="creating-a-texture2d"></a>Création d’un Texture2D

Nous devons créer une `Texture2D` instance à utiliser lors du rendu de notre Sprite. Tout le contenu du jeu est finalement contenu dans un dossier nommé **contenu,** situé dans le projet spécifique à la plateforme. Les projets partagés monojeu ne peuvent pas contenir de contenu, car le contenu doit utiliser des actions de génération spécifiques à la plateforme. Le dossier de contenu se trouve dans le projet iOS et dans le dossier ressources du projet Android.

Pour ajouter le contenu de votre jeu, cliquez avec le bouton droit sur le dossier **contenu** , puis sélectionnez **Ajouter > ajouter des fichiers...** Accédez à l’emplacement où le fichier. zip de contenu a été extrait, puis sélectionnez le fichier **charactersheet. png** . Si vous êtes invité à ajouter le fichier au dossier, sélectionnez l’option de **copie** :

![](part2-images/image1.png "Si vous êtes invité à ajouter le fichier au dossier, sélectionnez l’option de copie")

Le dossier Content contient maintenant le fichier charactersheet. png:

![](part2-images/image2.png "Le dossier de contenu contient maintenant le fichier charactersheet. png")

Ensuite, nous allons ajouter du code pour charger le fichier charactersheet. png et créer `Texture2D`un. Pour ce faire, ouvrez `Game1.cs` le fichier et ajoutez le champ suivant à la classe Game1.cs:

```csharp
Texture2D characterSheetTexture;
```

Ensuite, nous allons créer le `characterSheetTexture` dans la `LoadContent` méthode. Avant d’avoir `LoadContent` une méthode de modification similaire à celle-ci:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    // TODO: use this.Content to load your game content here
}
```

Nous devons noter que le projet par défaut instancie déjà `spriteBatch` l’instance pour nous. Nous utiliserons cette version ultérieurement, mais nous n’ajouterons aucun code supplémentaire pour préparer `spriteBatch` l’utilisation. D’un autre côté, notre `spriteSheetTexture` nécessite une initialisation, donc nous l’initialiserons après la création de `spriteBatch` :

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

Maintenant que nous disposons d' `SpriteBatch` une instance et `Texture2D` d’une instance, nous pouvons ajouter notre code `Game1.Draw` de rendu à la méthode:

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

L’exécution du jeu affiche maintenant un seul Sprite affichant la texture créée à partir de charactersheet. png:

![](part2-images/image3.png "L’exécution du jeu affiche maintenant un seul Sprite affichant la texture créée à partir de charactersheet. png")

## <a name="creating-the-characterentity"></a>Création du CharacterEntity

Jusqu’à présent, nous avons ajouté du code pour afficher un seul sprite à l’écran. Toutefois, si nous devions développer un jeu complet sans créer d’autres classes, nous pourrions rencontrer des problèmes d’organisation du code.

### <a name="what-is-an-entity"></a>Qu’est-ce qu’une entité?

Un modèle courant pour l’organisation du code de jeu consiste à créer une nouvelle classe pour chaque type d' *entité* de jeu. Une entité dans le développement de jeux est un objet qui peut contenir certaines des caractéristiques suivantes (tous ne sont pas obligatoires):

- Élément visuel, tel qu’un sprite, du texte ou un modèle 3D
- Physique ou tous les comportements de trame tels qu’une patrouille d’unité d’un chemin d’accès ou d’un joueur répondant à l’entrée
- Peut être créée et détruite dynamiquement, par exemple une mise sous tension qui s’affiche et est recueillie par le joueur

Les systèmes d’organisation d’entités peuvent être complexes et de nombreux moteurs de jeu offrent des classes pour faciliter la gestion des entités. Nous allons implémenter un système d’entités très simple. il est donc important de noter que les jeux complets requièrent généralement plus d’organisations sur la part du développeur.


### <a name="defining-the-characterentity"></a>Définition de CharacterEntity

Notre entité, que nous appellerons `CharacterEntity`, aura les caractéristiques suivantes:

- La possibilité de charger sa propre`Texture2D`
- La possibilité de s’afficher, y compris les méthodes d’appel contenantes pour mettre à jour l’animation de marche
- `X`et Y pour contrôler la position du caractère.
- La possibilité de se mettre à jour, en particulier pour lire des valeurs à partir de l’écran tactile et ajuster la position de manière appropriée.

Pour ajouter le `CharacterEntity` à notre jeu, cliquez avec le bouton droit ou cliquez avec le bouton droit sur le projet **WalkingGame** , puis sélectionnez **Ajouter > nouveau fichier...** . Sélectionnez l’option **classe vide** et nommez le nouveau fichier **CharacterEntity**, puis cliquez sur **nouveau**.

Tout d’abord, nous allons ajouter la `CharacterEntity` possibilité pour le `Texture2D` de charger un et de se dessiner lui-même. Nous allons modifier le fichier que vous `CharacterEntity.cs` venez d’ajouter comme suit:

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

Le code ci-dessus ajoute la responsabilité du positionnement, du rendu et du chargement `CharacterEntity`du contenu dans le. Prenons quelques instants pour souligner certaines considérations dans le code ci-dessus.

### <a name="why-are-x-and-y-floats"></a>Pourquoi les valeurs X et Y sont-elles flottantes?

Les développeurs qui découvrent la programmation de jeux peuvent se `float` demander pourquoi le type est utilisé par `int` opposition `double`à ou. La raison en est qu’une valeur 32 bits est plus courante pour le positionnement dans le code de rendu de bas niveau. Le double type occupe 64 bits pour la précision, ce qui est rarement nécessaire pour positionner les objets. Bien qu’une différence de 32 bits puisse paraître insignifiante, de nombreux jeux modernes incluent des graphiques qui requièrent le traitement de dizaines de milliers de valeurs de position pour chaque trame (30 ou 60 fois par seconde). Le fait de réduire la quantité de mémoire qui doit être déplacée par la moitié du pipeline graphique peut avoir un impact significatif sur les performances d’un jeu.

Le `int` type de données peut être une unité de mesure appropriée pour le positionnement, mais il peut placer des limitations sur la façon dont les entités sont positionnées. Par exemple, l’utilisation de valeurs entières rend plus difficile l’implémentation du mouvement fluide pour les jeux qui prennent en charge le zoom avant ou les `SpriteBatch`caméras 3D (qui sont autorisées par).

Enfin, nous verrons que la logique qui déplace notre caractère à travers l’écran utilisera les valeurs de minutage du jeu. Le résultat de la multiplication de la rapidité par le temps passé dans un frame donné génère rarement un nombre entier, donc nous devons utiliser `float` pour `X` et `Y`.

### <a name="why-is-charactersheettexture-static"></a>Pourquoi characterSheetTexture est-il statique?

`characterSheetTexture` L'`Texture2D` instance est une représentation à l’exécution du fichier charactersheet. png. En d’autres termes, il contient les valeurs de couleur pour chaque pixel comme extrait du fichier **charactersheet. png** source. Si notre jeu devait créer deux `CharacterEntity` instances, chacune d’entre elles aurait besoin d’accéder aux informations de charactersheet. png. Dans ce cas, nous ne voulons pas encourir le coût de performance lié au chargement de charactersheet. png deux fois, ni à la mémoire de texture stockée en mémoire vive. L’utilisation `static` d’un champ nous permet de partager `Texture2D` le même `CharacterEntity` sur toutes les instances.

L' `static` utilisation de membres pour la représentation du contenu au moment de l’exécution est une solution simpliste qui ne traite pas les problèmes rencontrés dans les plus grands jeux tels que le déchargement de contenu lors du passage d’un niveau à un autre. Des solutions plus sophistiquées, qui n’entrent pas dans le cadre de cette procédure pas à pas, incluent l’utilisation du pipeline de contenu monojeu ou la création d’un système de gestion de contenu personnalisé.

### <a name="why-is-spritebatch-passed-as-an-argument-instead-of-instantiated-by-the-entity"></a>Pourquoi SpriteBatch est-il passé comme argument au lieu d’être instancié par l’entité?

La `Draw` méthode implémentée ci-dessus `SpriteBatch` accepte un argument, `characterSheetTexture` mais en revanche, est instancié par `CharacterEntity`.

La raison en est que le rendu le plus efficace est possible lorsque la même `SpriteBatch` instance est utilisée pour tous `Draw` les appels, et lorsque `Draw` tous les appels sont effectués entre un ensemble unique `Begin` d' `End` appels et. Bien entendu, notre jeu n’inclut qu’une seule instance d’entité, mais des jeux plus compliqués bénéficient d’un modèle qui permet à plusieurs entités `SpriteBatch` d’utiliser la même instance.


## <a name="adding-characterentity-to-the-game"></a>Ajout de CharacterEntity au jeu

Maintenant que nous avons ajouté notre `CharacterEntity` code pour le rendu proprement dit, nous pouvons remplacer le `Game1.cs` code dans pour utiliser une instance de cette nouvelle entité. Pour ce faire, nous allons remplacer `Texture2D` le champ par `CharacterEntity` un champ `Game1`dans:

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

Ensuite, nous allons ajouter l’instanciation de cette entité `Game1.Initialize`dans:

```csharp
protected override void Initialize()
{
    character = new CharacterEntity (this.GraphicsDevice);

    base.Initialize();
}
```

Nous devons également supprimer la `Texture2D` création de `LoadContent` depuis, qui est maintenant gérée à l' `CharacterEntity`intérieur de notre. `Game1.LoadContent`doit se présenter comme suit:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
}
```

Il est important de noter qu’en dépit de son `LoadContent` nom, la méthode n’est pas le seul endroit où le contenu peut être chargé: de nombreux jeux implémentent le chargement de contenu dans leur méthode Initialize, ou même dans leur code de mise à jour en tant que contenu, peut ne pas être nécessaire tant que le joueur n’a pas atteint un certain point du jeu.

Enfin, vous pouvez modifier la méthode Draw comme suit:


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

Si nous exécutons le jeu, nous verrons maintenant le caractère. Étant donné que X et Y ont par défaut la valeur 0, le caractère est positionné par rapport à l’angle supérieur gauche de l’écran:

![](part2-images/image4.png "Étant donné que X et Y ont par défaut la valeur 0, le caractère est positionné par rapport à l’angle supérieur gauche de l’écran")

## <a name="creating-the-animation-class"></a>Création de la classe d’animation

Actuellement, `CharacterEntity` notre affiche le fichier **charactersheet. png** complet. Cette organisation de plusieurs images dans un fichier est appelée *feuille*de sprite. En règle générale, un sprite n’affiche qu’une partie de la feuille du sprite. Nous allons modifier le `CharacterEntity` pour afficher une partie de ce **charactersheet. png**, et cette partie changera au fil du temps pour afficher une animation de marche.

Nous allons créer la `Animation` classe pour contrôler la logique et l’état de l’animation CharacterEntity. La classe d’animation est une classe générale qui peut être utilisée pour n’importe quelle entité, `CharacterEntity` pas seulement pour les animations. Ultimate la `Animation` classe fournira un `Rectangle` que le `CharacterEntity` utilisera pour se dessiner lui-même. Nous allons également créer une `AnimationFrame` classe qui sera utilisée pour définir l’animation.


### <a name="defining-animationframe"></a>Définition de AnimationFrame

`AnimationFrame`ne contient pas de logique relative à l’animation. Nous ne l’utiliserons que pour stocker des données. Pour ajouter la `AnimationFrame` classe, cliquez avec le bouton droit ou cliquez avec le bouton droit sur le projet partagé **WalkingGame** , puis sélectionnez **Ajouter > nouveau fichier....** Entrez le nom **AnimationFrame** , puis cliquez sur le bouton **nouveau** . Nous allons modifier le `AnimationFrame.cs` fichier pour qu’il contienne le code suivant:


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

La `AnimationFrame` classe contient deux éléments d’information:

- `SourceRectangle`: Définit la zone du `Texture2D` qui sera affichée par le. `AnimationFrame` Cette valeur est mesurée en pixels, avec l’angle supérieur gauche (0, 0).
- `Duration`: Définit la durée pendant `AnimationFrame` laquelle un s’affiche lorsqu’il `Animation`est utilisé dans un.

### <a name="defining-animation"></a>Définir une animation

La `Animation` classe contient un `List<AnimationFrame>` , ainsi que la logique permettant de basculer le frame actuellement affiché en fonction du temps écoulé.

Pour ajouter la `Animation` classe, cliquez avec le bouton droit ou cliquez avec le bouton droit sur le projet partagé **WalkingGame** , puis sélectionnez **Ajouter > nouveau fichier...** . Entrez l' **animation** du nom, puis cliquez sur le bouton **nouveau** . Nous allons modifier le `Animation.cs` fichier pour qu’il contienne le code suivant:


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

Examinons quelques-uns des détails de la `Animation` classe.

### <a name="frames-list"></a>Liste de frames

Le `frames` membre est ce qui stocke les données pour notre animation. Le code qui instancie les animations ajoutera `AnimationFrame` des instances à `frames` la liste par `AddFrame` le biais de la méthode. Une implémentation plus complète peut offrir `public` des méthodes ou des propriétés pour `frames`la modification, mais nous limitons la fonctionnalité à l’ajout de frames pour cette procédure pas à pas.

### <a name="duration"></a>Duration

Duration retourne la durée totale du `Animation,` qui est obtenue en ajoutant la durée de toutes les instances contenues. `AnimationFrame` Cette valeur peut être mise en cache `AnimationFrame` si était un objet immuable, mais étant donné que nous avons implémenté AnimationFrame comme une classe qui peut être modifiée après avoir été ajoutée à l’animation, nous devons calculer cette valeur chaque fois que l’utilisateur accède à la propriété.

### <a name="update"></a>Mettre à jour

La `Update` méthode doit être appelée chaque frame (autrement dit, chaque fois que la totalité du jeu est mise à jour). Son objectif est d’augmenter le `timeIntoAnimation` membre utilisé pour retourner le frame actuellement affiché. La logique dans `Update` empêche la `timeIntoAnimation` plus grande que la durée de l’animation entière.

Étant donné que nous allons utiliser `Animation` la classe pour afficher une animation de marche, nous voulons que cette animation se répète lorsqu’elle a atteint la fin. Pour ce faire, nous vérifions si `timeIntoAnimation` le est plus grand `Duration` que la valeur. Si c’est le cas, il repasse au début et conserve le reste, ce qui entraîne une animation en boucle.

### <a name="obtaining-the-current-frames-rectangle"></a>Obtention du rectangle du frame actuel

L’objectif de la `Animation` classe est finalement de fournir un `Rectangle` qui peut être utilisé lors du dessin d’un sprite. Actuellement, `Animation` la classe contient une logique pour `timeIntoAnimation` la modification du membre, que nous utiliserons `Rectangle` pour obtenir ce qui doit être affiché. Pour ce faire, nous allons créer `CurrentRectangle` une propriété dans `Animation` la classe. Copiez cette propriété dans `Animation` la classe:

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

## <a name="adding-the-first-animation-to-characterentity"></a>Ajout de la première animation à CharacterEntity

Le `CharacterEntity` contient des animations pour la marche et l’debout, ainsi qu’une référence au actuel `Animation` affiché.

Tout d’abord, nous allons ajouter `Animation,` notre premier que nous allons utiliser pour tester la fonctionnalité telle qu’elle a été écrite jusqu’à présent. Nous allons ajouter les membres suivants à la classe CharacterEntity:

```csharp
Animation walkDown;
Animation currentAnimation;
```

Ensuite, nous allons définir l' `walkDown` animation. Pour ce faire, modifiez `CharacterEntity` le constructeur comme suit:

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

Comme mentionné précédemment, nous devons appeler `Animation.Update` pour que les animations basées sur le temps s’exécutent. Nous devons également affecter le `currentAnimation`. Pour l’instant, nous allons `currentAnimation` attribuer `walkDown`le à, mais nous allons remplacer ce code ultérieurement lorsque nous implémentons notre logique de mouvement. Nous allons ajouter la `Update` méthode à `CharacterEntity` comme suit:


```csharp
public void Update(GameTime gameTime)
{
    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

Maintenant que nous disposons de `currentAnimation` l’affectation et de la mise à jour, nous pouvons l’utiliser pour effectuer notre dessin. Nous allons modifier `CharacterEntity.Draw` les éléments suivants:

```csharp
public void Draw(SpriteBatch spriteBatch)
{
    Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
    Color tintColor = Color.White;
    var sourceRectangle = currentAnimation.CurrentRectangle;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, sourceRectangle, Color.White);
}
```

La dernière étape pour obtenir l' `CharacterEntity` animation consiste à appeler la méthode nouvellement ajoutée `Update` à partir `Game1`de. Modifiez `Game1.Update` comme suit:

```csharp
protected override void Update(GameTime gameTime)
{
    character.Update (gameTime);
    base.Update(gameTime);
}
```

À présent `CharacterEntity` , le lira `walkDown` son animation:

![](part2-images/image5.gif "À présent, le CharacterEntity lira son animation walkDown")

## <a name="adding-movement-to-the-character"></a>Ajout d’un déplacement au caractère

Nous allons ensuite ajouter un mouvement à notre caractère à l’aide de contrôles tactiles. Lorsque l’utilisateur touche l’écran, le caractère se déplace vers le point où l’écran est touché. Si aucun contact n’est détecté, le caractère se met en place.

### <a name="defining-getdesiredvelocityfrominput"></a>Définition de GetDesiredVelocityFromInput

Nous utiliserons la classe de `TouchPanel` monojeu, qui fournit des informations sur l’état actuel de l’écran tactile. Nous allons ajouter une méthode qui vérifiera et `TouchPanel` renverra la vélocité souhaitée de notre caractère:


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

La `TouchPanel.GetState` méthode retourne un `TouchCollection` qui contient des informations sur l’endroit où l’utilisateur touche l’écran. Si l’utilisateur ne touche pas l’écran, le `TouchCollection` sera vide. dans ce cas, nous ne devrions pas déplacer le caractère.

Si l’utilisateur touche l’écran, nous allons déplacer le caractère vers la première touche tactile, en d’autres termes, `TouchLocation` à l’index 0. Au départ, nous allons définir la vélocité souhaitée pour qu’elle soit égale à la différence entre l’emplacement du caractère et la première zone tactile:

```csharp
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;
```

Ce qui suit est un peu de Math qui conservera le déplacement du caractère à la même vitesse. Pour vous aider à expliquer ce qui est important, considérons une situation dans laquelle l’utilisateur touche l’écran 500 pixels à l’extérieur de l’emplacement où se trouve le caractère. La première ligne où `desiredVelocity.X` est défini affecte la valeur 500. Toutefois, si l’utilisateur touche l’écran à une distance de seulement 100 unités à partir du caractère, le `desiredVelocity.X` serait défini sur 100. Le résultat serait que la vitesse de déplacement du caractère répondait à la distance entre le point tactile et le caractère. Étant donné que nous voulons que le caractère se déplace toujours à la même vitesse, nous devons modifier le desiredVelocity.

L' `if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)` instruction vérifie si la vélocité est différente de zéro: en d’autres termes, elle vérifie que l’utilisateur ne touche pas le même emplacement que la position actuelle du caractère. Si ce n’est pas le cas, nous devons définir la vitesse du caractère sur constant, quelle que soit la distance entre le toucher. Pour ce faire, nous mettons en normal le vecteur de vélocité, ce qui donne une longueur de 1. Un vecteur de vélocité de 1 signifie que le caractère se déplacera à 1 pixel par seconde. Nous allons accélérer cela en multipliant la valeur par la vitesse souhaitée de 200.


### <a name="applying-velocity-to-position"></a>Application de la vélocité à la position

La rapidité retournée par `GetDesiredVelocityFromInput` doit être appliquée aux valeurs et `Y` du `X` caractère pour avoir un effet au moment de l’exécution. Nous allons modifier la `Update` méthode comme suit:

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

Ce que nous avons implémenté ici est appelé déplacement *basé sur le temps* (par opposition au déplacement à *base* de trame). Le déplacement basé sur le temps multiplie une valeur de vélocité (dans notre cas, les valeurs `velocity` stockées dans la variable) en fonction du temps écoulé depuis la dernière mise à `gameTime.ElapsedGameTime.TotalSeconds`jour, qui est stockée dans. Si le jeu s’exécute en moins d’images par seconde, le temps écoulé entre les frames augmente, le résultat final est que les objets qui utilisent le mouvement basé sur le temps se déplaceront toujours à la même vitesse, quelle que soit la fréquence d’images.

Si nous exécutons notre jeu maintenant, nous voyons que le personnage se déplace vers l’emplacement tactile:

![](part2-images/image6.gif "Le caractère se déplace vers l’emplacement tactile")

## <a name="matching-movement-and-animation"></a>Mouvement et animation correspondants

Une fois notre caractère déplacé et en lisant une seule animation, nous pouvons définir le reste de nos animations, puis les utiliser pour refléter le mouvement du caractère. Une fois terminé, nous aurons huit animations au total:

- Animations pour remonter, vers le haut, vers la gauche et vers la droite
- Animations pour rester debout et face vers le haut, vers le haut, vers la gauche et vers la droite

### <a name="defining-the-rest-of-the-animations"></a>Définition du reste des animations

Nous allons d’abord ajouter `Animation` les instances à `CharacterEntity` la classe pour toutes nos animations dans le même emplacement que celui où `walkDown`nous avons ajouté. Une fois cela fait, le `CharacterEntity` aura les membres suivants `Animation` :

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

Nous allons maintenant définir les animations dans le `CharacterEntity` constructeur comme suit:

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

Nous devons noter que le code ci-dessus a été `CharacterEntity` ajouté au constructeur afin de raccourcir cette procédure pas à pas. En général, les jeux séparent la définition des animations de caractères dans leurs propres classes ou chargent ces informations à partir d’un format de données tel que XML ou JSON.

Ensuite, nous allons ajuster la logique pour utiliser les animations en fonction de la direction dans laquelle le caractère est déplacé, ou en fonction de la dernière animation si le caractère vient d’être arrêté. Pour ce faire, nous allons modifier la `Update` méthode:


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

Le code permettant de changer les animations est divisé en deux blocs. La première vérifie si la vélocité n’est pas égale à `Vector2.Zero` : cela nous indique si le caractère est déplacé. Si le caractère est déplacé, nous pouvons examiner les valeurs et `velocity.X` `velocity.Y` pour déterminer l’animation de parcours à lire.

Si le caractère n’est pas déplacé, nous voulons définir le caractère `currentAnimation` sur une animation debout, mais nous le `currentAnimation` faisons uniquement si est une animation de marche ou si une animation n’a pas été définie. Si le `currentAnimation` n’est pas l’une des quatre animations de marche, le caractère est déjà debout et n’a donc pas `currentAnimation`besoin d’être modifié.

Le résultat de ce code est que le caractère s’anime correctement lorsqu’il progresse, puis confronte à la dernière direction qu’il parvient lorsqu’il s’arrête:

![](part2-images/image7.gif "Le résultat de ce code est que le caractère s’anime correctement lorsqu’il progresse, puis fait face à la dernière direction qu’il parcourt lorsqu’il s’arrête.")

## <a name="summary"></a>Récapitulatif

Cette procédure pas à pas a montré comment travailler avec un jeu d’éléments multiplateformes pour créer un jeu multiplateforme avec des sprites, le déplacement d’objets, la détection d’entrée et l’animation. Il aborde la création d’une classe d’animation à usage général. Elle a également montré comment créer une entité de caractère pour organiser la logique du code.

## <a name="related-links"></a>Liens associés

- [Ressource d’image CharacterSheet (exemple)](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)
- [Parcours du jeu terminé (exemple)](https://docs.microsoft.com/samples/xamarin/mobile-samples/walkinggamemg/)
