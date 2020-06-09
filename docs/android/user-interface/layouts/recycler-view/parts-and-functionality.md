---
title: Composants et fonctionnalités RecyclerView
description: Vue d’ensemble du gestionnaire de disposition, de l’adaptateur et du détenteur de l’affichage RecyclerView.
ms.prod: xamarin
ms.assetid: 54F999BE-2732-4BC7-A466-D17373961C48
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/13/2018
ms.openlocfilehash: 8d987fc91544cfb87a2da6adba9f178931fee563
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84567796"
---
# <a name="recyclerview-parts-and-functionality"></a>Composants et fonctionnalités RecyclerView

`RecyclerView`gère certaines tâches en interne (telles que le défilement et le recyclage des vues), mais il s’agit essentiellement d’un gestionnaire qui coordonne les classes d’assistance pour afficher une collection. `RecyclerView`délègue des tâches aux classes d’assistance suivantes :

- **`Adapter`**&ndash;Déflate les dispositions des éléments (instancie le contenu d’un fichier de disposition) et lie les données aux vues affichées dans un `RecyclerView` . L’adaptateur signale également les événements de clic d’élément.

- **`LayoutManager`**&ndash;Mesure et positionne des vues d’élément dans un `RecyclerView` et gère la stratégie pour le recyclage de vue.

- **`ViewHolder`**&ndash;Recherche et stocke les références de vue. Le détenteur de la vue facilite également la détection des clics sur les éléments.

- **`ItemDecoration`**&ndash;Permet à une application d’ajouter des décalages de dessin et de disposition spéciaux à des affichages spécifiques pour dessiner des séparateurs entre les éléments, les mises en évidence et les limites de regroupement visuel.

- **`ItemAnimator`**&ndash;Définit les animations qui ont lieu pendant les actions d’élément ou à mesure que des modifications sont apportées à l’adaptateur.

La relation entre les `RecyclerView` `LayoutManager` classes, et `Adapter` est illustrée dans le diagramme suivant :

![Diagramme de RecyclerView contenant LayoutManager, utilisant l’adaptateur pour accéder au jeu de données](parts-and-functionality-images/01-recyclerview-diagram.png)

Comme le montre cette illustration, le `LayoutManager` peut être considéré comme l’intermédiaire entre le `Adapter` et le `RecyclerView` . Le `LayoutManager` effectue des appels dans les `Adapter` méthodes pour le compte du `RecyclerView` . Par exemple, le `LayoutManager` appelle une `Adapter` méthode lorsqu’il est temps de créer un nouvel affichage pour une position d’élément particulière dans le `RecyclerView` . Le `Adapter` déflate la disposition de cet élément et crée une `ViewHolder` instance (non illustrée) pour mettre en cache les références aux vues à cette position. Lorsque le `LayoutManager` appelle `Adapter` pour lier un élément particulier au jeu de données, le `Adapter` localise les données de cet élément, les récupère dans le jeu de données et les copie dans la vue d’élément associée.

Lors de l’utilisation `RecyclerView` de dans votre application, il est nécessaire de créer des types dérivés des classes suivantes :

- **`RecyclerView.Adapter`**&ndash;Fournit une liaison du jeu de données de votre application (qui est spécifique à votre application) aux vues d’éléments qui sont affichées dans le `RecyclerView` . L’adaptateur sait comment associer chaque position d’affichage d’élément dans le `RecyclerView` à un emplacement spécifique dans la source de données. En outre, l’adaptateur gère la disposition du contenu dans chaque vue d’élément individuel et crée le détenteur de la vue pour chaque vue. L’adaptateur signale également les événements de clic d’élément qui sont détectés par l’affichage de l’élément.

- **`RecyclerView.ViewHolder`**&ndash;Met en cache les références aux vues dans votre fichier de disposition d’élément afin que les recherches de ressources ne soient pas répétées inutilement. Le détenteur de la vue organise également les événements de clic d’élément à transmettre à l’adaptateur lorsqu’un utilisateur appuie sur l’affichage d’élément associé au titulaire de l’affichage.

- **`RecyclerView.LayoutManager`**&ndash;Place des éléments dans le `RecyclerView` . Vous pouvez utiliser un ou plusieurs gestionnaires de disposition prédéfinis, ou vous pouvez implémenter votre propre gestionnaire de disposition personnalisé.
    `RecyclerView`délègue la stratégie de disposition au gestionnaire de disposition, afin que vous puissiez brancher un autre gestionnaire de disposition sans avoir à apporter de modifications significatives à votre application.

En outre, vous pouvez éventuellement étendre les classes suivantes pour modifier l’apparence de `RecyclerView` dans votre application :

- **`RecyclerView.ItemDecoration`**
- **`RecyclerView.ItemAnimator`**

Si vous n’étendez pas `ItemDecoration` et `ItemAnimator` , utilise des `RecyclerView` implémentations par défaut. Ce guide n’explique pas comment créer des classes et personnalisées. `ItemDecoration` `ItemAnimator` pour plus d’informations sur ces classes, consultez [RecyclerView. ItemDecoration](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemDecoration.html) et [RecyclerView. ItemAnimator](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemAnimator.html).

<a name="recycling"></a>

## <a name="how-view-recycling-works"></a>Fonctionnement de l’affichage du recyclage

`RecyclerView`n’alloue pas d’affichage d’élément pour chaque élément de votre source de données. Au lieu de cela, il alloue uniquement le nombre de vues d’éléments qui s’ajustent à l’écran et réutilise ces dispositions d’éléments à mesure que l’utilisateur fait défiler. Lorsque la vue défile tout d’abord, elle passe par le processus de recyclage illustré dans la figure suivante :

[![Diagramme illustrant les six étapes de l’affichage du recyclage](parts-and-functionality-images/02-view-recycling-sml.png)](parts-and-functionality-images/02-view-recycling.png#lightbox)

1. Lorsqu’une vue fait défiler la vue et n’est plus affichée, elle devient une *vue de perte*.

2. La vue Scrap est placée dans un pool et devient une *vue de recyclage*.
    Ce pool est un cache de vues qui affichent le même type de données.

3. Lorsqu’un nouvel élément doit être affiché, une vue est extraite du pool de recyclage pour être réutilisée. Étant donné que cette vue doit être reliée par l’adaptateur avant d’être affichée, elle s’appelle une *vue incorrecte*.

4. La vue incorrecte est recyclée : l’adaptateur localise les données pour l’élément suivant à afficher et copie ces données dans les vues de cet élément. Les références de ces vues sont extraites du détenteur de la vue associée à la vue recyclée.

5. La vue recyclée est ajoutée à la liste des éléments du qui sont sur le point de s’afficher à l' `RecyclerView` écran.

6. La vue recyclée s’affiche à l’écran lorsque l’utilisateur fait défiler `RecyclerView` vers l’élément suivant de la liste. Pendant ce temps, une autre vue défile et est recyclée en fonction des étapes ci-dessus.

En plus de la réutilisation de l’affichage des éléments, `RecyclerView` utilise également une autre optimisation de l’efficacité : afficher les détenteurs. Un *détenteur de vue* est une classe simple qui met en cache les références de vue. Chaque fois que l’adaptateur déflate un fichier de disposition d’élément, il crée également un détenteur de vue correspondant. Le détenteur de la vue utilise `FindViewById` pour obtenir des références aux vues à l’intérieur du fichier de disposition des éléments gonflé. Ces références sont utilisées pour charger les nouvelles données dans les vues chaque fois que la disposition est recyclée pour afficher les nouvelles données.

## <a name="the-layout-manager"></a>Gestionnaire de disposition

Le gestionnaire de disposition est chargé de positionner les éléments dans l' `RecyclerView` affichage ; il détermine le type de présentation (une liste ou une grille), l’orientation (si les éléments sont affichés verticalement ou horizontalement) et les éléments de direction à afficher (dans l’ordre normal ou inverse). Le gestionnaire de disposition est également responsable du calcul de la taille et de la position de chaque élément dans l’affichage **RecycleView** .

Le gestionnaire de disposition a un objectif supplémentaire : il détermine la stratégie de recyclage des vues d’éléments qui ne sont plus visibles pour l’utilisateur.
Étant donné que le gestionnaire de présentation reconnaît les vues visibles (et celles qui ne le sont pas), il est dans la meilleure position de décider quand une vue peut être recyclée. Pour recycler une vue, le gestionnaire de disposition effectue généralement des appels à l’adaptateur pour remplacer le contenu d’une vue recyclée par des données différentes, comme décrit précédemment dans [How View Recycling Works](#recycling).

Vous pouvez étendre `RecyclerView.LayoutManager` pour créer votre propre gestionnaire de dispositions, ou vous pouvez utiliser un gestionnaire de présentation prédéfini. `RecyclerView`fournit les gestionnaires de disposition prédéfinis suivants :

- **`LinearLayoutManager`**&ndash;Organise les éléments dans une colonne qui peut faire l’objet d’un défilement vertical ou dans une ligne qui peut faire l’objet d’un défilement horizontal.

- **`GridLayoutManager`**&ndash;Affiche des éléments dans une grille.

- **`StaggeredGridLayoutManager`**&ndash;Affiche des éléments dans une grille décalée, où certains éléments ont des hauteurs et des largeurs différentes.

Pour spécifier le gestionnaire de disposition, instanciez le gestionnaire de présentation que vous avez choisi et transmettez-le à la `SetLayoutManager` méthode. Notez que vous *devez* spécifier le gestionnaire de présentation &ndash; `RecyclerView` ne sélectionne pas un gestionnaire de présentation prédéfini par défaut.

Pour plus d’informations sur le gestionnaire de disposition, consultez la référence de la [classe RecyclerView. LayoutManager](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.LayoutManager.html).

## <a name="the-view-holder"></a>Le détenteur de la vue

Le détenteur de la vue est une classe que vous définissez pour la mise en cache des références de vue. L’adaptateur utilise ces références de vue pour lier chaque vue à son contenu. Chaque élément du `RecyclerView` a une instance de conteneur de vue associée qui met en cache les références de vue pour cet élément. Pour créer un détenteur de vue, procédez comme suit pour définir une classe qui contiendra le jeu de vues exact par élément :

1. Sous-classe `RecyclerView.ViewHolder` .
2. Implémentez un constructeur qui recherche et stocke les références de vue.
3. Implémentez les propriétés que l’adaptateur peut utiliser pour accéder à ces références.

Un exemple détaillé d' `ViewHolder` implémentation est présenté dans [un exemple de base RecyclerView](~/android/user-interface/layouts/recycler-view/recyclerview-example.md).
Pour plus d’informations sur `RecyclerView.ViewHolder` , consultez la référence de la [classe RecyclerView. ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).

## <a name="the-adapter"></a>L’adaptateur

La majeure partie du « gros-levage » du `RecyclerView` code d’intégration a lieu dans l’adaptateur. `RecyclerView`requiert que vous fournissiez un adaptateur dérivé de `RecyclerView.Adapter` pour accéder à votre source de données et remplir chaque élément avec le contenu de la source de données.
Étant donné que la source de données est spécifique à l’application, vous devez implémenter les fonctionnalités de l’adaptateur qui comprennent comment accéder à vos données. L’adaptateur extrait des informations de la source de données et les charge dans chaque élément de la `RecyclerView` collection.

Le dessin suivant illustre la façon dont l’adaptateur mappe le contenu d’une source de données par le biais des conteneurs de vue à des vues individuelles au sein de chaque élément de ligne dans le `RecyclerView` :

[![Diagramme illustrant l’adaptateur reliant la source de données à ViewHolders](parts-and-functionality-images/03-recyclerviewer-adapter-sml.png)](parts-and-functionality-images/03-recyclerviewer-adapter.png#lightbox)

L’adaptateur charge chaque `RecyclerView` ligne avec des données pour un élément de ligne particulier. Pour la position de ligne *p*, par exemple, l’adaptateur localise les données associées à la position *p* dans la source de données et copie ces données vers l’élément de ligne à la position *p* dans la `RecyclerView` collection.
Dans le dessin ci-dessus, par exemple, l’adaptateur utilise le détenteur de la vue pour rechercher les références du `ImageView` et `TextView` à cette position afin qu’il n’ait pas à appeler à plusieurs reprises `FindViewById` pour ces vues lorsque l’utilisateur fait défiler la collection et réutilise les vues.

Lorsque vous implémentez un adaptateur, vous devez substituer les `RecyclerView.Adapter` méthodes suivantes :

- **`OnCreateViewHolder`**&ndash;Instancie le fichier de disposition de l’élément et le détenteur de l’affichage.

- **`OnBindViewHolder`**&ndash;Charge les données à la position spécifiée dans les vues dont les références sont stockées dans le détenteur de vue donné.

- **`ItemCount`**&ndash;Retourne le nombre d’éléments dans la source de données.

Le gestionnaire de disposition appelle ces méthodes pendant qu’il positionne des éléments dans le `RecyclerView` .

## <a name="notifying-recyclerview-of-data-changes"></a>Notification de la RecyclerView des modifications de données

`RecyclerView`ne met pas automatiquement à jour son affichage lorsque le contenu de sa source de données change ; l’adaptateur doit avertir `RecyclerView` en cas de modification dans le jeu de données. Le jeu de données peut changer de nombreuses façons ; par exemple, le contenu d’un élément peut changer ou la structure globale des données peut être modifiée.
`RecyclerView.Adapter`fournit un certain nombre de méthodes que vous pouvez appeler pour `RecyclerView` répondre aux modifications de données de la manière la plus efficace :

- **`NotifyItemChanged`**&ndash;Signale que l’élément à la position spécifiée a changé.

- **`NotifyItemRangeChanged`**&ndash;Signale que les éléments de la plage de positions spécifiée ont été modifiés.

- **`NotifyItemInserted`**&ndash;Signale que l’élément de la position spécifiée a été récemment inséré.

- **`NotifyItemRangeInserted`**&ndash;Signale que les éléments de la plage de positions spécifiée ont été récemment insérés.

- **`NotifyItemRemoved`**&ndash;Signale que l’élément de la position spécifiée a été supprimé.

- **`NotifyItemRangeRemoved`**&ndash;Signale que les éléments de la plage de positions spécifiée ont été supprimés.

- **`NotifyDataSetChanged`**&ndash;Signale que le jeu de données a changé (force une mise à jour complète).

Si vous savez exactement comment votre jeu de données a changé, vous pouvez appeler les méthodes appropriées ci-dessus pour actualiser `RecyclerView` de façon optimale. Si vous ne savez pas exactement comment votre jeu de données a changé, vous pouvez appeler `NotifyDataSetChanged` , ce qui est beaucoup moins efficace, car `RecyclerView` doit actualiser toutes les vues qui sont visibles par l’utilisateur. Pour plus d’informations sur ces méthodes, consultez [RecyclerView. adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).

Dans la rubrique suivante, [un](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)exemple de RecyclerView de base, un exemple d’application est implémenté pour illustrer des exemples de code réels des parties et des fonctionnalités décrites ci-dessus.

## <a name="related-links"></a>Liens connexes

- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Exemple de RecyclerView de base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [Extension de l’exemple RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
