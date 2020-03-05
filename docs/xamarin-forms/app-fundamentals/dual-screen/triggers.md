---
title: Déclencheurs à deux écrans Xamarin. Forms
description: Cet article explique comment utiliser les déclencheurs à deux écrans Xamarin. Forms pour répondre aux modifications de l’interface utilisateur avec XAML.
ms.prod: xamarin
ms.assetid: 2181715D-3995-4E71-9A21-6B892F0B3B59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2020
ms.openlocfilehash: 0cce23973c90c89ce90e40651a2646d5f1bdd2c0
ms.sourcegitcommit: 0e35d3eafad833d3f19768b001bd804ddda8b69b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2020
ms.locfileid: "78165537"
---
# <a name="xamarinforms-dual-screen-triggers"></a>Déclencheurs à deux écrans Xamarin. Forms

![](~/media/shared/preview.png "This API is currently pre-release")

L’espace de noms [`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen) comprend deux déclencheurs d’État :

- [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger) déclenche une modification de [`VisualState`](xref:Xamarin.Forms.VisualState) lorsque le mode d’affichage de la disposition attachée change.
- `WindowSpanModeStateTrigger` déclenche une modification de [`VisualState`](xref:Xamarin.Forms.VisualState) lorsque le mode d’affichage de la fenêtre change.

Pour plus d’informations sur les déclencheurs d’État, consultez [déclencheurs d’État](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers).

## <a name="span-mode-state-trigger"></a>Déclencheur d’État en mode span

Un [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger) déclenche une modification [`VisualState`](xref:Xamarin.Forms.VisualState) lorsque le mode span de la disposition attachée change. Ce déclencheur a une propriété pouvant être liée unique :

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode), de type [`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode), qui indique le mode d’étendue auquel la [`VisualState`](xref:Xamarin.Forms.VisualState) doit être appliquée.

> [!NOTE]
> Le [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger) dérive de la classe [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) et peut donc attacher un gestionnaire d’événements à l’événement [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) .

L’exemple de code XAML suivant montre une [`Grid`](xref:Xamarin.Forms.Grid) qui comprend des objets `SpanModeStateTrigger` :

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

Dans cet exemple, les États visuels sont définis sur un objet [`Grid`](xref:Xamarin.Forms.Grid) . La couleur d’arrière-plan de la `Grid` est verte quand un seul volet s’affiche, est rouge lorsque les volets sont affichés côte à côte et est violet lorsque les volets sont affichés en haut à gauche.

## <a name="window-span-mode-state-trigger"></a>Déclencheur d’État du mode de l’étendue de la fenêtre

Un `WindowSpanModeStateTrigger` déclenche une modification [`VisualState`](xref:Xamarin.Forms.VisualState) lorsque le mode span de la fenêtre change. Ce déclencheur a une propriété pouvant être liée unique :

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode), de type [`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode), qui indique le mode d’étendue auquel la [`VisualState`](xref:Xamarin.Forms.VisualState) doit être appliquée.

> [!NOTE]
> Le `WindowSpanModeStateTrigger` dérive de la classe [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) et peut donc attacher un gestionnaire d’événements à l’événement [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) .

L’exemple de code XAML suivant montre une [`Grid`](xref:Xamarin.Forms.Grid) qui comprend des objets `WindowSpanModeStateTrigger` :

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

Dans cet exemple, les États visuels sont définis sur un objet [`Grid`](xref:Xamarin.Forms.Grid) . La couleur d’arrière-plan du `Grid` est rouge lorsqu’un seul volet est affiché, est vert lorsque les volets sont affichés côte à côte et est jaune lorsque les volets sont affichés en haut à gauche.

## <a name="related-links"></a>Liens connexes

- [Déclencheurs Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Gestionnaire d’état visuel Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
