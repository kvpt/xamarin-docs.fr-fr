---
title: Présentation de UrhoSharp
description: Ce document décrit la structure de base d’une application UrhoSharp et fournit des liens vers divers guides et exemples d’applications qui montrent comment utiliser UrhoSharp.
ms.prod: xamarin
ms.assetid: 18041443-5093-4AF7-8B20-03E00478EF35
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 37540053cee03a83582fe19ffb1dcf9e1cf4564c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73011742"
---
# <a name="introduction-to-urhosharp"></a>Présentation de UrhoSharp

![Logo UrhoSharp](introduction-images/urhosharp-icon.png)

UrhoSharp est un puissant moteur de jeu 3D pour les développeurs Xamarin et .NET.  Il est similaire aux SceneKit et SpriteKit d’Apple, et inclut la physique, la navigation, la mise en réseau et bien plus encore tout en étant multiplateformes.

Il s’agit d’une liaison .NET au moteur [Urho3D](https://urho3d.github.io/) et permet aux développeurs d’écrire du code multiplateforme qui peut cibler Android, iOS, Windows et Mac avec le même code base et peut être rendu aux systèmes OpenGL et Direct3D.

UrhoSharp est un moteur de jeu doté de nombreuses fonctionnalités prêtes à l’emploi :

- Rendu graphique 3D puissant
- Simulation physique (à l’aide de la bibliothèque de puces)
- Gestion des scènes
- Prise en charge await/Async
- API actions conviviales
- intégration 2D dans des scènes 3D
- Rendu des polices avec FreeType
- Fonctionnalités de mise en réseau client et serveur
- Importer un large éventail d’éléments multimédias (avec la bibliothèque Open assets)
- Maillage de navigation et pathfinding (à l’aide de refonte/detourage)
- Génération de la coque convexe pour la détection des collisions (à l’aide de StanHull)
- Lecture audio (avec **libvorbis**)

## <a name="get-started"></a>Prise en main

UrhoSharp est facilement distribué en tant que [package NuGet](https://www.nuget.org/) et peut être ajouté à vos C# projets ou F# qui ciblent Windows, Mac, Android ou iOS.  NuGet est fourni avec les deux bibliothèques requises pour exécuter votre programme, ainsi que pour les ressources de base (CoreData) utilisées par le moteur.

### <a name="urho-as-a-portable-class-library"></a>Urho en tant que bibliothèque de classes portable

Le package Urho peut être consommé soit à partir d’un projet spécifique à la plateforme, soit à partir d’un projet de bibliothèque de classes portables, ce qui vous permet de réutiliser tout votre code sur toutes les plateformes.  Cela signifie que tout ce que vous auriez à faire sur chaque plateforme consiste à écrire votre point d’entrée spécifique à la plateforme, puis à transférer le contrôle à votre code de jeu partagé.

### <a name="samples"></a>Exemples

Vous pouvez vous faire un goût des fonctionnalités de Urho en ouvrant Visual Studio pour Mac ou Visual Studio l’exemple de solution à partir de :

[https://github.com/xamarin/urho-samples](https://github.com/xamarin/urho-samples)

La solution par défaut contient des projets pour Android, iOS, Windows et Mac.  Nous avons structuré cette solution de sorte que nous ayons un lanceur spécifique à une plateforme, et que tous les exemples de code et de code de jeu se trouvent dans une bibliothèque de classes portables, illustrant ainsi comment optimiser la réutilisation du code sur toutes les plateformes.

Pour plus d’informations sur la création de vos propres solutions, consultez la page [Urho et votre](~/graphics-games/urhosharp/platform/index.md) page de plateforme.

Étant donné que tous les exemples partagent un ensemble commun d’éléments d’interface utilisateur, les exemples ont fait abstraction de la configuration de base dans ce fichier :

[https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs)

Cela fournit un exemple de classe de base qui gère des séquences de touches de base et des événements tactiles, configure un appareil photo, fournit des éléments d’interface utilisateur de base, et cela permet à chaque exemple de se concentrer sur les fonctionnalités spécifiques qui sont présentées.

L’exemple suivant montre ce que le moteur peut faire :

- [Jouez](https://github.com/xamarin/urho-samples/tree/master/SamplyGame) à un simple clone de ShootySkies.

Tandis que les autres exemples affichent les propriétés individuelles de chaque exemple.

## <a name="basic-structure"></a>Structure de base

Votre jeu doit sous-définir la classe `Application`, c’est là que vous allez configurer votre jeu (sur la méthode `Setup`) et démarrer votre jeu (dans la méthode `Start`).  Vous construisez ensuite votre interface utilisateur principale.  Nous allons examiner un petit exemple qui illustre les API permettant de configurer une scène 3D, certains éléments d’interface utilisateur et de lui associer un comportement simple.

```csharp
class MySample : Application {
    protected override void Start ()
    {
        CreateScene ();
        Input.KeyDown += (args) => {
            if (args.Key == Key.Esc) Exit ();
        };
    }

    async void CreateScene()
    {
        // UI text
        var helloText = new Text()
        {
            Value = "Hello World from MySample",
            HorizontalAlignment = HorizontalAlignment.Center,
            VerticalAlignment = VerticalAlignment.Center
        };
        helloText.SetColor(new Color(0f, 1f, 1f));
        helloText.SetFont(
            font: ResourceCache.GetFont("Fonts/Font.ttf"),
            size: 30);
        UI.Root.AddChild(helloText);

        // Create a top-level scene, must add the Octree
        // to visualize any 3D content.
        var scene = new Scene();
        scene.CreateComponent<Octree>();
        // Box
        Node boxNode = scene.CreateChild();
        boxNode.Position = new Vector3(0, 0, 5);
        boxNode.Rotation = new Quaternion(60, 0, 30);
        boxNode.SetScale(0f);
        StaticModel modelObject = boxNode.CreateComponent<StaticModel>();
        modelObject.Model = ResourceCache.GetModel("Models/Box.mdl");
        // Light
        Node lightNode = scene.CreateChild(name: "light");
        lightNode.SetDirection(new Vector3(0.6f, -1.0f, 0.8f));
        lightNode.CreateComponent<Light>();
        // Camera
        Node cameraNode = scene.CreateChild(name: "camera");
        Camera camera = cameraNode.CreateComponent<Camera>();
        // Viewport
        Renderer.SetViewport(0, new Viewport(scene, camera, null));
        // Perform some actions
        await boxNode.RunActionsAsync(
            new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
        await boxNode.RunActionsAsync(
            new RepeatForever(new RotateBy(duration: 1,
                deltaAngleX: 90, deltaAngleY: 0, deltaAngleZ: 0)));
     }
}
```

Si vous exécutez cette application, vous découvrirez rapidement que le runtime se plaint de vos ressources.  Ce que vous devez faire, c’est créer dans votre projet une hiérarchie qui commence par le nom de répertoire spécial « Data », et à l’intérieur de celle-ci, placer les ressources que vous référencez dans votre programme.  Vous devez ensuite définir dans les propriétés de l’élément de chaque ressource le « copier dans le répertoire de sortie » sur « copier si plus récent », afin de garantir que vos données y sont.

Nous allons expliquer ce qui se passe ici.

Pour lancer votre application, vous appelez la fonction d’initialisation du moteur, suivie de la création d’une nouvelle instance de votre classe d’application, comme suit :

```csharp
new MySample().Run();
```

Le runtime appellera les méthodes `Setup` et `Start`.  Si vous remplacez `Setup` vous pouvez configurer les paramètres du moteur (non affiché dans cet exemple).

Vous devez remplacer `Start`, car cette opération lance votre jeu.  Dans cette méthode, vous chargez vos ressources, connectez les gestionnaires d’événements, configurez votre scène et démarrez les actions que vous souhaitez.  Dans notre exemple, nous créons tous deux un peu d’interface utilisateur à afficher à l’utilisateur, ainsi que la configuration d’une scène 3D.

Le morceau de code suivant utilise l’infrastructure de l’interface utilisateur pour créer un élément de texte et l’ajouter à votre application :

```csharp
// UI text
var helloText = new Text()
{
    Value = "Hello World from UrhoSharp",
    HorizontalAlignment = HorizontalAlignment.Center,
    VerticalAlignment = VerticalAlignment.Center
};
helloText.SetColor(new Color(0f, 1f, 1f));
helloText.SetFont(
    font: ResourceCache.GetFont("Fonts/Font.ttf"),
    size: 30);
UI.Root.AddChild(helloText);
```

L’infrastructure d’interface utilisateur est là pour fournir une interface utilisateur de jeu très simple et fonctionne en ajoutant de nouveaux nœuds au nœud `UI.Root`.

La deuxième partie de notre exemple installe la scène principale.  Cela implique un certain nombre d’étapes, la création d’une scène 3D, la création d’une zone 3D dans l’écran, l’ajout d’une lumière, d’une caméra et d’une fenêtre d’affichage.  Celles-ci sont explorées plus en détail dans la section [scène, nœuds, composants et appareils photo](~/graphics-games/urhosharp/using.md#scenenodescomponentsandcameras).

La troisième partie de notre exemple déclenche quelques actions.  Les actions sont des recettes qui décrivent un effet particulier. une fois créées, elles peuvent être exécutées par un nœud à la demande en appelant la méthode `RunActionAsync` sur une `Node`.

La première action met à l’échelle la zone avec un effet de rebond et la seconde fait pivoter la boîte de façon infinie :

```csharp
await boxNode.RunActionsAsync(
    new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
```

L’illustration ci-dessus montre comment la première action que nous créons est une action `ScaleTo`, il s’agit simplement d’une recette qui indique que vous souhaitez mettre à l’échelle d’une seconde vers la valeur d’une propriété Scale d’un nœud.  Cette action est entourée d’une action d’accélération, le `EaseBounceOut` action.  Les actions d’accélération déforment l’exécution linéaire d’une action et appliquent un effet. dans ce cas, elle fournit l’effet de rebond.
Notre recette peut donc être écrite de la façon suivante :

```csharp
var recipe = new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1));
```

Une fois la recette créée, nous exécutons la recette :

```csharp
await boxNode.RunActionsAsync (recipe)
```

L’expression await indique que souhaite reprendre l’exécution après cette ligne lorsque l’action se termine.  Une fois l’action terminée, nous déclenchons la deuxième animation.

Le document [using UrhoSharp](~/graphics-games/urhosharp/using.md) explore plus en détail les concepts qui sous-tendent Urho et comment structurer votre code pour créer un jeu.

## <a name="copyrights"></a>Droits d’auteur

Cette documentation contient le contenu d’origine de Xamarin Inc, mais s’appuie largement sur la documentation Open source pour le projet Urho3D et contient des captures d’écran du projet Cocos2D.
