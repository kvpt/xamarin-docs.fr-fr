---
titre : « Xamarin.Forms déclencheurs à deux écrans » : « cet article explique comment utiliser Xamarin.Forms les déclencheurs à deux écrans pour répondre aux modifications de l’interface utilisateur avec XAML ».
ms. Prod : xamarin ms. AssetID : 2181715D-3995-4E71-9A21-6B892F0B3B59 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 02/28/2020 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-dual-screen-triggers"></a>Xamarin.Formsdéclencheurs à deux écrans

![](~/media/shared/preview.png "This API is currently pre-release")

L' [`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen) espace de noms comprend deux déclencheurs d’État :

- [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger)déclenche une [`VisualState`](xref:Xamarin.Forms.VisualState) modification lorsque le mode d’affichage de la disposition attachée change.
- `WindowSpanModeStateTrigger`déclenche une [`VisualState`](xref:Xamarin.Forms.VisualState) modification lorsque le mode d’affichage de la fenêtre change.

Pour plus d’informations sur les déclencheurs d’État, consultez [déclencheurs d’État](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers).

## <a name="span-mode-state-trigger"></a>Déclencheur d’État en mode span

Un [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger) déclenche une [`VisualState`](xref:Xamarin.Forms.VisualState) modification lorsque le mode span de la disposition attachée change. Ce déclencheur a une propriété pouvant être liée unique :

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode), de type [`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode) , qui indique le mode d’étendue auquel le [`VisualState`](xref:Xamarin.Forms.VisualState) doit être appliqué.

> [!NOTE]
> Le [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger) dérive de la [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) classe et peut donc attacher un gestionnaire d’événements à l' [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) événement.

L’exemple de code XAML suivant montre un [`Grid`](xref:Xamarin.Forms.Grid) qui comprend des `SpanModeStateTrigger` objets :

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

Dans cet exemple, les États visuels sont définis sur un [`Grid`](xref:Xamarin.Forms.Grid) objet. La couleur d’arrière-plan du `Grid` est verte lorsqu’un seul volet est affiché, est rouge lorsque les volets sont affichés côte à côte et est violet lorsque les volets sont affichés en haut à gauche.

## <a name="window-span-mode-state-trigger"></a>Déclencheur d’État du mode de l’étendue de la fenêtre

Un `WindowSpanModeStateTrigger` déclenche une [`VisualState`](xref:Xamarin.Forms.VisualState) modification lorsque le mode span de la fenêtre change. Ce déclencheur a une propriété pouvant être liée unique :

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode), de type [`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode) , qui indique le mode d’étendue auquel le [`VisualState`](xref:Xamarin.Forms.VisualState) doit être appliqué.

> [!NOTE]
> Le `WindowSpanModeStateTrigger` dérive de la [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) classe et peut donc attacher un gestionnaire d’événements à l' [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) événement.

L’exemple de code XAML suivant montre un [`Grid`](xref:Xamarin.Forms.Grid) qui comprend des `WindowSpanModeStateTrigger` objets :

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

Dans cet exemple, les États visuels sont définis sur un [`Grid`](xref:Xamarin.Forms.Grid) objet. La couleur d’arrière-plan du `Grid` est rouge lorsqu’un seul volet est affiché, est vert lorsque les volets sont affichés côte à côte et est jaune lorsque les volets sont affichés en haut à gauche.

## <a name="related-links"></a>Liens connexes

- [Xamarin.FormsDéclencheurs](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.FormsGestionnaire d’état visuel](~/xamarin-forms/user-interface/visual-state-manager.md)
