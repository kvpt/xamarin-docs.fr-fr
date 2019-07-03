---
title: Case à cocher de Xamarin.Forms
description: La case à cocher Xamarin.Forms est un type de bouton qui peut soit être vérifiée ou est vide. Quand une case à cocher est activée, elle est considérée comme doivent pour se trouver sur. Quand une case à cocher est vide, elle est considérée comme être hors tension.
ms.prod: xamarin
ms.assetid: B8B9268B-BCB8-42B9-B08C-C0F22C137238
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/11/2019
ms.openlocfilehash: 42631b1b67dc1d342e9f8666916604e68ee158d8
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67517918"
---
# <a name="xamarinforms-checkbox"></a>Case à cocher de Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CheckBoxDemos)

Xamarin.Forms `CheckBox` est un type de bouton qui peut être activé ou vide. Quand une case à cocher est activée, elle est considérée comme doivent pour se trouver sur. Quand une case à cocher est vide, elle est considérée comme être hors tension.

`CheckBox` définit un `bool` propriété nommée `IsChecked`, ce qui indique si le `CheckBox` est activée. Cette propriété est également appuyée par un [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objet, ce qui signifie qu’il peut être un style et être la cible des liaisons de données.

> [!NOTE]
> Le `IsChecked` propriété pouvant être liée a un mode de liaison par défaut de [ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay).

`CheckBox` définit un `CheckedChanged` événement qui a déclenché lorsque le `IsChecked` modifications apportées aux propriétés, soit via la manipulation de l’utilisateur, ou lorsqu’une application définit le `IsChecked` propriété. Le `CheckedChangedEventArgs` objet qui accompagne le `CheckedChanged` événement a une propriété unique nommée `Value`, de type `bool`. Lorsque l’événement est déclenché, la valeur de la `Value` propriété est définie sur la nouvelle valeur de la `IsChecked` propriété.

## <a name="create-a-checkbox"></a>Créer une case à cocher

L’exemple suivant montre comment instancier un `CheckBox` dans XAML :

```xaml
<CheckBox />
```

Ce XAML provoque l’affichage indiqué dans les captures d’écran suivante :

![Capture d’écran d’une case à cocher vide sur iOS et Android](checkbox-images/checkbox-empty.png "case à cocher vide")

Par défaut, le `CheckBox` est vide. Le `CheckBox` peut être vérifiée par manipulation de l’utilisateur, ou en définissant le `IsChecked` propriété `true`:

```xaml
<CheckBox IsChecked="true" />
```

Ce XAML provoque l’affichage indiqué dans les captures d’écran suivante :

![Capture d’écran d’une case cochée, sur iOS et Android](checkbox-images/checkbox-checked.png "cochée la case à cocher")

Vous pouvez également un `CheckBox` peuvent être créés dans le code :

```csharp
CheckBox checkBox = new CheckBox { IsChecked = true };
```

## <a name="respond-to-a-checkbox-changing-state"></a>Répondre à une case à cocher de changement d’état

Lorsque le `IsChecked` modifications apportées aux propriétés, soit via la manipulation de l’utilisateur, ou lorsqu’une application définit le `IsChecked` propriété, le `CheckedChanged` se déclenche des événements. Un gestionnaire d’événements pour cet événement peut être inscrit pour répondre à la modification :

```xaml
<CheckBox CheckedChanged="OnCheckBoxCheckedChanged" />
```

Le fichier code-behind contient le gestionnaire pour le `CheckedChanged` événement :

```csharp
void OnCheckBoxCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation after examining e.Value
}
```

Le `sender` argument est le `CheckBox` responsable de cet événement. Vous pouvez l’utiliser pour accéder à la `CheckBox` objet, ou faire la distinction entre plusieurs `CheckBox` objets partagent la même `CheckedChanged` événement.

Vous pouvez également un gestionnaire d’événements pour le `CheckedChanged` d’événement peut être enregistré dans le code :

```csharp
CheckBox checkBox = new CheckBox { ... };
checkBox.CheckedChanged += (sender, e) =>
{
    // Perform required operation after examining e.Value
};
```

## <a name="data-bind-a-checkbox"></a>Une case à cocher Lier des données

Le `CheckedChanged` Gestionnaire d’événements peut être éliminé en utilisant la liaison de données et des déclencheurs pour répondre à une `CheckBox` activé ou vide :

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

Dans cet exemple, le [ `Label` ](xref:Xamarin.Forms.Label) utilise une expression de liaison dans un déclencheur de données pour surveiller le `IsChecked` propriété de la `CheckBox`. Lorsque cette propriété devient `true`, le `FontAttributes` et `FontSize` propriétés de la `Label` modifier. Lorsque le `IsChecked` propriété renvoie vers `false`, le `FontAttributes` et `FontSize` propriétés de la `Label` sont rétablies à leur état initial.

Dans les captures d’écran suivante, la capture d’écran iOS affiche le [ `Label` ](xref:Xamarin.Forms.Label) mise en forme lorsque la `CheckBox` est vide, alors que la capture d’écran Android affiche le `Label` mise en forme lorsque le `CheckBox` est vérifiée :

[![Capture d’écran de données lié case à cocher, sur iOS et Android](checkbox-images/checkbox-databinding.png "case à cocher lié aux données")](checkbox-images/checkbox-databinding-large.png#lightbox "case à cocher lié aux données")

Pour plus d’informations sur les déclencheurs, consultez [Xamarin.Forms déclencheurs](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-checkbox"></a>Désactiver une case à cocher

Parfois, une application entre dans un état où un `CheckBox` en cours de vérification n’est pas une opération valide. Dans ce cas, le `CheckBox` peut être désactivée en définissant son `IsEnabled` propriété `false`.

## <a name="checkbox-appearance"></a>Apparence de la case à cocher

Outre les propriétés qui `CheckBox` hérite le [ `View` ](xref:Xamarin.Forms.View) (classe), `CheckBox` définit également un `Color` propriété qui définit sa couleur à un [ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<CheckBox Color="Red" />
```

Les captures d’écran suivantes montrent une série de checked `CheckBox` objets, où chaque objet possède ses `Color` propriété définie sur un autre [ `Color` ](xref:Xamarin.Forms.Color):

![Capture d’écran de cases à cocher en couleur, sur iOS et Android](checkbox-images/checkbox-colors.png "case à cocher en couleur")

## <a name="checkbox-visual-states"></a>Case à cocher des états visuels

`CheckBox` a un `IsChecked` [ `VisualState` ](xref:Xamarin.Forms.VisualState) qui peut être utilisé pour lancer un plus grand changement pour le `CheckBox` quand il est activé.

L’exemple XAML suivant montre comment définir un état visuel pour le `IsChecked` état :

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

Dans cet exemple, le `IsChecked` [ `VisualState` ](xref:Xamarin.Forms.VisualState) Spécifie que quand le `CheckBox` est activée, son `Color` propriété sera définie au vert. Le `Normal` `VisualState` Spécifie que quand le `CheckBox` est dans un état normal, son `Color` propriété sera définie en rouge. Par conséquent, l’effet global est que le `CheckBox` est rouge lorsqu’il est vide et vert quand elle est activée.

Pour plus d’informations sur les états visuels, consultez [Gestionnaire d’état visuel Xamarin.Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Liens connexes

- [Démonstrations de case à cocher (exemple)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CheckBoxDemos)
- [Déclencheurs de Xamarin.Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Gestionnaire d’état visuel de Xamarin.Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
