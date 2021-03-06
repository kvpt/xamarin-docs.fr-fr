---
title: Storyboards unifiés dans Xamarin. iOS
description: Ce document décrit les storyboards unifiés dans Xamarin. iOS. Les storyboards unifiés permettent aux développeurs de prendre en charge plusieurs tailles d’écran avec une seule définition d’interface.
ms.prod: xamarin
ms.assetid: F6F70374-FC2A-4401-A712-A16D0F9B340F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: fdfdd385563ee425f0e8767aed13304ae9541b94
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435355"
---
# <a name="unified-storyboards-in-xamarinios"></a>Storyboards unifiés dans Xamarin. iOS

iOS 8 comprend un nouveau mécanisme simple d’utilisation pour créer l’interface utilisateur, le Storyboard unifié. Avec une seule table de montage séquentiel pour couvrir toutes les différentes tailles d’écran matérielles, des vues rapides et réactives peuvent être créées dans un style « design-once, use-many ».

Étant donné que le développeur n’a plus besoin de créer un Storyboard distinct et spécifique pour les appareils iPhone et iPad, il a la possibilité de concevoir des applications avec une interface commune, puis de personnaliser cette interface pour différentes classes de taille. De cette façon, une application peut être adaptée aux points forts de chaque facteur de forme et chaque interface utilisateur peut être paramétrée pour offrir la meilleure expérience.

<a name="size-classes"></a>

## <a name="size-classes"></a>Classes de taille

Avant iOS 8, le développeur a utilisé `UIInterfaceOrientation` et `UIInterfaceIdiom` pour faire la distinction entre les modes portrait et paysage, et entre les appareils iPhone et iPad. Dans iOS8, l’orientation et l’appareil sont déterminés à l’aide des *classes de taille*.

Les appareils sont définis par des classes de taille, à la fois dans les axes vertical et horizontal, et il existe deux types de classes de taille dans iOS 8 :

- **Normal** : soit pour une grande taille d’écran (par exemple, un iPad), soit pour un gadget qui donne l’impression d’une grande taille (par exemple, un `UIScrollView`
- **Compact** : il s’agit des appareils plus petits (tels qu’un iPhone). Cette taille prend en compte l’orientation de l’appareil.

Si les deux concepts sont utilisés ensemble, le résultat est une grille 2 x 2 qui définit les différentes tailles possibles qui peuvent être utilisées dans les deux orientations différentes, comme illustré dans le diagramme suivant :

 [![Grille 2 x 2 qui définit les différentes tailles possibles qui peuvent être utilisées dans les orientations standard et compactes](unified-storyboards-images/sizeclassgrid.png)](unified-storyboards-images/sizeclassgrid.png#lightbox)

Le développeur peut créer un contrôleur d’affichage qui utilise l’une des quatre possibilités qui aboutirait à des dispositions différentes (comme indiqué dans les graphiques ci-dessus).

### <a name="ipad-size-classes"></a>Classes de taille iPad

L’iPad, en raison de la taille, a une taille de classe **normale** pour les deux orientations.

 [![Classes de taille iPad](unified-storyboards-images/image1.png)](unified-storyboards-images/image1.png#lightbox)

### <a name="iphone-size-classes"></a>Classes de taille iPhone

L’iPhone a des classes de taille différentes en fonction de l’orientation de l’appareil :

 [![Classes de taille iPhone](unified-storyboards-images/iphonesizeclasses.png)](unified-storyboards-images/iphonesizeclasses.png#lightbox)

- Lorsque l’appareil est en mode portrait, l’écran a une classe **compacte** horizontalement **et** verticalement
- Lorsque l’appareil est en mode paysage, les classes d’écran sont inversées en mode portrait.

### <a name="iphone-6-plus-size-classes"></a>Classes de taille de iPhone 6 plus

Les tailles sont les mêmes que les iPhone antérieurs lorsqu’elles sont en orientation portrait, mais différentes en mode paysage :

[![Classes de taille de iPhone 6 plus](unified-storyboards-images/iphone6sizeclasses.png)](unified-storyboards-images/iphone6sizeclasses.png#lightbox)

Étant donné que l’écran iPhone 6 plus est suffisamment grand, il peut avoir une classe de taille de largeur normale en mode paysage.

### <a name="support-for-a-new-screen-scale"></a>Prise en charge d’une nouvelle échelle d’écran

L’iPhone 6 plus utilise un nouvel affichage HD à l’écran avec un facteur d’échelle d’écran de 3,0 (trois fois la résolution d’écran de l’iPhone d’origine). Pour offrir la meilleure expérience possible sur ces appareils, incluez de nouvelles illustrations conçues pour cette échelle à l’écran. Dans Xcode 6 et versions ultérieures, les catalogues de ressources peuvent inclure des images à des tailles 1x, 2x et 3x. Ajoutez simplement les nouvelles ressources d’image et iOS choisira les ressources appropriées lors de l’exécution sur un iPhone 6 plus.

Le comportement de chargement d’image dans iOS reconnaît également un `@3x` suffixe sur les fichiers image. Par exemple, si le développeur comprend une ressource d’image (à des résolutions différentes) dans le bundle de l’application avec les noms de fichiers suivants : `MonkeyIcon.png` , `MonkeyIcon@2x.png` et `MonkeyIcon@3x.png` . Sur l’iPhone 6 plus l' `MonkeyIcon@3x.png` image sera utilisée automatiquement si le développeur charge une image à l’aide du code suivant :

```csharp
UIImage icon = UIImage.FromFile("MonkeyImage.png");
```

Ou, s’ils affectent l’image à un élément d’interface utilisateur à l’aide du concepteur iOS en tant que `MonkeyIcon.png` , le `MonkeyIcon@3x.png` sera utilisé, de nouveau automatiquement, sur l’iPhone 6 plus.

<a name="dynamic-launch-screens"></a>

### <a name="dynamic-launch-screens"></a>Écrans de lancement dynamique

Le fichier d’écran de lancement s’affiche sous la forme d’un écran de démarrage lorsqu’une application iOS est lancée pour fournir des commentaires à l’utilisateur que l’application est en train de démarrer. Avant iOS 8, le développeur devait inclure plusieurs `Default.png` ressources d’image pour chaque type d’appareil, l’orientation et la résolution d’écran sur lesquels l’application s’exécutait.

Nouveauté d’iOS 8, le développeur peut créer un seul fichier atomique `.xib` dans Xcode qui utilise des classes de mise en page et de taille automatiques pour créer un *écran de lancement dynamique* qui fonctionnera pour chaque appareil, résolution et orientation. Cela réduit non seulement la quantité de travail requise du développeur pour créer et gérer toutes les ressources d’image requises, mais elle réduit la taille de l’offre groupée installée de l’application.

## <a name="traits"></a>Caractéristiques

Les caractéristiques sont des propriétés qui peuvent être utilisées pour déterminer la façon dont une disposition change en fonction de l’évolution de son environnement. Ils se composent d’un ensemble de propriétés ( `HorizontalSizeClass` et `VerticalSizeClass` basées sur `UIUserInterfaceSizeClass` ), ainsi que de l’interface idiome ( `UIUserInterfaceIdiom` ) et de l’échelle d’affichage.

Tous les États ci-dessus sont encapsulés dans un conteneur auquel Apple fait référence en tant que collection de caractéristiques ( `UITraitCollection` ), qui contient non seulement les propriétés mais également leurs valeurs.

## <a name="trait-environment"></a>Environnement de trait

Les environnements de caractéristiques sont une nouvelle interface dans iOS 8 et peuvent retourner une collection de traits pour les objets suivants :

- Écrans ( `UIScreens` ).
- Windows ( `UIWindows` ).
- Contrôleurs d’affichage ( `UIViewController` ).
- Views ( `UIView` ).
- Contrôleur de présentation ( `UIPresentationController` ).

Le développeur utilise la collection de traits retournée par un environnement de trait pour déterminer la façon dont une interface utilisateur doit être présentée.

Tous les environnements de caractéristiques forment une hiérarchie comme indiqué dans le diagramme suivant :

 [![Diagramme de la hiérarchie des environnements de caractéristiques](unified-storyboards-images/viewhierarchy.png)](unified-storyboards-images/viewhierarchy.png#lightbox)

La collection de traits que chacun des environnements de caractéristiques ci-dessus est en passe, par défaut, du parent à l’environnement enfant.

Outre l’obtention de la collection de caractéristiques actuelle, l’environnement de trait a une `TraitCollectionDidChange` méthode, qui peut être substituée dans les sous-classes d’affichage ou de contrôleur d’affichage. Le développeur peut utiliser cette méthode pour modifier tous les éléments d’interface utilisateur qui dépendent des traits lorsque ces traits ont changé.

## <a name="typical-trait-collections"></a>Collections de caractéristiques typiques

Cette section couvre les types typiques de collections de traits que l’utilisateur rencontrera lorsqu’il travaillera avec iOS 8.

Voici une collection de caractéristiques typique que le développeur peut voir sur un iPhone :

|Propriété|Valeur|
|--- |--- |
|`HorizontalSizeClass`|Compact|
|`VerticalSizeClass`|Normal|
|`UserInterfaceIdom`|Téléphone|
|`DisplayScale`|2.0|

L’ensemble ci-dessus représente une collection de traits complète, car elle contient des valeurs pour toutes ses propriétés de trait.

Il est également possible de disposer d’une collection de caractéristiques qui ne contient pas certaines de ses valeurs (dont Apple fait référence à une valeur *non spécifiée*) :

|Propriété|Valeur|
|--- |--- |
|`HorizontalSizeClass`|Compact|
|`VerticalSizeClass`|Non spécifié|
|`UserInterfaceIdom`|Non spécifié|
|`DisplayScale`|Non spécifié|

En règle générale, toutefois, lorsque le développeur demande l’environnement de trait pour sa collection de traits, il retourne une collection complète, comme illustré dans l’exemple ci-dessus.

Si un environnement de trait (comme un affichage ou un contrôleur d’affichage) n’est pas à l’intérieur de la hiérarchie d’affichage actuelle, le développeur peut récupérer des valeurs non spécifiées pour une ou plusieurs des propriétés de trait.

Le développeur obtient également une collection de traits partiellement qualifiée s’ils utilisent l’une des méthodes de création fournies par Apple, telles que `UITraitCollection.FromHorizontalSizeClass` , pour créer une collection.

Une opération qui peut être effectuée sur plusieurs collections de caractéristiques consiste à les comparer les unes aux autres, ce qui implique de demander une collection de caractéristiques si elle en contient une autre. Ce qui est prévu par la *relation contenant-contenu* est que, pour toute caractéristique spécifiée dans la deuxième collection, la valeur doit correspondre exactement à la valeur de la première collection.

Pour tester deux caractéristiques, utilisez la `Contains` méthode du passage de la `UITraitCollection` valeur de la caractéristique à tester.

Le développeur peut effectuer manuellement les comparaisons dans le code pour déterminer comment mettre en page les vues ou les contrôleurs d’affichage. Toutefois, `UIKit` utilise cette méthode en interne pour fournir certaines de ses fonctionnalités, comme dans le proxy d’apparence, par exemple.

## <a name="appearance-proxy"></a>Apparence proxy

Le proxy d’apparence a été introduit dans les versions antérieures d’iOS pour permettre aux développeurs de personnaliser les propriétés de leurs vues. Il a été étendu dans iOS 8 pour prendre en charge les collections de caractéristiques.

Les proxys d’apparence incluent désormais une nouvelle méthode, `AppearanceForTraitCollection` , qui retourne un nouveau proxy d’apparence pour la collection de traits donnée qui a été passée. Toutes les personnalisations effectuées par le développeur sur ce proxy d’apparence prennent effet uniquement sur les vues qui se conforment à la collection de traits spécifiée.

En général, le développeur transmet une collection de traits partiellement spécifiée à la `AppearanceForTraitCollection` méthode, telle que celle qui vient de spécifier une classe de taille horizontale, afin qu’elle puisse personnaliser toute vue de l’application qui est compacte horizontalement.

## <a name="uiimage"></a>UIImage

Une autre classe à laquelle Apple a ajouté la collection de caractéristiques est `UIImage` . Dans le passé, le développeur devait spécifier une @1X @2x version et de n’importe quelle ressource graphique bitmap qu’il allait inclure dans l’application (par exemple, une icône).

iOS 8 a été développé pour permettre au développeur d’inclure plusieurs versions d’une image dans un catalogue d’images basé sur une collection de traits. Par exemple, le développeur peut inclure une image plus petite pour travailler avec une classe de caractéristiques compacte et une image de taille complète pour n’importe quelle autre collection.

Quand l’une des images est utilisée à l’intérieur d’une `UIImageView` classe, la vue d’image affiche automatiquement la version correcte de l’image pour sa collection de caractéristiques. Si l’environnement de trait change (par exemple lorsque l’utilisateur bascule l’appareil du portrait au mode paysage), la vue image sélectionne automatiquement la nouvelle taille d’image pour correspondre à la nouvelle collection de caractéristiques et modifie sa taille pour qu’elle corresponde à celle de la version actuelle de l’image affichée.

## <a name="uiimageasset"></a>UIImageAsset

Apple a ajouté une nouvelle classe à iOS 8 `UIImageAsset` , appelée pour offrir au développeur encore plus de contrôle sur la sélection d’images.

Une ressource d’image encapsule toutes les différentes versions d’une image et permet au développeur de demander une image spécifique qui correspond à une collection de traits qui a été transmise. Les images peuvent être ajoutées ou supprimées d’une ressource d’image à la volée.

Pour plus d’informations sur les ressources d’image, consultez la documentation [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset) d’Apple.

## <a name="combining-trait-collections"></a>Combinaison de collections de traits

Une autre fonction qu’un développeur peut effectuer sur les collections de traits consiste à en ajouter deux ensemble qui entraînent la collection combinée, où les valeurs non spécifiées d’une collection sont remplacées par les valeurs spécifiées dans une seconde. Pour ce faire, utilisez la `FromTraitsFromCollections` méthode de la `UITraitCollection` classe.

Comme indiqué ci-dessus, si l’une des caractéristiques n’est pas spécifiée dans l’une des collections de traits et est spécifiée dans une autre, la valeur sera définie sur la version spécifiée. Toutefois, si plusieurs versions d’une valeur donnée sont spécifiées, la valeur de la dernière collection de traits correspond à la valeur utilisée.

## <a name="adaptive-view-controllers"></a>Contrôleurs d’affichage adaptatif

Cette section explique en détail comment la vue iOS et les contrôleurs d’affichage ont adopté les concepts des caractéristiques et des classes de taille pour être automatiquement plus adaptables dans les applications du développeur.

### <a name="split-view-controller"></a>Contrôleur d’affichage fractionné

L’une des classes de contrôleur d’affichage qui a changé le plus dans iOS 8 est la `UISplitViewController` classe. Dans le passé, le développeur utiliserait souvent un contrôleur de vue fractionnée sur la version iPad de l’application, puis il devait fournir une version totalement différente de sa hiérarchie d’affichage pour la version iPhone de l’application.

Dans iOS 8, la `UISplitViewController` classe est disponible sur les deux plateformes (iPad et iPhone), ce qui permet au développeur de créer une hiérarchie de contrôleur d’affichage qui fonctionnera à la fois pour iPhone et iPad.

Quand un iPhone est en mode paysage, le contrôleur du mode fractionné présente ses affichages côte à côte, exactement comme s’il était affiché sur un iPad.

### <a name="overriding-traits"></a>Substituer des caractéristiques

Les environnements de caractéristiques sont en cascade à partir du conteneur parent jusqu’aux conteneurs enfants, comme dans le graphique suivant présentant un contrôleur d’affichage fractionné sur un iPad dans l’orientation paysage :

 [![Contrôleur de mode fractionné sur un iPad dans l’orientation paysage](unified-storyboards-images/cascadingclasses01.png)](unified-storyboards-images/cascadingclasses01.png#lightbox)

Étant donné que l’iPad a une classe de taille normale dans les orientations horizontale et verticale, le mode fractionné affiche les vues maître et détail.

Sur un iPhone, où la classe Size est compacte dans les deux orientations, le contrôleur de mode fractionné affiche uniquement la vue détaillée, comme indiqué ci-dessous :

 [![Le contrôleur de mode fractionné affiche uniquement la vue détaillée](unified-storyboards-images/cascadingclasses02.png)](unified-storyboards-images/cascadingclasses02.png#lightbox)

Dans une application où le développeur souhaite afficher les vues maître et détail sur un iPhone dans l’orientation paysage, le développeur doit insérer un conteneur parent pour le contrôleur d’affichage fractionné et remplacer la collection de traits. Comme indiqué dans le graphique ci-dessous :

 [![Le développeur doit insérer un conteneur parent pour le contrôleur d’affichage fractionné et remplacer la collection de traits](unified-storyboards-images/cascadingclasses03.png)](unified-storyboards-images/cascadingclasses03.png#lightbox)

Un `UIView` est défini en tant que parent du contrôleur d’affichage fractionné et la `SetOverrideTraitCollection` méthode est appelée sur la vue en passant dans une nouvelle collection de traits et en ciblant le contrôleur d’affichage fractionné. La nouvelle collection de caractéristiques remplace le `HorizontalSizeClass` , en lui affectant la valeur `Regular` , afin que le contrôleur du mode fractionné affiche à la fois les vues maître et détail sur un iPhone dans l’orientation paysage.

Notez que `VerticalSizeClass` a la valeur `unspecified` , ce qui permet d’ajouter la nouvelle collection de traits à la collection de traits sur le parent, ce qui génère un `Compact VerticalSizeClass` pour le contrôleur d’affichage fractionné enfant.

### <a name="trait-changes"></a>Modifications de caractéristiques

Cette section présente en détail le mode de transition des collections de traits lorsque l’environnement de trait change. Par exemple, lorsque l’appareil passe du portrait au paysage.

 [![Vue d’ensemble des modifications de caractéristiques portrait en paysage](unified-storyboards-images/traittransitions01.png)](unified-storyboards-images/traittransitions01.png#lightbox)

En premier lieu, iOS 8 effectue une certaine configuration pour préparer la transition. Ensuite, le système anime l’état de transition. Enfin, iOS 8 nettoie tous les États temporaires requis pendant la transition.

iOS 8 fournit plusieurs rappels que le développeur peut utiliser pour participer à la modification de trait, comme indiqué dans le tableau suivant :

|Phase|Rappel|Description|
|--- |--- |--- |
|Programme d’installation|<ul><li>`WillTransitionToTraitCollection`</li><li>`TraitCollectionDidChange`</li></ul>|<ul><li>Cette méthode est appelée au début d’une modification de trait avant qu’une collection de traits soit définie sur sa nouvelle valeur.</li><li>La méthode est appelée lorsque la valeur de la collection de traits a changé mais avant qu’une animation ait lieu.</li></ul>|
|Animation|`WillTransitionToTraitCollection`|Le coordinateur de transition qui est passé à cette méthode a une `AnimateAlongside` propriété qui permet au développeur d’ajouter des animations qui seront exécutées en même temps que les animations par défaut.|
|Nettoyage|`WillTransitionToTraitCollection`|Fournit une méthode permettant aux développeurs d’inclure leur propre code de nettoyage une fois la transition effectuée.|

La `WillTransitionToTraitCollection` méthode est idéale pour animer des contrôleurs d’affichage avec les modifications apportées à la collection de traits. La `WillTransitionToTraitCollection` méthode est uniquement disponible sur les contrôleurs d’affichage ( `UIViewController` ) et non sur d’autres environnements de caractéristiques, comme `UIViews` .

`TraitCollectionDidChange`Est parfait pour travailler avec la `UIView` classe, où le développeur souhaite mettre à jour l’interface utilisateur au fur et à mesure que les caractéristiques changent.

### <a name="collapsing-the-split-view-controllers"></a>Réduction des contrôleurs d’affichage fractionné

Examinons à présent de plus près ce qui se passe lorsqu’un contrôleur de vue fractionnée réduit d’une colonne à une vue d’une colonne. Dans le cadre de cette modification, il existe deux processus qui doivent se produire :

- Par défaut, le contrôleur du mode fractionné utilise le contrôleur d’affichage principal comme vue une fois la réduction effectuée. Le développeur peut substituer ce comportement en substituant la  `GetPrimaryViewControllerForCollapsingSplitViewController` méthode du  `UISplitViewControllerDelegate` et en fournissant un contrôleur d’affichage qu’il souhaite afficher dans l’État réduit.
- Le contrôleur d’affichage secondaire doit être fusionné dans le contrôleur d’affichage principal. En général, le développeur n’a pas besoin d’entreprendre aucune action pour cette étape. le contrôleur de vue fractionnée prend en charge la gestion automatique de cette phase en fonction du périphérique matériel. Toutefois, il peut y avoir des cas particuliers où le développeur souhaite interagir avec cette modification. L’appel  `CollapseSecondViewController` de la méthode du  `UISplitViewControllerDelegate` permet l’affichage du contrôleur d’affichage principal lorsque la réduction se produit, au lieu du mode Détails.

### <a name="expanding-the-split-view-controller"></a>Développement du contrôleur d’affichage fractionné

Examinons à présent de plus près ce qui se passe lorsqu’un contrôleur d’affichage fractionné est développé à partir d’un État réduit. Là encore, il y a deux étapes qui doivent se produire :

- Tout d’abord, définissez le nouveau contrôleur d’affichage principal. Par défaut, le contrôleur du mode fractionné utilise automatiquement le contrôleur d’affichage principal à partir de l’affichage réduit. Là encore, le développeur peut substituer ce comportement à l’aide  `GetPrimaryViewControllerForExpandingSplitViewController` de la méthode de  `UISplitViewControllerDelegate` .
- Une fois que le contrôleur d’affichage principal a été choisi, le contrôleur d’affichage secondaire doit être recréé. Là encore, le contrôleur de vue fractionnée prend en charge la gestion automatique de cette phase en fonction du périphérique matériel. Le développeur peut substituer ce comportement en appelant la  `SeparateSecondaryViewController` méthode de  `UISplitViewControllerDelegate` .

Dans un contrôleur d’affichage fractionné, le contrôleur d’affichage principal joue un rôle dans le développement et la réduction des vues en implémentant `CollapseSecondViewController` les `SeparateSecondaryViewController` méthodes et du `UISplitViewControllerDelegate` . `UINavigationController` implémente ces méthodes pour pousser et dépiler automatiquement le contrôleur d’affichage secondaire.

### <a name="showing-view-controllers"></a>Affichage des contrôleurs d’affichage

Une autre modification apportée par Apple à iOS 8 est la façon dont le développeur affiche les contrôleurs d’affichage. Dans le passé, si l’application disposait d’un contrôleur d’affichage feuille (tel qu’un contrôleur d’affichage de table) et que le développeur affichait un autre (par exemple, en réponse à l’utilisateur tape sur une cellule), l’application atteindrait la hiérarchie du contrôleur sur le contrôleur d’affichage de navigation et appellera la `PushViewController` méthode pour afficher la nouvelle vue.

Cela présentait un couplage très étroit entre le contrôleur de navigation et l’environnement dans lequel il s’exécutait. Dans iOS 8, Apple l’a découplée en fournissant deux nouvelles méthodes :

- `ShowViewController` – S’adapte pour afficher le nouveau contrôleur d’affichage en fonction de son environnement. Par exemple, dans,  `UINavigationController` il envoie simplement la nouvelle vue sur la pile. Dans un contrôleur d’affichage fractionné, le nouveau contrôleur d’affichage sera présenté sur le côté gauche en tant que nouveau contrôleur d’affichage principal. Si aucun contrôleur d’affichage de conteneur n’est présent, la nouvelle vue sera affichée en tant que contrôleur d’affichage modal.
- `ShowDetailViewController` : Fonctionne de façon similaire à  `ShowViewController` , mais est implémenté sur un contrôleur d’affichage fractionné pour remplacer le mode Détails par le nouveau contrôleur d’affichage passé. Si le contrôleur du mode fractionné est réduit (comme dans le cas d’une application iPhone), l’appel est redirigé vers la  `ShowViewController` méthode et la nouvelle vue est affichée en tant que contrôleur d’affichage principal. Là encore, si aucun contrôleur d’affichage de conteneur n’est présent, la nouvelle vue sera affichée en tant que contrôleur d’affichage modal.

Ces méthodes fonctionnent en démarrant au contrôleur d’affichage feuille et parcourent la hiérarchie d’affichage jusqu’à ce qu’elles trouvent le contrôleur d’affichage de conteneur approprié pour gérer l’affichage de la nouvelle vue.

Les développeurs peuvent implémenter `ShowViewController` et `ShowDetailViewController` dans leurs propres contrôleurs d’affichage personnalisés pour obtenir les mêmes fonctionnalités automatisées que celles `UINavigationController` fournies par `UISplitViewController` .

### <a name="how-it-works"></a>Fonctionnement

Dans cette section, nous allons voir comment ces méthodes sont réellement implémentées dans iOS 8. Examinons tout d’abord la nouvelle `GetTargetForAction` méthode :

 [![Nouvelle méthode GetTargetForAction](unified-storyboards-images/gettargetforaction.png)](unified-storyboards-images/gettargetforaction.png#lightbox)

Cette méthode parcourt la chaîne de hiérarchie jusqu’à ce que le contrôleur d’affichage de conteneur correct soit trouvé. Par exemple :

1. Si une  `ShowViewController` méthode est appelée, le premier contrôleur d’affichage de la chaîne qui implémente cette méthode est le contrôleur de navigation. il est donc utilisé comme parent de la nouvelle vue.
1. Si une  `ShowDetailViewController` méthode a été appelée à la place, le contrôleur de vue fractionnée est le premier contrôleur d’affichage à l’implémenter, donc il est utilisé comme parent.

La `GetTargetForAction` méthode fonctionne en localisant un contrôleur d’affichage qui implémente une action donnée, puis demande ce contrôleur d’affichage s’il souhaite recevoir cette action. Étant donné que cette méthode est publique, les développeurs peuvent créer leurs propres méthodes personnalisées qui fonctionnent de la même façon que les `ShowViewController` méthodes et intégrées `ShowDetailViewController` .

## <a name="adaptive-presentation"></a>Présentation adaptative

Dans iOS 8, Apple a également fait des présentations menu segue ( `UIPopoverPresentationController` ) adaptative. Par conséquent, un contrôleur d’affichage de présentation menu segue présente automatiquement une vue menu segue normale dans une classe de taille normale, mais l’affiche en plein écran dans une classe de taille horizontale (par exemple, sur un iPhone).

Pour tenir compte des modifications au sein du système de plan conceptuel unifié, un nouvel objet contrôleur a été créé pour gérer les contrôleurs d’affichage présentés `UIPresentationController` . Ce contrôleur est créé à partir du moment où le contrôleur d’affichage est présenté jusqu’à ce qu’il soit fermé. Comme il s’agit d’une classe de gestion, il peut être considéré comme une superclasse sur le contrôleur d’affichage, car il répond aux modifications de l’appareil qui affectent le contrôleur d’affichage (par exemple, l’orientation) qui sont ensuite renvoyées dans le contrôleur d’affichage des contrôles du contrôleur de présentation.

Lorsque le développeur présente un contrôleur d’affichage à l’aide de la `PresentViewController` méthode, la gestion du processus de présentation est transremise à `UIKit` . UIKit gère, entre autres choses, le contrôleur approprié pour le style en cours de création, à la seule exception près lorsqu’un contrôleur d’affichage a le style défini sur `UIModalPresentationCustom` . Ici, l’application peut fournir sa propre PresentationController au lieu d’utiliser le `UIKit` contrôleur.

### <a name="custom-presentation-styles"></a>Styles de présentation personnalisés

Avec un style de présentation personnalisé, les développeurs ont la possibilité d’utiliser un contrôleur de présentation personnalisé. Ce contrôleur personnalisé peut être utilisé pour modifier l’apparence et le comportement de la vue à laquelle il est associé.

<a name="size-classes"></a>

## <a name="working-with-size-classes"></a>Utilisation des classes de taille

Le projet Xamarin de photos adaptatif qui est inclus dans cet article fournit un exemple fonctionnel de l’utilisation des classes de taille et des contrôleurs d’affichage adaptatif dans une application d’interface unifiée iOS 8.

Bien que l’application crée entièrement son interface utilisateur à partir du code, par opposition à l’utilisation du concepteur IOS et à la création d’une table de montage séquentiel unifiée, les mêmes techniques s’appliquent. Plus loin dans cet article, nous allons montrer comment utiliser des classes de taille avec un Storyboard unifié et le concepteur iOS dans une application Xamarin.

Examinons à présent de plus près la façon dont le projet de photos adaptatives implémente plusieurs fonctionnalités de classe de taille dans iOS 8 pour créer une application adaptative.

### <a name="adapting-to-trait-environment-changes"></a>Adaptation aux modifications de l’environnement de trait

Lors de l’exécution de l’application images adaptatives sur un iPhone, lorsque l’utilisateur fait pivoter l’appareil du portrait au paysage, le contrôleur du mode fractionné affiche à la fois le mode maître et le mode Détails :

 [![Le contrôleur du mode fractionné affiche à la fois le mode maître et le mode Détails comme indiqué ici](unified-storyboards-images/rotation.png)](unified-storyboards-images/rotation.png#lightbox)

Pour ce faire, vous devez substituer la `UpdateConstraintsForTraitCollection` méthode du contrôleur d’affichage et ajuster les contraintes en fonction de la valeur de `VerticalSizeClass` . Par exemple :

```csharp
public void UpdateConstraintsForTraitCollection (UITraitCollection collection)
{
    var views = NSDictionary.FromObjectsAndKeys (
        new object[] { TopLayoutGuide, ImageView, NameLabel, ConversationsLabel, PhotosLabel },
        new object[] { "topLayoutGuide", "imageView", "nameLabel", "conversationsLabel", "photosLabel" }
    );

    var newConstraints = new List<NSLayoutConstraint> ();
    if (collection.VerticalSizeClass == UIUserInterfaceSizeClass.Compact) {
        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|[imageView]-[nameLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("[imageView]-[conversationsLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("[imageView]-[photosLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:|[topLayoutGuide]-[nameLabel]-[conversationsLabel]-[photosLabel]",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:|[topLayoutGuide][imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.Add (NSLayoutConstraint.Create (ImageView, NSLayoutAttribute.Width, NSLayoutRelation.Equal,
            View, NSLayoutAttribute.Width, 0.5f, 0.0f));
    } else {
        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|[imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[nameLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[conversationsLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[photosLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:[topLayoutGuide]-[nameLabel]-[conversationsLabel]-[photosLabel]-20-[imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));
    }

    if (constraints != null)
        View.RemoveConstraints (constraints.ToArray ());

    constraints = newConstraints;
    View.AddConstraints (constraints.ToArray ());
}
```

### <a name="adding-transition-animations"></a>Ajout d’animations de transition

Lorsque le contrôleur du mode fractionné de l’application images adaptatives passe de réduit à développé, les animations sont ajoutées aux animations par défaut en substituant la `WillTransitionToTraitCollection` méthode du contrôleur d’affichage. Par exemple :

```csharp
public override void WillTransitionToTraitCollection (UITraitCollection traitCollection, IUIViewControllerTransitionCoordinator coordinator)
{
    base.WillTransitionToTraitCollection (traitCollection, coordinator);
    coordinator.AnimateAlongsideTransition ((UIViewControllerTransitionCoordinatorContext) => {
        UpdateConstraintsForTraitCollection (traitCollection);
        View.SetNeedsLayout ();
    }, (UIViewControllerTransitionCoordinatorContext) => {
    });
}
```

### <a name="overriding-the-trait-environment"></a>Substitution de l’environnement de trait

Comme indiqué ci-dessus, l’application images adaptatives force le contrôleur du mode fractionné à afficher les détails et les vues principales lorsque l’appareil iPhone est dans la vue paysage.

Pour ce faire, utilisez le code suivant dans le contrôleur d’affichage :

```csharp
private UITraitCollection forcedTraitCollection = new UITraitCollection ();
...

public UITraitCollection ForcedTraitCollection {
    get {
        return forcedTraitCollection;
    }

    set {
        if (value != forcedTraitCollection) {
            forcedTraitCollection = value;
            UpdateForcedTraitCollection ();
        }
    }
}
...

public override void ViewWillTransitionToSize (SizeF toSize, IUIViewControllerTransitionCoordinator coordinator)
{
    ForcedTraitCollection = toSize.Width > 320.0f ?
         UITraitCollection.FromHorizontalSizeClass (UIUserInterfaceSizeClass.Regular) :
         new UITraitCollection ();

    base.ViewWillTransitionToSize (toSize, coordinator);
}

public void UpdateForcedTraitCollection ()
{
    SetOverrideTraitCollection (forcedTraitCollection, viewController);
}
```

### <a name="expanding-and-collapsing-the-split-view-controller"></a>Développement et réduction du contrôleur d’affichage fractionné

Examinons ensuite comment le développement et la réduction du comportement du contrôleur d’affichage fractionné ont été implémentés dans Xamarin. Dans `AppDelegate` , lorsque le contrôleur de vue fractionnée est créé, son délégué est assigné pour gérer ces modifications :

```csharp
public class SplitViewControllerDelegate : UISplitViewControllerDelegate
{
    public override bool CollapseSecondViewController (UISplitViewController splitViewController,
        UIViewController secondaryViewController, UIViewController primaryViewController)
    {
        AAPLPhoto photo = ((CustomViewController)secondaryViewController).Aapl_containedPhoto (null);
        if (photo == null) {
            return true;
        }

        if (primaryViewController.GetType () == typeof(CustomNavigationController)) {
            var viewControllers = new List<UIViewController> ();
            foreach (var controller in ((UINavigationController)primaryViewController).ViewControllers) {
                var type = controller.GetType ();
                MethodInfo method = type.GetMethod ("Aapl_containsPhoto");

                if ((bool)method.Invoke (controller, new object[] { null })) {
                    viewControllers.Add (controller);
                }
            }

            ((UINavigationController)primaryViewController).ViewControllers = viewControllers.ToArray<UIViewController> ();
        }

        return false;
    }

    public override UIViewController SeparateSecondaryViewController (UISplitViewController splitViewController,
        UIViewController primaryViewController)
    {
        if (primaryViewController.GetType () == typeof(CustomNavigationController)) {
            foreach (var controller in ((CustomNavigationController)primaryViewController).ViewControllers) {
                var type = controller.GetType ();
                MethodInfo method = type.GetMethod ("Aapl_containedPhoto");

                if (method.Invoke (controller, new object[] { null }) != null) {
                    return null;
                }
            }
        }

        return new AAPLEmptyViewController ();
    }
}
```

La `SeparateSecondaryViewController`  méthode effectue un test pour déterminer si une photo est affichée et prend une mesure en fonction de cet État. Si aucune photo n’est affichée, elle réduit le contrôleur d’affichage secondaire afin que le contrôleur d’affichage principal soit affiché.

La `CollapseSecondViewController` méthode est utilisée lors du développement du contrôleur d’affichage fractionné pour voir si des photos existent sur la pile, si elle est réduite à cette vue.

### <a name="moving-between-view-controllers"></a>Déplacement entre les contrôleurs d’affichage

Voyons ensuite comment l’application de photos adaptatives se déplace entre les contrôleurs d’affichage. Dans la `AAPLConversationViewController` classe, lorsque l’utilisateur sélectionne une cellule dans le tableau, la `ShowDetailViewController` méthode est appelée pour afficher le mode Détails :

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    var photo = PhotoForIndexPath (indexPath);
    var controller = new AAPLPhotoViewController ();
    controller.Photo = photo;

    int photoNumber = indexPath.Row + 1;
    int photoCount = (int)Conversation.Photos.Count;
    controller.Title = string.Format ("{0} of {1}", photoNumber, photoCount);
    ShowDetailViewController (controller, this);
}
```

### <a name="displaying-disclosure-indicators"></a>Affichage des indicateurs de divulgation

Dans l’application photo adaptative, il existe plusieurs endroits où les indicateurs de divulgation sont masqués ou affichés en fonction des modifications apportées à l’environnement de trait. Cela est géré avec le code suivant :

```csharp
public bool Aapl_willShowingViewControllerPushWithSender ()
{
    var selector = new Selector ("Aapl_willShowingViewControllerPushWithSender");
    var target = this.GetTargetViewControllerForAction (selector, this);

    if (target != null) {
        var type = target.GetType ();
        MethodInfo method = type.GetMethod ("Aapl_willShowingDetailViewControllerPushWithSender");
        return (bool)method.Invoke (target, new object[] { });
    } else {
        return false;
    }
}

public bool Aapl_willShowingDetailViewControllerPushWithSender ()
{
    var selector = new Selector ("Aapl_willShowingDetailViewControllerPushWithSender");
    var target = this.GetTargetViewControllerForAction (selector, this);

    if (target != null) {
        var type = target.GetType ();
        MethodInfo method = type.GetMethod ("Aapl_willShowingDetailViewControllerPushWithSender");
        return (bool)method.Invoke (target, new object[] { });
    } else {
        return false;
    }
}
```

Celles-ci sont implémentées à l’aide de la `GetTargetViewControllerForAction` méthode décrite en détail ci-dessus.

Lorsqu’un contrôleur d’affichage de table affiche des données, il utilise les méthodes implémentées ci-dessus pour voir si un push va se produire, et s’il faut ou non afficher ou masquer l’indicateur de divulgation en conséquence :

```csharp
public override void WillDisplay (UITableView tableView, UITableViewCell cell, NSIndexPath indexPath)
{
    bool pushes = ShouldShowConversationViewForIndexPath (indexPath) ?
         Aapl_willShowingViewControllerPushWithSender () :
         Aapl_willShowingDetailViewControllerPushWithSender ();

    cell.Accessory = pushes ? UITableViewCellAccessory.DisclosureIndicator : UITableViewCellAccessory.None;
    var conversation = ConversationForIndexPath (indexPath);
    cell.TextLabel.Text = conversation.Name;
}
```

### <a name="new-showdetailtargetdidchangenotification-type"></a>Nouveau `ShowDetailTargetDidChangeNotification` type

Apple a ajouté un nouveau type de notification pour travailler avec des classes de taille et des environnements de trait à partir d’un contrôleur d’affichage fractionné, `ShowDetailTargetDidChangeNotification` . Cette notification est envoyée chaque fois que la vue détaillée de la cible d’un contrôleur d’affichage fractionné change, par exemple lorsque le contrôleur est développé ou réduit.

L’application images adaptatives utilise cette notification pour mettre à jour l’état de l’indicateur de divulgation lorsque le contrôleur d’affichage détaillé change :

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    TableView.RegisterClassForCellReuse (typeof(UITableViewCell), AAPLListTableViewControllerCellIdentifier);
    NSNotificationCenter.DefaultCenter.AddObserver (this, new Selector ("showDetailTargetDidChange:"),
        UIViewController.ShowDetailTargetDidChangeNotification, null);
    ClearsSelectionOnViewWillAppear = false;
}
```

Examinez de plus près l’application images adaptatives pour voir toutes les façons dont les classes de taille, les collections de caractéristiques et les contrôleurs d’affichage adaptatif peuvent être utilisés pour créer facilement une application unifiée dans Xamarin. iOS.

## <a name="unified-storyboards"></a>Storyboards unifiés

Nouveauté d’iOS 8, les storyboards unifiés permettent au développeur de créer un fichier de Storyboard unifié qui peut être affiché sur les appareils iPhone et iPad en ciblant plusieurs classes de taille. En utilisant des storyboards unifiés, le développeur écrit moins de code spécifique à l’interface utilisateur et n’a qu’une seule conception d’interface à créer et à gérer.

Les principaux avantages des storyboards unifiés sont les suivants :

- Utilisez le même fichier de Storyboard pour iPhone et iPad.
- Déployez en arrière vers iOS 6 et iOS 7.
- Affichez un aperçu de la disposition des différents appareils, orientations et versions de système d’exploitation dans le concepteur iOS Xamarin.

Cette fonctionnalité est entièrement prise en charge dans Visual Studio pour Mac

<a name="enabling-size-classes"></a>

### <a name="enabling-size-classes"></a>Activation des classes de taille

Par défaut, tout nouveau projet Xamarin. iOS nous redimensionnera les classes. Pour utiliser des classes de taille et des SEGUES adaptatifs à l’intérieur d’un plan conceptuel à partir d’un ancien projet, vous devez d’abord le convertir au format de Storyboard unifié Xcode 6 à partir du concepteur iOS.

Pour ce faire, ouvrez le Storyboard à convertir dans le concepteur iOS et activez la case à cocher **utiliser les classes de taille** :

 [![La case à cocher utiliser les classes de taille](unified-storyboards-images/sizeclass01.png)](unified-storyboards-images/sizeclass01.png#lightbox)

Le concepteur iOS confirme que le développeur souhaite convertir le format de la table de montage séquentiel pour utiliser les classes de taille :

 [![Alerte d’utilisation des classes de taille](unified-storyboards-images/sizeclass02.png)](unified-storyboards-images/sizeclass02.png#lightbox)

> [!IMPORTANT]
> La mise en page automatique doit également être vérifiée pour que les classes de taille fonctionnent correctement.

### <a name="generic-device-types"></a>Types d’appareils génériques

Une fois que la table de montage séquentiel a été convertie pour utiliser des classes de taille, elle est réaffichée dans le Aire de conception et la **vue en tant que** périphérique est générique :

 [![Afficher en tant que type d’appareil générique](unified-storyboards-images/sizeclass03.png)](unified-storyboards-images/sizeclass03.png#lightbox)

Lorsque le type d’appareil générique est sélectionné, tous les contrôleurs d’affichage sont redimensionnés en carré 600 x 600. Ce carré représente les tailles de toute largeur et de toute hauteur. Quand le concepteur iOS est dans ce mode, toutes les modifications s’appliquent à toutes les classes de taille.

Le développeur a également la possibilité d’afficher l’aire de conception en tant qu’iPhone :

 [![Affichage de l’aire de conception en tant qu’iPhone](unified-storyboards-images/sizeclass04.png)](unified-storyboards-images/sizeclass04.png#lightbox)

Ou l’afficher en tant qu’iPad :

 [![Affichage de l’aire de conception en tant qu’iPad](unified-storyboards-images/sizeclass05.png)](unified-storyboards-images/sizeclass05.png#lightbox)

### <a name="select-a-size-class"></a>Sélectionner une classe de taille

Le bouton de sélection de la classe taille se trouve dans le coin supérieur gauche du Aire de conception (près de la vue en tant que liste déroulante). Elle permet au développeur de sélectionner les classes de taille en cours de modification :

 [![Sélectionner une classe de taille](unified-storyboards-images/sizeclass06.png)](unified-storyboards-images/sizeclass06.png#lightbox)

Le sélecteur présente la sélection de la classe taille sous la forme d’une grille 3 x 3. Chacun des carrés de la grille représente une combinaison d’une classe Width et d’une classe Height. Le carré central sélectionne la classe toute largeur/toute taille de hauteur (qui est la vue par défaut pour un Storyboard unifié). Lorsque ce carré est sélectionné, le développeur modifie la disposition par défaut, qui est héritée par toutes les autres configurations.

Le carré dans le coin supérieur gauche de la grille représente la classe de taille compacte/hauteur compacte :

 [![Classe compact largeur/hauteur compacte](unified-storyboards-images/sizeclass07.png)](unified-storyboards-images/sizeclass07.png#lightbox)

Ce mode correspond à un iPhone dans l’orientation paysage. Le carré dans le coin inférieur droit de la grille représente la classe de taille normale de largeur/hauteur normale, qui représente un iPad :

 [![Classe de taille normale de largeur/hauteur normale](unified-storyboards-images/sizeclass08.png)](unified-storyboards-images/sizeclass08.png#lightbox)

Pour modifier la disposition d’un iPhone dans l’orientation portrait, sélectionnez le carré dans le coin inférieur gauche. Il s’agit de la classe compact largeur/taille normale :

 [![Classe compact largeur/taille normale](unified-storyboards-images/sizeclass09.png)](unified-storyboards-images/sizeclass09.png#lightbox)

Cliquez sur le carré pour le sélectionner et la Aire de conception modifiera la taille des contrôleurs d’affichage pour qu’ils correspondent à la nouvelle sélection :

 [![La Aire de conception modifiera la taille des contrôleurs d’affichage pour qu’ils correspondent à la nouvelle sélection comme indiqué](unified-storyboards-images/sizeclass10.png)](unified-storyboards-images/sizeclass10.png#lightbox)

Reportez-vous à la section taille de la classe de cet article pour plus d’informations sur les classes de taille et sur la manière dont elles affectent la disposition des iPhone et des iPad.

### <a name="adaptive-segue-types"></a>Types segue adaptatifs

Si le développeur a déjà utilisé des storyboards, il connaîtra les types segue de **Push**, **modal** et **menu segue**existants. Lorsque les classes de taille sont activées sur un fichier de Storyboard unifié, les types adaptatifs segue suivants (qui correspondent à la nouvelle API de contrôleur d’affichage décrite ci-dessus) sont disponibles : **Afficher** et **afficher les détails**.

> [!IMPORTANT]
> Lorsque les classes de taille sont activées, les SEGUES existants sont convertis en nouveaux types.

Prenons l’exemple d’une application iOS 8 qui utilise une table de montage séquentiel unifiée avec un contrôleur d’affichage fractionné avec un menu de navigation de jeu simple dans la vue maître. Si l’utilisateur clique sur un bouton de menu, le contrôleur d’affichage de l’élément sélectionné doit s’afficher dans la section des détails du contrôleur d’affichage fractionné lors de l’exécution sur un iPad. Sur un iPhone, le contrôleur d’affichage de l’élément doit faire l’objet d’un push dans la pile de navigation.

Pour obtenir cet effet, dans le contrôle du concepteur iOS, cliquez sur le bouton, puis faites glisser une ligne vers le contrôleur d’affichage à afficher. Lorsque le bouton de la souris est relâché, sélectionnez `Show Detail` dans le menu contextuel type de segue :

 [![Sélectionnez Afficher les détails dans le menu contextuel type de Segue](unified-storyboards-images/segue01.png)](unified-storyboards-images/segue01.png#lightbox)

Le nouveau segue sera créé entre le bouton et le contrôleur d’affichage. À présent, exécutez l’application dans le simulateur iPhone et le menu principal s’affiche :

 [![Menu principal](unified-storyboards-images/segue02.png)](unified-storyboards-images/segue02.png#lightbox)

Cliquez sur le bouton **Sélectionner un jeu** et le contrôleur d’affichage de l’élément fait l’objet d’un push dans la pile de navigation :

 [![Le contrôleur d’affichage des éléments fait l’objet d’un push dans la pile de navigation comme indiqué](unified-storyboards-images/segue03.png)](unified-storyboards-images/segue03.png#lightbox)

Arrêtez le simulateur iPhone et exécutez l’application dans le simulateur iPad. Basculez vers l’orientation paysage et le menu principal s’affiche à nouveau :

 [![Menu principal affiché](unified-storyboards-images/segue04.png)](unified-storyboards-images/segue04.png#lightbox)

Là encore, cliquez sur le bouton **Sélectionner un jeu** et le contrôleur d’affichage de l’élément s’affiche dans la section Détails du contrôleur du mode fractionné :

 [![Le contrôleur d’affichage des éléments affiché dans la section des détails du contrôleur d’affichage fractionné](unified-storyboards-images/segue05.png)](unified-storyboards-images/segue05.png#lightbox)

### <a name="excluding-an-element-from-a-size-class"></a>Exclusion d’un élément d’une classe Size

Il peut arriver qu’un élément donné (tel qu’une vue, un contrôle ou une contrainte) ne soit pas obligatoire dans une classe de taille spécifique. Pour exclure un élément d’une classe Size, sélectionnez l’élément souhaité à exclure dans le **aire de conception**. Faites défiler vers le bas de l' **Explorateur de propriétés** , puis cliquez sur le menu déroulant **engrenage** . Sélectionnez la combinaison de **largeur** et de **hauteur** pour exclure l’élément :

[![Sélectionner la combinaison de largeur et de hauteur](unified-storyboards-images/exclude-a.png)](unified-storyboards-images/exclude-a.png#lightbox)

Un nouveau *cas d’exclusion* sera ajouté à l’élément dans le bas de l' **Explorateur de propriétés**. Ensuite, désactivez la case à cocher **installé** pour la classe de taille donnée :

[![Désactivez la case à cocher installé](unified-storyboards-images/exclude-b.png)](unified-storyboards-images/exclude-b.png#lightbox)

Basculer la Aire de conception sur la largeur et la hauteur à partir desquelles l’élément a été exclu, elle a été supprimée de la classe de taille donnée, mais pas de la conception de l’interface utilisateur entière :

 [![Faire passer le Aire de conception à la largeur et à la hauteur à laquelle l’élément a été exclu](unified-storyboards-images/exclude02.png)](unified-storyboards-images/exclude02.png#lightbox)

Revenir à la classe de taille n’importe quelle largeur/toute hauteur et l’élément est toujours en place :

 [![Retour à la classe n’importe quelle largeur/n’importe quelle taille de hauteur](unified-storyboards-images/exclude03.png)](unified-storyboards-images/exclude03.png#lightbox)

Lorsque l’application est exécutée dans le simulateur iPad, l’élément s’affiche :

 [![Élément affiché lorsque l’application en cours d’exécution dans le simulateur iPad](unified-storyboards-images/exclude04.png)](unified-storyboards-images/exclude04.png#lightbox)

Et lorsque l’application est exécutée sur le simulateur iPhone, l’élément est manquant :

 [![Élément manquant lors de l’exécution de l’application dans le simulateur iPhone](unified-storyboards-images/exclude05.png)](unified-storyboards-images/exclude05.png#lightbox)

Pour supprimer un cas d’exclusion d’un élément, sélectionnez simplement l’élément dans le **aire de conception**, faites défiler vers le bas de l' **Explorateur de propriétés** et cliquez sur le bouton en **-** regard du cas à supprimer.

Pour voir une implémentation des storyboards unifiés, consultez l' `UnifiedStoryboard` exemple d’application Xamarin iOS 8 jointe à ce document.

## <a name="dynamic-launch-screens"></a>Écrans de lancement dynamique

Le fichier d’écran de lancement s’affiche sous la forme d’un écran de démarrage lorsqu’une application iOS est lancée pour fournir des commentaires à l’utilisateur que l’application est en train de démarrer. Avant iOS 8, le développeur devait inclure plusieurs `Default.png` ressources d’image pour chaque type d’appareil, l’orientation et la résolution d’écran sur lesquels l’application s’exécutait. Par exemple,,, `Default@2x.png` `Default-Landscape@2x~ipad.png` `Default-Portrait@2x~ipad.png` , etc.

La factorisation des nouveaux appareils iPhone 6 et iPhone 6 plus (et de la prochaine Apple Watch) avec tous les appareils iPhone et iPad existants représente un grand nombre de tailles, d’orientations et de résolutions variables des `Default.png` ressources d’images d’écran de démarrage qui doivent être créées et gérées. En outre, ces fichiers peuvent être très volumineux et « grossir » le bundle d’applications livrable, ce qui augmente le temps nécessaire au téléchargement de l’application à partir de l’App Store iTunes (ce qui peut empêcher sa remise sur un réseau cellulaire) et l’augmentation de la quantité de stockage requise sur l’appareil de l’utilisateur final.

Nouveauté d’iOS 8, le développeur peut créer un seul fichier atomique `.xib` dans Xcode qui utilise des classes de mise en page et de taille automatiques pour créer un *écran de lancement dynamique* qui fonctionnera pour chaque appareil, résolution et orientation. Cela réduit non seulement la quantité de travail requise du développeur pour créer et gérer toutes les ressources d’image requises, mais elle réduit considérablement la taille de l’offre groupée installée de l’application.

Les écrans de lancement dynamiques présentent les limitations et les considérations suivantes :

- Utilisez uniquement des `UIKit` classes.
- Utilisez un affichage racine unique qui est un `UIView` `UIViewController` objet ou.
- N’établissez aucune connexion au code de l’application (n’ajoutez pas d' **actions** ou de **prises de sortie**).
- N’ajoutez pas d' `UIWebView` objets.
- N’utilisez pas de classes personnalisées.
- N’utilisez pas d’attributs d’exécution.

Avec les instructions ci-dessus à l’esprit, examinons l’ajout d’un écran de lancement dynamique à un projet Xamarin iOS 8 existant.

Effectuez les actions suivantes :

1. Ouvrez **Visual Studio pour Mac** et chargez la **solution** pour ajouter l’écran de lancement dynamique à.
2. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le `MainStoryboard.storyboard` fichier, puis sélectionnez **Ouvrir avec**  >  **Xcode Interface Builder**:

    [![Ouvrir avec Xcode Interface Builder](unified-storyboards-images/dls01.png)](unified-storyboards-images/dls01.png#lightbox)
3. Dans Xcode, sélectionnez **fichier**  >  **nouveau**  >  **fichier...**:

    [![Sélectionner un fichier/nouveau](unified-storyboards-images/dls02.png)](unified-storyboards-images/dls02.png#lightbox)
4. Sélectionnez **iOS**  >  **User Interface**  >  l'**écran de lancement** de l’interface utilisateur iOS, puis cliquez sur le bouton **suivant** :

    [![Sélectionnez iOS/interface utilisateur/lancer l’écran](unified-storyboards-images/dls03.png)](unified-storyboards-images/dls03.png#lightbox)
5. Nommez le fichier `LaunchScreen.xib` , puis cliquez sur le bouton **créer** :

    [![Nommez le fichier écran d’installation. XIB](unified-storyboards-images/dls04.png)](unified-storyboards-images/dls04.png#lightbox)
6. Modifiez la conception de l’écran de lancement en ajoutant des éléments graphiques et en utilisant des contraintes de disposition pour les positionner pour les périphériques, les orientations et les tailles d’écran spécifiés :

    [![Modification de la conception de l’écran de lancement](unified-storyboards-images/dls05.png)](unified-storyboards-images/dls05.png#lightbox)
7. Enregistrez les changements apportés à `LaunchScreen.xib`.
8. Sélectionnez la **cible applications** et l’onglet **général** :

    [![Sélectionnez la cible applications et l’onglet général](unified-storyboards-images/dls06.png)](unified-storyboards-images/dls06.png#lightbox)
9. Cliquez sur le bouton **Choose info. plist** , sélectionnez le `Info.plist` pour l’application Xamarin, puis cliquez sur le bouton **choisir** :

    [![Sélectionnez le fichier info. plist pour l’application Xamarin](unified-storyboards-images/dls07.png)](unified-storyboards-images/dls07.png#lightbox)
10. Dans la section **icônes d’application et images de lancement** , ouvrez la liste déroulante démarrer le fichier d' **écran** , puis choisissez le `LaunchScreen.xib` créé ci-dessus :

    [![Choisissez écran d’installation. XIB](unified-storyboards-images/dls08.png)](unified-storyboards-images/dls08.png#lightbox)
11. Enregistrez les modifications apportées au fichier et revenez à Visual Studio pour Mac.
12. Attendez que Visual Studio pour Mac termine la synchronisation des modifications avec Xcode.
13. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le dossier de **ressources** , puis sélectionnez **Ajouter**  >  **Ajouter des fichiers...**:

    [![Sélectionnez Ajouter/Ajouter des fichiers...](unified-storyboards-images/dls09.png)](unified-storyboards-images/dls09.png#lightbox)
14. Sélectionnez le `LaunchScreen.xib` fichier créé ci-dessus, puis cliquez sur le bouton **ouvrir** :

    [![Sélectionner le fichier écran d’installation. XIB](unified-storyboards-images/dls10.png)](unified-storyboards-images/dls10.png#lightbox)
15. Générez l’application.

### <a name="testing-the-dynamic-launch-screen"></a>Test de l’écran de lancement dynamique

Dans Visual Studio pour Mac, sélectionnez le simulateur iPhone 4 et exécutez l’application. L’écran de lancement dynamique s’affiche au format et à l’orientation appropriés :

[![Écran de lancement dynamique affiché dans l’orientation verticale](unified-storyboards-images/dls11.png)](unified-storyboards-images/dls11.png#lightbox)

Arrêtez l’application dans Visual Studio pour Mac et sélectionnez un appareil iPad iOS 8. Exécutez l’application et l’écran de lancement sera correctement mis en forme pour cet appareil et cette orientation :

[![Écran de lancement dynamique affiché dans l’orientation horizontale](unified-storyboards-images/dls12.png)](unified-storyboards-images/dls12.png#lightbox)

Revenez à Visual Studio pour Mac et arrêtez l’exécution de l’application.

### <a name="working-with-ios-7"></a>Utilisation d’iOS 7

Pour assurer la compatibilité descendante avec iOS 7, il vous suffit d’inclure les `Default.png` ressources d’image habituelles dans l’application iOS 8. iOS retourne au comportement précédent et utilise ces fichiers comme écran de démarrage lors de l’exécution sur un appareil iOS 7.

Pour afficher l’implémentation d’un écran de lancement dynamique dans Xamarin, consultez l’exemple d’application d' [écrans de lancement dynamique](/samples/xamarin/ios-samples/ios8-dynamiclaunchscreen) , iOS 8 joint à ce document.

## <a name="summary"></a>Récapitulatif

Cet article a examiné rapidement les classes de taille et comment elles affectent la disposition des appareils iPhone et iPad. Il a vu comment les caractéristiques, les environnements de trait et les collections de caractéristiques fonctionnent avec les classes de taille pour créer des interfaces unifiées. Il a suivi un bref aperçu des contrôleurs d’affichage adaptatif et de la façon dont ils fonctionnent avec les classes de taille dans des interfaces unifiées. Il a vu comment implémenter des classes de taille et des interfaces unifiées entièrement à partir de code C# à l’intérieur d’une application Xamarin iOS 8.

Enfin, cet article a abordé les principes de base de la création de storyboards unifiées avec le concepteur iOS Xamarin qui fonctionnera sur tous les appareils iOS et créera un écran de lancement dynamique unique qui sera affiché comme écran de démarrage sur chaque appareil iOS 8.

## <a name="related-links"></a>Liens associés

- [Photos adaptatives (exemple)](/samples/xamarin/ios-samples/ios8-adaptivephotos)
- [Écrans de lancement dynamique (exemple)](/samples/xamarin/ios-samples/ios8-dynamiclaunchscreen)
- [Introduction à iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Dispositions dynamiques dans iOS8-évolution de 2014 (vidéo)](https://youtu.be/f3mMGlS-lM4)
- [UIPresentationController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPresentationController_class/)
- [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset)