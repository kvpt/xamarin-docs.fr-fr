---
title: Modifications supplémentaires apportées à iOS 10 frameworks
description: Ce document décrit les modifications mineures et les améliorations apportées aux Frameworks existants dans iOS 10 et explique comment utiliser ces mises à jour dans Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 0E2217F1-FC96-4D0A-ABAB-D40AD8F96502
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 620b89ba4682d65552fa5555c978b7eb5f437714
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290769"
---
# <a name="additional-ios-10-frameworks-changes"></a>Modifications supplémentaires apportées à iOS 10 frameworks

_Cet article traite des modifications supplémentaires ou des améliorations apportées aux infrastructures existantes pour iOS 10._

## <a name="av-foundation-framework-additions"></a>Ajouts de l’infrastructure de Fondation AV

L’infrastructure AVFoundation intègre les améliorations suivantes :

- Dans iOS 10, le développeur n’a plus à implémenter des comportements [AVPlayerItem](xref:AVFoundation.AVPlayerItem) différents en fonction du type de contenu. Il vous suffit `Rate` de définir la propriété et AVFoundation déterminera quand un contenu suffisant sera disponible pour la lecture sans blocage.
- La nouvelle classe [AVCapturePhotoOutput](xref:AVFoundation.AVCaptureFileOutput) remplace la `AVCaptureStillImageOutput` classe déconseillée et fournit une méthode unifiée pour la gestion de tous les flux de travail de photographie en fournissant un contrôle et une surveillance sophistiqués du processus de capture et une prise en charge des nouvelles fonctionnalités telles que les photos en direct et le format de capture brut.
- La nouvelle `AVPlayerLooper` classe facilite la boucle d’un élément multimédia donné pendant la lecture.
- La `AVAssetDownloadURLSession` classe permet le téléchargement et la lecture ultérieure des flux TLS chiffrés fairplay.
- Par défaut, la classe [AVCaptureSession](xref:AVFoundation.AVCaptureSession) prend automatiquement en charge la capture large gamme de couleurs larges lorsque le matériel de l’appareil le prend en charge. Pour plus d’informations, consultez informations de référence sur la [compatibilité des appareils iOS](https://developer.apple.com/library/prerelease/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013599) d’Apple.

## <a name="avkit-additions"></a>Ajouts AVKit

L’infrastructure AVKit comprend désormais la nouvelle `UpdatesNowPlayingInfoCenter` propriété pour indiquer quand le centre d’informations de Playing doit être mis à jour.

## <a name="core-data-enhancements"></a>Améliorations des données de base

iOS 10 comprend les améliorations suivantes apportées à l’infrastructure de données de base :

- Les objets [NSManagedObjectContext](xref:CoreData.NSManagedObjectContext) avec les banques de données sqlite dans le mode journal de Wal prennent en charge la nouvelle fonctionnalité de génération de requêtes dans laquelle les contextes d’objets managés (MOC) peuvent être épinglés à des versions de base de données spécifiques pour des transactions de récupération et de défaillance futures.
- Les objets [NSManagedObjectContext](xref:CoreData.NSManagedObjectContext) racines prennent en charge l’erreur et l’extraction simultanés sans sérialisation.
- La classe [NSPersistentStoreCoordinator](xref:CoreData.NSPersistentStoreCoordinator) gère un pool de banques de données SQLite.
- Plusieurs nouvelles méthodes pratiques ont été ajoutées pour `NSManagedObject` faciliter l’exécution des extractions et la création de sous-classes.
- Utilisation du niveau `NSPersistenceContainer` élevé pour référencer les `NSPersistentStoreCoordinator`ressources de configuration de données principales, [NSManagedObjectModel](xref:CoreData.NSManagedObjectModel) et autres.

Pour plus d’informations, consultez Référence de l' [infrastructure de données de base](https://developer.apple.com/reference/coredata)d’Apple.

## <a name="core-image-enhancements"></a>Améliorations de l’image principale

iOS 10 apporte les améliorations suivantes à l’infrastructure d’images principales :

- Le développeur peut désormais traiter les images dans un espace de couleurs en dehors de l’espace de couleurs de travail du contexte de l’image principale en convertissant l’espace de couleurs avant et après le traitement.
- Pour les appareils iOS qui utilisent les processeurs a8 ou A9, le format d’image RAW est désormais pris en charge. L’image principale prend désormais en charge le décodage d’images BRUTes à partir de l’appareil photo iSight intégré ou d’une caméra tierce. Utilisez les `FilterWithImageData` méthodes `FilterWithImageURL` ou de la classe [CIFilter](xref:CoreImage.CIFilter) pour traiter les images brutes.
- Plusieurs améliorations des performances de rendu ont été apportées au `UIImage` rendu (lorsqu’ils sont sauvegardés par des magasins d’images d’images principales) dans des `UIImageView` objets. 
- `UIImage`les objets avec une large gamme de couleurs s’affichent sous la `UIImageView` forme d’une large gamme dans des objets sur des appareils IOS qui prennent en charge la couleur large.
- Le code noyau de l’image principale peut désormais demander des formats de sortie pixel spécifiques.
- La `ImageWithExtent` méthode de la classe [CIFilter](xref:CoreImage.CIFilter) peut être utilisée pour insérer un traitement personnalisé dans l’opération de filtrage. L’image principale appellera le rappel donné entre les filtres lors du traitement d’une image pour la sortie ou l’affichage.

En outre, les nouveaux filtres d’image principaux suivants ont été ajoutés :

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

## <a name="core-motion-additions"></a>Ajouts de mouvement de base

Nouveauté d’iOS 10, l’infrastructure de mouvement de base comprend des événements Pedometer qui permettent à une application de recevoir des notifications rapides et en temps réel de la suspension et de la reprise de l’utilisateur pendant l’exécution. Utilisez [CMPedometer](xref:CoreMotion.CMPedometer) pour vous inscrire aux événements de premier plan ou d’arrière-plan Pedometer.

## <a name="foundation-enhancements"></a>Améliorations de base

Les améliorations suivantes ont été apportées à l’infrastructure de base pour iOS 10 :

- Utilisez la nouvelle classe [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) pour mettre en forme les mesures localisées à afficher à l’utilisateur final.
- Utilisez la nouvelle classe [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) pour effectuer des calculs d’intervalles de date et d’heure tels que des durées, pour comparer des intervalles et tester des intersections d’intervalle.
- Utilisez la nouvelle classe [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) pour effectuer une conversion entre différentes unités de mesure ou effectuer des calculs sur des valeurs dans différents UOMs.

- Utilisez les nouvelles classes [NSUnit](https://developer.apple.com/reference/foundation/nsunit) et [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) pour représenter des UOMs spécifiques.
- Plusieurs nouvelles propriétés ont été ajoutées à la classe [NSLocal](https://developer.apple.com/reference/foundation/nslocale) pour obtenir des informations locales et les formats d’affichage disponibles.

## <a name="gamekit-enhancements"></a>Améliorations apportées à GameKit

Les améliorations suivantes ont été apportées à l’infrastructure GameKit dans iOS 10 :

- L' **application Game Center** a été dépréciée et supprimée d’iOS. Si l’application utilise GameKit, elle _doit_ présenter sa propre interface pour afficher les fonctionnalités GameKit telles que Leaderboards, etc. 
- Un nouveau type de compte iCloud est implémenté par la classe [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) .
- La nouvelle classe [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) fournit une solution généralisée pour la gestion du stockage de données persistant sur Game Center. `GKGameSession`gère une liste de joueurs et l’application est chargée d’implémenter comment et quand la date du participant est stockée, récupérée ou échangée entre les joueurs. Dans de nombreux cas, les sessions de jeu peuvent remplacer les correspondances basées sur les activations existantes, les correspondances en temps réel ou les méthodes d’enregistrement de jeu persistantes.

## <a name="gameplaykit-enhancements"></a>Améliorations apportées à GameplayKit

Les améliorations suivantes ont été apportées à l’infrastructure GameplayKit dans iOS 10 :

- Utilisez la nouvelle classe [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) pour fournir des chemins d’accès à hautes performances et à l’aspect naturel.
- La génération de bruit procédurale a été ajoutée et peut être utilisée pour améliorer le réalisme des textures à l’aspect naturel, ajouter un réalisme aux mouvements de l’appareil photo et aider à générer des mondes riches en jeux.
- Utilisez le partitionnement spatial pour partitionner les données du monde du jeu pour une recherche efficace.
- Un nouveau stratège de Monte-Carlo ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) a été ajouté pour un calcul de déplacement exhaustif possible.
- la prise en charge 3D a été ajoutée aux comportements de l’agent et du chemin d’accès existants à l’aide des nouvelles classes [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) et [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) .
- Les nouvelles classes [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) et [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) rendent la combinaison de GameplayKit et SpriteKit plus facile que jamais.
- Une nouvelle API d’arbre de décision a été ajoutée ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) et [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) pour améliorer la création de jeux.

## <a name="healthkit-enhancements"></a>Améliorations apportées à HealthKit

Les améliorations suivantes ont été apportées à l’infrastructure HealthKit dans iOS 10 :

- De nouvelles clés de métadonnées ont été ajoutées pour les types `HKWeatherConditionClear` météo `HKWeatherConditionCloudy`(tels que et) et les `HKWorkoutActivityTypeFlexibility` types `HKWorkoutActivityTypeWheelchairRunPace`d’entraînement (tels que et) ont été ajoutés.
- La nouvelle `HKCDADocument` classe a été ajoutée pour représenter un document au format CDA (clinique document architecture).
- Utilisez la nouvelle classe [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) pour spécifier le `ActivityType` et `LocationType` un entraînement.
- Le nouveau [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) et la `WheelchairUse` méthode de la classe [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) ont été ajoutés pour l’utilisation des données d’intégrité liées aux fauteuils roulants.

## <a name="homekit-enhancements"></a>Améliorations apportées à HomeKit

Les améliorations suivantes ont été apportées à l’infrastructure HomeKit dans iOS 10 :

- De nouveaux services et caractéristiques ont été ajoutés.
- Un iPad peut être configuré pour agir en tant que Hub HomeKit pour fournir un accès accessoire à distance, exécuter des déclencheurs Automation et activer des autorisations utilisateur partagées.
- La prise en charge a été ajoutée pour les accessoires Camera et Doorbell.
- Davantage de contexte et de configurations ont été fournis pour les accessoires.

Pour plus d’informations, consultez notre [Introduction à](~/ios/platform/homekit.md) la documentation de HomeKit.

## <a name="metal-enhancements"></a>Améliorations des métaux

Les améliorations suivantes ont été apportées à l’infrastructure métallique dans iOS 10 :

- les applications et les jeux 3D peuvent désormais utiliser la _facettisation_ pour restituer efficacement des scènes et une géométrie complexes via le GPU.
- Fournir un contrôle affiné de l’allocation des ressources pour optimiser les performances des applications basées sur le métal à l’aide de segments de ressources et de cibles de rendu sans mémoire.
- Utilisez la spécialisation de fonction pour créer une collection hautement optimisée de fonctions de combinaison de matériau et de lumière pour une scène.

Pour plus d’informations, consultez le [Guide de programmation métallique](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)d’Apple.

## <a name="modelio-enhancements"></a>Améliorations apportées à ModelIO

Les améliorations suivantes ont été apportées à l’infrastructure ModelIO dans iOS 10 :

- Le format de fichier USD est désormais pris en charge.
- La prise en charge des champs à distance signée a été ajoutée à la classe [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) .
- Utilisez la nouvelle `MDLLightProbeIrradianceDataSource` classe pour faciliter le positionnement de la sonde légère.
- Utilisez la nouvelle `MDLMaterialPropertyGraph` classe pour prendre en charge facilement les modifications du runtime pour les modèles.

## <a name="photos-enhancements"></a>Améliorations des photos

Les améliorations suivantes ont été apportées à l’infrastructure des photos dans iOS 10 :

- Utilisez les classes [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) et [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) pour tirer parti de la nouvelle fonctionnalité du processeur d’images principal afin d’effectuer des modifications.
- L’édition de photos en direct est désormais disponible pour les applications qui prennent en charge l’infrastructure de photos et les extensions de modification de photos (pour une utilisation dans les photos et les applications d’appareil photo).
- Utilisez la nouvelle classe [PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) pour appliquer les modifications à la vidéo et au contenu toujours des photos en direct.

## <a name="replaykit-enhancements"></a>Améliorations apportées à ReplayKit

Les améliorations suivantes ont été apportées à l’infrastructure ReplayKit dans iOS 10 :

- Utilisez les classes [RPScreenRecorder](https://developer.apple.com/reference/replaykit/rpscreenrecorder), [RPBroadcastActivityViewController](https://developer.apple.com/reference/replaykit/rpbroadcastactivityviewcontroller) et [RPBroadcastController](https://developer.apple.com/reference/replaykit/rpbroadcastcontroller) pour prendre en charge la diffusion de médias enregistrés via des sites tiers.
- L’interface utilisateur de diffusion et les extensions de Broadcast Upload sont requises pour prendre en charge les services de diffusion ReplayKit tiers dans l’application.

## <a name="scenekit-enhancements"></a>Améliorations apportées à SceneKit

Les améliorations suivantes ont été apportées à l’infrastructure SceneKit dans iOS 10 :

- La classe [SCNCamera](xref:SceneKit.SCNCamera) peut fournir un réalisme plus réaliste en utilisant les fonctionnalités HDR et les effets. Utilisez l’exposition adaptative pour créer des effets automatiques ou utiliser l’vignette, la frange des couleurs et le classement des couleurs pour ajouter des effets fillmatic au jeu.
- SceneKit comprend maintenant un nouveau système de rendu physique (PBR) pour des résultats plus réalistes avec la création de ressources plus simple.
- Utilisez le nouveau modèle [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) Shading pour produit un large éventail d’effets d’ombrage réalistes tout en exigeant uniquement trois propriétés fondamentales`Diffuse`( `Metalness` , `Roughness`et).
- Étant donné que l’ombrage PBR fonctionne mieux avec l’éclairage basé sur l' `LightingEnvironment` environnement, utilisez la propriété pour affecter un éclairage basé sur une image à une scène entière.
- Utilisez la `IESProfileURL` propriété pour importer des luminaires réels qui définissent l’éclairage en fonction de valeurs réelles telles que l’intensité (en lumières) et la température de couleur (en degrés Kelvin).
- Les fonctionnalités des caméras PBR et HDR offrent de meilleurs résultats que les techniques de rendu traditionnelles et, par conséquent, SceneKit effectue désormais tous les calculs de couleur dans un espace colorimétrique linéaire (à l’aide de la palette de couleurs P3 sur les écrans d’appareils larges couleurs).
- La couleur SceneKit Now correspond à toutes les couleurs en lisant les informations du profil de couleurs.
- SceneKit interprète les valeurs de composant de couleur dans un espace de couleurs RVB linéaire pour tous les types de nuanceur.
- Le rendu de l’espace de couleurs linéaire et la grande couleur peuvent être désactivés en spécifiant `SCNDisableLinearSpaceRendering` les `Info.plist`clés et `SCNDisableWideGamut` dans l’application.
- Générez des primates de polygones arbitraires (chargés à partir de fichiers ou générés par programme) pour spécifier Geometry avec la nouvelle classe [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon) .
- Étant donné que SceneKit lit et ajuste les informations de profil colorimétrique dans les images de texture, utilisez des catalogues de ressources pour toutes les images pour vous assurer que ces informations sont fournies.

## <a name="spritekit-enhancements"></a>Améliorations apportées à SpriteKit

Les améliorations suivantes ont été apportées à l’infrastructure SpriteKit dans iOS 10 :

- Les nuanceurs personnalisés peuvent fournir des`SKAttribute`attributs () qui peuvent être configurés séparément par chaque nœud qui utilise le nuanceur en fournissant une`SKAttributeValue`valeur d’attribut ().
- Tilemaps prennent désormais `SKTileMapMode`en charge les formes de mosaïque carrée, hexagonale et isométrique pour les jeux 2D, 2.5 d et de défilement latéral à l’aide des classes `SKTileGroupRule` , `SKTileGroup`et `SKTileSet` .
- Utilisez la nouvelle `SKWarpGeometry` classe pour étirer ou déformer le rendu [SKSpriteNode](xref:SpriteKit.SKSpriteNode) ou [SKEffectNode](xref:SpriteKit.SKEffectNode) . La nouvelle classe [SKAction](xref:SpriteKit.SKAction) peut être utilisée pour animer des transitions entre des effets de déformation.
- La classe [SKView](xref:SpriteKit.SKView) fournit plusieurs nouvelles méthodes pour fournir un contrôle précis sur le moment et le mode de rendu d’une scène.

## <a name="scrollview-enhancements"></a>Améliorations apportées à ScrollView

Les améliorations suivantes ont été apportées au contrôle ScrollView dans iOS 10,3 :

- `UIScrollView`incluez maintenant `IndexDisplayMode` la propriété pour contrôler la façon dont l’index est affiché lorsque l’utilisateur fait défiler `UIScrollViewIndexDisplayMode` en tant que :
  - `Automatic`-L’affichage de l’index est contrôlé par le système d’exploitation.
  - `AlwaysHidden`-L’affichage de l’index est toujours masqué.

Consultez l' [exemple iOSTenThree](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-iostenthree) pour plus d’utilisation.

## <a name="uikit-enhancements"></a>Améliorations apportées à UIKit

Les améliorations suivantes ont été apportées à l’infrastructure UIKit dans iOS 10 :

- La nouvelle API [UIPasteboard](xref:UIKit.UIPasteboard) fournit de nouvelles options (telles que les limitations de durée de vie) et déclare automatiquement les types de contenu compatibles pour les types de classe courants.
- Une nouvelle prise en charge d’animation interruptible entièrement interactive, basée sur les objets, a été ajoutée et peut être liée aux gestes. Consultez Référence du [protocole UIViewAnimating](https://developer.apple.com/reference/uikit/uiviewanimating)d’Apple, [référence de la classe UIViewPropertyAnimator](https://developer.apple.com/reference/uikit/uiviewpropertyanimator), référence du [protocole UITimingCurveProvider](https://developer.apple.com/reference/uikit/uitimingcurveprovider), référence de la [classe UICubicTimingParameters](https://developer.apple.com/reference/uikit/uicubictimingparameters) et [ Référence de la classe UISpringTimingParameter](https://developer.apple.com/reference/uikit/uispringtimingparameters) pour plus d’informations.
- Le nouveau `UIPreviewInteraction` et `UIPreviewInteractionDelegate` permet à l’application de développement de fournir une interface personnalisée pour les opérations de lecture et d’affichage.
- La nouvelle `UIAccessibilityCustomRotor` classe permet à l’application de fournir des fonctionnalités personnalisées, spécifiques au contexte, à des technologies d’assistance telles que la voix.
- Utilisez les `UIAccessibilityIsAssistiveTouchRunning` symboles `UIAccessibilityAssistiveTouchStatusDidChangeNotification` et pour déterminer si l’assistance tactile est activé.
- Utilisez les `UIAccessibilityHearingDevicePairedEar` symboles `UIAccessibilityHearingDevicePairedEarDidChangeNotification` et pour connaître l’état de toutes les aides auditives appariées par les IFM.
- Pour prendre en charge le type dynamique dans les étiquettes, les champs de texte `PreferredFontForTextStyle` et les zones `UIFont` de texte, utilisez la nouvelle méthode de la classe.
- Pour déterminer si un élément doit mettre à jour sa police lorsque l' `UIContentSizeCategory` appareil change, utilisez `AdjustsFontForContentSizeCategory` la propriété du `UIContentSizeCategoryAdjusting` délégué.
- La `OpenURL` méthode de la `UIApplication` classe est appelée de manière asynchrone et prend désormais en charge un gestionnaire d’achèvement qui est appelé une fois l’action d’ouverture terminée.
- Initiez le partage CloudKit et modifiez ses propriétés à `UICloudSharingController` l' `UICloudSharingControllerDelegate` aide des nouvelles classes et.
- Tirez parti des cellules préextraites pour améliorer l’expérience de défilement de `UICollectionViews` avec le nouveau `UICollectionViewDataSourcePrefetching` délégué.
- Le développeur peut désormais contrôler l’apparence du badge pour les éléments de la barre d’onglets (tels que le texte et la couleur d’arrière-plan).
- Le contrôle d’actualisation est désormais pris en charge dans toutes les sous-classes d’affichage de défilement `UICollectionView`et d’affichage de défilement (comme).

## <a name="webkit-enhancements"></a>Améliorations apportées à WebKit

Les améliorations suivantes ont été apportées à l’infrastructure WebKit dans iOS 10 :

- La prise en charge des aperçus et des `WKWebView` pop a été ajoutée à la classe. Utilisez la `ShouldPreviewElement` méthode pour déterminer si un affichage Web donné doit afficher un aperçu.


## <a name="related-links"></a>Liens associés

- [Exemples iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
- [Nouveautés d’iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)
