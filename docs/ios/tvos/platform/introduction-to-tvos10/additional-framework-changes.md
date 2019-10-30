---
title: Modifications supplémentaires apportées au Framework tvOS 10
description: Ce document décrit les modifications mineures et les améliorations apportées aux Frameworks existants dans iOS 10. Il aborde les mises à jour apportées à AVFoundation, AVKit, Core Data, Core Graphics, Foundation, GameKit, GameplayKit, etc.
ms.prod: xamarin
ms.assetid: F771640A-F92E-4954-82D5-2D720434971E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 1339a6c2909c7ba62592d66dcdf08bcfd2e668a4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030637"
---
# <a name="additional-tvos-10-frameworks-changes"></a>Modifications supplémentaires apportées au Framework tvOS 10

Outre les modifications majeures apportées à tvOS, Apple a apporté des modifications et des améliorations à plusieurs infrastructures existantes dans tvOS 10.

<a name="AV-Foundation-Framework" />

## <a name="avfoundation-framework-additions"></a>Ajouts du Framework AVFoundation

L’infrastructure AVFoundation intègre les améliorations suivantes :

- Dans tvOS 10, l’application n’implémente plus de comportements [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) basés sur le type de contenu. Définissez simplement la propriété `Rate` et AVFoundation déterminera quand un contenu suffisant sera disponible pour la lecture sans blocage.
- La nouvelle classe `AVPlayerLooper` facilite la boucle d’un élément multimédia donné pendant la lecture.

<a name="AVKit-Framework-Enhancements" />

## <a name="avkit-framework-enhancements"></a>Améliorations apportées à AVKit Framework

L’infrastructure AVKit intègre les améliorations suivantes :

- L’application contrôle désormais le comportement ignoré de [AVPlayerViewController](https://developer.apple.com/reference/avkit/avplayerviewcontroller) de sorte qu’un geste ignoré peut passer à l’élément suivant dans la sélection ou avancer dans l’élément actuel.

<a name="Core-Data-Enhancements" />

## <a name="core-data-enhancements"></a>Améliorations des données de base

tvOS 10 comprend les améliorations suivantes apportées à l’infrastructure de données de base :

- Les objets [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) racines prennent en charge l’erreur et l’extraction simultanés sans sérialisation.
- La classe [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) gère un pool de banques de données SQLite.
- Les objets [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) avec les banques de données sqlite dans le mode journal de Wal prennent en charge la nouvelle fonctionnalité de génération de requêtes dans laquelle les contextes d’objets managés (MOC) peuvent être épinglés à des versions de base de données spécifiques pour des transactions de récupération et de défaillance futures.
- Utilisation du `NSPersistenceContainer` de haut niveau pour référencer les ressources de configuration de `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) et autres données de base.
- Plusieurs nouvelles méthodes pratiques ont été ajoutées à `NSManagedObject` ce qui facilite l’exécution d’extractions et la création de sous-classes.

Pour plus d’informations, consultez Référence de l' [infrastructure de données de base](https://developer.apple.com/reference/coredata)d’Apple.

<a name="Core-Graphics-Enhancements" />

## <a name="core-graphics-enhancements"></a>Améliorations des graphiques de base

tvOS 10 comprend les améliorations suivantes apportées à l’infrastructure graphique principale :

- La nouvelle classe [CGColorConverterRef](https://developer.apple.com/reference/coregraphics/cgcolorconverterref) peut être utilisée pour effectuer une série de conversions de couleurs.

<a name="Core-Image-Enhancements" />

## <a name="core-image-enhancements"></a>Améliorations de l’image principale

tvOS 10 apporte les améliorations suivantes à l’infrastructure d’images principales :

- La méthode `ImageWithExtent` de la classe [CIFilter](https://developer.apple.com/reference/coreimage/cifilter) peut être utilisée pour insérer un traitement personnalisé dans l’opération de filtrage. L’image principale appellera le rappel donné entre les filtres lors du traitement d’une image pour la sortie ou l’affichage.
- L’application peut maintenant traiter les images dans un espace de couleurs en dehors de l’espace de couleurs de travail du contexte d’image principal en convertissant dans et en dehors de l’espace de couleurs avant et après le traitement.
- Plusieurs améliorations des performances de rendu ont été apportées à `UIImage` le rendu (en cas de stockage d’images d’images principales) dans `UIImageView` objets. 
- les objets `UIImage` avec une large gamme sont rendus sous la forme d’une large gamme de couleurs dans les objets `UIImageView` sur les appareils iOS qui prennent en charge la couleur large.
- Le code noyau de l’image principale peut désormais demander des formats de sortie pixel spécifiques.

En outre, les nouveaux filtres d’image principaux suivants ont été ajoutés :

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

<a name="Foundation-Enhancements" />

## <a name="foundation-enhancements"></a>Améliorations de base

Les améliorations suivantes ont été apportées à l’infrastructure de base pour tvOS 10 :

- Utilisez la nouvelle classe [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) pour effectuer des calculs d’intervalles de date et d’heure tels que des durées, pour comparer des intervalles et tester des intersections d’intervalle.
- Plusieurs nouvelles propriétés ont été ajoutées à la classe [NSLocal](https://developer.apple.com/reference/foundation/nslocale) pour obtenir des informations locales et les formats d’affichage disponibles.
- Utilisez la nouvelle classe [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) pour effectuer une conversion entre différentes unités de mesure ou effectuer des calculs sur des valeurs dans différents UOMs.
- Utilisez la nouvelle classe [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) pour mettre en forme les mesures localisées à afficher à l’utilisateur final.
- Utilisez les nouvelles classes [NSUnit](https://developer.apple.com/reference/foundation/nsunit) et [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) pour représenter des UOMs spécifiques.

<a name="GameKit-Enhancements" />

## <a name="gamekit-enhancements"></a>Améliorations apportées à GameKit

Les améliorations suivantes ont été apportées à l’infrastructure GameKit dans tvOS 10 :

- Un nouveau type de compte iCloud est implémenté par la classe [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) .
- La nouvelle classe [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) fournit une solution généralisée pour la gestion du stockage de données persistant sur Game Center. `GKGameSession` gère une liste de joueurs et l’application est responsable de la mise en œuvre de la manière et du moment où la date du participant est stockée, récupérée ou échangée entre les joueurs. Dans de nombreux cas, les sessions de jeu peuvent remplacer les correspondances basées sur les activations existantes, les correspondances en temps réel ou les méthodes d’enregistrement de jeu persistantes.

<a name="GameplayKit-Enhancements" />

## <a name="gameplaykit-enhancements"></a>Améliorations apportées à GameplayKit

Les améliorations suivantes ont été apportées à l’infrastructure GameplayKit dans tvOS 10 :

- La génération de bruit procédurale a été ajoutée et peut être utilisée pour améliorer le réalisme des textures à l’aspect naturel, ajouter un réalisme aux mouvements de l’appareil photo et aider à générer des mondes riches en jeux.
- Utilisez le partitionnement spatial pour partitionner les données du monde du jeu pour une recherche efficace.
- Un nouveau stratège de Monte-Carlo ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) a été ajouté pour un calcul de déplacement exhaustif possible.
- Une nouvelle API d’arbre de décision a été ajoutée ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) et [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) pour améliorer la création de jeux.
- la prise en charge 3D a été ajoutée aux comportements de l’agent et du chemin d’accès existants à l’aide des nouvelles classes [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) et [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) .
- Utilisez la nouvelle classe [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) pour fournir des chemins d’accès à hautes performances et à l’aspect naturel.
- Les nouvelles classes [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) et [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) rendent la combinaison de GameplayKit et SpriteKit plus facile que jamais.

<a name="Metal-Enhancements" />

## <a name="metal-enhancements"></a>Améliorations des métaux

Les améliorations suivantes ont été apportées à l’infrastructure métallique dans tvOS 10 :

- les applications et les jeux 3D peuvent désormais utiliser la _facettisation_ pour restituer efficacement des scènes et une géométrie complexes via le GPU.
- Utilisez la spécialisation de fonction pour créer une collection hautement optimisée de fonctions de combinaison de matériau et de lumière pour une scène.
- Fournir un contrôle affiné de l’allocation des ressources pour optimiser les performances des applications basées sur le métal à l’aide de segments de ressources et de cibles de rendu sans mémoire.

Pour plus d’informations, consultez le [Guide de programmation métallique](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)d’Apple.

<a name="Metal-Performance-Shaders-Enhancements" />

## <a name="metal-performance-shaders-enhancements"></a>Améliorations des nuanceurs de performances métalliques

Les améliorations suivantes ont été apportées à l’infrastructure des nuanceurs de performances métalliques dans tvOS 10 :

- De nombreux nouveaux noyaux ont été ajoutés à l’infrastructure des nuanceurs de performances métalliques pour permettre à l’application de tirer parti des calculs en parallèle de données hautement optimisés, tels que les conversions d’espace colorimétrique et les opérations de réseau neuronal.

<a name="ModelIO-Enhancements" />

## <a name="modelio-enhancements"></a>Améliorations apportées à ModelIO

Les améliorations suivantes ont été apportées à l’infrastructure ModelIO dans tvOS 10 :

- Le format de fichier USD est désormais pris en charge.
- Utilisez la nouvelle classe `MDLMaterialPropertyGraph` pour prendre en charge facilement les modifications du runtime pour les modèles.
- La prise en charge des champs à distance signée a été ajoutée à la classe [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) .
- Utilisez la nouvelle classe `MDLLightProbeIrradianceDataSource` pour faciliter le positionnement de la sonde légère.

<a name="SceneKit-Enhancements" />

## <a name="scenekit-enhancements"></a>Améliorations apportées à SceneKit

Les améliorations suivantes ont été apportées à l’infrastructure SceneKit dans tvOS 10 :

- SceneKit comprend maintenant un nouveau système de rendu physique (PBR) pour des résultats plus réalistes avec la création de ressources plus simple.
- Utilisez le nouveau modèle [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) Shading pour produit un large éventail d’effets d’ombrage réalistes tout en exigeant uniquement trois propriétés fondamentales (`Diffuse`, `Metalness` et `Roughness`).
- Étant donné que l’ombrage PBR fonctionne mieux avec l’éclairage basé sur l’environnement, utilisez la propriété `LightingEnvironment` pour affecter un éclairage basé sur une image à la scène dans son ensemble.
- Utilisez la propriété `IESProfileURL` pour importer des luminaires légers qui définissent la base d’éclairage sur des valeurs réelles telles que l’intensité (en lumières) et la température de couleur (en degrés Kelvin).
- La classe [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera) peut fournir un réalisme plus réaliste en utilisant les fonctionnalités HDR et les effets. Utilisez l’exposition adaptative pour créer des effets automatiques ou utiliser l’vignette, la frange des couleurs et le classement des couleurs pour ajouter des effets FILMATIC au jeu.
- Les fonctionnalités des caméras PBR et HDR offrent de meilleurs résultats que les techniques de rendu traditionnelles et, par conséquent, SceneKit effectue désormais tous les calculs de couleur dans un espace colorimétrique linéaire (à l’aide de la palette de couleurs P3 sur les écrans d’appareils larges couleurs).
- La couleur SceneKit Now correspond à toutes les couleurs en lisant les informations du profil de couleurs.
- SceneKit interprète les valeurs de composant de couleur dans un espace de couleurs RVB linéaire pour tous les types de nuanceur.
- Étant donné que SceneKit lit et ajuste les informations de profil colorimétrique dans les images de texture, utilisez des catalogues de ressources pour toutes les images pour vous assurer que ces informations sont fournies.
- Le rendu de l’espace de couleurs linéaire et la grande couleur peuvent être désactivés en spécifiant les clés `SCNDisableLinearSpaceRendering` et `SCNDisableWideGamut` dans le `Info.plist`de l’application.
- Générez des primates de polygones arbitraires (chargés à partir de fichiers ou générés par programme) pour spécifier Geometry avec la nouvelle classe [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon) .

<a name="SpriteKit-Enhancements" />

## <a name="spritekit-enhancements"></a>Améliorations apportées à SpriteKit

Les améliorations suivantes ont été apportées à l’infrastructure SpriteKit dans tvOS 10 :

- Tilemaps prennent désormais en charge les formes de mosaïque carrée, hexagonale et isométrique pour les jeux 2D, 2,5 D et défilement latéral à l’aide des classes `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` et `SKTileSet`.
- Utilisez la nouvelle classe `SKWarpGeometry` pour étirer ou déformer le rendu [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) ou [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) . La nouvelle classe [SKAction](https://developer.apple.com/reference/spritekit/skaction) peut être utilisée pour animer des transitions entre des effets de déformation.
- Les nuanceurs personnalisés peuvent fournir des attributs (`SKAttribute`) qui peuvent être configurés séparément par chaque nœud qui utilise le nuanceur en fournissant une valeur d’attribut (`SKAttributeValue`).
- La classe [SKView](https://developer.apple.com/reference/spritekit/skview) fournit plusieurs nouvelles méthodes pour fournir un contrôle précis sur le moment et le mode de rendu d’une scène.

<a name="UIKit-Enhancements" />

## <a name="uikit-enhancements"></a>Améliorations apportées à UIKit

Les améliorations suivantes ont été apportées à l’infrastructure UIKit dans tvOS 10 :

- L’API focus a été améliorée pour prendre en charge le focus d’un élément sans vue en plus de `UIViews`. Les éléments qui prennent en charge le focus _doivent_ implémenter l’interface `IUIFocusItem`.
- La nouvelle classe `UIGraphicsRender` fournit une méthode orientée objet qui permet de créer des images bitmap ou des fichiers PDF à partir du rendu UIKit ou des graphiques principaux, et remplace la méthode `UIGraphicsBeginImageContext` déconseillée.
- La classe `UIUserInterfaceStyle` a été ajoutée pour déterminer le thème de l’interface utilisateur (sombre ou clair) qui est actuellement actif.
- La nouvelle prise en charge de l’animation interruptible, entièrement interactive et basée sur les objets a été ajoutée et Van est liée aux gestes. Agréable consultez Référence du [protocole UIViewAnimating](https://developer.apple.com/reference/uikit/uiviewanimating)d’Apple, référence de la [classe UIViewPropertyAnimator](https://developer.apple.com/reference/uikit/uiviewpropertyanimator), référence du [protocole UITimingCurveProvider](https://developer.apple.com/reference/uikit/uitimingcurveprovider), référence de la [classe UICubicTimingParameters](https://developer.apple.com/reference/uikit/uicubictimingparameters) et [ Référence de la classe UISpringTimingParameter](https://developer.apple.com/reference/uikit/uispringtimingparameters) pour plus d’informations.
- Les nouvelles `UIPreviewInteraction` et `UIPreviewInteractionDelegate` permettent à l’application de fournir une interface personnalisée pour les opérations de lecture et d’affichage.
- La nouvelle classe `UIAccessibilityCustomRotor` permet à l’application de fournir des fonctionnalités personnalisées, spécifiques au contexte, à des technologies d’assistance telles que la voix.
- Utilisez les symboles `UIAccessibilityIsAssistiveTouchRunning` et `UIAccessibilityAssistiveTouchStatusDidChangeNotification` pour déterminer si l’assistance tactile est activé.
- Utilisez les symboles `UIAccessibilityHearingDevicePairedEar` et `UIAccessibilityHearingDevicePairedEarDidChangeNotification` pour connaître l’état de toutes les aides auditives appariées par les IFM.
- La nouvelle API [UIPasteboard](https://developer.apple.com/reference/uikit/uipasteboard) fournit de nouvelles options (telles que les limitations de durée de vie) et déclare automatiquement les types de contenu compatibles pour les types de classe courants.
- Pour prendre en charge le type dynamique dans les étiquettes, les champs de texte et les zones de texte, utilisez la nouvelle méthode `PreferredFontForTextStyle` de la classe `UIFont`.
- Pour déterminer si un élément doit le mettre à jour lorsque les périphériques `UIContentSizeCategory` changent, utilisez la propriété `AdjustsFontForContentSizeCategory` du délégué `UIContentSizeCategoryAdjusting`.
- L’application peut maintenant contrôler l’apparence du badge pour les éléments de la barre d’onglets tels que le texte et la couleur d’arrière-plan.
- Le contrôle d’actualisation dans est désormais pris en charge dans toutes les sous-classes d’affichage de défilement et d’affichage de défilement (comme `UICollectionView`).
- La méthode `OpenURL` de la classe `UIApplication` est appelée de manière asynchrone prend désormais en charge un gestionnaire d’achèvement qui est appelé une fois l’ouverture terminée.
- Initiez le partage CloudKit et modifiez ses propriétés à l’aide des nouvelles classes `UICloudSharingController` et `UICloudSharingControllerDelegate`.
- Tirez parti des cellules préextraites pour améliorer l’expérience de défilement des `UICollectionViews` avec le nouveau délégué `UICollectionViewDataSourcePrefetching`.

## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [Nouveautés de tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
