---
title: Utilisation de CocosSharp dans Xamarin.Forms
description: CocosSharp peut être utilisé pour ajouter forme précis, l’image et le rendu de texte à une application pour la visualisation avancée
ms.prod: xamarin
ms.assetid: E0F404D5-5C6B-4288-92EC-78996C674E4E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2016
ms.openlocfilehash: 7b465391958a6e862bfed9fde8d9da1fdd52bee5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759756"
---
# <a name="using-cocossharp-in-xamarinforms"></a>Utilisation de CocosSharp dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](h https://github.com/xamarin/xamarin-forms-samples/tree/master/CocosSharpForms)

_CocosSharp peut être utilisé pour ajouter forme précis, l’image et le rendu de texte à une application pour la visualisation avancée_

> [!VIDEO https://youtube.com/embed/eYCx63FeqVU]

**Evolve 2016 : Cocos # dans Xamarin. Forms**

## <a name="overview"></a>Présentation

CocosSharp est une technologie flexible et puissante pour l’affichage de graphiques, de lecture entrée tactile, de lecture de contenu audio et la gestion. Ce guide explique comment ajouter CocosSharp à une application Xamarin.Forms. Il couvre les sujets suivants :

- [Nouveautés CocosSharp](#what)
- [Ajouter les packages Nuget de CocosSharp](#nuget)
- [Procédure pas à pas : Ajout de CocosSharp à une application Xamarin. Forms](#add)

<a name="what" />

## <a name="what-is-cocossharp"></a>Nouveautés CocosSharp

[CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md) est un moteur de jeu open source qui est disponible sur la plateforme Xamarin.
CocosSharp est une bibliothèque runtime-efficace qui inclut les fonctionnalités suivantes :

- Rendu d’image à `CCSprite` l’aide de la classe
- Rendu de forme à `CCDrawNode` l’aide de la classe
- Logique chaque frame à l’aide `CCNode.Schedule` de la classe
- Gestion de contenu (chargement et déchargement de ressources telles que des fichiers. png) à l’aide de`CCTextureCache`
- Animations utilisant la `CCAction` classe

Domaines de spécialisation de CocosSharp consiste à simplifier la création de jeux 2D multiplateformes ; Toutefois, il peut également être un excellent complément pour les applications Xamarin Form. Dans la mesure où les jeux nécessitent généralement un rendu efficace et un contrôle précis sur les éléments visuels, CocosSharp peut être utilisé pour ajouter une visualisation puissante et effets à des applications hors jeu.

Xamarin.Forms est basé sur les systèmes de l’interface utilisateur natives, spécifiques à la plateforme. Par exemple, [ `Button`s](xref:Xamarin.Forms.Button) s’affichent différemment sur iOS et Android et peut être différente même version de système d’exploitation. En revanche, CocosSharp n’utilise pas les objets visuels spécifiques à la plateforme, par conséquent, tous les objets visuels apparaissent identiques sur toutes les plateformes. Bien sûr, résolution et proportions diffèrent entre les appareils, et cela peut affecter la façon dont CocosSharp rend ses visuels. Ces détails seront abordées plus loin dans ce guide.

Vous trouverez des informations plus détaillées dans le [CocosSharp section](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md).

<a name="nuget" />

## <a name="adding-the-cocossharp-nuget-packages"></a>Ajouter les packages Nuget de CocosSharp

Avant d’utiliser CocosSharp, les développeurs doivent effectuer quelques ajouts à leur projet Xamarin.Forms.
Ce guide suppose un projet Xamarin.Forms avec un iOS, Android et .NET Standard projet de bibliothèque.
Tout le code sera écrit dans le projet de bibliothèque .NET Standard. Toutefois, les bibliothèques doivent être ajoutés pour les projets iOS et Android.

Le package Nuget de CocosSharp contient tous les objets nécessaires pour créer des objets de CocosSharp.
Le package nuget CocosSharp.Forms inclut la `CocosSharpView` classe, qui est utilisé pour héberger CocosSharp dans Xamarin.Forms.
Ajouter le **CocosSharp.Forms** NuGet et **CocosSharp** sera automatiquement ajouté également.
Pour ce faire, cliquez sur le **Packages** dossier dans le projet de bibliothèque .NET Standard, puis sélectionnez **ajouter des Packages...** . Entrez le terme de recherche **CocosSharp.Forms**, sélectionnez **CocosSharp pour Xamarin.Forms**, puis cliquez sur **ajouter un Package**.

![](cocossharp-images/image1.png "Ajouter la boîte de dialogue Packages")

Les deux **CocosSharp** et **CocosSharp.Forms** les packages NuGet seront ajoutés au projet :

![](cocossharp-images/image2.png "Dossier packages")

Répétez les étapes ci-dessus pour les projets spécifiques à la plateforme (par exemple, iOS et Android).

<a name="add" />

## <a name="walkthrough-adding-cocossharp-to-a-xamarinforms-app"></a>Procédure pas à pas : Ajout de CocosSharp à une application Xamarin. Forms

Suivez ces étapes pour ajouter une vue de CocosSharp simple à une application Xamarin.Forms :

1. [Création d’un Xamarin Forms Page](#1)
1. [Ajout d’un CocosSharpView](#2)
1. [Création de la GameScene](#3)
1. [Ajout d’un cercle](#4)
1. [Interaction avec CocosSharp](#5)

Une fois que vous venez d’ajouter une vue de CocosSharp à une application Xamarin.Forms, visitez le [CocosSharp documentation](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md) pour en savoir plus sur la création de contenu avec CocosSharp.

<a name="1" />

### <a name="1-creating-a-xamarin-forms-page"></a>1. Création d’un Xamarin Forms Page

CocosSharp peut être hébergé dans n’importe quel conteneur de Xamarin.Forms. Cet exemple de cette page utilise une page appelée `HomePage`. `HomePage` est divisé en deux par un `Grid` pour montrer comment Xamarin.Forms et CocosSharp peuvent être rendus simultanément sur la même page.

Tout d’abord, configurer la Page afin qu’il contient un `Grid` et deux `Button` instances :

```csharp
public class HomePage : ContentPage
{
public HomePage ()
    {
        // This is the top-level grid, which will split our page in half
        var grid = new Grid ();
        this.Content = grid;
        grid.RowDefinitions = new RowDefinitionCollection {
            // Each half will be the same size:
            new RowDefinition{ Height = new GridLength(1, GridUnitType.Star)},
            new RowDefinition{ Height = new GridLength(1, GridUnitType.Star)},
        };
        CreateTopHalf (grid);
        CreateBottomHalf (grid);
    }
    void CreateTopHalf(Grid grid)
    {
        // We'll be adding our CocosSharpView here:
    }
    void CreateBottomHalf(Grid grid)
    {
        // We'll use a StackLayout to organize our buttons
        var stackLayout = new StackLayout();
        // The first button will move the circle to the left when it is clicked:
        var moveLeftButton = new Button {
            Text = "Move Circle Left"
        };
        stackLayout.Children.Add (moveLeftButton);

        // The second button will move the circle to the right when clicked:
        var moveCircleRight = new Button {
            Text = "Move Circle Right"
        };
        stackLayout.Children.Add (moveCircleRight);
        // The stack layout will be in the bottom half (row 1):

        grid.Children.Add (stackLayout, 0, 1);
    }
}
```

Sur iOS, le `HomePage` s’affiche comme illustré dans l’image suivante :

![](cocossharp-images/image3.png "Capture d’écran de la page d’accueil")

<a name="2" />

### <a name="2-adding-a-cocossharpview"></a>2. Ajout d’un CocosSharpView

Le `CocosSharpView` classe est utilisée pour incorporer CocosSharp dans une application Xamarin.Forms. Dans la mesure où `CocosSharpView` hérite le [Xamarin.Forms.View](xref:Xamarin.Forms.View) (classe), il fournit une interface familière pour la disposition, et il peut être utilisé au sein de conteneurs de disposition tel que [Xamarin.Forms.Grid](xref:Xamarin.Forms.Grid). Ajouter un nouveau `CocosSharpView` au projet en effectuant le `CreateTopHalf` méthode :

```csharp
void CreateTopHalf(Grid grid)
{
    // This hosts our game view.
    var gameView = new CocosSharpView () {
        // Notice it has the same properties as other XamarinForms Views
        HorizontalOptions = LayoutOptions.FillAndExpand,
        VerticalOptions = LayoutOptions.FillAndExpand,
        // This gets called after CocosSharp starts up:
        ViewCreated = HandleViewCreated
    };
    // We'll add it to the top half (row 0)
    grid.Children.Add (gameView, 0, 0);
}
```

L’initialisation de CocosSharp n’est pas immédiate, par conséquent, enregistre un événement quand la `CocosSharpView` a terminé sa création. Effectuer cette opération dans le `HandleViewCreated` méthode :

```csharp
void HandleViewCreated (object sender, EventArgs e)
{
    var gameView = sender as CCGameView;
    if (gameView != null)
    {
        // This sets the game "world" resolution to 100x100:
        gameView.DesignResolution = new CCSizeI (100, 100);
        // GameScene is the root of the CocosSharp rendering hierarchy:
        gameScene = new GameScene (gameView);
        // Starts CocosSharp:
        gameView.RunWithScene (gameScene);
    }
}
```

Le `HandleViewCreated` méthode a deux détails importants que nous examinerons. La première est la `GameScene` classe, qui sera créée dans la section suivante. Il est important de noter que l’application ne se compilera pas jusqu'à ce que le `GameScene` est créé et le `gameScene` instance référence est résolue.

Le deuxième détail important est le `DesignResolution` propriété, qui définit la zone visible du jeu pour les objets de CocosSharp. Le `DesignResolution` propriété est étudiée après avoir créé `GameScene`.

<a name="3" />

### <a name="3-creating-the-gamescene"></a>3. Création de la GameScene

Le `GameScene` classe hérite de CocosSharp `CCScene`. `GameScene` est le premier point où nous préoccuper purement CocosSharp. Code contenu dans `GameScene` fonctionnera dans n’importe quelle application CocosSharp, si elle est hébergée dans un projet Xamarin.Forms ou non.

Le `CCScene` classe est la visual racine de l’ensemble du rendu CocosSharp. N’importe quel objet CocosSharp visible doit être contenue dans un `CCScene`. Plus précisément, les objets visuels doivent être ajoutés à `CCLayer` instances et ceux `CCLayer` instances doivent être ajoutés à un `CCScene`.

Le graphique suivant peut aider à visualiser une hiérarchie de CocosSharp classique :

![](cocossharp-images/image4.png "Hiérarchie typique de CocosSharp")

Seul `CCScene` peut être active à la fois. La plupart des jeux utilisent plusieurs `CCLayer` instances pour trier le contenu, mais notre application utilise un seul. De même, la plupart des jeux utilisent plusieurs objets visuels, mais nous aurons uniquement un dans notre application. Plus détaillée la discussion sur la CocosSharp hiérarchie visuelle se trouve dans le [BouncingGame procédure pas à pas](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/bouncing-game.md).

Initialement le `GameScene` classe sera presque vide : nous allons créer simplement pour répondre à la référence dans `HomePage`. Ajoutez une nouvelle classe à votre projet de bibliothèque .NET Standard nommé `GameScene`. Elle doit hériter de la `CCScene` classe comme suit :

```csharp
public class GameScene : CCScene
{
    public GameScene (CCGameView gameView) : base(gameView)
    {

    }
}
```

Maintenant que `GameScene` est défini, nous pouvons retourner à `HomePage` et ajouter un champ :

```csharp
// Keep the GameScene at class scope
// so the button click events can access it:
GameScene gameScene;
```

Nous pouvons maintenant compiler notre projet et exécutez-le pour voir CocosSharp en cours d’exécution. Nous n’avons pas ajouté quoi que ce soit à notre `GameScene,` donc la moitié supérieure de notre page est noir – la couleur par défaut d’une scène de CocosSharp :

![](cocossharp-images/image5.png "GameScene vide")

<a name="4" />

### <a name="4-adding-a-circle"></a>4. Ajout d’un cercle

L’application possède actuellement une instance en cours d’exécution du moteur de CocosSharp, affichage vide `CCScene`. Ensuite, nous allons ajouter un objet visuel : un cercle. Le `CCDrawNode` classe peut être utilisée pour dessiner diverses formes géométriques, comme indiqué dans le [dessin une géométrie avec CCDrawNode guide](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md).

Ajouter un cercle à notre `GameScene` classe et l’instancier dans le constructeur, comme indiqué dans le code suivant :

```csharp
public class GameScene : CCScene
{
    CCDrawNode circle;
    public GameScene (CCGameView gameView) : base(gameView)
    {
        var layer = new CCLayer ();
        this.AddLayer (layer);
        circle = new CCDrawNode ();
        layer.AddChild (circle);
        circle.DrawCircle (
            // The center to use when drawing the circle,
            // relative to the CCDrawNode:
            new CCPoint (0, 0),
            radius:15,
            color:CCColor4B.White);
        circle.PositionX = 20;
        circle.PositionY = 50;
    }
}
```

Exécution de l’application maintenant affiche un cercle sur le côté gauche de la zone d’affichage de CocosSharp :

![](cocossharp-images/image6.png "Cercle dans GameScene")

#### <a name="understanding-designresolution"></a>Présentation DesignResolution

Maintenant qu’un objet de CocosSharp visuel s’affiche, nous pouvons examiner la `DesignResolution` propriété.

La `DesignResolution` représente la largeur et la hauteur de la zone de CocosSharp de placement et redimensionnement des objets. La résolution réelle de la zone est mesurée en *pixels* tandis que le `DesignResolution` est mesuré dans le monde *unités*. Le diagramme suivant montre la résolution de plusieurs parties de la vue tel qu’affiché sur un iPhone 5 avec une résolution d’écran de 640 x 1136 pixels :

![](cocossharp-images/image7.png "iPhone 5 s résolution de la conception")

Le diagramme ci-dessus affiche les dimensions en pixels à l’extérieur de l’écran en texte noir. Unités sont affichées à l’intérieur du diagramme dans le texte blanc. Voici quelques détails importants affichés au-dessus :

- L’origine de l’affichage de CocosSharp est en bas à gauche. Déplacer vers la droite augmente la valeur de X et remontons augmente la valeur Y. Notez que la valeur Y est inversée par rapport à certains autres moteurs de disposition 2D, où (0,0) est l’angle supérieur gauche de la zone de dessin.
- Le comportement par défaut de CocosSharp consiste à conserver les proportions de son affichage. Étant donné que la première ligne dans la grille est plus large que haute, CocosSharp ne remplit pas toute la largeur de la cellule, comme indiqué par le rectangle blanc en pointillés. Ce comportement peut être modifié, comme décrit dans la [guide de gestion de résolutions multiples dans CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/resolutions.md).
- Dans cet exemple, CocosSharp gère une zone d’affichage de 100 unités et larges indépendamment de la taille ou les proportions de son appareil. Cela signifie que le code peut supposer X = 100 représente lié le plus à droite de la CocosSharp s’afficher, conservation disposition cohérente sur tous les appareils.

#### <a name="ccdrawnode-details"></a>Détails de CCDrawNode

Notre application simple utilise le `CCDrawNode` classe pour dessiner un cercle. Cette classe peut être très utile pour les applications métier car elle fournit le rendu vectoriel de géométrie – une fonctionnalité manquante dans Xamarin.Forms. En plus des cercles, la `CCDrawNode` classe peut être utilisée pour dessiner des rectangles, des splines, lignes et polygones personnalisés. `CCDrawNode` est également facile à utiliser dans la mesure où il ne nécessite pas l’utilisation de fichiers image (.png). Vous trouverez une présentation plus détaillée des CCDrawNode dans le [dessin une géométrie avec CCDrawNode guide](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md).

<a name="5" />

### <a name="5-interacting-with-cocossharp"></a>5. Interaction avec CocosSharp

Éléments visuels de CocosSharp (tel que `CCDrawNode`) héritent la `CCNode` classe. `CCNode` fournit deux propriétés qui peuvent être utilisées pour positionner un objet par rapport à son parent : `PositionX` et `PositionY`. Notre code utilise actuellement ces deux propriétés pour positionner le centre du cercle, comme indiqué dans cet extrait de code :

```csharp
circle.PositionX = 20;
circle.PositionY = 50;
```

Il est important de noter que les objets de CocosSharp sont positionnés par les valeurs de position explicites, par opposition à la plupart des vues Xamarin.Forms, qui sont placées automatiquement en fonction du comportement de leurs contrôles de disposition du parent.

Nous allons ajouter le code pour autoriser l’utilisateur à cliquer sur un des deux boutons pour déplacer le cercle à gauche ou à droite de 10 unités (pas pixels, étant donné que le cercle dessine dans l’espace d’unité CocosSharp world). Tout d’abord, nous allons créer deux méthodes publiques dans le `GameScene` classe :

```csharp
public void MoveCircleLeft()
{
    circle.PositionX -= 10;
}

public void MoveCircleRight()
{
    circle.PositionX += 10;
}
```

Ensuite, nous allons ajouter des gestionnaires pour les deux boutons de `HomePage` pour répondre aux clics. Lorsque vous avez terminé, notre `CreateBottomHalf` méthode contient le code suivant :

```csharp
void CreateBottomHalf(Grid grid)
{
    // We'll use a StackLayout to organize our buttons
    var stackLayout = new StackLayout();

    // The first button will move the circle to the left when it is clicked:
    var moveLeftButton = new Button {
        Text = "Move Circle Left"
    };
    moveLeftButton.Clicked += (sender, e) => gameScene.MoveCircleLeft ();
    stackLayout.Children.Add (moveLeftButton);

    // The second button will move the circle to the right when clicked:
    var moveCircleRight = new Button {
        Text = "Move Circle Right"
    };
    moveCircleRight.Clicked += (sender, e) => gameScene.MoveCircleRight ();
    stackLayout.Children.Add (moveCircleRight);

    // The stack layout will be in the bottom half (row 1):
    grid.Children.Add (stackLayout, 0, 1);
}
```

Le cercle CocosSharp déplace maintenant en réponse aux clics. Nous pouvons également clairement voir les limites de la zone de dessin CocosSharp en déplaçant le cercle suffisamment loin vers la gauche ou droite :

![](cocossharp-images/image8.png "GameScene avec déplacement de cercle")

## <a name="summary"></a>Récapitulatif

Ce guide montre comment ajouter CocosSharp à un Xamarin.Forms existant du projet, la création d’interaction entre Xamarin.Forms et CocosSharp et aborde différentes Considérations lors de la création de dispositions dans CocosSharp.

Le moteur de jeu de CocosSharp offre un grand nombre de fonctionnalités et de profondeur, donc ce guide ne fait que gratter la surface de CocosSharp réalisables. Les développeurs qui souhaitent en savoir plus sur CocosSharp peuvent trouver de nombreux articles dans l' [Archive CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/).

## <a name="related-links"></a>Liens associés

- [CocosSharpForms (exemple)](https://github.com/xamarin/xamarin-forms-samples/tree/master/CocosSharpForms)
