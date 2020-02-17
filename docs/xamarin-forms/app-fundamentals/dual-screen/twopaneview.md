---
title: Xamarin.Forms TwoPaneView
description: Ce guide explique comment utiliser Xamarin.Forms TwoPaneView pour optimiser l’expérience dans votre application sur des appareils double écran tels que Surface Duo et Surface Neo.
ms.prod: xamarin
ms.assetid: 17ee8afa-5e7c-4a4f-a9b6-2aca03f30fe3
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 1992a714774dba79e42c82e71af0bfe6aed7feb7
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77145503"
---
# <a name="xamarinforms-twopaneview"></a>Xamarin.Forms TwoPaneView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/pre-release/UserInterface/DualScreenDemos)

Représente un conteneur avec deux vues qui dimensionnent et positionnent le contenu dans l’espace disponible, côte à côte ou de haut en bas. Pour simplifier les choses, étant donné que TwoPaneView hérite de Grid, imaginez que ces propriétés sont appliquées à une grille.

## <a name="set-up-twopaneview"></a>Configurer TwoPaneView

La propriété `Source` de `TwoPaneView` peut utiliser un URI ou un chemin de fichier local. La lecture démarre immédiatement à l’ouverture du média.

```xaml
<ContentPage xmlns:dualScreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen">
    <dualScreen:TwoPaneView>
        <dualScreen:TwoPaneView.Pane1>
            <StackLayout>
                <Label Text="Pane1 Content"></Label>
            </StackLayout>
        </dualScreen:TwoPaneView.Pane1>
        <dualScreen:TwoPaneView.Pane2>
            <StackLayout>
                <Label Text="Pane2 Content"></Label>
            </StackLayout>
        </dualScreen:TwoPaneView.Pane2>
    </dualScreen:TwoPaneView>
</ContentPage>
```

## <a name="understand-twopaneview-modes"></a>Comprendre les modes de TwoPaneView

Un seul de ces modes peut être actif.

- `SinglePane` : un seul volet est actuellement visible.
- `Wide` : les deux volets sont disposés horizontalement. Un volet est à gauche et l’autre à droite. Sur deux écrans, ce mode est activé quand l’appareil est en mode portrait.
- `Tall` : les deux volets sont disposés verticalement. Un volet est en haut et l’autre en bas. Sur deux écrans, ce mode est activé quand l’appareil est en mode portrait.

## <a name="control-twopaneview-when-its-only-on-one-screen"></a>Contrôler TwoPaneView sur un seul écran

Les propriétés suivantes ne s’appliquent que si TwoPaneView occupe un seul écran. Si TwoPaneView est réparti sur deux écrans, ces propriétés n’ont aucun effet.

- `MinTallModeHeight` : indique la hauteur minimale du contrôle pour entrer en mode Tall.
- `MinWideModeWidth` : indique la largeur minimale du contrôle pour entrer en mode Wide.
- `Pane1Length` : définit la largeur de Pane1 en mode Wide, la hauteur de Pane1 en mode Tall, mais n’a aucun effet en mode SinglePane.
- `Pane2Length` : définit la largeur de Pane2 en mode Wide, la hauteur de Pane2 en mode Tall, mais n’a aucun effet en mode SinglePane.

## <a name="properties-that-apply-when-on-one-screen-or-two"></a>Propriétés qui s’appliquent à un ou deux écrans

- `TallModeConfiguration` : en mode Tall, indique la disposition gauche/droite ou si vous ne souhaitez qu’un seul volet soit visible (comme défini par TwoPaneViewPriority).
- `WideModeConfiguration` : en mode Tall, indique la disposition haut/bas ou si vous ne souhaitez qu’un seul volet soit visible (comme défini par TwoPaneViewPriority).
- `PanePriority` : indique s’il faut montrer Pane1 ou Pane2 en mode SinglePane.

## <a name="related-links"></a>Liens connexes

- [DualScreen (sample)](https://github.com/xamarin/xamarin-forms-samples/tree/pre-release/UserInterface/DualScreenDemos)
