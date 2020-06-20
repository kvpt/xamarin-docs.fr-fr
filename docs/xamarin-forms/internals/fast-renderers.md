---
title: Xamarin.FormsConvertisseurs rapides
description: Cet article présente les convertisseurs rapides, qui réduisent les coûts d’inflation et de rendu d’un Xamarin.Forms contrôle sur Android en aplatint la hiérarchie des contrôles natifs résultante.
ms.prod: xamarin
ms.assetid: 097f87f2-d891-4f3c-be02-fb7d195a481a
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 29f79e4aed0314fe1590fa26c8e4b052e14a94d6
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84198062"
---
# <a name="xamarinforms-fast-renderers"></a>Xamarin.FormsConvertisseurs rapides

Traditionnellement, la plupart des convertisseurs de contrôle d’origine sur Android sont composés de deux vues :

- Contrôle natif, tel que `Button` ou `TextView` .
- Conteneur `ViewGroup` qui gère une partie du travail de disposition, la gestion des mouvements et d’autres tâches.

Toutefois, cette approche a une implication sur les performances dans le fait que deux vues sont créées pour chaque contrôle logique, ce qui aboutit à une arborescence d’éléments visuels plus complexe qui nécessite plus de mémoire et un plus grand traitement à afficher à l’écran.

Les convertisseurs rapides réduisent les coûts d’inflation et de rendu d’un Xamarin.Forms contrôle en une seule vue. Par conséquent, au lieu de créer deux vues et de les ajouter à l’arborescence d’affichage, une seule est créée. Cela améliore les performances en créant moins d’objets, ce qui signifie à son tour une arborescence d’affichage moins complexe et une utilisation moins importante de la mémoire (ce qui entraîne également moins d’interruptions de garbage collection).

Les convertisseurs rapides sont disponibles pour les contrôles suivants dans Xamarin.Forms sur Android :

- [`Button`](xref:Xamarin.Forms.Button)
- [`Frame`](xref:Xamarin.Forms.Frame)
- [`Image`](xref:Xamarin.Forms.Image)
- [`Label`](xref:Xamarin.Forms.Label)
- [`MediaElement`](xref:Xamarin.Forms.MediaElement)

Fonctionnellement, ces convertisseurs rapides ne sont pas différents des convertisseurs hérités. À partir de Xamarin.Forms 4,0, toutes les applications `FormsAppCompatActivity` qui ciblent utiliseront ces convertisseurs rapides par défaut. Les convertisseurs pour tous les nouveaux contrôles, y compris [`ImageButton`](xref:Xamarin.Forms.ImageButton) et [`CollectionView`](xref:Xamarin.Forms.CollectionView) , utilisent l’approche de rendu rapide.

Les améliorations des performances lorsque vous utilisez des convertisseurs rapides varient pour chaque application, en fonction de la complexité de la disposition. Par exemple, les améliorations des performances de x2 sont possibles lorsque vous faites défiler un [`ListView`](xref:Xamarin.Forms.ListView) contenant des milliers de lignes de données, où les cellules de chaque ligne sont constituées de contrôles qui utilisent des convertisseurs rapides, ce qui entraîne un défilement visible.

> [!NOTE]
> Les convertisseurs personnalisés peuvent être créés pour les convertisseurs rapides à l’aide de la même approche que celle utilisée pour les convertisseurs hérités. Pour plus d’informations, consultez [Renderers personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="backwards-compatibility"></a>Compatibilité descendante

Les convertisseurs rapides peuvent être remplacés par les approches suivantes :

1. Pour activer les convertisseurs hérités, ajoutez la ligne de code suivante à votre `MainActivity` classe avant d’appeler `Forms.Init` :

    ```csharp
    Forms.SetFlags("UseLegacyRenderers");
    ```

1. À l’aide de convertisseurs personnalisés ciblant les convertisseurs hérités. Tous les convertisseurs personnalisés existants continuent de fonctionner avec les convertisseurs hérités.
1. En spécifiant un différent `View.Visual` , tel que `Material` , qui utilise différents convertisseurs. Pour plus d’informations sur l’élément visuel Material, consultez [ Xamarin.Forms élément visuel](~/xamarin-forms/user-interface/visual/material-visual.md).

## <a name="related-links"></a>Liens connexes

- [Renderers personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
