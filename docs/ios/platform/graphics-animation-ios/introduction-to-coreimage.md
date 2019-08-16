---
title: Image principale dans Xamarin. iOS
description: Core image est une nouvelle infrastructure introduite avec iOS 5 pour fournir une fonctionnalité de traitement d’image et d’amélioration vidéo en direct. Cet article présente ces fonctionnalités avec les exemples Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 91E0780B-FF8A-E70D-9CD4-419119612B2D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 6960fe3db1ddf7d6d911fe8151e49b1a42388d26
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527854"
---
# <a name="core-image-in-xamarinios"></a>Image principale dans Xamarin. iOS

_Core image est une nouvelle infrastructure introduite avec iOS 5 pour fournir une fonctionnalité de traitement d’image et d’amélioration vidéo en direct. Cet article présente ces fonctionnalités avec les exemples Xamarin. iOS._

L’image principale est une nouvelle infrastructure introduite dans iOS 5, qui fournit un certain nombre de filtres et d’effets intégrés à appliquer aux images et aux vidéos, y compris la détection des visages.

Ce document contient des exemples simples de:

- Détection de visage.
- Application de filtres à une image
- Liste des filtres disponibles.


Ces exemples doivent vous aider à commencer à incorporer des fonctionnalités d’image principales dans vos applications Xamarin. iOS.

## <a name="requirements"></a>Configuration requise

Vous devez utiliser la dernière version de Xcode.

## <a name="face-detection"></a>Détection de visage

La fonctionnalité de détection de visage d’image principale fait exactement ce qu’elle dit: elle tente d’identifier des visages dans une photo et retourne les coordonnées des visages qu’elle reconnaît. Ces informations peuvent être utilisées pour compter le nombre de personnes dans une image, dessiner des indicateurs sur l’image (par exemple, pour «baliser» des personnes dans une photographie, ou tout autre point que vous pouvez considérer.

Ce code de CoreImage\SampleCode.cs montre comment créer et utiliser la détection de visage sur une image incorporée:

```csharp
var image = new UIImage("photoFace.JPG");
var context = CIContext.FromOptions(null);
var detector = CIDetector.CreateFaceDetector (context, true);
var ciImage = CIImage.FromCGImage(image.CGImage);
CIFeature[] features = detector.FeaturesInImage(ciImage);
```

Le tableau de fonctionnalités est rempli avec `CIFaceFeature` des objets (si des visages ont été détectés). Il existe un `CIFaceFeature` pour chaque visage. `CIFaceFeature`a les propriétés suivantes:

- HasMouthPosition: indique si une bouche a été détectée pour ce visage.
- HasLeftEyePosition: indique si l’œil gauche a été détecté pour cette face.
- HasRightEyePosition: indique si l’œil droit a été détecté pour cette face. 
- MouthPosition: coordonnées de l’embouchure pour ce visage.
- LeftEyePosition: coordonnées de l’œil gauche de cette face.
- RightEyePosition: coordonnées de l’œil droit de cette face.


Les coordonnées de toutes ces propriétés ont leur origine dans le coin inférieur gauche, contrairement à UIKit qui utilise l’angle supérieur gauche comme origine. Lorsque vous utilisez les coordonnées `CIFaceFeature` , veillez à les «retourner». Cette vue d’image personnalisée de base dans CoreImage\CoreImageViewController.cs montre comment dessiner des triangles «indicateur de visage» sur l’image ( `FlipForBottomOrigin` Notez la méthode):

```csharp
public class FaceDetectImageView : UIView
{
    public Xamarin.iOS.CoreImage.CIFeature[] Features;
    public UIImage Image;
    public FaceDetectImageView (RectangleF rect) : base(rect) {}
    CGPath path;
    public override void Draw (RectangleF rect) {
        base.Draw (rect);
        if (Image != null)
            Image.Draw(rect);

        using (var context = UIGraphics.GetCurrentContext()) {
            context.SetLineWidth(4);
            UIColor.Red.SetStroke ();
            UIColor.Clear.SetFill ();
            if (Features != null) {
                foreach (var feature in Features) { // for each face
                    var facefeature = (CIFaceFeature)feature;
                    path = new CGPath ();
                    path.AddLines(new PointF[]{ // assumes all 3 features found
                        FlipForBottomOrigin(facefeature.LeftEyePosition, 200),
                        FlipForBottomOrigin(facefeature.RightEyePosition, 200),
                        FlipForBottomOrigin(facefeature.MouthPosition, 200)
                    });
                    path.CloseSubpath();
                    context.AddPath(path);
                    context.DrawPath(CGPathDrawingMode.FillStroke);
                }
            }
        }
    }
    /// <summary>
    /// Face recognition coordinates have their origin in the bottom-left
    /// but we are drawing with the origin in the top-left, so "flip" the point
    /// </summary>
    PointF FlipForBottomOrigin (PointF point, int height)
    {
        return new PointF(point.X, height - point.Y);
    }
}
```

Ensuite, dans le fichier SampleCode.cs, l’image et les fonctionnalités sont affectées avant le redessin de l’image:

```csharp
faceView.Image = image;
faceView.Features = features;
faceView.SetNeedsDisplay();
```

La capture d’écran montre l’exemple de sortie: l’emplacement des fonctionnalités du visage détectées est affiché dans un UITextView et dessiné sur l’image source à l’aide de CoreGraphics.

En raison de la façon dont la reconnaissance faciale fonctionne, elle peut parfois détecter des choses en dehors des visages humains (comme les singes de jouet!).

## <a name="filters"></a>Filtres

Il existe plus de 50 filtres intégrés différents, et l’infrastructure est extensible afin que de nouveaux filtres puissent être implémentés.

## <a name="using-filters"></a>Utilisation de filtres

L’application d’un filtre à une image se compose de quatre étapes distinctes: le chargement de l’image, la création du filtre, l’application du filtre et l’enregistrement (ou l’affichage) du résultat.

Tout d’abord, chargez une `CIImage` image dans un objet.

```csharp
var uiimage = UIImage.FromFile ("photo.JPG");
var ciimage = new CIImage (uiimage);
```

Ensuite, créez la classe de filtre et définissez ses propriétés.

```csharp
var sepia = new CISepiaTone();
sepia.Image = ciimage;
sepia.Intensity = 0.8f;
```

Troisièmement, accédez `OutputImage` à la propriété et `CreateCGImage` appelez la méthode pour restituer le résultat final.

```csharp
CIImage output = sepia.OutputImage;
var context = CIContext.FromOptions(null);
var cgimage = context.CreateCGImage (output, output.Extent);
```

Enfin, assignez l’image à une vue pour afficher le résultat. Dans une application réelle, l’image obtenue peut être enregistrée dans le système de fichiers, l’album photo, un tweet ou un e-mail.

```csharp
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

Ces captures d’écran montrent le résultat `CISepia` des `CIHueAdjust` filtres et qui sont illustrés dans l’exemple de code CoreImage. zip.

Pour obtenir un exemple de `CIColorControls` filtre, consultez ajuster le [contrat et la luminosité d’une recette d’image](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image) .

```csharp
var uiimage = UIImage.FromFile("photo.JPG");
var ciimage = new CIImage(uiimage);
var hueAdjust = new CIHueAdjust();   // first filter
hueAdjust.Image = ciimage;
hueAdjust.Angle = 2.094f;
var sepia = new CISepiaTone();       // second filter
sepia.Image = hueAdjust.OutputImage; // output from last filter, input to this one
sepia.Intensity = 0.3f;
CIFilter color = new CIColorControls() { // third filter
    Saturation = 2,
    Brightness = 1,
    Contrast = 3,
    Image = sepia.OutputImage    // output from last filter, input to this one
};
var output = color.OutputImage;
var context = CIContext.FromOptions(null);
// ONLY when CreateCGImage is called do all the effects get rendered
var cgimage = context.CreateCGImage (output, output.Extent);
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

```csharp
var context = CIContext.FromOptions (null);
```

```csharp
var context = CIContext.FromOptions(new CIContextOptions() {
    UseSoftwareRenderer = true  // CPU
});
var cgimage = context.CreateCGImage (output, output.Extent);
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

### <a name="listing-filters-and-their-properties"></a>Liste des filtres et de leurs propriétés

Ce code de CoreImage\SampleCode.cs génère la liste complète des filtres intégrés et de leurs paramètres.

```csharp
var filters = CIFilter.FilterNamesInCategories(new string[0]);
foreach (var filter in filters){
   display.Text += filter +"\n";
   var f = CIFilter.FromName (filter);
   foreach (var key in f.InputKeys){
     var attributes = (NSDictionary)f.Attributes[new NSString(key)];
     var attributeClass = attributes[new NSString("CIAttributeClass")];
     display.Text += "   " + key;
     display.Text += "   " + attributeClass + "\n";
   }
}
```

La [référence de la classe CIFilter](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.html) décrit les filtres intégrés 50 et leurs propriétés. À l’aide du code ci-dessus, vous pouvez interroger les classes de filtre, y compris les valeurs par défaut des paramètres et les valeurs maximales et minimales autorisées (qui peuvent être utilisées pour valider les entrées avant d’appliquer un filtre).

La sortie de la liste Catégories ressemble à ceci sur le simulateur: vous pouvez faire défiler la liste pour afficher tous les filtres et leurs paramètres.

 [![](introduction-to-coreimage-images/coreimage05.png "La sortie de la liste des catégories ressemble à ceci sur le simulateur")](introduction-to-coreimage-images/coreimage05.png#lightbox)

Chaque filtre répertorié est exposé en tant que classe dans Xamarin. iOS. vous pouvez donc également explorer l’API Xamarin. iOS. CoreImage dans le navigateur d’assembly ou utiliser la saisie semi-automatique dans Visual Studio pour Mac ou Visual Studio. 

## <a name="summary"></a>Récapitulatif

Cet article a montré comment utiliser certaines des nouvelles fonctionnalités d’infrastructure d’images principales iOS 5, telles que la détection de visages et l’application de filtres à une image. Des dizaines de filtres d’image différents sont disponibles dans l’infrastructure que vous pouvez utiliser.

## <a name="related-links"></a>Liens associés

- [Image principale (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/coreimage)
- [Ajuster le contrat et la luminosité d’une recette d’image](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image)
- [Utilisation de filtres d’image principaux](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Conceptual/CoreImaging/ci_tasks/ci_tasks.html)
- [Référence de la classe CIFilter](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.htm)
