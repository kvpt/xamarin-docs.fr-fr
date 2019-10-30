---
title: ARKit 2 dans Xamarin. iOS
description: Ce document décrit les mises à jour de ARKit dans iOS 12. Il se concentre sur l’utilisation d’objets de référence et d’images pour la détection, comprend du code pour la texturation environnementale et décrit les problèmes courants de la programmation ARKit.
ms.prod: xamarin
ms.assetid: af758092-1523-4ab7-aa53-c37a81fb156a
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/22/2018
ms.openlocfilehash: 11c106483a98e4cd1412a6edb185d5da42da61ea
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032043"
---
# <a name="arkit-2-in-xamarinios"></a>ARKit 2 dans Xamarin. iOS

ARKit a considérablement évolué depuis son introduction l’année dernière dans iOS 11. Tout d’abord, vous pouvez désormais détecter des plans verticaux et horizontaux, ce qui améliore considérablement l’expérience des expériences de réalité augmentées en intérieur. En outre, il existe de nouvelles fonctionnalités :

- Reconnaissance d’images et d’objets de référence en tant que jonction entre le monde réel et l’image numérique
- Nouveau mode d’éclairage qui simule l’éclairage réel
- Possibilité de partager et de conserver des environnements AR
- Nouveau format de fichier préféré pour le stockage du contenu AR

## <a name="recognizing-reference-objects"></a>Reconnaissance d’objets de référence

Une fonctionnalité de vitrine dans ARKit 2 est la possibilité de reconnaître des images et des objets de référence. Les images de référence peuvent être chargées à partir de fichiers image normaux ([abordés plus loin](#more-tracking-configurations)), mais les objets de référence doivent être analysés à l’aide de l' [`ARObjectScanningConfiguration`](xref:ARKit.ARObjectScanningConfiguration)axée sur le développeur.

### <a name="sample-app-scanning-and-detecting-3d-objects"></a>Exemple d’application : analyse et détection d’objets 3D

L’exemple [analyse et détection d’objets 3D](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-scanninganddetecting3dobjects) est un port d’un [projet Apple](https://developer.apple.com/documentation/arkit/scanning_and_detecting_3d_objects?language=objc) qui illustre les éléments suivants :

- Gestion de l’état des applications à l’aide d’objets [`NSNotification`](xref:Foundation.NSNotification)
- Visualisation personnalisée
- Mouvements complexes
- Analyse d’objets
- Stockage d’un [`ARReferenceObject`](xref:ARKit.ARReferenceObject)

L’analyse d’un objet de référence est gourmande en batterie et en processeur, et les appareils plus anciens ont souvent des difficultés à atteindre un suivi stable.

### <a name="state-management-using-nsnotification-objects"></a>Gestion d’État à l’aide d’objets NSNotification

Cette application utilise une machine à États qui passe des États suivants :

- `AppState.StartARSession`
- `AppState.NotReady`
- `AppState.Scanning`
- `AppState.Testing`

En outre, utilise un ensemble d’États et de transitions intégrés dans `AppState.Scanning`:

- `Scan.ScanState.Ready`
- `Scan.ScanState.DefineBoundingBox`
- `Scan.ScanState.Scanning`
- `Scan.ScanState.AdjustingOrigin`

L’application utilise une architecture réactive qui publie des notifications de transition d’État vers [`NSNotificationCenter`](xref:Foundation.NSNotificationCenter) et s’abonne à ces notifications. L’installation ressemble à cet extrait de code `ViewController.cs`:

```csharp
// Configure notifications for application state changes
var notificationCenter = NSNotificationCenter.DefaultCenter;

notificationCenter.AddObserver(Scan.ScanningStateChangedNotificationName, State.ScanningStateChanged);
notificationCenter.AddObserver(ScannedObject.GhostBoundingBoxCreatedNotificationName, State.GhostBoundingBoxWasCreated);
notificationCenter.AddObserver(ScannedObject.GhostBoundingBoxRemovedNotificationName, State.GhostBoundingBoxWasRemoved);
notificationCenter.AddObserver(ScannedObject.BoundingBoxCreatedNotificationName, State.BoundingBoxWasCreated);
notificationCenter.AddObserver(BoundingBox.ScanPercentageChangedNotificationName, ScanPercentageChanged);
notificationCenter.AddObserver(BoundingBox.ExtentChangedNotificationName, BoundingBoxExtentChanged);
notificationCenter.AddObserver(BoundingBox.PositionChangedNotificationName, BoundingBoxPositionChanged);
notificationCenter.AddObserver(ObjectOrigin.PositionChangedNotificationName, ObjectOriginPositionChanged);
notificationCenter.AddObserver(NSProcessInfo.PowerStateDidChangeNotification, DisplayWarningIfInLowPowerMode);

```

Un gestionnaire de notifications standard met à jour l’interface utilisateur et peut éventuellement modifier l’état de l’application, tel que le gestionnaire mis à jour à mesure que l’objet est analysé :

```csharp
private void ScanPercentageChanged(NSNotification notification)
{
    var pctNum = TryGet<NSNumber>(notification.UserInfo, BoundingBox.ScanPercentageUserKey);
    if (pctNum == null)
    {
        return;
    }
    double percentage = pctNum.DoubleValue;
    // Switch to the next state if scan is complete
    if (percentage >= 100.0)
    {
        State.SwitchToNextState();
    }
    else
    {
        DispatchQueue.MainQueue.DispatchAsync(() => navigationBarController.SetNavigationBarTitle($"Scan ({percentage})"));
    }
}

```

Enfin, `Enter{State}` méthodes modifient le modèle et l’expérience utilisateur en fonction du nouvel État :

```csharp
internal void EnterStateTesting()
{
    navigationBarController.SetNavigationBarTitle("Testing");
    navigationBarController.ShowBackButton(false);
    loadModelButton.Hidden = true;
    flashlightButton.Hidden = false;
    nextButton.Enabled = true;
    nextButton.SetTitle("Share", UIControlState.Normal);

    testRun = new TestRun(sessionInfo, sceneView);
    TestObjectDetection();
    CancelMaxScanTimeTimer();
}
```

### <a name="custom-visualization"></a>Visualisation personnalisée

L’application affiche le « Cloud point » de bas niveau de l’objet contenu dans un cadre englobant projeté sur un plan horizontal détecté.

Ce Cloud de point est disponible pour les développeurs dans la propriété [`ARFrame.RawFeaturePoints`](xref:ARKit.ARFrame.RawFeaturePoints) . La visualisation efficace du Cloud point peut être un problème délicat. En effectuant une itération sur les points, la création et le placement d’un nouveau nœud SceneKit pour chaque point interrompent la fréquence d’images. En guise d’alternative, s’il s’agit d’une opération asynchrone, il y a un décalage. L’exemple gère les performances avec une stratégie en trois parties :

- Utilisation de code non sécurisé pour épingler les données sur place et interpréter les données comme une mémoire tampon d’octets brute.
- Conversion de cette mémoire tampon brute en [`SCNGeometrySource`](xref:SceneKit.SCNGeometrySource) et création d’un objet [`SCNGeometryElement`](xref:SceneKit.SCNGeometryElement) « template ».
- Regrouper rapidement les données brutes et le modèle à l’aide de [`SCNGeometry.Create(SCNGeometrySource[], SCNGeometryElement[])`](xref:SceneKit.SCNGeometry.Create(SceneKit.SCNGeometrySource[],SceneKit.SCNGeometryElement[]))

```csharp
internal static SCNGeometry CreateVisualization(NVector3[] points, UIColor color, float size)
{
  if (points.Length == 0)
  {
    return null;
  }

  unsafe
  {
    var stride = sizeof(float) * 3;

    // Pin the data down so that it doesn't move
    fixed (NVector3* pPoints = &amp;points[0])
    {
      // Important: Don't unpin until after `SCNGeometry.Create`, because geometry creation is lazy

      // Grab a pointer to the data and treat it as a byte buffer of the appropriate length
      var intPtr = new IntPtr(pPoints);
      var pointData = NSData.FromBytes(intPtr, (System.nuint) (stride * points.Length));

      // Create a geometry source (factory) configured properly for the data (3 vertices)
      var source = SCNGeometrySource.FromData(
        pointData,
        SCNGeometrySourceSemantics.Vertex,
        points.Length,
        true,
        3,
        sizeof(float),
        0,
        stride
      );

      // Create geometry element
      // The null and bytesPerElement = 0 look odd, but this is just a template object
      var template = SCNGeometryElement.FromData(null, SCNGeometryPrimitiveType.Point, points.Length, 0);
      template.PointSize = 0.001F;
      template.MinimumPointScreenSpaceRadius = size;
      template.MaximumPointScreenSpaceRadius = size;

      // Stitch the data (source) together with the template to create the new object
      var pointsGeometry = SCNGeometry.Create(new[] { source }, new[] { template });
      pointsGeometry.Materials = new[] { Utilities.Material(color) };
      return pointsGeometry;
    }
  }
}
```

Le résultat ressemble à ceci :

![point_cloud](images/arkit_point_cloud.jpeg)

### <a name="complex-gestures"></a>Mouvements complexes

L’utilisateur peut mettre à l’échelle, faire pivoter et faire glisser le cadre englobant qui entoure l’objet cible. Les module de reconnaissance de mouvement associés présentent deux éléments intéressants.

Tout d’abord, tous les module de reconnaissance de mouvement s’activent uniquement après la transmission d’un seuil ; par exemple, un doigt a fait glisser un grand nombre de pixels ou la rotation dépasse un certain angle. La technique consiste à accumuler le déplacement jusqu’à ce que le seuil soit dépassé, puis à l’appliquer de façon incrémentielle :

```csharp
// A custom rotation gesture recognizer that fires only when a threshold is passed
internal partial class ThresholdRotationGestureRecognizer : UIRotationGestureRecognizer
{
    // The threshold after which this gesture is detected.
    const double threshold = Math.PI / 15; // (12°)

    // Indicates whether the currently active gesture has exceeded the threshold
    private bool thresholdExceeded = false;

    private double previousRotation = 0;
    internal double RotationDelta { get; private set; }

    internal ThresholdRotationGestureRecognizer(IntPtr handle) : base(handle)
    {
    }

    // Observe when the gesture's state changes to reset the threshold
    public override UIGestureRecognizerState State
    {
        get => base.State;
        set
        {
            base.State = value;

            switch(value)
            {
                case UIGestureRecognizerState.Began :
                case UIGestureRecognizerState.Changed :
                    break;
                default :
                    // Reset threshold check
                    thresholdExceeded = false;
                    previousRotation = 0;
                    RotationDelta = 0;
                    break;
            }
        }
    }

    public override void TouchesMoved(NSSet touches, UIEvent evt)
    {
        base.TouchesMoved(touches, evt);

        if (thresholdExceeded)
        {
            RotationDelta = Rotation - previousRotation;
            previousRotation = Rotation;
        }

        if (! thresholdExceeded && Math.Abs(Rotation) > threshold)
        {
            thresholdExceeded = true;
            previousRotation = Rotation;
        }
    }
}
```

La deuxième chose intéressante en ce qui concerne les gestes est la façon dont le cadre englobant est déplacé par rapport aux plans réels détectés. Cet aspect est abordé dans [ce billet de blog Xamarin](https://blog.xamarin.com/exploring-new-ios-12-arkit-capabilities-with-xamarin/).

## <a name="other-new-features-in-arkit-2"></a>Autres nouvelles fonctionnalités de ARKit 2

### <a name="more-tracking-configurations"></a>Autres configurations de suivi

À présent, vous pouvez utiliser l’un des éléments suivants comme base pour une expérience de réalité mixte :

- Uniquement l’accéléromètre de l’appareil ([`AROrientationTrackingConfiguration`](xref:ARKit.AROrientationTrackingConfiguration), iOS 11)
- Visages ([`ARFaceTrackingConfiguration`](xref:ARKit.ARFaceTrackingConfiguration), iOS 11)
- Images de référence ([`ARImageTrackingConfiguration`](xref:ARKit.ARImageTrackingConfiguration), IOS 12)
- Analyse des objets 3D ([`ARObjectScanningConfiguration`](xref:ARKit.ARObjectScanningConfiguration), IOS 12)
- Odometry d’inertie visuel ([`ARWorldTrackingConfiguration`](xref:ARKit.ARWorldTrackingConfiguration), amélioré dans IOS 12)

`AROrientationTrackingConfiguration`, présenté dans [ce billet de blog F# et notre exemple](https://github.com/lobrien/FSharp_Face_AR), est le plus limité et fournit une expérience de réalité mixte médiocre, car il place uniquement les objets numériques par rapport au mouvement de l’appareil, sans essayer de lier l’appareil et l’écran dans le réel réelles.

Le `ARImageTrackingConfiguration` vous permet de reconnaître des images 2D réelles (peintures, logos, etc.) et de les utiliser pour ancrer les images numériques :

```csharp
var imagesAndWidths = new[] {
    ("cover1.jpg", 0.185F),
    ("cover2.jpg", 0.185F),
     //...etc...
    ("cover100.jpg", 0.185F),
};

var referenceImages = new NSSet<ARReferenceImage>(
    imagesAndWidths.Select( imageAndWidth =>
    {
      // Tuples cannot be destructured in lambda arguments
        var (image, width) = imageAndWidth;
        // Read the image
        var img = UIImage.FromFile(image).CGImage;
        return new ARReferenceImage(img, ImageIO.CGImagePropertyOrientation.Up, width);
    }).ToArray());

configuration.TrackingImages = referenceImages;
```

Cette configuration comporte deux aspects intéressants :

- Elle est efficace et peut être utilisée avec un nombre potentiellement important d’images de référence
- L’image numérique est ancrée à l’image, même si cette image se déplace dans le monde réel (par exemple, si la couverture d’un livre est reconnue, elle suit le livre tel qu’il est retiré de l’étagère, arrêtée, etc.).

La `ARObjectScanningConfiguration` a été abordée [précédemment](#recognizing-reference-objects) et est une configuration centrée sur les développeurs pour l’analyse des objets 3D. Le processeur et la batterie sont très gourmands en ressources et ne doivent pas être utilisés dans les applications des utilisateurs finaux. L’exemple d' [analyse et de détection d’objets 3D](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-scanninganddetecting3dobjects) illustre l’utilisation de cette configuration.

La configuration de suivi finale, `ARWorldTrackingConfiguration`, est la plus propre des expériences de réalité mixte. Cette configuration utilise « odometry d’inertie visuel » pour mettre en relation des « points de fonctionnalité » réels avec l’image numérique. Les géométries numériques ou les sprites sont ancrés par rapport à des plans horizontaux réels et verticaux ou relatifs à des instances de `ARReferenceObject` détectées. Dans cette configuration, l’origine du monde est la position d’origine de la caméra dans l’espace, l’axe Z étant aligné sur la gravité et les objets numériques « restent en place » par rapport aux objets dans le monde réel.

### <a name="environmental-texturing"></a>Texturation environnementale

ARKit 2 prend en charge la « texturation environnementale » qui utilise l’image capturée pour estimer l’éclairage et même appliquer des surbrillances spéculaires aux objets brillants. Le carte cubique environnemental est créé dynamiquement et, une fois que la caméra a regardé toutes les directions, peut produire une expérience réaliste impressionnante :

![image de démonstration de la texture environnementale](images/arkit_env_texturing.png)

Afin d’utiliser la texturation environnementale :

- Vos objets [`SCNMaterial`](xref:SceneKit.SCNMaterial) doivent utiliser des [`SCNLightingModel.PhysicallyBased`](xref:SceneKit.SCNLightingModel.PhysicallyBased) et affecter une valeur comprise entre 0 et 1 pour [`Metalness.Contents`](xref:SceneKit.SCNMaterial.Metalness) et [`Roughness.Contents`](xref:SceneKit.SCNMaterialProperty.Contents) et
- Votre configuration de suivi doit définir [`EnvironmentTexturing`](xref:ARKit.ARWorldTrackingConfiguration.EnvironmentTexturing) = [`AREnvironmentTexturing.Automatic`](xref:ARKit.AREnvironmentTexturing.Automatic) :

```csharp
var sphere = SCNSphere.Create(0.33F);
sphere.FirstMaterial.LightingModelName = SCNLightingModel.PhysicallyBased;
// Shiny metallic sphere
sphere.FirstMaterial.Metalness.Contents = new NSNumber(1.0F);
sphere.FirstMaterial.Roughness.Contents = new NSNumber(0.0F);

// Session configuration:
var configuration = new ARWorldTrackingConfiguration
{
    PlaneDetection = ARPlaneDetection.Horizontal | ARPlaneDetection.Vertical,
    LightEstimationEnabled = true,
    EnvironmentTexturing = AREnvironmentTexturing.Automatic
};
```

Bien que la texture parfaite réfléchie apparaissant dans l’extrait de code précédent soit amusante dans un exemple, la texturation environnementale est probablement mieux utilisée avec la restriction de restriction. elle déclenche une réponse « unformy Valley » (la texture n’est qu’une estimation basée sur l’appareil photo enregistré).

### <a name="shared-and-persistent-ar-experiences"></a>Expériences AR partagées et persistantes

Un autre ajout majeur à ARKit 2 est la classe [`ARWorldMap`](xref:ARKit.ARWorldMap) , qui vous permet de partager ou de stocker des données de suivi universel. Vous recevez le plan World actuel avec [`ARSession.GetCurrentWorldMapAsync`](xref:ARKit.ARSession.GetCurrentWorldMapAsync) ou [`GetCurrentWorldMap(Action<ARWorldMap,NSError>)`](xref:ARKit.ARSession.GetCurrentWorldMap(System.Action{ARKit.ARWorldMap,Foundation.NSError})) :

```csharp
// Local storage
var PersistentWorldPath => Environment.GetFolderPath(Environment.SpecialFolder.Personal) + "/arworldmap";

// Later, after scanning the environment thoroughly...
var worldMap = await Session.GetCurrentWorldMapAsync();
if (worldMap != null)
{
    var data = NSKeyedArchiver.ArchivedDataWithRootObject(worldMap, true, out var err);
    if (err != null)
    {
        Console.WriteLine(err);
    }
    File.WriteAllBytes(PersistentWorldPath, data.ToArray());
}
```

Pour partager ou restaurer la carte du monde :

1. Charger les données à partir du fichier,
2. Désarchivez-la dans un objet `ARWorldMap`,
3. Utilisez-le comme valeur de la propriété [`ARWorldTrackingConfiguration.InitialWorldMap`](xref:ARKit.ARWorldTrackingConfiguration.InitialWorldMap) :

```csharp
var data = NSData.FromArray(File.ReadAllBytes(PersistentWorldController.PersistenWorldPath));
var worldMap = (ARWorldMap)NSKeyedUnarchiver.GetUnarchivedObject(typeof(ARWorldMap), data, out var err);

var configuration = new ARWorldTrackingConfiguration
{
    PlaneDetection = ARPlaneDetection.Horizontal | ARPlaneDetection.Vertical,
    LightEstimationEnabled = true,
    EnvironmentTexturing = AREnvironmentTexturing.Automatic,
    InitialWorldMap = worldMap
};
```

Le `ARWorldMap` contient uniquement des données de suivi universelles non visibles et les objets de [`ARAnchor`](xref:ARKit.ARAnchor) , il ne contient _pas_ de ressources numériques. Pour partager une géométrie ou une image, vous devez développer votre propre stratégie adaptée à votre cas d’utilisation (par exemple, en stockant/transmettant uniquement l’emplacement et l’orientation de la géométrie et en l’appliquant aux `SCNGeometry` statiques ou éventuellement par le stockage/la transmission objets sérialisés). L’avantage de la `ARWorldMap` est que les ressources, une fois placées par rapport à un `ARAnchor`partagé, s’affichent de manière cohérente entre les appareils ou les sessions.

### <a name="universal-scene-description-file-format"></a>Format de fichier de description de scène universelle

La dernière fonctionnalité de ARKit 2 est l’adoption par Apple du format de fichier de [Description de scène universelle](https://graphics.pixar.com/usd/docs/Introduction-to-USD.html) de Pixar. Ce format remplace le format DAE de Collada comme format préféré pour le partage et le stockage des ressources ARKit. La prise en charge de la visualisation des ressources est intégrée à iOS 12 et Mojave. L’extension de fichier USDZ est une archive zip non compressée et non chiffrée contenant des fichiers USD. Pixar [fournit des outils permettant de travailler avec des fichiers USD](https://graphics.pixar.com/usd/docs/USD-Toolset.html#USDToolset-usdedit) , mais il n’existe pas encore de prise en charge de tiers.

## <a name="arkit-programming-tips"></a>Conseils de programmation ARKit

### <a name="manual-resource-management"></a>Gestion manuelle des ressources

Dans ARKit, il est essentiel de gérer manuellement les ressources. Non seulement cela permet d’obtenir des fréquences d’images élevées, mais il est en fait _nécessaire_ d’éviter un « blocage d’écran » confus. L’infrastructure ARKit est différée de la fourniture d’un nouveau Frame d’appareil photo ([`ARSession.CurrentFrame`](xref:ARKit.ARSession.CurrentFrame). Tant que le [`ARFrame`](xref:ARKit.ARFrame) actif n’a pas `Dispose()` appelé, ARKit ne fournira pas de nouveau Frame ! La vidéo est alors « figée » même si le reste de l’application est réactif. La solution consiste à toujours accéder à `ARSession.CurrentFrame` à l’aide d’un bloc `using` ou à appeler manuellement `Dispose()` dessus.

Tous les objets dérivés de `NSObject` sont `IDisposable` et `NSObject` implémente le [modèle de suppression](https://docs.microsoft.com/dotnet/standard/design-guidelines/dispose-pattern). par conséquent, vous devez généralement suivre [ce modèle pour implémenter `Dispose` sur une classe dérivée](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose).

### <a name="manipulating-transform-matrices"></a>Manipulation des matrices de transformation

Dans n’importe quelle application 3D, vous allez utiliser des matrices de transformation 4x4 qui décrivent de façon compacte comment déplacer, faire pivoter et déformer un objet à l’aide de l’espace 3D. Dans SceneKit, il s’agit d’objets [`SCNMatrix4`](xref:SceneKit.SCNMatrix4) .  

La propriété [`SCNNode.Transform`](xref:SceneKit.SCNNode.Transform) retourne la matrice de transformation `SCNMatrix4` pour le [`SCNNode`](xref:SceneKit.SCNNode) _comme étant sauvegardé par_ le type de `simdfloat4x4` de ligne principale. Par exemple :

```csharp
var node = new SCNNode { Position = new SCNVector3(2, 3, 4) };  
var xform = node.Transform;
Console.WriteLine(xform);
// Output is: "(1, 0, 0, 0)\n(0, 1, 0, 0)\n(0, 0, 1, 0)\n(2, 3, 4, 1)"
```  

Comme vous pouvez le voir, la position est encodée dans les trois premiers éléments de la ligne inférieure.

Dans Xamarin, le type commun pour manipuler des matrices de transformation est `NVector4`, qui, par Convention, est interprété dans une colonne principale. Autrement dit, le composant translation/position est attendu dans M14, M24, M34, et non M41, M42, M43 :

![ligne-principale vs colonne-principale](images/arkit_row_vs_column.png)

La cohérence avec le choix de l’interprétation de la matrice est vitale pour un comportement correct. Étant donné que les matrices de transformation 3D sont 4x4, les erreurs de cohérence ne produisent aucun type d’exception au moment de la compilation ou même au moment de l’exécution ; il s’agit simplement que les opérations agissent de façon inattendue. Si vos objets SceneKit/ARKit semblent être bloqués, volés ou instables, une matrice de transformation incorrecte est une bonne possibilité. La solution est simple : [`NMatrix4.Transpose`](xref:OpenTK.NMatrix4.Transpose*) effectue une transposition sur place des éléments.

## <a name="related-links"></a>Liens connexes

- [Exemple d’application-analyse et détection d’objets 3D](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-scanninganddetecting3dobjects)
- [Nouveautés de ARKit 2 (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/602/)
- [Fonctionnement du suivi et de la détection ARKit (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/610/)
- [Présentation de ARKit dans Xamarin. iOS](https://docs.microsoft.com/xamarin/ios/platform/introduction-to-ios11/arkit/)
