---
title: Présentation de UrhoSharp
description: Ce document décrit la structure de base d’une application de UrhoSharp et de des liens vers les différents guides et exemples d’applications qui illustrent l’utilisation de UrhoSharp.
ms.prod: xamarin
ms.assetid: 18041443-5093-4AF7-8B20-03E00478EF35
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 441a3cc19b4246fb2bdea54508142a894af5c051
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832542"
---
# <a name="introduction-to-urhosharp"></a>Présentation de UrhoSharp

![Logo de UrhoSharp](introduction-images/urhosharp-icon.png)

UrhoSharp est un puissant moteur de jeu en 3D pour les développeurs Xamarin et .NET.  Il est même esprit d’Apple SceneKit et SpriteKit et inclure physique, la navigation, la mise en réseau et bien plus tandis que toujours en cours de multiplateforme.

Il existe une liaison de .NET à la [Urho3D](http://urho3d.github.io/) moteur et permet aux développeurs d’écrire du code inter-plateformes qui peut cibler Android, iOS, Windows et Mac avec la même base de code et peuvent rendre aux systèmes OpenGL et Direct3D.

UrhoSharp est un moteur de jeu avec un grand nombre de fonctionnalités prêtes à l’emploi :

- Affichage de graphiques 3D puissants
- Simulation physique (à l’aide de la bibliothèque de puces)
- Gestion de la scène
- Prise en charge d’Async/await
- Actions conviviales API
- Intégration 2D dans les scènes 3D
- Rendu des polices avec FreeType
- Client et serveur, fonctionnalités de réseau
- Importer un large éventail de ressources (avec la bibliothèque d’actifs ouverts)
- Maillage de navigation et pathfinding (à l’aide de la refonte/détour)
- Génération de la forme convexe pour la détection de collision (à l’aide de StanHull)
- Lecture audio (avec **libvorbis**)

## <a name="get-started"></a>Prise en main

UrhoSharp est facilement distribué comme un [package NuGet](https://www.nuget.org/) et il peut être ajouté à votre C# ou F# les projets qui ciblent Windows, Mac, Android ou iOS.  Le package NuGet est fourni avec les bibliothèques nécessaires pour exécuter votre programme, ainsi que les ressources de base (CoreData) utilisés par le moteur.

### <a name="urho-as-a-portable-class-library"></a>Urho comme une bibliothèque de classes Portable

Le package Urho peut être consommé à partir d’un projet spécifique à la plateforme, ou à partir d’un projet de bibliothèque de classes Portable, ce qui vous permet de réutiliser tout votre code sur toutes les plateformes.  Cela signifie que tout vous seriez obligé de le faire sur chaque plateforme consiste à écrire votre point d’entrée spécifique de plate-forme, puis transférer le contrôle à votre code de jeu partagé.

### <a name="samples"></a>Exemples

Vous pouvez obtenir un aperçu des fonctionnalités des Urho en l’ouvrant dans Visual Studio pour Mac ou Visual Studio à partir de l’exemple de solution :

[https://github.com/xamarin/urho-samples](https://github.com/xamarin/urho-samples)

La solution par défaut contient des projets pour Android, iOS, Windows et Mac.  Nous avons structuré cette solution afin que nous avons un lanceur spécifique de plate-forme minuscule, et tous les exemples de code et le code de jeu se trouve dans une bibliothèque de classes portable, illustrant comment optimiser la réutilisation du code sur toutes les plateformes.

Consultez le [Urho et votre plateforme](~/graphics-games/urhosharp/platform/index.md) page pour plus d’informations sur la façon de créer vos propres solutions.

Étant donné que tous les exemples partagent un ensemble commun d’éléments d’interface utilisateur, les exemples ont extrait le paramétrage de base dans ce fichier :

[https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs)

Cela fournit une classe de base d’exemple qui gère certaines combinaisons de touches de base et touch paramétrages des événements, un appareil photo, fournit les éléments d’interface utilisateur de base et cela permet à chaque exemple de vous concentrer sur la fonctionnalité spécifique qui est présentée.

L’exemple suivant montre ce que le moteur est capable de faire :

- [Jeux samply](https://github.com/xamarin/urho-samples/tree/master/SamplyGame) un clone simple de ShootySkies.

Alors que les autres exemples montrent les propriétés individuelles de chaque exemple.

## <a name="basic-structure"></a>Structure de base

Votre jeu doit sous-classe la `Application` (classe), voici où vous permettra de configurer votre jeu (sur le `Setup` méthode) et commencez à créer votre jeu (dans le `Start` (méthode)).  Puis vous construisez votre interface utilisateur principale.  Nous allons pour vous guider dans un petit échantillon qui affiche les API pour configurer une scène 3D, certains éléments d’interface utilisateur et y attachant un comportement simple.

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

Si vous exécutez cette application, vous découvrirez rapidement que le runtime se plaint vos ressources ne sont pas présents.  Ce que vous devez faire est de créer une hiérarchie dans votre projet qui commence par le nom du répertoire spécial « Données », et à l’intérieur de cela, vous placez les ressources que vous référencez dans votre programme.  Vous devez ensuite définir dans les propriétés d’élément pour chaque élément multimédia du « répertoire de sortie » à « Copier si plus récent », cela permettra de garantir que vos données sont présentes.

Nous expliquer ce qui se passe ici.

Pour lancer votre application, vous appelez la fonction de l’initialisation du moteur, puis en créant une nouvelle instance de la classe d’Application, comme suit :

```csharp
new MySample().Run();
```

Le runtime appelle le `Setup` et `Start` méthodes pour vous.  Si vous substituez `Setup` vous pouvez configurer les paramètres du moteur (pas les afficher dans cet exemple).

Vous devez substituer `Start` que cette action lance votre jeu.  Dans cette méthode vous charger vos ressources, connectez les gestionnaires d’événements, le programme d’installation de votre scène et démarrer toutes les actions que vous désirez.  Dans notre exemple, nous les deux créer un peu de l’interface utilisateur à afficher pour l’utilisateur, ainsi que la configuration d’une scène 3D.

Le fragment de code suivant utilise l’infrastructure de l’interface utilisateur pour créer un élément de texte et l’ajouter à votre application :

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

L’infrastructure de l’interface utilisateur est là pour fournir une interface utilisateur dans le jeu très simple, et il fonctionne en ajoutant de nouveaux nœuds à la `UI.Root` nœud.

La deuxième partie de nos exemples de configuration la scène principale.  Cela implique un nombre d’étapes, en créant une scène 3D, création d’une boîte 3D dans l’écran, ajout d’une lumière, un appareil photo et une fenêtre d’affichage.  Elles sont abordées plus en détail dans la section [scène, les nœuds, les composants et les caméras](~/graphics-games/urhosharp/using.md#scenenodescomponentsandcameras).

La troisième partie de notre exemple déclenche deux actions.  Les actions sont des recettes qui décrivent un effet particulier et après sa création qu’ils peuvent être exécutées par un nœud à la demande en appelant le `RunActionAsync` méthode sur un `Node`.

La première action met à l’échelle de la zone avec un effet rebondissant et l’autre fait pivoter la zone indéfiniment :

```csharp
await boxNode.RunActionsAsync(
    new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
```

La méthode ci-dessus montre comment la première action que nous créons un `ScaleTo` action, il s’agit simplement une Recipe (Recette) qui indique que vous souhaitez mettre à l’échelle une seconde vers la valeur une fois la propriété de mise à l’échelle d’un nœud.  Cette action à son tour enveloppée autour d’une action d’accélération, le `EaseBounceOut` action.  Les actions d’accélération déforment l’exécution linéaire d’une action et appliquent un effet, dans ce cas, il fournit l’effet de rebondissement-out.
Par conséquent, notre Recipe (Recette) peut être écrite en tant que :

```csharp
var recipe = new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1));
```

Une fois que la recette a été créée, nous exécutons la recette :

```csharp
await boxNode.RunActionsAsync (recipe)
```

L’expression await indique que le voudrez reprendre l’exécution après cette ligne lorsque l’action est terminée.  Une fois que l’action se termine, nous déclenchons la deuxième animation.

Le [à l’aide de UrhoSharp](~/graphics-games/urhosharp/using.md) document explore plus en détail les concepts qui sous-tendent Urho et la structure de votre code pour créer un jeu.

## <a name="copyrights"></a>Copyrights

Cette documentation contient le contenu d’origine à partir de Xamarin Inc, mais il dessine largement à partir de la documentation open source pour le projet Urho3D et contient des captures d’écran à partir du projet Cocos2D.
