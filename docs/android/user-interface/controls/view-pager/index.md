---
title: ViewPager
description: ViewPager est un gestionnaire de disposition qui vous permet d’implémenter la navigation Gestural. La navigation Gestural permet à l’utilisateur de balayer vers la gauche et vers la droite pour parcourir les pages de données. Ce guide explique comment implémenter la navigation Gestural avec ViewPager, avec et sans fragments. Elle explique également comment ajouter des indicateurs de page à l’aide de PagerTitleStrip et PagerTabStrip.
ms.prod: xamarin
ms.assetid: D42896C0-DE7C-4818-B171-CB2D5E5DD46A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 8c7aae672d284d4105c6b09dfe81b72cf4ad519b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645512"
---
# <a name="viewpager"></a>ViewPager

_ViewPager est un gestionnaire de disposition qui vous permet d’implémenter la navigation Gestural. La navigation Gestural permet à l’utilisateur de balayer vers la gauche et vers la droite pour parcourir les pages de données. Ce guide explique comment implémenter la navigation Gestural avec ViewPager, avec et sans fragments. Elle explique également comment ajouter des indicateurs de page à l’aide de PagerTitleStrip et PagerTabStrip._

 
## <a name="overview"></a>Présentation

Un scénario courant dans le développement d’applications est la nécessité de fournir aux utilisateurs une navigation Gestural entre les vues frères. Dans cette approche, l’utilisateur glisse vers la gauche ou vers la droite pour accéder aux pages de contenu (par exemple, dans un assistant d’installation ou un diaporama). Vous pouvez créer ces vues balayées à l' `ViewPager` aide du widget, disponible dans la [bibliothèque de prise en charge Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Le `ViewPager` est un widget de disposition constitué de plusieurs vues enfants où chaque vue enfant constitue une page de la disposition: 

[![Captures d’écran de l’application TreePager avec un exemple de balayage horizontal](images/01-intro-sml.png)](images/01-intro.png#lightbox)

En général, `ViewPager` est utilisé conjointement avec les [fragments](~/android/platform/fragments/index.md); Toutefois, il existe des situations où vous pouvez souhaiter utiliser `ViewPager` sans la complexité supplémentaire de `Fragment`s.

`ViewPager`utilise un modèle d’adaptateur pour lui fournir les vues à afficher. L’adaptateur utilisé ici est conceptuellement similaire à celui utilisé par [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) &ndash; vous fournissez une implémentation `PagerAdapter` de pour générer les pages que `ViewPager` le affiche à l’utilisateur. Les pages affichées par `ViewPager` peuvent être `View`s ou `Fragment`s. Quand `View`les s sont affichés, l’adaptateur sous-classe la `PagerAdapter` classe de base d’Android. Si `Fragment`des s sont affichés, l’adaptateur sous-classe les `FragmentPagerAdapter`Android. La bibliothèque de prise en charge `FragmentPagerAdapter` Android comprend également (une `PagerAdapter`sous-classe de) pour vous aider `Fragment`à obtenir des informations détaillées sur la connexion des s aux données. 

Ce guide présente les deux approches: 

-   Dans [Viewpager avec views](~/android/user-interface/controls/view-pager/viewpager-and-views.md), une application [TreePager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-treepager) est développée pour montrer comment utiliser `ViewPager` pour afficher les vues d’un catalogue d’arborescence (une galerie d’images d’arbres à feuilles de feuilles et de feuilles de route). 
    `PagerTabStrip`et `PagerTitleStrip` sont utilisés pour afficher des titres qui facilitent la navigation entre les pages.

-   Dans [Viewpager avec des fragments](~/android/user-interface/controls/view-pager/viewpager-and-fragments.md), une application [FlashCardPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-treepager) légèrement plus complexe est développée pour montrer comment utiliser `ViewPager` avec `Fragment`pour créer une application qui présente des problèmes mathématiques sous forme de cartes Flash et répond aux entrées d’utilisateur. 


## <a name="requirements"></a>Configuration requise

Pour utiliser `ViewPager` dans votre projet d’application, vous devez installer le package v4 de la [bibliothèque de prise en charge Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) . Pour plus d’informations sur l’installation des packages [NuGet, consultez Procédure pas à pas: Y compris un NuGet dans votre](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)projet. 

 
## <a name="architecture"></a>Architecture

Trois composants sont utilisés pour implémenter la navigation `ViewPager`Gestural avec:

-   ViewPager
-   Adaptateur
-   Indicateur de radiomessagerie

Chacun de ces composants est résumé ci-dessous.



### <a name="viewpager"></a>ViewPager

`ViewPager`est un gestionnaire de disposition qui affiche une collection `View`d’un à la fois. Son travail consiste à détecter le mouvement de balayage de l’utilisateur et à accéder à l’affichage suivant ou précédent, le cas échéant. Par exemple, la capture d’écran ci `ViewPager` -dessous illustre une transition d’une image à la suivante en réponse à un mouvement utilisateur: 

[![Gros plan de l’application TreePager affichant une transition entre les vues](images/02-transition-sml.png)](images/02-transition.png#lightbox)


### <a name="adapter"></a>Adaptateur

`ViewPager`extrait ses données d’un *adaptateur*. La tâche de l’adaptateur consiste à créer `View`les s affichés par `ViewPager`le, en les fournissant en fonction des besoins. Le diagramme ci-dessous illustre ce &ndash; concept que l’adaptateur crée et `View`remplit les `ViewPager`et les fournit au. Étant donné que `View` détectelesgestesdebalayagedel’utilisateur,ildemandeàl’adaptateurdefournirle`ViewPager` approprié pour afficher: 

[![Diagramme illustrant la manière dont l’adaptateur connecte les images et les noms à ViewPager](images/03-adapter-sml.png)](images/03-adapter.png#lightbox)

Dans cet exemple, chaque `View` est construit à partir d’une image d’arborescence et d’un nom d’arborescence avant d’être passé à l `ViewPager`'. 



### <a name="pager-indicator"></a>Indicateur de radiomessagerie

`ViewPager`peut être utilisé pour afficher un grand jeu de données (par exemple, une galerie d’images peut contenir des centaines d’images). Pour aider l’utilisateur à naviguer dans de grands `ViewPager` jeux de données, est souvent accompagné d’un *indicateur* de radiomessagerie qui affiche une chaîne. La chaîne peut être le titre de l’image, une légende ou simplement la position actuelle de l’affichage dans le jeu de données. 

Il existe deux vues qui peuvent produire ces informations de navigation pour vous `PagerTabStrip` : `PagerTitleStrip.` et chacune affiche une chaîne en haut d’un `ViewPager`, et chacune extrait ses données de l' `ViewPager`adaptateur de pour qu’elles restent toujours synchronisées avec le actuellement affiché `View`. La différence réside dans le fait `PagerTabStrip` que comprend un indicateur visuel pour la chaîne «actuelle» `PagerTitleStrip` alors que ne le fait pas (comme indiqué dans ces captures d’écran): 

[![Captures d’écran de l’application TreePager avec PagerTitleStrip et PagerTabStrip](images/04-comparison-sml.png)](images/04-comparison.png#lightbox)

Ce guide montre comment effectuer des `ViewPager`immplement, des adaptateurs et des composants d’application indicateur et les intégrer pour prendre en charge la navigation Gestural. 



## <a name="related-links"></a>Liens associés

- [TreePager (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-treepager)
- [FlashCardPager (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager)
