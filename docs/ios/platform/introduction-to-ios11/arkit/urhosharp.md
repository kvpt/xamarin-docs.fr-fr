---
title: Utilisation de ARKit avec UrhoSharp dans Xamarin. iOS
description: Ce document explique comment configurer une application ARKit dans Xamarin. iOS, puis examine le rendu des images, comment ajuster l’appareil photo, comment détecter des plans, comment utiliser l’éclairage, et bien plus encore. Il aborde également UrhoSharp et l’écriture de code pour HoloLens.
ms.prod: xamarin
ms.assetid: 877AF974-CC2E-48A2-8E1A-0EF9ABF2C92D
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 08/01/2017
ms.openlocfilehash: 4c23caade91a1a46d6b2b9bb2425a5bdead40030
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70289231"
---
# <a name="using-arkit-with-urhosharp-in-xamarinios"></a>Utilisation de ARKit avec UrhoSharp dans Xamarin. iOS

Avec l’introduction de [ARKit](https://developer.apple.com/arkit/), Apple a facilité la création d’applications de réalité augmentées pour les développeurs. ARKit peut effectuer le suivi de la position exacte de votre appareil et détecter les différentes surfaces du monde, et il est alors au développeur de fusionner les données provenant de ARKit dans votre code.

[UrhoSharp](~/graphics-games/urhosharp/index.md) fournit une API 3D complète et facile à utiliser, que vous pouvez utiliser pour créer des applications 3D.   Ils peuvent être mélangés ensemble, ARKit pour fournir les informations physiques sur le monde et Urho pour afficher les résultats.

Cette page explique comment connecter ces deux mondes ensemble pour créer des applications de réalité enrichies.

## <a name="the-basics"></a>Principes de base

Ce que nous voulons faire, c’est de présenter le contenu 3D en haut du monde, tel qu’il est vu par l’iPhone/iPad.   L’idée est de fusionner le contenu provenant de la caméra de l’appareil avec le contenu 3D et, lorsque l’utilisateur de l’appareil se déplace dans la salle pour s’assurer que l’objet 3D se comporte comme c’est le cas dans cette salle de conversation, cela s’effectue en ancrant les objets dans ce monde.

![Illustration animée dans ARKit](urhosharp-images/image1.gif)

Nous allons utiliser la bibliothèque Urho pour charger nos ressources en 3D et les placer dans le monde entier. nous utiliserons ARKit pour recevoir le flux vidéo provenant de l’appareil photo, ainsi que l’emplacement du téléphone dans le monde.   Lorsque l’utilisateur se déplace avec son téléphone, nous utilisons les modifications de l’emplacement pour mettre à jour le système de coordonnées affiché par le moteur Urho.

De cette façon, lorsque vous placez un objet dans l’espace 3D et que l’utilisateur se déplace, l’emplacement de l’objet 3D reflète le lieu et l’emplacement où il a été placé.

## <a name="setting-up-your-application"></a>Configuration de votre application

### <a name="ios-application-launch"></a>Lancement de l’application iOS

Votre application iOS doit créer et lancer votre contenu 3D. pour cela, vous devez créer un qui implémente une sous- `Urho.Application` classe du et fournir votre code d’installation en `Start` remplaçant la méthode.  C’est là que votre scène est remplie avec des données, que les gestionnaires d’événements sont configurés, et ainsi de suite.

Nous avons introduit une `Urho.ArkitApp` classe qui sous- `Urho.Application` classe et sur sa `Start` méthode fait le gros du levage.   Tout ce que vous devez faire pour votre application Urho existante est de changer la classe de base en `Urho.ArkitApp` type et vous avez une application qui exécutera votre scène Urho dans le monde entier.

### <a name="the-arkitapp-class"></a>La classe ArkitApp

Cette classe fournit un ensemble de valeurs par défaut pratiques, à la fois une scène avec certains objets clés et le traitement des événements ARKit tels qu’ils sont fournis par le système d’exploitation.

L’installation a lieu dans la `Start` méthode virtuelle.   Lorsque vous substituez cette méthode sur votre sous-classe, vous devez vous assurer de chaîner à votre parent en `base.Start()` utilisant sur votre propre implémentation.

La `Start` méthode Configure la scène, la fenêtre d’affichage, l’appareil photo et un éclairage directionnel, et les fait apparaître en tant que propriétés publiques :

- `Scene` pour stocker vos objets,
- directionnel `Light` avec des ombres et dont l’emplacement est disponible via `LightNode` la propriété
- `Camera` dont les composants sont mis à jour quand ARKit fournit une mise à jour de l’application et
- `ViewPort` affichant les résultats.

### <a name="your-code"></a>Votre code

Vous devez ensuite sous-définir la `ArkitApp` classe et substituer la `Start` méthode.   La première chose que votre méthode doit faire est d' `ArkitApp.Start` enchaîner au en appelant. `base.Start()`  Après cela, vous pouvez utiliser l’une des propriétés du programme d’installation de ArkitApp pour ajouter vos objets à la scène, personnaliser les lumières, les ombres ou les événements que vous souhaitez gérer.

L’exemple ARKit/UrhoSharp charge un caractère animé avec des textures et lit l’animation, avec l’implémentation suivante :

```csharp
public class MutantDemo : ArkitApp
{
    [Preserve]
    public MutantDemo(ApplicationOptions opts) : base(opts) { }

    Node mutantNode;

    protected override void Start()
    {
        base.Start ();

        // Mutant
        mutantNode = Scene.CreateChild();
        mutantNode.Rotation = new Quaternion(x: 0, y:15, z:0);
        mutantNode.Position = new Vector3(0, -1f, 2f); /*two meters away*/
        mutantNode.SetScale(0.5f);

        var mutant = mutantNode.CreateComponent<AnimatedModel>();
        mutant.Model = ResourceCache.GetModel("Models/Mutant.mdl");
        mutant.Material = ResourceCache.GetMaterial("Materials/mutant_M.xml");

        var animation = mutantNode.CreateComponent<AnimationController>();
        animation.Play("Animations/Mutant_HipHop1.ani", 0, true, 0.2f);
    }
}
```

Et c’est tout ce que vous devez faire à ce stade pour que votre contenu 3D soit affiché en réalité augmentée.

Urho utilise des formats personnalisés pour les animations et les modèles 3D. vous devez donc exporter vos ressources dans ce format.   Vous pouvez utiliser des outils tels que le [complément Urho3D Blender](https://github.com/reattiva/Urho3D-Blender) et [UrhoAssetImporter](https://github.com/EgorBo/UrhoAssetImporter) qui peuvent convertir ces ressources à partir de formats populaires comme dbx, DAE, obj, Blend, 3D-Max au format requis par Urho.

Pour en savoir plus sur la création d’applications 3D à l’aide de Urho, consultez le guide [Introduction à UrhoSharp](~/graphics-games/urhosharp/introduction.md) .

## <a name="arkitapp-in-depth"></a>ArkitApp en profondeur

> [!NOTE]
> Cette section s’adresse aux développeurs qui souhaitent personnaliser l’expérience par défaut de UrhoSharp et ARKit ou souhaitent avoir un aperçu approfondi du fonctionnement de l’intégration.   Il n’est pas nécessaire de lire cette section.

L’API ARKit est assez simple, mais vous créez et configurez un objet [ARSession](https://developer.apple.com/documentation/arkit/arsession) qui démarre ensuite la diffusion d’objets [ARFrame](https://developer.apple.com/documentation/arkit/arframe) .   Elles contiennent à la fois l’image capturée par l’appareil photo et la position réelle estimée de l’appareil.

Nous allons composer les images fournies par la caméra avec notre contenu 3D et ajuster l’appareil photo dans UrhoSharp pour qu’il corresponde à l’emplacement et à la position de l’appareil.

Le diagramme suivant montre ce qui se produit dans la `ArkitApp` classe :

[![Diagramme des classes et des écrans dans le ArkitApp](urhosharp-images/image2.png)](urhosharp-images/image2.png#lightbox)

### <a name="rendering-the-frames"></a>Rendu des frames

L’idée est simple et combinez la vidéo de l’appareil photo avec nos graphiques 3D pour produire l’image combinée.     Nous recevrons une série de ces images capturées en séquence, et nous allons mélanger cette entrée à la scène Urho.

La méthode la plus simple consiste à insérer un `RenderPathCommand` dans le principal. `RenderPath`  Il s’agit d’un ensemble de commandes qui sont effectuées pour dessiner une seule image.  Cette commande remplit la fenêtre d’affichage avec les textures que nous lui transmettons.    Nous l’avons configurée sur la première image qui est en cours de traitement et la définition réelle est effectuée dans le fichier **ARRenderPath. xml** qui est chargé à ce stade.

Toutefois, nous sommes confrontés à deux problèmes pour combiner ces deux mondes :


1. Sur iOS, les textures GPU doivent avoir une résolution qui est une puissance de deux, mais les trames que nous obtenons de l’appareil photo n’ont pas de résolution qui sont une puissance de deux, par exemple : 1280 x 720.
2. Les frames sont encodés au format [YUV](https://en.wikipedia.org/wiki/YUV) , représenté par deux images : luminance et chrominance.

Les trames YUV présentent deux résolutions différentes.  une image 1280 x 720 représentant la luminance (fondamentalement une image de mise à l’échelle grise) et des 640 x 360 de plus petite taille pour le composant de chrominance :

![Image illustrant la combinaison des composants Y et UV](urhosharp-images/image3.png)


Pour dessiner une image de couleur entière à l’aide d’OpenGL ES, nous devons écrire un petit nuanceur qui prend la luminance (composant Y) et la chrominance (plans UV) à partir des emplacements de texture.  Dans UrhoSharp, ils portent les noms « sDiffMap » et « sNormalMap » et les convertissent au format RVB :

```csharp
mat4 ycbcrToRGBTransform = mat4(
    vec4(+1.0000, +1.0000, +1.0000, +0.0000),
    vec4(+0.0000, -0.3441, +1.7720, +0.0000),
    vec4(+1.4020, -0.7141, +0.0000, +0.0000),
    vec4(-0.7010, +0.5291, -0.8860, +1.0000));

vec4 ycbcr = vec4(texture2D(sDiffMap, vTexCoord).r,
                    texture2D(sNormalMap, vTexCoord).ra, 1.0);
gl_FragColor = ycbcrToRGBTransform * ycbcr;
```

Pour afficher la texture qui n’a pas de puissance de deux résolutions, nous devons définir Texture2D avec les paramètres suivants :

```chsarp
// texture for UV-plane;
cameraUVtexture = new Texture2D();
cameraUVtexture.SetNumLevels(1);
cameraUVtexture.SetSize(640, 360, Graphics.LuminanceAlphaFormat, TextureUsage.Dynamic);
cameraUVtexture.FilterMode = TextureFilterMode.Bilinear;
cameraUVtexture.SetAddressMode(TextureCoordinate.U, TextureAddressMode.Clamp);
cameraUVtexture.SetAddressMode(TextureCoordinate.V, TextureAddressMode.Clamp);
```

Nous sommes donc en mesure de restituer les images capturées en tant qu’arrière-plan et de rendre une scène au-dessus de ce type mutant.

### <a name="adjusting-the-camera"></a>Réglage de l’appareil photo

Les `ARFrame` objets contiennent également la position estimée de l’appareil.  Nous devons maintenant déplacer l’appareil photo de jeu ARFrame-Before ARKit il n’était pas important de suivre l’orientation des appareils (Roll, tangage et lacet) et d’afficher un hologramme épinglé en plus de la vidéo, mais si vous déplacez votre appareil, les hologrammes seront déplacés.

Cela se produit parce que les capteurs intégrés tels que les gyroscopes ne sont pas en mesure de suivre les mouvements, ils peuvent uniquement s’accélérer.  ARKit analyse chaque cadre et extrait les points de fonctionnalité à suivre et peut donc nous fournir une matrice de transformation exacte contenant les données de mouvement et de rotation.

Par exemple, voici comment nous pouvons obtenir la position actuelle :

```csharp
var row = arCamera.Transform.Row3;
CameraNode.Position = new Vector3(row.X, row.Y, -row.Z);
```

Nous utilisons `-row.Z` car ARKit utilise un système de coordonnées droitier.


### <a name="plane-detection"></a>Détection de plan

ARKit est en mesure de détecter les plans horizontaux et cette capacité vous permet d’interagir avec le monde réel, par exemple, nous pouvons placer le mutant sur une table réelle ou un étage. La façon la plus simple de procéder consiste à utiliser la méthode HitTest (Raycasting). Il convertit les coordonnées d’écran (0.5 ; 0.5 est le centre) en coordonnées réelles (0 ; 0 ; 0 est l’emplacement du premier frame).

```chsarp
protected Vector3? HitTest(float screenX = 0.5f, float screenY = 0.5f)
{
    var result = ARSession.CurrentFrame.HitTest(new CGPoint(screenX, screenY),
        ARHitTestResultType.ExistingPlaneUsingExtent)?.FirstOrDefault();
    if (result != null)
    {
        var row = result.WorldTransform.Row3;
        return new Vector3(row.X, row.Y, -row.Z);
    }
    return null;
}
```

Nous pouvons maintenant placer le mutant sur une surface horizontale en fonction de l’emplacement de l’écran de l’appareil que vous appuyez :

```chsarp
void OnTouchEnd(TouchEndEventArgs e)
{
    float x = e.X / (float)Graphics.Width;
    float y = e.Y / (float)Graphics.Height;
    var pos = HitTest(x, y);
    if (pos != null)
    mutantNode.Position = pos.Value;
}
```

![Figures animées modification des plans en fonction des déplacements de vue](urhosharp-images/image4.gif)

### <a name="realistic-lighting"></a>Éclairage réaliste

En fonction des conditions d’éclairage réelles, la scène virtuelle doit être plus claire ou plus sombre pour mieux correspondre à son environnement. ARFrame contient une propriété LightEstimate que nous pouvons utiliser pour ajuster la lumière ambiante Urho. cela s’effectue comme suit :

```csharp
var ambientIntensity = (float) frame.LightEstimate.AmbientIntensity / 1000f;
var zone = Scene.GetComponent<Zone>();
zone.AmbientColor = Color.White * ambientIntensity;
```

### <a name="beyond-ios---hololens"></a>Au-delà d’iOS-HoloLens

UrhoSharp [s’exécute sur tous les principaux systèmes d’exploitation](~/graphics-games/urhosharp/platform/index.md), ce qui vous permet de réutiliser votre code existant ailleurs.

HoloLens est l’une des plateformes les plus passionnantes sur lesquelles il s’exécute.   Cela signifie que vous pouvez facilement basculer entre iOS et HoloLens pour créer des applications de réalité plus enrichies à l’aide de UrhoSharp.

Vous pouvez trouver la source MutantDemo sur [github.com/EgorBo/ARKitXamarinDemo](https://github.com/EgorBo/ARKitXamarinDemo).


## <a name="related-links"></a>Liens associés

- [UrhoSharp](~/graphics-games/urhosharp/index.md)
- [ARKitXamarinDemo (avec UrhoSharp) (exemple)](https://github.com/EgorBo/ARKitXamarinDemo)
