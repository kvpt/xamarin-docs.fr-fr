---
title: Dessin de graphiques 3D avec des vertex dans un monojeu
description: Le monojeu prend en charge l’utilisation de tableaux de vertex pour définir la manière dont un objet 3D est rendu par point. Les utilisateurs peuvent tirer parti des tableaux de vertex pour créer une géométrie dynamique, implémenter des effets spéciaux et améliorer l’efficacité de leur rendu par le biais de l’élimination.
ms.prod: xamarin
ms.assetid: 932AF5C2-884D-46E1-9455-4C359FD7C092
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: dfb03815f8642519cecf49ab7b626b9575821af1
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937629"
---
# <a name="drawing-3d-graphics-with-vertices-in-monogame"></a>Dessin de graphiques 3D avec des vertex dans un monojeu

_Le monojeu prend en charge l’utilisation de tableaux de vertex pour définir la manière dont un objet 3D est rendu par point. Les utilisateurs peuvent tirer parti des tableaux de vertex pour créer une géométrie dynamique, implémenter des effets spéciaux et améliorer l’efficacité de leur rendu par le biais de l’élimination._

Les utilisateurs qui ont lu le [Guide sur les modèles de rendu](~/graphics-games/monogame/3d/part1.md) sont familiarisés avec le rendu d’un modèle 3D en monojeu. La `Model` classe est un moyen efficace de restituer des graphiques 3D lorsque vous travaillez avec des données définies dans un fichier (par exemple,. FBX), et lorsque vous traitez des données statiques. Certains jeux nécessitent que la géométrie 3D soit définie ou manipulée dynamiquement au moment de l’exécution. Dans ce cas, nous pouvons utiliser des tableaux de *vertex* pour définir et restituer la géométrie. Un sommet est un terme général pour un point dans l’espace 3D qui fait partie d’une liste triée utilisée pour définir la géométrie. En général, les vertex sont triés de manière à définir une série de triangles.

Pour vous aider à visualiser la manière dont les vertex sont utilisés pour créer des objets 3D, prenons la sphère suivante :

![Pour mieux visualiser la manière dont les vertex sont utilisés pour créer des objets 3D, envisagez cette sphère](part2-images/image1.png)

Comme indiqué ci-dessus, la sphère est clairement composée de plusieurs triangles. Nous pouvons afficher le filaire de la sphère pour voir comment les vertex se connectent aux triangles de forme :

![Affichez le filaire de la sphère pour voir comment les vertex se connectent aux triangles de forme](part2-images/image2.png)

Cette procédure pas à pas traite des sujets suivants :

- Création d’un projet
- Création des vertex
- Ajout de code de dessin
- Rendu avec une texture
- Modification des coordonnées de texture
- Rendu des vertex avec des modèles

Le projet terminé contient un étage à damier qui est dessiné à l’aide d’un tableau de vertex :

![Le projet terminé contient un étage à damier qui est dessiné à l’aide d’un tableau de vertex](part2-images/image3.png)

## <a name="creating-a-project"></a>Création d’un projet

Tout d’abord, nous allons télécharger un projet qui servira de point de départ. Nous allons utiliser le projet de modèle [qui se trouve ici](https://docs.microsoft.com/samples/xamarin/mobile-samples/modelrenderingmg/).

Une fois que vous avez téléchargé et décompressé, ouvrez et exécutez le projet. Nous pensons que six modèles de robot sont dessinés à l’écran :

![Six modèles de robot dessinés à l’écran](part2-images/image4.png)

À la fin de ce projet, nous allons combiner notre propre rendu de vertex personnalisé avec le robot `Model` , donc nous n’allons pas supprimer le code de rendu du robot. Au lieu de cela, nous allons simplement effacer l' `Game1.Draw` appel pour supprimer le dessin des 6 robots pour l’instant. Pour ce faire, ouvrez le fichier **Game1.cs** et recherchez la `Draw` méthode. Modifiez-le de sorte qu’il contienne le code suivant :

```csharp
protected override void Draw(GameTime gameTime)
{
  GraphicsDevice.Clear(Color.CornflowerBlue);
  base.Draw(gameTime);
}
```

Cela entraîne l’affichage d’un écran bleu vide :

![Cela entraîne l’affichage d’un écran bleu vide pour le jeu](part2-images/image5.png)

## <a name="creating-the-vertices"></a>Création des vertex

Nous allons créer un tableau de vertex pour définir notre géométrie. Dans cette procédure pas à pas, nous allons créer un plan 3D (un carré dans l’espace 3D, et non un avion). Bien que notre plan ait quatre côtés et quatre coins, il est composé de deux triangles, chacun d’entre eux nécessitant trois vertex. Par conséquent, nous allons définir six points au total.

Jusqu’à présent, nous avons parlé des vertex dans un sens général, mais le monojeu fournit des structs standard qui peuvent être utilisés pour les vertex :

- `Microsoft.Xna.Framework.Graphics.VertexPositionColor`
- `Microsoft.Xna.Framework.Graphics.VertexPositionColorTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionNormalTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionTexture`

Le nom de chaque type indique les composants qu’il contient. Par exemple, `VertexPositionColor` contient des valeurs pour position et couleur. Examinons chacun des composants :

- Position : tous les types vertex incluent un `Position` composant. Les `Position` valeurs définissent l’emplacement du vertex dans l’espace 3D (X, Y et Z).
- Couleur : les sommets peuvent éventuellement spécifier une `Color` valeur pour effectuer une teinte personnalisée.
- Normal : les normales définissent la façon dont la surface de l’objet est dirigée. Les normales sont nécessaires si le rendu d’un objet avec éclairage étant donné que la direction d’une surface a un impact sur la quantité de lumière qu’il reçoit. Les normales sont généralement spécifiées comme *vecteur d’unité* : un vecteur 3D dont la longueur est égale à 1.
- Texture : la texture fait référence aux coordonnées de la texture, c’est-à-dire la partie d’une texture qui doit apparaître à un sommet donné. Les valeurs de texture sont nécessaires si le rendu d’un objet 3D avec une texture est nécessaire. Les coordonnées de texture sont des coordonnées normalisées, ce qui signifie que les valeurs sont comprises entre 0 et 1. Nous traiterons des coordonnées de texture plus en détail plus loin dans ce guide.

Notre plan servira d’étage et nous souhaitons appliquer une texture lors de la réalisation de notre rendu. nous utiliserons donc le `VertexPositionTexture` type pour définir nos sommets.

Tout d’abord, nous allons ajouter un membre à notre classe Game1 :

```csharp
VertexPositionTexture[] floorVerts;
```

Ensuite, définissez nos sommets dans `Game1.Initialize` . Notez que le modèle fourni mentionné plus haut dans cet article ne contient pas de `Game1.Initialize` méthode, donc nous devons ajouter la méthode entière à `Game1` :

```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];
    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);
    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;
    // We’ll be assigning texture values later
    base.Initialize ();
}
```

Pour mieux visualiser l’aspect de nos sommets, examinez le diagramme suivant :

![Pour mieux visualiser ce à quoi ressemblera les vertex, examinez ce diagramme](part2-images/image6.png)

Nous devons nous appuyer sur notre schéma pour visualiser les sommets jusqu’à la fin de l’implémentation de notre code de rendu.

## <a name="adding-drawing-code"></a>Ajout de code de dessin

Maintenant que nous avons défini les positions de notre géométrie, nous pouvons écrire notre code de rendu.

Tout d’abord, nous devons définir une `BasicEffect` instance qui contiendra des paramètres de rendu tels que la position et l’éclairage. Pour ce faire, ajoutez un `BasicEffect` membre à la `Game1` classe ci-dessous, où le `floorVerts` champ est défini :

```csharp
...
VertexPositionTexture[] floorVerts;
// new code:
BasicEffect effect;
```

Ensuite, modifiez la `Initialize` méthode pour définir l’effet :

```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;
    // new code:
    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
}
```

À présent, nous pouvons ajouter du code pour effectuer le dessin :

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
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

    foreach (var pass in effect.CurrentTechnique.Passes)
    {
        pass.Apply ();

        graphics.GraphicsDevice.DrawUserPrimitives (
            // We’ll be rendering two trinalges
            PrimitiveType.TriangleList,
            // The array of verts that we want to render
            floorVerts,
            // The offset, which is 0 since we want to start
            // at the beginning of the floorVerts array
            0,
            // The number of triangles to draw
            2);
    }
}
```

Nous devons appeler `DrawGround` notre `Game1.Draw` :

```csharp
protected override void Draw (GameTime gameTime)
{
    GraphicsDevice.Clear (Color.CornflowerBlue);

    DrawGround ();

    base.Draw (gameTime);
}
```

L’application affiche ce qui suit lors de son exécution :

![L’application l’affiche quand elle est exécutée](part2-images/image7.png)

Examinons quelques-uns des détails dans le code ci-dessus.

### <a name="view-and-projection-properties"></a>Propriétés de la vue et de la projection

Les `View` `Projection` Propriétés et contrôlent le mode d’affichage de la scène. Nous allons modifier ce code ultérieurement quand nous rajoutons le code de rendu du modèle. Plus précisément, `View` contrôle l’emplacement et l’orientation de l’appareil photo et `Projection` contrôle le *champ de la vue* (qui peut être utilisé pour effectuer un zoom sur l’appareil photo).

### <a name="techniques-and-passes"></a>Techniques et passes

Une fois que nous avons affecté des propriétés à notre effet, nous pouvons effectuer le rendu réel.

La propriété n’est pas modifiée `CurrentTechnique` dans cette procédure pas à pas, mais les jeux plus avancés peuvent avoir un effet unique qui peut effectuer des dessins de différentes façons (par exemple, la façon dont la valeur de couleur est appliquée). Chacun de ces modes de rendu peut être représenté sous la forme d’une technique qui peut être assignée avant le rendu. En outre, chaque technique peut nécessiter plusieurs passages pour s’afficher correctement. Les effets peuvent nécessiter plusieurs passes si vous restituez des éléments visuels complexes tels qu’une surface lumineuse ou un fourrure.

Le point important à retenir est que la `foreach` boucle permet au même code C# de restituer n’importe quel effet, quelle que soit la complexité de l’élément sous-jacent `BasicEffect` .

### <a name="drawuserprimitives"></a>DrawUserPrimitives

`DrawUserPrimitives`est l’endroit où les vertex sont rendus. Le premier paramètre indique à la méthode comment nous avons organisé nos sommets. Nous les avons structurées de sorte que chaque triangle est défini par trois sommets ordonnés. nous utilisons donc la `PrimitiveType.TriangleList` valeur.

Le deuxième paramètre est le tableau de vertex que nous avons défini précédemment.

Le troisième paramètre spécifie le premier index à dessiner. Étant donné que nous souhaitons que notre tableau de vertex entier soit rendu, nous transmettons une valeur de 0.

Enfin, nous spécifions le nombre de triangles à afficher. Notre tableau de vertex contient deux triangles. par conséquent, transmettez une valeur de 2.

## <a name="rendering-with-a-texture"></a>Rendu avec une texture

À ce stade, notre application affiche un plan blanc (en perspective). Nous allons ensuite ajouter une texture à votre projet à utiliser lors du rendu de notre plan.

Pour simplifier les choses, nous allons ajouter le. png directement à notre projet plutôt que d’utiliser l’outil de pipeline monojeu. Pour ce faire, téléchargez [ce fichier. png](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true) sur votre ordinateur. Une fois téléchargé, cliquez avec le bouton droit sur le dossier **content** dans le panneau solutions, puis sélectionnez **Ajouter>ajouter des fichiers...** . Si vous travaillez sur Android, ce dossier se trouve dans le dossier **ressources** du projet Android. Si sur iOS, ce dossier sera à la racine du projet iOS. Accédez à l’emplacement où **checkerboard.png** est enregistré et sélectionnez ce fichier. Sélectionnez cette option pour copier le fichier dans le répertoire.

Ensuite, nous allons ajouter le code pour créer notre `Texture2D` instance. Tout d’abord, ajoutez le `Texture2D` en tant que membre de `Game1` sous l' `BasicEffect` instance :

```csharp
...
BasicEffect effect;
// new code:
Texture2D checkerboardTexture;
```

Modifiez `Game1.LoadContent` comme suit :

```csharp
protected override void LoadContent()
{
    // Notice that loading a model is very similar
    // to loading any other XNB (like a Texture2D).
    // The only difference is the generic type.
    model = Content.Load<Model> ("robot");

    // We aren't using the content pipeline, so we need
    // to access the stream directly:
    using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
    {
        checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
    }
}
```

Modifiez ensuite la `DrawGround` méthode. La seule modification nécessaire consiste à assigner `effect.TextureEnabled` à `true` et à affecter à la valeur `effect.Texture` `checkerboardTexture` :

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
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

    // new code:
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
```

Enfin, nous devons modifier la `Game1.Initialize` méthode pour affecter également des coordonnées de texture sur nos sommets :

```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;

    // New code:
    floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
    floorVerts [1].TextureCoordinate = new Vector2 (0, 1);
    floorVerts [2].TextureCoordinate = new Vector2 (1, 0);

    floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
    floorVerts [4].TextureCoordinate = new Vector2 (1, 1);
    floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
}
```

Si nous exécutons le code, nous pouvons voir que notre plan affiche maintenant un modèle de damier :

![Le plan affiche maintenant un modèle de damier](part2-images/image8.png)

## <a name="modifying-texture-coordinates"></a>Modification des coordonnées de texture

Le monojeu utilise des coordonnées de texture normalisées, qui sont des coordonnées comprises entre 0 et 1, et non entre 0 et la largeur ou la hauteur de la texture. Le diagramme suivant peut vous aider à visualiser les coordonnées normalisées :

![Ce diagramme peut vous aider à visualiser les coordonnées normalisées](part2-images/image9.png)

Les coordonnées de texture normalisées autorisent le redimensionnement de la texture sans avoir à réécrire le code ou à recréer des modèles (tels que les fichiers. FBX). Cela est possible, car les coordonnées normalisées représentent un ratio plutôt que des pixels spécifiques. Par exemple, (1, 1) représente toujours le coin inférieur droit, quelle que soit la taille de la texture.

Nous pouvons modifier l’assignation de la coordonnée de texture pour utiliser une variable unique pour le nombre de répétitions :

```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

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

    base.Initialize ();
}
```

Cela entraîne la répétition de la texture 20 fois :

![Cela entraîne la répétition de la texture 20 fois](part2-images/image10.png)

## <a name="rendering-vertices-with-models"></a>Rendu des vertex avec des modèles

Maintenant que notre plan est correctement rendu, nous pouvons rajouter les modèles pour tout afficher ensemble. Tout d’abord, nous allons rajouter le code de modèle à notre `Game1.Draw` méthode (avec des positions modifiées) :

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    DrawGround ();

    DrawModel (new Vector3 (-4, 0, 3));
    DrawModel (new Vector3 ( 0, 0, 3));
    DrawModel (new Vector3 ( 4, 0, 3));

    DrawModel (new Vector3 (-4, 4, 3));
    DrawModel (new Vector3 ( 0, 4, 3));
    DrawModel (new Vector3 ( 4, 4, 3));

    base.Draw(gameTime);
}
```

Nous allons également créer un `Vector3` dans `Game1` pour représenter la position de la caméra. Nous allons ajouter un champ sous notre `checkerboardTexture` déclaration :

```csharp
...
Texture2D checkerboardTexture;
// new code:
Vector3 cameraPosition = new Vector3(0, 10, 10);
```

Ensuite, supprimez la `cameraPosition` variable locale de la `DrawModel` méthode :

```csharp
void DrawModel(Vector3 modelPosition)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = Matrix.CreateTranslation (modelPosition);

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);
            ...
```

Supprimez de la même manière la `cameraPosition` variable locale de la `DrawGround` méthode :

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);
    ...
```

Maintenant, si nous exécutons le code, nous pouvons voir les modèles et le sol en même temps :

![Les modèles et le sol s’affichent en même temps](part2-images/image11.png)

Si nous modifions la position de la caméra (par exemple, en accroissant sa valeur X, ce qui, dans ce cas, déplace l’appareil photo vers la gauche), nous pouvons voir que la valeur a un impact sur le sol et les modèles :

```csharp
Vector3 cameraPosition = new Vector3(15, 10, 10);
```

Ce code génère les éléments suivants :

![Ce code génère cette vue](part2-images/image3.png)

## <a name="summary"></a>Résumé

Cette procédure pas à pas a montré comment utiliser un tableau de vertex pour effectuer un rendu personnalisé. Dans ce cas, nous avons créé un étage à damier en combinant notre rendu basé sur le vertex avec une texture et `BasicEffect` , mais le code présenté ici sert de base pour tout rendu 3D. Nous avons également démontré que le rendu basé sur un vertex peut être mélangé à des modèles dans la même scène.

## <a name="related-links"></a>Liens associés

- [Fichier damier (exemple)](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true)
- [Projet terminé (exemple)](https://docs.microsoft.com/samples/xamarin/mobile-samples/modelsandvertsmg/)
