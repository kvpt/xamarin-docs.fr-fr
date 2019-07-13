---
title: 'Procédure pas à pas : À l’aide de Touch dans Xamarin.iOS'
description: Ce document décrit comment gérer des entrées tactiles dans les applications Xamarin.iOS, traitant des interactions tactiles d’exemple, les modules de reconnaissance de mouvement et modules de reconnaissance de mouvement personnalisée.
ms.prod: xamarin
ms.assetid: 13F8289B-7A80-4959-AF3F-57874D866DCA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: abcd89a6c4547680df0b96d235531312d3b21c52
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864332"
---
# <a name="walkthrough-using-touch-in-xamarinios"></a>Procédure pas à pas : À l’aide de Touch dans Xamarin.iOS

Cette procédure pas à pas montre comment écrire du code qui répond à différents types d’événements tactiles. Chaque exemple est contenue dans un écran distinct :

- [Touch exemples](#Touch_Samples) – comment répondre aux événements tactiles.
- [Exemples de module de reconnaissance du mouvement](#Gesture_Recognizer_Samples) – comment utiliser des modules de reconnaissance de mouvement intégrés.
- [Exemple de reconnaissance de mouvement personnalisé](#Custom_Gesture_Recognizer) – comment créer un module de reconnaissance de mouvement personnalisée.

Chaque section contient des instructions pour écrire le code à partir de zéro.
Le [à partir d’exemples de code](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start) inclut déjà un écran de storyboard et menu terminée :

 [![](ios-touch-walkthrough-images/image3.png "L’exemple inclut l’écran de menu")](ios-touch-walkthrough-images/image3.png#lightbox)

Suivez les instructions ci-dessous pour ajouter du code à la table de montage séquentiel et en savoir plus sur les différents types d’événements tactiles disponibles dans iOS. Vous pouvez également ouvrir le [exemple terminé](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final) pour voir ce que tout fonctionne.

<a name="Touch_Samples"/>

## <a name="touch-samples"></a>Exemples de contact

Dans cet exemple, nous allons illustrer certaines des fonctions tactiles API. Suivez ces étapes pour ajouter le code requis pour implémenter des événements tactiles :


1. Ouvrez le projet **Touch_Start**. Première exécution du projet pour vous assurer que tout est OK et l’interaction tactile le **exemples Touch** bouton. Vous devez voir un écran semblable à ce qui suit (même si aucun bouton ne fonctionne pas) :
    
    [![](ios-touch-walkthrough-images/image4.png "Exemple d’application s’exécuter avec les boutons qui ne fonctionnent pas")](ios-touch-walkthrough-images/image4.png#lightbox)


1. Modifiez le fichier **TouchViewController.cs** et ajoutez les variables de deux instance suivantes à la classe `TouchViewController`:

    ```csharp 
    #region Private Variables
    private bool imageHighlighted = false;
    private bool touchStartedInside;
    #endregion
    ```


1. Implémentez la `TouchesBegan` méthode, comme indiqué dans le code ci-dessous :

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
    
    Cette méthode fonctionne en recherchant un `UITouch` de l’objet et si elle existe effectuer une action selon où la pression tactile a eu lieu :

    * _À l’intérieur de TouchImage_ : afficher le texte `Touches Began` dans une étiquette et la modification de l’image.
    * _À l’intérieur de DoubleTouchImage_ : modifier l’image affichée si le mouvement a été un double-clic.
    * _À l’intérieur de DragImage_ : définir un indicateur indiquant que la pression tactile a démarré. La méthode `TouchesMoved` utilise cet indicateur pour déterminer si `DragImage` doit être déplacé sur l’écran ou non, comme nous le verrons dans l’étape suivante.

    Touche individuels ne gère que le code ci-dessus, il n’est toujours aucun comportement si l’utilisateur déplace son doigt sur l’écran. Pour répondre au déplacement des, implémentez `TouchesMoved` comme indiqué dans le code ci-dessous :

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

    Cette méthode obtient un `UITouch` objet, puis vérifie pour voir où la pression tactile a eu lieu. Si la pression tactile se sont produites dans `TouchImage`, puis le texte déplacé touche est affiché sur l’écran. 

    Si `touchStartedInside` a la valeur true, nous savons que l’utilisateur dispose de son doigt `DragImage` et il tourne autour. Le code déplacera `DragImage` quand l’utilisateur déplace son doigt sur l’écran.

1. Nous avons besoin de gérer les cas où l’utilisateur retire son doigt hors de l’écran ou iOS annule l’événement tactile. Pour ce faire, nous allons implémenter `TouchesEnded` et `TouchesCancelled` comme indiqué ci-dessous :

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
    
    Ces deux méthodes réinitialisera la `touchStartedInside` indicateur sur false. `TouchesEnded` affiche également `TouchesEnded` sur l’écran.

1. À ce stade, l’écran tactile des échantillons est terminé. Notez comment l’écran change à mesure que vous interagissez avec chacune des images, comme illustré dans la capture d’écran suivante :
        
    [![](ios-touch-walkthrough-images/image4.png "L’écran à partir de l’application")](ios-touch-walkthrough-images/image4.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image5.png "L’écran une fois que l’utilisateur fait glisser un bouton")](ios-touch-walkthrough-images/image5.png#lightbox)
 

<a name="Gesture_Recognizer_Samples" />

## <a name="gesture-recognizer-samples"></a>Exemples de module de reconnaissance de mouvement

Le [section précédente](#Touch_Samples) vous a montré comment faire glisser un objet autour de l’écran à l’aide des événements tactiles.
Dans cette section, nous débarrasser des événements tactiles et montrent comment utiliser les modules de reconnaissance de mouvement suivantes :

-  Le `UIPanGestureRecognizer` permettant de faire glisser une image autour de l’écran.
-  Le `UITapGestureRecognizer` pour répondre aux doubles clics sur l’écran.

Si vous exécutez le [à partir d’exemples de code](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start) , puis cliquez sur le **exemples de module de reconnaissance de mouvement** bouton, vous devez voir l’écran suivant :

 [![](ios-touch-walkthrough-images/image6.png "En cliquant sur le bouton d’exemples de module de reconnaissance de mouvement indique cet écran")](ios-touch-walkthrough-images/image6.png#lightbox)

Suivez ces étapes pour implémenter des modules de reconnaissance de mouvement :


1. Modifiez le fichier **GestureViewController.cs** et ajoutez la variable d’instance suivant :

    ```csharp
    #region Private Variables
    private bool imageHighlighted = false;
    private RectangleF originalImageFrame = RectangleF.Empty;
    #endregion
    ```

    Nous avons besoin de cette variable d’instance pour effectuer le suivi de l’emplacement précédent de l’image.
Le module de reconnaissance de mouvement panoramique utilisera le `originalImageFrame` valeur pour calculer le décalage obligé pour redessiner l’image sur l’écran.

1. Ajoutez la méthode suivante au contrôleur :

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

    Ce code instancie un `UIPanGestureRecognizer` de l’instance et l’ajoute à une vue.
Notez que nous attribuons une cible pour le mouvement sous la forme de la méthode `HandleDrag` – cette méthode est fournie à l’étape suivante.

1. Pour implémenter HandleDrag, ajoutez le code suivant pour le contrôleur :

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

    Le code ci-dessus sera tout d’abord vérifier l’état de la reconnaissance de mouvement, puis déplacez l’image autour de l’écran. Avec ce code en place, le contrôleur peut désormais en charge en faisant glisser une image de l’écran.


1. Ajouter un `UITapGestureRecognizer` qui modifie l’image qui est affiché dans DoubleTouchImage. Ajoutez la méthode suivante à la `GestureViewController` contrôleur :

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

    Ce code est très similaire au code pour le `UIPanGestureRecognizer` mais au lieu d’utiliser un délégué pour une cible que nous utilisons un `Action`. 

1. La dernière chose que nous devons faire est de modifier `ViewDidLoad` afin qu’il appelle les méthodes que nous venons d’ajouter. Modifiez ViewDidLoad afin qu’il ressemble au code suivant :

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

    Notez également que nous initialisons la valeur de `originalImageFrame`.


1. Exécutez l’application et interagir avec les deux images.
La capture d’écran suivante est un exemple de ces interactions :
    
    [![](ios-touch-walkthrough-images/image7.png "Cette capture d’écran montre une interaction de glisser")](ios-touch-walkthrough-images/image7.png#lightbox)



<a name="Custom_Gesture_Recognizer"/>

## <a name="custom-gesture-recognizer"></a>Reconnaissance de mouvement personnalisée

Dans cette section, nous appliquons les concepts dans les sections précédentes pour créer un module de reconnaissance de mouvement personnalisée. La reconnaissance de mouvement personnalisé sera sous-classes `UIGestureRecognizer`et va reconnaître quand l’utilisateur dessine un « V » sur l’écran, puis basculer d’une image bitmap. La capture d’écran suivante est un exemple de cet écran :

 [![](ios-touch-walkthrough-images/image8.png "L’application reconnaît lorsque l’utilisateur dessine un « V » sur l’écran")](ios-touch-walkthrough-images/image8.png#lightbox)

Suivez ces étapes pour créer un module de reconnaissance de mouvement personnalisé :


1. Ajoutez une nouvelle classe au projet nommé `CheckmarkGestureRecognizer`et qu’elle ressemble à ce qui suit :

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

    La méthode Reset est appelée lorsque le `State` prend le propriété `Recognized` ou `Ended`. Il s’agit de l’heure pour réinitialiser n’importe quel état interne défini dans le module de reconnaissance de mouvement personnalisée.
La classe peut désormais rase prochaine fois que l’utilisateur interagit avec l’application et être prêt tenter une nouvelle reconnaissant le mouvement.



1. Maintenant que nous avons défini un module de reconnaissance de mouvement personnalisé (`CheckmarkGestureRecognizer`) modifier la **CustomGestureViewController.cs** fichier, puis ajoutez les variables de deux instance suivantes :

    ```csharp
    #region Private Variables
    private bool isChecked = false;
    private CheckmarkGestureRecognizer checkmarkGesture;
    #endregion
    ```

1. Pour instancier et configurer notre reconnaissance de mouvement, ajoutez la méthode suivante au contrôleur :

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

1. Modifier `ViewDidLoad` afin qu’il appelle `WireUpCheckmarkGestureRecognizer`, comme illustré dans l’extrait de code suivant :

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
    
        // Wire up the gesture recognizer
        WireUpCheckmarkGestureRecognizer();
    }
    ```

1. Exécutez l’application et essayez de dessiner un « V » sur l’écran. Vous devez voir l’image en cours affiche les modifications, comme indiqué dans les captures d’écran suivante :
    
    [![](ios-touch-walkthrough-images/image9.png "Le bouton activé")](ios-touch-walkthrough-images/image9.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image10.png "Le bouton non contrôlé")](ios-touch-walkthrough-images/image10.png#lightbox)



La méthode ci-dessus trois sections présentent les méthodes différentes pour répondre aux événements dans iOS tactiles : à l’aide des événements tactiles, les modules de reconnaissance de mouvement intégré, ou avec un module de reconnaissance de mouvement personnalisée.



## <a name="related-links"></a>Liens associés

- [iOS Touch Démarrer (exemple)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start)
- [iOS Touch finale (exemple)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final)
