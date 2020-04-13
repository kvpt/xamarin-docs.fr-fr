---
title: Xamarin.Forms double écran déclencheurs
description: Cet article explique comment utiliser Xamarin.Forms déclencheurs à double écran pour répondre aux changements d’interface utilisateur avec XAML.
ms.prod: xamarin
ms.assetid: 2181715D-3995-4E71-9A21-6B892F0B3B59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2020
ms.openlocfilehash: 0cce23973c90c89ce90e40651a2646d5f1bdd2c0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78165537"
---
# <a name="xamarinforms-dual-screen-triggers"></a>Xamarin.Forms double écran déclencheurs

![](~/media/shared/preview.png "This API is currently pre-release")

L’espace [`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen) nom comprend deux déclencheurs d’état :

- [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger)déclenche une [`VisualState`](xref:Xamarin.Forms.VisualState) modification lorsque le mode de vue de la mise en page ci-jointe change.
- `WindowSpanModeStateTrigger`déclenche un [`VisualState`](xref:Xamarin.Forms.VisualState) changement lorsque le mode de vue de la fenêtre change.

Pour plus d’informations sur les déclencheurs de l’État, voir [déclencheurs de l’État](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers).

## <a name="span-mode-state-trigger"></a>Déclencheur d’état de mode d’envergure

Un [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger) déclenche [`VisualState`](xref:Xamarin.Forms.VisualState) une modification lorsque le mode de portée de la mise en page ci-jointe change. Ce déclencheur a une propriété liant unique :

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode), du [`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode)type , qui indique [`VisualState`](xref:Xamarin.Forms.VisualState) le mode de travée auquel le doit être appliqué.

> [!NOTE]
> Le [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger) dérive de [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) la classe et peut donc [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) joindre un gestionnaire d’événements à l’événement.

L’exemple XAML [`Grid`](xref:Xamarin.Forms.Grid) suivant `SpanModeStateTrigger` montre un qui inclut des objets :

```xaml
<Grid>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup>
            <VisualState x:Name="GridSingle">
                <VisualState.StateTriggers>
                    <dualScreen:SpanModeStateTrigger SpanMode="SinglePane"/>
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Green" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="GridWide">
                <VisualState.StateTriggers>
                    <dualScreen:SpanModeStateTrigger SpanMode="Wide" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Red" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="GridTall">
                <VisualState.StateTriggers>
                    <dualScreen:SpanModeStateTrigger SpanMode="Tall" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Purple" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
    ...
</Grid>
```

Dans cet exemple, les états [`Grid`](xref:Xamarin.Forms.Grid) visuels sont fixés sur un objet. La couleur de `Grid` fond de l’est vert quand un seul volet est montré, est rouge lorsque les vitres sont montrées côte à côte, et est pourpre lorsque les vitres sont montrées en bas.

## <a name="window-span-mode-state-trigger"></a>Déclencheur d’état de mode d’épagneul de fenêtre

Un `WindowSpanModeStateTrigger` déclenche [`VisualState`](xref:Xamarin.Forms.VisualState) un changement lorsque le mode d’envergure de la fenêtre change. Ce déclencheur a une propriété liant unique :

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode), du [`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode)type , qui indique [`VisualState`](xref:Xamarin.Forms.VisualState) le mode de travée auquel le doit être appliqué.

> [!NOTE]
> Le `WindowSpanModeStateTrigger` dérive de [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) la classe et peut donc [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) joindre un gestionnaire d’événements à l’événement.

L’exemple XAML [`Grid`](xref:Xamarin.Forms.Grid) suivant `WindowSpanModeStateTrigger` montre un qui inclut des objets :

```xaml
<Grid>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup>
            <VisualState x:Name="NotSpanned">
                <VisualState.StateTriggers>
                    <dualScreen:WindowSpanModeStateTrigger SpanMode="SinglePane"/>
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Red" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="Spanned">
                <VisualState.StateTriggers>
                    <dualScreen:WindowSpanModeStateTrigger SpanMode="Wide" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Green" />
                </VisualState.Setters>
            </VisualState>
                <VisualState x:Name="Tall">
                    <VisualState.StateTriggers>
                        <dualScreen:WindowSpanModeStateTrigger SpanMode="Tall" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor" Value="Yellow" />
                    </VisualState.Setters>
                </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
    ...
</Grid>    
```

Dans cet exemple, les états [`Grid`](xref:Xamarin.Forms.Grid) visuels sont fixés sur un objet. La couleur de `Grid` fond de la est rouge quand un seul volet est montré, est vert lorsque les vitres sont montrées côte à côte, et est jaune lorsque les vitres sont montrées en bas.

## <a name="related-links"></a>Liens connexes

- [Déclencheurs Xamarin.Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)
