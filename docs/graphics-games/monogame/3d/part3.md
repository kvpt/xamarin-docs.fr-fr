---
title: Coordonnées 3D en monojeu
description: La compréhension du système de coordonnées 3D est une étape importante dans le développement de jeux 3D. Le monojeu fournit un certain nombre de classes pour le positionnement, l’orientation et la mise à l’échelle des objets dans l’espace 3D.
ms.prod: xamarin
ms.assetid: A4130995-48FD-4E2E-9C2B-ADCEFF35BE3A
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: a38f4b81f684d6d416e6abe017bc463e3097c6b1
ms.sourcegitcommit: 93697a20e6fc7da547a8714ac109d7953b61d63f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72980867"
---
# <a name="3d-coordinates-in-monogame"></a>Coordonnées 3D en monojeu

_La compréhension du système de coordonnées 3D est une étape importante dans le développement de jeux 3D. Le monojeu fournit un certain nombre de classes pour le positionnement, l’orientation et la mise à l’échelle des objets dans l’espace 3D._

Pour développer des jeux 3D, vous devez comprendre comment manipuler des objets dans un système de coordonnées 3D. Cette procédure pas à pas explique comment manipuler des objets visuels (plus particulièrement un modèle). Nous allons nous appuyer sur les concepts de contrôle d’un modèle pour créer une classe de caméra 3D.

Les concepts présentés proviennent de l’algèbre linéaire, mais nous allons adopter une approche pratique afin que n’importe quel utilisateur sans un arrière-plan mathématique fort puisse appliquer ces concepts dans leurs propres jeux.

Nous allons aborder les sujets suivants :

- Création d'un projet
- Création d’une entité robot
- Déplacement de l’entité robot
- Multiplication de matrices
- Création de l’entité Camera
- Déplacement de l’appareil photo avec l’entrée

Une fois que vous avez terminé, nous aurons un projet avec un robot qui se déplace dans un cercle et une caméra qui peut être contrôlé par entrée tactile :

![](part3-images/image1.gif "Once finished, the app will include a project with a robot moving in a circle and a camera which can be controlled by touch input")

## <a name="creating-a-project"></a>Création d'un projet

Cette procédure pas à pas se concentre sur le déplacement d’objets dans l’espace 3D. Nous allons commencer par le projet de rendu des modèles et des tableaux de vertex [qui se trouvent ici](https://docs.microsoft.com/samples/xamarin/mobile-samples/modelsandvertsmg/). Une fois téléchargé, décompressez et ouvrez le projet pour vérifier qu’il s’exécute et que les éléments suivants doivent s’afficher :

![](part3-images/image2.png "Once downloaded, unzip and open the project to make sure it runs and this view should be displayed")

## <a name="creating-a-robot-entity"></a>Création d’une entité robot

Avant de commencer à déplacer notre robot, nous allons créer une classe `Robot` pour contenir la logique de dessin et de déplacement. Les développeurs de jeux font référence à l’encapsulation de la logique et des données en tant qu' *entité*.

Ajoutez un nouveau fichier de classe vide à la bibliothèque de classes portable **MonoGame3D** (et non à l’ModelAndVerts. Android spécifique à la plateforme). Nommez-le **robot** , puis cliquez sur **nouveau**:

![](part3-images/image3.png "Name it Robot and click New")

Modifiez la classe `Robot` comme suit :

```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Content;

namespace MonoGame3D
{
    public class Robot
    {
        Model model;

        public void Initialize(ContentManager contentManager)
        {
            model = contentManager.Load<Model> ("robot");

        }

        // For now we'll take these values in, eventually we'll
        // take a Camera object
        public void Draw(Vector3 cameraPosition, float aspectRatio)
        {
            foreach (var mesh in model.Meshes)
            {
                foreach (BasicEffect effect in mesh.Effects)
                {
                    effect.EnableDefaultLighting ();
                    effect.PreferPerPixelLighting = true;

                    effect.World = Matrix.Identity; 
                    var cameraLookAtVector = Vector3.Zero;
                    var cameraUpVector = Vector3.UnitZ;

                    effect.View = Matrix.CreateLookAt (
                        cameraPosition, cameraLookAtVector, cameraUpVector);

                    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                    float nearClipPlane = 1;
                    float farClipPlane = 200;

                    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

                }

                // Now that we've assigned our properties on the effects we can
                // draw the entire mesh
                mesh.Draw ();
            }
        }
    }
}
```

Le code `Robot` est essentiellement le même code dans `Game1` pour le dessin d’un `Model`. Pour une révision sur `Model` le chargement et le dessin, consultez [ce guide sur l’utilisation des modèles](~/graphics-games/monogame/3d/part1.md). Nous pouvons maintenant supprimer tous les `Model` le chargement et le rendu du code à partir de `Game1`, puis le remplacer par une instance `Robot` :

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        Vector3 cameraPosition = new Vector3(15, 10, 10);

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;

            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

            floorVerts [0].Position = new Vector3 (-20, -20, 0);
            floorVerts [1].Position = new Vector3 (-20,  20, 0);
            floorVerts [2].Position = new Vector3 ( 20, -20, 0);

            floorVerts [3].Position = floorVerts[1].Position;
            floorVerts [4].Position = new Vector3 ( 20,  20, 0);
            floorVerts [5].Position = floorVerts[2].Position;

            int repetitions = 20;

            floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
            floorVerts [1].TextureCoordinate = new Vector2 (0, repetitions);
            floorVerts [2].TextureCoordinate = new Vector2 (repetitions, 0);

            floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
            floorVerts [4].TextureCoordinate = new Vector2 (repetitions, repetitions);
            floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

            effect = new BasicEffect (graphics.GraphicsDevice);

            robot = new Robot ();
            robot.Initialize (Content);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            float aspectRatio = 
                graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
            robot.Draw (cameraPosition, aspectRatio);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);

            float aspectRatio = 
                graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
            float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
            float nearClipPlane = 1;
            float farClipPlane = 200;

            effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

            effect.TextureEnabled = true;
            effect.Texture = checkerboardTexture;

            foreach (var pass in effect.CurrentTechnique.Passes)
            {
                pass.Apply ();

                graphics.GraphicsDevice.DrawUserPrimitives (
                            PrimitiveType.TriangleList,
                    floorVerts,
                    0,
                    2);
            }
        }
    }
}
```

Si nous exécutons le code maintenant, nous aurons une scène avec un seul robot dessiné principalement sous le plancher :

![](part3-images/image4.png "If the code is run now, the app will display a scene with only one robot which is drawn mostly under the floor")

## <a name="moving-the-robot"></a>Déplacement du robot

Maintenant que nous avons une classe `Robot`, nous pouvons ajouter une logique de mouvement au robot. Dans ce cas, nous allons simplement faire passer le robot dans un cercle en fonction de la durée du jeu. Il s’agit d’une implémentation quelque peu pratique pour un jeu réel, car un personnage peut généralement répondre à l’intelligence artificielle ou d’entrée, mais fournit un environnement pour nous pour explorer le positionnement et la rotation 3D.

Les seules informations dont nous aurons besoin en dehors de la classe `Robot` sont l’heure actuelle du jeu. Nous allons ajouter une méthode `Update` qui prendra un paramètre `GameTime`. Ce paramètre de `GameTime` sera utilisé pour incrémenter une variable d’angle que nous allons utiliser pour déterminer la position finale du robot.

Tout d’abord, nous allons ajouter le champ angle à la classe `Robot` sous le champ `model` :

```csharp
public class Robot
{
    public Model model;

    // new code:
    float angle;
    ...
```

 Nous pouvons à présent incrémenter cette valeur dans une fonction de `Update` :

```csharp
public void Update(GameTime gameTime)
{
    // TotalSeconds is a double so we need to cast to float
    angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
}
```

Nous devons nous assurer que la méthode `Update` est appelée à partir de `Game1.Update`:

```csharp
protected override void Update(GameTime gameTime)
{
    robot.Update (gameTime);
    base.Update(gameTime);
}
```

Bien entendu, à ce stade, le champ angle n’a aucun effet, nous devons écrire du code pour l’utiliser. Nous allons modifier la méthode de `Draw` afin que nous puissions calculer le `Matrix` universel dans une méthode dédiée : 

```csharp
public void Draw(Vector3 cameraPosition, float aspectRatio)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;
            // We’ll be doing our calculations here...
            effect.World = GetWorldMatrix();

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);

            float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
            float nearClipPlane = 1;
            float farClipPlane = 200;

            effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
        }

        mesh.Draw ();
    }
}
```

Ensuite, nous allons implémenter la méthode `GetWorldMatrix` dans la classe `Robot` :

```csharp
Matrix GetWorldMatrix()
{
    const float circleRadius = 8;
    const float heightOffGround = 3;

    // this matrix moves the model "out" from the origin
    Matrix translationMatrix = Matrix.CreateTranslation (
        circleRadius, 0, heightOffGround);

    // this matrix rotates everything around the origin
    Matrix rotationMatrix = Matrix.CreateRotationZ (angle);

    // We combine the two to have the model move in a circle:
    Matrix combined = translationMatrix * rotationMatrix;

    return combined;
}
```

Le résultat de l’exécution de ce code entraîne le déplacement de l’automate dans un cercle :

![](part3-images/image5.gif "Running this code results in the robot moving in a circle")

## <a name="matrix-multiplication"></a>Multiplication de matrices

Le code ci-dessus fait pivoter le robot en créant un `Matrix` dans la méthode `GetWorldMatrix`. Le struct `Matrix` contient 16 valeurs float qui peuvent être utilisées pour traduire (définir la position), faire pivoter et mettre à l’échelle (définir la taille). Lorsque nous attribuons la propriété `effect.World`, nous indiquons au système de rendu sous-jacent comment positionner, dimensionner et orienter tout ce qui se produit pour le dessin (une `Model` ou une géométrie à partir de vertex). 

Heureusement, le struct `Matrix` comprend un certain nombre de méthodes qui simplifient la création de types courants de matrices. Le premier utilisé dans le code ci-dessus est `Matrix.CreateTranslation`. La *traduction* de terme mathématique fait référence à une opération qui produit un point (ou dans le cas d’un modèle) passant d’un emplacement à un autre sans aucune autre modification (telle que la rotation ou le redimensionnement). La fonction prend une valeur X, Y et Z pour la traduction. Si nous voyons notre scène de haut en haut, notre méthode de `CreateTranslation` (isolation) effectue les opérations suivantes :

![](part3-images/image6.png "The CreateTranslation method in isolation performs this action")

La deuxième matrice que nous créons est une matrice de rotation à l’aide de la matrice de `CreateRotationZ`. Il s’agit de l’une des trois méthodes qui peuvent être utilisées pour créer une rotation :

- `CreateRotationX`
- `CreateRoationY`
- `CreateRotationZ`

Chaque méthode crée une matrice de rotation en tournant sur un axe donné. Dans notre cas, nous allons faire pivoter autour de l’axe Z, qui pointe vers le haut. Les éléments suivants peuvent vous aider à visualiser le fonctionnement de la rotation basée sur l’axe :

![](part3-images/image7.png "This can help visualize how axis-based rotation works")

Nous utilisons également la méthode `CreateRotationZ` avec le champ angle, qui s’incrémente au fil du temps en raison de l’appel de notre méthode `Update`. Le résultat est que la méthode `CreateRotationZ` amène notre robot à Orbiter autour de l’origine à mesure que le temps passe.

La dernière ligne de code combine les deux matrices en une seule :

```csharp
Matrix combined = translationMatrix * rotationMatrix;
```

C’est ce que l’on appelle la multiplication de matrice, qui fonctionne légèrement différente de la multiplication normale. La *propriété commutative de la multiplication* indique que l’ordre des nombres dans une opération de multiplication ne change pas le résultat. Autrement dit, 3 \* 4 équivaut à 4 \* 3. La multiplication de matrice diffère en ce qu’elle n’est pas commutative. Autrement dit, la ligne ci-dessus peut être lue comme « appliquer le translationMatrix pour déplacer le modèle, puis faire pivoter tout en appliquant le rotationMatrix ». Nous pouvons visualiser la manière dont la ligne ci-dessus affecte la position et la rotation comme suit :

![](part3-images/image8.png "A visualization pf the way that the above line affects the position and rotation")

Pour mieux comprendre comment l’ordre de multiplication des matrices peut avoir un impact sur le résultat, prenez en compte les éléments suivants, où la multiplication de la matrice est inversée :

```csharp
Matrix combined = rotationMatrix * translationMatrix;
```

Le code ci-dessus commence par faire pivoter le modèle sur place, puis le traduire :

![](part3-images/image9.png "The code above would first rotate the model in-place, then translate it")

Si nous exécutons le code avec la multiplication inversée, nous remarquerons que puisque la rotation s’applique en premier, elle n’affecte que l’orientation du modèle et la position du modèle reste la même. En d’autres termes, le modèle pivote en place :

![](part3-images/image10.gif "The model rotates in place")

## <a name="creating-the-camera-entity"></a>Création de l’entité Camera

L’entité `Camera` contient toute la logique nécessaire pour effectuer un déplacement basé sur l’entrée et fournir des propriétés pour affecter des propriétés sur la classe `BasicEffect`.

Tout d’abord, nous allons implémenter une caméra statique (sans déplacement basé sur l’entrée) et l’intégrer dans notre projet existant. Ajoutez une nouvelle classe à la bibliothèque de classes portable **MonoGame3D** (le même projet que `Robot.cs`) et nommez-la **appareil photo**. Remplacez le contenu du fichier par le code suivant :

```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Camera
    {
        // We need this to calculate the aspectRatio
        // in the ProjectionMatrix property.
        GraphicsDevice graphicsDevice;

        Vector3 position = new Vector3(15, 10, 10);

        public Matrix ViewMatrix
        {
            get
            {
                var lookAtVector = Vector3.Zero;
                var upVector = Vector3.UnitZ;

                return Matrix.CreateLookAt (
                    position, lookAtVector, upVector);
            }
        }

        public Matrix ProjectionMatrix
        {
            get
            {
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                float nearClipPlane = 1;
                float farClipPlane = 200;
                float aspectRatio = graphicsDevice.Viewport.Width / (float)graphicsDevice.Viewport.Height;

                return Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
            }
        }

        public Camera(GraphicsDevice graphicsDevice)
        {
            this.graphicsDevice = graphicsDevice;
        }

        public void Update(GameTime gameTime)
        {
            // We'll be doing some input-based movement here
        }
    }
}
```

Le code ci-dessus est très similaire au code de `Game1` et `Robot` qui assignent les matrices sur `BasicEffect`. 

Nous pouvons maintenant intégrer la nouvelle classe `Camera` dans nos projets existants. Tout d’abord, nous allons modifier la classe `Robot` pour prendre une instance `Camera` dans sa méthode `Draw`, ce qui élimine un grand nombre de code en double. Remplacez la méthode `Robot.Draw` par le code suivant :

```csharp
public void Draw(Camera camera)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = GetWorldMatrix();
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;
        }

        mesh.Draw ();
    }
}
```

Modifiez ensuite le fichier `Game1.cs` :

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        // New camera code
        Camera camera;

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;

            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

            floorVerts [0].Position = new Vector3 (-20, -20, 0);
            floorVerts [1].Position = new Vector3 (-20,  20, 0);
            floorVerts [2].Position = new Vector3 ( 20, -20, 0);

            floorVerts [3].Position = floorVerts[1].Position;
            floorVerts [4].Position = new Vector3 ( 20,  20, 0);
            floorVerts [5].Position = floorVerts[2].Position;

            int repetitions = 20;

            floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
            floorVerts [1].TextureCoordinate = new Vector2 (0, repetitions);
            floorVerts [2].TextureCoordinate = new Vector2 (repetitions, 0);

            floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
            floorVerts [4].TextureCoordinate = new Vector2 (repetitions, repetitions);
            floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

            effect = new BasicEffect (graphics.GraphicsDevice);

            robot = new Robot ();
            robot.Initialize (Content);

            // New camera code
            camera = new Camera (graphics.GraphicsDevice);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            robot.Update (gameTime);
            // New camera code
            camera.Update (gameTime);
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            // New camera code
            robot.Draw (camera);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
            // New camera code
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;

            effect.TextureEnabled = true;
            effect.Texture = checkerboardTexture;

            foreach (var pass in effect.CurrentTechnique.Passes)
            {
                pass.Apply ();

                graphics.GraphicsDevice.DrawUserPrimitives (
                            PrimitiveType.TriangleList,
                    floorVerts,
                    0,
                    2);
            }
        }
    }
}
```

Les modifications apportées à la `Game1` à partir de la version précédente (qui sont identifiées par `// New camera code`) sont les suivantes :

- champ `Camera` dans `Game1`
- instanciation de `Camera` dans `Game1.Initialize`
- `Camera.Update` appel dans `Game1.Update`
- `Robot.Draw` accepte maintenant un paramètre `Camera`
- `Game1.Draw` utilise à présent `Camera.ViewMatrix` et `Camera.ProjectionMatrix`

## <a name="moving-the-camera-with-input"></a>Déplacement de l’appareil photo avec l’entrée

Jusqu’à présent, nous avons ajouté une entité `Camera`, mais je n’ai rien fait avec elle pour modifier le comportement du Runtime. Nous allons ajouter un comportement qui permet à l’utilisateur d’effectuer les opérations suivantes :

- Appuyez sur le côté gauche de l’écran pour faire pivoter l’appareil photo vers la gauche
- Touchez le côté droit de l’écran pour activer l’appareil photo à droite
- Toucher au centre de l’écran pour avancer l’appareil photo

### <a name="making-lookat-relative"></a>Réalisation d’une Lookique relative

Tout d’abord, nous allons mettre à jour la classe `Camera` pour inclure un champ `angle` qui sera utilisé pour définir la direction de l' `Camera`. Actuellement, notre `Camera` détermine le sens dans lequel elle est dirigée via le `lookAtVector`local, qui est affecté à `Vector3.Zero`. En d’autres termes, notre `Camera` examine toujours l’origine. Si l’appareil photo se déplace, l’angle d’accès à l’appareil photo est également modifié :

![](part3-images/image11.gif "If the Camera moves, then the angle that the camera is facing will also change")

Nous souhaitons que les `Camera` soient dirigées vers la même direction, quel que soit leur emplacement, au moins jusqu’à ce que nous implémentions la logique permettant de faire pivoter les `Camera` à l’aide de l’entrée. La première modification consiste à ajuster la variable de `lookAtVector` en fonction de l’emplacement actuel, plutôt que de regarder une position absolue :

```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    // Let's start at X = 0 so we're looking at things head-on
    Vector3 position = new Vector3(0, 20, 10);

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    }
    ...
```

Ainsi, les `Camera` le monde entier. Notez que la valeur initiale de la `position` a été modifiée pour `(0, 20, 10)`, de sorte que la `Camera` est centrée sur l’axe X. L’exécution du jeu affiche :

![](part3-images/image12.png "Running the game displays this view")

### <a name="creating-an-angle-variable"></a>Création d’une variable d’angle

La variable `lookAtVector` contrôle l’angle que notre appareil photo affiche. Actuellement, il est corrigé pour afficher l’axe des Y négatif et légèrement incliné vers le haut (à partir de la valeur Z `-.5f`). Nous allons créer une variable `angle` qui sera utilisée pour ajuster la propriété `lookAtVector`. 

Dans les sections précédentes de cette procédure pas à pas, nous avons montré que les matrices peuvent être utilisées pour faire pivoter la façon dont les objets sont dessinés. Nous pouvons également utiliser des matrices pour faire pivoter des vecteurs comme le `lookAtVector` à l’aide de la méthode `Vector3.Transform`. 

Ajoutez un champ `angle` et modifiez la propriété `ViewMatrix` comme suit :

```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    Vector3 position = new Vector3(0, 20, 10);

    float angle;

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            // We'll create a rotation matrix using our angle
            var rotationMatrix = Matrix.CreateRotationZ (angle);
            // Then we'll modify the vector using this matrix:
            lookAtVector = Vector3.Transform (lookAtVector, rotationMatrix);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    }
    ...
```

### <a name="reading-input"></a>Lecture de l’entrée

Notre entité `Camera` peut désormais être entièrement contrôlée par le biais de ses variables de position et d’angle. il suffit de les modifier en fonction de l’entrée.

Tout d’abord, nous allons obtenir l’état du `TouchPanel` pour Rechercher l’endroit où l’utilisateur touche l’écran. Pour plus d’informations sur l’utilisation de la classe `TouchPanel`, consultez les informations de [référence sur l’API de](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_Touch_TouchPanel)l’écran tactile.

Si l’utilisateur touche le troisième à gauche, nous ajustons la valeur `angle` pour que le `Camera` pivote vers la gauche et, si l’utilisateur touche le troisième droit, nous allons faire pivoter l’autre méthode. Si l’utilisateur touche le troisième milieu de l’écran, nous allons déplacer le `Camera` vers l’avant.

Tout d’abord, ajoutez une instruction using pour qualifier les classes `TouchPanel` et `TouchCollection` dans `Camera.cs`:

```csharp
using Microsoft.Xna.Framework.Input.Touch; 
```

Ensuite, modifiez la méthode `Update` pour lire le panneau tactile et ajuster les variables `angle` et `position` de manière appropriée :

```csharp
public void Update(GameTime gameTime)
{
    TouchCollection touchCollection = TouchPanel.GetState();

    bool isTouchingScreen = touchCollection.Count > 0;
    if (isTouchingScreen)
    {
        var xPosition = touchCollection [0].Position.X;

        float xRatio = xPosition / (float)graphicsDevice.Viewport.Width;

        if (xRatio < 1 / 3.0f)
        {
            angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
        else if (xRatio < 2 / 3.0f)
        {
            var forwardVector = new Vector3 (0, -1, 0);

            var rotationMatrix = Matrix.CreateRotationZ (angle);
            forwardVector = Vector3.Transform (forwardVector, rotationMatrix);

            const float unitsPerSecond = 3;

            this.position += forwardVector * unitsPerSecond *
                (float)gameTime.ElapsedGameTime.TotalSeconds ;
        }
        else
        {
            angle -= (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
    }
}
```

À présent, le `Camera` répondra à l’entrée tactile :

![](part3-images/image1.gif "Now the Camera will respond to touch input")

La méthode Update commence par appeler `TouchPanel.GetState`, qui retourne une collection de touches. Même si `TouchPanel.GetState` peut retourner plusieurs points tactiles, nous ne nous soucions de la première fois que pour des raisons de simplicité.

Si l’utilisateur touche l’écran, le code vérifie si la première touche tactile se trouve à gauche, au milieu ou à droite de l’écran. Les tiers gauche et droit font pivoter l’appareil photo en accroissant ou en diminuant la `angle` variable en fonction de la valeur de `TotalSeconds` (afin que le jeu se comporte de la même manière quelle que soit la fréquence d’images).

Si l’utilisateur touche le troisième centre de l’écran, l’appareil photo se déplace vers l’avant. Cela est effectué en premier en obtenant le vecteur Forward, qui est initialement défini comme pointant vers l’axe Y négatif, puis pivoté par une matrice créée à l’aide de `Matrix.CreateRotationZ` et de la valeur `angle`. Enfin, le `forwardVector` est appliqué à `position` à l’aide du coefficient de `unitsPerSecond`.

## <a name="summary"></a>Récapitulatif

Cette procédure pas à pas explique comment déplacer et faire pivoter des `Models` dans l’espace 3D à l’aide de `Matrices` et de la propriété `BasicEffect.World`. Cette forme de mouvement fournit la base pour déplacer des objets dans les jeux 3D. Cette procédure pas à pas explique également comment implémenter une entité `Camera` pour afficher le monde à partir d’une position et d’un angle.

## <a name="related-links"></a>Liens associés

- [Lien de l’API monojeu](http://www.monogame.net/documentation/?page=api)
- [Projet terminé (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/monogame3dcamera)
