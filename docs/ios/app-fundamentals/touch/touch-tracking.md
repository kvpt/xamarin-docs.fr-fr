---
title: Suivi des doigts multipoint dans Xamarin. iOS
description: Ce document décrit comment suivre des doigts individuels dans les gestes multipoint dans une application Xamarin. iOS. Il est axé sur un exemple d’application de peinture par doigt.
ms.prod: xamarin
ms.assetid: 48E8B20D-0833-43D2-976A-0605DDB386E3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: deda3a96272db42af17221e613822b858d57abb1
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436337"
---
# <a name="multi-touch-finger-tracking-in-xamarinios"></a>Suivi des doigts multipoint dans Xamarin. iOS

_Ce document montre comment suivre les événements tactiles à partir de plusieurs doigts_

Il arrive parfois qu’une application multipoint doive suivre les doigts individuels lorsqu’ils se déplacent simultanément sur l’écran. Une application classique est un programme de peinture par doigt. Vous souhaitez que l’utilisateur soit en mesure de dessiner avec un seul doigt, mais également de dessiner avec plusieurs doigts à la fois. À mesure que votre programme traite plusieurs événements tactiles, il doit faire la distinction entre ces doigts.

Quand un doigt touche l’écran pour la première fois, iOS crée un [`UITouch`](xref:UIKit.UITouch) objet pour ce doigt. Cet objet reste le même que le doigt se déplace sur l’écran, puis monte de l’écran, à partir duquel l’objet est supprimé. Pour effectuer le suivi des doigts, un programme doit éviter de stocker cet `UITouch` objet directement. Au lieu de cela, il peut utiliser la [`Handle`](xref:Foundation.NSObject.Handle) propriété de type `IntPtr` pour identifier ces objets de manière unique `UITouch` .

Presque toujours, un programme qui suit des doigts individuels conserve un dictionnaire pour le suivi tactile. Pour un programme iOS, la clé de dictionnaire est la `Handle` valeur qui identifie un doigt particulier. La valeur du dictionnaire dépend de l’application. Dans le programme [FingerPaint](/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint) , chaque trait de doigt (du toucher au lancement) est associé à un objet qui contient toutes les informations nécessaires pour afficher la ligne dessinée avec ce doigt. Le programme définit une petite `FingerPaintPolyline` classe à cet effet :

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new CGPath();
    }

    public CGColor Color { set; get; }

    public float StrokeWidth { set; get; }

    public CGPath Path { private set; get; }
}
```

Chaque polyligne a une couleur, une largeur de trait et un objet graphique iOS [`CGPath`](xref:CoreGraphics.CGPath) pour accumuler et afficher plusieurs points de la ligne lorsqu’elle est dessinée.

Tout le reste du code présenté ci-dessous est contenu dans une `UIView` dérivée nommée `FingerPaintCanvasView` . Cette classe gère un dictionnaire d’objets de type `FingerPaintPolyline` pendant la durée pendant laquelle ils sont activement dessinés par un ou plusieurs doigts :

```csharp
Dictionary<IntPtr, FingerPaintPolyline> inProgressPolylines = new Dictionary<IntPtr, FingerPaintPolyline>();
```

Ce dictionnaire permet à la vue d’obtenir rapidement les `FingerPaintPolyline` informations associées à chaque doigt en fonction de la `Handle` propriété de l' `UITouch` objet.

La `FingerPaintCanvasView` classe gère également un `List` objet pour les polylignes qui ont été terminées :

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Les objets de ce `List` sont dans le même ordre que celui dans lequel ils ont été dessinés.

`FingerPaintCanvasView` remplace cinq méthodes définies par `View` :

- [`TouchesBegan`](xref:UIKit.UIResponder.TouchesBegan(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesMoved`](xref:UIKit.UIResponder.TouchesMoved(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesEnded`](xref:UIKit.UIResponder.TouchesEnded(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesCancelled`](xref:UIKit.UIResponder.TouchesCancelled(Foundation.NSSet,UIKit.UIEvent))
- [`Draw`](xref:UIKit.UIView.Draw(CoreGraphics.CGRect))

Les différents `Touches` remplacements accumulent les points qui composent les polylignes.

La `Draw` substitution [] dessine les polylignes terminées, puis les polylignes en cours :

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    using (CGContext context = UIGraphics.GetCurrentContext())
    {
        // Stroke settings
        context.SetLineCap(CGLineCap.Round);
        context.SetLineJoin(CGLineJoin.Round);

        // Draw the completed polylines
        foreach (FingerPaintPolyline polyline in completedPolylines)
        {
            context.SetStrokeColor(polyline.Color);
            context.SetLineWidth(polyline.StrokeWidth);
            context.AddPath(polyline.Path);
            context.DrawPath(CGPathDrawingMode.Stroke);
        }

        // Draw the in-progress polylines
        foreach (FingerPaintPolyline polyline in inProgressPolylines.Values)
        {
            context.SetStrokeColor(polyline.Color);
            context.SetLineWidth(polyline.StrokeWidth);
            context.AddPath(polyline.Path);
            context.DrawPath(CGPathDrawingMode.Stroke);
        }
    }
}
```

Chacun des `Touches` remplacements signale potentiellement les actions de plusieurs doigts, indiquées par un ou plusieurs `UITouch` objets stockés dans l' `touches` argument de la méthode. Les `TouchesBegan` substitutions parcourent ces objets. Pour chaque `UITouch` objet, la méthode crée et initialise un nouvel `FingerPaintPolyline` objet, y compris le stockage de l’emplacement initial du doigt obtenu à partir de la `LocationInView` méthode. Cet `FingerPaintPolyline` objet est ajouté au `InProgressPolylines` dictionnaire à l’aide `Handle` de la propriété de l' `UITouch` objet en tant que clé de dictionnaire :

```csharp
public override void TouchesBegan(NSSet touches, UIEvent evt)
{
    base.TouchesBegan(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Create a FingerPaintPolyline, set the initial point, and store it
        FingerPaintPolyline polyline = new FingerPaintPolyline
        {
            Color = StrokeColor,
            StrokeWidth = StrokeWidth,
        };

        polyline.Path.MoveToPoint(touch.LocationInView(this));
        inProgressPolylines.Add(touch.Handle, polyline);
    }
    SetNeedsDisplay();
}
```

La méthode conclut en appelant `SetNeedsDisplay` pour générer un appel à la `Draw` substitution et pour mettre à jour l’écran.

À mesure que le doigt ou les doigts se déplacent sur l’écran, le `View` obtient plusieurs appels à sa `TouchesMoved` substitution. Cette substitution effectue une boucle de la même manière dans les `UITouch` objets stockés dans l' `touches` argument et ajoute l’emplacement actuel du doigt au tracé graphique :

```csharp
public override void TouchesMoved(NSSet touches, UIEvent evt)
{
    base.TouchesMoved(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Add point to path
        inProgressPolylines[touch.Handle].Path.AddLineToPoint(touch.LocationInView(this));
    }
    SetNeedsDisplay();
}
```

La `touches` collection contient uniquement les `UITouch` objets pour les doigts qui ont été déplacés depuis le dernier appel à `TouchesBegan` ou `TouchesMoved` . Si vous avez besoin d' `UITouch` objets correspondant à *tous* les doigts actuellement en contact avec l’écran, ces informations sont disponibles via la `AllTouches` propriété de l’argument de la `UIEvent` méthode.

Le `TouchesEnded` remplacement a deux tâches. Il doit ajouter le dernier point au chemin d’accès graphique et transférer l' `FingerPaintPolyline` objet du `inProgressPolylines` dictionnaire à la `completedPolylines` liste :

```csharp
public override void TouchesEnded(NSSet touches, UIEvent evt)
{
    base.TouchesEnded(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Get polyline from dictionary and remove it from dictionary
        FingerPaintPolyline polyline = inProgressPolylines[touch.Handle];
        inProgressPolylines.Remove(touch.Handle);

        // Add final point to path and save with completed polylines
        polyline.Path.AddLineToPoint(touch.LocationInView(this));
        completedPolylines.Add(polyline);
    }
    SetNeedsDisplay();
}
```

Le `TouchesCancelled` remplacement est géré en abandonnant simplement l' `FingerPaintPolyline` objet dans le dictionnaire :

```csharp
public override void TouchesCancelled(NSSet touches, UIEvent evt)
{
    base.TouchesCancelled(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        inProgressPolylines.Remove(touch.Handle);
    }
    SetNeedsDisplay();
}
```

Ensemble, ce traitement permet au programme [FingerPaint](/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint) d’effectuer le suivi des doigts individuels et de dessiner les résultats à l’écran :

[![Suivi des doigts individuels et dessin des résultats à l’écran](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

Vous avez maintenant vu comment vous pouvez suivre des doigts individuels sur l’écran et les distinguer.

## <a name="related-links"></a>Liens associés

- [Guide Xamarin Android équivalent](~/android/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint (exemple)](/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint)