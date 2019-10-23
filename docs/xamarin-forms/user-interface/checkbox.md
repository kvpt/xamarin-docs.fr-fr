---
title: Xamarin. Forms, case à cocher
description: La case à cocher Xamarin. Forms est un type de bouton qui peut être activé ou vide. Lorsqu’une case à cocher est activée, elle est considérée comme étant activée. Quand une case à cocher est vide, elle est considérée comme étant désactivée.
ms.prod: xamarin
ms.assetid: B8B9268B-BCB8-42B9-B08C-C0F22C137238
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/11/2019
ms.openlocfilehash: f78ca9d2cf7a9e57b81c5d923c64b36a7982c4b0
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "68739144"
---
# <a name="xamarinforms-checkbox"></a>Xamarin. Forms, case à cocher

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)

Le `CheckBox` Xamarin. Forms est un type de bouton qui peut être activé ou vide. Lorsqu’une case à cocher est activée, elle est considérée comme étant activée. Quand une case à cocher est vide, elle est considérée comme étant désactivée.

`CheckBox` définit une propriété `bool` nommée `IsChecked`, qui indique si l' `CheckBox` est cochée. Cette propriété est également sauvegardée par un objet [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , ce qui signifie qu’elle peut être stylisée et être la cible des liaisons de données.

> [!NOTE]
> La propriété pouvant être liée `IsChecked` a un mode de liaison par défaut de [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay).

`CheckBox` définit un événement `CheckedChanged` qui est déclenché lorsque la propriété `IsChecked` change, soit via une manipulation de l’utilisateur, soit quand une application définit la propriété `IsChecked`. L’objet `CheckedChangedEventArgs` qui accompagne l’événement `CheckedChanged` a une propriété unique nommée `Value`, de type `bool`. Lorsque l’événement est déclenché, la valeur de la propriété `Value` est définie sur la nouvelle valeur de la propriété `IsChecked`.

## <a name="create-a-checkbox"></a>Créer une case à cocher

L’exemple suivant montre comment instancier un `CheckBox` en XAML :

```xaml
<CheckBox />
```

Ce code XAML produit l’apparence indiquée dans les captures d’écran suivantes :

![Capture d’écran d’une case à cocher vide sur iOS et Android](checkbox-images/checkbox-empty.png "Case à cocher vide")

Par défaut, le `CheckBox` est vide. La `CheckBox` peut être vérifiée par la manipulation de l’utilisateur, ou en affectant à la propriété `IsChecked` la valeur `true` :

```xaml
<CheckBox IsChecked="true" />
```

Ce code XAML produit l’apparence indiquée dans les captures d’écran suivantes :

![Capture d’écran d’une case à cocher activée sur iOS et Android](checkbox-images/checkbox-checked.png "Case à cocher activée")

Vous pouvez également créer un `CheckBox` dans le code :

```csharp
CheckBox checkBox = new CheckBox { IsChecked = true };
```

## <a name="respond-to-a-checkbox-changing-state"></a>Répondre à un état de modification de case à cocher

Lorsque la propriété `IsChecked` change, soit par manipulation de l’utilisateur, soit quand une application définit la propriété `IsChecked`, l’événement `CheckedChanged` se déclenche. Un gestionnaire d’événements pour cet événement peut être enregistré pour répondre à la modification :

```xaml
<CheckBox CheckedChanged="OnCheckBoxCheckedChanged" />
```

Le fichier code-behind contient le gestionnaire de l’événement `CheckedChanged` :

```csharp
void OnCheckBoxCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation after examining e.Value
}
```

L’argument `sender` est le `CheckBox` responsable de cet événement. Vous pouvez l’utiliser pour accéder à l’objet `CheckBox`, ou pour faire la distinction entre plusieurs objets `CheckBox` partageant le même `CheckedChanged` événement.

Un gestionnaire d’événements pour l’événement `CheckedChanged` peut également être enregistré dans le code :

```csharp
CheckBox checkBox = new CheckBox { ... };
checkBox.CheckedChanged += (sender, e) =>
{
    // Perform required operation after examining e.Value
};
```

## <a name="data-bind-a-checkbox"></a>Case à cocher lier aux données

Le gestionnaire d’événements `CheckedChanged` peut être éliminé à l’aide de la liaison de données et des déclencheurs pour répondre à une `CheckBox` qui est cochée ou vide :

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

Dans cet exemple, le [`Label`](xref:Xamarin.Forms.Label) utilise une expression de liaison dans un déclencheur de données pour surveiller la propriété `IsChecked` de l' `CheckBox`. Lorsque cette propriété devient `true`, les propriétés `FontAttributes` et `FontSize` de la `Label` changent. Lorsque la propriété `IsChecked` revient à `false`, les propriétés `FontAttributes` et `FontSize` de la `Label` sont réinitialisées à leur état initial.

Dans les captures d’écran suivantes, la capture d’écran iOS affiche la mise en forme de [`Label`](xref:Xamarin.Forms.Label) lorsque le `CheckBox` est vide, tandis que la capture d’écran Android affiche la mise en forme du `Label` lorsque l' `CheckBox` est vérifiée :

[![Capture d’écran d’une case à cocher liée aux données, sur iOS et Android](checkbox-images/checkbox-databinding.png "Case à cocher lié aux données")](checkbox-images/checkbox-databinding-large.png#lightbox "Case à cocher lié aux données")

Pour plus d’informations sur les déclencheurs, consultez [déclencheurs Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-checkbox"></a>Désactiver une case à cocher

Parfois, une application entre dans un État où un `CheckBox` vérifié n’est pas une opération valide. Dans ce cas, l' `CheckBox` peut être désactivée en affectant à sa propriété `IsEnabled` la valeur `false`.

## <a name="checkbox-appearance"></a>Apparence de la case à cocher

En plus des propriétés que `CheckBox` hérite de la classe [`View`](xref:Xamarin.Forms.View) , `CheckBox` définit également une propriété `Color` qui définit sa couleur sur un [`Color`](xref:Xamarin.Forms.Color):

```xaml
<CheckBox Color="Red" />
```

Les captures d’écran suivantes montrent une série d’objets `CheckBox` activés, où chaque objet a sa propriété `Color` définie sur un [`Color`](xref:Xamarin.Forms.Color)différent :

![Capture d’écran des cases à cocher en couleur, sur iOS et Android](checkbox-images/checkbox-colors.png "Case à cocher en couleur")

## <a name="checkbox-visual-states"></a>États visuels des cases à cocher

`CheckBox` a un [`VisualState`](xref:Xamarin.Forms.VisualState) `IsChecked` qui peut être utilisé pour initier une modification visuelle de l' `CheckBox` lorsqu’il est activé.

L’exemple de code XAML suivant montre comment définir un état visuel pour l’État `IsChecked` :

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

Dans cet exemple, la `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) spécifie que lorsque la `CheckBox` est cochée, sa propriété `Color` est définie sur Green. L' `Normal` `VisualState` spécifie que lorsque le `CheckBox` est dans un état normal, sa propriété `Color` est définie sur rouge. Par conséquent, l’effet global est que le `CheckBox` est rouge lorsqu’il est vide, et vert lorsqu’il est activé.

Pour plus d’informations sur les États visuels, consultez [Xamarin. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Liens connexes

- [Démonstrations de case à cocher (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)
- [Déclencheurs Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Gestionnaire d’état visuel Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
