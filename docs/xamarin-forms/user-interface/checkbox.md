---
titre : « Xamarin.Forms CheckBox » Description : la Xamarin.Forms case à cocher est un type de bouton qui peut être activé ou vide. Lorsqu’une case à cocher est activée, elle est considérée comme étant activée. Quand une case à cocher est vide, elle est considérée comme étant désactivée.
ms. Prod : xamarin ms. AssetID : B8B9268B-BCB8-42B9-B08C-C0F22C137238 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 06/11/2019 No-Loc :
- "Xamarin.Forms"
- "Xamarin.Essentials"

---

# <a name="no-locxamarinforms-checkbox"></a>Xamarin.Forms Activé

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)

Le Xamarin.Forms `CheckBox` est un type de bouton qui peut être activé ou vide. Lorsqu’une case à cocher est activée, elle est considérée comme étant activée. Quand une case à cocher est vide, elle est considérée comme étant désactivée.

`CheckBox` définit une `bool` propriété nommée `IsChecked` , qui indique si l’option `CheckBox` est activée. Cette propriété est également sauvegardée par un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objet, ce qui signifie qu’elle peut être stylisée et être la cible des liaisons de données.

> [!NOTE]
> La `IsChecked` propriété pouvant être liée a un mode de liaison par défaut de [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) .

`CheckBox` définit un `CheckedChanged` événement qui est déclenché lorsque la `IsChecked` propriété change, soit via une manipulation de l’utilisateur, soit quand une application définit la `IsChecked` propriété. L' `CheckedChangedEventArgs` objet qui accompagne l' `CheckedChanged` événement a une propriété unique nommée `Value` , de type `bool` . Lorsque l’événement est déclenché, la valeur de la `Value` propriété est définie sur la nouvelle valeur de la `IsChecked` propriété.

## <a name="create-a-checkbox"></a>Créer une case à cocher

L’exemple suivant montre comment instancier un `CheckBox` en XAML :

```xaml
<CheckBox />
```

Ce code XAML produit l’apparence indiquée dans les captures d’écran suivantes :

![Capture d’écran d’une case à cocher vide sur iOS et Android](checkbox-images/checkbox-empty.png "Case à cocher vide")

Par défaut, `CheckBox` est vide. `CheckBox`Peut être vérifié par la manipulation de l’utilisateur, ou en affectant à la propriété la valeur `IsChecked` `true` :

```xaml
<CheckBox IsChecked="true" />
```

Ce code XAML produit l’apparence indiquée dans les captures d’écran suivantes :

![Capture d’écran d’une case à cocher activée sur iOS et Android](checkbox-images/checkbox-checked.png "Case à cocher activée")

Vous `CheckBox` pouvez également créer un dans le code :

```csharp
CheckBox checkBox = new CheckBox { IsChecked = true };
```

## <a name="respond-to-a-checkbox-changing-state"></a>Répondre à un état de modification de case à cocher

Lorsque la `IsChecked` propriété change, soit par manipulation de l’utilisateur, soit quand une application définit la `IsChecked` propriété, l' `CheckedChanged` événement se déclenche. Un gestionnaire d’événements pour cet événement peut être enregistré pour répondre à la modification :

```xaml
<CheckBox CheckedChanged="OnCheckBoxCheckedChanged" />
```

Le fichier code-behind contient le gestionnaire de l' `CheckedChanged` événement :

```csharp
void OnCheckBoxCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation after examining e.Value
}
```

L' `sender` argument est le `CheckBox` responsable de cet événement. Vous pouvez l’utiliser pour accéder à l' `CheckBox` objet, ou pour faire la distinction entre plusieurs `CheckBox` objets qui partagent le même `CheckedChanged` Gestionnaire d’événements.

Un gestionnaire d’événements pour l' `CheckedChanged` événement peut également être enregistré dans le code :

```csharp
CheckBox checkBox = new CheckBox { ... };
checkBox.CheckedChanged += (sender, e) =>
{
    // Perform required operation after examining e.Value
};
```

## <a name="data-bind-a-checkbox"></a>Case à cocher lier aux données

Le `CheckedChanged` Gestionnaire d’événements peut être éliminé à l’aide de la liaison de données et des déclencheurs pour répondre à un `CheckBox` en cours de vérification ou de vidage :

```xaml
<CheckBox x:Name="checkBox" />
<Label Text="Lorem ipsum dolor sit amet, elit rutrum, enim hendrerit augue vitae praesent sed non, lorem aenean quis praesent pede.">
    <Label.Triggers>
        <DataTrigger TargetType="Label"
                     Binding="{Binding Source={x:Reference checkBox}, Path=IsChecked}"
                     Value="true">
            <Setter Property="FontAttributes"
                    Value="Italic, Bold" />
            <Setter Property="FontSize"
                    Value="Large" />
        </DataTrigger>
    </Label.Triggers>
</Label>
```

Dans cet exemple, le [`Label`](xref:Xamarin.Forms.Label) utilise une expression de liaison dans un déclencheur de données pour surveiller la `IsChecked` propriété de `CheckBox` . Lorsque cette propriété devient `true` , les `FontAttributes` `FontSize` Propriétés et de la `Label` modification. Lorsque la `IsChecked` propriété retourne à `false` , les `FontAttributes` `FontSize` Propriétés et de `Label` sont rétablies à leur état initial.

Dans les captures d’écran suivantes, la capture d’écran iOS affiche la [`Label`](xref:Xamarin.Forms.Label) mise en forme lorsque le `CheckBox` est vide, tandis que la capture d’écran Android affiche la `Label` mise en forme lorsque le `CheckBox` est activé :

[![Capture d’écran d’une case à cocher liée aux données, sur iOS et Android](checkbox-images/checkbox-databinding.png "Case à cocher lié aux données")](checkbox-images/checkbox-databinding-large.png#lightbox "Case à cocher lié aux données")

Pour plus d’informations sur les déclencheurs, consultez [ Xamarin.Forms déclencheurs](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-checkbox"></a>Désactiver une case à cocher

Parfois, une application entre dans un État où une `CheckBox` vérification n’est pas une opération valide. Dans ce cas, le `CheckBox` peut être désactivé en affectant `IsEnabled` à sa propriété la valeur `false` .

## <a name="checkbox-appearance"></a>Apparence de CheckBox

En plus des propriétés qui `CheckBox` héritent de la [`View`](xref:Xamarin.Forms.View) classe, `CheckBox` définit également une `Color` propriété qui définit sa couleur sur un [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<CheckBox Color="Red" />
```

Les captures d’écran suivantes montrent une série d’objets cochés `CheckBox` , où la propriété de chaque objet a la `Color` valeur différent [`Color`](xref:Xamarin.Forms.Color) :

![Capture d’écran des cases à cocher en couleur, sur iOS et Android](checkbox-images/checkbox-colors.png "Case à cocher en couleur")

## <a name="checkbox-visual-states"></a>États visuels des cases à cocher

`CheckBox` a un `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) qui peut être utilisé pour initier une modification visuelle au `CheckBox` lorsqu’il est activé.

L’exemple de code XAML suivant montre comment définir un état visuel pour l' `IsChecked` État :

```xaml
<CheckBox ...>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="Color"
                            Value="Red" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="IsChecked">
                <VisualState.Setters>
                    <Setter Property="Color"
                            Value="Green" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</CheckBox>
```

Dans cet exemple, le `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) spécifie que lorsque `CheckBox` est activé, sa `Color` propriété est définie sur Green. `Normal` `VisualState` Spécifie que lorsque le `CheckBox` est dans un état normal, sa `Color` propriété est définie sur rouge. Par conséquent, l’effet global est que `CheckBox` est rouge lorsqu’il est vide, et vert lorsqu’il est activé.

Pour plus d’informations sur les États visuels, consultez [ Xamarin.Forms Gestionnaire d’état visuel](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Liens connexes

- [Démonstrations de case à cocher (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)
- [Xamarin.Forms Déclencheurs](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms Gestionnaire d’état visuel](~/xamarin-forms/user-interface/visual-state-manager.md)