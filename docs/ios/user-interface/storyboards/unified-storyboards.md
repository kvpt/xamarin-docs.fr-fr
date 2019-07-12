---
title: Storyboards unifiés dans Xamarin.iOS
description: Ce document décrit les storyboards unifiés dans Xamarin.iOS. Storyboards unifiés permettent aux développeurs de prendre en charge plusieurs tailles d’écran avec une définition d’une interface unique.
ms.prod: xamarin
ms.assetid: F6F70374-FC2A-4401-A712-A16D0F9B340F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: c9d98d9d3052f52dc7860ba513756e3a33d1dc58
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831919"
---
# <a name="unified-storyboards-in-xamarinios"></a>Storyboards unifiés dans Xamarin.iOS

iOS 8 inclut un mécanisme de nouvelle et plus simple à utiliser pour la création de l’interface utilisateur, la table de montage séquentiel unifiée. Avec un storyboard unique pour couvrir toutes les tailles d’écran de matériel différents, rapides et réactifs des vues peuvent être créés dans un « conception-une fois, utilisez plusieurs « style.

Comme le développeur n’a plus besoin créer une table de montage séquentiel distincte et spécifique pour les appareils iPhone et iPad, ils ont la possibilité de concevoir des applications avec une interface commune, puis personnaliser cette interface pour les classes de taille différente. De cette façon, une application peut être adaptée pour les points forts de chaque facteur de forme et chaque interface utilisateur peut être ajustée pour offrir une expérience optimale.

<a name="size-classes" />

## <a name="size-classes"></a>Classes de taille

Avant d’iOS 8, le développeur a utilisée `UIInterfaceOrientation` et `UIInterfaceIdiom` pour faire la distinction entre les modes portrait et paysage et entre les appareils iPhone et iPad. Dans iOS8, l’orientation et l’appareil est déterminé est à l’aide de *catégories de taille*.

Les appareils sont définis par les Classes de taille, dans des axes verticaux et horizontaux, et il existe deux types de classes de taille dans iOS 8 :

-  **Régulière** – il s’agit d’une taille d’écran de grande taille (tels qu’un iPad) ou un gadget qui donne l’impression de grande taille (comme un `UIScrollView`
-  **Compact** – il s’agit pour les appareils plus petits (par exemple, un iPhone). Cette taille prend en compte l’orientation de l’appareil.


Si les deux concepts sont utilisées ensemble, le résultat est une grille de 2 x 2 qui définit les différentes tailles possible qui peuvent être utilisées dans les deux les différentes orientations, comme indiqué dans le diagramme suivant :

 [![](unified-storyboards-images/sizeclassgrid.png "Une grille de 2 x 2 qui définit les différentes tailles possible qui peuvent être utilisés dans des orientations ordinaires et Compact")](unified-storyboards-images/sizeclassgrid.png#lightbox)

Le développeur peut créer un contrôleur d’affichage qui utilise un des quatre possibilités qui entraîneraient des dispositions différentes (comme indiqué dans le graphique ci-dessus).

### <a name="ipad-size-classes"></a>iPad catégories de taille

IPad, en raison de la taille, a un **régulière** classe taille pour les deux orientations.

 [![](unified-storyboards-images/image1.png "iPad catégories de taille")](unified-storyboards-images/image1.png#lightbox)


### <a name="iphone-size-classes"></a>iPhone catégories de taille

L’iPhone comporte des classes de taille différente selon l’orientation de l’appareil :

 [![](unified-storyboards-images/iphonesizeclasses.png "iPhone catégories de taille")](unified-storyboards-images/iphonesizeclasses.png#lightbox)

-  Quand l’appareil est en mode portrait, l’écran a une **compact** classe horizontalement et **régulière** verticalement
-  Quand l’appareil est en mode paysage, les classes de l’écran sont inversés à partir du mode portrait.

### <a name="iphone-6-plus-size-classes"></a>Classes de taille d’iPhone 6 Plus

Les tailles sont les mêmes que les iPhone antérieures en orientation portrait, mais différentes dans le paysage :

[![](unified-storyboards-images/iphone6sizeclasses.png "Classes de taille d’iPhone 6 Plus")](unified-storyboards-images/iphone6sizeclasses.png#lightbox)

Étant donné que l’iPhone 6 Plus présente un écran suffisamment grand, il est capable d’avoir une classe de taille de la largeur normale en mode paysage.

### <a name="support-for-a-new-screen-scale"></a>Prise en charge pour une nouvelle échelle de l’écran

L’iPhone 6 Plus utilise un nouvel affichage Retina HD avec un facteur de mise à l’échelle d’écran de 3.0 (trois fois la d’origine iPhone résolution d’écran). Pour fournir la meilleure expérience possible sur ces appareils, incluent la nouvelle illustration conçue pour cette échelle de l’écran. Dans Xcode 6 et versions ultérieures, la ressource catalogues peuvent inclure des images à 1 x, x 2 et 3 tailles x ; Ajoutez simplement les composants de l’image et iOS choisir les ressources appropriées lors de l’exécution sur un iPhone 6 Plus.

L’image du chargement de comportement dans iOS également reconnaît un `@3x` suffixe dans des fichiers image. Par exemple, si le développeur inclut un composant de l’image (à différentes résolutions) dans le bundle de l’application avec les noms de fichier suivants : `MonkeyIcon.png`, `MonkeyIcon@2x.png`, et `MonkeyIcon@3x.png`. Sur l’iPhone 6 Plus la `MonkeyIcon@3x.png` image est utilisée automatiquement si le développeur charge une image en utilisant le code suivant :

```csharp
UIImage icon = UIImage.FromFile("MonkeyImage.png");
```
Ou si elles affecter l’image à un élément d’interface utilisateur à l’aide de l’iOS concepteur en tant que `MonkeyIcon.png`, le `MonkeyIcon@3x.png` servira, automatiquement, sur l’iPhone 6 Plus.

<a name="dynamic-launch-screens" />

### <a name="dynamic-launch-screens"></a>Écrans de lancement dynamique

Le fichier d’écran de démarrage s’affiche comme écran de démarrage pendant le lance d’une application iOS pour fournir des commentaires à l’utilisateur que l’application est en fait en commençant à distance. Avant d’iOS 8, le développeur devra inclure plusieurs `Default.png` actifs pour chaque résolution de type, l’orientation et écran de l’appareil qui s’exécuterait sur l’application de l’image.

Nouveau à iOS 8, le développeur peut créer un seul, atomique `.xib` fichier dans Xcode qui utilise des Classes de taille et de disposition automatique pour créer un *écran de lancement dynamique* qui fonctionnera pour chaque appareil, la résolution et l’orientation. Cela réduit non seulement la quantité de travail nécessaire au développeur de créer et gérer toutes les ressources d’image obligatoires, mais il réduit la taille de lot installé l’application.

## <a name="traits"></a>Caractéristiques

Les caractéristiques sont des propriétés qui peuvent être utilisées pour déterminer comment une mise en page modifications en tant que les modifications apportées à son environnement. Il s’agit d’un ensemble de propriétés (le `HorizontalSizeClass` et `VerticalSizeClass` selon `UIUserInterfaceSizeClass`), ainsi que l’idiome interface ( `UIUserInterfaceIdiom`) et l’échelle d’affichage.

Tous les États ci-dessus sont encapsulés dans un conteneur Apple désigne comme une Collection de caractéristique ( `UITraitCollection`), qui contient non seulement les propriétés, mais aussi leurs valeurs.

## <a name="trait-environment"></a>Environnement de trait

Les environnements de caractéristique sont une nouvelle interface dans iOS 8 et sont en mesure de retourner une Collection de Trait pour les objets suivants :

-  Écrans ( `UIScreens` ).
-  Windows ( `UIWindows` ).
-  Afficher les contrôleurs ( `UIViewController` ).
-  Vues ( `UIView` ).
-  Contrôleur de présentation ( `UIPresentationController` ).


Le développeur utilise la Collection de caractéristique retournée par un environnement de caractéristique pour déterminer la façon dont une interface utilisateur doit être disposée.

Tous les environnements de caractéristique de rendre une hiérarchie comme indiqué dans le diagramme suivant :

 [![](unified-storyboards-images/viewhierarchy.png "Le diagramme de la hiérarchie des environnements de Trait")](unified-storyboards-images/viewhierarchy.png#lightbox)

La Collection de caractéristiques qui ont chacune des environnements caractéristique ci-dessus circule, par défaut, à partir du parent à l’environnement de l’enfant.

En plus de l’obtention de la Collection actuelle de la caractéristique, l’environnement de caractéristique a un `TraitCollectionDidChange` (méthode), qui peut être substituée dans les sous-classes de la vue ou contrôleur d’affichage. Le développeur peut utiliser cette méthode pour modifier un des éléments d’interface utilisateur qui dépendent des caractéristiques lorsque ces caractéristiques ont changé.

## <a name="typical-trait-collections"></a>Collections de caractéristique classique

Cette section décrit les types classiques de collections de caractéristique que l’utilisateur connaîtrez lorsque vous travaillez avec iOS 8.

Ce qui suit est une Collection de caractéristique classique le développeur peut s’afficher sur un iPhone :

|Propriété|`Value`|
|--- |--- |
|`HorizontalSizeClass`|Compact|
|`VerticalSizeClass`|Normal|
|`UserInterfaceIdom`|Phone|
|`DisplayScale`|2.0|

L’ensemble ci-dessus représente une Collection de caractéristique entièrement qualifié, car il comporte des valeurs pour toutes ses propriétés de trait.

Il est également possible d’avoir une Collection de caractéristique auquel il manque certaines de ses valeurs (c'est-à-dire, Apple en tant que *Unspecified*) :

|Propriété|Valeur|
|--- |--- |
|`HorizontalSizeClass`|Compact|
|`VerticalSizeClass`|Non spécifié|
|`UserInterfaceIdom`|Non spécifié|
|`DisplayScale`|Non spécifié|

En règle générale, cependant, lorsque le développeur demande l’environnement de Trait de sa Collection de caractéristique, elle renvoie une collection entièrement qualifiée comme indiqué dans l’exemple ci-dessus.

Si un environnement de Trait (par exemple, une vue ou d’un contrôleur d’affichage) n’est pas à l’intérieur de la hiérarchie d’affichage actuel, le développeur peut revenir les valeurs non spécifiés pour un ou plusieurs des propriétés de trait.

Le développeur sera également obtenir une Collection de caractéristique partiellement qualifié s’ils utilisent une des méthodes de création fournis par Apple, tels que `UITraitCollection.FromHorizontalSizeClass`, pour créer une nouvelle collection.

Une opération qui peut être effectuée sur plusieurs Collections de caractéristique est en les comparant à d’autres, qui consiste à demander une caractéristique de Collection si elle contient un autre. Qu’entend-on par *relation contenant-contenu* est que, pour n’importe quel paramètre spécifié dans la deuxième collection, la valeur doit correspondre exactement à la valeur dans la première collection.

Pour tester l’utilisation de deux traits le `Contains` méthode de la `UITraitCollection` en passant la valeur de la caractéristique à tester.

Le développeur peut exécuter les comparaisons manuellement dans le code pour déterminer comment les vues de disposition ou de contrôleurs d’affichage. Toutefois, `UIKit` utilise cette méthode en interne pour fournir certaines de ses fonctionnalités, comme dans le Proxy de l’apparence, par exemple.

## <a name="appearance-proxy"></a>Proxy de l’apparence

Le Proxy de l’apparence a été introduit dans les versions antérieures d’e/s pour permettre aux développeurs de personnaliser les propriétés de leurs points de vue. Il a été étendu dans iOS 8 pour prendre en charge les Collections de caractéristique.

Proxys d’apparence incluent désormais une nouvelle méthode, `AppearanceForTraitCollection`, qui retourne un nouveau Proxy d’apparence pour la Collection de caractéristique donnée n’a été transmise. Toutes les personnalisations que le développeur effectue sur cette apparence Proxy sera uniquement effective sur les vues qui sont conformes à la Collection spécifiée de la caractéristique.

En règle générale le développeur passera dans une Collection de caractéristique partiellement spécifié à la `AppearanceForTraitCollection` méthode, tel que celui que vous venez de spécifier un Horizontal taille classe de Compact, afin que leur pu personnaliser n’importe quelle vue dans l’application est compacte horizontalement.

## <a name="uiimage"></a>UIImage

Une autre classe Apple a ajouté la Collection de caractéristique à est `UIImage`. Dans le passé, le développeur devait spécifier une @1X et @2x version de toute ressource graphique bitmap qu’ils veulent en venir à inclure dans l’application (par exemple, une icône).

iOS 8 a été étendue pour permettre au développeur d’inclure plusieurs versions d’une image dans un catalogue de l’Image selon une Collection de caractéristique. Par exemple, le développeur peut inclure une image plus petite pour travailler avec une classe de caractéristiques Compact et une image de taille complète pour n’importe quelle autre collection.

Lorsque l’une des images est utilisée à l’intérieur d’un `UIImageView` classe, l’affichage de l’Image s’affiche automatiquement la version correcte de l’image pour sa Collection de caractéristique. Si l’environnement de caractéristique change (par exemple, le changement d’utilisateur l’appareil du mode portrait au mode paysage), la vue de l’Image sélectionne automatiquement la nouvelle taille d’image pour correspondre à la nouvelle Collection de Trait et modifier sa taille pour correspondre à celui de la version actuelle de l’image en cours affiché.

## <a name="uiimageasset"></a>UIImageAsset

Apple a ajouté une nouvelle classe à iOS 8 appelée `UIImageAsset` pour donner au développeur un contrôle accru sur la sélection de l’image.

Une ressource Image conclut toutes les différentes versions d’une image et permet au développeur de demander une image spécifique qui correspond à une Collection de caractéristique n’a été transmise. Images peuvent être ajoutés ou supprimés à partir d’un composant de l’Image, à la volée.

Pour plus d’informations sur les ressources d’Image, consultez d’Apple [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset) documentation.

## <a name="combining-trait-collections"></a>Combiner des Collections de Trait

Une autre fonction qu’un développeur peut effectuer sur les Collections de caractéristique consiste à additionner deux qui vous entraîner dans la collection combinée, où les valeurs non spécifiés à partir d’une collection sont remplacés par les valeurs spécifiées dans un autre. Cette opération est effectuée à l’aide de la `FromTraitsFromCollections` méthode de la `UITraitCollection` classe.

Comme indiqué ci-dessus, si toutes les caractéristiques n’est pas spécifié dans une des Collections de Trait et est spécifié dans une autre, la valeur sera définie à la version spécifiée. Toutefois, s’il existe plusieurs versions d’une valeur donnée spécifiée, la valeur de la dernière caractéristique Collection sera la valeur qui est utilisée.

## <a name="adaptive-view-controllers"></a>Contrôleurs d’affichage ADAPTATIF

Cette section décrit les détails de la façon dont l’iOS vue et les contrôleurs d’affichage ont adopté les concepts de caractéristiques et les Classes de taille automatiquement plus adaptatif dans les applications du développeur.

### <a name="split-view-controller"></a>Contrôleur d’affichage fractionné

Une des classes de contrôleur d’affichage qui a changé le meilleur dans iOS 8 est le `UISplitViewController` classe. Dans le passé, le développeur utilise souvent un contrôleur d’affichage fractionné sur la version de l’iPad de l’application, et puis ils doivent fournir une version complètement différente de leur hiérarchie d’affichage pour la version iPhone de l’application.

Dans iOS 8, le `UISplitViewController` classe n’est disponible sur les deux plateformes (iPad et iPhone), ce qui permet au développeur de créer une hiérarchie de contrôleur d’affichage qui ne peut fonctionner pour iPhone et iPad.

Lorsqu’un iPhone est en mode paysage, le contrôleur d’affichage fractionné présente ses vues côte-à-côte, comme il le ferait lors de l’affichage sur un iPad.

### <a name="overriding-traits"></a>Substitution de caractéristiques

Environnements de trait en cascade à partir du conteneur parent vers les conteneurs enfants, comme dans le graphique suivant montrant un contrôleur d’affichage fractionné sur un iPad en paysage :

 [![](unified-storyboards-images/cascadingclasses01.png "Un contrôleur d’affichage fractionné sur un iPad en paysage")](unified-storyboards-images/cascadingclasses01.png#lightbox)

Dans la mesure où l’iPad a une classe de taille normale dans les deux orientations horizontales et verticales, le mode Fractionné affiche les vues maître et détail.

Sur un iPhone, où la classe de taille est compacte dans les deux orientations, le contrôleur d’affichage fractionné affiche uniquement la vue détail, comme indiqué ci-dessous :

 [![](unified-storyboards-images/cascadingclasses02.png "Le contrôleur d’affichage fractionné affiche uniquement l’affichage des détails")](unified-storyboards-images/cascadingclasses02.png#lightbox)

Dans une application où le développeur souhaite l’affichage maître et détail sur un iPhone l’orientation paysage, le développeur doit insérer un conteneur parent pour le contrôleur d’affichage fractionné et remplacer la Collection de caractéristique. Comme indiqué dans l’illustration ci-dessous :

 [![](unified-storyboards-images/cascadingclasses03.png "Le développeur doit insérer un conteneur parent pour le contrôleur d’affichage fractionné et remplacer la Collection de Trait")](unified-storyboards-images/cascadingclasses03.png#lightbox)

Un `UIView` est défini comme étant le parent du contrôleur d’affichage fractionné et le `SetOverrideTraitCollection` méthode est appelée sur la vue en passant une nouvelle Collection de Trait et ciblant le contrôleur d’affichage fractionné. Se substitue à la nouvelle Collection de Trait de la `HorizontalSizeClass`, configurez-la sur `Regular`, de sorte que le contrôleur d’affichage fractionné affiche des vues maître et détail sur un iPhone dans l’orientation paysage.

Notez que le `VerticalSizeClass` a été défini sur `unspecified`, ce qui permet à la nouvelle Collection de caractéristique à ajouter à la Collection de Trait sur la page parente, ce qui entraîne un `Compact VerticalSizeClass` pour le contrôleur d’affichage fractionné de l’enfant.

### <a name="trait-changes"></a>Modifications de trait

Cette section prendra un coup de œil, en détail, comment les Collections de caractéristique de transition lorsque l’environnement de caractéristique change. Par exemple, lorsque l’appareil est pivoté de portrait à paysage.

 [![](unified-storyboards-images/traittransitions01.png "Le portrait à paysage de vue d’ensemble de modifications de Trait")](unified-storyboards-images/traittransitions01.png#lightbox)

Tout d’abord, iOS 8 effectue certaines le programme d’installation pour vous préparer à la transition se déroulent. Ensuite, le système s’anime à l’état de transition. Enfin, iOS 8 Nettoie tout état temporaire requis pendant la transition.

iOS 8 fournit plusieurs rappels que le développeur peut utiliser pour participer à la modification de caractéristique comme indiqué dans le tableau suivant :

|Phase|Rappel|Description|
|--- |--- |--- |
|Installation|<ul><li>`WillTransitionToTraitCollection`</li><li>`TraitCollectionDidChange`</li></ul>|<ul><li>Cette méthode est appelée au début d’une modification de caractéristique avant une Collection de caractéristique est définie sur sa nouvelle valeur.</li><li>La méthode est appelée lorsque la valeur de la Collection de caractéristique a changé, mais avant qu’une animation que ne survienne.</li></ul>|
|Animation|`WillTransitionToTraitCollection`|Le coordinateur de Transition qui est passé à cette méthode a un `AnimateAlongside` propriété qui permet au développeur d’ajouter des animations qui seront exécutées, ainsi que les animations par défaut.|
|Nettoyage|`WillTransitionToTraitCollection`|Fournit une méthode pour les développeurs à inclure leur propre code de nettoyage après que la transition a lieu.|

Le `WillTransitionToTraitCollection` méthode est très utile pour animer des contrôleurs d’affichage, ainsi que les modifications de la Collection de caractéristique. Le `WillTransitionToTraitCollection` méthode est uniquement disponible sur les contrôleurs d’affichage ( `UIViewController`) et non sur les autres environnements de caractéristique, comme `UIViews`.

Le `TraitCollectionDidChange` est parfaite pour travailler avec le `UIView` (classe), où le développeur souhaite mettre à jour l’interface utilisateur que la modifiant des caractéristiques.

### <a name="collapsing-the-split-view-controllers"></a>Réduction des contrôleurs d’affichage de fractionnement

Maintenant nous allons Examinez plus en détail ce qui se produit lorsqu’un contrôleur d’affichage fractionné réduit à partir d’une colonne de deux à une vue d’une colonne. Dans le cadre de cette modification, il existe deux processus qui doivent se produire :

-  Par défaut, le contrôleur d’affichage fractionné utilisera le contrôleur d’affichage principal en tant que la vue après que la réduction se produit. Le développeur peut remplacer ce comportement en substituant la `GetPrimaryViewControllerForCollapsingSplitViewController` méthode de la `UISplitViewControllerDelegate` et en fournissant n’importe quel contrôleur d’affichage qu’ils souhaitent afficher dans l’état réduit.
-  Le contrôleur d’affichage secondaire doit sont fusionnées dans le contrôleur d’affichage principal. En règle générale le développeur ne devrez pas aucune action pour cette étape ; le contrôleur d’affichage fractionné inclut la gestion automatique de cette phase basée sur le périphérique matériel. Toutefois, il peut y être certains cas spéciaux où le développeur souhaitez interagir avec cette modification. Appel de la `CollapseSecondViewController` méthode de la `UISplitViewControllerDelegate` permet au contrôleur de vue maître à afficher lorsque la réduction se produit, au lieu de l’affichage des détails.


### <a name="expanding-the-split-view-controller"></a>Développez le contrôleur d’affichage fractionné

Maintenant nous allons Examinez plus en détail ce qui se produit lorsqu’un contrôleur d’affichage fractionné est développé à partir d’un état réduit. Une fois encore, il existe deux étapes qui doivent se produire :

-  Tout d’abord, définissez le nouveau contrôleur d’affichage principal. Par défaut, le contrôleur d’affichage fractionné utilisera automatiquement le contrôleur d’affichage principal à partir de l’affichage réduit. Là encore, le développeur peut remplacer ce comportement à l’aide de la `GetPrimaryViewControllerForExpandingSplitViewController` méthode de la `UISplitViewControllerDelegate` .
-  Une fois que le contrôleur d’affichage principal a été choisi, le contrôleur d’affichage secondaire doit être recréé. Là encore, le contrôleur d’affichage fractionné inclut la gestion automatique de cette phase basée sur le périphérique matériel. Le développeur peut remplacer ce comportement en appelant le `SeparateSecondaryViewController` méthode de la `UISplitViewControllerDelegate` .


Dans un contrôleur d’affichage fractionné, le contrôleur d’affichage principal joue un rôle dans le développement et réduction des vues en implémentant la `CollapseSecondViewController` et `SeparateSecondaryViewController` méthodes de la `UISplitViewControllerDelegate`. `UINavigationController` implémente ces méthodes pour automatiquement push et pop le contrôleur d’affichage secondaire.

### <a name="showing-view-controllers"></a>Contrôleurs d’affichage montrant

Une autre modification par Apple à iOS 8 est de la manière que le développeur affiche des contrôleurs d’affichage. Dans le passé, si l’application avait un contrôleur d’affichage de feuille (par exemple, un contrôleur d’affichage Table), et que le développeur a montré une autre (par exemple, en réponse à l’exploitant d’utilisateur sur une cellule), l’application atteindraient par le biais de la hiérarchie de contrôleur pour le Contrôleur d’affichage de navigation et appelez le `PushViewController` méthode dessus pour afficher la nouvelle vue.

Cela affiche un couplage très étroit entre le contrôleur de Navigation et de l’environnement dans lequel il a été exécuté dans. Dans iOS 8, Apple a découplé cela en fournissant deux nouvelles méthodes :

-  `ShowViewController` – S’adapte pour afficher le nouveau contrôleur d’affichage en fonction de son environnement. Par exemple, dans un `UINavigationController` il transmet simplement la nouvelle vue dans la pile. Dans un contrôleur d’affichage fractionné, le contrôleur d’affichage s’affiche sur le côté gauche en tant que le nouveau contrôleur d’affichage principal. Si aucun contrôleur d’affichage conteneur n’est présent, la nouvelle vue apparaîtront en tant qu’un contrôleur d’affichage Modal.
-  `ShowDetailViewController` – Fonctionne de manière similaire à `ShowViewController`, mais est implémenté sur un contrôleur d’affichage fractionné pour remplacer l’affichage des détails avec le nouveau contrôleur de vue passé. Si le contrôleur d’affichage fractionné est réduit (comme peut se produire dans un Application iPhone), l’appel sera redirigé vers le `ShowViewController` (méthode) et la nouvelle vue seront affichera en tant que le contrôleur d’affichage principal. Là encore, si aucun contrôleur d’affichage conteneur n’est présent, la nouvelle vue apparaîtront en tant qu’un contrôleur d’affichage Modal.


Ces méthodes fonctionnent en commençant au niveau du contrôleur de vue feuille et remonter dans la hiérarchie d’affichage jusqu'à trouver le contrôleur d’affichage conteneur pour gérer l’affichage de la nouvelle vue.

Les développeurs peuvent implémenter `ShowViewController` et `ShowDetailViewController` dans leurs propres contrôleurs d’affichage personnalisé pour obtenir les mêmes automatisé des fonctionnalités qui `UINavigationController` et `UISplitViewController` fournit.

### <a name="how-it-works"></a>Fonctionnement

Dans cette section, nous allons examiner comment ces méthodes sont réellement implémentées dans iOS 8. Première jetons un œil à la nouvelle `GetTargetForAction` méthode :

 [![](unified-storyboards-images/gettargetforaction.png "La nouvelle méthode GetTargetForAction")](unified-storyboards-images/gettargetforaction.png#lightbox)

Cette méthode parcourt la chaîne de la hiérarchie jusqu'à ce que le contrôleur d’affichage conteneur approprié est trouvé. Par exemple :

1.  Si un `ShowViewController` est appelée, le premier contrôleur d’affichage dans la chaîne qui implémente cette méthode est le contrôleur de Navigation, il est utilisé comme parent de la nouvelle vue.
1.  Si un `ShowDetailViewController` méthode a été appelée au lieu de cela, le contrôleur d’affichage fractionné est le premier contrôleur d’affichage pour l’implémenter, il est utilisé comme parent.


Le `GetTargetForAction` méthode fonctionne par localisation d’un contrôleur de vue qui implémente une Action donnée et ensuite demandé ce contrôleur d’affichage s’il souhaite recevoir cette action. Dans la mesure où cette méthode est publique, les développeurs peuvent créer leurs propres méthodes personnalisées qui fonctionnent comme intégrée dans `ShowViewController` et `ShowDetailViewController` méthodes.

## <a name="adaptive-presentation"></a>Présentation adaptative

Dans iOS 8, Apple a effectué des présentations de menu ( `UIPopoverPresentationController`) adaptive également. Ainsi, un contrôleur d’affichage de menu Présentation automatiquement présente une vue normale de menu dans une classe de taille normale, mais affiche plein écran dans une classe de taille horizontalement compact (tel que sur un iPhone).

Pour prendre en compte les modifications au sein du système d’animation unifiée, un nouvel objet de contrôleur a été créé pour gérer les contrôleurs d’affichage présenté — `UIPresentationController`. Ce contrôleur est créé à partir du moment que le contrôleur d’affichage est présenté jusqu'à ce qu’il a été abandonnée. Comme il s’agit d’une classe de gestion, il peut être considéré une super classe sur le contrôleur d’affichage comme il répond aux modifications d’appareil qui affectent le contrôleur d’affichage (par exemple, l’orientation) qui sont ensuite le répercutés dans le contrôleur d’affichage le contrôleur de présentation contrôle.

Lorsque le développeur présente un contrôleur d’affichage à l’aide de la `PresentViewController` (méthode), la gestion du processus de présentation est transmise à `UIKit`. UIKit gère (entre autres), le contrôleur correct pour le style en cours de création, avec la seule exception quand un contrôleur d’affichage a le style défini sur `UIModalPresentationCustom`. Ici, l’application peut fournir doivent impérativement propre PresentationController au lieu d’utiliser la `UIKit` contrôleur.

### <a name="custom-presentation-styles"></a>Styles de présentation personnalisé

Avec un style de présentation personnalisée, les développeurs ont la possibilité d’utiliser un contrôleur de présentation personnalisé. Ce contrôleur personnalisé peut être utilisé pour modifier l’apparence et le comportement de la vue, il est des connexes à.

<a name="size-classes"/>

## <a name="working-with-size-classes"></a>Utilisation des Classes de taille

Le projet Xamarin Photos adaptatif qui accompagne cet article donne un exemple d’utilisation des Classes de taille et adaptatif contrôleurs d’affichage dans une application d’Interface unifiée iOS 8.

Bien que l’application crée complètement son interface utilisateur à partir du code, par opposition à l’aide du concepteur IOS et création d’un Storyboard unifiée, les mêmes techniques s’appliquent. Plus loin dans cet article, nous allons montrer comment utiliser les Classes de taille avec un Storyboard unifiée et le concepteur dans une Application Xamarin iOS.

Maintenant nous allons prendre examiner plus en détail comment le projet de Photos adaptative est mise en œuvre plusieurs fonctionnalités de la classe de taille dans iOS 8 pour créer une Application adaptative.

### <a name="adapting-to-trait-environment-changes"></a>Adaptation aux modifications de l’environnement caractéristique

Lorsque vous exécutez l’application adaptative Photos sur un iPhone, lorsque l’utilisateur fait pivoter l’appareil de portrait à paysage, le contrôleur d’affichage fractionné affiche le masque et les détails de l’affichage :

 [![](unified-storyboards-images/rotation.png "Le contrôleur d’affichage fractionné affiche à la fois le contrôleur et affichage des détails comme illustré ici")](unified-storyboards-images/rotation.png#lightbox)

Cela est effectué en substituant le `UpdateConstraintsForTraitCollection` méthode de contrôleur d’affichage et en ajustant les contraintes basée sur la valeur de la `VerticalSizeClass`. Par exemple :

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

### <a name="adding-transition-animations"></a>Ajout d’Animations de Transition

Lorsque le contrôleur d’affichage fractionné dans les Photos Adaptive application passe à partir de réduit à développée, animations sont ajoutées aux animations par défaut en remplaçant le `WillTransitionToTraitCollection` méthode du contrôleur d’affichage. Par exemple :

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

### <a name="overriding-the-trait-environment"></a>Substitution de l’environnement de Trait

Comme indiqué ci-dessus, l’application de Photos ADAPTATIF force le contrôleur d’affichage fractionné pour afficher les détails et les vues maîtres lors de l’appareil iPhone est en mode paysage.

Cela a été accompli en utilisant le code suivant dans le contrôleur d’affichage :

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

### <a name="expanding-and-collapsing-the-split-view-controller"></a>Développer et réduire le contrôleur d’affichage fractionné

Suivant nous allons examiner comment le comportement de réduction et de l’extension de contrôleur d’affichage fractionné a été implémenté dans Xamarin. Dans le `AppDelegate`, lorsque le contrôleur d’affichage fractionné est créé, son délégué est assigné pour gérer ces modifications :

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

Le `SeparateSecondaryViewController` méthode vérifie si une photo est actuellement affichée et prend des mesures en fonction de cet état. Si aucune photo n’est affiché il réduit le contrôleur d’affichage secondaire, afin que le contrôleur d’affichage principale s’affiche.

Le `CollapseSecondViewController` méthode est utilisée lors du développement du contrôleur d’affichage fractionné pour voir si les photos existent sur la pile, si par conséquent, elle est réduite à cette vue.

### <a name="moving-between-view-controllers"></a>Déplacement entre des contrôleurs d’affichage

Ensuite, examinons à présent comment l’application de Photos ADAPTATIF se déplace entre les contrôleurs d’affichage. Dans le `AAPLConversationViewController` classe lorsque l’utilisateur sélectionne une cellule de la table, la `ShowDetailViewController` méthode est appelée pour afficher la vue de détails :

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

Dans l’application Photo ADAPTATIF, il existe plusieurs endroits où la divulgation d’indicateurs sont affichés ou masqués sur les modifications apportées à l’environnement de caractéristique. Ceci est géré par le code suivant :

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

Celles-ci sont implémentées à l’aide de la `GetTargetViewControllerForAction` méthode abordée en détail ci-dessus.

Quand un contrôleur d’affichage Table affiche des données, il utilise les méthodes implémentées ci-dessus pour afficher ou non une notification push aura lieu et s’il faut afficher ou masquer l’indicateur de la divulgation en conséquence :

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

### <a name="new-showdetailtargetdidchangenotification-type"></a>Nouvelle `ShowDetailTargetDidChangeNotification` Type

Apple a ajouté un nouveau type de notification pour l’utilisation des Classes de taille et d’environnements caractéristique depuis un contrôleur d’affichage fractionné, `ShowDetailTargetDidChangeNotification`. Cette notification est envoyée chaque fois que la vue détaillée pour un contrôleur d’affichage fractionné cible change, par exemple lorsque le contrôleur développe ou réduit.

L’application de Photos adaptative utilise cette notification pour mettre à jour l’état de l’indicateur de divulgation lorsque le contrôleur d’affichage Détails change :

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

Examinons plus en détail l’application de Photos adaptatif pour voir toutes les méthodes que les Classes taille, caractéristique de Collections et des contrôleurs d’affichage ADAPTATIF peuvent servir à créer facilement une Application unifiée dans Xamarin.iOS.

## <a name="unified-storyboards"></a>Storyboards unifiés

Nouveau à iOS 8, les Storyboards unifiés permettent au développeur de créer un, unifiée fichier storyboard qui peut être affiché sur les appareils iPhone et iPad en ciblant plusieurs Classes de taille. À l’aide de Storyboards unifiés, le développeur écrit moins spécifiques de code de l’interface utilisateur et a la conception de l’interface qu’une seule pour créer et gérer.

Les principaux avantages de Storyboards unifiés sont :

-  Utiliser le même fichier de storyboard pour iPhone et iPad.
-  Déployer vers l’arrière à iOS 6 et iOS 7.
-  L’aperçu pour différents appareils, orientations et les versions à partir du système d’exploitation dans le Concepteur de Xamarin iOS.

Cette fonctionnalité est entièrement pris en charge dans Visual Studio pour Mac

<a name="enabling-size-classes" />

### <a name="enabling-size-classes"></a>L’activation des Classes de taille

Par défaut, tous les nouveaux projets Xamarin.iOS utilise les classes de taille. Pour utiliser les Classes de taille et les enchaînements adaptative à l’intérieur d’une table de montage séquentiel à partir d’un projet antérieur, il doit d’abord être converti au format à partir de Xcode 6 unifiée Storyboard à l’intérieur du concepteur iOS.

Pour effectuer cette ouvre le Storyboard à convertir dans le concepteur iOS et vérification de la **utiliser les Classes de taille** case à cocher :

 [![](unified-storyboards-images/sizeclass01.png "La case à cocher Utiliser les Classes de taille")](unified-storyboards-images/sizeclass01.png#lightbox)

Le concepteur iOS confirme que le développeur souhaite convertir le format du storyboard à utiliser les Classes de taille :

 [![](unified-storyboards-images/sizeclass02.png "L’alerte de catégories de taille de l’utilisation")](unified-storyboards-images/sizeclass02.png#lightbox)

> [!IMPORTANT]
> Disposition automatique doit également être activée pour les Classes de taille fonctionner correctement.

### <a name="generic-device-types"></a>Types d’appareils générique

Une fois que la table de montage séquentiel a été converti pour utiliser les Classes de taille, il s’affiche de nouveau dans l’aire de conception et le **afficher en tant que** appareil sera générique :

 [![](unified-storyboards-images/sizeclass03.png "Afficher sous la forme d’un type d’appareil générique")](unified-storyboards-images/sizeclass03.png#lightbox)

Lorsque le type d’appareil générique est sélectionné, tous les contrôleurs d’affichage doit être redimensionnées à un carré de 600 x 600. Ce carré représente la taille de toute la largeur et de n’importe quelle hauteur. Lorsque le concepteur iOS est dans ce mode, les modifications s’appliqueront à toutes les Classes de taille.

Le développeur a également la possibilité d’afficher l’aire de conception en tant qu’un iPhone :

 [![](unified-storyboards-images/sizeclass04.png "Affichage de l’aire de conception sous la forme d’un iPhone")](unified-storyboards-images/sizeclass04.png#lightbox)

Ou visualiser en tant qu’un iPad :

 [![](unified-storyboards-images/sizeclass05.png "Affichage de l’aire de conception sous la forme d’un iPad")](unified-storyboards-images/sizeclass05.png#lightbox)

### <a name="select-a-size-class"></a>Sélectionner une classe de taille

Le bouton de sélecteur de classe de taille est dans l’angle supérieur gauche de l’aire de conception (à côté de la liste déroulante Afficher en tant que). Il permet au développeur de sélectionner les catégories de taille sont actuellement en cours de modification :

 [![](unified-storyboards-images/sizeclass06.png "Sélectionner une classe de taille")](unified-storyboards-images/sizeclass06.png#lightbox)

Le sélecteur présente la sélection de catégorie de taille dans une grille de 3 x 3. Chacun des carrés dans la grille représente une combinaison d’une classe de la largeur et une classe de hauteur. Le carré central sélectionne la classe de n’importe quelle taille de hauteur Width/Any (qui est l’affichage par défaut pour une table de montage séquentiel unifiée). Lorsque ce carré est sélectionnée, le développeur modifie la disposition par défaut, qui est héritée par toutes les autres configurations.

Le carré dans le coin supérieur gauche de la grille représente la classe de taille de la largeur/Compact hauteur Compact :

 [![](unified-storyboards-images/sizeclass07.png "La classe de taille de la largeur/Compact Compact hauteur")](unified-storyboards-images/sizeclass07.png#lightbox)

Ce mode correspond à un iPhone dans l’orientation paysage. Le carré dans le coin inférieur droit de la grille représente la classe de taille du hauteur de la largeur/Regular régulière, qui représente un iPad :

 [![](unified-storyboards-images/sizeclass08.png "La classe de taille de hauteur régulière de la largeur/standard")](unified-storyboards-images/sizeclass08.png#lightbox)

Pour modifier la disposition d’un iPhone dans l’orientation portrait, sélectionnez le carré dans le coin inférieur gauche. Représente la classe de taille de hauteur de la largeur/Regular Compact :

 [![](unified-storyboards-images/sizeclass09.png "La catégorie de taille de hauteur de la largeur/Regular Compact")](unified-storyboards-images/sizeclass09.png#lightbox)

Cliquez dans le carré pour le sélectionner et l’aire de conception change la taille de contrôleurs d’affichage pour correspondre à la nouvelle sélection :

 [![](unified-storyboards-images/sizeclass10.png "L’aire de conception modifie la taille de contrôleurs d’affichage pour correspondre à la nouvelle sélection comme indiqué")](unified-storyboards-images/sizeclass10.png#lightbox)

Consultez la section de la catégorie de taille de cet article pour plus d’informations sur les Classes de taille et leur impact sur la disposition pour iPhone et iPad.

### <a name="adaptive-segue-types"></a>Types de Segue ADAPTATIF

Si le développeur a utilisé des storyboards avant, ils seront familiarisés avec les types de segue existants de **Push**, **modale** et **menu**. Lorsque les Classes de taille sont activés sur un fichier de Storyboard unifiée, Adaptive Segue suivants (qui correspondent à la nouvelle API de contrôleur de vue décrits ci-dessus) sont accessibles : **Afficher** et **afficher les détails**.

> [!IMPORTANT]
> Lorsque les Classes de taille sont activées, existants Enchaînements va être convertis en types de nouveau.

Prenons l’exemple d’une e/s 8 Application qui utilise un Storyboard unifiée avec un contrôleur d’affichage fractionné qui comporte un menu de navigation du jeu simple dans la vue principale. Si l’utilisateur clique sur un bouton de menu, contrôleur d’affichage de l’élément sélectionné doit figurer dans la section Détails du contrôleur d’affichage fractionné lors de l’exécution sur un iPad. Sur un iPhone contrôleur d’affichage de l’élément doit être placé sur la pile de Navigation.

Pour obtenir cet effet, dans le concepteur iOS contrôle-cliquez sur le bouton, puis faites glisser une ligne pour le contrôleur d’affichage à afficher. Lorsque le bouton de la souris est relâché, sélectionnez `Show Detail` dans le menu contextuel de Type Segue :

 [![](unified-storyboards-images/segue01.png "Sélectionnez Afficher les détails dans le menu contextuel de Type Segue")](unified-storyboards-images/segue01.png#lightbox)

Le segue nouveau est créé entre le bouton et le contrôleur d’affichage. Exécuter l’application dans le simulateur iPhone et le Menu principal s’affichera :

 [![](unified-storyboards-images/segue02.png "Le Menu principal")](unified-storyboards-images/segue02.png#lightbox)

Cliquez sur le **jeu sélectionnez** seront poussée de bouton et le contrôleur d’affichage de l’élément dans la pile de Navigation :

 [![](unified-storyboards-images/segue03.png "Les éléments contrôleur d’affichage placés de la pile de Navigation comme indiqué")](unified-storyboards-images/segue03.png#lightbox)

Arrêter le simulateur iPhone et exécuter l’Application dans le simulateur iPad. Basculez vers l’orientation paysage et la main menu s’affiche à nouveau :

 [![](unified-storyboards-images/segue04.png "Le menu principal s’affiché")](unified-storyboards-images/segue04.png#lightbox)

Là encore, cliquez sur le **jeu sélectionnez** bouton et le contrôleur d’affichage de l’élément est indiqué dans la section Détails du contrôleur d’affichage fractionné :

 [![](unified-storyboards-images/segue05.png "Les éléments contrôleur d’affichage indiqué dans la section Détails du contrôleur d’affichage fractionné")](unified-storyboards-images/segue05.png#lightbox)

### <a name="excluding-an-element-from-a-size-class"></a>À l’exclusion d’un élément à partir d’une classe de taille

Il est parfois quand un élément donné (par exemple, une vue, contrôle ou une contrainte) n’est pas nécessaire à l’intérieur d’une classe de taille spécifique. Pour exclure un élément d’une classe de taille, sélectionnez l’élément souhaité à exclure de la **aire de conception**. Faites défiler vers le bas de la **Explorateur de propriétés** et cliquez sur le **ENGRENAGE** menu déroulant. Sélectionnez la combinaison de **largeur** et **hauteur** à exclure de l’élément à partir de :

[![](unified-storyboards-images/exclude-a.png "Sélectionnez la combinaison de la largeur et la hauteur")](unified-storyboards-images/exclude-a.png#lightbox)

Un nouveau *les cas d’Exclusion* sera ajouté à l’élément dans la partie inférieure de la **Explorateur de propriétés**. Ensuite, désactivez le **installé** case à cocher pour la classe de taille donnée :

[![](unified-storyboards-images/exclude-b.png "Décochez la case à cocher installé")](unified-storyboards-images/exclude-b.png#lightbox)

Commutateur de l’aire de conception à la largeur et la hauteur de l’élément a été exclu, il a été supprimé de la classe de taille donnée, mais pas la conception de l’interface utilisateur complète :

 [![](unified-storyboards-images/exclude02.png "Commutateur à la largeur et la hauteur de l’élément a été exclu de l’aire de conception")](unified-storyboards-images/exclude02.png#lightbox)

Basculer vers la classe de taille Any Width/Any hauteur et l’élément est toujours en place :

 [![](unified-storyboards-images/exclude03.png "Basculer vers la classe de taille Any Width/Any hauteur")](unified-storyboards-images/exclude03.png#lightbox)

Lorsque l’application est exécutée dans l’iPad simulateur, l’élément s’affiche :

 [![](unified-storyboards-images/exclude04.png "L’élément indiquée lorsque l’application en cours d’exécution dans le simulateur iPad")](unified-storyboards-images/exclude04.png#lightbox)

Et lorsque l’application est exécutée sur le simulateur iPhone, l’élément est manquant :

 [![](unified-storyboards-images/exclude05.png "L’élément manquant quand l’application en cours d’exécution dans le simulateur iPhone")](unified-storyboards-images/exclude05.png#lightbox)

Pour supprimer un cas d’Exclusion d’un élément, il suffit de sélectionner l’élément dans le **aire de conception**, faites défiler vers le bas de la **Explorateur de propriétés** et cliquez sur le **-** bouton en regard de l’incident à supprimer.

Pour voir une implémentation de Storyboards unifiés, examinez le `UnifiedStoryboard` exemple d’application iOS 8 de Xamarin attachée à ce document.

## <a name="dynamic-launch-screens"></a>Écrans de lancement dynamique

Le fichier d’écran de démarrage s’affiche comme écran de démarrage pendant le lance d’une application iOS pour fournir des commentaires à l’utilisateur que l’application est en fait en commençant à distance. Avant d’iOS 8, le développeur devra inclure plusieurs `Default.png` actifs pour chaque résolution de type, l’orientation et écran de l’appareil qui s’exécuterait sur l’application de l’image. Par exemple, `Default@2x.png`, `Default-Landscape@2x~ipad.png`, `Default-Portrait@2x~ipad.png`, etc.

En tenant compte de la nouvelle iPhone 6 et iPhone 6 Plus les appareils (et l’Apple Watch à venir) avec tous les iPhone existant et les appareils iPad, cela représente un grand tableau de tailles différentes, les orientations et les résolutions de `Default.png` des ressources d’image d’écran démarrage qui doivent Création et la maintenance. En outre, ces fichiers peuvent être très volumineux et seront « encombrements » le bundle d’applications livrable, augmenter la quantité de temps nécessaire pour télécharger l’application à partir de l’iTunes Store d’application (éventuellement mettez-le régulièrement de pouvoir être remis via un réseau cellulaire) et en augmentant la quantité de stockage disponible requis sur l’appareil de l’utilisateur final.

Nouveau à iOS 8, le développeur peut créer un seul, atomique `.xib` fichier dans Xcode qui utilise des Classes de taille et de disposition automatique pour créer un *écran de lancement dynamique* qui fonctionnera pour chaque appareil, la résolution et l’orientation. Cela réduit non seulement la quantité de travail nécessaire au développeur de créer et gérer toutes les ressources d’image obligatoires, mais elle réduit considérablement la taille de lot installé l’application.


Les écrans de lancement dynamique ont les considérations et limitations suivantes :

- Utilisez uniquement `UIKit` classes.
- Utiliser une vue de racine unique qui est un `UIView` ou `UIViewController` objet.
- N’effectuez pas toutes les connexions au code de l’application (n’ajoutez pas **Actions** ou **Outlets**).
- N’ajoutez pas `UIWebView` objets.
- N’utilisez pas les classes personnalisées.
- N’utilisez pas les attributs de runtime.

Avec les instructions ci-dessus à l’esprit, nous allons étudier l’ajout d’un écran de lancement dynamique à un projet d’iOS 8 Xamarin existant.

Effectuez ce qui suit :

1. Ouvrez **Visual Studio pour Mac** et charger le **Solution** pour ajouter l’écran de lancement dynamique à.
2. Dans le **l’Explorateur de solutions**, avec le bouton droit le `MainStoryboard.storyboard` fichier et sélectionnez **ouvrir avec** > **Xcode Interface Builder**:

    [![](unified-storyboards-images/dls01.png "Ouvrir avec Xcode Interface Builder")](unified-storyboards-images/dls01.png#lightbox)
3. Dans Xcode, sélectionnez **fichier** > **New** > **fichier...** :

    [![](unified-storyboards-images/dls02.png "Sélectionnez le fichier / nouveau")](unified-storyboards-images/dls02.png#lightbox)
4. Sélectionnez **iOS** > **Interface utilisateur** > **écran de lancement** et cliquez sur le **suivant** bouton :

    [![](unified-storyboards-images/dls03.png "Sélectionnez iOS / Interface utilisateur / écran de lancement")](unified-storyboards-images/dls03.png#lightbox)
5. Nommez le fichier `LaunchScreen.xib` et cliquez sur le **créer** bouton :

    [![](unified-storyboards-images/dls04.png "Nommez le fichier LaunchScreen.xib")](unified-storyboards-images/dls04.png#lightbox)
6. Modifier la conception de l’écran de démarrage en ajoutant des éléments graphiques et à l’aide des contraintes de disposition pour positionner les appareils donnés, orientations et tailles d’écran :

    [![](unified-storyboards-images/dls05.png "Modification de la conception de l’écran de lancement")](unified-storyboards-images/dls05.png#lightbox)
7. Enregistrez les modifications apportées à `LaunchScreen.xib`.
8. Sélectionnez le **Applications cible** et **général** onglet :

    [![](unified-storyboards-images/dls06.png "Sélectionnez la cible des Applications et l’onglet Général")](unified-storyboards-images/dls06.png#lightbox)
9. Cliquez sur le **Info.plist choisissez** bouton, sélectionnez le `Info.plist` pour l’application Xamarin et cliquez sur le **choisir** bouton :

    [![](unified-storyboards-images/dls07.png "Sélectionnez le fichier Info.plist pour l’application Xamarin")](unified-storyboards-images/dls07.png#lightbox)
10. Dans le **icônes d’application et les Images de lancement** section, ouvre le **fichier de l’écran de lancement** liste déroulante et choisissez le `LaunchScreen.xib` créé ci-dessus :

    [![](unified-storyboards-images/dls08.png "Choisissez le LaunchScreen.xib")](unified-storyboards-images/dls08.png#lightbox)
11. Enregistrez les modifications dans le fichier et revenir à Visual Studio pour Mac.
12. Attendez que Visual Studio pour Mac terminer la synchronisation des modifications avec Xcode.
13. Dans le **l’Explorateur de solutions**, avec le bouton droit sur le **ressource** dossier et sélectionnez **ajouter** > **ajouter des fichiers...** :

    [![](unified-storyboards-images/dls09.png "Sélectionnez Ajouter / ajoute des fichiers...")](unified-storyboards-images/dls09.png#lightbox)
14. Sélectionnez le `LaunchScreen.xib` fichier créé ci-dessus et cliquez sur le **Open** bouton :

    [![](unified-storyboards-images/dls10.png "Sélectionnez le fichier LaunchScreen.xib")](unified-storyboards-images/dls10.png#lightbox)
15. Générez l'application.

### <a name="testing-the-dynamic-launch-screen"></a>Test de l’écran de démarrage dynamique

Dans Visual Studio pour Mac, sélectionnez le simulateur de rétine iPhone 4 et exécuter l’application. L’écran de lancement dynamique s’affiche dans le format approprié et l’orientation :

[![](unified-storyboards-images/dls11.png "L’écran de lancement dynamique affiché dans l’orientation verticale")](unified-storyboards-images/dls11.png#lightbox)

Arrêter l’application dans Visual Studio pour Mac et sélectionnez un appareil iOS 8 d’iPad. Exécutez l’application et l’écran de démarrage sera correctement formaté pour cet appareil et l’orientation :

[![](unified-storyboards-images/dls12.png "L’écran de lancement dynamique affiché dans l’orientation horizontale")](unified-storyboards-images/dls12.png#lightbox)

Retournez dans Visual Studio pour Mac et arrêter l’application de s’exécuter.

### <a name="working-with-ios-7"></a>Travailler avec iOS 7

Pour assurer la compatibilité descendante avec iOS 7, d’inclure simplement la habituelles `Default.png` actifs comme d’habitude dans l’application iOS 8 de l’image. iOS sera retourner le comportement précédent et utiliser ces fichiers en tant que l’écran de démarrage lors de l’exécution sur un appareil iOS 7.

Pour voir une implémentation d’un écran de lancement dynamique dans Xamarin, examinez le [écrans de lancement dynamique](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/) exemple d’application iOS 8 jointe à ce document.

## <a name="summary"></a>Récapitulatif

Cet article a eu un aperçu des Classes de taille et leur impact sur la disposition des appareils iPhone et iPad. Elle a expliqué comment les caractéristiques, des environnements de Trait et des Collections de caractéristique fonctionnent avec les Classes de taille pour créer des Interfaces unifiée. Il a fallu bref aperçu des contrôleurs d’affichage adaptatif et leur fonctionnement avec les Classes de taille à l’intérieur d’Interfaces unifiée. Il recherche sur l’implémentation de Classes de taille et unifiée des Interfaces intégralement à partir C# code à l’intérieur d’une application Xamarin iOS 8.

Enfin, cet article a abordé les principes fondamentaux de la création des Storyboards unifiés avec le Kit de développement Xamarin iOS concepteur qui fonctionne sur des appareils iOS et création d’un seul écran de lancement dynamique qui s’affichera sous forme de l’écran de démarrage sur tous les appareils iOS 8.

## <a name="related-links"></a>Liens associés

- [Photos adaptative (exemple)](https://developer.xamarin.com/samples/monotouch/ios8/AdaptivePhotos/)
- [Exemple de StoryboardIntro](https://developer.xamarin.com/samples/monotouch/StoryboardIntro/)
- [Écrans de lancement dynamique (exemple)](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)
- [Introduction à iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Dispositions dynamiques dans iOS8 - 2014 évoluer (vidéo)](http://youtu.be/f3mMGlS-lM4)
- [UIPresentationController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPresentationController_class/)
- [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset)
