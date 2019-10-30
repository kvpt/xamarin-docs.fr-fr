---
title: TextKit dans Xamarin. iOS
description: Ce document explique comment utiliser TextKit dans Xamarin. iOS. TextKit fournit de puissantes fonctionnalités de rendu et de disposition du texte.
ms.prod: xamarin
ms.assetid: 1D0477E8-CD1E-48A9-B7C8-7CA892069EFF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 6c1dee464de1f7ba708b1f7d60affc1616e71ee9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031420"
---
# <a name="textkit-in-xamarinios"></a>TextKit dans Xamarin. iOS

TextKit est une nouvelle API qui offre de puissantes fonctionnalités de présentation et de rendu du texte. Il s’appuie sur l’infrastructure de texte de base de bas niveau, mais il est beaucoup plus facile à utiliser que le texte de base.

Pour que les fonctionnalités de TextKit soient disponibles pour les contrôles standard, plusieurs contrôles de texte iOS ont été réimplémentés pour utiliser TextKit, notamment :

- UITextView
- UITextField
- UILabel

## <a name="architecture"></a>Architecture

TextKit fournit une architecture en couches qui sépare le stockage de texte de la disposition et de l’affichage, y compris les classes suivantes :

- `NSTextContainer` : fournit le système de coordonnées et la géométrie utilisés pour mettre en forme le texte.
- `NSLayoutManager` : met du texte en forme de glyphes.
- `NSTextStorage` : contient les données texte, ainsi que les mises à jour des propriétés de texte par lots. Toutes les mises à jour par lots sont transmises au gestionnaire de disposition pour le traitement réel des modifications, telles que le recalcul de la disposition et le redessin du texte.

Ces trois classes sont appliquées à une vue qui restitue le texte. Les vues de gestion de texte intégrées, telles que `UITextView`, `UITextField`et `UILabel` les ont déjà définies, mais vous pouvez les créer et les appliquer à n’importe quelle instance de `UIView` également.

La figure suivante illustre cette architecture :

 ![](textkit-images/textkitarch.png "This figure illustrates the TextKit architecture")

## <a name="text-storage-and-attributes"></a>Stockage et attributs de texte

La classe `NSTextStorage` contient le texte affiché par une vue. Il communique également toutes les modifications apportées au texte, telles que les modifications apportées aux caractères ou à leurs attributs, dans le gestionnaire de disposition pour l’affichage. `NSTextStorage` hérite de `MSMutableAttributed` chaîne, ce qui permet de spécifier les modifications apportées aux attributs de texte par lots entre les appels `BeginEditing` et `EndEditing`.

Par exemple, l’extrait de code suivant spécifie une modification des couleurs de premier plan et d’arrière-plan, respectivement, et cible des plages spécifiques :

```csharp
textView.TextStorage.BeginEditing ();
textView.TextStorage.AddAttribute(UIStringAttributeKey.ForegroundColor, UIColor.Green, new NSRange(200, 400));
textView.TextStorage.AddAttribute(UIStringAttributeKey.BackgroundColor, UIColor.Black, new NSRange(210, 300));
textView.TextStorage.EndEditing ();
```

Une fois `EndEditing` appelée, les modifications sont envoyées au gestionnaire de présentation, qui à son tour effectue toute mise en page et tout calcul de rendu nécessaires pour le texte à afficher dans la vue.

## <a name="layout-with-exclusion-path"></a>Disposition avec chemin d’exclusion

TextKit prend également en charge la disposition et permet des scénarios complexes, tels que le texte à plusieurs colonnes et le placement de texte autour de chemins d’accès spécifiés, appelés *chemins d’exclusion*. Les chemins d’exclusion sont appliqués au conteneur de texte, qui modifie la géométrie de la disposition du texte, provoquant le déplacement du texte autour des chemins d’accès spécifiés.

L’ajout d’un chemin d’exclusion requiert la définition de la propriété `ExclusionPaths` sur le gestionnaire de disposition. La définition de cette propriété entraîne l’invalidation de la disposition du texte par le gestionnaire de disposition et le placement du texte autour du chemin d’exclusion.

### <a name="exclusion-based-on-a-cgpath"></a>Exclusion basée sur un CGPath

Tenez compte de l’implémentation de sous-classe `UITextView` suivante :

```csharp
public class ExclusionPathView : UITextView
{
    CGPath exclusionPath;
    CGPoint initialPoint;
    CGPoint latestPoint;
    UIBezierPath bezierPath;

    public ExclusionPathView (string text)
    {
        Text = text;
        ContentInset = new UIEdgeInsets (20, 0, 0, 0);
        BackgroundColor = UIColor.White;
        exclusionPath = new CGPath ();
        bezierPath = UIBezierPath.Create ();

        LayoutManager.AllowsNonContiguousLayout = false;
    }

    public override void TouchesBegan (NSSet touches, UIEvent evt)
    {
        base.TouchesBegan (touches, evt);

        var touch = touches.AnyObject as UITouch;

        if (touch != null) {
            initialPoint = touch.LocationInView (this);
        }
    }

    public override void TouchesMoved (NSSet touches, UIEvent evt)
    {
        base.TouchesMoved (touches, evt);

        UITouch touch = touches.AnyObject as UITouch;

        if (touch != null) {
            latestPoint = touch.LocationInView (this);
            SetNeedsDisplay ();
        }
    }

    public override void TouchesEnded (NSSet touches, UIEvent evt)
    {
        base.TouchesEnded (touches, evt);

        bezierPath.CGPath = exclusionPath;
        TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
    }

    public override void Draw (CGRect rect)
    {
        base.Draw (rect);

        if (!initialPoint.IsEmpty) {

            using (var g = UIGraphics.GetCurrentContext ()) {

                g.SetLineWidth (4);
                UIColor.Blue.SetStroke ();

                if (exclusionPath.IsEmpty) {
                    exclusionPath.AddLines (new CGPoint[] { initialPoint, latestPoint });
                } else {
                    exclusionPath.AddLineToPoint (latestPoint);
                }

                g.AddPath (exclusionPath);
                g.DrawPath (CGPathDrawingMode.Stroke);
            }
        }
    }
}
```

Ce code ajoute la prise en charge du dessin sur l’affichage de texte à l’aide de graphiques principaux. Étant donné que la classe `UITextView` est désormais générée pour utiliser TextKit pour son rendu et sa disposition de texte, elle peut utiliser toutes les fonctionnalités de TextKit, telles que la définition de chemins d’exclusion.

> [!IMPORTANT]
> Cet exemple sous-classe `UITextView` pour ajouter la prise en charge du dessin tactile. Le sous-classing `UITextView` n’est pas nécessaire pour bénéficier des fonctionnalités de TextKit.

Une fois que l’utilisateur a dessiné l’affichage de texte, le `CGPath` dessiné est appliqué à une instance `UIBezierPath` en définissant la propriété `UIBezierPath.CGPath` :

```csharp
bezierPath.CGPath = exclusionPath;
```

La mise à jour de la ligne de code suivante rend la mise à jour de la disposition du texte autour du chemin d’accès :

```csharp
TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
```

La capture d’écran suivante illustre la façon dont la disposition du texte change pour circuler autour du tracé dessiné :

<!-- ![](textkit-images/exclusionpath1.png "This screenshot illustrates how the text layout changes to flow around the drawn path")-->
![](textkit-images/exclusionpath2.png "This screenshot illustrates how the text layout changes to flow around the drawn path")

Notez que la propriété `AllowsNonContiguousLayout` du gestionnaire de présentation est définie sur false dans ce cas. Cela entraîne le recalcul de la disposition pour tous les cas où le texte change. Si vous affectez la valeur true, vous pouvez bénéficier des performances en évitant une actualisation de la mise en page complète, en particulier dans le cas de documents volumineux. Toutefois, l’affectation de la valeur true à `AllowsNonContiguousLayout` empêche le chemin d’exclusion de mettre à jour la disposition dans certains cas, par exemple, si du texte est entré au moment de l’exécution sans retour chariot de fin avant la définition du chemin d’accès.

## <a name="related-links"></a>Liens associés

- [Introduction à iOS 7 (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/introtoios7)
- [Vue d'ensemble de l'interface utilisateur iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Backgrounding](~/ios/app-fundamentals/backgrounding/index.md)
