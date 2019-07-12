---
title: Modifications des infrastructures supplémentaires tvOS 10
description: Ce document décrit les changements mineurs et améliorations apportées à des infrastructures existantes dans iOS 10. Il aborde les mises à jour de AVFoundation, AVKit, données de base, graphismes de base, Foundation, GameKit, GameplayKit et bien plus encore.
ms.prod: xamarin
ms.assetid: F771640A-F92E-4954-82D5-2D720434971E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 3eccba01f235382b7969a2f4a122c09ce9b4127b
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832345"
---
# <a name="additional-tvos-10-frameworks-changes"></a>Modifications des infrastructures supplémentaires tvOS 10

En plus des modifications majeures à tvOS, Apple a apporté des modifications et améliorations apportées à plusieurs infrastructures existantes dans tvOS 10.

<a name="AV-Foundation-Framework" />

## <a name="avfoundation-framework-additions"></a>Ajouts de AVFoundation Framework

Le framework AVFoundation inclut les améliorations suivantes :

- Dans tvOS 10, l’application n’implémente plus différents [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) comportements selon le type de contenu. Il suffit de définir le `Rate` propriété et AVFoundation déterminent quand suffisamment contenu n’est disponible pour la lecture sans blocage.
 - La nouvelle `AVPlayerLooper` classe facilite la boucle d’un élément donné de média pendant la lecture.

<a name="AVKit-Framework-Enhancements" />

## <a name="avkit-framework-enhancements"></a>Améliorations AVKit Framework

Le framework AVKit inclut les améliorations suivantes :

- L’application dispose maintenant d’un contrôle sur le comportement est ignoré de la [AVPlayerViewController](https://developer.apple.com/reference/avkit/avplayerviewcontroller) donc un mouvement est ignoré peut passer à l’élément suivant dans la sélection ou l’avance au sein de l’élément actuel.

<a name="Core-Data-Enhancements" />

## <a name="core-data-enhancements"></a>Améliorations de données centrales

tvOS 10 inclut les améliorations suivantes à l’infrastructure de données de base :

- Racine [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) prend en charge les objets simultanés de provoquer une erreur et de récupération sans sérialisation.
- Le [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) classe gère un pool de magasins de données SQLite.
- Le [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) objets avec des banques de données SQLite dans la prise en charge du Mode du Journal WAL la nouvelle génération de requête de fonctionnalités où contextes gérés de l’objet (MOC) peuvent être épinglés aux versions de base de données spécifique pour l’extraction des futures et transactions défaillante.
- À l’aide de la vue `NSPersistenceContainer` pour référencer le `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) et d’autres ressources de configuration des données de base.
- Plusieurs nouvelles méthodes pratiques ont été ajoutés à `NSManagedObject` rend plus facile à effectuer des extractions et créer les sous-classes.

Pour plus d’informations, consultez le site d’Apple [Core données Framework référence](https://developer.apple.com/reference/coredata).

<a name="Core-Graphics-Enhancements" />

## <a name="core-graphics-enhancements"></a>Améliorations des graphiques Core

tvOS 10 inclut les améliorations suivantes à l’infrastructure principale de graphiques :

- La nouvelle [CGColorConverterRef](https://developer.apple.com/reference/coregraphics/cgcolorconverterref) classe peut être utilisée pour effectuer une série de conversions de couleur.

<a name="Core-Image-Enhancements" />

## <a name="core-image-enhancements"></a>Améliorations de l’Image Core

tvOS 10 apporte les améliorations suivantes à l’infrastructure de l’Image de base :

- Le `ImageWithExtent` méthode de la [CIFilter](https://developer.apple.com/reference/coreimage/cifilter) classe peut être utilisée pour insérer un traitement personnalisé dans l’opération de filtrage. Affiche ou appeler le rappel donné entre les filtres lors du traitement d’une image pour la sortie Image Core.
- L’application peut désormais traiter des images dans un espace de couleurs en dehors de l’espace colorimétrique de travail du contexte de l’Image de base par la conversion vers et depuis l’espace de couleurs avant et après le traitement.
- Plusieurs améliorations de performances de rendu ont été apportées à `UIImage` rendu (si pris en charge par les magasins d’images Image Core) dans `UIImageView` objets. 
- `UIImage` objets avec balises gamme affichera comme gamme de couleurs dans `UIImageView` objets sur des appareils iOS qui prennent en charge la large couleur.
- Code de noyau d’Image Core peut maintenant demander des formats de sortie de pixel spécifique.

En outre, les nouveaux filtres Image Core suivants ont été ajoutées :

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

<a name="Foundation-Enhancements" />

## <a name="foundation-enhancements"></a>Améliorations de base

Les améliorations suivantes ont été apportées à l’infrastructure Foundation pour tvOS 10 :

- Utilisez la nouvelle [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) classe pour effectuer les calculs d’intervalle de date et heure telles que des durées, pour comparer les intervalles et de test pour les intersections d’intervalle.
- Plusieurs nouvelles propriétés ont été ajoutées à la [NSLocal](https://developer.apple.com/reference/foundation/nslocale) classe pour acquérir les informations locales et les formats d’affichage disponible.
- Utilisez la nouvelle [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) classe pour convertir entre différentes unités de mesure (UDM) ou effectuer des calculs sur les valeurs d’unités différentes.
- Utilisez la nouvelle [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) classe à mettre en forme des mesures localisées pour l’affichage à l’utilisateur final.
- Utilisez la nouvelle [NSUnit](https://developer.apple.com/reference/foundation/nsunit) et [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) classes pour représenter des unités spécifiques.

<a name="GameKit-Enhancements" />

## <a name="gamekit-enhancements"></a>Améliorations de GameKit

Les améliorations suivantes ont été apportées à l’infrastructure GameKit dans tvOS 10 :

- Un nouveau type de compte iCloud uniquement a été implémenté par le [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) classe.
- La nouvelle [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) classe fournit une solution généralisée pour la gestion de stockage des données persistantes sur Game Center. `GKGameSession` gère une liste de joueurs et que l’application est responsable de la mise en œuvre quand et comment date participant est stocké, récupérés ou échangés entre joueurs. Dans de nombreux cas Sessions de jeu peut remplacer les correspondances existants en alternance, correspondances en temps réel ou persistant jeu d’enregistrement méthodes.

<a name="GameplayKit-Enhancements" />

## <a name="gameplaykit-enhancements"></a>Améliorations de GameplayKit

Les améliorations suivantes ont été apportées à l’infrastructure GameplayKit dans tvOS 10 :

- Génération de bruit procédurales a été ajoutée peut être utilisée pour améliorer le réalisme des textures d’aspect naturel, ajouter réalisme aux mouvements de caméra et aider à générer des jeux aux univers élaborés.
- Utiliser le partitionnement Spatial pour partitionner les données du monde du jeu pour la recherche efficace.
- Un nouveau stratège de Monte-Carlo ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) a été ajoutée pour le calcul de déplacement possibles exhaustive.
- Une nouvelle API d’arborescence de décision a été ajoutée ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) et [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) pour améliorer l’intelligence artificielle de création de jeu.
- Prise en charge 3D a été ajouté à l’agent existant et les comportements de recherche de chemin d’accès à l’aide de la nouvelle [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) et [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) classes.
- Utilisez la nouvelle [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) classe pour fournir des chemins d’accès hautes performances, l’aspect naturel.
- La nouvelle [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) et [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) make classes combinant GameplayKit et SpriteKit plus facile que jamais.

<a name="Metal-Enhancements" />

## <a name="metal-enhancements"></a>Améliorations complètes

Les améliorations suivantes ont été apportées à l’infrastructure complète dans tvOS 10 :

- Peuvent désormais utiliser les applications et jeux 3D _pavage_ pour afficher efficacement des scènes complexes et geometry via le GPU.
- Spécialisation de la fonction permet de créer une collection fortement optimisé de matériel et les fonctions de combinaison clair pour une scène.
- Fournir un contrôle précis de l’allocation des ressources pour optimiser les performances de métal en fonction des applications à l’aide des ressources de segments de mémoire et cibles de rendu Memoryless.

Pour plus d’informations, consultez le site d’Apple [Guide de programmation complète](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221).

<a name="Metal-Performance-Shaders-Enhancements" />

## <a name="metal-performance-shaders-enhancements"></a>Améliorations des nuanceurs de performances complète

Les améliorations suivantes ont été apportées à l’infrastructure complète performances nuanceurs dans tvOS 10 :

- Plusieurs nouveaux noyaux ont été ajoutées à l’infrastructure complète performances nuanceurs pour permettre à l’application tirer parti des calculs hautement optimisée, données parallèles telles que les conversions d’espace de couleurs et les opérations de réseau neuronal.

<a name="ModelIO-Enhancements" />

## <a name="modelio-enhancements"></a>Améliorations de ModelIO

Les améliorations suivantes ont été apportées à l’infrastructure ModelIO dans tvOS 10 :

- Le format de fichier (USD) est maintenant pris en charge.
- Utilisez la nouvelle `MDLMaterialPropertyGraph` classe facilement prendre en charge runtime remplace les modèles.
- Signé champ Distance prise en charge a été ajouté à la [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) classe.
- Utilisez la nouvelle `MDLLightProbeIrradianceDataSource` classe pour faciliter la sélection élective de sondage de la lumière.

<a name="SceneKit-Enhancements" />

## <a name="scenekit-enhancements"></a>Améliorations de SceneKit

Les améliorations suivantes ont été apportées à l’infrastructure SceneKit dans tvOS 10 :

- SceneKit inclut désormais un nouveau système physiquement en fonction de rendu (PBR) pour des résultats plus réalistes avec la création de ressource plus simple.
- Utilisez la nouvelle [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) ombrage modéliser une grande variété d’effets d’ombrage réaliste au produit tout en nécessitant uniquement trois propriétés fondamentales (`Diffuse`, `Metalness` et `Roughness`).
- Depuis PBR ombrage fonctionne mieux avec éclairage basé sur l’environnement, utilisez le `LightingEnvironment` propriété à attribuer à base d’images d’éclairage à tan scène entière.
- Utilisez le `IESProfileURL` propriété à importer des appareils d’éclairage réel qui définissent la base d’éclairage sur les valeurs réelles telles qu’intensité (lumens) et la couleur de température (en degrés Kelvin).
- Le [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera) classe peut fournir réalisme supérieur à l’aide des fonctionnalités HDR et effets. Exposition adaptative permet de créer des effets automatique ou utilisez vignettage, frange de couleur et dégradé de couleur pour ajouter des effets filmatic au jeu.
- Fonctionnalités d’appareil photo PBR et HDR fournissent les meilleurs résultats que les techniques traditionnelles de rendu et, par conséquent, SceneKit procède désormais à tous les calculs de couleur dans un espace de couleur linéaire (à l’aide de la gamme de couleurs P3 sur les écrans de couleur à l’échelle de l’appareil).
- SceneKit maintenant couleur correspond à toutes les couleurs en lisant les informations de profil de couleur.
- SceneKit interprète les valeurs de composant de couleur dans un espace de couleurs RVB linéaire pour tous les types de nuanceur.
- Dans la mesure où SceneKit lit et ajuste en fonction des informations de profil de couleurs dans les images de la texture, utilisez les catalogues de composants pour toutes les images pour garantir que ces informations sont fournies.
- Rendu de l’espace de couleurs à la fois linéaire et l’échelle de couleurs peut être désactivée en spécifiant le `SCNDisableLinearSpaceRendering` et `SCNDisableWideGamut` clés dans l’application `Info.plist`.
- Générer des primates polygone arbitraire (soit chargé à partir de fichiers soit généré par programmation) pour spécifier la géométrie avec le nouveau [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon) classe.

<a name="SpriteKit-Enhancements" />

## <a name="spritekit-enhancements"></a>Améliorations de SpriteKit

Les améliorations suivantes ont été apportées à l’infrastructure SpriteKit dans tvOS 10 :

- Tilemaps prennent désormais en charge la vignette carrée, hexagonale et isométrique formes pour 2D, 2,5 D et défilement à côté des jeux à l’aide de la `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` et `SKTileSet` classes.
- Utilisez la nouvelle `SKWarpGeometry` classe pour étirer ou de déformer [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) ou [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) rendu. La nouvelle [SKAction](https://developer.apple.com/reference/spritekit/skaction) classe peut être utilisée pour animer des transitions entre les effets de warp.
- Des nuanceurs personnalisés peuvent fournir des attributs (`SKAttribute`) qui peut être configuré séparément à chaque nœud qui utilise le nuanceur en fournissant une valeur d’attribut (`SKAttributeValue`).
- Le [SKView](https://developer.apple.com/reference/spritekit/skview) classe fournit plusieurs nouvelles méthodes pour donner un contrôle affiné sur quand et comment une scène est affichée.

<a name="UIKit-Enhancements" />

## <a name="uikit-enhancements"></a>Améliorations de UIKit

Les améliorations suivantes ont été apportées au framework UIKit dans tvOS 10 :

- Le focus API a été amélioré pour prendre en charge de focus de non afficher l’élément en outre à `UIViews`. Les éléments qui prennent en charge le focus _doit_ implémenter la `IUIFocusItem` interface.
- La nouvelle `UIGraphicsRender` classe fournit une méthode orientée objet de création des fichiers bitmap ou des fichiers PDF à partir de UIKit rendu ou Core Graphics et remplace déconseillées `UIGraphicsBeginImageContext` (méthode).
- Le `UIUserInterfaceStyle` classe a été ajoutée pour déterminer quel thème de l’interface utilisateur (sombre ou clair) est actuellement actif.
- Nouvelle prise en charge d’animation interruptible, totalement interactives fondé sur l’objet a été ajouté et van être lié aux gestes. De Veuillez consultez Apple [UIViewAnimating Protocol Reference](https://developer.apple.com/reference/uikit/uiviewanimating), [référence de classe UIViewPropertyAnimator](https://developer.apple.com/reference/uikit/uiviewpropertyanimator), [UITimingCurveProvider protocole référence](https://developer.apple.com/reference/uikit/uitimingcurveprovider), [Référence de classe UICubicTimingParameters](https://developer.apple.com/reference/uikit/uicubictimingparameters) et [référence de classe UISpringTimingParameter](https://developer.apple.com/reference/uikit/uispringtimingparameters) pour plus d’informations.
- La nouvelle `UIPreviewInteraction` et `UIPreviewInteractionDelegate` permettent à l’application fournir une interface personnalisée pour les opérations de lecture et pop.
- La nouvelle `UIAccessibilityCustomRotor` classe permet à l’application fournir des fonctionnalités personnalisées, spécifiques à un contexte pour les technologies d’assistance telles que le doublage.
- Utilisez le `UIAccessibilityIsAssistiveTouchRunning` et `UIAccessibilityAssistiveTouchStatusDidChangeNotification` symboles pour déterminer si l’assistance tactile est activé.
- Utilisez le `UIAccessibilityHearingDevicePairedEar` et `UIAccessibilityHearingDevicePairedEarDidChangeNotification` pour obtenir l’état de tous les symboles associés auditifs IFM.
- La nouvelle [UIPasteboard](https://developer.apple.com/reference/uikit/uipasteboard) API fournit de nouvelles options (telles que les limitations de la durée de vie) et déclarent automatiquement les types de contenu compatibles pour les types courants de classe.
- Pour prendre en charge de Type dynamique dans les étiquettes, utilisent les nouveaux champs de texte et des zones de texte `PreferredFontForTextStyle` méthode de la `UIFont` classe.
- Pour déterminer si un élément doit mettre à jour police lorsque les appareils `UIContentSizeCategory` modifiée, utilisez la `AdjustsFontForContentSizeCategory` propriété de la `UIContentSizeCategoryAdjusting` déléguer.
- L’application peut désormais contrôler l’apparence du badge pour les éléments de barre d’onglet telles que la couleur de texte et d’arrière-plan.
- Le contrôle de l’actualisation maintenant pris en charge dans toutes les vues de défilement et défilement sous-classes (tel que `UICollectionView`).
- Le `OpenURL` méthode de la `UIApplication` classe est appelée de façon asynchrone prend désormais en charge un gestionnaire d’achèvement qui est appelée après l’ouverture est terminé.
- Démarrer le partage de CloudKit et modifiez ses propriétés en utilisant la nouvelle `UICloudSharingController` et `UICloudSharingControllerDelegate` classes.
- Tirer parti des cellules prérécupérées pour améliorer l’expérience de défilement de `UICollectionViews` avec la nouvelle `UICollectionViewDataSourcePrefetching` déléguer.



## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Quelles sont les nouveautés dans tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
