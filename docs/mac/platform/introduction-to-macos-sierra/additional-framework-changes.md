---
title: Autres modifications du framework macOS Sierra
description: Ce document décrit les modifications mineures et les améliorations apportées aux infrastructures existantes introduites dans macOS Sierra. Il examine les modifications apportées à l’infrastructure Accelerat, AppKit, AVFoundation, Core Data, Core image, Foundation et bien plus encore.
ms.prod: xamarin
ms.assetid: CA701269-D11E-4DE3-89C1-58EF8993A482
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 44468d3f2d323065161c290f2df8e6f0e89d3def
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291831"
---
# <a name="additional-macos-sierra-framework-changes"></a>Autres modifications du framework macOS Sierra

<a name="Accelerate-Framework-Enhancements" />

## <a name="accelerate-framework-enhancements"></a>Améliorations de l’infrastructure accélérée

Les améliorations suivantes ont été apportées à l’infrastructure Accelerate pour macOS Sierra :

- Ajout de quadrature (calcul intégral).
- Ajout de fonctions de base pour la construction de réseaux neuronaux.
- Ajout de fonctions de prédicat géométrique pour tester des choses telles que l’intersection de deux objets géométriques.

<a name="AppKit-Framework-Enhancements" />

## <a name="appkit-framework-enhancements"></a>Améliorations apportées à AppKit Framework

Les améliorations suivantes ont été apportées à l’infrastructure AppKit pour macOS Sierra :

- Plusieurs améliorations apportées à `NSCollectionView` telles que :
  - **Sections réductibles** : permet à l’utilisateur de réduire une section de vue de collection en une seule ligne horizontale.
  - **En-têtes flottants** -les en-têtes et les pieds de page peuvent désormais être flottants (dans une mise en page fluide) à l’aide de la même API que [UICollectionView](https://developer.apple.com/reference/uikit/uicollectionview) dans iOS.
  - **Vues d’arrière-plan avec défilement** : un arrière-plan de vues de collection peut désormais être défini pour faire défiler le contenu.
- La passe de disposition de la vue différée a été optimisée et étendue.
- L’API glisser-déplacer comprend désormais les nouvelles classes de `NSFilePromiseProvider` et de `NSFilePromiseReceiver` pour prendre en charge le troupeau de glissement.
- Plusieurs constructeurs pratiques ont été ajoutés aux contrôles existants :
  - `NSButton` comprend de nouveaux constructeurs pour créer des boutons de commande, des cases à cocher et des cases d’option.
  - `NSTextField` comprend de nouveaux constructeurs permettant de créer des étiquettes d’habillage et sans habillage, des étiquettes avec attributs et des champs de texte modifiables.
  - `NSSegmentedControl` comprend de nouveaux constructeurs pour la création de contrôles segmentés à partir d’un groupe d’étiquettes ou d’images.
  - `NSSlider` comprend de nouveaux constructeurs pour créer des curseurs linéaires horizontaux.
  - `NSImageView` comprend de nouveaux constructeurs permettant de créer des vues d’images non modifiables à partir d’une `NSImage`donnée.
- Le nouveau `NSGridView` a été ajouté pour mettre automatiquement en forme une collection de sous-vues dans une grille avec des lignes et des colonnes de taille variable qui peuvent être masquées ou affichées dynamiquement.

<a name="AVFoundation-Framework-Enhancements" />

## <a name="avfoundation-framework-enhancements"></a>Améliorations apportées à AVFoundation Framework

Les améliorations suivantes ont été apportées à l’infrastructure AVFoundation pour macOS Sierra :

- Dans macOS, l’application n’a plus à implémenter des comportements [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) différents en fonction du type de contenu. Définissez simplement la propriété `Rate` et AVFoundation déterminera quand un contenu suffisant sera disponible pour la lecture sans blocage.
- La nouvelle classe `AVPlayerLooper` facilite la boucle d’un élément multimédia donné pendant la lecture.
- La classe `AVAssetDownloadURLSession` permet le téléchargement et la lecture ultérieure de flux TLS chiffrés FairPlay.

<a name="Core-Data-Framework-Enhancements" />

## <a name="core-data-framework-enhancements"></a>Améliorations de l’infrastructure de données principale

Les améliorations suivantes ont été apportées à l’infrastructure de données de base pour macOS Sierra :

- Les objets [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) racines prennent en charge l’erreur et l’extraction simultanés sans sérialisation.
- La classe [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) gère un pool de banques de données SQLite.
- Les objets [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) avec les banques de données sqlite dans le mode journal de Wal prennent en charge la nouvelle fonctionnalité de génération de requêtes dans laquelle les contextes d’objets managés (MOC) peuvent être épinglés à des versions de base de données spécifiques pour des transactions de récupération et de défaillance futures.
- Utilisation du `NSPersistenceContainer` de haut niveau pour référencer les ressources de configuration de `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) et autres données de base.
- Plusieurs nouvelles méthodes pratiques ont été ajoutées à `NSManagedObject` ce qui facilite l’exécution d’extractions et la création de sous-classes.

Pour plus d’informations, consultez Référence de l' [infrastructure de données de base](https://developer.apple.com/reference/coredata)d’Apple.

<a name="Core-Image-Framework-Enhancements" />

## <a name="core-image-framework-enhancements"></a>Améliorations de l’infrastructure d’images principales

Les améliorations suivantes ont été apportées à l’infrastructure d’images principales pour macOS Sierra :

- La méthode `ImageWithExtent` de la classe [CIFilter](https://developer.apple.com/reference/coreimage/cifilter) peut être utilisée pour insérer un traitement personnalisé dans l’opération de filtrage. L’image principale appellera le rappel donné entre les filtres lors du traitement d’une image pour la sortie ou l’affichage.
- L’application peut maintenant traiter les images dans un espace de couleurs en dehors de l’espace de couleurs de travail du contexte d’image principal en convertissant dans et en dehors de l’espace de couleurs avant et après le traitement.
- Le noyau de l’image principale peut désormais demander un format de sortie de pixel spécifique.
- Les nouveaux filtres d’image suivants ont été ajoutés : `CINinePartTitled`, `CINinePartStretched`, `CIHueSaturationValueGradient`, `CIEdgePreserveUpsampleFilter` et `CIClamp`.

<a name="Foundation-Framework-Enhancements" />

## <a name="foundation-framework-enhancements"></a>Améliorations de Foundation Framework

Les améliorations suivantes ont été apportées à l’infrastructure de base pour macOS Sierra :

- Utilisez l’API [NSDimentions](https://developer.apple.com/reference/foundation/nsdimension) pour représenter, convertir et afficher la plupart des unités physiques les plus courantes, telles que la masse, la longueur, la vitesse, la durée et la température.
- Utilisez la classe [NSISO8601DateFormatter](https://developer.apple.com/reference/foundation/nsiso8601dateformatter) pour l’analyse et la génération de dates au format ISO 8601.
- Utilisez la nouvelle classe [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) pour effectuer des calculs d’intervalles de date et d’heure tels que des durées, pour comparer des intervalles et tester des intersections d’intervalle.
- Utilisez la classe [NSPersonNameComponentsFormatter](https://developer.apple.com/reference/foundation/nspersonnamecomponentsformatter) pour analyser les éléments du nom d’une personne à partir d’une chaîne.
- Utilisez la nouvelle classe [NSURLSessionTaskMetrics](https://developer.apple.com/reference/foundation/nsurlsessiontaskmetrics) pour obtenir les métriques d’une session de mise en réseau d’URL.

Pour plus d’informations, consultez les notes de publication d’Apple [pour OS X v 10.12 et iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/RN-Foundation-OSX10.12/index.html).

<a name="GameKit-Framework-Enhancements" />

## <a name="gamekit-framework-enhancements"></a>Améliorations apportées à GameKit Framework

Les améliorations suivantes ont été apportées à l’infrastructure GameKit pour macOS Sierra :

- L' **application Game Center** a été dépréciée et supprimée de MacOS. Si l’application utilise GameKit, elle _doit_ présenter sa propre interface pour afficher les fonctionnalités GameKit telles que Leaderboards, etc.
- Un nouveau type de compte iCloud est implémenté par la classe [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) .
- La nouvelle classe [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) fournit une solution généralisée pour la gestion du stockage de données persistant sur Game Center. `GKGameSession` gère une liste de joueurs et l’application est responsable de la mise en œuvre de la manière et du moment où la date du participant est stockée, récupérée ou échangée entre les joueurs. Dans de nombreux cas, les sessions de jeu peuvent remplacer les correspondances basées sur les activations existantes, les correspondances en temps réel ou les méthodes d’enregistrement de jeu persistantes.

<a name="GamePlayKit-Framework-Enhancements" />

## <a name="gameplaykit-framework-enhancements"></a>Améliorations apportées à GamePlayKit Framework

Les améliorations suivantes ont été apportées à l’infrastructure GamePlayKit pour macOS Sierra :

- La génération de bruit procédurale a été ajoutée et peut être utilisée pour améliorer le réalisme des textures à l’aspect naturel, ajouter un réalisme aux mouvements de l’appareil photo et aider à générer des mondes riches en jeux.
- Utilisez le partitionnement spatial pour partitionner les données du monde du jeu pour une recherche efficace.
- Un nouveau stratège de Monte-Carlo ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) a été ajouté pour un calcul de déplacement exhaustif possible.
- Une nouvelle API d’arbre de décision a été ajoutée ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) et [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) pour améliorer la création de jeux.
- la prise en charge 3D a été ajoutée aux comportements de l’agent et du chemin d’accès existants à l’aide des nouvelles classes [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) et [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) .
- Utilisez la nouvelle classe [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) pour fournir des chemins d’accès à hautes performances et à l’aspect naturel.
- Les nouvelles classes [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) et [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) rendent la combinaison de GameplayKit et SpriteKit plus facile que jamais.

<a name="Metal-Framework-Enhancements" />

## <a name="metal-framework-enhancements"></a>Améliorations apportées à Framework métallique

Les améliorations suivantes ont été apportées à l’infrastructure métallique pour macOS Sierra :

- les applications et les jeux 3D peuvent désormais utiliser la _facettisation_ pour restituer efficacement des scènes et une géométrie complexes via le GPU.
- Utilisez la spécialisation de fonction pour créer une collection hautement optimisée de fonctions de combinaison de matériau et de lumière pour une scène.
- Fournir un contrôle affiné de l’allocation des ressources pour optimiser les performances des applications basées sur le métal à l’aide de segments de ressources et de cibles de rendu sans mémoire.

Pour plus d’informations, consultez le [Guide de programmation métallique](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)d’Apple.

<a name="ModelIO-Framework-Enhancements" />

## <a name="model-io-framework-enhancements"></a>Améliorations de l’infrastructure d’e/s de modèle

Les améliorations suivantes ont été apportées à l’infrastructure d’e/s de modèle pour macOS Sierra :

- Le format de fichier USD est désormais pris en charge.
- Utilisez la nouvelle classe `MDLMaterialPropertyGraph` pour prendre en charge facilement les modifications du runtime pour les modèles.
- La prise en charge des champs à distance signée a été ajoutée à la classe [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) .
- Utilisez la nouvelle classe `MDLLightProbeIrradianceDataSource` pour faciliter le positionnement de la sonde légère.

<a name="Photos-Framework-Enhancements" />

## <a name="photos-framework-enhancements"></a>Améliorations de l’infrastructure des photos

Les améliorations suivantes ont été apportées à l’infrastructure des photos pour macOS Sierra :

- L’édition de photos en direct est désormais disponible pour les applications qui prennent en charge l’infrastructure de photos et les extensions de modification de photos (pour une utilisation dans les photos et les applications d’appareil photo).
- Utilisez la nouvelle classe [PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) pour appliquer les modifications à la vidéo et au contenu toujours des photos en direct.
- Utilisez les classes [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) et [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) pour tirer parti de la nouvelle fonctionnalité du processeur d’images principal afin d’effectuer des modifications.
- Pour prendre en charge les photos en direct, les classes [PHLivePhoto](https://developer.apple.com/reference/photos/phlivephoto) et [PHLivePhotoView](https://developer.apple.com/reference/photosui/phlivephotoview) ont été portées d’iOS vers MacOS.

<a name="SceneKit-Framework-Enhancements" />

## <a name="scenekit-framework-enhancements"></a>Améliorations apportées à SceneKit Framework

Les améliorations suivantes ont été apportées à l’infrastructure SceneKit pour macOS Sierra :

- Comprend maintenant un nouveau système de rendu physique (PBR) pour des résultats plus réalistes grâce à la création de ressources plus simples.
- Utilisez le nouveau modèle [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) Shading pour produit un large éventail d’effets d’ombrage réalistes tout en exigeant uniquement trois propriétés fondamentales (`Diffuse`, `Metalness` et `Roughness`).
- Étant donné que l’ombrage PBR fonctionne mieux avec l’éclairage basé sur l’environnement, utilisez la propriété `LightingEnvironment` pour affecter un éclairage basé sur une image à la scène dans son ensemble.
- Utilisez la propriété `IESProfileURL` pour importer des luminaires légers qui définissent la base d’éclairage sur des valeurs réelles telles que l’intensité (en lumières) et la température de couleur (en degrés Kelvin).
- La classe [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera) peut fournir un réalisme plus réaliste en utilisant les fonctionnalités HDR et les effets. Utilisez l’exposition adaptative pour créer des effets automatiques ou utiliser l’vignette, la frange des couleurs et le classement des couleurs pour ajouter des effets FILMATIC au jeu.
- Les fonctionnalités des caméras PBR et HDR offrent de meilleurs résultats que les techniques de rendu traditionnelles et, par conséquent, SceneKit effectue désormais tous les calculs de couleur dans un espace colorimétrique linéaire (à l’aide de la palette de couleurs P3 sur les écrans d’appareils larges couleurs).
- La couleur SceneKit Now correspond à toutes les couleurs en lisant les informations du profil de couleurs.
- SceneKit interprète les valeurs de composant de couleur dans un espace de couleurs RVB linéaire pour tous les types de nuanceur.
- Étant donné que SceneKit lit et ajuste les informations de profil colorimétrique dans les images de texture, utilisez des catalogues de ressources pour toutes les images pour vous assurer que ces informations sont fournies.
- Le rendu de l’espace de couleurs linéaire et la grande couleur peuvent être désactivés en spécifiant les clés `SCNDisableLinearSpaceRendering` et `SCNDisableWideGamut` dans le `Info.plist`de l’application.
- Générez des primates de polygones arbitraires (chargés à partir de fichiers ou générés par programme) pour spécifier Geometry avec la nouvelle classe [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/documentation/scenekit/scngeometryprimitivetype/scngeometryprimitivetypepolygon?language=objc) .

<a name="Security-Framework-Enhancements" />

## <a name="security-framework-enhancements"></a>Améliorations de l’infrastructure de sécurité

Les améliorations suivantes ont été apportées à l’infrastructure de sécurité pour macOS Sierra :

- L’interface `SecKey` a été moderne et unifiée sur toutes les plateformes (iOS, tvOS, Watchos et macOS).

<a name="SpriteKit-Framework-Enhancements" />

## <a name="spritekit-framework-enhancements"></a>Améliorations apportées à SpriteKit Framework

Les améliorations suivantes ont été apportées à l’infrastructure SpriteKit pour macOS Sierra :

- Tilemaps prennent désormais en charge les formes de mosaïque carrée, hexagonale et isométrique pour les jeux 2D, 2,5 D et défilement latéral à l’aide des classes `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` et `SKTileSet`.
- Utilisez la nouvelle classe `SKWarpGeometry` pour étirer ou déformer le rendu [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) ou [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) . La nouvelle classe [SKAction](https://developer.apple.com/reference/spritekit/skaction) peut être utilisée pour animer des transitions entre des effets de déformation.
- Les nuanceurs personnalisés peuvent fournir des attributs (`SKAttribute`) qui peuvent être configurés séparément par chaque nœud qui utilise le nuanceur en fournissant une valeur d’attribut (`SKAttributeValue`).
- La classe [SKView](https://developer.apple.com/reference/spritekit/skview) fournit plusieurs nouvelles méthodes pour fournir un contrôle précis sur le moment et le mode de rendu d’une scène.

<a name="New-Frameworks" />

## <a name="new-frameworks"></a>Nouvelles infrastructures

Les infrastructures suivantes ont été ajoutées à macOS Sierra :

- **Framework intentions** : ce Framework permet à l’application d’examiner les interactions (telles que l’emplacement ou les actions de l’utilisateur) et de prendre des mesures en fonction de ces informations.
- **Framework SafariServices** : ce Framework permet à l’application de développer des extensions d’application pour Safari (comme les bloqueurs de contenu) pour MacOS et iOS.

## <a name="related-links"></a>Liens connexes

- [Exemples Mac](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [Nouveautés du système d’exploitation X 10,12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
