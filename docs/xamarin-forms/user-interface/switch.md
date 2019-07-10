---
title: Commutateur de Xamarin.Forms
description: Le commutateur Xamarin.Forms est un type de bouton qui peut être manipulé par l’utilisateur de basculer entre et désactiver les États. Cet article explique comment utiliser la classe de commutateur pour afficher un élément d’interface utilisateur bascule si celle-ci.
ms.prod: xamarin
ms.assetId: B2F9CC65-481B-4323-8E77-C6BE29C90DE9
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/03/2019
ms.openlocfilehash: 22a17f9a916d94a3a0f44a451512de43c943e95a
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675043"
---
# <a name="xamarinforms-switch"></a>Commutateur de Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/SwitchDemos)

Xamarin.Forms [ `Switch` ](xref:Xamarin.Forms.Switch) est un bouton bascule horizontale qui peut être manipulé par l’utilisateur de basculer entre et désactiver les États, qui sont représentées par un `boolean` valeur. Le `Switch` hérite de la classe [ `View` ](xref:Xamarin.Forms.View).

La capture d’écran suivante montre un `Switch` dans contrôler son **sur** et **hors** activer/désactiver les États sur iOS et Android :

![Capture d’écran de commutateurs dans et désactiver les États, sur iOS et Android](switch-images/switch-states-default.png "bascule sur iOS et Android")

Le `Switch` contrôle définit deux propriétés :

* [`OnColor`](xref:Xamarin.Forms.Switch.OnColor) est un `Color` qui affecte la `Switch` est restitué dans basculé, ou **sur**, l’état.
* [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) est un `boolean` valeur qui indique si le `Switch` est **sur**.

Ces propriétés sont soutenues par une [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objet, ce qui signifie que le `Switch` styles peuvent leur être et être la cible des liaisons de données.

Le `Switch` contrôle définit un `Toggled` événement est déclenché quand le `IsToggled` modifications apportées aux propriétés, soit via la manipulation de l’utilisateur, ou lorsqu’une application définit le `IsToggled` propriété. Le `ToggledEventArgs` objet qui accompagne le `Toggled` événement a une propriété unique nommée `Value`, de type `bool`. Lorsque l’événement est déclenché, la valeur de la `Value` propriété reflète la nouvelle valeur de la `IsToggled` propriété.

## <a name="create-a-switch"></a>Créer un commutateur

Un `Switch` peut être instanciée dans XAML. Son `IsToggled` propriété peut être définie pour activer/désactiver le `Switch`. Par défaut, le `IsToggled` propriété est `false`. L’exemple suivant montre comment instancier un `Switch` dans XAML avec le paramètre facultatif `IsToggled` jeu de propriétés :

```xaml
<Switch IsToggled="true"/>
```

Un `Switch` peut également être créé dans le code :

```csharp
Switch switch = new Switch { IsToggled = true };
```

### <a name="switch-style-properties"></a>Propriétés de style de commutateur

Le `OnColor` propriété permettre être configurée pour définir le `Switch` de couleur lorsqu’il est activé à son **sur** état. L’exemple suivant montre comment instancier un `Switch` dans XAML avec la `OnColor` jeu de propriétés :

```xaml
<Switch OnColor="Orange" />
```

Le `OnColor` propriété peut également être définie lors de la création un `Switch` dans le code :

```csharp
Switch switch = new Switch { OnColor = Color.Orange };
```

La capture d’écran suivante montre le `Switch` dans son **sur** et **hors** activer/désactiver les États, avec le `OnColor` propriété définie sur `Color.Orange` sur iOS et Android :

![Capture d’écran de commutateurs dans et désactiver les États, sur iOS et Android](switch-images/switch-states-oncolor.png "bascule sur iOS et Android")

## <a name="respond-to-a-switch-state-change"></a>Répondre à un changement d’état de commutateur

Lorsque le `IsToggled` modifications apportées aux propriétés, soit via la manipulation de l’utilisateur, ou lorsqu’une application définit le `IsToggled` propriété, le `Toggled` se déclenche des événements. Un gestionnaire d’événements pour cet événement peut être inscrit pour répondre à la modification :

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

Le `sender` argument dans le Gestionnaire d’événements est le `Switch` chargé de déclencher cet événement. Vous pouvez utiliser la `sender` propriété pour accéder à la `Switch` objet, ou faire la distinction entre plusieurs `Switch` objets partagent la même `Toggled` Gestionnaire d’événements.

Le `Toggled` Gestionnaire d’événements peut également être attribué dans le code :

```csharp
Switch switch = new Switch {...};
switch.Toggled += (sender, e) =>
{
    // Perform an action after examining e.Value
}
```

## <a name="data-bind-a-switch"></a>Un commutateur de lier des données

Le `Toggled` Gestionnaire d’événements peut être éliminé en utilisant la liaison de données et des déclencheurs pour répondre à une `Switch` états bascule de modification.

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

Dans cet exemple, le [ `Label` ](xref:Xamarin.Forms.Label) utilise une expression de liaison dans un `DataTrigger` pour surveiller le `IsToggled` propriété de la `Switch` nommé `styleSwitch`. Lorsque cette propriété devient `true`, le `FontAttributes` et `FontSize` propriétés de la `Label` sont modifiés. Lorsque le `IsToggled` propriété renvoie vers `false`, le `FontAttributes` et `FontSize` propriétés de la `Label` sont rétablies à leur état initial.

Pour plus d’informations sur les déclencheurs, consultez [Xamarin.Forms déclencheurs](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="disable-a-switch"></a>Désactiver un commutateur

Une application peut entrer dans un état où le `Switch` est affiché/masqué n’est pas une opération valide. Dans ce cas, le `Switch` peut être désactivée en définissant son `IsEnabled` propriété `false`. Cela empêchera les utilisateurs d’être en mesure de manipuler le `Switch`.

## <a name="related-links"></a>Liens connexes

* [Démonstrations de commutateur](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/SwitchDemos)
* [Déclencheurs de Xamarin.Forms](~/xamarin-forms/app-fundamentals/triggers.md)
