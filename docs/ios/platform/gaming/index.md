---
title: API de jeu iOS dans Xamarin. iOS
description: Cet article aborde les nouvelles améliorations apportées à iOS 9 pour les jeux, qui peuvent être utilisées pour améliorer les fonctionnalités graphiques et audio de votre jeu Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 958D38FD-9240-482E-9A42-D6671ED8F2B0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: b21e72cc072b614c2e900d7c7b3aa04c9f0812cb
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939774"
---
# <a name="ios-gaming-apis-in-xamarinios"></a>API de jeu iOS dans Xamarin. iOS

_Cet article aborde les nouvelles améliorations apportées à iOS 9 pour les jeux, qui peuvent être utilisées pour améliorer les fonctionnalités graphiques et audio de votre jeu Xamarin. iOS._

Apple a apporté plusieurs améliorations technologiques aux API de jeu dans iOS 9 qui facilitent l’implémentation de graphiques et de sons de jeu dans une application Xamarin. iOS.
Celles-ci incluent la facilité de développement via des frameworks de haut niveau et l’exploitation de la puissance du GPU de l’appareil iOS pour améliorer la vitesse et les capacités graphiques.

[![Exemple d’application qui exécute le cheptel](images/flocking01.png)](images/flocking01.png#lightbox)

Cela comprend les nuanceurs GameplayKit, ReplayKit, Model I/O, MetalKit et Metal performance, ainsi que les nouvelles fonctionnalités améliorées de metal, SceneKit et SpriteKit.

Cet article présente toutes les façons d’améliorer votre jeu Xamarin. iOS avec les nouvelles améliorations apportées aux jeux d’iOS 9 :

## <a name="introducing-gameplaykit"></a>Présentation de GameplayKit

La nouvelle infrastructure GameplayKit d’Apple fournit un ensemble de technologies qui facilite la création de jeux pour les appareils iOS en réduisant la quantité de code courant et répétitif requis pour l’implémentation. GameplayKit fournit des outils permettant de développer les mécanismes de jeu qui peuvent ensuite être facilement combinés avec un moteur graphique (tel que SceneKit ou SpriteKit) pour fournir rapidement un jeu terminé.

GameplayKit comprend plusieurs algorithmes, courants et de jeu, tels que :

- Un comportement basé sur une simulation d’agent qui vous permet de définir des mouvements et des objectifs que l’IA poursuivra automatiquement.
- Une intelligence artificielle MinMax pour les jeux basés sur l’activation.
- Système de règles pour la logique de jeu pilotée par les données avec un raisonnement approximatif pour fournir un comportement émergente.

En outre, GameplayKit prend une approche de bloc de construction du développement de jeux à l’aide d’une architecture modulaire qui offre les fonctionnalités suivantes :

- Machine à États pour gérer des systèmes complexes basés sur du code procédural dans le jeu.
- Outils pour fournir une lecture aléatoire de jeux et une imprévisible sans entraîner de problèmes de débogage.
- Architecture basée sur des entités, réutilisable et composant.

Pour en savoir plus sur GameplayKit, consultez le [Guide de programmation GameplayKit](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/GameplayKit_Guide/index.html#//apple_ref/doc/uid/TP40015172) d’Apple et [référence GameplayKit Framework](https://developer.apple.com/library/prerelease/ios/documentation/GameplayKit/Reference/GameplayKit_Framework/index.html#//apple_ref/doc/uid/TP40015199).

## <a name="gameplaykit-examples"></a>Exemples GameplayKit

Examinons rapidement l’implémentation d’une simple application de jeu de jeux dans une application Xamarin. iOS à l’aide du kit Game Play.

### <a name="pathfinding"></a>Pathfinding

Pathfinding est la capacité d’un élément AI d’un jeu à se trouver dans la grille du jeu.
Par exemple, un ennemi 2D se trouve à travers un labyrinthe ou un caractère 3D par le biais d’un terrain universel de première personne.

Tenez compte de la carte suivante :

[![Exemple de carte pathfinding](images/gkpathfindpath.png)](images/gkpathfindpath.png#lightbox)

À l’aide de pathfinding, ce code C# peut trouver un moyen par le biais de la carte :

```csharp
var a = GKGraphNode2D.FromPoint (new Vector2 (0, 5));
var b = GKGraphNode2D.FromPoint (new Vector2 (3, 0));
var c = GKGraphNode2D.FromPoint (new Vector2 (2, 6));
var d = GKGraphNode2D.FromPoint (new Vector2 (4, 6));
var e = GKGraphNode2D.FromPoint (new Vector2 (6, 5));
var f = GKGraphNode2D.FromPoint (new Vector2 (6, 0));

a.AddConnections (new [] { b, c }, false);
b.AddConnections (new [] { e, f }, false);
c.AddConnections (new [] { d }, false);
d.AddConnections (new [] { e, f }, false);

var graph = GKGraph.FromNodes(new [] { a, b, c, d, e, f });

var a2e = graph.FindPath (a, e); // [ a, c, d, e ]
var a2f = graph.FindPath (a, f); // [ a, b, f ]

Console.WriteLine(String.Join ("->", (object[]) a2e));
Console.WriteLine(String.Join ("->", (object[]) a2f));
```

### <a name="classical-expert-system"></a>Système expert classique

L’extrait de code C# suivant montre comment GameplayKit peut être utilisé pour implémenter un système expert classique :

```csharp
string output = "";
bool reset = false;
int input = 15;

public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    /*
    If reset is true, clear the output and set reset to false
    */
    var clearRule = GKRule.FromPredicate ((rules) => reset, rules => {
        output = "";
        reset = false;
    });
    clearRule.Salience = 1;

    var fizzRule = GKRule.FromPredicate (mod (3), rules => {
        output += "fizz";
    });
    fizzRule.Salience = 2;

    var buzzRule = GKRule.FromPredicate (mod (5), rules => {
        output += "buzz";
    });
    buzzRule.Salience = 2;

    /*
    This *always* evaluates to true, but is higher Salience, so evaluates after lower-salience items
    (which is counter-intuitive). Print the output, and reset (thus triggering "ResetRule" next time)
    */
    var outputRule = GKRule.FromPredicate (rules => true, rules => {
        System.Console.WriteLine(output == "" ? input.ToString() : output);
        reset = true;
    });
    outputRule.Salience = 3;

    var rs = new GKRuleSystem ();
    rs.AddRules (new [] {
        clearRule,
        fizzRule,
        buzzRule,
        outputRule
    });

    for (input = 1; input < 16; input++) {
        rs.Evaluate ();
        rs.Reset ();
    }
}

protected Func<GKRuleSystem, bool> mod(int m)
{
    Func<GKRuleSystem,bool> partiallyApplied = (rs) => input % m == 0;
    return partiallyApplied;
}
```

En fonction d’un ensemble de règles donné ( `GKRule` ) et d’un ensemble connu d’entrées, le système expert ( `GKRuleSystem` ) crée une sortie prévisible ( `fizzbuzz` pour l’exemple ci-dessus).

### <a name="flocking"></a>Cheptel

Le troupeau permet à un groupe d’entités de jeu contrôlées par l’intelligence artificielle de se comporter comme un troupeau, où le groupe répond aux mouvements et aux actions d’une entité de chef comme un cheptel d’oiseaux en vol ou une école de pêche de poisson.

L’extrait de code C# suivant implémente un comportement de troupeau à l’aide de GameplayKit et SpriteKit pour l’affichage graphique :

```csharp
using System;
using SpriteKit;
using CoreGraphics;
using UIKit;
using GameplayKit;
using Foundation;
using System.Collections.Generic;
using System.Linq;
using OpenTK;

namespace FieldBehaviorExplorer
{
    public static class FlockRandom
    {
        private static GKARC4RandomSource rand = new GKARC4RandomSource ();

        static FlockRandom ()
        {
            rand.DropValues (769);
        }

        public static float NextUniform ()
        {
            return rand.GetNextUniform ();
        }
    }

    public class FlockingScene : SKScene
    {
        List<Boid> boids = new List<Boid> ();
        GKComponentSystem componentSystem;
        GKAgent2D trackingAgent; //Tracks finger on screen
        double lastUpdateTime = Double.NaN;
        //Hold on to behavior so it doesn't get GC'ed
        static GKBehavior flockingBehavior;
        static GKGoal seekGoal;

        public FlockingScene (CGSize size) : base (size)
        {
            AddRandomBoids (20);

            var scale = 0.4f;
            //Flocking system
            componentSystem = new GKComponentSystem (typeof(GKAgent2D));
            var behavior = DefineFlockingBehavior (boids.Select (boid => boid.Agent).ToArray<GKAgent2D>(), scale);
            boids.ForEach (boid => {
                boid.Agent.Behavior = behavior;
                componentSystem.AddComponent(boid.Agent);
            });

            trackingAgent = new GKAgent2D ();
            trackingAgent.Position = new Vector2 ((float) size.Width / 2.0f, (float) size.Height / 2.0f);
            seekGoal = GKGoal.GetGoalToSeekAgent (trackingAgent);
        }

        public override void TouchesBegan (NSSet touches, UIEvent evt)
        {
            boids.ForEach(boid => boid.Agent.Behavior.SetWeight(1.0f, seekGoal));
        }

        public override void TouchesEnded (NSSet touches, UIEvent evt)
        {
            boids.ForEach (boid => boid.Agent.Behavior.SetWeight (0.0f, seekGoal));
        }

        public override void TouchesMoved (NSSet touches, UIEvent evt)
        {
            var touch = (UITouch) touches.First();
            var loc = touch.LocationInNode (this);
            trackingAgent.Position = new Vector2((float) loc.X, (float) loc.Y);
        }

        private void AddRandomBoids (int count)
        {
            var scale = 0.4f;
            for (var i = 0; i < count; i++) {
                var b = new Boid (UIColor.Red, this.Size, scale);
                boids.Add (b);
                this.AddChild (b);
            }
        }

        internal static GKBehavior DefineFlockingBehavior(GKAgent2D[] boidBrains, float scale)
        {
            if (flockingBehavior == null) {
                var flockingGoals = new GKGoal[3];
                flockingGoals [0] = GKGoal.GetGoalToSeparate (boidBrains, 100.0f * scale, (float)Math.PI * 8.0f);
                flockingGoals [1] = GKGoal.GetGoalToAlign (boidBrains, 40.0f * scale, (float)Math.PI * 8.0f);
                flockingGoals [2] = GKGoal.GetGoalToCohere (boidBrains, 40.0f * scale, (float)Math.PI * 8.0f);

                flockingBehavior = new GKBehavior ();
                flockingBehavior.SetWeight (25.0f, flockingGoals [0]);
                flockingBehavior.SetWeight (10.0f, flockingGoals [1]);
                flockingBehavior.SetWeight (10.0f, flockingGoals [2]);
            }
            return flockingBehavior;
        }

        public override void Update (double currentTime)
        {
            base.Update (currentTime);
            if (Double.IsNaN(lastUpdateTime)) {
                lastUpdateTime = currentTime;
            }
            var delta = currentTime - lastUpdateTime;
            componentSystem.Update (delta);
        }
    }

    public class Boid : SKNode, IGKAgentDelegate
    {
        public GKAgent2D Agent { get { return brains; } }
        public SKShapeNode Sprite { get { return sprite; } }

        class BoidSprite : SKShapeNode
        {
            public BoidSprite (UIColor color, float scale)
            {
                var rot = CGAffineTransform.MakeRotation((float) (Math.PI / 2.0f));
                var path = new CGPath ();
                path.MoveToPoint (rot, new CGPoint (10.0, 0.0));
                path.AddLineToPoint (rot, new CGPoint (0.0, 30.0));
                path.AddLineToPoint (rot, new CGPoint (10.0, 20.0));
                path.AddLineToPoint (rot, new CGPoint (20.0, 30.0));
                path.AddLineToPoint (rot, new CGPoint (10.0, 0.0));
                path.CloseSubpath ();

                this.SetScale (scale);
                this.Path = path;
                this.FillColor = color;
                this.StrokeColor = UIColor.White;

            }
        }

        private GKAgent2D brains;
        private BoidSprite sprite;
        private static int boidId = 0;

        public Boid (UIColor color, CGSize size, float scale)
        {
            brains = BoidBrains (size, scale);
            sprite = new BoidSprite (color, scale);
            sprite.Position = new CGPoint(brains.Position.X, brains.Position.Y);
            sprite.ZRotation = brains.Rotation;
            sprite.Name = boidId++.ToString ();

            brains.Delegate = this;

            this.AddChild (sprite);
        }

        private GKAgent2D BoidBrains(CGSize size, float scale)
        {
            var brains = new GKAgent2D ();
            var x = (float) (FlockRandom.NextUniform () * size.Width);
            var y = (float) (FlockRandom.NextUniform () * size.Height);
            brains.Position = new Vector2 (x, y);

            brains.Rotation = (float)(FlockRandom.NextUniform () * Math.PI * 2.0);
            brains.Radius = 30.0f * scale;
            brains.MaxSpeed = 0.5f;
            return brains;
        }

        [Export ("agentDidUpdate:")]
        public void AgentDidUpdate (GameplayKit.GKAgent agent)
        {
        }

        [Export ("agentWillUpdate:")]
        public void AgentWillUpdate (GameplayKit.GKAgent agent)
        {
            var brainsIn = (GKAgent2D) agent;
            sprite.Position = new CGPoint(brainsIn.Position.X, brainsIn.Position.Y);
            sprite.ZRotation = brainsIn.Rotation;
            Console.WriteLine ($"{sprite.Name} -> [{sprite.Position}], {sprite.ZRotation}");
        }
    }
}
```

Ensuite, implémentez cette scène dans un contrôleur d’affichage :

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
        // Perform any additional setup after loading the view, typically from a nib.
        this.View = new SKView {
        ShowsFPS = true,
        ShowsNodeCount = true,
        ShowsDrawCount = true
    };
}

public override void ViewWillLayoutSubviews ()
{
    base.ViewWillLayoutSubviews ();

    var v = (SKView)View;
    if (v.Scene == null) {
        var scene = new FlockingScene (View.Bounds.Size);
        scene.ScaleMode = SKSceneScaleMode.AspectFill;
        v.PresentScene (scene);
    }
}
```

Lorsqu’il est exécuté, le petit _« Boids »_ animé s’anime autour de nos pressions sur les doigts :

[![Les petits Boids animés s’appuient sur les pressions des doigts](images/flocking01.png)](images/flocking01.png#lightbox)

### <a name="other-apple-examples"></a>Autres exemples Apple

Outre les exemples présentés ci-dessus, Apple a fourni les exemples d’applications suivants qui peuvent être transcodés en C# et Xamarin. iOS :

- [FourInARow : utilisation de la stratège GameplayKit MinMax pour les adversaires IA](https://developer.apple.com/library/prerelease/ios/samplecode/FourInARow/Introduction/Intro.html#//apple_ref/doc/uid/TP40016142)
- [AgentsCatalog : utilisation du système d’agents dans GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/AgentsCatalog/Introduction/Intro.html#//apple_ref/doc/uid/TP40016141)
- [DemoBots : création d’un jeu multiplateforme avec SpriteKit et GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)

## <a name="metal"></a>Métallique

Dans iOS 9, Apple a apporté plusieurs modifications et ajouts à Metal pour fournir un accès à faible charge au GPU. À l’aide de metal, vous pouvez maximiser le potentiel graphique et informatique de vos applications iOS.

L’infrastructure métallique comprend les nouvelles fonctionnalités suivantes :

- Nouvelles textures de stencil privées et de profondeur pour OS X.
- Amélioration de la qualité de l’ombre avec la fixation de profondeur et les valeurs de gabarit avant et arrière distinctes.
- Améliorations apportées au langage Metal Shading Language et à la bibliothèque Metal standard.
- Les nuanceurs de calcul prennent en charge une plus large gamme de formats de pixel.

### <a name="the-metalkit-framework"></a>Framework MetalKit

L’infrastructure MetalKit fournit un ensemble de classes et de fonctionnalités utilitaires qui réduisent la quantité de travail nécessaire pour utiliser le métal dans une application iOS. MetalKit fournit la prise en charge dans trois domaines clés :

1. Le chargement de texture asynchrone à partir de diverses sources, dont des formats courants tels que PNG, JPEG, KTX et PVR.
2. Accès facile aux ressources basées sur les e/s de modèle pour la gestion des modèles spécifiques aux métaux. Ces fonctionnalités ont été hautement optimisées pour fournir un transfert de données efficace entre les maillages d’e/s de modèle et les tampons métalliques.
3. Affichages métalliques prédéfinis et gestion des affichages qui réduisent de façon considérable la quantité de code requise pour afficher les rendus graphiques dans une application iOS.

Pour en savoir plus sur MetalKit, consultez les informations de référence sur l' [infrastructure MetalKit](https://developer.apple.com/library/prerelease/ios/documentation/MetalKit/Reference/MTKFrameworkReference/index.html#//apple_ref/doc/uid/TP40015356)d’Apple, le [Guide de programmation métallique](https://developer.apple.com/library/prerelease/ios/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221), le Guide de [Référence du Framework métallique](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalFrameworkReference/index.html#//apple_ref/doc/uid/TP40014161) et le [Guide du langage d’ombrage métallique](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364).

### <a name="metal-performance-shaders-framework"></a>Infrastructure des nuanceurs de performances métalliques

L’infrastructure de nuanceur de performances métalliques fournit un ensemble hautement optimisé de nuanceurs basés sur des graphiques et des calculs pour une utilisation dans vos applications iOS basées sur le métal. Chaque nuanceur de metal performance shader Framework a été spécifiquement réglé pour offrir des performances élevées sur les GPU iOS prises en charge par Metal.

En utilisant des classes de nuanceur de performances métalliques, vous pouvez obtenir les meilleures performances possibles sur chaque GPU iOS spécifique sans avoir à cibler et à gérer des bases de code individuelles. Les nuanceurs de performances métalliques peuvent être utilisés avec toutes les ressources métalliques, telles que les textures et les tampons.

L’infrastructure de nuanceur de performances métalliques fournit un ensemble de nuanceurs courants tels que :

- **Flou gaussien** ( `MPSImageGaussianBlur` )
- **Détection Sobel Edge** ( `MPSImageSobel` )
- **Histogramme** ( `MPSImageHistogram` )

Pour plus d’informations, consultez le [Guide du langage Metal Shading](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364)d’Apple.

## <a name="introducing-model-io"></a>Présentation des e/s de modèle

L’infrastructure d’e/s de modèle d’Apple offre une compréhension approfondie des ressources 3D (telles que les modèles et leurs ressources associées). Les e/s de modèle fournissent vos jeux iOS avec des matériaux physiques, des modèles et de l’éclairage qui peuvent être utilisés avec GameplayKit, Metal et SceneKit.

Avec les e/s de modèle, vous pouvez prendre en charge les types de tâches suivants :

- Importez l’éclairage, les matériaux, les données de maillage, les paramètres de l’appareil photo et d’autres informations basées sur les scènes à partir d’un large éventail de logiciels et de formats de moteurs de jeux.
- Traitez ou générez des informations basées sur les scènes, telles que la création d’un Domes ou d’un éclairage de cuisson dans une maille.
- Fonctionne avec MetalKit, SceneKit et GLKit pour charger efficacement les ressources de jeu dans des mémoires tampons GPU à des fins de rendu.
- Exportez les informations basées sur les scènes vers un large éventail de logiciels populaires et de formats de moteurs de jeux.

Pour en savoir plus sur les e/s de modèle, consultez [référence de l’infrastructure d’e/s de modèle](https://developer.apple.com/library/prerelease/ios/documentation/ModelIO/Reference/ModelIO_Framework/index.html#//apple_ref/doc/uid/TP40015421) d’Apple

## <a name="introducing-replaykit"></a>Présentation de ReplayKit

La nouvelle infrastructure ReplayKit d’Apple vous permet d’ajouter facilement un enregistrement de jeu à votre jeu iOS et de permettre à l’utilisateur de modifier et de partager rapidement et facilement cette vidéo à partir de l’application.

Pour plus d’informations, consultez la page [réseaux sociaux d’Apple avec ReplayKit et Game Center](https://developer.apple.com/videos/wwdc/2015/?id=605) et leur [DemoBots : création d’un jeu multiplateforme avec l’exemple d’application SpriteKit et GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179) .

## <a name="scenekit"></a>SceneKit

Scene Kit est une API graphique de scène 3D qui simplifie l’utilisation des graphiques 3D. Elle a été introduite pour la première fois dans le système d’exploitation X 10,8 et est désormais disponible dans iOS 8. Avec Scene Kit, la création de visualisations 3D immersifs et de jeux informels 3D ne nécessite pas d’expertise dans OpenGL. En s’appuyant sur les concepts courants du graphique de scène, Scene Kit résume les complexités de OpenGL et OpenGL ES, ce qui facilite l’ajout de contenu 3D à une application. Toutefois, si vous êtes un expert OpenGL, Scene kit offre une excellente prise en charge de la liaison directe avec OpenGL. Il comprend également de nombreuses fonctionnalités qui complètent les graphiques 3D, telles que la physique, et s’intègrent parfaitement avec plusieurs autres frameworks Apple, tels que l’animation principale, l’image principale et le sprite kit.

Pour plus d’informations, consultez notre documentation [SceneKit](~/ios/platform/gaming/scenekit.md) .

### <a name="scenekit-changes"></a>Modifications de SceneKit

Apple a ajouté les nouvelles fonctionnalités suivantes à SceneKit pour iOS 9 :

- Xcode fournit maintenant un éditeur de scène qui vous permet de créer rapidement des jeux et des applications 3D interactives en modifiant des scènes directement à partir de Xcode.
- Les `SCNView` `SCNSceneRenderer` classes et peuvent être utilisées pour activer le rendu métallique (sur les appareils iOS pris en charge).
- Les `SCNAudioPlayer` `SCNNode` classes et peuvent être utilisées pour ajouter des effets audio spatiaux qui effectuent le suivi automatique de la position d’un joueur dans une application iOS.

Pour plus d’informations, consultez notre [documentation SceneKit](~/ios/platform/introduction-to-ios8.md#scenekit) et la [référence de Framework SceneKit](https://developer.apple.com/library/prerelease/ios/documentation/SceneKit/Reference/SceneKit_Framework/index.html#//apple_ref/doc/uid/TP40012283) d’Apple et [Fox : Building a SceneKit match with the Xcode Scene Editor](https://developer.apple.com/library/prerelease/ios/samplecode/Fox/Introduction/Intro.html#//apple_ref/doc/uid/TP40016154) Sample Project.

## <a name="spritekit"></a>SpriteKit

Sprite Kit, l’infrastructure de jeu 2D d’Apple, présente de nouvelles fonctionnalités intéressantes dans iOS 8 et OS X Yosemite. Cela inclut l’intégration avec Scene Kit, la prise en charge du nuanceur, l’éclairage, les ombres, les contraintes, la génération normale de cartes et les améliorations physiques. En particulier, les nouvelles fonctionnalités physiques rendent très facile l’ajout d’effets réalistes à un jeu.

Pour plus d’informations, consultez notre documentation [SpriteKit](~/ios/platform/gaming/spritekit.md) .

### <a name="spritekit-changes"></a>Modifications de SpriteKit

Apple a ajouté les nouvelles fonctionnalités suivantes à SpriteKit pour iOS 9 :

- Effet audio spatial qui effectue le suivi automatique de la position du joueur avec la `SKAudioNode` classe.
- Xcode propose désormais un éditeur de scène et un éditeur d’action pour faciliter la création de jeux et d’applications en 2D.
- Prise en charge du jeu de défilement facile avec les nouveaux objets de nœuds d’appareil photo ( `SKCameraNode` ).
- Sur les appareils iOS qui prennent en charge Metal, SpriteKit les utilisera automatiquement pour le rendu, même si vous utilisiez déjà des nuanceurs OpenGL ES personnalisés.

Pour plus d’informations, consultez notre [documentation SpriteKit informations](~/ios/platform/introduction-to-ios8.md#spritekit) de [référence](https://developer.apple.com/library/prerelease/ios/documentation/SpriteKit/Reference/SpriteKitFramework_Ref/index.html#//apple_ref/doc/uid/TP40013041) sur l’infrastructure d’Apple SpriteKit et leur [DemoBots : génération d’un jeu multiplateforme avec SpriteKit et GameplayKit exemple d'](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179) application.

## <a name="summary"></a>Résumé

Cet article a abordé les nouvelles fonctionnalités de jeux fournies par iOS 9 pour vos applications Xamarin. iOS.
Il a introduit les GameplayKit et les e/s de modèle ; les principales améliorations apportées à Metal ; et les nouvelles fonctionnalités de SceneKit et SpriteKit.

## <a name="related-links"></a>Liens associés

- [Exemples iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 pour les développeurs](https://developer.apple.com/ios/pre-release/)
- [iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
