---
title: Core Graphics dans Xamarin. iOS
description: Cet article présente les principales infrastructures iOS Graphics. Il montre comment utiliser des graphiques de base pour dessiner des fichiers de géométrie, des images et des fichiers PDF.
ms.prod: xamarin
ms.assetid: 4A30F480-0723-4B8A-9049-7CEB6211304A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: dac81a40983ea8414ec730f10c4c1f17e4d9915c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653813"
---
# <a name="core-graphics-in-xamarinios"></a>Core Graphics dans Xamarin. iOS

_Cet article présente les principales infrastructures iOS Graphics. Il montre comment utiliser des graphiques de base pour dessiner des fichiers de géométrie, des images et des fichiers PDF._

iOS comprend l’infrastructure [*graphique principale*](https://developer.apple.com/library/prerelease/ios/documentation/CoreGraphics/Reference/CoreGraphics_Framework/index.html) pour fournir un support de dessin de bas niveau. Ces infrastructures offrent des fonctionnalités graphiques riches dans UIKit. 

Core Graphics est un Framework graphique 2D de bas niveau qui permet de dessiner des graphiques indépendants des appareils. Tout dessin 2D dans UIKit utilise les graphiques de base en interne.

Core Graphics prend en charge le dessin dans plusieurs scénarios, notamment:

-  [Dessin à l’écran via un `UIView` ](#Drawing_in_a_UIView_Subclass) .
-  [Dessin d’images en mémoire ou à l’écran](#Drawing_Images_and_Text).
-  Création et dessin dans un fichier PDF.
-  Lecture et dessin d’un fichier PDF existant.


## <a name="geometric-space"></a>Espace géométrique

Quel que soit le scénario, tout dessin effectué avec des graphiques de base est effectué dans un espace géométrique, ce qui signifie qu’il fonctionne en points abstraits plutôt qu’en pixels. Vous décrivez ce que vous souhaitez dessiner en termes de géométrie et d’état de dessin, tels que les couleurs, les styles de ligne, etc. et les graphiques de base, qui traduisent tout en pixels. Cet État est ajouté à un contexte graphique, que vous pouvez considérer comme un canevas d’un peintre.

Cette approche présente quelques avantages:

-  Le dessin de code devient dynamique et peut ensuite modifier des graphiques au moment de l’exécution.
-  La réduction de la nécessité d’images statiques dans le bundle d’applications peut réduire la taille de l’application.
-  Les graphiques sont plus résistants aux modifications de résolution sur les appareils.

<a name="Drawing_in_a_UIView_Subclass"/>

## <a name="drawing-in-a-uiview-subclass"></a>Dessin dans une sous-classe UIView

Chaque `UIView` a un `Draw` méthode est appelée par le système lorsqu’il doit être dessiné. Pour ajouter du code de dessin à une vue, `UIView` une sous- `Draw`classe et une substitution:

```csharp
public class TriangleView : UIView
{
    public override void Draw (CGRect rect)
    {
        base.Draw (rect);
    }
}
```

Draw ne doit jamais être appelé directement. Elle est appelée par le système au cours du traitement de la boucle d’exécution. La première fois dans la boucle d’exécution après une vue est ajoutée à la hiérarchie d’affichage, son `Draw` méthode est appelée. Les appels suivants à `Draw` se produisent lorsque la vue est marquée comme devant être dessiné en appelant `SetNeedsDisplay` ou `SetNeedsDisplayInRect` sur la vue.

### <a name="pattern-for-graphics-code"></a>Modèle pour le code graphique

Le code de l' `Draw` implémentation doit décrire ce qu’il veut dessiner. Le code de dessin suit un modèle dans lequel il définit un état de dessin et appelle une méthode pour demander qu’il soit dessiné. Ce modèle peut être généralisé comme suit:

1. Obtenir un contexte graphique.

2. Configurez des attributs de dessin.

3. Créer une certaine géométrie à partir de primitives de dessin.

4. Appeler une méthode de dessin ou de trait.

### <a name="basic-drawing-example"></a>Exemple de dessin de base

Par exemple, considérez l’extrait de code suivant:

```csharp
//get graphics context
using (CGContext g = UIGraphics.GetCurrentContext ()) {
            
    //set up drawing attributes
    g.SetLineWidth (10);
    UIColor.Blue.SetFill ();
    UIColor.Red.SetStroke ();

    //create geometry
    var path = new CGPath ();

    path.AddLines (new CGPoint[]{
    new CGPoint (100, 200),
    new CGPoint (160, 100), 
    new CGPoint (220, 200)});

    path.CloseSubpath ();

    //add geometry to graphics context and draw it
    g.AddPath (path);       
    g.DrawPath (CGPathDrawingMode.FillStroke);
}
```

Décomposons ce code:

```csharp
using (CGContext g = UIGraphics.GetCurrentContext ()) {
...
}
```
Avec cette ligne, il obtient d’abord le contexte graphique actuel à utiliser pour le dessin. Vous pouvez considérer un contexte graphique comme la zone de dessin sur laquelle le dessin s’effectue, contenant tous les États sur le dessin, tels que les couleurs de trait et de remplissage, ainsi que la géométrie à dessiner.

```csharp
g.SetLineWidth (10);
UIColor.Blue.SetFill ();
UIColor.Red.SetStroke ();
``` 

Après avoir obtenu un contexte graphique, le code configure certains attributs à utiliser lors du dessin, comme indiqué ci-dessus. Dans ce cas, la largeur de ligne, les couleurs de trait et de remplissage sont définies. Tout dessin suivant utilisera ensuite ces attributs, car ils sont conservés dans l’état du contexte graphique.

Pour créer Geometry, le code `CGPath`utilise un, qui permet de décrire un tracé graphique à partir de lignes et de courbes. Dans ce cas, le chemin d’accès ajoute des lignes qui connectent un tableau de points pour créer un triangle. Comme indiqué ci-dessous, le graphique principal utilise un système de coordonnées pour le dessin de vue, où l’origine se trouve dans le coin supérieur gauche, avec un x-direct positif à droite et la direction y positive vers le bas:

```csharp
var path = new CGPath ();

path.AddLines (new CGPoint[]{
new CGPoint (100, 200),
new CGPoint (160, 100), 
new CGPoint (220, 200)});

path.CloseSubpath ();
``` 

Une fois le chemin d’accès créé, il est ajouté au contexte graphique afin que `AddPath` l' `DrawPath` appel de et puisse le dessiner.

La vue obtenue est illustrée ci-dessous:

 ![](core-graphics-images/00-bluetriangle.png "Le triangle de sortie de l’exemple")

## <a name="creating-gradient-fills"></a>Création de remplissages dégradés

Des formes de dessin plus riches sont également disponibles. Par exemple, les graphiques de base permettent de créer des remplissages dégradés et d’appliquer des tracés de découpage. Pour dessiner un remplissage dégradé à l’intérieur du tracé de l’exemple précédent, le chemin d’accès doit être défini en tant que chemin d’accès de découpage:

```csharp
// add the path back to the graphics context so that it is the current path
g.AddPath (path);
// set the current path to be the clipping path
g.Clip ();
```

La définition du chemin d’accès actuel en tant que chemin d’accès de découpage limite tous les dessins suivants dans la géométrie du tracé, par exemple le code suivant, qui dessine un dégradé linéaire:

```csharp
// the color space determines how Core Graphics interprets color information
    using (CGColorSpace rgb = CGColorSpace.CreateDeviceRGB()) {
        CGGradient gradient = new CGGradient (rgb, new CGColor[] {
        UIColor.Blue.CGColor,
        UIColor.Yellow.CGColor
    });

// draw a linear gradient
    g.DrawLinearGradient (
        gradient, 
        new CGPoint (path.BoundingBox.Left, path.BoundingBox.Top), 
        new CGPoint (path.BoundingBox.Right, path.BoundingBox.Bottom), 
        CGGradientDrawingOptions.DrawsBeforeStartLocation);
    }
```

Ces modifications produisent un remplissage dégradé comme indiqué ci-dessous:

 ![](core-graphics-images/01-gradient-fill.png "L’exemple avec un remplissage dégradé")

## <a name="modifying-line-patterns"></a>Modification des modèles de ligne

Les attributs de dessin des lignes peuvent également être modifiés à l’aide de graphiques principaux. Cela comprend la modification de la largeur et de la couleur du trait, ainsi que le modèle de ligne lui-même, comme indiqué dans le code suivant:

```csharp
//use a dashed line
g.SetLineDash (0, new nfloat[] { 10, 4 * (nfloat)Math.PI });
```

Si vous ajoutez ce code avant toute opération de dessin, les traits en pointillés ont une longueur de 10 unités, avec 4 unités d’espace entre les tirets, comme indiqué ci-dessous:

 ![](core-graphics-images/02-dashed-stroke.png "Ajout de ce code avant toute opération de dessin entraînant des traits en pointillés")
 
Notez que lorsque vous utilisez l’API unifiée dans Xamarin. iOS, le type de tableau doit être `nfloat`un et doit également être explicitement converti en math. pi.

<a name="Drawing_Images_and_Text"/>

## <a name="drawing-images-and-text"></a>Dessin d’images et de texte

En plus de dessiner des tracés dans le contexte graphique d’une vue, le graphique principal prend également en charge les images de dessin et le texte. Pour dessiner une image, créez simplement un `CGImage` et transmettez-le `DrawImage` à un appel:

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    using(CGContext g = UIGraphics.GetCurrentContext ()){
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
    }
}
```

Toutefois, cela produit une image dessinée à l’envers, comme indiqué ci-dessous:

 ![](core-graphics-images/03-upside-down-monkey.png "Image dessinée à l’envers")

La raison est l’origine des graphiques de base pour le dessin d’image est dans la partie inférieure gauche, tandis que la vue a son origine dans le coin supérieur gauche. Par conséquent, pour afficher correctement l’image, l’origine doit être modifiée, ce qui peut être effectué en modifiant la *matrice de transformation actuelle* *(CTM)* . Le CTM définit l’emplacement des points actifs, également appelés *espace utilisateur*. L’inversion du CTM dans la direction y et le décalage de la hauteur des limites dans l’axe y négatif peuvent retourner l’image.

Le contexte graphique contient des méthodes d’assistance pour transformer le CTM. Dans ce cas, `ScaleCTM` «retourne» le dessin et `TranslateCTM` le déplace vers l’angle supérieur gauche, comme indiqué ci-dessous:

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);
    
    using (CGContext g = UIGraphics.GetCurrentContext ()) {

        // scale and translate the CTM so the image appears upright
        g.ScaleCTM (1, -1);
        g.TranslateCTM (0, -Bounds.Height);
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
}   
```

L’image résultante est alors affichée verticalement:

 ![](core-graphics-images/04-upright-monkey.png "Image d’exemple affichée verticalement")

> [!IMPORTANT]
> Les modifications apportées au contexte graphique s’appliquent à toutes les opérations de dessin suivantes. Par conséquent, lorsque le CTM est transformé, il affecte tout dessin supplémentaire. Par exemple, si vous avez dessiné le triangle après la transformation CTM, il apparaît à l’envers.

### <a name="adding-text-to-the-image"></a>Ajout de texte à l’image

Comme pour les tracés et les images, le fait de dessiner du texte avec des graphiques principaux implique le même modèle de base pour la définition de certains États graphiques et l’appel d’une méthode pour dessiner. Dans le cas de texte, la méthode d’affichage du texte `ShowText`est. En cas d’ajout à l’exemple de dessin d’image, le code suivant dessine du texte à l’aide de graphiques principaux:

```csharp
public override void Draw (RectangleF rect)
{
    base.Draw (rect);
    
    // image drawing code omitted for brevity ...

    // translate the CTM by the font size so it displays on screen
    float fontSize = 35f;
    g.TranslateCTM (0, fontSize);

    // set general-purpose graphics state
    g.SetLineWidth (1.0f);
    g.SetStrokeColor (UIColor.Yellow.CGColor);
    g.SetFillColor (UIColor.Red.CGColor);
    g.SetShadow (new CGSize (5, 5), 0, UIColor.Blue.CGColor);

    // set text specific graphics state
    g.SetTextDrawingMode (CGTextDrawingMode.FillStroke);
    g.SelectFont ("Helvetica", fontSize, CGTextEncoding.MacRoman);

    // show the text
    g.ShowText ("Hello Core Graphics");
}
```

Comme vous pouvez le voir, la définition de l’État Graphics pour le dessin de texte est similaire à la géométrie de dessin. Toutefois, pour le dessin de texte, le mode dessin de texte et la police sont également appliqués. Dans ce cas, une ombre est également appliquée, bien que l’application des ombres fonctionne de la même façon pour le tracé.

Le texte obtenu s’affiche avec l’image, comme indiqué ci-dessous:

 ![](core-graphics-images/05-text-on-image.png "Le texte obtenu s’affiche avec l’image")

## <a name="memory-backed-images"></a>Images sauvegardées en mémoire

En plus de dessiner dans le contexte graphique d’une vue, Core Graphics prend en charge le dessin d’images sauvegardées en mémoire, également connues sous le nom de dessin hors écran. Cela nécessite les éléments suivants:

-  Création d’un contexte graphique qui est stocké par un en mémoire bitmap
-  Définition de l’état des dessins et des commandes de dessin émises
-  Obtention de l’image à partir du contexte
-  Suppression du contexte


Contrairement à `Draw` la méthode, où le contexte est fourni par la vue, dans ce cas, vous créez le contexte de l’une des deux manières suivantes:

1. En appelant `UIGraphics.BeginImageContext` (ou `BeginImageContextWithOptions`)

2. En créant un nouveau`CGBitmapContextInstance`

 `CGBitmapContextInstance`est utile lorsque vous travaillez directement avec les bits d’image, par exemple dans les cas où vous utilisez un algorithme de manipulation d’image personnalisé. Dans tous les autres cas, vous devez `BeginImageContext` utiliser `BeginImageContextWithOptions`ou.

Une fois que vous avez un contexte d’image, l’ajout de code de dessin est `UIView` semblable à celui d’une sous-classe. Par exemple, l’exemple de code utilisé précédemment pour dessiner un triangle peut être utilisé pour dessiner dans une image en mémoire plutôt que dans `UIView`un, comme indiqué ci-dessous:

```csharp
UIImage DrawTriangle ()
{
    UIImage triangleImage;

    //push a memory backed bitmap context on the context stack
    UIGraphics.BeginImageContext (new CGSize (200.0f, 200.0f));

    //get graphics context
    using(CGContext g = UIGraphics.GetCurrentContext ()){

        //set up drawing attributes
        g.SetLineWidth(4);
        UIColor.Purple.SetFill ();
        UIColor.Black.SetStroke ();

        //create geometry
        path = new CGPath ();

        path.AddLines(new CGPoint[]{
            new CGPoint(100,200),
            new CGPoint(160,100), 
            new CGPoint(220,200)});

        path.CloseSubpath();

        //add geometry to graphics context and draw it
        g.AddPath(path);
        g.DrawPath(CGPathDrawingMode.FillStroke);

        //get a UIImage from the context
        triangleImage = UIGraphics.GetImageFromCurrentImageContext ();
    }
    
    return triangleImage;
}
```

Une utilisation courante du dessin dans une bitmap mémoire est de capturer une image à partir de n’importe `UIView`quel. Par exemple, le code suivant restitue la couche d’une vue dans un contexte bitmap et crée `UIImage` un à partir de celle-ci:

```csharp
UIGraphics.BeginImageContext (cellView.Frame.Size);

//render the view's layer in the current context
anyView.Layer.RenderInContext (UIGraphics.GetCurrentContext ());

//get a UIImage from the context
UIImage anyViewImage = UIGraphics.GetImageFromCurrentImageContext ();
UIGraphics.EndImageContext ();
```

## <a name="drawing-pdfs"></a>Dessin de fichiers PDF

En plus des images, Core Graphics prend en charge le dessin PDF. Comme les images, vous pouvez afficher un fichier PDF en mémoire et lire un fichier PDF pour le rendu `UIView`dans un.

### <a name="pdf-in-a-uiview"></a>PDF dans un UIView

Core Graphics prend également en charge la lecture d’un fichier PDF à partir d’un fichier `CGPDFDocument` et son rendu dans une vue à l’aide de la classe. La `CGPDFDocument` classe représente un PDF dans le code et peut être utilisée pour lire et dessiner des pages.

Par exemple, le code suivant dans une `UIView` sous-classe lit un fichier PDF à partir d' `CGPDFDocument`un fichier dans:

```csharp
public class PDFView : UIView
{
    CGPDFDocument pdfDoc;

    public PDFView ()
    {
        //create a CGPDFDocument from file.pdf included in the main bundle
        pdfDoc = CGPDFDocument.FromFile ("file.pdf");
    }
  
     public override void Draw (Rectangle rect)
    {
        ...
    }
}
```

La `Draw` méthode peut ensuite utiliser le `CGPDFDocument` `CGPDFPage` pour lire une page et l’afficher en appelant `DrawPDFPage`, comme indiqué ci-dessous:

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);
        
    //flip the CTM so the PDF will be drawn upright
    using (CGContext g = UIGraphics.GetCurrentContext ()) {
        g.TranslateCTM (0, Bounds.Height);
        g.ScaleCTM (1, -1);
        
        // render the first page of the PDF
        using (CGPDFPage pdfPage = pdfDoc.GetPage (1)) {
            
        //get the affine transform that defines where the PDF is drawn
        CGAffineTransform t = pdfPage.GetDrawingTransform (CGPDFBox.Crop, rect, 0, true);
        
        //concatenate the pdf transform with the CTM for display in the view
        g.ConcatCTM (t);
        
        //draw the pdf page
        g.DrawPDFPage (pdfPage);
        }
    }
}
```

### <a name="memory-backed-pdf"></a>PDF sauvegardé en mémoire

Pour un fichier PDF en mémoire, vous devez créer un contexte PDF en appelant `BeginPDFContext`. Le dessin au format PDF est granulaire pour les pages. Chaque page est démarrée en `BeginPDFPage` appelant et terminée en `EndPDFContent`appelant, avec le code Graphics entre les deux. En outre, comme pour le dessin d’images, le dessin PDF sauvegardé en mémoire utilise une origine dans le coin inférieur gauche, qui peut être comptabilisé en modifiant les CTM comme avec les images.

Le code suivant montre comment dessiner du texte dans un fichier PDF:

```csharp
//data buffer to hold the PDF
NSMutableData data = new NSMutableData ();

//create a PDF with empty rectangle, which will configure it for 8.5x11 inches
UIGraphics.BeginPDFContext (data, CGRect.Empty, null);

//start a PDF page
UIGraphics.BeginPDFPage ();
       
using (CGContext g = UIGraphics.GetCurrentContext ()) {
    g.ScaleCTM (1, -1);
    g.TranslateCTM (0, -25);      
    g.SelectFont ("Helvetica", 25, CGTextEncoding.MacRoman);
    g.ShowText ("Hello Core Graphics");
    }
    
//complete a PDF page
UIGraphics.EndPDFContent ();
```

Le texte obtenu est dessiné dans le fichier PDF, qui est ensuite contenu dans `NSData` un qui peut être enregistré, téléchargé, envoyé par courrier électronique, etc.


## <a name="summary"></a>Récapitulatif

Dans cet article, nous avons examiné les fonctionnalités graphiques fournies par le biais de l’infrastructure *graphique principale* . Nous avons vu comment utiliser des graphiques de base pour dessiner une géométrie, des images et des fichiers `UIView,` PDF dans le contexte d’un, ainsi que des contextes de graphiques sauvegardés en mémoire.

## <a name="related-links"></a>Liens associés

- [Core Graphics, exemple](https://docs.microsoft.com/samples/xamarin/ios-samples/graphicsandanimation)
- [Procédure pas à pas graphiques et animation](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Animation de base](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Recettes de l’animation principale](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
