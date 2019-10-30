---
title: Couleurs larges dans Xamarin. iOS
description: Ce document présente les couleurs larges et comment elles peuvent être utilisées dans une application Xamarin. iOS ou Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 576E978A-F182-489A-83E4-D8CDC6890B24
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: e7240a271de1f0199c2c9fc045f5c95745eb98c5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031247"
---
# <a name="wide-color-in-xamarinios"></a>Couleurs larges dans Xamarin. iOS

_Cet article couvre la largeur et la façon dont il peut être utilisé dans une application Xamarin. iOS ou Xamarin. Mac._

iOS 10 et macOS Sierra améliorent la prise en charge des formats de pixel étendus et des espaces de couleurs à grande échelle dans le système, y compris les infrastructures telles que Core Graphics, Core image, Metal et AVFoundation. La prise en charge des appareils avec des affichages de couleurs larges est encore plus facilitée en fournissant ce comportement dans l’ensemble de la pile graphique.

## <a name="asset-catalogs"></a>Catalogues de composants

### <a name="supporting-wide-color-with-asset-catalogs"></a>Prise en charge des couleurs larges avec les catalogues de ressources

Dans iOS 10 et macOS Sierra, Apple a développé les catalogues de ressources utilisés pour inclure et catégoriser le contenu des images statiques dans l’offre groupée de l’application afin de prendre en charge une large gamme de couleurs.

L’utilisation des catalogues de ressources offre les avantages suivants à une application :

- Ils fournissent la meilleure option de déploiement pour les ressources d’images statiques.
- Ils prennent en charge la correction automatique des couleurs.
- Ils prennent en charge l’optimisation automatique du format pixel.
- Ils prennent en charge le découpage des applications et l’affinage des applications, ce qui garantit que seul le contenu pertinent est remis à l’appareil de l’utilisateur final.

Apple a apporté les améliorations suivantes aux catalogues de ressources pour la prise en charge des couleurs larges :

- Ils prennent en charge le contenu source 16 bits (par canal de couleur).
- Ils prennent en charge le catalogage du contenu par affichage de la gamme. Le contenu peut être balisé pour les gammes de couleurs sRVB ou d’affichage P3.

Le développeur a trois options pour la prise en charge d’un contenu à couleurs larges dans ses applications :

1. **Ne rien faire** : étant donné que le contenu à couleurs larges doit uniquement être utilisé dans les situations où l’application doit afficher des couleurs vives (où elles améliorent l’expérience de l’utilisateur), le contenu en dehors de cette exigence doit être laissé tel quel. Il continuera à être rendu correctement dans toutes les situations matérielles.
2. **Mettre à niveau le contenu existant pour afficher P3** : le développeur doit remplacer le contenu de l’image existante dans son catalogue de ressources par un nouveau fichier mis à niveau au format P3 et le baliser comme tel dans l’éditeur de ressources. Au moment de la génération, une image dérivée de sRGB sera générée à partir de ces ressources.
3. **Fournir un contenu de ressource optimisé** : dans ce cas, le développeur fournira à la fois un sRGB 8 bits et une vision de 16 bits pour l’affichage de chaque image dans le catalogue de ressources (correctement marquée dans l’éditeur de ressources).

### <a name="asset-catalog-deployment"></a>Déploiement du catalogue de ressources

Voici ce qui se produit lorsque le développeur soumet une application à l’App Store avec des catalogues de ressources incluant du contenu d’image en couleurs larges :

- Lorsque l’application est déployée sur l’utilisateur final, le découpage de l’application permet de garantir que seule la variante de contenu appropriée est remise à l’appareil de l’utilisateur.
- Sur les appareils qui ne prennent pas en charge les couleurs larges, il n’y a aucun coût de charge utile pour l’ajout de contenu à couleurs larges, car il n’est jamais expédié à l’appareil.
- `NSImage` sur macOS Sierra (et versions ultérieures) sélectionne automatiquement la meilleure représentation du contenu pour l’affichage du matériel.
- Le contenu affiché est actualisé automatiquement quand ou si les caractéristiques de l’affichage du matériel des appareils changent.

### <a name="asset-catalog-storage"></a>Stockage du catalogue de ressources

Le stockage du catalogue de ressources présente les propriétés et les implications suivantes pour une application :

- Au moment de la génération, Apple tente d’optimiser le stockage du contenu de l’image via des conversions d’images de haute qualité.
- 16 bits sont utilisés par canal de couleur pour le contenu de couleurs larges.
- La compression d’images dépendantes du contenu est utilisée pour réduire les tailles de contenu livrable. De nouvelles compressions « avec perte » ont été ajoutées pour optimiser davantage la taille du contenu.

## <a name="rendering-off-screen-images-in-app"></a>Rendu d’images hors écran dans l’application

En fonction du type d’application créé, il peut permettre à l’utilisateur d’inclure du contenu d’image qu’il a collecté sur Internet ou de créer du contenu d’image directement à l’intérieur de l’application (comme une application de dessin vectoriel, par exemple).

Dans ces deux cas, l’application peut restituer l’image requise hors écran en couleurs larges à l’aide des fonctionnalités améliorées ajoutées à iOS et macOS.

### <a name="drawing-wide-color-in-ios"></a>Dessin de couleurs larges dans iOS

Avant de savoir comment dessiner correctement une image de couleur étendue dans iOS 10, jetez un coup d’œil au code de dessin iOS courant suivant :

```csharp
public UIImage DrawWideColorImage ()
{
    var size = new CGSize (250, 250);
    UIGraphics.BeginImageContext (size);

    ...

    UIGraphics.EndImageContext ();
    return image = UIGraphics.GetImageFromCurrentImageContext ();
    }
```

Il y a des problèmes au niveau du code standard qui devront être traités _avant_ de pouvoir être utilisés pour dessiner une image de couleur étendue. La méthode `UIGraphics.BeginImageContext (size)` utilisée pour démarrer le dessin d’images iOS présente les limitations suivantes :

- Il ne peut pas créer de contextes d’image avec plus de 8 bits par canal de couleur.
- Il ne peut pas représenter des couleurs dans l’espace de couleurs sRVB de la plage étendue.
- Il n’a pas la possibilité de fournir une interface pour créer des contextes dans un espace de couleurs non sRVB en raison de l’appel des routines C de bas niveau en arrière-plan.

Pour gérer ces limitations et dessiner une image de couleur étendue dans iOS 10, utilisez le code suivant à la place :

```csharp
public UIImage DrawWideColorImage ()
{
    var size = new CGSize (250, 250);
    var render = new UIGraphicsImageRenderer (size);

    var image = render.CreateImage ((UIGraphicsImageRendererContext context) => {
        var bounds = context.Format.Bounds;
        CGRect slice;
        CGRect remainder;
        bounds.Divide (bounds.Width / 2, CGRectEdge.MinXEdge, out slice, out remainder);

        var redP3 = UIColor.FromDisplayP3 (1.0F, 0.0F, 0.0F, 1.0F);
        redP3.SetColor ();
        context.FillRect (slice);

        var redRGB = UIColor.Red;
        redRGB.SetColor ();
        context.FillRect (remainder);
    });

    // Return results
    return image;
}
```

La nouvelle classe `UIGraphicsImageRenderer` crée un nouveau contexte d’image qui est capable de gérer l’espace de couleurs sRVB de la plage étendue et présente les fonctionnalités suivantes :

- Par défaut, elle est entièrement gérée par la couleur.
- Par défaut, il prend en charge l’espace de couleurs sRVB de la plage étendue.
- Il détermine intelligemment s’il doit s’afficher dans l’espace de couleurs sRGB ou de plage étendue, en fonction des capacités du périphérique iOS sur lequel l’application s’exécute.
- Il gère entièrement et automatiquement la durée de vie du contexte d’image (`CGContext`), de sorte que le développeur n’a pas à se soucier de l’appel des commandes de contexte Begin et end.
- Elle est compatible avec la méthode `UIGraphics.GetCurrentContext()`.

La méthode `CreateImage` de la classe `UIGraphicsImageRenderer` est appelée pour créer une image de couleur étendue et passé un gestionnaire d’achèvement avec le contexte d’image dans lequel dessiner. Tout le dessin est effectué à l’intérieur de ce gestionnaire d’achèvement comme suit :

- La méthode `UIColor.FromDisplayP3` crée une nouvelle couleur rouge entièrement saturée dans la gamme large qui affiche l’espace de couleurs P3 et est utilisée pour dessiner la première moitié du rectangle. 
- La deuxième moitié du rectangle est dessinée dans la couleur rouge entièrement saturée de couleurs RVB pour la comparaison.

### <a name="drawing-wide-color-in-macos"></a>Dessin de couleurs larges dans macOS

La classe `NSImage` a été développée en macOS Sierra pour prendre en charge le dessin d’images de couleur large. Exemple :

```csharp
var size = CGSize(250,250);
var wideColorImage = new NSImage(size, false, (drawRect) =>{
    var rects = drawRect.Divide(drawRect.Size.Width/2,CGRect.MinXEdge);

    var color = new NSColor(1.0, 0.0, 0.0, 1.0).Set();
    var path = new NSBezierPath(rects.Slice).Fill();

    color = new NSColor(1.0, 0.0, 0.0, 1.0).Set();
    path = new NSBezierPath(rects.Remainder).Fill();

    // Return modified
    return true;
});
```

## <a name="rendering-on-screen-images-in-app"></a>Rendu d’images à l’écran dans l’application

Pour afficher des images de couleur larges à l’écran, le processus fonctionne de la même façon que pour les images en couleurs larges hors écran pour macOS et iOS présentées ci-dessus.

### <a name="rendering-on-screen-in-ios"></a>Affichage à l’écran dans iOS

Lorsque l’application doit restituer une image en couleurs larges à l’écran dans iOS, remplacez la méthode `Draw` du `UIView` en question comme d’habitude. Exemple :

```csharp
using System;
using UIKit;
using CoreGraphics;

namespace MonkeyTalk
{
    public class MonkeyView : UIView
    {
        public MonkeyView ()
        {
        }

        public override void Draw (CGRect rect)
        {
            base.Draw (rect);

            // Draw the image to screen
        ...
        }
    }
}
```

Comme iOS 10 utilise la classe `UIGraphicsImageRenderer` présentée ci-dessus, il détermine intelligemment s’il doit s’afficher dans l’espace de couleurs sRVB de la plage étendue ou sRVB en fonction des capacités du périphérique iOS sur lequel l’application s’exécute lors de l’appel de la méthode `Draw`. En outre, la `UIImageView` a été gérée par la couleur depuis iOS 9,3 également.

Si l’application doit savoir comment le rendu est effectué sur un `UIView` ou `UIViewController`, elle peut vérifier la nouvelle propriété `DisplayGamut` de la classe `UITraitCollection`. Cette valeur sera un `UIDisplayGamut` énumération des éléments suivants :

- P3
- SRVB
- Non spécifié

Si l’application souhaite contrôler l’espace colorimétrique utilisé pour dessiner une image, elle peut utiliser une nouvelle propriété `ContentsFormat` du `CALayer` pour spécifier l’espace de couleurs souhaité. Cette valeur peut être un `CAContentsFormat` énumération des éléments suivants :

- Gray8Uint
- Rgba16Float
- Rgba8Uint

### <a name="rendering-on-screen-in-macos"></a>Affichage à l’écran dans macOS

Lorsque l’application doit afficher une image en couleurs larges à l’écran dans macOS, remplacez la méthode `DrawRect` du `NSView` en question comme d’habitude. Exemple :

```csharp
using System;
using AppKit;
using CoreGraphics;
using CoreAnimation;

namespace MonkeyTalkMac
{
    public class MonkeyView : NSView
    {
        public MonkeyView ()
        {
        }

        public override void DrawRect (CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

            // Draw graphics into the view
            ...
        }
    }
}
```

Là encore, il décide s’il doit s’afficher dans l’espace de couleurs sRGB ou de plage étendue, en fonction des capacités du matériel Mac sur lequel l’application s’exécute lorsque la méthode `DrawRect` est appelée.

Si l’application souhaite contrôler l’espace colorimétrique utilisé pour dessiner une image, elle peut utiliser une nouvelle propriété `DepthLimit` de la classe `NSWindow` pour spécifier l’espace colorimétrique souhaité. Cette valeur peut être un `NSWindowDepth` énumération des éléments suivants :

- OneHundredTwentyEightBitRgb
- SixtyfourBitRgb
- TwentyfourBitRgb
