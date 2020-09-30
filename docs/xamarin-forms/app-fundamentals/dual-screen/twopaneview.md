---
title: Xamarin.Forms disposition sur deux écrans
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
ms.openlocfilehash: 3c77e81dccea9bc6953d719d52056c772c16d823
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562663"
---
# <a name="no-locxamarinforms-twopaneview-layout"></a>Xamarin.Forms Disposition TwoPaneView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)

La classe `TwoPaneView` représente un conteneur avec deux vues qui dimensionnent et positionnent le contenu dans l’espace disponible, côte à côte ou de haut en bas. Pour simplifier les choses, étant donné que `TwoPaneView` hérite de `Grid`, imaginez que ces propriétés sont appliquées à une grille.

## <a name="set-up-twopaneview"></a>Configurer TwoPaneView

Suivez ces instructions pour créer une disposition à deux écrans dans votre application :

1. Suivez les instructions de [prise en main](index.md) pour ajouter NuGet et configurer la `MainActivity` classe Android.
1. Commencez avec un de base `TwoPaneView` à l’aide du code XAML suivant :

    ```xaml
    <ContentPage
        xmlns:dualScreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen">
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

> [!TIP]
> Le code XAML ci-dessus omet de nombreux attributs communs de l' `ContentPage` élément. Quand vous ajoutez un `TwoPaneView` à votre application, n’oubliez pas de déclarer l' `xmlns:dualScreen` espace de noms comme indiqué.

## <a name="understand-twopaneview-modes"></a>Comprendre les modes de TwoPaneView

Un seul de ces modes peut être actif :

- `SinglePane` : un seul volet est actuellement visible.
- `Wide` : les deux volets sont disposés horizontalement. Un volet est à gauche et l’autre est à droite. Avec les deux écrans actifs, c’est le mode quand l’appareil est en mode portrait.
- `Tall` : les deux volets sont disposés verticalement. Un volet est en haut et l’autre est en bas. Avec les deux écrans actifs, c’est le mode quand l’appareil est en mode paysage.

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

- `TallModeConfiguration` indique, en mode haut, la disposition supérieure/inférieure ou si vous souhaitez qu’un seul volet soit visible comme défini par TwoPaneViewPriority.
- `WideModeConfiguration` indique, en mode étendu, la disposition gauche/droite ou si vous souhaitez qu’un seul volet soit visible comme défini par TwoPaneViewPriority.
- `PanePriority` détermine s’il faut montrer Pane1 ou Pane2 en mode SinglePane.

## <a name="related-links"></a>Liens connexes

- [DualScreen (sample)](/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)