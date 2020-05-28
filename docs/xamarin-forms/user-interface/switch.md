---
title: Xamarin.FormsUtilisez
description: Le Xamarin.Forms commutateur est un type de bouton qui peut être manipulé par l’utilisateur pour basculer entre les États activé et désactivé. Cet article explique comment utiliser la classe Switch pour afficher un élément d’interface utilisateur de basculement.
ms.prod: ''
ms.assetId: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a5c2583b7632acdfa7d8439dc96b3964fa3cfcab
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136238"
---
# <a name="xamarinforms-switch"></a>Xamarin.FormsUtilisez

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)

Le Xamarin.Forms [`Switch`](xref:Xamarin.Forms.Switch) contrôle est un bouton bascule horizontal qui peut être manipulé par l’utilisateur pour basculer entre les États activés et désactivés, représentés par une `boolean` valeur. La `Switch` classe hérite de [`View`](xref:Xamarin.Forms.View) .

Les captures d’écran suivantes montrent un `Switch` contrôle **on** dans ses États d’activation et de **désactivation** sur iOS et Android :

![Capture d’écran des commutateurs activés et désactivés, sur iOS et Android](switch-images/switch-states-default.png "Commutateurs sur iOS et Android")

Le `Switch` contrôle définit les propriétés suivantes :

* [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled)est une `boolean` valeur qui indique si `Switch` est **activé**.
* [`OnColor`](xref:Xamarin.Forms.Switch.OnColor)est un `Color` qui affecte la façon dont le `Switch` est rendu dans l’état activé **on**ou désactivé.
* `ThumbColor`est le `Color` du curseur de commutateur.

Ces propriétés sont sauvegardées par un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objet, ce qui signifie que `Switch` peut être stylisé et être la cible des liaisons de données.

Le `Switch` contrôle définit un `Toggled` événement qui est déclenché lorsque la `IsToggled` propriété change, soit via une manipulation de l’utilisateur, soit quand une application définit la `IsToggled` propriété. L' `ToggledEventArgs` objet qui accompagne l' `Toggled` événement a une propriété unique nommée `Value` , de type `bool` . Lorsque l’événement est déclenché, la valeur de la `Value` propriété reflète la nouvelle valeur de la `IsToggled` propriété.

## <a name="create-a-switch"></a>Créer un commutateur

Un `Switch` peut être instancié en XAML. Sa `IsToggled` propriété peut être définie pour basculer le `Switch` . Par défaut, la `IsToggled` propriété a la valeur `false` . L’exemple suivant montre comment instancier un `Switch` en XAML avec le `IsToggled` jeu de propriétés facultatif :

```xaml
<Switch IsToggled="true"/>
```

Un `Switch` peut également être créé dans le code :

```csharp
Switch switchControl = new Switch { IsToggled = true };
```

## <a name="switch-appearance"></a>Changer l’apparence

En plus des propriétés qui [`Switch`](xref:Xamarin.Forms.Switch) héritent de la [`View`](xref:Xamarin.Forms.View) classe, `Switch` définit également les `OnColor` `ThumbColor` Propriétés et. La `OnColor` propriété peut être définie pour définir la `Switch` couleur lorsqu’elle est basculée vers son état **on** , et la `ThumbColor` propriété peut être définie pour définir le `Color` du curseur de basculement. L’exemple suivant montre comment instancier un `Switch` en XAML avec ces propriétés définies :

```xaml
<Switch OnColor="Orange"
        ThumbColor="Green" />
```

Les propriétés peuvent également être définies lors de la création d’un `Switch` dans le code :

```csharp
Switch switch = new Switch { OnColor = Color.Orange, ThumbColor = Color.Green };
```

La capture d’écran suivante montre les `Switch` États de basculement **activés** et **désactivés** , avec les `OnColor` `ThumbColor` Propriétés et définies :

![Capture d’écran des commutateurs activés et désactivés, sur iOS et Android](switch-images/switch-states-colors.png "Commutateurs sur iOS et Android")

## <a name="respond-to-a-switch-state-change"></a>Répondre à une modification de l’état du commutateur

Lorsque la `IsToggled` propriété change, soit par manipulation de l’utilisateur, soit quand une application définit la `IsToggled` propriété, l' `Toggled` événement se déclenche. Un gestionnaire d’événements pour cet événement peut être enregistré pour répondre à la modification :

```xaml
<Switch Toggled="OnToggled" />
```

Le fichier code-behind contient le gestionnaire de l' `Toggled` événement :

```csharp
void OnToggled(object sender, ToggledEventArgs e)
{
    // Perform an action after examining e.Value
}
```

L' `sender` argument dans le gestionnaire d’événements est `Switch` chargé de déclencher cet événement. Vous pouvez utiliser la `sender` propriété pour accéder à l' `Switch` objet, ou pour faire la distinction entre plusieurs `Switch` objets qui partagent le même `Toggled` Gestionnaire d’événements.

Le `Toggled` Gestionnaire d’événements peut également être assigné dans le code :

```csharp
Switch switchControl = new Switch {...};
switchControl.Toggled += (sender, e) =>
{
    // Perform an action after examining e.Value
}
```

## <a name="data-bind-a-switch"></a>Liaison de données d’un commutateur

Le `Toggled` Gestionnaire d’événements peut être éliminé à l’aide de la liaison de données et des déclencheurs pour répondre à des `Switch` États bascule modifiés.

```xaml
<Switch x:Name="styleSwitch" />
<Label Text="Lorem ipsum dolor sit amet, elit rutrum, enim hendrerit augue vitae praesent sed non, lorem aenean quis praesent pede.">
    <Label.Triggers>
        <DataTrigger TargetType="Label"
                     Binding="{Binding Source={x:Reference styleSwitch}, Path=IsToggled}"
                     Value="true">
            <Setter Property="FontAttributes"
                    Value="Italic, Bold" />
            <Setter Property="FontSize"
                    Value="Large" />
        </DataTrigger>
    </Label.Triggers>
</Label>
```

Dans cet exemple, le [`Label`](xref:Xamarin.Forms.Label) utilise une expression de liaison dans un `DataTrigger` pour surveiller la `IsToggled` propriété du `Switch` nommé `styleSwitch` . Lorsque cette propriété devient `true` , les `FontAttributes` `FontSize` Propriétés et de `Label` sont modifiées. Lorsque la `IsToggled` propriété retourne à `false` , les `FontAttributes` `FontSize` Propriétés et de `Label` sont rétablies à leur état initial.

Pour plus d’informations sur les déclencheurs, consultez [ Xamarin.Forms déclencheurs](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-switch"></a>Désactiver un commutateur

Une application peut entrer dans un État où le `Switch` basculement n’est pas une opération valide. Dans ce cas, le `Switch` peut être désactivé en affectant `IsEnabled` à sa propriété la valeur `false` . Cela empêchera les utilisateurs de manipuler le `Switch` .

## <a name="related-links"></a>Liens connexes

* [Basculer les démonstrations](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)
* [Xamarin.FormsDéclencheurs](~/xamarin-forms/app-fundamentals/triggers.md)
