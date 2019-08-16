---
title: 'Procédure pas à pas : Utilisation de Touch dans Xamarin. iOS'
description: Ce document explique comment gérer les fonctions tactiles dans les applications Xamarin. iOS, en discutant des exemples d’interactions tactiles, des détecteurs de mouvement et des détecteurs de mouvement personnalisés.
ms.prod: xamarin
ms.assetid: 13F8289B-7A80-4959-AF3F-57874D866DCA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 352a312d795ba292b47621ddb84540c2259ea10a
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527055"
---
# <a name="walkthrough-using-touch-in-xamarinios"></a>Procédure pas à pas : Utilisation de Touch dans Xamarin. iOS

Cette procédure pas à pas montre comment écrire du code qui répond à différents genres d’événements tactiles. Chaque exemple est contenu dans un écran distinct:

- [Exemples tactiles](#Touch_Samples) : comment répondre aux événements tactiles.
- [Exemples](#Gesture_Recognizer_Samples) de module de reconnaissance de mouvement: comment utiliser des détecteurs de mouvement intégrés.
- Exemple de module de [reconnaissance de mouvement personnalisé](#Custom_Gesture_Recognizer) : comment créer un module de reconnaissance de mouvement personnalisé.

Chaque section contient des instructions permettant d’écrire le code à partir de zéro.
L' [exemple de code de départ](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-start) comprend déjà un Storyboard complet et un écran de menu:

 [![](ios-touch-walkthrough-images/image3.png "L’exemple comprend l’écran du menu")](ios-touch-walkthrough-images/image3.png#lightbox)

Suivez les instructions ci-dessous pour ajouter du code à la table de montage séquentiel et découvrir les différents types d’événements tactiles disponibles dans iOS. Vous pouvez également ouvrir l' [exemple terminé](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-final) pour voir tout ce qui est en cours d’exécution.

<a name="Touch_Samples"/>

## <a name="touch-samples"></a>Exemples tactiles

Dans cet exemple, nous allons présenter certaines des API tactiles. Procédez comme suit pour ajouter le code requis pour implémenter les événements tactiles:


1. Ouvrez le projet **Touch_Start**. Exécutez d’abord le projet pour vérifier que tout est correct, puis appuyez sur le bouton d' **exemples tactiles** . Vous devez voir un écran similaire à ce qui suit (même si aucun des boutons ne fonctionne):
    
    [![](ios-touch-walkthrough-images/image4.png "Exemple d’exécution d’application avec des boutons non fonctionnels")](ios-touch-walkthrough-images/image4.png#lightbox)


1. Modifiez le fichier **TouchViewController.cs** et ajoutez les deux variables d’instance suivantes à la `TouchViewController`classe:

    ```csharp 
    #region Private Variables
    private bool imageHighlighted = false;
    private bool touchStartedInside;
    #endregion
    ```


1. Implémentez `TouchesBegan` la méthode, comme indiqué dans le code ci-dessous:

    ```csharp 
    public override void TouchesBegan(NSSet touches, UIEvent evt)
    {
        base.TouchesBegan(touches, evt);
    
        // If Multitouch is enabled, report the number of fingers down
        TouchStatus.Text = string.Format ("Number of fingers {0}", touches.Count);
    
        // Get the current touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            // Check to see if any of the images have been touched
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                // Fist image touched
                TouchImage.Image = UIImage.FromBundle("TouchMe_Touched.png");
                TouchStatus.Text = "Touches Began";
            } else if (touch.TapCount == 2 && DoubleTouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                // Second image double-tapped, toggle bitmap
                if (imageHighlighted)
                {
                    DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe.png");
                    TouchStatus.Text = "Double-Tapped Off";
                }
                else
                {
                    DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe_Highlighted.png");
                    TouchStatus.Text = "Double-Tapped On";
                }
                imageHighlighted = !imageHighlighted;
            } else if (DragImage.Frame.Contains(touch.LocationInView(View)))
            {
                // Third image touched, prepare to drag
                touchStartedInside = true;
            }
        }
    }
    ```
    
    Cette méthode permet de rechercher un `UITouch` objet et, s’il existe, d’effectuer une action en fonction de l’endroit où la pression tactile s’est produite:

    * _À l’intérieur de TouchImage_ : `Touches Began` Affichez le texte dans une étiquette et modifiez l’image.
    * _À l’intérieur de DoubleTouchImage_ : modifiez l’image affichée si le mouvement était un double-appui.
    * _Dans DragImage_ , définissez un indicateur qui indique que la fonction tactile a démarré. La méthode `TouchesMoved` utilise cet indicateur pour déterminer si `DragImage` doit être déplacé à l’écran ou non, comme nous le verrons à l’étape suivante.

    Le code ci-dessus ne traite que les touches individuelles, il n’y a toujours aucun comportement si l’utilisateur déplace son doigt sur l’écran. Pour répondre au mouvement, implémentez `TouchesMoved` comme indiqué dans le code ci-dessous:

    ```csharp 
    public override void TouchesMoved(NSSet touches, UIEvent evt)
    {
        base.TouchesMoved(touches, evt);
        // get the touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            //==== IMAGE TOUCH
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                TouchStatus.Text = "Touches Moved";
            }

            //==== IMAGE DRAG
            // check to see if the touch started in the drag me image
            if (touchStartedInside)
            {
                // move the shape
                float offsetX = touch.PreviousLocationInView(View).X - touch.LocationInView(View).X;
                float offsetY = touch.PreviousLocationInView(View).Y - touch.LocationInView(View).Y;
                DragImage.Frame = new RectangleF(new PointF(DragImage.Frame.X - offsetX, DragImage.Frame.Y - offsetY), DragImage.Frame.Size);
            }
        }
    }
    ```

    Cette méthode obtient un `UITouch` objet, puis vérifie où la touche tactile s’est produite. Si la pression tactile s' `TouchImage`est produite dans, le texte touche déplacer s’affiche à l’écran. 

    Si `touchStartedInside` a la valeur true, nous savons que l’utilisateur a son doigt `DragImage` sur et le déplace. Le code `DragImage` se déplace lorsque l’utilisateur déplace son doigt sur l’écran.

1. Nous devons gérer le cas où l’utilisateur soulève son doigt de l’écran, ou iOS annule l’événement tactile. Pour cela, nous allons `TouchesEnded` implémenter `TouchesCancelled` et, comme indiqué ci-dessous:

    ```csharp
    public override void TouchesCancelled(NSSet touches, UIEvent evt)
    {
        base.TouchesCancelled(touches, evt);
    
        // reset our tracking flags
        touchStartedInside = false;
        TouchImage.Image = UIImage.FromBundle("TouchMe.png");
        TouchStatus.Text = "";
    }
    
    public override void TouchesEnded(NSSet touches, UIEvent evt)
    {
        base.TouchesEnded(touches, evt);
        // get the touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            //==== IMAGE TOUCH
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                TouchImage.Image = UIImage.FromBundle("TouchMe.png");
                TouchStatus.Text = "Touches Ended";
            }
        }
        // reset our tracking flags
        touchStartedInside = false;
    }
    ```
    
    Ces deux méthodes réinitialisent l' `touchStartedInside` indicateur sur false. `TouchesEnded`s’affiche `TouchesEnded` également à l’écran.

1. À ce stade, l’écran d’exemples tactiles est terminé. Notez que l’écran change quand vous interagissez avec chacune des images, comme illustré dans la capture d’écran suivante:
        
    [![](ios-touch-walkthrough-images/image4.png "Écran de démarrage de l’application")](ios-touch-walkthrough-images/image4.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image5.png "L’écran lorsque l’utilisateur fait glisser un bouton")](ios-touch-walkthrough-images/image5.png#lightbox)
 

<a name="Gesture_Recognizer_Samples" />

## <a name="gesture-recognizer-samples"></a>Exemples de module de reconnaissance de mouvement

La [section précédente](#Touch_Samples) expliquait comment faire glisser un objet autour de l’écran à l’aide d’événements tactiles.
Dans cette section, nous allons supprimer les événements tactiles et montrer comment utiliser les module de reconnaissance de mouvement suivants:

- `UIPanGestureRecognizer` Pour faire glisser une image autour de l’écran.
- `UITapGestureRecognizer` Pour répondre aux doubles pressions sur l’écran.

Si vous exécutez l' [exemple de code de départ](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-start) et que vous cliquez sur le bouton exemples de module de **reconnaissance de mouvement** , l’écran suivant doit s’afficher:

 [![](ios-touch-walkthrough-images/image6.png "Cliquer sur le bouton exemples de module de reconnaissance de mouvement affiche cet écran")](ios-touch-walkthrough-images/image6.png#lightbox)

Suivez ces étapes pour implémenter des module de reconnaissance de mouvement:


1. Modifiez le fichier **GestureViewController.cs** et ajoutez la variable d’instance suivante:

    ```csharp
    #region Private Variables
    private bool imageHighlighted = false;
    private RectangleF originalImageFrame = RectangleF.Empty;
    #endregion
    ```

    Nous avons besoin de cette variable d’instance pour effectuer le suivi de l’emplacement précédent de l’image.
Le module de reconnaissance de mouvement de panoramique `originalImageFrame` utilise la valeur pour calculer le décalage requis pour redessiner l’image à l’écran.

1. Ajoutez la méthode suivante au contrôleur:

    ```csharp
    private void WireUpDragGestureRecognizer()
    {
        // Create a new tap gesture
        UIPanGestureRecognizer gesture = new UIPanGestureRecognizer();
    
        // Wire up the event handler (have to use a selector)
        gesture.AddTarget(() => HandleDrag(gesture));  // to be defined
    
        // Add the gesture recognizer to the view
        DragImage.AddGestureRecognizer(gesture);
    }
    ```

    Ce code instancie une `UIPanGestureRecognizer` instance et l’ajoute à une vue.
Notez que nous attribuons une cible au geste sous la forme de la méthode `HandleDrag` . cette méthode est fournie à l’étape suivante.

1. Pour implémenter HandleDrag, ajoutez le code suivant au contrôleur:

    ```csharp
    private void HandleDrag(UIPanGestureRecognizer recognizer)
    {
        // If it's just began, cache the location of the image
        if (recognizer.State == UIGestureRecognizerState.Began)
        {
            originalImageFrame = DragImage.Frame;
        }
    
        // Move the image if the gesture is valid
        if (recognizer.State != (UIGestureRecognizerState.Cancelled | UIGestureRecognizerState.Failed
            | UIGestureRecognizerState.Possible))
        {
            // Move the image by adding the offset to the object's frame
            PointF offset = recognizer.TranslationInView(DragImage);
            RectangleF newFrame = originalImageFrame;
            newFrame.Offset(offset.X, offset.Y);
            DragImage.Frame = newFrame;
        }
    }
    ```

    Le code ci-dessus vérifie tout d’abord l’état du module de reconnaissance de mouvement, puis déplace l’image autour de l’écran. Avec ce code en place, le contrôleur peut désormais prendre en charge le glissement d’une image à l’écran.


1. Ajoutez un `UITapGestureRecognizer` qui modifiera l’image affichée dans DoubleTouchImage. Ajoutez la méthode suivante au `GestureViewController` contrôleur:

    ```csharp
    private void WireUpTapGestureRecognizer()
    {
        // Create a new tap gesture
        UITapGestureRecognizer tapGesture = null;
    
        // Report touch
        Action action = () => {
            TouchStatus.Text = string.Format("Image touched at: {0}",tapGesture.LocationOfTouch(0, DoubleTouchImage));
    
            // Toggle the image
            if (imageHighlighted)
            {
                DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe.png");
            }
            else
            {
                DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe_Highlighted.png");
            }
            imageHighlighted = !imageHighlighted;
        };
    
        tapGesture = new UITapGestureRecognizer(action);
    
        // Configure it
        tapGesture.NumberOfTapsRequired = 2;
    
        // Add the gesture recognizer to the view
        DoubleTouchImage.AddGestureRecognizer(tapGesture);
    }
    ```

    Ce code est très similaire au code pour `UIPanGestureRecognizer` , mais au lieu d’utiliser un délégué pour une cible, nous utilisons un. `Action` 

1. La dernière chose à faire est de modifier `ViewDidLoad` afin qu’il appelle les méthodes que nous venons d’ajouter. Modifiez ViewDidLoad afin qu’il ressemble au code suivant:

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
    
        Title = "Gesture Recognizers";
    
        // Save initial state
        originalImageFrame = DragImage.Frame;
    
        WireUpTapGestureRecognizer();
        WireUpDragGestureRecognizer();
    }
    ```

    Notez également que nous initialisant la valeur de `originalImageFrame`.


1. Exécutez l’application et interagissez avec les deux images.
La capture d’écran suivante est un exemple de ces interactions:
    
    [![](ios-touch-walkthrough-images/image7.png "Cette capture d’écran montre une interaction de glissement")](ios-touch-walkthrough-images/image7.png#lightbox)



<a name="Custom_Gesture_Recognizer"/>

## <a name="custom-gesture-recognizer"></a>Module de reconnaissance de mouvement personnalisé

Dans cette section, nous allons appliquer les concepts des sections précédentes pour créer un module de reconnaissance de mouvement personnalisé. Le module de reconnaissance de mouvement personnalisé est sous `UIGestureRecognizer`-classé et reconnaîtra quand l’utilisateur dessinera un «V» à l’écran, puis basculera une image bitmap. La capture d’écran suivante est un exemple de cet écran:

 [![](ios-touch-walkthrough-images/image8.png "L’application reconnaîtra quand l’utilisateur dessinera un «V» à l’écran")](ios-touch-walkthrough-images/image8.png#lightbox)

Pour créer un module de reconnaissance de mouvement personnalisé, procédez comme suit:


1. Ajoutez une nouvelle classe au projet nommé `CheckmarkGestureRecognizer`et faites en sorte qu’elle ressemble au code suivant:

    ```csharp
    using System;
    using CoreGraphics;
    using Foundation;
    using UIKit;
    
    namespace Touch
    {
        public class CheckmarkGestureRecognizer : UIGestureRecognizer
        {
            #region Private Variables
            private CGPoint midpoint = CGPoint.Empty;
            private bool strokeUp = false;
            #endregion
    
            #region Override Methods
            /// <summary>
            ///   Called when the touches end or the recognizer state fails
            /// </summary>
            public override void Reset()
            {
                base.Reset();
    
                strokeUp = false;
                midpoint = CGPoint.Empty;
            }
    
            /// <summary>
            ///   Is called when the fingers touch the screen.
            /// </summary>
            public override void TouchesBegan(NSSet touches, UIEvent evt)
            {
                base.TouchesBegan(touches, evt);
    
                // we want one and only one finger
                if (touches.Count != 1)
                {
                    base.State = UIGestureRecognizerState.Failed;
                }
    
                Console.WriteLine(base.State.ToString());
            }
    
            /// <summary>
            ///   Called when the touches are cancelled due to a phone call, etc.
            /// </summary>
            public override void TouchesCancelled(NSSet touches, UIEvent evt)
            {
                base.TouchesCancelled(touches, evt);
                // we fail the recognizer so that there isn't unexpected behavior
                // if the application comes back into view
                base.State = UIGestureRecognizerState.Failed;
            }
    
            /// <summary>
            ///   Called when the fingers lift off the screen
            /// </summary>
            public override void TouchesEnded(NSSet touches, UIEvent evt)
            {
                base.TouchesEnded(touches, evt);
                //
                if (base.State == UIGestureRecognizerState.Possible && strokeUp)
                {
                    base.State = UIGestureRecognizerState.Recognized;
                }
    
                Console.WriteLine(base.State.ToString());
            }
    
            /// <summary>
            ///   Called when the fingers move
            /// </summary>
            public override void TouchesMoved(NSSet touches, UIEvent evt)
            {
                base.TouchesMoved(touches, evt);
    
                // if we haven't already failed
                if (base.State != UIGestureRecognizerState.Failed)
                {
                    // get the current and previous touch point
                    CGPoint newPoint = (touches.AnyObject as UITouch).LocationInView(View);
                    CGPoint previousPoint = (touches.AnyObject as UITouch).PreviousLocationInView(View);
    
                    // if we're not already on the upstroke
                    if (!strokeUp)
                    {
                        // if we're moving down, just continue to set the midpoint at
                        // whatever point we're at. when we start to stroke up, it'll stick
                        // as the last point before we upticked
                        if (newPoint.X >= previousPoint.X && newPoint.Y >= previousPoint.Y)
                        {
                            midpoint = newPoint;
                        }
                        // if we're stroking up (moving right x and up y [y axis is flipped])
                        else if (newPoint.X >= previousPoint.X && newPoint.Y <= previousPoint.Y)
                        {
                            strokeUp = true;
                        }
                        // otherwise, we fail the recognizer
                        else
                        {
                            base.State = UIGestureRecognizerState.Failed;
                        }
                    }
                }
    
                Console.WriteLine(base.State.ToString());
            }
            #endregion
        }
    }
    ```

    La méthode de réinitialisation est `State` appelée lorsque la propriété `Recognized` prend `Ended`la valeur ou. Il s’agit de la durée de réinitialisation de tout état interne défini dans le module de reconnaissance de mouvement personnalisé.
Désormais, la classe peut démarrer la prochaine fois que l’utilisateur interagit avec l’application et être prêt à essayer à nouveau de reconnaître le geste.



1. Maintenant que nous avons défini un module de reconnaissance de mouvement`CheckmarkGestureRecognizer`personnalisé (), modifiez le fichier **CustomGestureViewController.cs** et ajoutez les deux variables d’instance suivantes:

    ```csharp
    #region Private Variables
    private bool isChecked = false;
    private CheckmarkGestureRecognizer checkmarkGesture;
    #endregion
    ```

1. Pour instancier et configurer notre module de reconnaissance de mouvement, ajoutez la méthode suivante au contrôleur:

    ```csharp
    private void WireUpCheckmarkGestureRecognizer()
    {
        // Create the recognizer
        checkmarkGesture = new CheckmarkGestureRecognizer();
    
        // Wire up the event handler
        checkmarkGesture.AddTarget(() => {
            if (checkmarkGesture.State == (UIGestureRecognizerState.Recognized | UIGestureRecognizerState.Ended))
            {
                if (isChecked)
                {
                    CheckboxImage.Image = UIImage.FromBundle("CheckBox_Unchecked.png");
                }
                else
                {
                    CheckboxImage.Image = UIImage.FromBundle("CheckBox_Checked.png");
                }
                isChecked = !isChecked;
            }
        });
    
        // Add the gesture recognizer to the view
        View.AddGestureRecognizer(checkmarkGesture);
    }
    ```

1. Modifiez `ViewDidLoad` afin qu’il appelle `WireUpCheckmarkGestureRecognizer`, comme illustré dans l’extrait de code suivant:

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
    
        // Wire up the gesture recognizer
        WireUpCheckmarkGestureRecognizer();
    }
    ```

1. Exécutez l’application et essayez de dessiner un «V» à l’écran. Vous devez voir l’image en cours de modification, comme illustré dans les captures d’écran suivantes:
    
    [![](ios-touch-walkthrough-images/image9.png "Bouton activé")](ios-touch-walkthrough-images/image9.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image10.png "Bouton désactivé")](ios-touch-walkthrough-images/image10.png#lightbox)



Les trois sections ci-dessus ont démontré différentes façons de répondre aux événements tactiles dans iOS: à l’aide des événements tactiles, des détecteurs de mouvement intégrés ou avec un module de reconnaissance de mouvement personnalisé.



## <a name="related-links"></a>Liens associés

- [Démarrage tactile iOS (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-start)
- [iOS Touch final (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-final)
