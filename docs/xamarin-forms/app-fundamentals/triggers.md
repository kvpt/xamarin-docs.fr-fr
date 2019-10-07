---
title: Déclencheurs Xamarin.Forms
description: Cet article explique comment utiliser des déclencheurs Xamarin.Forms pour répondre aux modifications de l’interface utilisateur avec du code XAML. Les déclencheurs vous permettent d’exprimer des actions de manière déclarative en XAML. Les actions en question modifient l’apparence des contrôles en fonction des événements ou des modifications apportées aux propriétés.
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: 66323974fa44f5397e21541595a187ce0ba4d061
ms.sourcegitcommit: 4cf434b126eb7df6b2fd9bb1d71613bf2b6aac0e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2019
ms.locfileid: "71997153"
---
# <a name="xamarinforms-triggers"></a>Déclencheurs Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)

Les déclencheurs vous permettent d’exprimer des actions de manière déclarative en XAML. Les actions en question modifient l’apparence des contrôles en fonction des événements ou des modifications apportées aux propriétés.

Vous pouvez affecter directement un déclencheur à un contrôle, ou vous pouvez l’ajouter à un dictionnaire de ressources de niveau page ou de niveau application afin de l’appliquer à plusieurs contrôles.

Il existe quatre types de déclencheurs :

- [Déclencheur de propriétés](#property) : activé lorsqu’une propriété d’un contrôle est définie sur une valeur particulière.

- [Déclencheur de données](#data) : utilise la liaison de données pour son déclenchement, en se basant sur les propriétés d’un autre contrôle.

- [Déclencheur d’événements](#event) : activé lorsqu’un événement se produit au niveau d’un contrôle.

- [Déclencheur multiple](#multi) : permet de définir plusieurs conditions de déclenchement avant qu’une action ne se produise.

<a name="property" />

## <a name="property-triggers"></a>Déclencheurs de propriété

Un déclencheur simple peut être exprimé entièrement en XAML, en ajoutant un élément `Trigger` à la collection de déclencheurs d’un contrôle.
Cet exemple montre un déclencheur qui modifie la couleur d’arrière-plan d’un `Entry` lorsqu’il reçoit le focus :

```xaml
<Entry Placeholder="enter name">
    <Entry.Triggers>
        <Trigger TargetType="Entry"
             Property="IsFocused" Value="True">
            <Setter Property="BackgroundColor" Value="Yellow" />
        </Trigger>
    </Entry.Triggers>
</Entry>
```

Les parties importantes de la déclaration d’un déclencheur sont :

- **TargetType** : type du contrôle auquel le déclencheur est appliqué.

- **Property** : propriété du contrôle qui est supervisé.

- **Value** : valeur qui entraîne l’activation du déclencheur, pour la propriété supervisée.

- **Setter** : collection d’éléments `Setter` qui peuvent être ajoutés lorsque la condition de déclenchement est remplie. Vous devez spécifier le `Property` et le `Value` à définir.

- **EnterActions et ExitActions** (non montrés ici) : sont écrits en code et peuvent être utilisés en plus des éléments `Setter` (ou à leur place). Ils sont [décrits ci-dessous](#enterexit).

### <a name="applying-a-trigger-using-a-style"></a>Application d’un déclencheur à l’aide d’un style

Les déclencheurs peuvent également être ajoutés à une déclaration `Style` dans le `ResourceDictionary` d’un contrôle, d’une page ou d’une d’application. Cet exemple déclare un style implicite (c’est-à-dire qu’aucun `Key` n’est défini), ce qui signifie qu’il s’applique à tous les contrôles `Entry`de la page.

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <Style TargetType="Entry">
                        <Style.Triggers>
                <Trigger TargetType="Entry"
                         Property="IsFocused" Value="True">
                    <Setter Property="BackgroundColor" Value="Yellow" />
                </Trigger>
            </Style.Triggers>
        </Style>
    </ResourceDictionary>
</ContentPage.Resources>
```

<a name="data" />

## <a name="data-triggers"></a>Déclencheurs de données

Les déclencheurs de données utilisent la liaison de données pour superviser un autre contrôle afin de provoquer l’appel de `Setter`. Au lieu de l’attribut `Property` d’un déclencheur de propriété, définissez l’attribut `Binding` de manière à surveiller la valeur spécifiée.

L’exemple ci-dessous utilise la syntaxe de liaison de données `{Binding Source={x:Reference entry}, Path=Text.Length}`
qui est celle utilisée pour référencer les propriétés d’un autre contrôle. Lorsque la longueur de `entry` est égale à zéro, le déclencheur est activé. Dans cet exemple, le déclencheur désactive le bouton lorsque l’entrée est vide.

```xaml
<!-- the x:Name is referenced below in DataTrigger-->
<!-- tip: make sure to set the Text="" (or some other default) -->
<Entry x:Name="entry"
       Text=""
       Placeholder="required field" />

<Button x:Name="button" Text="Save"
        FontSize="Large"
        HorizontalOptions="Center">
    <Button.Triggers>
        <DataTrigger TargetType="Button"
                     Binding="{Binding Source={x:Reference entry},
                                       Path=Text.Length}"
                     Value="0">
            <Setter Property="IsEnabled" Value="False" />
        </DataTrigger>
    </Button.Triggers>
</Button>
```

> [!TIP]
> Lorsque vous évaluez `Path=Text.Length`, fournissez toujours une valeur par défaut pour la propriété cible (par exemple, `Text=""`), car sinon, sa valeur sera `null` et le déclencheur ne fonctionnera pas comme prévu.

En plus de spécifier des `Setter`, vous pouvez également fournir [`EnterActions` et `ExitActions`](#enterexit).

<a name="event" />

## <a name="event-triggers"></a>Déclencheurs d’événements

L’ élément `EventTrigger` nécessite uniquement une propriété `Event`, comme `"Clicked"` dans l’exemple ci-dessous.

```xaml
<EventTrigger Event="Clicked">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

Notez qu’il n’y a aucun élément `Setter`, mais plutôt une référence à une classe définie par `local:NumericValidationTriggerAction` qui nécessite que `xmlns:local` soit déclaré dans le code XAML de la page :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

La classe implémente `TriggerAction`, ce qui signifie qu’elle doit fournir un remplacement pour la méthode `Invoke` qui est appelée chaque fois que l’événement déclencheur se produit.

Une implémentation d’action de déclencheur doit :

- Implémenter la classe générique `TriggerAction<T>`, avec le paramètre générique correspondant au type de contrôle auquel le déclencheur doit être appliqué. Vous pouvez utiliser des superclasses telles que `VisualElement` pour écrire des actions de déclencheur qui fonctionnent avec un large éventail de contrôles, ou vous pouvez spécifier un type de contrôle comme `Entry`.

- Remplacer la méthode `Invoke` qui est appelée chaque fois que les critères de déclenchement sont remplis.

- Exposez éventuellement des propriétés qui peuvent être définies dans le XAML lorsque le déclencheur est déclaré. Pour obtenir un exemple, consultez la classe `VisualElementPopTriggerAction` dans l’exemple d’application associé.

```csharp
public class NumericValidationTriggerAction : TriggerAction<Entry>
{
    protected override void Invoke (Entry entry)
    {
        double result;
        bool isValid = Double.TryParse (entry.Text, out result);
        entry.TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

Le déclencheur d’événements peut ensuite être consommé à partir de XAML :

```xaml
<EventTrigger Event="TextChanged">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

Soyez prudent lorsque vous partagez des déclencheurs dans un `ResourceDictionary`. En effet, une même instance sera partagée entre plusieurs contrôles, donc tout état configuré une fois sera appliqué à tous les autres contrôles.

Notez que les déclencheurs d’événements ne prennent pas en charge `EnterActions` et `ExitActions` qui sont [décrits plus bas](#enterexit).

<a name="multi" />

## <a name="multi-triggers"></a>Déclencheurs multiples

Un `MultiTrigger` est similaire à un `Trigger` ou à un `DataTrigger`, à ceci près qu’il peut avoir plusieurs conditions. Toutes les conditions doivent être remplies avant que les `Setter` ne soient déclenchés.

Voici un exemple de déclencheur pour un bouton qui lie deux entrées différentes (`email` et `phone`) :

```xaml
<MultiTrigger TargetType="Button">
    <MultiTrigger.Conditions>
        <BindingCondition Binding="{Binding Source={x:Reference email},
                                   Path=Text.Length}"
                               Value="0" />
        <BindingCondition Binding="{Binding Source={x:Reference phone},
                                   Path=Text.Length}"
                               Value="0" />
    </MultiTrigger.Conditions>

  <Setter Property="IsEnabled" Value="False" />
    <!-- multiple Setter elements are allowed -->
</MultiTrigger>
```

La collection `Conditions` peut également contenir des éléments `PropertyCondition` comme ceci :

```xaml
<PropertyCondition Property="Text" Value="OK" />
```

### <a name="building-a-require-all-multi-trigger"></a>Création d’un déclencheur multiple de type « tout exiger »

Le déclencheur multiple ne met à jour son contrôle que lorsque toutes les conditions sont remplies. La condition « toutes les longueurs de champs sont égales à zéro » (par exemple, une page de connexion où toutes les entrées doivent être effectuées) est difficile à tester, car cela nécessite une condition où « Text.Length > 0 », ce qui ne peut pas être exprimé en XAML.

Pour cela, vous devez utiliser un `IValueConverter`. Le code de convertisseur ci-dessous transforme la liaison `Text.Length` en un `bool` qui indique si un champ est vide ou non :

```csharp
public class MultiTriggerConverter : IValueConverter
{
    public object Convert(object value, Type targetType,
        object parameter, CultureInfo culture)
    {
        if ((int)value > 0) // length > 0 ?
            return true;            // some data has been entered
        else
            return false;            // input is empty
    }

    public object ConvertBack(object value, Type targetType,
        object parameter, CultureInfo culture)
    {
        throw new NotSupportedException ();
    }
}
```

Pour utiliser ce convertisseur dans un déclencheur multiple, ajoutez-le d’abord au dictionnaire de ressources de la page (avec une définition d’espace de noms `xmlns:local` personnalisée) :

```xaml
<ResourceDictionary>
   <local:MultiTriggerConverter x:Key="dataHasBeenEntered" />
</ResourceDictionary>
```

Le code XAML est fourni ci-dessous. Notez les différences suivantes par rapport au premier exemple de déclencheur multiple :

- Dans le bouton, `IsEnabled="false"` est défini par défaut.
- Les conditions du déclencheur multiple utilisent le convertisseur pour transformer la valeur `Text.Length` en un `boolean`.
- Lorsque toutes les conditions sont `true`, la méthode setter définit la propriété `IsEnabled` du bouton sur `true`.

```xaml
<Entry x:Name="user" Text="" Placeholder="user name" />

<Entry x:Name="pwd" Text="" Placeholder="password" />

<Button x:Name="loginButton" Text="Login"
        FontSize="Large"
        HorizontalOptions="Center"
        IsEnabled="false">
  <Button.Triggers>
    <MultiTrigger TargetType="Button">
      <MultiTrigger.Conditions>
        <BindingCondition Binding="{Binding Source={x:Reference user},
                              Path=Text.Length,
                              Converter={StaticResource dataHasBeenEntered}}"
                          Value="true" />
        <BindingCondition Binding="{Binding Source={x:Reference pwd},
                              Path=Text.Length,
                              Converter={StaticResource dataHasBeenEntered}}"
                          Value="true" />
      </MultiTrigger.Conditions>
      <Setter Property="IsEnabled" Value="True" />
    </MultiTrigger>
  </Button.Triggers>
</Button>
```

Ces captures d’écran montrent la différence entre les deux exemples de déclencheurs multiples. Dans la partie supérieure des écrans, l’entrée de texte dans un seul `Entry` suffit à activer le bouton **Save** (Enregistrer).
Dans la partie inférieure de l’écran, le bouton **Login** (Connexion) reste inactif jusqu’à ce que les deux champs contiennent des données.

![](triggers-images/multi-requireall.png "Exemples de déclencheurs multiples")

<a name="enterexit" />

## <a name="enteractions-and-exitactions"></a>EnterActions et ExitActions

Une autre façon d’implémenter des modifications lorsqu’un déclencheur est activé est d’ajouter des collections `EnterActions` et `ExitActions`, et de spécifier des implémentations `TriggerAction<T>`.

La collection [`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions) est utilisée pour définir un `IList` d’objets [`TriggerAction`](xref:Xamarin.Forms.TriggerAction) qui seront appelés lorsque la condition de déclenchement sera remplie. La collection [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions) est utilisée pour définir un `IList` d’objets `TriggerAction` qui seront appelés une fois que la condition de déclencheur n’est plus remplie.

> [!NOTE]
> Les objets [`TriggerAction`](xref:Xamarin.Forms.TriggerAction) définis dans les collections `EnterActions` et `ExitActions` sont ignorés par la classe [`EventTrigger`](xref:Xamarin.Forms.EventTrigger) .    

Vous pouvez fournir à la *fois* `EnterActions` et `ExitActions` ainsi que `Setter`S dans un déclencheur, mais sachez que le  est appelé immédiatement (ils n’attendent pas que le `EnterAction` ou `ExitAction` se termine). Vous pouvez également tout effectuer dans le code sans utiliser de `Setter`.

```xaml
<Entry Placeholder="enter job title">
    <Entry.Triggers>
        <Trigger TargetType="Entry"
                 Property="Entry.IsFocused" Value="True">
            <Trigger.EnterActions>
                <local:FadeTriggerAction StartsFrom="0"" />
            </Trigger.EnterActions>

            <Trigger.ExitActions>
                <local:FadeTriggerAction StartsFrom="1" />
            </Trigger.ExitActions>
            <!-- You can use both Enter/Exit and Setter together if required -->
        </Trigger>
    </Entry.Triggers>
</Entry>
```

Comme toujours, lorsqu’une classe est référencée dans du code XAML, vous devez déclarer un espace de noms tel que `xmlns:local`, comme indiqué ici :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

Ce code `FadeTriggerAction` est fourni ci-dessous:

```csharp
public class FadeTriggerAction : TriggerAction<VisualElement>
{
    public FadeTriggerAction() {}

    public int StartsFrom { set; get; }

    protected override void Invoke (VisualElement visual)
    {
            visual.Animate("", new Animation( (d)=>{
                var val = StartsFrom==1 ? d : 1-d;
                visual.BackgroundColor = Color.FromRgb(1, val, 1);

            }),
            length:1000, // milliseconds
            easing: Easing.Linear);
    }
}
```

## <a name="related-links"></a>Liens associés

- [Exemples de déclencheurs](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)
- [Documentation sur les API Xamarin.Forms](xref:Xamarin.Forms.TriggerAction`1)
