---
title: Xamarin. Forms, commutateur
description: Le commutateur Xamarin. Forms est un type de bouton qui peut être manipulé par l’utilisateur pour basculer entre les États activé et désactivé. Cet article explique comment utiliser la classe Switch pour afficher un élément d’interface utilisateur de basculement.
ms.prod: xamarin
ms.assetId: B2F9CC65-481B-4323-8E77-C6BE29C90DE9
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/18/2019
ms.openlocfilehash: 88655aabdbd32db63aaf3330a18b0ad8105ea26c
ms.sourcegitcommit: b751605179bef8eee2df92cb484011a7dceb6fda
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2020
ms.locfileid: "78291914"
---
# <a name="xamarinforms-switch"></a>Xamarin. Forms, commutateur

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)

Le contrôle Xamarin. Forms [`Switch`](xref:Xamarin.Forms.Switch) est un bouton bascule horizontal qui peut être manipulé par l’utilisateur pour basculer entre les États activés et désactivés, qui sont représentés par une valeur de `boolean`. La classe `Switch` hérite de [`View`](xref:Xamarin.Forms.View).

Les captures d’écran suivantes montrent un contrôle de `Switch` **dans ses États d’activation** et de **désactivation** sur iOS et Android :

![Capture d’écran des commutateurs activés et désactivés, sur iOS et Android](switch-images/switch-states-default.png "Commutateurs sur iOS et Android")

Le contrôle `Switch` définit les propriétés suivantes :

* [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) est une valeur `boolean` qui indique si la `Switch` est **activée**.
* [`OnColor`](xref:Xamarin.Forms.Switch.OnColor) est un `Color` qui affecte la façon dont le `Switch` est rendu dans l’état activé **ou désactivé**.
* `ThumbColor` est le `Color` du curseur de commutation.

Ces propriétés sont sauvegardées par un objet [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , ce qui signifie que les `Switch` peuvent être stylisées et être la cible des liaisons de données.

Le contrôle `Switch` définit un événement `Toggled` qui est déclenché lorsque la propriété `IsToggled` change, soit via une manipulation de l’utilisateur, soit quand une application définit la propriété `IsToggled`. L’objet `ToggledEventArgs` qui accompagne l’événement `Toggled` a une propriété unique nommée `Value`, de type `bool`. Lorsque l’événement est déclenché, la valeur de la propriété `Value` reflète la nouvelle valeur de la propriété `IsToggled`.

## <a name="create-a-switch"></a>Créer un commutateur

Un `Switch` peut être instancié en XAML. Sa propriété `IsToggled` peut être définie pour basculer l' `Switch`. Par défaut, la propriété `IsToggled` est `false`. L’exemple suivant montre comment instancier un `Switch` en XAML avec la propriété facultative `IsToggled` définie :

```xaml
<Switch IsToggled="true"/>
```

Vous pouvez également créer un `Switch` dans le code :

```csharp
Switch switchControl = new Switch { IsToggled = true };
```

## <a name="switch-appearance"></a>Changer l’apparence

En plus des propriétés que [`Switch`](xref:Xamarin.Forms.Switch) hérite de la classe [`View`](xref:Xamarin.Forms.View) , `Switch` définit également les propriétés `OnColor` et `ThumbColor`. La propriété `OnColor` peut être définie pour définir la couleur d' `Switch` lorsqu’elle est basculée vers son état **on** , et la propriété `ThumbColor` peut être définie pour définir la `Color` du curseur de commutation. L’exemple suivant montre comment instancier un `Switch` en XAML avec ces propriétés définies :

```xaml
<Switch OnColor="Orange"
        ThumbColor="Green" />
```

Les propriétés peuvent également être définies lors de la création d’un `Switch` dans le code :

```csharp
Switch switch = new Switch { OnColor = Color.Orange, ThumbColor = Color.Green };
```

La capture d’écran suivante montre les `Switch` **dans les États bascule activé et** **désactivé** , avec les propriétés `OnColor` et `ThumbColor` définies :

![Capture d’écran des commutateurs activés et désactivés, sur iOS et Android](switch-images/switch-states-colors.png "Commutateurs sur iOS et Android")

## <a name="respond-to-a-switch-state-change"></a>Répondre à une modification de l’état du commutateur

Lorsque la propriété `IsToggled` change, soit par manipulation de l’utilisateur, soit quand une application définit la propriété `IsToggled`, l’événement `Toggled` se déclenche. Un gestionnaire d’événements pour cet événement peut être enregistré pour répondre à la modification :

```xaml
<Switch Toggled="OnToggled" />
```

Le fichier code-behind contient le gestionnaire de l’événement `Toggled` :

```csharp
void OnToggled(object sender, ToggledEventArgs e)
{
    // Perform an action after examining e.Value
}
```

L’argument `sender` dans le gestionnaire d’événements est le `Switch` chargé de déclencher cet événement. Vous pouvez utiliser la propriété `sender` pour accéder à l’objet `Switch`, ou pour faire la distinction entre plusieurs objets `Switch` qui partagent le même `Toggled` gestionnaire d’événements.

Le gestionnaire d’événements `Toggled` peut également être assigné dans le code :

```csharp
Switch switchControl = new Switch {...};
switchControl.Toggled += (sender, e) =>
{
    // Perform an action after examining e.Value
}
```

## <a name="data-bind-a-switch"></a>Liaison de données d’un commutateur

Le gestionnaire d’événements `Toggled` peut être éliminé à l’aide de la liaison de données et des déclencheurs pour répondre à une `Switch` modifiant les États de basculement.

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

Dans cet exemple, l' [`Label`](xref:Xamarin.Forms.Label) utilise une expression de liaison dans une `DataTrigger` pour surveiller la propriété `IsToggled` de l' `Switch` nommée `styleSwitch`. Lorsque cette propriété devient `true`, les propriétés `FontAttributes` et `FontSize` de l' `Label` sont modifiées. Lorsque la propriété `IsToggled` revient à `false`, les propriétés `FontAttributes` et `FontSize` de la `Label` sont réinitialisées à leur état initial.

Pour plus d’informations sur les déclencheurs, consultez [déclencheurs Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-switch"></a>Désactiver un commutateur

Une application peut entrer dans un État où la `Switch` basculée n’est pas une opération valide. Dans ce cas, l' `Switch` peut être désactivée en affectant à sa propriété `IsEnabled` la valeur `false`. Cela empêchera les utilisateurs de manipuler le `Switch`.

## <a name="related-links"></a>Liens connexes

* [Basculer les démonstrations](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)
* [Déclencheurs Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md)
