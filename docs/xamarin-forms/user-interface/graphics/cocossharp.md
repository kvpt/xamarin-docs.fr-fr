---
title: Utilisation de CocosSharp dansXamarin.Forms
description: CocosSharp peut être utilisé pour ajouter un rendu de forme, d’image et de texte précis à une application pour une visualisation avancée
ms.prod: xamarin
ms.assetid: E0F404D5-5C6B-4288-92EC-78996C674E4E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 31b82586b47ead1a851000d59c8271deec063020
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86933781"
---
# <a name="using-cocossharp-in-xamarinforms"></a>Utilisation de CocosSharp dansXamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/CocosSharpForms)

_CocosSharp peut être utilisé pour ajouter un rendu de forme, d’image et de texte précis à une application pour une visualisation avancée_

> [!VIDEO https://youtube.com/embed/eYCx63FeqVU]

**Évolution de 2016 : Cocos # inXamarin.Forms**

## <a name="overview"></a>Vue d’ensemble

CocosSharp est une technologie flexible et puissante permettant d’afficher des graphiques, de lire des entrées tactiles, de lire des données audio et de gérer du contenu. Ce guide explique comment ajouter des CocosSharp à une Xamarin.Forms application.

## <a name="what-is-cocossharp"></a>Qu’est-ce que CocosSharp ?

[CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md) est un moteur de jeu Open source disponible sur la plateforme Xamarin.
CocosSharp est une bibliothèque efficace qui comprend les fonctionnalités suivantes :

- Rendu d’image à l’aide de la `CCSprite` classe
- Rendu de forme à l’aide de la `CCDrawNode` classe
- Logique chaque frame à l’aide de la `CCNode.Schedule` classe
- Gestion de contenu (chargement et déchargement de ressources telles que des fichiers. png) à l’aide de`CCTextureCache`
- Animations utilisant la `CCAction` classe

Le principal objectif de CocosSharp est de simplifier la création de jeux 2D multiplateforme. Toutefois, il peut également s’agir d’un excellent ajout aux applications de formulaire Xamarin. Étant donné que les jeux nécessitent généralement un rendu efficace et un contrôle précis sur les éléments visuels, CocosSharp peut être utilisé pour ajouter une visualisation et des effets puissants aux applications non-jeux.

Xamarin.Formsrepose sur des systèmes d’interface utilisateur natifs spécifiques à la plateforme. Par exemple, les [ `Button` s](xref:Xamarin.Forms.Button) s’affichent différemment sur iOS et Android, et peuvent même différer par la version du système d’exploitation. En revanche, CocosSharp n’utilise pas d’objets visuels spécifiques à la plateforme, donc tous les objets visuels apparaissent identiques sur toutes les plateformes. Bien entendu, la résolution et le rapport hauteur/largeur diffèrent entre les appareils, ce qui peut avoir un impact sur la façon dont CocosSharp affiche ses visuels. Ces informations seront abordées plus loin dans ce guide.

Vous trouverez des informations plus détaillées dans la [section CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md).

## <a name="adding-the-cocossharp-nuget-packages"></a>Ajout des packages NuGet CocosSharp

Avant d’utiliser CocosSharp, les développeurs doivent effectuer quelques ajouts à leur Xamarin.Forms projet.
Ce guide part du principe qu’il s’agit d’un Xamarin.Forms projet avec un projet de bibliothèque iOS, Android et .NET standard.
Tout le code est écrit dans le projet de bibliothèque .NET Standard ; Toutefois, les bibliothèques doivent être ajoutées aux projets iOS et Android.

Le package NuGet CocosSharp contient tous les objets nécessaires pour créer des objets CocosSharp.
Le package NuGet CocosSharp. Forms comprend la `CocosSharpView` classe, qui est utilisée pour héberger des CocosSharp dans Xamarin.Forms .
Ajoutez le **CocosSharp. Forms** NuGet et **CocosSharp** sera également ajouté automatiquement.
Pour ce faire, cliquez avec le bouton droit sur le dossier **packages** dans le projet de bibliothèque .NET standard, puis sélectionnez **Ajouter des packages...**. Entrez le terme de recherche **CocosSharp. Forms**, sélectionnez **CocosSharp pour Xamarin.Forms **, puis cliquez sur Ajouter un **package**.

![Boîte de dialogue Ajouter des packages](cocossharp-images/image1.png)

Les packages NuGet **CocosSharp** et **CocosSharp. Forms** seront ajoutés au projet :

![Dossier Packages](cocossharp-images/image2.png)

Répétez les étapes ci-dessus pour les projets spécifiques à une plateforme (par exemple, iOS et Android).

## <a name="walkthrough-adding-cocossharp-to-a-xamarinforms-app"></a>Procédure pas à pas : ajout de CocosSharp à une Xamarin.Forms application

Pour ajouter une vue CocosSharp simple à une application, procédez comme suit Xamarin.Forms :

1. [Création d’une page de formulaires Xamarin](#1-creating-a-xamarin-forms-page)
1. [Ajout d’un CocosSharpView](#2-adding-a-cocossharpview)
1. [Création du GameScene](#3-creating-the-gamescene)
1. [Ajout d’un cercle](#4-adding-a-circle)
1. [Interaction avec CocosSharp](#5-interacting-with-cocossharp)

Une fois que vous avez ajouté avec succès une vue CocosSharp à une Xamarin.Forms application, consultez la [documentation CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md) pour en savoir plus sur la création de contenu avec CocosSharp.

### <a name="1-creating-a-xamarin-forms-page"></a>1. création d’une page de formulaires Xamarin

CocosSharp peut être hébergé dans n’importe quel Xamarin.Forms conteneur. Cet exemple pour cette page utilise une page appelée `HomePage` . `HomePage`est divisé en deux par un `Grid` pour montrer comment Xamarin.Forms et CocosSharp peuvent être restitués simultanément sur la même page.

Tout d’abord, configurez la page pour qu’elle contienne un `Grid` et deux `Button` instances :

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

![Capture d’écran page d’accueil](cocossharp-images/image3.png)

### <a name="2-adding-a-cocossharpview"></a>2. Ajout d’un CocosSharpView

La `CocosSharpView` classe est utilisée pour incorporer CocosSharp dans une Xamarin.Forms application. Comme `CocosSharpView` hérite de [ Xamarin.Forms . ](xref:Xamarin.Forms.View)Classe de vue, elle fournit une interface familière pour la disposition, et elle peut être utilisée dans des conteneurs de disposition tels que [ Xamarin.Forms . Grille](xref:Xamarin.Forms.Grid). Ajoutez un nouveau `CocosSharpView` au projet en terminant la `CreateTopHalf` méthode :

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

L’initialisation de CocosSharp n’étant pas immédiate, inscrivez un événement pour lorsque le `CocosSharpView` a terminé sa création. Procédez `HandleViewCreated` comme suit dans la méthode :

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

La `HandleViewCreated` méthode comporte deux détails importants que nous allons examiner. La première est la `GameScene` classe, qui sera créée dans la section suivante. Il est important de noter que l’application n’est pas compilée tant que le `GameScene` n’est pas créé et que la référence de l' `gameScene` instance n’est pas résolue.

Le deuxième détail important est la `DesignResolution` propriété, qui définit la zone visible du jeu pour les objets CocosSharp. La `DesignResolution` propriété sera examinée après la création de `GameScene` .

### <a name="3-creating-the-gamescene"></a>3. création du GameScene

La `GameScene` classe hérite de CocosSharp `CCScene` . `GameScene`est le premier point où nous faisons simplement affaire avec CocosSharp. Le code contenu dans fonctionnera `GameScene` dans toute application CocosSharp, qu’il soit hébergé dans un Xamarin.Forms projet ou non.

La `CCScene` classe est la racine visuelle de tous les rendus CocosSharp. Tout objet CocosSharp visible doit être contenu dans un `CCScene` . Plus précisément, les objets visuels doivent être ajoutés aux `CCLayer` instances, et ces `CCLayer` instances doivent être ajoutées à un `CCScene` .

Le graphique suivant peut vous aider à visualiser une hiérarchie CocosSharp type :

![Hiérarchie CocosSharp classique](cocossharp-images/image4.png)

Une seule `CCScene` peut être active à la fois. La plupart des jeux utilisent plusieurs `CCLayer` instances pour trier le contenu, mais notre application n’en utilise qu’une seule. De même, la plupart des jeux utilisent plusieurs objets visuels, mais nous n’en aurons qu’un dans notre application. Vous trouverez une discussion plus détaillée sur la hiérarchie visuelle CocosSharp dans la [procédure pas à pas BouncingGame](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/bouncing-game.md).

Initialement, la `GameScene` classe sera presque vide. nous allons la créer pour répondre à la référence dans `HomePage` . Ajoutez une nouvelle classe à votre projet de bibliothèque .NET Standard nommé `GameScene` . Il doit hériter de la `CCScene` classe comme suit :

```csharp
public class GameScene : CCScene
{
    public GameScene (CCGameView gameView) : base(gameView)
    {

    }
}
```

Maintenant que `GameScene` est défini, nous pouvons revenir à `HomePage` et ajouter un champ :

```csharp
// Keep the GameScene at class scope
// so the button click events can access it:
GameScene gameScene;
```

Nous pouvons maintenant compiler notre projet et l’exécuter pour voir CocosSharp en cours d’exécution. Nous n’avons rien ajouté à notre `GameScene,` , si bien que la moitié supérieure de notre page est noire, la couleur par défaut d’une scène CocosSharp :

![GameScene vide](cocossharp-images/image5.png)

### <a name="4-adding-a-circle"></a>4. Ajout d’un cercle

L’application a actuellement une instance en cours d’exécution du moteur CocosSharp, ce qui affiche un vide `CCScene` . Nous allons ensuite ajouter un objet visuel : un cercle. La `CCDrawNode` classe peut être utilisée pour dessiner une variété de formes géométriques, comme indiqué dans la [géométrie de dessin avec le guide CCDrawNode](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md).

Ajoutez un cercle à notre `GameScene` classe et instanciez-le dans le constructeur comme indiqué dans le code suivant :

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

L’exécution de l’application affiche maintenant un cercle sur le côté gauche de la zone d’affichage CocosSharp :

![Cercle dans GameScene](cocossharp-images/image6.png)

#### <a name="understanding-designresolution"></a>Fonctionnement de DesignResolution

Maintenant qu’un objet Visual CocosSharp est affiché, nous pouvons examiner la `DesignResolution` propriété.

`DesignResolution`Représente la largeur et la hauteur de la zone CocosSharp pour le placement et le redimensionnement des objets. La résolution réelle de la zone est mesurée en *pixels* , tandis que `DesignResolution` est mesurée en *unités*universelles. Le diagramme suivant illustre la résolution des différentes parties de la vue, telles qu’elles apparaissent sur un iPhone 5 avec une résolution d’écran de 640x1136 pixels :

![Résolution de conception de iPhone 5 s](cocossharp-images/image7.png)

Le diagramme ci-dessus affiche les dimensions en pixels à l’extérieur de l’écran en texte noir. Les unités sont affichées à l’intérieur du diagramme en texte blanc. Voici quelques informations importantes affichées ci-dessus :

- L’origine de l’affichage CocosSharp est en bas à gauche. Le déplacement vers la droite augmente la valeur X, et le déplacement vers le haut augmente la valeur Y. Notez que la valeur Y est inversée par rapport à d’autres moteurs de disposition 2D, où (0, 0) est l’angle supérieur gauche du canevas.
- Le comportement par défaut de CocosSharp est de conserver les proportions de sa vue. Étant donné que la première ligne de la grille est plus large que la hauteur, CocosSharp ne remplit pas toute la largeur de sa cellule, comme le montre le rectangle blanc en pointillés. Ce comportement peut être modifié, comme décrit dans le [Guide gestion de plusieurs résolutions dans CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/resolutions.md).
- Dans cet exemple, CocosSharp conserve une zone d’affichage de 100 unités de largeur et de hauteur, quelle que soit la taille ou le rapport hauteur/largeur de son appareil. Cela signifie que le code peut supposer que X = 100 représente la limite droite de l’affichage CocosSharp, en conservant la disposition cohérente sur tous les appareils.

#### <a name="ccdrawnode-details"></a>Détails de CCDrawNode

Notre application simple utilise la `CCDrawNode` classe pour dessiner un cercle. Cette classe peut être très utile pour les applications d’entreprise dans la mesure où elle fournit un rendu géométrique basé sur des vecteurs, une fonctionnalité manquante dans Xamarin.Forms . En plus des cercles, la `CCDrawNode` classe peut être utilisée pour dessiner des rectangles, des splines, des lignes et des polygones personnalisés. `CCDrawNode`est également facile à utiliser, car il ne nécessite pas l’utilisation de fichiers image (tels que. png). Vous trouverez une description plus détaillée de CCDrawNode dans la section [géométrie de dessin avec le guide CCDrawNode](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md).

### <a name="5-interacting-with-cocossharp"></a>5. interaction avec CocosSharp

Les éléments visuels CocosSharp (tels que `CCDrawNode` ) héritent de la `CCNode` classe. `CCNode`fournit deux propriétés qui peuvent être utilisées pour positionner un objet par rapport à son parent : `PositionX` et `PositionY` . Notre code utilise actuellement ces deux propriétés pour positionner le centre du cercle, comme indiqué dans cet extrait de code :

```csharp
circle.PositionX = 20;
circle.PositionY = 50;
```

Il est important de noter que les objets CocosSharp sont positionnés par des valeurs de position explicite, par opposition à la plupart des Xamarin.Forms vues, qui sont automatiquement positionnées en fonction du comportement de leurs contrôles de disposition parents.

Nous allons ajouter du code pour permettre à l’utilisateur de cliquer sur l’un des deux boutons pour déplacer le cercle vers la gauche ou vers la droite de 10 unités (et non de pixels, puisque le cercle dessine dans l’espace de l’unité universelle CocosSharp). Tout d’abord, nous allons créer deux méthodes publiques dans la `GameScene` classe :

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

Ensuite, nous ajouterons des gestionnaires aux deux boutons dans `HomePage` pour répondre aux clics. Lorsque vous avez terminé, notre `CreateBottomHalf` méthode contient le code suivant :

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

Le cercle CocosSharp se déplace maintenant en réponse aux clics. Nous pouvons également voir clairement les limites de la zone de dessin CocosSharp en déplaçant le cercle suffisamment loin vers la gauche ou vers la droite :

![GameScene avec cercle mobile](cocossharp-images/image8.png)

## <a name="summary"></a>Résumé

Ce guide montre comment ajouter CocosSharp à un projet existant Xamarin.Forms , comment créer une interaction entre Xamarin.Forms et CocosSharp, et présente les différentes considérations à prendre en compte lors de la création de dispositions dans CocosSharp.

Le moteur de jeu CocosSharp offre un grand nombre de fonctionnalités et de détails. ce guide ne fait que gratter la surface de ce que CocosSharp peut faire. Les développeurs qui souhaitent en savoir plus sur CocosSharp peuvent trouver de nombreux articles dans l' [Archive CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/).

## <a name="related-links"></a>Liens associés

- [CocosSharpForms (exemple)](https://github.com/xamarin/xamarin-forms-samples/tree/master/CocosSharpForms)
