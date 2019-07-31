---
title: Contrôles d’appareil photo manuels dans Xamarin. iOS
description: Ce document décrit comment l’infrastructure AVFoundation iOS peut être utilisée avec Xamarin. iOS pour activer les contrôles manuels de l’appareil photo. Les contrôles d’appareil photo manuels permettent à un utilisateur de contrôler les paramètres de focus, d’équilibre des blancs et d’exposition.
ms.prod: xamarin
ms.assetid: 56340225-5F3C-4BFC-9A79-61496D7FE5B5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 5230294dcacf6677e145dd8803d65841b3e22618
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655432"
---
# <a name="manual-camera-controls-in-xamarinios"></a>Contrôles d’appareil photo manuels dans Xamarin. iOS

Les contrôles manuels de l’appareil photo, `AVFoundation Framework` fournis par le dans iOS 8, permettent à une application mobile de prendre le contrôle total de l’appareil photo d’un appareil iOS. Ce niveau de contrôle affiné peut être utilisé pour créer des applications d’appareil photo de niveau professionnel et fournir des compositions d’artistes en modifiant les paramètres de l’appareil photo tout en utilisant une image ou une vidéo continue.

Ces contrôles peuvent également être utiles lors du développement d’applications scientifiques ou industrielles, où les résultats sont moins adaptés à l’exactitude ou la beauté de l’image, et qui sont destinés à mettre en évidence une fonctionnalité ou un élément de l’image en cours de prise.

## <a name="avfoundation-capture-objects"></a>Objets de capture AVFoundation

Que vous effectuiez des images vidéo ou continues à l’aide de l’appareil photo sur un appareil iOS, le processus utilisé pour capturer ces images est en grande partie identique. Cela est vrai pour les applications qui utilisent les contrôles d’appareil photo automatisés par défaut ou ceux qui tirent parti des nouveaux contrôles d’appareil photo manuels:

 [![](intro-to-manual-camera-controls-images/image1.png "Vue d’ensemble des objets de capture AVFoundation")](intro-to-manual-camera-controls-images/image1.png#lightbox)

L’entrée provient d’un `AVCaptureSession` `AVCaptureConnection`dansunau moyen d’un. `AVCaptureDeviceInput` Le résultat est la sortie en tant qu’image continue ou en tant que flux vidéo. L’ensemble du processus est contrôlé par `AVCaptureDevice`un.

## <a name="manual-controls-provided"></a>Contrôles manuels fournis

À l’aide des nouvelles API fournies par iOS 8, l’application peut prendre le contrôle des fonctionnalités suivantes de l’appareil photo:

-  **Focus manuel** : en permettant à l’utilisateur final de prendre le contrôle du focus directement, une application peut fournir davantage de contrôle sur l’image prise.
-  **Exposition manuelle** : en fournissant un contrôle manuel sur l’exposition, une application peut fournir une plus grande liberté aux utilisateurs et leur permettre d’obtenir un aspect stylisé.
-  **Balance des blancs manuelle** : l’équilibre des blancs est utilisé pour ajuster la couleur dans une image, souvent pour paraître réaliste. Différentes sources de lumière ont des températures de couleur différentes, et les paramètres d’appareil photo utilisés pour capturer une image sont ajustés pour compenser ces différences. Là encore, en autorisant l’utilisateur à contrôler l’équilibre des blancs, les utilisateurs peuvent effectuer des ajustements qui ne peuvent pas être effectués automatiquement.


iOS 8 fournit des extensions et des améliorations aux API iOS existantes pour fournir ce contrôle affiné sur le processus de capture d’image.

## <a name="bracketed-capture"></a>Capture entre crochets

La capture entre crochets est basée sur les paramètres des contrôles d’appareil photo manuels présentés ci-dessus et permet à l’application de capturer un moment dans le temps, de différentes manières.

Simplement indiqué, la capture entre crochets est une rafale d’images fixes prises avec divers paramètres, de l’image à l’image.

## <a name="requirements"></a>Configuration requise

Les éléments suivants sont requis pour effectuer les étapes présentées dans cet article:

-  **Xcode 7 + et iOS 8 ou version ultérieure** : les API Xcode 7 et iOS 8 d’Apple et les API plus récentes doivent être installées et configurées sur l’ordinateur du développeur.
-  **Visual Studio pour Mac** : la dernière version de Visual Studio pour Mac doit être installée et configurée sur l’appareil de l’utilisateur.
-  **appareil iOS 8** : un appareil iOS exécutant la dernière version d’iOS 8. Les fonctionnalités de l’appareil photo ne peuvent pas être testées dans iOS Simulator.


## <a name="general-av-capture-setup"></a>Configuration de capture AV générale

Lors de l’enregistrement d’une vidéo sur un appareil iOS, un code d’installation général est toujours requis. Cette section traite de la configuration minimale requise pour enregistrer la vidéo à partir de l’appareil photo de l’appareil iOS et afficher cette vidéo en temps réel `UIImageView`dans un.

### <a name="output-sample-buffer-delegate"></a>Exemple de mémoire tampon déléguée de sortie

L’une des premières choses nécessaires est un délégué pour surveiller l’exemple de mémoire tampon de sortie et afficher une image extraite de la mémoire `UIImageView` tampon vers une dans l’interface utilisateur de l’application.

La routine suivante analyse l’exemple de mémoire tampon et met à jour l’interface utilisateur:

```csharp
using System;
using Foundation;
using UIKit;
using System.CodeDom.Compiler;
using System.Collections.Generic;
using System.Linq;
using AVFoundation;
using CoreVideo;
using CoreMedia;
using CoreGraphics;

namespace ManualCameraControls
{
    public class OutputRecorder : AVCaptureVideoDataOutputSampleBufferDelegate
    {
        #region Computed Properties
        public UIImageView DisplayView { get; set; }
        #endregion

        #region Constructors
        public OutputRecorder ()
        {

        }
        #endregion

        #region Private Methods
        private UIImage GetImageFromSampleBuffer(CMSampleBuffer sampleBuffer) {

            // Get a pixel buffer from the sample buffer
            using (var pixelBuffer = sampleBuffer.GetImageBuffer () as CVPixelBuffer) {
                // Lock the base address
                pixelBuffer.Lock (0);

                // Prepare to decode buffer
                var flags = CGBitmapFlags.PremultipliedFirst | CGBitmapFlags.ByteOrder32Little;

                // Decode buffer - Create a new colorspace
                using (var cs = CGColorSpace.CreateDeviceRGB ()) {

                    // Create new context from buffer
                    using (var context = new CGBitmapContext (pixelBuffer.BaseAddress,
                        pixelBuffer.Width,
                        pixelBuffer.Height,
                        8,
                        pixelBuffer.BytesPerRow,
                        cs,
                        (CGImageAlphaInfo)flags)) {

                        // Get the image from the context
                        using (var cgImage = context.ToImage ()) {

                            // Unlock and return image
                            pixelBuffer.Unlock (0);
                            return UIImage.FromImage (cgImage);
                        }
                    }
                }
            }
        }
        #endregion

        #region Override Methods
        public override void DidOutputSampleBuffer (AVCaptureOutput captureOutput, CMSampleBuffer sampleBuffer, AVCaptureConnection connection)
        {
            // Trap all errors
            try {
                // Grab an image from the buffer
                var image = GetImageFromSampleBuffer(sampleBuffer);

                // Display the image
                if (DisplayView !=null) {
                    DisplayView.BeginInvokeOnMainThread(() => {
                        // Set the image
                        if (DisplayView.Image != null) DisplayView.Image.Dispose();
                        DisplayView.Image = image;

                        // Rotate image to the correct display orientation
                        DisplayView.Transform = CGAffineTransform.MakeRotation((float)Math.PI/2);
                    });
                }

                // IMPORTANT: You must release the buffer because AVFoundation has a fixed number
                // of buffers and will stop delivering frames if it runs out.
                sampleBuffer.Dispose();
            }
            catch(Exception e) {
                // Report error
                Console.WriteLine ("Error sampling buffer: {0}", e.Message);
            }
        }
        #endregion
    }
}
```

Une fois cette routine en place, `AppDelegate` vous pouvez modifier l’pour ouvrir une session de capture AV afin d’enregistrer un flux vidéo en direct.

### <a name="creating-an-av-capture-session"></a>Création d’une session de capture AV

La session de capture AV est utilisée pour contrôler l’enregistrement de la vidéo en direct à partir de l’appareil photo de l’appareil iOS et est nécessaire pour obtenir de la vidéo dans une application iOS. Étant donné que `ManualCameraControl` l’exemple d’application utilise la session de capture à différents emplacements, il sera configuré dans le `AppDelegate` et mis à la disposition de l’application entière.

Procédez comme suit pour modifier l’application `AppDelegate` et ajouter le code nécessaire:


1. Double-cliquez sur `AppDelegate.cs` le fichier dans le Explorateur de solutions pour l’ouvrir et le modifier.
1. Ajoutez les instructions using suivantes au début du fichier:
    
    ```
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    ```

1. Ajoutez les variables privées et les propriétés calculées suivantes à `AppDelegate` la classe:
    
    ```
    #region Private Variables
    private NSError Error;
    #endregion
    
    #region Computed Properties
    public override UIWindow Window {get;set;}
    public bool CameraAvailable { get; set; }
    public AVCaptureSession Session { get; set; }
    public AVCaptureDevice CaptureDevice { get; set; }
    public OutputRecorder Recorder { get; set; }
    public DispatchQueue Queue { get; set; }
    public AVCaptureDeviceInput Input { get; set; }
    #endregion
    ```
  
1. Remplacez la méthode terminée et remplacez-la par:
    
    ```
    public override void FinishedLaunching (UIApplication application)
    {
        // Create a new capture session
        Session = new AVCaptureSession ();
        Session.SessionPreset = AVCaptureSession.PresetMedium;
    
        // Create a device input
        CaptureDevice = AVCaptureDevice.DefaultDeviceWithMediaType (AVMediaType.Video);
        if (CaptureDevice == null) {
            // Video capture not supported, abort
            Console.WriteLine ("Video recording not supported on this device");
            CameraAvailable = false;
            return;
        }
    
        // Prepare device for configuration
        CaptureDevice.LockForConfiguration (out Error);
        if (Error != null) {
            // There has been an issue, abort
            Console.WriteLine ("Error: {0}", Error.LocalizedDescription);
            CaptureDevice.UnlockForConfiguration ();
            return;
        }
    
        // Configure stream for 15 frames per second (fps)
        CaptureDevice.ActiveVideoMinFrameDuration = new CMTime (1, 15);
    
        // Unlock configuration
        CaptureDevice.UnlockForConfiguration ();
    
        // Get input from capture device
        Input = AVCaptureDeviceInput.FromDevice (CaptureDevice);
        if (Input == null) {
            // Error, report and abort
            Console.WriteLine ("Unable to gain input from capture device.");
            CameraAvailable = false;
            return;
        }
    
        // Attach input to session
        Session.AddInput (Input);
    
        // Create a new output
        var output = new AVCaptureVideoDataOutput ();
        var settings = new AVVideoSettingsUncompressed ();
        settings.PixelFormatType = CVPixelFormatType.CV32BGRA;
        output.WeakVideoSettings = settings.Dictionary;
    
        // Configure and attach to the output to the session
        Queue = new DispatchQueue ("ManCamQueue");
        Recorder = new OutputRecorder ();
        output.SetSampleBufferDelegate (Recorder, Queue);
        Session.AddOutput (output);
    
        // Let tabs know that a camera is available
        CameraAvailable = true;
    }
    ```  
  
1. Enregistrez les modifications dans le fichier.


Avec ce code en place, vous pouvez facilement implémenter les contrôles d’appareil photo manuels pour l’expérimentation et le test.

## <a name="manual-focus"></a>Focus manuel

En autorisant l’utilisateur final à prendre directement les contrôles du focus, une application peut fournir un contrôle plus artistique sur l’image prise.

Par exemple, un photographe professionnel peut atténuer le focus d’une image pour obtenir un [effet bokeh](https://en.wikipedia.org/wiki/Bokeh):

[![](intro-to-manual-camera-controls-images/image2.png "Un effet bokeh")](intro-to-manual-camera-controls-images/image2.png#lightbox)

Ou créez un [effet d’extraction de focus](http://www.mediacollege.com/video/camera/focus/pull.html), par exemple:

[![](intro-to-manual-camera-controls-images/image3.png "Effet d’extraction du focus")](intro-to-manual-camera-controls-images/image3.png#lightbox)

Pour les scientifiques ou un scripteur d’applications médicales, l’application peut déplacer par programmation la lentille pour les expériences. Dans les deux cas, la nouvelle API permet à l’utilisateur final ou à l’application de prendre le contrôle du focus au moment où l’image est prise.

### <a name="how-focus-works"></a>Fonctionnement du focus

Avant de discuter des détails relatifs au contrôle du focus dans une application IOS 8. Voyons rapidement comment le focus fonctionne dans un appareil iOS:

[![](intro-to-manual-camera-controls-images/image4.png "Fonctionnement du focus dans un appareil iOS")](intro-to-manual-camera-controls-images/image4.png#lightbox)

Light entre l’objectif de l’appareil photo sur l’appareil iOS et se focalise sur un capteur d’images. Distance de la lentille à partir des contrôles de capteur où le point focal (la zone où l’image apparaît le plus clair) est, par rapport au capteur. Plus la lentille est éloignée du capteur, plus les objets de distance semblent le plus clair et plus les objets proches semblent aigus.

Dans un appareil iOS, l’objectif est plus proche ou plus éloigné du capteur par les aimants et les ressorts. Par conséquent, le positionnement exact de l’objectif est impossible, car il varie d’un appareil à l’appareil et peut être affecté par des paramètres tels que l’orientation de l’appareil ou l’âge de l’appareil et le ressort.

### <a name="important-focus-terms"></a>Conditions de focalisation importantes

Lorsque vous vous concentrez sur le focus, le développeur doit connaître les conditions suivantes:

-  **Profondeur de champ** : distance entre les objets in-Focus les plus proches et les plus éloignés. 
-  **Macro** : il s’agit de la plus proche de la gamme de focalisation et est la distance la plus proche à laquelle la lentille peut se concentrer.
-  **Infinity** : il s’agit de la fin du spectre de focus et de la distance la plus éloignée à laquelle la lentille peut se concentrer.
-  **Distance hyperfocal** : il s’agit du point dans le spectre de focus où l’objet le plus éloigné dans le cadre est juste à la fin de la concentration. En d’autres termes, il s’agit de la position focale qui maximise la profondeur du champ. 
-  **Position** de l’objectif: c’est ce qui contrôle tous les autres termes ci-dessus. Il s’agit de la distance de la lentille par rapport au capteur et, par conséquent, du contrôleur de focus.


Avec ces termes et connaissances à l’esprit, les nouveaux contrôles de focus manuel peuvent être implémentés avec succès dans une application iOS 8.

### <a name="existing-focus-controls"></a>Contrôles de focus existants

iOS 7 et versions antérieures, à condition que les contrôles de `FocusMode`Focus existants via la propriété soient les suivants:

-   `AVCaptureFocusModeLocked`: Le focus est verrouillé à un point de concentration unique.
-   `AVCaptureFocusModeAutoFocus`: L’appareil photo balaye l’objectif à travers tous les points de référence jusqu’à ce qu’il trouve un focus aigu, puis le reste.
-   `AVCaptureFocusModeContinuousAutoFocus`: L’appareil photo se focalise à chaque fois qu’il détecte une condition de non-focus.


Les contrôles existants offraient également un point d’intérêt définissable via`FocusPointOfInterest` la propriété, afin que l’utilisateur puisse cliquer pour se concentrer sur une zone particulière. L’application peut également suivre le mouvement de la lentille en `IsAdjustingFocus` surveillant la propriété.

En outre, une restriction de plage a été `AutoFocusRangeRestriction` fournie par la propriété en tant que:

-   `AVCaptureAutoFocusRangeRestrictionNear`: Limite l’autofocus à des profondeurs proches. Utile dans des situations telles que l’analyse d’un code QR ou d’un code-barres.
-   `AVCaptureAutoFocusRangeRestrictionFar`: Limite le focus à la profondeur distante. Utile dans les situations où les objets qui ne sont pas pertinents se trouvent dans le champ de vue (par exemple, un cadre de fenêtre).


Enfin, il existe `SmoothAutoFocus` une propriété qui ralentit l’algorithme de focus automatique et la détaille en incréments plus petits pour éviter de déplacer des artefacts lors de l’enregistrement de vidéos.

### <a name="new-focus-controls-in-ios-8"></a>Nouveaux contrôles de focus dans iOS 8

Outre les fonctionnalités déjà fournies par iOS 7 et versions ultérieures, les fonctionnalités suivantes sont désormais disponibles pour contrôler le focus dans iOS 8:

-  Contrôle manuel complet de la position de l’objectif lors du verrouillage du focus.
-  Observation de la valeur clé de la position de l’objectif dans n’importe quel mode focus.


Pour implémenter les fonctionnalités ci- `AVCaptureDevice` dessus, la classe a été modifiée de façon à `LensPosition` inclure une propriété en lecture seule utilisée pour obtenir la position actuelle de l’objectif de l’appareil photo.

Pour prendre le contrôle manuel de la position de l’objectif, le périphérique de capture doit être en mode Focus verrouillé. Exemple :

 `CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;`

La `SetFocusModeLocked` méthode du périphérique de capture est utilisée pour ajuster la position de l’objectif de l’appareil photo. Une routine de rappel facultative peut être fournie pour recevoir une notification lorsque la modification prend effet. Exemple :

```csharp
ThisApp.CaptureDevice.LockForConfiguration(out Error);
ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
ThisApp.CaptureDevice.UnlockForConfiguration();
```

Comme indiqué dans le code ci-dessus, l’appareil de capture doit être verrouillé pour la configuration avant qu’une modification de la position de l’objectif puisse être effectuée. Les valeurs valides de position d’objectif sont comprises entre 0,0 et 1,0.

### <a name="manual-focus-example"></a>Exemple de focus manuel

Une fois le code d’installation de capture AV général en `UIViewController` place, un peut être ajouté à la table de montage séquentiel de l’application et configuré comme suit:

[![](intro-to-manual-camera-controls-images/image5.png "Un UIViewController peut être ajouté à la table de montage séquentiel applications et configuré comme indiqué ici")](intro-to-manual-camera-controls-images/image5.png#lightbox)

La vue contient les éléments principaux suivants:

-  `UIImageView` Qui affichera le flux vidéo.
-  `UISegmentedControl` Qui va remplacer le mode de focus automatique par le mode verrouillé.
-  `UISlider` Qui affiche et met à jour la position de l’objectif actuel.


Procédez comme suit pour connecter le contrôleur d’affichage pour le contrôle de focus manuel:


1. Ajoutez les instructions using suivantes:

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```  
  
1. Ajoutez les variables privées suivantes:

    ```csharp
    #region Private Variables
    private NSError Error;
    private bool Automatic = true;
    #endregion
    ```  
  
1. Ajoutez les propriétés calculées suivantes:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. Substituez la `ViewDidLoad` méthode et ajoutez le code suivant:

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
    
        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;
    
        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;
    
        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Update position slider
            Position.BeginInvokeOnMainThread(() =>{
                Position.Value = ThisApp.Input.Device.LensPosition;
            });
        };
    
        // Watch for value changes
        Segments.ValueChanged += (object sender, EventArgs e) => {
    
            // Lock device for change
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
    
            // Take action based on the segment selected
            switch(Segments.SelectedSegment) {
            case 0:
                // Activate auto focus and start monitoring position
                Position.Enabled = false;
                ThisApp.CaptureDevice.FocusMode = AVCaptureFocusMode.ContinuousAutoFocus;
                SampleTimer.Start();
                Automatic = true;
                break;
            case 1:
                // Stop auto focus and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;
                Automatic = false;
                Position.Enabled = true;
                break;
            }
    
            // Unlock device
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        // Monitor position changes
        Position.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            if (Automatic) return;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    }
    ```  
  
1. Substituez la `ViewDidAppear` méthode et ajoutez le code suivant pour démarrer l’enregistrement lorsque la vue se charge:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```  
  
1. Avec l’appareil photo en mode auto, le curseur se déplace automatiquement à mesure que l’appareil photo ajuste le focus:

    [![](intro-to-manual-camera-controls-images/image6.png "Le curseur se déplace automatiquement à mesure que la caméra ajuste le focus dans cet exemple d’application")](intro-to-manual-camera-controls-images/image6.png#lightbox)
1. Appuyez sur le segment verrouillé, puis faites glisser le curseur position pour ajuster la position de l’objectif manuellement:

    [![](intro-to-manual-camera-controls-images/image7.png "Réglage manuel de la position de l’objectif")](intro-to-manual-camera-controls-images/image7.png#lightbox)
1. Arrêtez l’application.


Le code ci-dessus montre comment surveiller la position de l’objectif quand l’appareil photo est en mode automatique ou utiliser un curseur pour contrôler la position de l’objectif lorsqu’il est en mode verrouillé.

## <a name="manual-exposure"></a>Exposition manuelle

L’exposition fait référence à la luminosité d’une image par rapport à la luminosité source et est déterminée par la quantité d’éclairage atteint le capteur, pendant la durée et par le niveau de gain du capteur (mappage ISO). En fournissant un contrôle manuel sur l’exposition, une application peut fournir plus de liberté à l’utilisateur final et lui permettre d’obtenir un aspect stylisé.

À l’aide des contrôles d’exposition manuelle, l’utilisateur peut prendre une image de très forte luminosité et Moody:

[![](intro-to-manual-camera-controls-images/image8.png "Un exemple d’image qui montre l’exposition de la luminosité et de la Moody")](intro-to-manual-camera-controls-images/image8.png#lightbox)

Là encore, cette opération peut être effectuée automatiquement à l’aide du contrôle par programmation pour les applications scientifiques ou via des contrôles manuels fournis par l’interface utilisateur des applications. Dans les deux cas, les nouvelles API d’exposition iOS 8 offrent un contrôle affiné sur les paramètres d’exposition de l’appareil photo.

### <a name="how-exposure-works"></a>Fonctionnement de l’exposition

Avant de discuter des détails relatifs au contrôle de l’exposition dans une application IOS 8. Jetons un coup d’œil rapide sur le fonctionnement de l’exposition:

[![](intro-to-manual-camera-controls-images/image9.png "Fonctionnement de l’exposition")](intro-to-manual-camera-controls-images/image9.png#lightbox)

Les trois éléments de base qui se combinent pour contrôler l’exposition sont les suivants:

-  **Vitesse d’obturation** : il s’agit de la durée pendant laquelle l’obturateur est ouvert pour laisser la lumière sur le capteur d’appareil photo. Plus la durée d’ouverture de l’obturateur est faible, plus la lumière est faible et plus l’image est nette (moins de flou de mouvement). Plus l’obturateur est ouvert, plus la lumière est longue et plus le flou de mouvement est grand.
-  **Mappage ISO** : il s’agit d’un terme emprunté par film photographie et qui fait référence à la sensibilité des produits chimiques dans le film à la lumière. Les valeurs ISO faibles du film ont moins de grain et une reproduction plus fine des couleurs; les valeurs ISO faibles sur les capteurs numériques ont moins de bruit de capteur mais moins de luminosité. Plus la valeur ISO est élevée, plus l’image est brillante, mais avec plus de bruit de capteur. «ISO» sur un capteur numérique est une mesure de [gain électronique](https://en.wikipedia.org/wiki/Gain), et non une fonctionnalité physique. 
-  **Ouverture** de l’objectif: il s’agit de la taille de l’ouverture de la lentille. Sur tous les appareils iOS, l’ouverture de l’objectif est fixe, donc les seules deux valeurs qui peuvent être utilisées pour ajuster l’exposition sont vitesse d’obturation et ISO.


### <a name="how-continuous-auto-exposure-works"></a>Fonctionnement de l’exposition continue

Avant de découvrir le fonctionnement de l’exposition manuelle, il est judicieux de comprendre le fonctionnement de l’exposition automatique continue dans un appareil iOS.

[![](intro-to-manual-camera-controls-images/image10.png "Fonctionnement de l’exposition automatique continue dans un appareil iOS")](intro-to-manual-camera-controls-images/image10.png#lightbox)

Tout d’abord est le bloc d’exposition automatique, il a le travail de calcul de l’exposition idéale et est en train d’alimenter les statistiques de contrôle. Il utilise ces informations pour calculer le mélange optimal de la vitesse ISO et de la vitesse d’obturation pour que la scène soit bien éclairée. Ce cycle est appelé «boucle AE».

### <a name="how-locked-exposure-works"></a>Fonctionnement de l’exposition verrouillée

Ensuite, examinons le fonctionnement de l’exposition verrouillée dans les appareils iOS.

[![](intro-to-manual-camera-controls-images/image11.png "Fonctionnement de l’exposition verrouillée dans les appareils iOS")](intro-to-manual-camera-controls-images/image11.png#lightbox)

Là encore, vous disposez du bloc d’exposition automatique qui tente de calculer les valeurs d’iOS et de durée optimales. Toutefois, dans ce mode, le bloc AE est déconnecté du moteur de statistiques de contrôle.

### <a name="existing-exposure-controls"></a>Contrôles d’exposition existants

iOS 7 et versions ultérieures, fournissez les contrôles d’exposition `ExposureMode` existants suivants via la propriété:

-   `AVCaptureExposureModeLocked`: Échantillonne la scène une fois et utilise ces valeurs tout au long de la scène.
-   `AVCaptureExposureModeContinuousAutoExposure`: Échantillonne en continu la scène pour s’assurer qu’elle est bien éclairée.


Peut être utilisé pour exposer la scène en sélectionnant un objet cible à exposer, et l’application peut surveiller la `AdjustingExposure` propriété pour voir quand l’exposition est ajustée. `ExposurePointOfInterest`

### <a name="new-exposure-controls-in-ios-8"></a>Nouveaux contrôles d’exposition dans iOS 8

Outre les fonctionnalités déjà fournies par iOS 7 et versions ultérieures, les fonctionnalités suivantes sont désormais disponibles pour contrôler l’exposition dans iOS 8:

-  Exposition personnalisée entièrement manuelle.
-  Obtient, définit et clé-valeur observent IOS et la vitesse d’obturation (durée).


Pour implémenter les fonctionnalités ci-dessus `AVCaptureExposureModeCustom` , un nouveau mode a été ajouté. Lorsque l’appareil photo dans est le mode personnalisé, le code suivant peut être utilisé pour ajuster la durée d’exposition et ISO:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.LockExposure(DurationValue,ISOValue,null);
CaptureDevice.UnlockForConfiguration();
```

Dans les modes auto et Locked, l’application peut ajuster le décalage de la routine d’exposition automatique à l’aide du code suivant:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.SetExposureTargetBias(Value,null);
CaptureDevice.UnlockForConfiguration();
```

Les plages de paramètres minimale et maximale dépendent du périphérique sur lequel l’application s’exécute, donc ils ne doivent jamais être codés en dur. Au lieu de cela, utilisez les propriétés suivantes pour récupérer les plages de valeurs minimale et maximale:

-   `CaptureDevice.MinExposureTargetBias` 
-   `CaptureDevice.MaxExposureTargetBias` 
-   `CaptureDevice.ActiveFormat.MinISO` 
-   `CaptureDevice.ActiveFormat.MaxISO` 
-   `CaptureDevice.ActiveFormat.MinExposureDuration` 
-   `CaptureDevice.ActiveFormat.MaxExposureDuration` 


Comme indiqué dans le code ci-dessus, l’appareil de capture doit être verrouillé pour la configuration avant qu’une modification de l’exposition puisse être apportée.

### <a name="manual-exposure-example"></a>Exemple d’exposition manuelle

Une fois le code d’installation de capture AV général en `UIViewController` place, un peut être ajouté à la table de montage séquentiel de l’application et configuré comme suit:

[![](intro-to-manual-camera-controls-images/image12.png "Un UIViewController peut être ajouté à la table de montage séquentiel applications et configuré comme indiqué ici")](intro-to-manual-camera-controls-images/image12.png#lightbox)

La vue contient les éléments principaux suivants:

-  `UIImageView` Qui affichera le flux vidéo.
-  `UISegmentedControl` Qui va remplacer le mode de focus automatique par le mode verrouillé.
-  Quatre `UISlider` contrôles qui affichent et mettent à jour le décalage, la durée, l’ISO et le biais.


Procédez comme suit pour connecter le contrôleur d’affichage pour le contrôle de l’exposition manuelle:


1. Ajoutez les instructions using suivantes:
    
    ```
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```  
  
1. Ajoutez les variables privées suivantes:

    ```csharp
    #region Private Variables
    private NSError Error; 
    private bool Automatic = true;
    private nfloat ExposureDurationPower = 5;
    private nfloat ExposureMinimumDuration = 1.0f/1000.0f;
    #endregion
    ```  
  
1. Ajoutez les propriétés calculées suivantes:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. Substituez la `ViewDidLoad` méthode et ajoutez le code suivant:

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
    
        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;
    
        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;
    
        // Set min and max values
        Offset.MinValue = ThisApp.CaptureDevice.MinExposureTargetBias;
        Offset.MaxValue = ThisApp.CaptureDevice.MaxExposureTargetBias;
    
        Duration.MinValue = 0.0f;
        Duration.MaxValue = 1.0f;
    
        ISO.MinValue = ThisApp.CaptureDevice.ActiveFormat.MinISO;
        ISO.MaxValue = ThisApp.CaptureDevice.ActiveFormat.MaxISO;
    
        Bias.MinValue = ThisApp.CaptureDevice.MinExposureTargetBias;
        Bias.MaxValue = ThisApp.CaptureDevice.MaxExposureTargetBias;
    
        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Update position slider
            Offset.BeginInvokeOnMainThread(() =>{
                Offset.Value = ThisApp.Input.Device.ExposureTargetOffset;
            });
    
            Duration.BeginInvokeOnMainThread(() =>{
                var newDurationSeconds = CMTimeGetSeconds(ThisApp.Input.Device.ExposureDuration);
                var minDurationSeconds = Math.Max(CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MinExposureDuration), ExposureMinimumDuration);
                var maxDurationSeconds = CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MaxExposureDuration);
                var p = (newDurationSeconds - minDurationSeconds) / (maxDurationSeconds - minDurationSeconds);
                Duration.Value = (float)Math.Pow(p, 1.0f/ExposureDurationPower);
            });
    
            ISO.BeginInvokeOnMainThread(() => {
                ISO.Value = ThisApp.Input.Device.ISO;
            });
    
            Bias.BeginInvokeOnMainThread(() => {
                Bias.Value = ThisApp.Input.Device.ExposureTargetBias;
            });
        };
    
        // Watch for value changes
        Segments.ValueChanged += (object sender, EventArgs e) => {
    
            // Lock device for change
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
    
            // Take action based on the segment selected
            switch(Segments.SelectedSegment) {
            case 0:
                // Activate auto exposure and start monitoring position
                Duration.Enabled = false;
                ISO.Enabled = false;
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.ContinuousAutoExposure;
                SampleTimer.Start();
                Automatic = true;
                break;
            case 1:
                // Lock exposure and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.Locked;
                Automatic = false;
                Duration.Enabled = false;
                ISO.Enabled = false;
                break;
            case 2:
                // Custom exposure and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.Custom;
                Automatic = false;
                Duration.Enabled = true;
                ISO.Enabled = true;
                break;
            }
    
            // Unlock device
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        // Monitor position changes
        Duration.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            if (Automatic) return;
    
            // Calculate value
            var p = Math.Pow(Duration.Value,ExposureDurationPower);
            var minDurationSeconds = Math.Max(CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MinExposureDuration),ExposureMinimumDuration);
            var maxDurationSeconds = CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MaxExposureDuration);
            var newDurationSeconds = p * (maxDurationSeconds - minDurationSeconds) +minDurationSeconds;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.LockExposure(CMTime.FromSeconds(p,1000*1000*1000),ThisApp.CaptureDevice.ISO,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        ISO.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            if (Automatic) return;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.LockExposure(ThisApp.CaptureDevice.ExposureDuration,ISO.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        Bias.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            // if (Automatic) return;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.SetExposureTargetBias(Bias.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    }
    ```  
  
1. Substituez la `ViewDidAppear` méthode et ajoutez le code suivant pour démarrer l’enregistrement lorsque la vue se charge:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```  
  
1. Une fois l’appareil photo en mode auto, les curseurs se déplacent automatiquement à mesure que l’appareil photo ajuste l’exposition:

    [![](intro-to-manual-camera-controls-images/image13.png "Les curseurs se déplaceront automatiquement à mesure que l’appareil photo ajuste l’exposition")](intro-to-manual-camera-controls-images/image13.png#lightbox)
1. Appuyez sur le segment verrouillé, puis faites glisser le curseur décalage pour ajuster manuellement le décalage de l’exposition automatique:

    [![](intro-to-manual-camera-controls-images/image14.png "Ajustement manuel de l’écart de l’exposition automatique")](intro-to-manual-camera-controls-images/image14.png#lightbox)
1. Appuyez sur le segment personnalisé et faites glisser les curseurs durée et ISO pour contrôler manuellement l’exposition:

    [![](intro-to-manual-camera-controls-images/image15.png "Faites glisser les curseurs durée et ISO pour contrôler manuellement l’exposition")](intro-to-manual-camera-controls-images/image15.png#lightbox)
1. Arrêtez l’application.


Le code ci-dessus montre comment surveiller les paramètres d’exposition lorsque l’appareil photo est en mode automatique, et comment utiliser des curseurs pour contrôler l’exposition lorsqu’il est en mode verrouillé ou personnalisé.

## <a name="manual-white-balance"></a>Balance des blancs manuelle

Les contrôles d’équilibre des blancs permettent aux utilisateurs d’ajuster le solde des colosr dans une image afin de les rendre plus réalistes. Différentes sources de lumière ont des températures de couleur différentes, et les paramètres d’appareil photo utilisés pour capturer une image doivent être ajustés pour compenser ces différences. Là encore, en permettant à l’utilisateur de contrôler l’équilibre des blancs, il peut effectuer des ajustements professionnels que les routines automatiques sont incapables d’obtenir des effets artistiques.

[![](intro-to-manual-camera-controls-images/image16.png "Exemple d’image présentant les ajustements manuels des soldes blancs")](intro-to-manual-camera-controls-images/image16.png#lightbox)

Par exemple, l’heure d’été a un cast bleu, alors que les lumières incandescentes du tungstène ont une teinte jaune-orange plus chaude. (En toute confusion, les couleurs «froides» ont des températures de couleur supérieures à celles des couleurs «chaudes». Les températures de couleur sont une mesure physique, et non un percepteur.

L’esprit humain est très efficace lorsqu’il s’agit de compenser les différences de température de couleur, mais ce n’est pas le cas d’une caméra. L’appareil photo fonctionne en augmentant la couleur sur le spectre opposé pour ajuster les différences de couleur.

La nouvelle API d’exposition iOS 8 permet à l’application de prendre le contrôle du processus et de fournir un contrôle précis sur les paramètres d’équilibre des blancs de la caméra.

### <a name="how-white-balance-works"></a>Fonctionnement de l’équilibre des blancs

Avant de discuter des détails relatifs au contrôle de l’équilibre des blancs dans une application IOS 8. Examinons rapidement le fonctionnement de l’équilibre des blancs:

Dans l’étude de la perception des couleurs, l’espace colorimétrique [cie 1931 RVB et l’espace de couleurs cie 1931 XYZ](https://en.wikipedia.org/wiki/CIE_1931_color_space) sont les premiers espaces colorimétriques définis par mathématique. Ils ont été créés par la Commission internationale de l’éclairage (CIE) dans 1931.

[![](intro-to-manual-camera-controls-images/image17.png "Espace de couleurs CIE 1931 RVB et espace de couleurs CIE 1931 XYZ")](intro-to-manual-camera-controls-images/image17.png#lightbox)

Le graphique ci-dessus montre toutes les couleurs visibles à l’oeil humain, du bleu profond au vert vif au rouge vif. Tout point du diagramme peut être tracé avec une valeur X et Y, comme le montre le graphique ci-dessus.

Comme visible dans le graphique, il y a des valeurs X et Y qui peuvent être tracées sur le graphique, qui seraient en dehors de la plage de la vision humaine. par conséquent, ces couleurs ne peuvent pas être reproduites par une caméra.

La plus petite courbe dans le graphique ci-dessus est appelée [locus de Planck](https://en.wikipedia.org/wiki/Planckian_locus), qui exprime la température de couleur (en degrés Kelvin), avec des nombres supérieurs sur le côté bleu (plus chaud) et des nombres inférieurs sur le côté rouge (refroidisseur). Elles sont utiles pour les situations d’éclairage classiques.

Dans les conditions d’éclairage mixte, les réglages de l’équilibre blanc doivent s’écarter du locus du Planck pour apporter les modifications requises. Dans ces situations, l’ajustement doit être décalé vers le côté vert ou rouge/Magenta de l’échelle CIE.

les appareils iOS compensent les conversions en couleurs en amplifiant le gain de couleurs opposé. Par exemple, si une scène est trop bleue, le gain rouge sera augmenté pour compenser. Ces valeurs de gain sont calibrées pour des appareils spécifiques et dépendent de l’appareil.

### <a name="existing-white-balance-controls"></a>Contrôles d’équilibre des blancs existants

iOS 7 et versions ultérieures fournissait les contrôles d’équilibre `WhiteBalanceMode` des blancs existants suivants via la propriété:

-   `AVCapture WhiteBalance ModeLocked`: Échantillonne la scène une seule fois et en utilisant ces valeurs tout au long de la scène.
-   `AVCapture WhiteBalance ModeContinuousAutoExposure`: Échantillonne en continu la scène pour s’assurer qu’elle est bien équilibrée.


Et l’application peut surveiller la `AdjustingWhiteBalance` propriété pour voir quand l’exposition est ajustée.

### <a name="new-white-balance-controls-in-ios-8"></a>Nouveaux contrôles d’équilibre des blancs dans iOS 8

Outre les fonctionnalités déjà fournies par iOS 7 et versions ultérieures, les fonctionnalités suivantes sont désormais disponibles pour contrôler l’équilibre des blancs dans iOS 8:

-  Contrôle total manuel des gains RGB de l’appareil.
-  L’obtention, la définition et la valeur de clé observent les gains RGB de l’appareil.
-  Prise en charge de l’équilibre des blancs à l’aide d’une carte grise.
-  Routines de conversion vers et à partir d’espaces de couleurs indépendants du périphérique.


Pour implémenter les fonctionnalités ci- `AVCaptureWhiteBalanceGain` dessus, la structure a été ajoutée avec les membres suivants:

-   `RedGain` 
-   `GreenGain` 
-   `BlueGain` 


Le gain de solde blanc maximal est actuellement de quatre (4) et peut être prêt `MaxWhiteBalanceGain` à partir de la propriété. Par conséquent, la plage légale va de 1 à `MaxWhiteBalanceGain` (4) actuellement.

La `DeviceWhiteBalanceGains` propriété peut être utilisée pour observer les valeurs actuelles. Utilisez `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains` pour ajuster les gains de balance lorsque l’appareil photo est dans le mode de balance des blancs verrouillé.

#### <a name="conversion-routines"></a>Routines de conversion

Des routines de conversion ont été ajoutées à iOS 8 pour faciliter la conversion vers et depuis les espaces de couleurs indépendants des appareils. Pour implémenter les routines de conversion `AVCaptureWhiteBalanceChromaticityValues` , la structure a été ajoutée avec les membres suivants:

-   `X`-est une valeur comprise entre 0 et 1.
-   `Y`-est une valeur comprise entre 0 et 1.


Une `AVCaptureWhiteBalanceTemperatureAndTintValues` structure a également été ajoutée avec les membres suivants:

-   `Temperature`-est une valeur à virgule flottante en degrés Kelvin.
-   `Tint`-décalage par rapport au vert ou magenta de 0 à 150 avec des valeurs positives vers le sens vert et négatif vers le magenta.


Utilisez les `CaptureDevice.GetTemperatureAndTintValues`méthodes et `CaptureDevice.GetDeviceWhiteBalanceGains`pour effectuer des conversions entre les espaces colorimétriques de température et de teinte, la chromatique et l’obtention RVB.

> [!NOTE]
> Les routines de conversion sont plus précises, plus la valeur à convertir est proche du locus du Planck.




#### <a name="gray-card-support"></a>Prise en charge des cartes grises

Apple utilise le terme «gris» pour faire référence à la prise en charge des cartes grises intégrée à iOS 8. Il permet à l’utilisateur de se concentrer sur une carte grise physique qui couvre au moins 50% du centre du cadre et l’utilise pour ajuster l’équilibre du blanc. L’objectif de la carte grise est d’obtenir un blanc qui semble neutre.

Cela peut être implémenté dans une application en invitant l’utilisateur à placer une carte grise physique devant l’appareil photo, en surveillant la `GrayWorldDeviceWhiteBalanceGains` propriété et en attendant jusqu’à ce que les valeurs soient réglées.

L’application verrouille alors les gains de balance blancs pour la `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains` méthode à l’aide des valeurs `GrayWorldDeviceWhiteBalanceGains` de la propriété pour appliquer les modifications.

L’appareil de capture doit être verrouillé pour la configuration avant qu’une modification de l’équilibre des blancs puisse être effectuée.

### <a name="manual-white-balance-example"></a>Exemple de balance des blancs manuelle

Une fois le code d’installation de capture AV général en `UIViewController` place, un peut être ajouté à la table de montage séquentiel de l’application et configuré comme suit:

[![](intro-to-manual-camera-controls-images/image18.png "Un UIViewController peut être ajouté à la table de montage séquentiel applications et configuré comme indiqué ici")](intro-to-manual-camera-controls-images/image18.png#lightbox)

La vue contient les éléments principaux suivants:

-  `UIImageView` Qui affichera le flux vidéo.
-  `UISegmentedControl` Qui va remplacer le mode de focus automatique par le mode verrouillé.
-  Deux `UISlider` contrôles qui affichent et mettent à jour la température et la teinte.
-  `UIButton` Utilisé pour échantillonner un espace de carte grise (monde gris) et définir l’équilibre blanc à l’aide de ces valeurs.


Procédez comme suit pour connecter le contrôleur d’affichage pour le contrôle manuel de l’équilibre des blancs:


1. Ajoutez les instructions using suivantes:

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```  
  
1. Ajoutez les variables privées suivantes:

    ```csharp
    #region Private Variables
    private NSError Error; 
    private bool Automatic = true;
    #endregion
    ```
  
1. Ajoutez les propriétés calculées suivantes:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. Ajoutez la méthode privée suivante pour définir la nouvelle température et la teinte de l’équilibre blanc:

    ```csharp
    #region Private Methods
    void SetTemperatureAndTint() {
        // Grab current temp and tint
        var TempAndTint = new AVCaptureWhiteBalanceTemperatureAndTintValues (Temperature.Value, Tint.Value);

        // Convert Color space
        var gains = ThisApp.CaptureDevice.GetDeviceWhiteBalanceGains (TempAndTint);

        // Set the new values
        if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {
            gains = NomralizeGains (gains);
            ThisApp.CaptureDevice.SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains (gains, null);
            ThisApp.CaptureDevice.UnlockForConfiguration ();
        }
    }
    
    AVCaptureWhiteBalanceGains NomralizeGains (AVCaptureWhiteBalanceGains gains)
    {
        gains.RedGain = Math.Max (1, gains.RedGain);
        gains.BlueGain = Math.Max (1, gains.BlueGain);
        gains.GreenGain = Math.Max (1, gains.GreenGain);

        float maxGain = ThisApp.CaptureDevice.MaxWhiteBalanceGain;
        gains.RedGain = Math.Min (maxGain, gains.RedGain);
        gains.BlueGain = Math.Min (maxGain, gains.BlueGain);
        gains.GreenGain = Math.Min (maxGain, gains.GreenGain);

        return gains;
    }
    #endregion
    ```   
  
1. Substituez la `ViewDidLoad` méthode et ajoutez le code suivant:

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;

        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;

        // Set min and max values
        Temperature.MinValue = 1000f;
        Temperature.MaxValue = 10000f;

        Tint.MinValue = -150f;
        Tint.MaxValue = 150f;

        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Convert color space
            var TempAndTint = ThisApp.CaptureDevice.GetTemperatureAndTintValues (ThisApp.CaptureDevice.DeviceWhiteBalanceGains);

            // Update slider positions
            Temperature.BeginInvokeOnMainThread (() => {
                Temperature.Value = TempAndTint.Temperature;
            });

            Tint.BeginInvokeOnMainThread (() => {
                Tint.Value = TempAndTint.Tint;
            });
        };

        // Watch for value changes
        Segments.ValueChanged += (sender, e) => {
            // Lock device for change
            if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {

                // Take action based on the segment selected
                switch (Segments.SelectedSegment) {
                case 0:
                // Activate auto focus and start monitoring position
                    Temperature.Enabled = false;
                    Tint.Enabled = false;
                    ThisApp.CaptureDevice.WhiteBalanceMode = AVCaptureWhiteBalanceMode.ContinuousAutoWhiteBalance;
                    SampleTimer.Start ();
                    Automatic = true;
                    break;
                case 1:
                // Stop auto focus and allow the user to control the camera
                    SampleTimer.Stop ();
                    ThisApp.CaptureDevice.WhiteBalanceMode = AVCaptureWhiteBalanceMode.Locked;
                    Automatic = false;
                    Temperature.Enabled = true;
                    Tint.Enabled = true;
                    break;
                }

                // Unlock device
                ThisApp.CaptureDevice.UnlockForConfiguration ();
            }
        };

        // Monitor position changes
        Temperature.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Update white balance
            SetTemperatureAndTint ();
        };

        Tint.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Update white balance
            SetTemperatureAndTint ();
        };

        GrayCardButton.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Get gray card values
            var gains = ThisApp.CaptureDevice.GrayWorldDeviceWhiteBalanceGains;

            // Set the new values
            if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {
                ThisApp.CaptureDevice.SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains (gains, null);
                ThisApp.CaptureDevice.UnlockForConfiguration ();
            }
        };
    }
    ``` 
  
1. Substituez la `ViewDidAppear` méthode et ajoutez le code suivant pour démarrer l’enregistrement lorsque la vue se charge:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```  
  
1. Enregistrez les modifications apportées au code et exécutez l’application.
1. Une fois l’appareil photo en mode auto, les curseurs se déplacent automatiquement à mesure que l’appareil photo ajuste l’équilibre des blancs:

    [![](intro-to-manual-camera-controls-images/image19.png "Les curseurs se déplaceront automatiquement à mesure que l’appareil photo ajuste l’équilibre des blancs.")](intro-to-manual-camera-controls-images/image19.png#lightbox)
1. Appuyez sur le segment verrouillé, puis faites glisser les curseurs Temp et teinte pour ajuster manuellement l’équilibre du blanc:

    [![](intro-to-manual-camera-controls-images/image20.png "Faites glisser les curseurs de teinte et de température pour ajuster manuellement l’équilibre du blanc")](intro-to-manual-camera-controls-images/image20.png#lightbox)
1. Le segment verrouillé étant toujours sélectionné, placez une carte grise à l’avant de la caméra, puis appuyez sur le bouton de la carte grise pour ajuster l’équilibre des blancs avec le monde gris:

    [![](intro-to-manual-camera-controls-images/image21.png "Appuyez sur le bouton de la carte grise pour ajuster l’équilibre des blancs avec le monde gris")](intro-to-manual-camera-controls-images/image21.png#lightbox)
1. Arrêtez l’application.

Le code ci-dessus montre comment surveiller les paramètres d’équilibre des blancs lorsque l’appareil photo est en mode automatique ou utiliser des curseurs pour contrôler l’équilibre des blancs lorsqu’il est en mode verrouillé.

## <a name="bracketed-capture"></a>Capture entre crochets

La capture entre crochets est basée sur les paramètres des contrôles d’appareil photo manuels présentés ci-dessus et permet à l’application de capturer un moment dans le temps, de différentes manières.

Simplement indiqué, la capture entre crochets est une rafale d’images fixes prises avec divers paramètres, de l’image à l’image.

[![](intro-to-manual-camera-controls-images/image22.png "Fonctionnement de la capture entre crochets")](intro-to-manual-camera-controls-images/image22.png#lightbox)

À l’aide de la capture entre crochets dans iOS 8, une application peut prédéfinir une série de contrôles manuels d’appareil photo, émettre une seule commande et faire revenir une série d’images pour chacune des présélections manuelles.

### <a name="bracketed-capture-basics"></a>Notions de base des captures entre crochets

Là encore, la capture entre crochets est une rafale d’images fixes prises avec des paramètres variés de l’image à l’image. Les types de captures entre parenthèses disponibles sont les suivants:

-  **Crochet d’exposition automatique** : où toutes les images ont une quantité de décalage variable.
-  **Crochet d’exposition manuelle** : dans laquelle toutes les images ont une vitesse d’obturateur variable (durée) et un montant ISO.
-  **Crochet de rafale simple** : série d’images fixes prises en succession rapide.


### <a name="new-bracketed-capture-controls-in-ios-8"></a>Nouveaux contrôles de capture entre crochets dans iOS 8

Toutes les commandes de capture entre crochets sont implémentées dans la `AVCaptureStillImageOutput` classe. Utilisez la `CaptureStillImageBracket`méthode pour obtenir une série d’images avec le tableau de paramètres donné.

Deux nouvelles classes ont été implémentées pour gérer les paramètres:

-   `AVCaptureAutoExposureBracketedStillImageSettings`: Il possède une propriété, `ExposureTargetBias`, utilisée pour définir le décalage pour un crochet d’exposition automatique. 
-   `AVCaptureManual`  `ExposureBracketedStillImageSettings`: Il a deux propriétés, `ExposureDuration` et `ISO`, utilisées pour définir la vitesse d’obturation et ISO pour un crochet d’exposition manuel. 


### <a name="bracketed-capture-controls-dos-and-donts"></a>Les contrôles de capture entre crochets ne sont pas

#### <a name="dos"></a>N’est-ce pas

Vous trouverez ci-dessous une liste des opérations à effectuer lors de l’utilisation des contrôles de capture entre crochets dans iOS 8:

-  Préparez l’application pour la situation de capture la plus défavorable en `PrepareToCaptureStillImageBracket` appelant la méthode.
-  Supposons que les exemples de mémoires tampons proviennent du même pool partagé.
-  Pour libérer la mémoire qui a été allouée par un appel de `PrepareToCaptureStillImageBracket` préparation précédent, appelez à nouveau et envoyez-lui un tableau d’un objet.


#### <a name="donts"></a>Choses à faire

La liste suivante répertorie les éléments qui ne doivent pas être faits lors de l’utilisation des contrôles de capture entre crochets dans iOS 8:

-  Ne mélangez pas les types de paramètres de capture entre crochets dans une seule capture.
-  Ne demandez pas plus `MaxBracketedCaptureStillImageCount` de images que dans une seule capture.


### <a name="bracketed-capture-details"></a>Détails de la capture entre crochets

Les informations suivantes doivent être prises en considération lors de l’utilisation de capture entre crochets dans iOS 8:

-  Les paramètres entre crochets remplacent temporairement `AVCaptureDevice` les paramètres.
-  Les paramètres de stabilisation d’image continue et Flash sont ignorés.
-  Toutes les images doivent utiliser le même format de sortie (JPEG, png, etc.)
-  La version préliminaire de la vidéo peut déposer des frames.
-  La capture entre crochets est prise en charge sur tous les appareils compatibles avec iOS 8.


Avec ces informations à l’esprit, jetons un coup d’œil à l’utilisation de la capture entre crochets dans iOS 8.

### <a name="bracket-capture-example"></a>Exemple de capture de fourchette

Une fois le code d’installation de capture AV général en `UIViewController` place, un peut être ajouté à la table de montage séquentiel de l’application et configuré comme suit:

[![](intro-to-manual-camera-controls-images/image23.png "Un UIViewController peut être ajouté à la table de montage séquentiel applications et configuré comme indiqué ici")](intro-to-manual-camera-controls-images/image23.png#lightbox)

La vue contient les éléments principaux suivants:

-  `UIImageView` Qui affichera le flux vidéo.
-  Trois `UIImageViews` qui affichent les résultats de la capture.
-  `UIScrollView` Pour héberger le flux vidéo et les vues des résultats.
-  `UIButton` Utilisé pour prendre une capture entre crochets avec des paramètres prédéfinis.


Procédez comme suit pour connecter le contrôleur d’affichage pour la capture entre crochets:


1. Ajoutez les instructions using suivantes:

    ```csharp
    using System;
    using System.Drawing;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using CoreImage;
    ```  
  
1. Ajoutez les variables privées suivantes:

    ```csharp
    #region Private Variables
    private NSError Error;
    private List<UIImageView> Output = new List<UIImageView>();
    private nint OutputIndex = 0;
    #endregion
    ```    
  
1. Ajoutez les propriétés calculées suivantes:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    #endregion
    ```  
  
1. Ajoutez la méthode privée suivante pour générer les vues d’images de sortie requises:

    ```csharp
    #region Private Methods
    private UIImageView BuildOutputView(nint n) {
    
        // Create a new image view controller
        var imageView = new UIImageView (new CGRect (CameraView.Frame.Width * n, 0, CameraView.Frame.Width, CameraView.Frame.Height));
    
        // Load a temp image
        imageView.Image = UIImage.FromFile ("Default-568h@2x.png");
    
        // Add a label
        UILabel label = new UILabel (new CGRect (0, 20, CameraView.Frame.Width, 24));
        label.TextColor = UIColor.White;
        label.Text = string.Format ("Bracketed Image {0}", n);
        imageView.AddSubview (label);
    
        // Add to scrolling view
        ScrollView.AddSubview (imageView);
    
        // Return new image view
        return imageView;
    }
    #endregion
    ```  
  
1. Substituez la `ViewDidLoad` méthode et ajoutez le code suivant:
    
    ```
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
    
        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;
    
        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;
    
        // Setup scrolling area
        ScrollView.ContentSize = new SizeF (CameraView.Frame.Width * 4, CameraView.Frame.Height);
    
        // Add output views
        Output.Add (BuildOutputView (1));
        Output.Add (BuildOutputView (2));
        Output.Add (BuildOutputView (3));
    
        // Create preset settings
        var Settings = new AVCaptureBracketedStillImageSettings[] {
            AVCaptureAutoExposureBracketedStillImageSettings.Create(-2.0f),
            AVCaptureAutoExposureBracketedStillImageSettings.Create(0.0f),
            AVCaptureAutoExposureBracketedStillImageSettings.Create(2.0f)
        };
    
        // Wireup capture button
        CaptureButton.TouchUpInside += (sender, e) => {
            // Reset output index
            OutputIndex = 0;
    
            // Tell the camera that we are getting ready to do a bracketed capture
            ThisApp.StillImageOutput.PrepareToCaptureStillImageBracket(ThisApp.StillImageOutput.Connections[0],Settings,async (bool ready, NSError err) => {
                // Was there an error, if so report it
                if (err!=null) {
                    Console.WriteLine("Error: {0}",err.LocalizedDescription);
                }
            });
    
            // Ask the camera to snap a bracketed capture
            ThisApp.StillImageOutput.CaptureStillImageBracket(ThisApp.StillImageOutput.Connections[0],Settings, (sampleBuffer, settings, err) =>{
                // Convert raw image stream into a Core Image Image
                var imageData = AVCaptureStillImageOutput.JpegStillToNSData(sampleBuffer);
                var image = CIImage.FromData(imageData);
    
                // Display the resulting image
                Output[OutputIndex++].Image = UIImage.FromImage(image);
    
                // IMPORTANT: You must release the buffer because AVFoundation has a fixed number
                // of buffers and will stop delivering frames if it runs out.
                sampleBuffer.Dispose();
            });
        };
    }
    ```
    
  
1. Substituez la `ViewDidAppear` méthode et ajoutez le code suivant:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
        }
    }
    
    ```  
    
1. Enregistrez les modifications apportées au code et exécutez l’application.
1. Cadrez une scène et appuyez sur le bouton de crochet de capture:

    [![](intro-to-manual-camera-controls-images/image24.png "Cadrez une scène et appuyez sur le bouton de crochet de capture")](intro-to-manual-camera-controls-images/image24.png#lightbox)
1. Balayez de droite à gauche pour voir les trois images prises par la capture entre crochets:

    [![](intro-to-manual-camera-controls-images/image25.png "Balayer de droite à gauche pour voir les trois images prises par la capture entre crochets")](intro-to-manual-camera-controls-images/image25.png#lightbox)
1. Arrêtez l’application.


Le code ci-dessus a montré comment configurer et prendre une mesure d’exposition automatique entre crochets dans iOS 8.

## <a name="summary"></a>Récapitulatif

Dans cet article, nous avons abordé une présentation des nouveaux contrôles d’appareil photo manuels fournis par iOS 8, ainsi que les concepts de base de ce qu’ils font et de leur fonctionnement. Nous avons donné des exemples de focus manuel, d’exposition manuelle et de balance des blancs manuelle. Enfin, nous avons donné un exemple de capture entre crochets à l’aide des commandes manuelles d’appareil photo décrites précédemment.

## <a name="related-links"></a>Liens associés

- [ManualCameraControls (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/manualcameracontrols)
- [Introduction à iOS 8](~/ios/platform/introduction-to-ios8.md)
