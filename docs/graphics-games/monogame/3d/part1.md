---
title: Utilisation de la classe de modèle
description: La classe Model simplifie grandement le rendu des objets 3D complexes par rapport à la méthode traditionnelle de rendu des graphiques 3D. Les objets de modèle sont créés à partir de fichiers de contenu, ce qui permet une intégration facile du contenu sans code personnalisé.
ms.prod: xamarin
ms.assetid: AD0A7971-51B1-4E38-B412-7907CE43CDDF
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 06d585e976ce41080682f531b9661ff06cedaebe
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437007"
---
# <a name="using-the-model-class"></a>Utilisation de la classe de modèle

_La classe Model simplifie grandement le rendu des objets 3D complexes par rapport à la méthode traditionnelle de rendu des graphiques 3D. Les objets de modèle sont créés à partir de fichiers de contenu, ce qui permet une intégration facile du contenu sans code personnalisé._

L’API monojeu comprend une `Model` classe qui peut être utilisée pour stocker les données chargées à partir d’un fichier de contenu et pour effectuer le rendu. Les fichiers de modèle peuvent être très simples (par exemple, un triangle de couleur unie) ou peuvent inclure des informations pour un rendu complexe, y compris la texturation et l’éclairage.

Cette procédure pas à pas utilise [un modèle 3D d’un robot](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true) et couvre les éléments suivants :

- Démarrage d’un nouveau projet de jeu
- Création de XNBs pour le modèle et sa texture
- Inclusion de XNBs dans le projet de jeu
- Dessin d’un modèle 3D
- Dessin de plusieurs modèles

Lorsque vous avez terminé, notre projet s’affiche comme suit :

![Exemple terminé avec six robots](part1-images/image1.png)

## <a name="creating-an-empty-game-project"></a>Création d’un projet de jeu vide

Nous devons d’abord configurer un projet de jeu appelé MonoGame3D. Pour plus d’informations sur la création d’un nouveau projet de monojeu, consultez [cette procédure pas à pas sur la création d’un projet multiplateforme multiplateforme](~/graphics-games/monogame/introduction/part1.md).

Avant de poursuivre, vérifiez que le projet s’ouvre et se déploie correctement. Une fois le déploiement terminé, nous devrions voir un écran bleu vide :

![Écran de jeu bleu vide](part1-images/image2.png)

## <a name="including-the-xnbs-in-the-game-project"></a>Inclusion de XNBs dans le projet de jeu

Le format de fichier. xnb est une extension standard pour le contenu généré (contenu créé par l' [outil de pipeline monojeu](http://www.monogame.net/documentation/?page=Pipeline)). Tout le contenu généré a un fichier source (qui est un fichier. FBX dans le cas de notre modèle) et un fichier de destination (un fichier. xnb). Le format. FBX est un format de modèle 3D courant qui peut être créé par des applications telles que [Maya](https://www.autodesk.com/products/maya/overview) et [Blender](https://www.blender.org/). 

La `Model` classe peut être construite en chargeant un fichier. xnb à partir d’un disque qui contient des données geometry 3D.   Ce fichier. xnb est créé via un projet de contenu. Les modèles monojeu incluent automatiquement un projet de contenu (avec l’extension. MGCP) dans notre dossier de contenu. Pour une présentation détaillée de l’outil de pipeline monojeu, consultez le [Guide de pipeline de contenu](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md).

Pour ce guide, nous allons ignorer l’utilisation de l’outil de pipeline monojeu et utiliser le. Fichiers XNB inclus ici. Notez que le. Les fichiers XNB diffèrent selon la plateforme. Veillez donc à utiliser l’ensemble de fichiers XNB approprié pour la plateforme que vous utilisez.

Nous allons décompresser le [ fichierContent.zip](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true) pour pouvoir utiliser les fichiers. xnb contenus dans notre jeu. Si vous travaillez sur un projet Android, cliquez avec le bouton droit sur le dossier **composants** dans le projet **WalkingGame. Android** . Si vous travaillez sur un projet iOS, cliquez avec le bouton droit sur le projet **WalkingGame. iOS** . Sélectionnez **Ajouter->ajouter des fichiers...** , puis sélectionnez les deux fichiers. xnb dans le dossier de la plateforme sur laquelle vous travaillez.

Les deux fichiers doivent faire partie de notre projet maintenant :

![Dossier de contenu de l’Explorateur de solutions avec les fichiers xnb](part1-images/xnbsinxs.png)

Visual Studio pour Mac peut ne pas définir automatiquement l’action de génération pour les XNBs récemment ajoutés. Pour iOS, cliquez avec le bouton droit sur chacun des fichiers, puis sélectionnez **générer une action->BundleResource**. Pour Android, cliquez avec le bouton droit sur chacun des fichiers, puis sélectionnez **générer une action->AndroidAsset**.

## <a name="rendering-a-3d-model"></a>Rendu d’un modèle 3D

La dernière étape nécessaire pour voir le modèle à l’écran consiste à ajouter le code de chargement et de dessin. Plus précisément, nous allons procéder comme suit :

- Définition d’une `Model` instance dans notre `Game1` classe
- Chargement `Model` de l’instance dans `Game1.LoadContent`
- Dessin `Model` de l’instance dans `Game1.Draw`

Remplacez le `Game1.cs` fichier de code (qui se trouve dans le PCL **WalkingGame** ) par ce qui suit :

```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;

    // This is the model instance that we'll load
    // our XNB into:
    Model model;

    public Game1()
    {
        graphics = new GraphicsDeviceManager(this);
        graphics.IsFullScreen = true;

        Content.RootDirectory = "Content";
    }
    protected override void LoadContent()
    {
        // Notice that loading a model is very similar
        // to loading any other XNB (like a Texture2D).
        // The only difference is the generic type.
        model = Content.Load<Model> ("robot");
    }

    protected override void Update(GameTime gameTime)
    {
        base.Update(gameTime);
    }

    protected override void Draw(GameTime gameTime)
    {
        GraphicsDevice.Clear(Color.CornflowerBlue);

        // A model is composed of "Meshes" which are
        // parts of the model which can be positioned
        // independently, which can use different textures,
        // and which can have different rendering states
        // such as lighting applied.
        foreach (var mesh in model.Meshes)
        {
            // "Effect" refers to a shader. Each mesh may
            // have multiple shaders applied to it for more
            // advanced visuals. 
            foreach (BasicEffect effect in mesh.Effects)
            {
                // We could set up custom lights, but this
                // is the quickest way to get somethign on screen:
                effect.EnableDefaultLighting ();
                // This makes lighting look more realistic on
                // round surfaces, but at a slight performance cost:
                effect.PreferPerPixelLighting = true;

                // The world matrix can be used to position, rotate
                // or resize (scale) the model. Identity means that
                // the model is unrotated, drawn at the origin, and
                // its size is unchanged from the loaded content file.
                effect.World = Matrix.Identity;

                // Move the camera 8 units away from the origin:
                var cameraPosition = new Vector3 (0, 8, 0);
                // Tell the camera to look at the origin:
                var cameraLookAtVector = Vector3.Zero;
                // Tell the camera that positive Z is up
                var cameraUpVector = Vector3.UnitZ;

                effect.View = Matrix.CreateLookAt (
                    cameraPosition, cameraLookAtVector, cameraUpVector);

                // We want the aspect ratio of our display to match
                // the entire screen's aspect ratio:
                float aspectRatio = 
                    graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
                // Field of view measures how wide of a view our camera has.
                // Increasing this value means it has a wider view, making everything
                // on screen smaller. This is conceptually the same as "zooming out".
                // It also 
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                // Anything closer than this will not be drawn (will be clipped)
                float nearClipPlane = 1;
                // Anything further than this will not be drawn (will be clipped)
                float farClipPlane = 200;

                effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

            }

            // Now that we've assigned our properties on the effects we can
            // draw the entire mesh
            mesh.Draw ();
        }
        base.Draw(gameTime);
    }
}
```

Si nous exécutons ce code, le modèle s’affiche à l’écran :

![Modèle affiché à l’écran](part1-images/image8.png "Si ce code est exécuté, le modèle s’affiche à l’écran.")

### <a name="model-class"></a>Classe de modèle

La `Model` classe est la classe principale pour effectuer un rendu 3D à partir de fichiers de contenu (tels que les fichiers. FBX). Elle contient toutes les informations nécessaires pour le rendu, y compris la géométrie 3D, les références de texture et les `BasicEffect` instances qui contrôlent la position, l’éclairage et les valeurs de l’appareil photo.

La `Model` classe elle-même n’a pas directement de variables à positionner, car une seule instance de modèle peut être rendue à plusieurs emplacements, comme nous le verrons plus loin dans ce guide.

Chaque `Model` est composé d’une ou de plusieurs `ModelMesh` instances, qui sont exposées par l’intermédiaire de la `Meshes` propriété. Bien que nous puissions considérer un `Model` comme un seul objet de jeu (comme un robot ou une voiture), chacun `ModelMesh` peut être dessiné avec des `BasicEffect` valeurs différentes. Par exemple, des parties de maillage individuelles peuvent représenter les jambes d’un robot ou les roues d’une voiture, et nous pouvons affecter les `BasicEffect` valeurs pour faire tourner les roues ou les jambes bouger. 

### <a name="basiceffect-class"></a>BasicEffect, classe

La `BasicEffect` classe fournit des propriétés permettant de contrôler les options de rendu. La première modification apportée à l' `BasicEffect` consiste à appeler la `EnableDefaultLighting` méthode. Comme son nom l’indique, cela permet l’éclairage par défaut, ce qui est très pratique pour vérifier qu’un `Model` s’affiche comme prévu. Si nous commentons l' `EnableDefaultLighting` appel, nous voyons que le modèle est rendu avec sa texture uniquement, mais sans ombrage ni lueur spéculaire :

```csharp
//effect.EnableDefaultLighting ();
```

![Le modèle est rendu avec sa texture uniquement, mais sans ombrage ni lueur spéculaire](part1-images/image9.png "Le modèle est rendu avec sa texture uniquement, mais sans ombrage ni lueur spéculaire")

La `World` propriété peut être utilisée pour ajuster la position, la rotation et l’échelle du modèle. Le code ci-dessus utilise la `Matrix.Identity` valeur, ce qui signifie que le `Model` rendra le jeu exactement tel qu’il est spécifié dans le fichier. FBX. Nous allons couvrir les matrices et les coordonnées 3D plus en détail dans la [partie 3](~/graphics-games/monogame/3d/part3.md), mais, à titre d’exemple, nous pouvons modifier la position du `Model` en modifiant la `World` propriété comme suit :

```csharp
// Z is up, so changing Z to 3 moves the object up 3 units:
var modelPosition = new Vector3 (0, 0, 3);
effect.World = Matrix.CreateTranslation (modelPosition); 
```

Ce code permet de déplacer l’objet de 3 unités universelles vers le haut :

![Ce code entraîne le déplacement de l’objet de 3 unités universelles vers le haut](part1-images/image10.png "Ce code entraîne le déplacement de l’objet de 3 unités universelles vers le haut")

Les deux propriétés finales assignées à `BasicEffect` sont `View` et `Projection` . Nous allons aborder les caméras 3D dans la [partie 3](~/graphics-games/monogame/3d/part3.md), mais, à titre d’exemple, nous pouvons modifier la position de l’appareil photo en modifiant la `cameraPosition` variable locale :

```csharp
// The 8 has been changed to a 30 to move the Camera further back
var cameraPosition = new Vector3 (0, 30, 0);
```

Nous pouvons voir que l’appareil photo a été déplacé à nouveau, ce qui a entraîné un `Model` affichage plus petit en raison de la perspective :

![L’appareil photo a été déplacé à nouveau, ce qui a pour effet que le modèle est plus petit en raison de la perspective](part1-images/image11.png "L’appareil photo a été déplacé à nouveau, ce qui a pour effet que le modèle est plus petit en raison de la perspective")

## <a name="rendering-multiple-models"></a>Rendu de plusieurs modèles

Comme indiqué ci-dessus, un seul `Model` peut être dessiné plusieurs fois. Pour faciliter cette opération, nous allons déplacer le `Model` Code de dessin dans sa propre méthode qui prend la `Model` position souhaitée en tant que paramètre. Une fois terminé, `Draw` nos `DrawModel` méthodes et se présentent comme suit :

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);
    DrawModel (new Vector3 (-4, 0, 0));
    DrawModel (new Vector3 ( 0, 0, 0));
    DrawModel (new Vector3 ( 4, 0, 0));
    DrawModel (new Vector3 (-4, 0, 3));
    DrawModel (new Vector3 ( 0, 0, 3));
    DrawModel (new Vector3 ( 4, 0, 3));
    base.Draw(gameTime);
}
void DrawModel(Vector3 modelPosition)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;
            effect.World = Matrix.CreateTranslation (modelPosition);
            var cameraPosition = new Vector3 (0, 10, 0);
            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;
            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);
            float aspectRatio = 
                graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
            float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
            float nearClipPlane = 1;
            float farClipPlane = 200;
            effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
        }
        // Now that we've assigned our properties on the effects we can
        // draw the entire mesh
        mesh.Draw ();
    }
}
```

Le modèle robot est alors dessiné six fois :

![Le modèle robot est alors dessiné six fois](part1-images/image1.png "Le modèle robot est alors dessiné six fois")

## <a name="summary"></a>Récapitulatif

Cette procédure pas à pas a introduit la classe de monojeu `Model` . Elle traite de la conversion d’un fichier. FBX en. xnb, qui peut à son tour être chargé dans une `Model` classe. Il montre également comment les modifications apportées à une `BasicEffect` instance peuvent avoir un impact sur le `Model` dessin.

## <a name="related-links"></a>Liens associés

- [Référence du modèle monojeu](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Graphics_Model)
- [Content.zip](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)
- [Projet terminé (exemple)](/samples/xamarin/mobile-samples/modelrenderingmg/)