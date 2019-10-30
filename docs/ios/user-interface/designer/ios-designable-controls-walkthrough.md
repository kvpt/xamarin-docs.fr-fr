---
title: utilisation de contrôles personnalisés avec le concepteur iOS
description: Ce document décrit comment créer un contrôle personnalisé et l’utiliser avec l’Xamarin Designer pour iOS. Il montre comment rendre le contrôle disponible dans la boîte à outils du concepteur iOS, implémenter le contrôle afin qu’il s’affiche correctement et au moment de la conception, et bien plus encore.
ms.prod: xamarin
ms.assetid: 9032B32E-97BD-4DA6-9955-811B84682578
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 4b8c72da1e280b83e215bca9316bc0b9de99402c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73003800"
---
# <a name="using-custom-controls-with-the-ios-designer"></a>utilisation de contrôles personnalisés avec le concepteur iOS

## <a name="requirements"></a>spécifications

Le Xamarin Designer pour iOS est disponible dans Visual Studio pour Mac et Visual Studio 2017 et versions ultérieures sur Windows.

Ce guide suppose que vous connaissez le contenu abordé dans les guides de [prise en main](~/ios/get-started/index.md).

## <a name="walkthrough"></a>Procédure pas à pas

> [!IMPORTANT]
> À partir de Xamarin. Studio 5,5, la façon dont les contrôles personnalisés sont créés est légèrement différente de celle des versions antérieures. Pour créer un contrôle personnalisé, l’interface `IComponent` est requise (avec les méthodes d’implémentation associées) ou la classe peut être annotée avec `[DesignTimeVisible(true)]`. La dernière méthode est utilisée dans l’exemple de procédure pas à pas suivant.

1. Créez une nouvelle solution à partir de l’application **iOS > > modèle d' C# application à affichage unique >** , nommez-la `ScratchTicket`et poursuivez avec l’Assistant Nouveau projet :

    [![](ios-designable-controls-walkthrough-images/01new.png "Create a new solution")](ios-designable-controls-walkthrough-images/01new.png#lightbox)

1. Créez un fichier de classe vide nommé `ScratchTicketView`:

    [![](ios-designable-controls-walkthrough-images/02new.png "Create a new ScratchTicketView class")](ios-designable-controls-walkthrough-images/02new.png#lightbox)

1. Ajoutez le code suivant pour `ScratchTicketView` classe :

    ```csharp
    using System;
    using System.ComponentModel;
    using CoreGraphics;
    using Foundation;
    using UIKit;

    namespace ScratchTicket
    {
        [Register("ScratchTicketView"), DesignTimeVisible(true)]
        public class ScratchTicketView : UIView
        {
            CGPath path;
            CGPoint initialPoint;
            CGPoint latestPoint;
            bool startNewPath = false;
            UIImage image;

            [Export("Image"), Browsable(true)]
            public UIImage Image
            {
                get { return image; }
                set
                {
                    image = value;
                    SetNeedsDisplay();
                }
            }

            public ScratchTicketView(IntPtr p)
                : base(p)
            {
                Initialize();
            }

            public ScratchTicketView()
            {
                Initialize();
            }

            void Initialize()
            {
                initialPoint = CGPoint.Empty;
                latestPoint = CGPoint.Empty;
                BackgroundColor = UIColor.Clear;
                Opaque = false;
                path = new CGPath();
                SetNeedsDisplay();
            }

            public override void TouchesBegan(NSSet touches, UIEvent evt)
            {
                base.TouchesBegan(touches, evt);

                var touch = touches.AnyObject as UITouch;

                if (touch != null)
                {
                    initialPoint = touch.LocationInView(this);
                }
            }

            public override void TouchesMoved(NSSet touches, UIEvent evt)
            {
                base.TouchesMoved(touches, evt);

                var touch = touches.AnyObject as UITouch;

                if (touch != null)
                {
                    latestPoint = touch.LocationInView(this);
                    SetNeedsDisplay();
                }
            }

            public override void TouchesEnded(NSSet touches, UIEvent evt)
            {
                base.TouchesEnded(touches, evt);
                startNewPath = true;
            }

            public override void Draw(CGRect rect)
            {
                base.Draw(rect);

                using (var g = UIGraphics.GetCurrentContext())
                {
                    if (image != null)
                        g.SetFillColor((UIColor.FromPatternImage(image).CGColor));
                    else
                        g.SetFillColor(UIColor.LightGray.CGColor);
                    g.FillRect(rect);

                    if (!initialPoint.IsEmpty)
                    {
                        g.SetLineWidth(20);
                        g.SetBlendMode(CGBlendMode.Clear);
                        UIColor.Clear.SetColor();

                        if (path.IsEmpty || startNewPath)
                        {
                            path.AddLines(new CGPoint[] { initialPoint, latestPoint });
                            startNewPath = false;
                        }
                        else
                        {
                            path.AddLineToPoint(latestPoint);
                        }

                        g.SetLineCap(CGLineCap.Round);
                        g.AddPath(path);
                        g.DrawPath(CGPathDrawingMode.Stroke);
                    }
                }
            }
        }
    }
    ```

1. Ajoutez les fichiers `FillTexture.png`, `FillTexture2.png` et `Monkey.png` (disponibles [à partir de GitHub](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)) au dossier **Resources** .

1. Double-cliquez sur le fichier `Main.storyboard` pour l’ouvrir dans le concepteur :

    [![](ios-designable-controls-walkthrough-images/03new.png "The iOS Designer")](ios-designable-controls-walkthrough-images/03new.png#lightbox)

1. Glissez-déplacez une **vue d’image** de la **boîte à outils** vers la vue dans le Storyboard.

    [![](ios-designable-controls-walkthrough-images/04new.png "An Image View added to the layout")](ios-designable-controls-walkthrough-images/04new.png#lightbox)

1. Sélectionnez la **vue image** et définissez sa propriété **image** sur `Monkey.png`.

    [![](ios-designable-controls-walkthrough-images/05new.png "Setting Image View Image property to Monkey.png")](ios-designable-controls-walkthrough-images/05new.png#lightbox)

1. Comme nous utilisons des classes de taille, nous devrons contraindre cette vue d’image. Cliquez deux fois sur l’image pour la placer en mode de contrainte. Nous allons la contraindre au centre en cliquant sur la poignée centrale et en l’alignant à la fois verticalement et horizontalement :

    [![](ios-designable-controls-walkthrough-images/06new.png "Centering the image")](ios-designable-controls-walkthrough-images/06new.png#lightbox)

1. Pour limiter la hauteur et la largeur, cliquez sur les poignées de taille (les poignées de forme « OS ») et sélectionnez respectivement la largeur et la hauteur :

    [![](ios-designable-controls-walkthrough-images/07new.png "Adding Constraints")](ios-designable-controls-walkthrough-images/07new.png#lightbox)

1. Mettez à jour le frame en fonction des contraintes en cliquant sur le bouton mettre à jour dans la barre d’outils :

    [![](ios-designable-controls-walkthrough-images/08new.png "The Constraints toolbar")](ios-designable-controls-walkthrough-images/08new.png#lightbox)

1. Ensuite, générez le projet afin que la **vue de ticket Scratch** s’affiche sous **composants personnalisés** dans la boîte à outils :

    [![](ios-designable-controls-walkthrough-images/09new.png "The Custom Components toolbox")](ios-designable-controls-walkthrough-images/09new.png#lightbox)

1. Faites glisser et déposez une **vue de ticket Scratch** pour qu’elle apparaisse sur l’image de singe. Ajustez les poignées de glissement pour que l’affichage du ticket Scratch couvre complètement le singe, comme indiqué ci-dessous :

    [![](ios-designable-controls-walkthrough-images/10new.png "A Scratch Ticket View over the Image View")](ios-designable-controls-walkthrough-images/10new.png#lightbox)

1. Limitez la vue de ticket scratch à la vue image en dessinant un rectangle englobant pour sélectionner les deux vues. Sélectionnez les options pour les contraindre à la largeur, à la hauteur, au centre et au milieu, et mettez à jour les frames en fonction des contraintes, comme indiqué ci-dessous :

    [![](ios-designable-controls-walkthrough-images/11new.png "Centering and adding Constraints")](ios-designable-controls-walkthrough-images/11new.png#lightbox)

1. Exécutez l’application et « éraflure » de l’image pour révéler le singe.

    [![](ios-designable-controls-walkthrough-images/10-app.png "A sample app run")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="adding-design-time-properties"></a>Ajout de propriétés au moment de la conception

Le concepteur comprend également la prise en charge au moment du design des contrôles personnalisés de type de propriété numeric, Enumeration, String, bool, CGSize, UIColor et UIImage. Pour illustrer cela, nous allons ajouter une propriété au `ScratchTicketView` pour définir l’image qui est « rayée ».

Ajoutez le code suivant à la classe `ScratchTicketView` pour la propriété :

```csharp
[Export("Image"), Browsable(true)]
public UIImage Image
{
    get { return image; }
    set {
            image = value;
              SetNeedsDisplay ();
        }
}
```

Nous pouvons également ajouter un contrôle null à la méthode `Draw`, comme suit :

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    using (var g = UIGraphics.GetCurrentContext())
    {
        if (image != null)
            g.SetFillColor ((UIColor.FromPatternImage (image).CGColor));
        else
            g.SetFillColor (UIColor.LightGray.CGColor);

        g.FillRect(rect);

        if (!initialPoint.IsEmpty)
        {
             g.SetLineWidth(20);
             g.SetBlendMode(CGBlendMode.Clear);
             UIColor.Clear.SetColor();

             if (path.IsEmpty || startNewPath)
             {
                 path.AddLines(new CGPoint[] { initialPoint, latestPoint });
                 startNewPath = false;
             }
             else
             {
                 path.AddLineToPoint(latestPoint);
             }

             g.SetLineCap(CGLineCap.Round);
             g.AddPath(path);
             g.DrawPath(CGPathDrawingMode.Stroke);
        }
    }
}
```

L’inclusion d’un `ExportAttribute` et d’un `BrowsableAttribute` avec l’argument défini sur `true` entraîne l’affichage de la propriété dans le panneau des **Propriétés** du concepteur. La modification de la propriété en une autre image incluse dans le projet, par exemple `FillTexture2.png`, entraîne la mise à jour du contrôle au moment du design, comme indiqué ci-dessous :

 [![](ios-designable-controls-walkthrough-images/11-customproperty.png "Editing Design Time properties")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="summary"></a>Récapitulatif

Dans cet article, nous avons parcouru la création d’un contrôle personnalisé, ainsi que son utilisation dans une application iOS à l’aide du concepteur iOS. Nous avons vu comment créer et générer le contrôle pour le rendre disponible pour une application dans la **boîte à outils**du concepteur. En outre, nous avons vu comment implémenter le contrôle de sorte qu’il s’affiche correctement à la fois au moment du design et au moment de l’exécution, et comment exposer des propriétés de contrôle personnalisées dans le concepteur.

## <a name="related-links"></a>Liens associés

- [ScratchTicket (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/scratchticket)
- [images requises (exemple)](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)
