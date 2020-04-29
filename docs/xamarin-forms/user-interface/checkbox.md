---
title: Xamarin.Forms, case à cocher
description: La case à cocher Xamarin. Forms est un type de bouton qui peut être activé ou vide. Lorsqu’une case à cocher est activée, elle est considérée comme étant activée. Quand une case à cocher est vide, elle est considérée comme étant désactivée.
ms.prod: xamarin
ms.assetid: B8B9268B-BCB8-42B9-B08C-C0F22C137238
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/11/2019
ms.openlocfilehash: 10b7c4c3478545863ef49a23ef0f1be777e7eda9
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517116"
---
# <a name="xamarinforms-checkbox"></a>Xamarin.Forms, case à cocher

[![Télécharger l'](~/media/shared/download.png) exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)

Xamarin. Forms `CheckBox` est un type de bouton qui peut être activé ou vide. Lorsqu’une case à cocher est activée, elle est considérée comme étant activée. Quand une case à cocher est vide, elle est considérée comme étant désactivée.

`CheckBox`définit une `bool` propriété nommée `IsChecked`, qui indique si l' `CheckBox` option est activée. Cette propriété est également sauvegardée par un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objet, ce qui signifie qu’elle peut être stylisée et être la cible des liaisons de données.

> [!NOTE]
> La `IsChecked` propriété pouvant être liée a un mode de liaison [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay)par défaut de.

`CheckBox`définit un `CheckedChanged` événement qui est déclenché lorsque la `IsChecked` propriété change, soit via une manipulation de l’utilisateur, soit quand `IsChecked` une application définit la propriété. L' `CheckedChangedEventArgs` objet qui accompagne l' `CheckedChanged` événement a une propriété unique nommée `Value`, de type. `bool` Lorsque l’événement est déclenché, la valeur de la `Value` propriété est définie sur la nouvelle valeur de la `IsChecked` propriété.

## <a name="create-a-checkbox"></a>Créer une case à cocher

L’exemple suivant montre comment instancier un `CheckBox` en XAML :

```xaml
<CheckBox />
```

Ce code XAML produit l’apparence indiquée dans les captures d’écran suivantes :

![Capture d’écran d’une case à cocher vide sur iOS et Android](checkbox-images/checkbox-empty.png "Case à cocher vide")

Par défaut, `CheckBox` est vide. Peut `CheckBox` être vérifié par la manipulation de l’utilisateur, ou en `IsChecked` affectant `true`à la propriété la valeur :

```xaml
<CheckBox IsChecked="true" />
```

Ce code XAML produit l’apparence indiquée dans les captures d’écran suivantes :

![Capture d’écran d’une case à cocher activée sur iOS et Android](checkbox-images/checkbox-checked.png "Case à cocher activée")

Vous pouvez également créer `CheckBox` un dans le code :

```csharp
CheckBox checkBox = new CheckBox { IsChecked = true };
```

## <a name="respond-to-a-checkbox-changing-state"></a>Répondre à un état de modification de case à cocher

Lorsque la `IsChecked` propriété change, soit par manipulation de l’utilisateur, soit quand une `IsChecked` application définit la `CheckedChanged` propriété, l’événement se déclenche. Un gestionnaire d’événements pour cet événement peut être enregistré pour répondre à la modification :

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

L' `sender` argument est le `CheckBox` responsable de cet événement. Vous pouvez l’utiliser pour accéder à `CheckBox` l’objet, ou pour faire la `CheckBox` distinction entre plusieurs objets `CheckedChanged` qui partagent le même gestionnaire d’événements.

Un gestionnaire d’événements pour l’événement peut `CheckedChanged` également être enregistré dans le code :

```csharp
CheckBox checkBox = new CheckBox { ... };
checkBox.CheckedChanged += (sender, e) =>
{
    // Perform required operation after examining e.Value
};
```

## <a name="data-bind-a-checkbox"></a>Case à cocher lier aux données

Le `CheckedChanged` gestionnaire d’événements peut être éliminé à l’aide de la liaison de données et des `CheckBox` déclencheurs pour répondre à un en cours de vérification ou de vidage :

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

Dans cet exemple, le [`Label`](xref:Xamarin.Forms.Label) utilise une expression de liaison dans un déclencheur de données `IsChecked` pour surveiller la `CheckBox`propriété de. Lorsque cette propriété devient `true`, les `FontAttributes` propriétés `FontSize` et de la `Label` modification. Lorsque la `IsChecked` propriété retourne à `false`, les `FontAttributes` propriétés `FontSize` et de `Label` sont rétablies à leur état initial.

Dans les captures d’écran suivantes, la capture d' [`Label`](xref:Xamarin.Forms.Label) écran iOS affiche la `CheckBox` mise en forme lorsque le est vide, tandis que la capture d’écran Android affiche la `Label` mise en forme lorsque le `CheckBox` est activé :

[![Capture d’écran d’une case à cocher liée aux données, sur iOS et Android](checkbox-images/checkbox-databinding.png "Case à cocher lié aux données")](checkbox-images/checkbox-databinding-large.png#lightbox "Case à cocher lié aux données")

Pour plus d’informations sur les déclencheurs, consultez [déclencheurs Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-checkbox"></a>Désactiver une case à cocher

Parfois, une application entre dans un état `CheckBox` où une vérification n’est pas une opération valide. Dans ce cas, le `CheckBox` peut être désactivé en affectant `IsEnabled` à `false`sa propriété la valeur.

## <a name="checkbox-appearance"></a>Apparence de CheckBox

En plus des `CheckBox` propriétés qui héritent de la [`View`](xref:Xamarin.Forms.View) classe, `CheckBox` définit également une `Color` propriété qui définit sa couleur sur un : [`Color`](xref:Xamarin.Forms.Color)

```xaml
<CheckBox Color="Red" />
```

Les captures d’écran suivantes montrent une série `CheckBox` d’objets cochés, où la `Color` propriété de chaque objet a [`Color`](xref:Xamarin.Forms.Color)la valeur différent :

![Capture d’écran des cases à cocher en couleur, sur iOS et Android](checkbox-images/checkbox-colors.png "Case à cocher en couleur")

## <a name="checkbox-visual-states"></a>États visuels des cases à cocher

`CheckBox`a un `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) qui peut être utilisé pour initier une modification visuelle au `CheckBox` lorsqu’il est activé.

L’exemple de code XAML suivant montre comment définir un état visuel pour `IsChecked` l’État :

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

Dans cet exemple, le `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) spécifie que lorsque `CheckBox` est activé, sa `Color` propriété est définie sur Green. Spécifie que lorsque le `CheckBox` est dans un état normal, sa `Color` propriété est définie sur rouge. `Normal` `VisualState` Par conséquent, l’effet global est que `CheckBox` est rouge lorsqu’il est vide, et vert lorsqu’il est activé.

Pour plus d’informations sur les États visuels, consultez [Xamarin. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Liens connexes

- [Démonstrations de case à cocher (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)
- [Déclencheurs Xamarin.Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Gestionnaire d’état visuel Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
