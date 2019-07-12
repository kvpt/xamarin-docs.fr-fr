---
title: Modifications des infrastructures supplémentaires iOS 10
description: Ce document décrit les changements mineurs et améliorations apportées à des infrastructures existantes dans iOS 10 et explique comment vous utilisez ces mises à jour dans Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 0E2217F1-FC96-4D0A-ABAB-D40AD8F96502
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: ac255baf44951518b29112d2903950039a80ee53
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831220"
---
# <a name="additional-ios-10-frameworks-changes"></a>Modifications des infrastructures supplémentaires iOS 10

_Cet article traite des modifications mineures supplémentaires ou des améliorations apportées aux infrastructures existantes pour iOS 10._

## <a name="av-foundation-framework-additions"></a>Ajouts de Framework AV Foundation

Le framework AVFoundation inclut les améliorations suivantes :

- Dans iOS 10, le développeur n’a plus à implémenter différentes [AVPlayerItem](https://developer.xamarin.com/api/type/AVFoundation.AVPlayerItem/) comportements selon le type de contenu. Il suffit de définir le `Rate` propriété et AVFoundation déterminent quand suffisamment contenu n’est disponible pour la lecture sans blocage.
- La nouvelle [AVCapturePhotoOutput](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureFileOutput/) classe remplace déconseillées `AVCaptureStillImageOutput` classe et fournit une méthode unifiée pour gérer tous les workflows de photographie en fournissant un contrôle sophistiqué et de surveillance du processus de capture et prend en charge pour les nouvelles fonctionnalités telles que les Photos en direct et le format de capture brutes.
- La nouvelle `AVPlayerLooper` classe facilite la boucle d’un élément donné de média pendant la lecture.
- Le `AVAssetDownloadURLSession` classe permet le téléchargement et une lecture ultérieure de FairPlay chiffré flux HLS.
- Par défaut, le [AVCaptureSession](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureSession/) classe prend en charge automatiquement capture à l’échelle-color, gamme lorsque le matériel du périphérique prend en charge. Consultez d’Apple [référence de compatibilité des appareils iOS](https://developer.apple.com/library/prerelease/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013599) pour plus d’informations.

## <a name="avkit-additions"></a>Ajouts de AVKit

Le framework AVKit inclut désormais le nouveau `UpdatesNowPlayingInfoCenter` propriété pour indiquer quand le centre d’information de jouer maintenant doit être mis à jour.

## <a name="core-data-enhancements"></a>Améliorations de données centrales

iOS 10 inclut les améliorations suivantes à l’infrastructure de données de base :

- Le [NSManagedObjectContext](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectContext/) objets avec des banques de données SQLite dans la prise en charge du Mode du Journal WAL la nouvelle génération de requête de fonctionnalités où contextes gérés de l’objet (MOC) peuvent être épinglés aux versions de base de données spécifique pour l’extraction des futures et transactions défaillante.
- Racine [NSManagedObjectContext](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectContext/) prend en charge les objets simultanés de provoquer une erreur et de récupération sans sérialisation.
- Le [NSPersistentStoreCoordinator](https://developer.xamarin.com/api/type/CoreData.NSPersistentStoreCoordinator/) classe gère un pool de magasins de données SQLite.
- Plusieurs nouvelles méthodes pratiques ont été ajoutés à `NSManagedObject` rend plus facile à effectuer des extractions et créer les sous-classes.
- À l’aide de la vue `NSPersistenceContainer` pour référencer le `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectModel/) et d’autres ressources de configuration des données de base.

Pour plus d’informations, consultez le site d’Apple [Core données Framework référence](https://developer.apple.com/reference/coredata).

## <a name="core-image-enhancements"></a>Améliorations de l’Image Core

iOS 10 apporte les améliorations suivantes à l’infrastructure de l’Image de base :

- Le développeur peut désormais traiter des images dans un espace de couleurs en dehors de l’espace colorimétrique de travail du contexte de l’Image de base par la conversion vers et depuis l’espace de couleurs avant et après le traitement.
- Pour les appareils iOS qui utilisent le A8 ou A9 processeurs, le format d’image RAW est maintenant pris en charge. Image Core prend désormais en charge pour l’appareil photo intégré iSight de décodage des images brutes à partir de l’ou d’un appareil photo de tiers 3e. Utilisez le `FilterWithImageData` ou `FilterWithImageURL` méthodes de la [CIFilter](https://developer.xamarin.com/api/type/CoreImage.CIFilter/) classe pour traiter les images RAW.
- Plusieurs améliorations de performances de rendu ont été apportées à `UIImage` rendu (si pris en charge par les magasins d’images Image Core) dans `UIImageView` objets. 
- `UIImage` objets avec balises gamme affichera comme gamme de couleurs dans `UIImageView` objets sur des appareils iOS qui prennent en charge la large couleur.
- Code de noyau d’Image Core peut maintenant demander des formats de sortie de pixel spécifique.
- Le `ImageWithExtent` méthode de la [CIFilter](https://developer.xamarin.com/api/type/CoreImage.CIFilter/) classe peut être utilisée pour insérer un traitement personnalisé dans l’opération de filtrage. Affiche ou appeler le rappel donné entre les filtres lors du traitement d’une image pour la sortie Image Core.

En outre, les nouveaux filtres Image Core suivants ont été ajoutées :

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

## <a name="core-motion-additions"></a>Ajouts de mouvement Core

Nouveau à iOS 10, le framework Core mouvement inclut les événements PODOMÈTRE permettant à une application de recevoir de rapides et des notifications en temps réel de l’utilisateur de suspension et reprise de suivi lors de son exécution. Utilisez le [CMPedometer](https://developer.xamarin.com/api/type/CoreMotion.CMPedometer/) pour enregistrer les événements de PODOMÈTRE au premier plan ou d’arrière-plan.

## <a name="foundation-enhancements"></a>Améliorations de base

Les améliorations suivantes ont été apportées à l’infrastructure Foundation pour iOS 10 :

- Utilisez la nouvelle [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) classe à mettre en forme des mesures localisées pour l’affichage à l’utilisateur final.
- Utilisez la nouvelle [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) classe pour effectuer les calculs d’intervalle de date et heure telles que des durées, pour comparer les intervalles et de test pour les intersections d’intervalle.
- Utilisez la nouvelle [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) classe pour convertir entre différentes unités de mesure (UDM) ou effectuer des calculs sur les valeurs d’unités différentes.

- Utilisez la nouvelle [NSUnit](https://developer.apple.com/reference/foundation/nsunit) et [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) classes pour représenter des unités spécifiques.
- Plusieurs nouvelles propriétés ont été ajoutées à la [NSLocal](https://developer.apple.com/reference/foundation/nslocale) classe pour acquérir les informations locales et les formats d’affichage disponible.

## <a name="gamekit-enhancements"></a>Améliorations de GameKit

Les améliorations suivantes ont été apportées à l’infrastructure GameKit dans iOS 10 :

- Le **Game Center application** a été déconseillé et supprimé à partir d’iOS. Si l’application utilise GameKit, il _doit_ présenter son propre interface pour afficher les fonctionnalités GameKit (leaderboards, etc.). 
- Un nouveau type de compte iCloud uniquement a été implémenté par le [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) classe.
- La nouvelle [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) classe fournit une solution généralisée pour la gestion de stockage des données persistantes sur Game Center. `GKGameSession` gère une liste de lecteurs et de l’application est chargée d’implémenter quand et comment date participant est stocké, récupéré ou échangé entre joueurs. Dans de nombreux cas Sessions de jeu peut remplacer les correspondances existants en alternance, correspondances en temps réel ou persistant jeu d’enregistrement méthodes.

## <a name="gameplaykit-enhancements"></a>Améliorations de GameplayKit

Les améliorations suivantes ont été apportées à l’infrastructure GameplayKit dans iOS 10 :

- Utilisez la nouvelle [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) classe pour fournir des chemins d’accès hautes performances, l’aspect naturel.
- Génération de bruit procédurales a été ajoutée peut être utilisée pour améliorer le réalisme des textures d’aspect naturel, ajouter réalisme aux mouvements de caméra et aider à générer des jeux aux univers élaborés.
- Utiliser le partitionnement Spatial pour partitionner les données du monde du jeu pour la recherche efficace.
- Un nouveau stratège de Monte-Carlo ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) a été ajoutée pour le calcul de déplacement possibles exhaustive.
- Prise en charge 3D a été ajouté à l’agent existant et les comportements de recherche de chemin d’accès à l’aide de la nouvelle [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) et [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) classes.
- La nouvelle [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) et [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) make classes combinant GameplayKit et SpriteKit plus facile que jamais.
- Une nouvelle API d’arborescence de décision a été ajoutée ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) et [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) pour améliorer l’intelligence artificielle de création de jeu.

## <a name="healthkit-enhancements"></a>Améliorations d’HealthKit

Les améliorations suivantes ont été apportées à l’infrastructure d’HealthKit dans iOS 10 :

- Nouvelles clés de métadonnées ont été ajoutées pour les types de météo (tel que `HKWeatherConditionClear` et `HKWeatherConditionCloudy`) et les types d’entraînement (tel que `HKWorkoutActivityTypeFlexibility` et `HKWorkoutActivityTypeWheelchairRunPace`) ont été ajoutés.
- La nouvelle `HKCDADocument` classe a été ajoutée pour représenter une Architecture de Document cliniques (CDA) mis en forme de document.
- Utilisez la nouvelle [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) classe pour spécifier le `ActivityType` et `LocationType` d’un exercice.
- La nouvelle [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) et `WheelchairUse` méthode de la [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) classe ont été ajoutées pour travailler avec fauteuil liés des données d’intégrité.

## <a name="homekit-enhancements"></a>Améliorations de HomeKit

Les améliorations suivantes ont été apportées à l’infrastructure d’HomeKit dans iOS 10 :

- Caractéristiques et nouveaux services ont été ajoutés.
- Un iPad peut être configuré pour agir comme un concentrateur d’HomeKit pour fournir un accès à distance accessoire, exécuter les déclencheurs d’automation et activer partagé des autorisations utilisateur.
- Prise en charge a été ajoutée pour les appareils photo et sonnette accessoires.
- Plus de contexte et de configurations ont été fournies pour les accessoires.

Consultez notre [présentation d’HomeKit](~/ios/platform/homekit.md) documentation pour plus d’informations.

## <a name="metal-enhancements"></a>Améliorations complètes

Les améliorations suivantes ont été apportées à l’infrastructure complète dans iOS 10 :

- Peuvent désormais utiliser les applications et jeux 3D _pavage_ pour afficher efficacement des scènes complexes et geometry via le GPU.
- Fournir un contrôle précis de l’allocation des ressources pour optimiser les performances de métal en fonction des applications à l’aide des ressources de segments de mémoire et cibles de rendu Memoryless.
- Spécialisation de la fonction permet de créer une collection fortement optimisé de matériel et les fonctions de combinaison clair pour une scène.

Pour plus d’informations, consultez le site d’Apple [Guide de programmation complète](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221).

## <a name="modelio-enhancements"></a>Améliorations de ModelIO

Les améliorations suivantes ont été apportées à l’infrastructure ModelIO dans iOS 10 :

- Le format de fichier (USD) est maintenant pris en charge.
- Signé champ Distance prise en charge a été ajouté à la [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) classe.
- Utilisez la nouvelle `MDLLightProbeIrradianceDataSource` classe pour faciliter la sélection élective de sondage de la lumière.
- Utilisez la nouvelle `MDLMaterialPropertyGraph` classe facilement prendre en charge runtime remplace les modèles.

## <a name="photos-enhancements"></a>Améliorations de photos

Les améliorations suivantes ont été apportées à l’infrastructure de Photos dans iOS 10 :

- Utilisez le [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) et [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) classes pour tirer parti de la nouvelle fonctionnalité de processeur Core Image pour effectuer des modifications.
- Modification dynamique de Photo est désormais disponible pour les applications qui prennent en charge l’infrastructure de Photos et aux extensions de type retouche photo (pour une utilisation à l’intérieur de la photo et Photos applications).
- Utilisez la nouvelle [PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) classe pour appliquer des modifications à la vidéo et toujours le contenu de Photos en direct.

## <a name="replaykit-enhancements"></a>Améliorations de ReplayKit

Les améliorations suivantes ont été apportées à l’infrastructure ReplayKit dans iOS 10 :

- Utilisez le [RPScreenRecorder](https://developer.apple.com/reference/replaykit/rpscreenrecorder), [RPBroadcastActivityViewController](https://developer.apple.com/reference/replaykit/rpbroadcastactivityviewcontroller) et [RPBroadcastController](https://developer.apple.com/reference/replaykit/rpbroadcastcontroller) classes pour prendre en charge la diffusion d’enregistrement des éléments multimédias via 3ème partie sites.
- Les extensions de l’IU de diffusion et de charger de diffusion sont requis pour prendre en charge des services de diffusion ReplayKit 3e partie dans l’application.

## <a name="scenekit-enhancements"></a>Améliorations de SceneKit

Les améliorations suivantes ont été apportées à l’infrastructure SceneKit dans iOS 10 :

- Le [SCNCamera](xref:SceneKit.SCNCamera) classe peut fournir réalisme supérieur à l’aide des fonctionnalités HDR et effets. Exposition adaptative permet de créer des effets automatique ou utilisez vignettage, frange de couleur et dégradé de couleur pour ajouter des effets de fillmatic au jeu.
- SceneKit inclut désormais un nouveau système physiquement en fonction de rendu (PBR) pour des résultats plus réalistes avec la création de ressource plus simple.
- Utilisez la nouvelle [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) ombrage modéliser une grande variété d’effets d’ombrage réaliste au produit tout en nécessitant uniquement trois propriétés fondamentales (`Diffuse`, `Metalness` et `Roughness`).
- Depuis PBR ombrage fonctionne mieux avec éclairage basé sur l’environnement, utilisez le `LightingEnvironment` propriété à attribuer un éclairage basée sur une image à une scène entière.
- Utilisez le `IESProfileURL` propriété à importer des appareils d’éclairage réel qui définissent l’éclairage en fonction des valeurs de monde réel tels que l’intensité (lumens) et la température de couleur (en degrés Kelvin).
- Fonctionnalités d’appareil photo PBR et HDR fournissent les meilleurs résultats que les techniques traditionnelles de rendu et, par conséquent, SceneKit procède désormais à tous les calculs de couleur dans un espace de couleur linéaire (à l’aide de la gamme de couleurs P3 sur les écrans de couleur à l’échelle de l’appareil).
- SceneKit maintenant couleur correspond à toutes les couleurs en lisant les informations de profil de couleur.
- SceneKit interprète les valeurs de composant de couleur dans un espace de couleurs RVB linéaire pour tous les types de nuanceur.
- Rendu de l’espace de couleurs à la fois linéaire et l’échelle de couleurs peut être désactivée en spécifiant le `SCNDisableLinearSpaceRendering` et `SCNDisableWideGamut` clés dans l’application `Info.plist`.
- Générer des primates polygone arbitraire (soit chargé à partir de fichiers soit généré par programmation) pour spécifier la géométrie avec le nouveau [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon) classe.
- Dans la mesure où SceneKit lit et ajuste en fonction des informations de profil de couleurs dans les images de la texture, utilisez les catalogues de composants pour toutes les images pour garantir que ces informations sont fournies.

## <a name="spritekit-enhancements"></a>Améliorations de SpriteKit

Les améliorations suivantes ont été apportées à l’infrastructure SpriteKit dans iOS 10 :

- Des nuanceurs personnalisés peuvent fournir des attributs (`SKAttribute`) qui peut être configuré séparément à chaque nœud qui utilise le nuanceur en fournissant une valeur d’attribut (`SKAttributeValue`).
- Tilemaps prennent désormais en charge la vignette carrée, hexagonale et isométrique formes pour 2D, 2,5 D et défilement à côté des jeux à l’aide de la `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` et `SKTileSet` classes.
- Utilisez la nouvelle `SKWarpGeometry` classe pour étirer ou de déformer [SKSpriteNode](https://developer.xamarin.com/api/type/SpriteKit.SKSpriteNode/) ou [SKEffectNode](https://developer.xamarin.com/api/type/SpriteKit.SKEffectNode/) rendu. La nouvelle [SKAction](https://developer.xamarin.com/api/type/SpriteKit.SKAction/) classe peut être utilisée pour animer des transitions entre les effets de warp.
- Le [SKView](https://developer.xamarin.com/api/type/SpriteKit.SKView/) classe fournit plusieurs nouvelles méthodes pour donner un contrôle affiné sur quand et comment une scène est affichée.

## <a name="scrollview-enhancements"></a>Améliorations de ScrollView

Les améliorations suivantes ont été apportées au contrôle dans iOS 10.3 ScrollView :

- `UIScrollView` incluent désormais le `IndexDisplayMode` propriété pour contrôler la manière dont l’index apparaissent alors que l’utilisateur fait défiler comme un `UIScrollViewIndexDisplayMode` de :
    - `Automatic` -L’affichage de l’index est contrôlée par le système d’exploitation.
    - `AlwaysHidden` -L’affichage de l’index est toujours masqué.

Consultez le [iOSTenThree exemple](https://developer.xamarin.com/samples/monotouch/iOS10/iOSTenThree) pour utilisation.

## <a name="uikit-enhancements"></a>Améliorations de UIKit

Les améliorations suivantes ont été apportées au framework UIKit dans iOS 10 :

- La nouvelle [UIPasteboard](xref:UIKit.UIPasteboard) API fournit de nouvelles options (telles que les limitations de la durée de vie) et déclarent automatiquement les types de contenu compatibles pour les types courants de classe.
- Nouvelle prise en charge d’animation interruptible, totalement interactives fondé sur l’objet a été ajouté et peut être lié aux gestes. Consultez d’Apple [UIViewAnimating Protocol Reference](https://developer.apple.com/reference/uikit/uiviewanimating), [référence de classe UIViewPropertyAnimator](https://developer.apple.com/reference/uikit/uiviewpropertyanimator), [UITimingCurveProvider protocole référence](https://developer.apple.com/reference/uikit/uitimingcurveprovider), [Référence de classe UICubicTimingParameters](https://developer.apple.com/reference/uikit/uicubictimingparameters) et [référence de classe UISpringTimingParameter](https://developer.apple.com/reference/uikit/uispringtimingparameters) pour plus d’informations.
- La nouvelle `UIPreviewInteraction` et `UIPreviewInteractionDelegate` autoriser l’application de développeur de fournir une interface personnalisée pour les opérations de lecture et pop.
- La nouvelle `UIAccessibilityCustomRotor` classe permet à l’application fournir des fonctionnalités personnalisées, spécifiques à un contexte pour les technologies d’assistance telles que le doublage.
- Utilisez le `UIAccessibilityIsAssistiveTouchRunning` et `UIAccessibilityAssistiveTouchStatusDidChangeNotification` symboles pour déterminer si l’assistance tactile est activé.
- Utilisez le `UIAccessibilityHearingDevicePairedEar` et `UIAccessibilityHearingDevicePairedEarDidChangeNotification` pour obtenir l’état de tous les symboles associés auditifs IFM.
- Pour prendre en charge de Type dynamique dans les étiquettes, utilisent les nouveaux champs de texte et des zones de texte `PreferredFontForTextStyle` méthode de la `UIFont` classe.
- Pour déterminer si un élément doit mettre à jour sa police lorsque l’appareil `UIContentSizeCategory` modifiée, utilisez la `AdjustsFontForContentSizeCategory` propriété de la `UIContentSizeCategoryAdjusting` déléguer.
- Le `OpenURL` méthode de la `UIApplication` classe est appelée de façon asynchrone et prend désormais en charge un gestionnaire d’achèvement qui est appelée après l’action d’ouverture est terminé.
- Démarrer le partage de CloudKit et modifiez ses propriétés en utilisant la nouvelle `UICloudSharingController` et `UICloudSharingControllerDelegate` classes.
- Tirer parti des cellules prérécupérées pour améliorer l’expérience de défilement de `UICollectionViews` avec la nouvelle `UICollectionViewDataSourcePrefetching` déléguer.
- Le développeur peut contrôler maintenant l’apparence du badge pour les éléments de barre d’onglet (par exemple, la couleur de texte et d’arrière-plan).
- Le contrôle de l’actualisation est désormais pris en charge dans toutes les affichage du défilement et les sous-classes de vue de défilement (tels que `UICollectionView`).

## <a name="webkit-enhancements"></a>Améliorations de WebKit

Les améliorations suivantes ont été apportées à l’infrastructure WebKit dans iOS 10 :

- Aperçu et pop prise en charge a été ajouté à la `WKWebView` classe. Utilisez le `ShouldPreviewElement` méthode pour déterminer si une vue web donnée doit afficher un aperçu.


## <a name="related-links"></a>Liens associés

- [Exemples iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Quelles sont les nouveautés dans iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)
