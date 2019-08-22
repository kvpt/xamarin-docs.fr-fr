---
title: Xamarin. Forms, commutateur
description: Le commutateur Xamarin. Forms est un type de bouton qui peut être manipulé par l’utilisateur pour basculer entre les États activé et désactivé. Cet article explique comment utiliser la classe Switch pour afficher un élément d’interface utilisateur de basculement.
ms.prod: xamarin
ms.assetId: B2F9CC65-481B-4323-8E77-C6BE29C90DE9
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/18/2019
ms.openlocfilehash: 1f2ef838287e32df5df42f73e4b43816d618552d
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887884"
---
# <a name="xamarinforms-switch"></a>Xamarin. Forms, commutateur

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)

Le contrôle Xamarin. [`Switch`](xref:Xamarin.Forms.Switch) Forms est un bouton bascule horizontal qui peut être manipulé par l’utilisateur pour basculer entre les États activés et désactivés, représentés par `boolean` une valeur. La `Switch` classe hérite de [`View`](xref:Xamarin.Forms.View).

Les captures d’écran suivantes `Switch` montrent un contrôle dans ses États d’activation et de désactivation sur iOS et Android:

![Capture d’écran des commutateurs activés et désactivés, sur iOS et Android](switch-images/switch-states-default.png "Commutateurs sur iOS et Android")

Le `Switch` contrôle définit deux propriétés:

* [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled)est une `boolean` valeur qui indique `Switch` si est **activé**.
* [`OnColor`](xref:Xamarin.Forms.Switch.OnColor)est un `Color` qui affecte la façon `Switch` dont le est rendu dans l’état activéou désactivé.
* `ThumbColor`est le `Color` du curseur de commutateur.

Ces propriétés sont sauvegardées par un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objet, ce qui `Switch` signifie que peut être stylisé et être la cible des liaisons de données.

Le `Switch` contrôle définit un `Toggled` événement qui est déclenché lorsque la `IsToggled` propriété change, soit via une manipulation de l’utilisateur, soit quand `IsToggled` une application définit la propriété. L' `ToggledEventArgs` objet qui accompagne l' `Toggled` événement a une propriété unique nommée `Value`, de type `bool`. Lorsque l’événement est déclenché, la valeur de la `Value` propriété reflète la nouvelle valeur de la `IsToggled` propriété.

## <a name="create-a-switch"></a>Créer un commutateur

Un `Switch` peut être instancié en XAML. Sa `IsToggled` propriété peut être définie pour basculer le `Switch`. Par défaut, la `IsToggled` propriété a `false`la valeur. L’exemple suivant montre comment instancier un `Switch` en XAML avec le jeu `IsToggled` de propriétés facultatif:

```xaml
<Switch IsToggled="true"/>
```

Un `Switch` peut également être créé dans le code:

```csharp
Switch switchControl = new Switch { IsToggled = true };
```

## <a name="switch-appearance"></a>Changer l’apparence

En plus des [`Switch`](xref:Xamarin.Forms.Switch) propriétés qui héritent de la [`View`](xref:Xamarin.Forms.View) classe, `Switch` définit `OnColor` également les propriétés `ThumbColor` et. La `OnColor` propriété peut être définie pour définir la `Switch` couleur lorsqu’elle est basculée vers son état **on** , et la `ThumbColor` propriété peut être définie pour définir le `Color` du curseur de basculement. L’exemple suivant montre comment instancier un `Switch` en XAML avec ces propriétés définies:

```xaml
<Switch OnColor="Orange"
        ThumbColor="Green" />
```

Les propriétés peuvent également être définies lors de la `Switch` création d’un dans le code:

```csharp
Switch switch = new Switch { OnColor = Color.Orange, ThumbColor = Color.Green };
```

La capture d’écran suivante `Switch` montre les États de basculement activés et désactivés `ThumbColor` , avec les `OnColor` propriétés et définies:

![Capture d’écran des commutateurs activés et désactivés, sur iOS et Android](switch-images/switch-states-colors.png "Commutateurs sur iOS et Android")

## <a name="respond-to-a-switch-state-change"></a>Répondre à une modification de l’état du commutateur

Lorsque la `IsToggled` propriété change, soit par manipulation de l’utilisateur, soit quand une `IsToggled` application définit la `Toggled` propriété, l’événement se déclenche. Un gestionnaire d’événements pour cet événement peut être enregistré pour répondre à la modification:

```xaml
<Switch Toggled="OnToggled" />
```

Le fichier code-behind contient le gestionnaire pour le `Toggled` événement :

```csharp
void OnToggled(object sender, ToggledEventArgs e)
{
    // Perform an action after examining e.Value
}
```

L' `sender` argument dans le gestionnaire d’événements `Switch` est chargé de déclencher cet événement. Vous pouvez utiliser la `sender` propriété pour accéder à `Switch` l’objet, ou pour faire la `Switch` distinction entre plusieurs objets `Toggled` qui partagent le même gestionnaire d’événements.

Le `Toggled` gestionnaire d’événements peut également être assigné dans le code:

```csharp
Switch switchControl = new Switch {...};
switchControl.Toggled += (sender, e) =>
{
    // Perform an action after examining e.Value
}
```

## <a name="data-bind-a-switch"></a>Liaison de données d’un commutateur

Le `Toggled` gestionnaire d’événements peut être éliminé à l’aide de la liaison de données et des `Switch` déclencheurs pour répondre à des États bascule modifiés.

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

Dans cet exemple, le [`Label`](xref:Xamarin.Forms.Label) utilise une expression de liaison dans `DataTrigger` un pour `Switch` surveiller `IsToggled` la propriété du nommé `styleSwitch`. Lorsque cette propriété devient `true`, les `FontAttributes` propriétés `FontSize` et de `Label` sont modifiées. Lorsque la `IsToggled` propriété retourne à `false`, `FontAttributes` lespropriétés`FontSize` et de sontrétabliesàleurétatinitial.`Label`

Pour plus d’informations sur les déclencheurs, consultez [déclencheurs Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-switch"></a>Désactiver un commutateur

Une application peut entrer dans un État où `Switch` le basculement n’est pas une opération valide. Dans ce cas, le `Switch` peut être désactivé en affectant `IsEnabled` à `false`sa propriété la valeur. Cela empêchera les utilisateurs de manipuler le `Switch`.

## <a name="related-links"></a>Liens connexes

* [Basculer les démonstrations](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)
* [Déclencheurs Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md)
