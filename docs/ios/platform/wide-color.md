---
title: Large couleur dans Xamarin.iOS
description: Ce document aborde large couleur et comment elle peut être utilisée dans une application Xamarin.iOS ou Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 576E978A-F182-489A-83E4-D8CDC6890B24
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 523aa1a97e1c536b5afbdb66c52f605382fa338d
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67268806"
---
# <a name="wide-color-in-xamarinios"></a>Large couleur dans Xamarin.iOS

_Cet article décrit la couleur de large et comment elle peut être utilisée dans une application Xamarin.iOS ou Xamarin.Mac._

iOS 10 et macOS Sierra améliore la prise en charge pour les formats de pixel de la plage étendue et des espaces d’une gamme de couleurs dans tout le système, y compris les infrastructures telles que graphismes de base, l’Image de Core, complète et AVFoundation. Prise en charge pour les appareils avec des couleurs large est davantage facilité en fournissant ce comportement dans toute la pile de graphique entière.

## <a name="asset-catalogs"></a>Catalogues de composants

### <a name="supporting-wide-color-with-asset-catalogs"></a>Large couleur avec les catalogues de composants de prise en charge

Dans iOS 10 et macOS Sierra, Apple a étendu les catalogues de composants utilisé pour inclure et classer les contenus d’image statique dans le bundle de l’application pour prendre en charge la large couleur.

À l’aide de catalogues de composants offrent les avantages suivants à une application :

- Ils fournissent la meilleure option de déploiement pour les ressources d’image statique.
- Ils prennent en charge automatiquement des couleurs.
- Ils prennent en charge l’optimisation de format de pixel automatique.
- Ils prennent en charge le découpage de l’application et la réduction de l’application qui garantit que seul le contenu qui est pertinente de get livré à l’appareil de l’utilisateur final.

Apple a fait les améliorations suivantes pour les catalogues de composants pour la prise en charge de la couleur large :

- Ils prennent en charge le contenu de la source de 16 bits (par canal de couleur).
- Ils prennent en charge contenu catalogage par gamme d’affichage. Contenu peut être marqué pour le sRVB ou gammes de Display P3.

Le développeur a trois options pour prendre en charge le contenu de large couleur dans leurs applications :

1. **Ne rien faire** -étant donné que le contenu de large couleur doit uniquement être utilisé dans les situations où l’application a besoin pour afficher les couleurs vives (où ils améliorera l’expérience utilisateur), le contenu en dehors de cette exigence doit être conservé en tant que-est. Il continue à s’afficher correctement dans toutes les situations de matériel.
2. **Mise à niveau de contenu existant vers Display P3** -cela nécessite au développeur de remplacer le contenu d’image existant dans le catalogue de leurs ressources avec un nouveau fichier mis à niveau dans le format de P3 et marquez-la en tant que tel dans l’éditeur de ressources. Au moment de la génération, une image dérivés sRVB sera générée à partir de ces ressources.
3. **Fournir optimisé le contenu de la ressource** -dans ce cas, le développeur fournira un sRVB de 8 bits et une vision de Display P3 16 bits de chaque image dans le catalogue de composants (avec balises correctement dans l’éditeur de ressources).

### <a name="asset-catalog-deployment"></a>Déploiement du catalogue Asset

Les éléments suivants seront produit lorsque le développeur soumet une application à l’App Store avec les catalogues de composants qui incluent le contenu de l’image large couleur :

- Quand l’application est déployée à l’utilisateur final, découpage de l’application garantit que la variante de contenu appropriée est remise à l’appareil de l’utilisateur.
- Sur les appareils qui ne prennent pas en charge les couleurs étendue, il n’existe aucun coût de la charge utile pour inclure le contenu de large couleur, comme il n’est jamais renvoyé à l’appareil.
- `NSImage` sur macOS Sierra (et versions ultérieures) sélectionne automatiquement la meilleure représentation contenue pour l’affichage de votre matériel.
- Le contenu affiché est actualisé automatiquement lorsqu’ou si le matériel des appareils affiche la modification de caractéristiques.

### <a name="asset-catalog-storage"></a>Stockage de catalogue Asset

Stockage de catalogue Asset a les propriétés et les implications d’une application suivantes :

- Au moment de la génération, Apple tente d’optimiser le stockage de contenu de l’image par le biais de la conversion des images haute qualité.
- 16-bits sont utilisés par le canal de couleur pour le contenu de large couleur.
- Contenu de compression d’image dépendante est utilisée pour réduire les tailles de contenu livrable. Nouveau compressions avec « pertes » ont été ajoutées pour optimiser davantage les tailles de contenu.

## <a name="rendering-off-screen-images-in-app"></a>Rendu des Images hors écran dans une application

Selon le type d’application en cours de création, elle peut autoriser l’utilisateur à inclure le contenu de l’image qu’ils ont collectées à partir d’internet ou créer le contenu de l’image directement à l’intérieur de l’application (par exemple, un vecteur d’application de dessin par exemple).

Dans ces deux cas, l’application peut rendre l’imagerie requise hors écran large couleur à l’aide des fonctionnalités améliorées ajoutées pour iOS et macOS.

### <a name="drawing-wide-color-in-ios"></a>Large couleur de dessin dans iOS

Avant d’aborder la façon de dessiner correctement une image large couleur dans iOS 10, examinons le code de dessin iOS courants suivants :

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

Il existe des problèmes avec le code standard qui devront être adressés _avant_ il peut être utilisé pour dessiner une image large couleur. Le `UIGraphics.BeginImageContext (size)` méthode utilisée pour démarrer le dessin d’image iOS présente les limitations suivantes :

- Il ne peut pas créer des contextes d’image avec plus de 8 bits par canal de couleur.
- Il ne peut pas représenter des couleurs dans la plage étendue sRVB espace colorimétrique.
- Il n’a pas la possibilité de fournir une interface pour créer des contextes dans un espace de couleurs sRVB non en raison des routines de C bas niveau qui est appelées en arrière-plan.

Pour gérer ces limitations et dessiner une image large couleur dans iOS 10, utilisez le code suivant à la place :

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

La nouvelle `UIGraphicsImageRenderer` classe crée un nouveau contexte de l’image qui est capable de gérer l’espace de couleurs de plage étendue sRVB et il présente les caractéristiques suivantes :

- Il est entièrement géré par défaut de couleur.
- Il prend en charge la plage étendue sRVB espace de couleurs par défaut.
- Il décide intelligemment si elle doit être rendu le sRVB ou une plage étendue sRVB espace colorimétrique basée sur les fonctionnalités de l’appareil iOS que l’application est en cours d’exécution.
- Il gère automatiquement et entièrement le contexte de l’image (`CGContext`) durée de vie pour le développeur n’a pas à vous soucier d’appel commencent et se terminent les commandes de contexte.
- Il est compatible avec le `UIGraphics.GetCurrentContext()` (méthode).

Le `CreateImage` méthode de la `UIGraphicsImageRenderer` classe est appelée pour créer une image de couleur large et passée avec le contexte de l’image à dessiner dans un gestionnaire d’achèvement. Tout le dessin est effectué à l’intérieur de ce gestionnaire d’achèvement comme suit :

- Le `UIColor.FromDisplayP3` méthode crée une nouvelle couleur rouge saturée dans la gamme large espace de couleurs Display P3 et il est utilisé pour dessiner la première moitié du rectangle. 
- La seconde moitié du rectangle est dessiné dans le sRVB normal entièrement saturé de couleur rouge pour la comparaison.

### <a name="drawing-wide-color-in-macos"></a>Large couleur de dessin dans macOS

Le `NSImage` classe a été développée dans macOS Sierra pour prendre en charge le dessin d’images de couleur large. Exemple :

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

## <a name="rendering-on-screen-images-in-app"></a>À l’écran de rendu d’Images dans l’application

Pour restituer des images couleur large à l’écran, le processus est similaire au dessin d’une image hors écran large couleur pour macOS et iOS présenté ci-dessus.

### <a name="rendering-on-screen-in-ios"></a>Rendu à l’écran dans iOS

Lorsque l’application doit afficher une image dans un large couleur à l’écran dans iOS, remplacer le `Draw` méthode de la `UIView` en question comme d’habitude. Exemple :

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

Comme le fait avec iOS 10 le `UIGraphicsImageRenderer` classe illustrée ci-dessus, il décide intelligemment s’il doit être rendu le sRVB ou une plage étendue sRVB espace colorimétrique basée sur les fonctionnalités de l’appareil iOS que l’application est en cours d’exécution sur le le `Draw` méthode est appelée. En outre, le `UIImageView` a été géré depuis iOS 9.3 de couleur.

Si l’application doit savoir comment rendu est effectué sur un `UIView` ou `UIViewController`, il peut vérifier le nouveau `DisplayGamut` propriété de la `UITraitCollection` classe. Cette valeur sera une `UIDisplayGamut` enum des opérations suivantes :

- P3
- SRVB
- Non spécifié

Si l’application souhaite de contrôle qui espace colorimétrique est utilisé pour dessiner une image, il peut utiliser un nouveau `ContentsFormat` propriété de la `CALayer` pour spécifier l’espace de couleurs souhaité. Cette valeur peut être un `CAContentsFormat` enum des opérations suivantes :

- Gray8Uint
- Rgba16Float
- Rgba8Uint

### <a name="rendering-on-screen-in-macos"></a>Rendu à l’écran dans macOS

Lorsque l’application doit afficher une image dans un large couleur à l’écran dans macOS, remplacer le `DrawRect` méthode de la `NSView` en question comme d’habitude. Exemple :

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

Là encore, il décide intelligemment s’il doit être rendu le sRVB ou une plage étendue sRVB espace de couleurs en fonction des capacités du matériel Mac que l’application est en cours d’exécution sur le le `DrawRect` méthode est appelée.

Si l’application souhaite de contrôle qui espace colorimétrique est utilisé pour dessiner une image, il peut utiliser un nouveau `DepthLimit` propriété de la `NSWindow` classe pour spécifier l’espace de couleurs souhaité. Cette valeur peut être un `NSWindowDepth` enum des opérations suivantes :

- OneHundredTwentyEightBitRgb
- SixtyfourBitRgb
- TwentyfourBitRgb
