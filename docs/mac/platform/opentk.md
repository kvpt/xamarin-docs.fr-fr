---
title: Présentation de OpenTK dans Xamarin. Mac
description: Cet article fournit une introduction à l’utilisation de OpenTK dans une application Xamarin. Mac. Il aborde la création et la gestion d’une fenêtre de jeu, le rendu d’un objet simple et l’affichage de l’objet pour l’utilisateur.
ms.prod: xamarin
ms.assetid: BDE05645-7273-49D3-809B-8642347678D2
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 953b36eb48823cc23c5e7b3e831beca7b655a057
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645061"
---
# <a name="introduction-to-opentk-in-xamarinmac"></a>Présentation de OpenTK dans Xamarin. Mac

OpenTK (la boîte à outils ouverte) est une bibliothèque de bas C# niveau avancée qui facilite l’utilisation de OpenGL, OpenCL et Open. OpenTK peut être utilisé pour les jeux, les applications scientifiques ou d’autres projets qui requièrent des fonctionnalités graphiques, audio ou de calcul en 3D. Cet article fournit une brève introduction à l’utilisation de OpenTK dans une application Xamarin. Mac.

[![](opentk-images/intro01.png "Exemple d’exécution d’application")](opentk-images/intro01.png#lightbox)

Dans cet article, nous allons aborder les principes fondamentaux de OpenTK dans une application Xamarin. Mac. Nous vous recommandons vivement d’utiliser l’article [Hello, Mac](~/mac/get-started/hello-mac.md) , en particulier la [Présentation de Xcode et Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) et les sections [actions et actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , car il aborde les concepts et les techniques clés que nous allons utiliser dans. Cet article.

Vous pouvez également jeter un coup d’œil à la section [exposition des C# classes/méthodes à Objective-C](~/mac/internals/how-it-works.md) du document [Internals Xamarin. Mac.](~/mac/internals/how-it-works.md) elle explique également les `Register` commandes `Export` et utilisées pour relier vos C# classes à Objets objective-C et éléments d’interface utilisateur.

<a name="About_OpenTK" />

## <a name="about-opentk"></a>À propos de OpenTK

Comme indiqué ci-dessus, OpenTK (la boîte à outils ouverte) est une bibliothèque C# de bas niveau avancée qui facilite l’utilisation de OpenGL, OpenCL et Open. L’utilisation de OpenTK dans une application Xamarin. Mac offre les fonctionnalités suivantes:

- **Développement rapide** -OpenTK fournit des types de données forts et une documentation Inline pour améliorer votre workflow de codage et intercepter les erreurs plus rapidement et plus tôt.
- **Intégration simple** : OpenTK a été conçu pour une intégration facile avec les applications .net.
- **Licence permissif** -OpenTK est distribuée sous les licences mit/X11 et est entièrement gratuit.
- **Liaisons riches et de type sécurisé** : OpenTK prend en charge les dernières versions de OpenGL, OpenGL | ES, Open et OpenCL avec chargement automatique des extensions, vérification des erreurs et documentation en ligne.
- **Options GUI flexibles** -OpenTK fournit la fenêtre de jeu native et à hautes performances conçue spécifiquement pour les jeux et Xamarin. Mac.
- Le **code entièrement managé conforme CLS** -OpenTK prend en charge les versions 32 bits et 64 bits de MacOS sans bibliothèques non managées.
- **boîte à outils mathématiques 3D** OpenTK fournit `Vector` `Matrix` `Bezier` des structs, et à l’aide de sa boîte à outils mathématiques 3D. `Quaternion`

OpenTK peut être utilisé pour les jeux, les applications scientifiques ou d’autres projets qui requièrent des fonctionnalités graphiques, audio ou de calcul en 3D.

Pour plus d’informations, consultez [le site Web Open Toolkit](http://www.opentk.com) .

<a name="OpenTK_Quickstart" />

## <a name="opentk-quickstart"></a>Démarrage rapide de OpenTK

En guise d’introduction rapide à l’utilisation de OpenTK dans une application Xamarin. Mac, nous allons créer une application simple qui ouvre une vue de jeu, restitue un triangle simple dans cette vue et attache la vue de jeu à la fenêtre principale de l’application Mac pour afficher le triangle à l’utilisateur.

<a name="Starting_a_New_Project" />

### <a name="starting-a-new-project"></a>Démarrage d’un nouveau projet

Démarrez Visual Studio pour Mac et créez une nouvelle solution Xamarin. Mac. Sélectionnez application **Mac** >  > application de cacao**générale:**  > 

[![](opentk-images/sample01.png "Ajout d’une nouvelle application de cacao")](opentk-images/sample01.png#lightbox)

Entrez `MacOpenTK` pour le **nom du projet**:

[![](opentk-images/sample02.png "Définition du nom du projet")](opentk-images/sample02.png#lightbox)

Cliquez sur le bouton **créer** pour générer le nouveau projet.

<a name="Including_OpenTK" />

### <a name="including-opentk"></a>Y compris OpenTK

Avant de pouvoir utiliser Open TK dans une application Xamarin. Mac, vous devez inclure une référence à l’assembly OpenTK. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le dossier **références** et sélectionnez **modifier les références...** .

Activez la case à `OpenTK` cocher et cliquez sur le bouton **OK** :

[![](opentk-images/sample03.png "Modification des références de projet")](opentk-images/sample03.png#lightbox)

<a name="Using_OpenTK" />

### <a name="using-opentk"></a>Utilisation de OpenTK

Une fois le nouveau projet créé, double-cliquez `MainWindow.cs` sur le fichier dans le **Explorateur de solutions** pour l’ouvrir et le modifier. Faites en `MainWindow` sorte que la classe ressemble à ce qui suit:

```csharp
using System;
using System.Drawing;
using OpenTK;
using OpenTK.Graphics;
using OpenTK.Graphics.OpenGL;
using OpenTK.Platform.MacOS;
using Foundation;
using AppKit;
using CoreGraphics;

namespace MacOpenTK
{
    public partial class MainWindow : NSWindow
    {
        #region Computed Properties
        public MonoMacGameView Game { get; set; }
        #endregion

        #region Constructors
        public MainWindow (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindow (NSCoder coder) : base (coder)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Create new Game View and replace the window content with it
            Game = new MonoMacGameView(ContentView.Frame);
            ContentView = Game;

            // Wire-up any required Game events
            Game.Load += (sender, e) =>
            {
                // TODO: Initialize settings, load textures and sounds here
            };

            Game.Resize += (sender, e) =>
            {
                // Adjust the GL view to be the same size as the window
                GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
            };

            Game.UpdateFrame += (sender, e) =>
            {
                // TODO: Add any game logic or physics
            };

            Game.RenderFrame += (sender, e) =>
            {
                // Setup buffer
                GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
                GL.MatrixMode(MatrixMode.Projection);

                // Draw a simple triangle
                GL.LoadIdentity();
                GL.Ortho(-1.0, 1.0, -1.0, 1.0, 0.0, 4.0);
                GL.Begin(BeginMode.Triangles);
                GL.Color3(Color.MidnightBlue);
                GL.Vertex2(-1.0f, 1.0f);
                GL.Color3(Color.SpringGreen);
                GL.Vertex2(0.0f, -1.0f);
                GL.Color3(Color.Ivory);
                GL.Vertex2(1.0f, 1.0f);
                GL.End();

            };

            // Run the game at 60 updates per second
            Game.Run(60.0);
        }
        #endregion
    }
}
```

Passons en revue ce code en détail ci-dessous.

<a name="Required_APIs" />

### <a name="required-apis"></a>API requises

Plusieurs références sont requises pour utiliser OpenTK dans une classe Xamarin. Mac. Au début de la définition, nous avons inclus les instructions `using` suivantes:

```csharp
using System;
using System.Drawing;
using OpenTK;
using OpenTK.Graphics;
using OpenTK.Graphics.OpenGL;
using OpenTK.Platform.MacOS;
using Foundation;
using CoreGraphics;
```
Cet ensemble minimal est requis pour toute classe qui utilise OpenTK.

<a name="Adding_the_Game_View" />

### <a name="adding-the-game-view"></a>Ajout de la vue du jeu

Ensuite, nous devons créer une vue de jeu pour contenir toutes les interactions avec OpenTK et afficher les résultats. Nous avons utilisé le code suivant:

```csharp
public MonoMacGameView Game { get; set; }
...

// Create new Game View and replace the window content with it
Game = new MonoMacGameView(ContentView.Frame);
ContentView = Game;
```

Ici, nous avons fait de la vue du jeu la même taille que notre fenêtre Mac principale et remplacé l’affichage du contenu de la `MonoMacGameView`fenêtre par le nouveau. Étant donné que nous avons remplacé le contenu de la fenêtre existante, notre vue est automatiquement redimensionnée lorsque la fenêtre principale est redimensionnée.

<a name="Responding_to_Events" />

### <a name="responding-to-events"></a>Réponse aux événements

Chaque vue de jeu doit répondre à plusieurs événements par défaut. Dans cette section, vous allez aborder les principaux événements requis.

<a name="The_Load_Event" />

### <a name="the-load-event"></a>Événement de chargement

L' `Load` événement est l’emplacement où charger des ressources à partir d’un disque, comme des images, des textures ou de la musique. Pour notre application de test simple, nous n’utilisons pas l' `Load` événement, mais nous l’avons inclus à des fins de référence:

```csharp
Game.Load += (sender, e) =>
{
    // TODO: Initialize settings, load textures and sounds here
};
```

<a name="The_Resize_Event" />

### <a name="the-resize-event"></a>Événement Resize

L' `Resize` événement doit être appelé chaque fois que la vue du jeu est redimensionnée. Pour notre exemple d’application, nous définissons la fenêtre d’affichage du GL sur la même taille que notre vue de jeu (qui est automatiquement redimensionnée par la fenêtre principale Mac) avec le code suivant:

```csharp
Game.Resize += (sender, e) =>
{
    // Adjust the GL view to be the same size as the window
    GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
};
```

<a name="The_UpdateFrame_Event" />

### <a name="the-updateframe-event"></a>Événement UpdateFrame

L' `UpdateFrame` événement est utilisé pour gérer les entrées d’utilisateur, mettre à jour les positions des objets, exécuter des calculs physiques ou ai. Pour notre application de test simple, nous n’utilisons pas l' `UpdateFrame` événement, mais nous l’avons inclus à des fins de référence: 

```csharp
Game.UpdateFrame += (sender, e) =>
{
    // TODO: Add any game logic or physics
};
```

> [!IMPORTANT]
> L’implémentation de Xamarin. Mac de OpenTK n’inclut pas `Input API`le. vous devrez donc utiliser les API fournies par Apple pour ajouter la prise en charge du clavier et de la souris. Vous pouvez éventuellement créer une instance personnalisée du `MonoMacGameView` et remplacer les `KeyDown` méthodes et `KeyUp` .

<a name="The_RenderFrame_Event" />

### <a name="the-renderframe-event"></a>Événement RenderFrame

L' `RenderFrame` événement contient le code utilisé pour effectuer le rendu (dessin) de vos graphiques. Pour notre exemple d’application, nous remplissons la vue de jeu avec un triangle simple: 

```csharp
Game.RenderFrame += (sender, e) =>
{
    // Setup buffer
    GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
    GL.MatrixMode(MatrixMode.Projection);

    // Draw a simple triangle
    GL.LoadIdentity();
    GL.Ortho(-1.0, 1.0, -1.0, 1.0, 0.0, 4.0);
    GL.Begin(BeginMode.Triangles);
    GL.Color3(Color.MidnightBlue);
    GL.Vertex2(-1.0f, 1.0f);
    GL.Color3(Color.SpringGreen);
    GL.Vertex2(0.0f, -1.0f);
    GL.Color3(Color.Ivory);
    GL.Vertex2(1.0f, 1.0f);
    GL.End();

};
```

En général, le code de rendu est avec un `GL.Clear` appel à pour supprimer tous les éléments existants avant de dessiner les nouveaux éléments.

> [!IMPORTANT]
> Pour la version Xamarin. Mac de OpenTK, **n'** appelez pas `SwapBuffers` la méthode de `MonoMacGameView` votre instance à la fin de votre code de rendu. Cela entraînera un stroboscopique rapide de la vue du jeu au lieu d’afficher la vue affichée.

<a name="Running_the_Game_View" />

### <a name="running-the-game-view"></a>Exécution de la vue du jeu

Lorsque tous les événements requis sont définis et que la vue du jeu est attachée à la fenêtre Mac principale de notre application, nous allons lire la vue du jeu et afficher nos graphiques. Utilisez le code suivant :

```csharp
// Run the game at 60 updates per second
Game.Run(60.0);
```

Nous passons la fréquence d’images souhaitée à laquelle nous voulons que la vue de jeu se met à jour. pour `60` notre exemple, nous avons choisi images par seconde (le même taux de rafraîchissement que la TV normale).

Exécutons notre application et voyons la sortie:

[![](opentk-images/intro01.png "Exemple de sortie d’applications")](opentk-images/intro01.png#lightbox)

Si nous redimensionneons notre fenêtre, la vue du jeu sera également hébergée et le triangle sera également redimensionné et mis à jour en temps réel.

<a name="Where_to_Next" />

### <a name="where-to-next"></a>Où ensuite?

Avec les principes fondamentaux de l’utilisation de OpenTk dans une application Xamarin. Mac, voici quelques suggestions sur les étapes à suivre:

- Essayez de modifier la couleur du triangle et la couleur d’arrière-plan de la vue `Load` du `RenderFrame` jeu dans les événements et.
- Modifiez la couleur du triangle lorsque l’utilisateur appuie sur une touche dans `UpdateFrame` les `RenderFrame` événements et ou créez votre propre `MonoMacGameView` classe personnalisée et substituez `KeyUp` les `KeyDown` méthodes et.
- Faites passer le triangle sur l’écran à l’aide des touches sensibles `UpdateFrame` dans l’événement. Indicateur: utilisez la `Matrix4.CreateTranslation` méthode pour créer une matrice de traduction et appelez `GL.LoadMatrix` la méthode pour la charger dans `RenderFrame` l’événement.
- Utilisez une `for` boucle pour restituer plusieurs triangles dans `RenderFrame` l’événement.
- Faites pivoter l’appareil photo pour obtenir une vue différente du triangle dans l’espace 3D. Indicateur: utilisez la `Matrix4.CreateTranslation` méthode pour créer une matrice de traduction et appelez `GL.LoadMatrix` la méthode pour la charger. Vous pouvez également utiliser les `Vector2`classes `Vector3` `Vector4` , et `Matrix4` pour les manipulations d’appareil photo.

Pour obtenir plus d’exemples, consultez [OpenTK Samples GitHub](https://github.com/opentk/opentk/tree/master/Source/Examples) référentiel. Il contient une liste officielle d’exemples d’utilisation de OpenTK. Vous devrez adapter ces exemples pour utiliser avec la version Xamarin. Mac de OpenTK.

Pour obtenir un exemple Xamarin. Mac plus complexe d’une implémentation OpenTK, consultez notre exemple [MonoMacGameView](https://docs.microsoft.com/samples/xamarin/mac-samples/monomacgamewindow) .

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a fait un aperçu rapide de l’utilisation de OpenTK dans une application Xamarin. Mac. Nous avons vu comment créer une fenêtre de jeu, comment attacher la fenêtre de jeu à une fenêtre Mac et comment afficher une forme simple dans la fenêtre du jeu.

## <a name="related-links"></a>Liens associés

- [MacOpenTK (exemple)](https://docs.microsoft.com/samples/xamarin/mac-samples/macopentk)
- [MonoMacGameView (exemple)](https://docs.microsoft.com/samples/xamarin/mac-samples/monomacgamewindow)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Utilisation de Windows](~/mac/user-interface/window.md)
- [La boîte à outils ouverte](http://www.opentk.com)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Présentation de Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
