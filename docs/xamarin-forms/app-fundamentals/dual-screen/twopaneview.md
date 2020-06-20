---
title: Xamarin.Formsdisposition sur deux écrans
description: Ce guide explique comment utiliser Xamarin.Forms TwoPaneView pour optimiser votre expérience d’application pour les appareils à deux écrans tels que surface Duo et surface Neo.
ms.prod: xamarin
ms.assetid: 17ee8afa-5e7c-4a4f-a9b6-2aca03f30fe3
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 28d4b3da44cc1a022b70c0de0720be747e047f9f
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138890"
---
# <a name="xamarinforms-dual-screen-layout"></a>Xamarin.Formsdisposition sur deux écrans

![](~/media/shared/preview.png "This API is currently pre-release")

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)

La classe `TwoPaneView` représente un conteneur avec deux vues qui dimensionnent et positionnent le contenu dans l’espace disponible, côte à côte ou de haut en bas. Pour simplifier les choses, étant donné que `TwoPaneView` hérite de `Grid`, imaginez que ces propriétés sont appliquées à une grille.

## <a name="set-up-twopaneview"></a>Configurer TwoPaneView

La propriété `TwoPaneView.Source` peut utiliser un URI ou un chemin de fichier local. La lecture démarre immédiatement à l’ouverture du média :

```xaml
<ContentPage xmlns:dualScreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen">
    <dualScreen:TwoPaneView>
        <dualScreen:TwoPaneView.Pane1>
            <StackLayout>
                <Label Text="Pane1 Content" />
            </StackLayout>
        </dualScreen:TwoPaneView.Pane1>
        <dualScreen:TwoPaneView.Pane2>
            <StackLayout>
                <Label Text="Pane2 Content" />
            </StackLayout>
        </dualScreen:TwoPaneView.Pane2>
    </dualScreen:TwoPaneView>
</ContentPage>
```

## <a name="understand-twopaneview-modes"></a>Comprendre les modes de TwoPaneView

Un seul de ces modes peut être actif :

- `SinglePane` : un seul volet est actuellement visible.
- `Wide` : les deux volets sont disposés horizontalement. Un volet est à gauche et l’autre à droite. Avec les deux écrans actifs, c’est le mode quand l’appareil est en mode portrait.
- `Tall` : les deux volets sont disposés verticalement. Un volet est en haut et l’autre en bas. Avec les deux écrans actifs, c’est le mode quand l’appareil est en mode paysage.

## <a name="control-twopaneview-when-its-only-on-one-screen"></a>Contrôler TwoPaneView sur un seul écran

Les propriétés suivantes s’appliquent quand `TwoPaneView` occupe un seul écran :

- `MinTallModeHeight` indique la hauteur minimale du contrôle pour entrer en mode Tall.
- `MinWideModeWidth` indique la largeur minimale du contrôle pour entrer en mode Wide.
- `Pane1Length` définit la largeur de Pane1 en mode Wide, la hauteur de Pane1 en mode Tall, et n’a aucun effet en mode SinglePane.
- `Pane2Length` définit la largeur de Pane2 en mode Wide, la hauteur de Pane2 en mode Tall, et n’a aucun effet en mode SinglePane.

> [!IMPORTANT]
> Si `TwoPaneView` est réparti sur deux écrans, ces propriétés n’ont aucun effet.

## <a name="properties-that-apply-when-on-one-screen-or-two"></a>Propriétés qui s’appliquent à un ou deux écrans

Les propriétés suivantes s’appliquent quand `TwoPaneView` occupe un seul écran ou deux écrans :

- `TallModeConfiguration` en mode Tall indique la disposition gauche/droite ou si vous voulez qu’un seul volet soit visible (comme défini par TwoPaneViewPriority).
- `WideModeConfiguration` en mode Wide indique la disposition haut/bas ou si vous voulez qu’un seul volet soit visible (comme défini par TwoPaneViewPriority).
- `PanePriority` détermine s’il faut montrer Pane1 ou Pane2 en mode SinglePane.

## <a name="related-links"></a>Liens connexes

- [DualScreen (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)
