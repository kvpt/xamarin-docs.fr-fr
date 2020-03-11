---
title: Déclencheurs Xamarin.Forms
description: Cet article explique comment utiliser des déclencheurs Xamarin.Forms pour répondre aux modifications de l’interface utilisateur avec du code XAML. Les déclencheurs vous permettent d’exprimer des actions de manière déclarative en XAML. Les actions en question modifient l’apparence des contrôles en fonction des événements ou des modifications apportées aux propriétés.
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2020
ms.openlocfilehash: bf9c06dae0df7da1cc69a85d8436376494039959
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78912081"
---
# <a name="xamarinforms-triggers"></a>Déclencheurs Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)

Les déclencheurs vous permettent d’exprimer des actions de manière déclarative en XAML. Les actions en question modifient l’apparence des contrôles en fonction des événements ou des modifications apportées aux propriétés. En outre, les déclencheurs d’État, qui sont un groupe spécialisé de déclencheurs, définissent quand un [`VisualState`](xref:Xamarin.Forms.VisualState) doit être appliqué.

Vous pouvez affecter directement un déclencheur à un contrôle, ou vous pouvez l’ajouter à un dictionnaire de ressources de niveau page ou de niveau application afin de l’appliquer à plusieurs contrôles.

## <a name="property-triggers"></a>Déclencheurs de propriété

Un déclencheur simple peut être exprimé entièrement en XAML, en ajoutant un élément `Trigger` à la collection de déclencheurs d’un contrôle.
Cet exemple montre un déclencheur qui modifie la couleur d’arrière-plan d’un `Entry` lorsqu’il reçoit le focus :

```xaml
<Entry Placeholder="enter name">
    <Entry.Triggers>
        <Trigger TargetType="Entry"
                 Property="IsFocused" Value="True">
            <Setter Property="BackgroundColor" Value="Yellow" />
            <!-- multiple Setters elements are allowed -->
        </Trigger>
    </Entry.Triggers>
</Entry>
```

Les parties importantes de la déclaration d’un déclencheur sont :

- **TargetType** : type du contrôle auquel le déclencheur est appliqué.

- **Property** : propriété du contrôle qui est supervisé.

- **Value** : valeur qui entraîne l’activation du déclencheur, pour la propriété supervisée.

- **Setter** : collection d’éléments `Setter` qui peuvent être ajoutés lorsque la condition de déclenchement est remplie. Vous devez spécifier le `Property` et le `Value` à définir.

- **EnterActions et ExitActions** (non montrés ici) : sont écrits en code et peuvent être utilisés en plus des éléments `Setter` (ou à leur place). Ils sont [décrits ci-dessous](#enteractions-and-exitactions).

### <a name="applying-a-trigger-using-a-style"></a>Application d’un déclencheur à l’aide d’un style

Les déclencheurs peuvent également être ajoutés à une déclaration `Style` dans le `ResourceDictionary` d’un contrôle, d’une page ou d’une d’application. Cet exemple déclare un style implicite (c’est-à-dire qu’aucun `Key` n’est défini), ce qui signifie qu’il s’applique à tous les contrôles `Entry` de la page.

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <Style TargetType="Entry">
                        <Style.Triggers>
                <Trigger TargetType="Entry"
                         Property="IsFocused" Value="True">
                    <Setter Property="BackgroundColor" Value="Yellow" />
                    <!-- multiple Setters elements are allowed -->
                </Trigger>
            </Style.Triggers>
        </Style>
    </ResourceDictionary>
</ContentPage.Resources>
```

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
            <!-- multiple Setters elements are allowed -->
        </DataTrigger>
    </Button.Triggers>
</Button>
```

> [!TIP]
> Lorsque vous évaluez `Path=Text.Length`, fournissez toujours une valeur par défaut pour la propriété cible (par exemple, `Text=""`), car sinon, sa valeur sera `null` et le déclencheur ne fonctionnera pas comme prévu.

En plus de spécifier des `Setter`, vous pouvez également fournir [`EnterActions` et `ExitActions`](#enteractions-and-exitactions).

## <a name="event-triggers"></a>Déclencheurs d’événement

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

- Si vous le souhaitez, vous pouvez exposer des propriétés qui peuvent être définies dans le code XAML quand le déclencheur est déclaré. Pour obtenir un exemple, consultez la classe `VisualElementPopTriggerAction` dans l’exemple d’application associé.

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

Le déclencheur d’événements peut ensuite être consommé à partir du code XAML :

```xaml
<EventTrigger Event="TextChanged">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

Soyez prudent lorsque vous partagez des déclencheurs dans un `ResourceDictionary`. En effet, une même instance sera partagée entre plusieurs contrôles, donc tout état configuré une fois sera appliqué à tous les autres contrôles.

Notez que les déclencheurs d’événements ne prennent pas en charge `EnterActions` et `ExitActions` qui sont [décrits plus bas](#enteractions-and-exitactions).

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

![](triggers-images/multi-requireall.png "MultiTrigger Examples")

## <a name="enteractions-and-exitactions"></a>EnterActions et ExitActions

Une autre façon d’implémenter des modifications lorsqu’un déclencheur est activé est d’ajouter des collections `EnterActions` et `ExitActions`, et de spécifier des implémentations `TriggerAction<T>`.

La collection [`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions) permet de définir un `IList` d’objets [`TriggerAction`](xref:Xamarin.Forms.TriggerAction) à appeler quand la condition de déclenchement est remplie. La collection [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions) permet de définir un `IList` d’objets `TriggerAction` à appeler quand la condition de déclenchement n’est plus remplie.

> [!NOTE]
> Les objets [`TriggerAction`](xref:Xamarin.Forms.TriggerAction) définis dans les collections `EnterActions` et `ExitActions` sont ignorés par la classe [`EventTrigger`](xref:Xamarin.Forms.EventTrigger).    

Vous pouvez fournir *à la fois* `EnterActions` et `ExitActions` ainsi que des `Setter` dans un déclencheur. Sachez toutefois que les `Setter` sont appelés immédiatement (ils n’attendent pas la fin de `EnterAction` ou de `ExitAction`). Vous pouvez également tout effectuer dans le code sans utiliser de `Setter`.

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
    public int StartsFrom { set; get; }

    protected override void Invoke(VisualElement sender)
    {
        sender.Animate("FadeTriggerAction", new Animation((d) =>
        {
            var val = StartsFrom == 1 ? d : 1 - d;
            // so i was aiming for a different color, but then i liked the pink :)
            sender.BackgroundColor = Color.FromRgb(1, val, 1);
        }),
        length: 1000, // milliseconds
        easing: Easing.Linear);
    }
}
```

## <a name="state-triggers"></a>Déclencheurs d’État

Les déclencheurs d’État ont été introduits dans Xamarin. Forms 4,5 et sont un groupe spécialisé de déclencheurs qui définissent les conditions dans lesquelles un [`VisualState`](xref:Xamarin.Forms.VisualState) doit être appliqué. Toutefois, ils sont actuellement expérimentaux et ne peuvent être utilisés qu’en ajoutant la ligne de code suivante à votre fichier *app.Xaml.cs* :

```csharp
Device.SetFlags(new string[]{ "StateTriggers_Experimental" });
```

Les déclencheurs d’État sont ajoutés à la collection [`StateTriggers`](xref:Xamarin.Forms.VisualState.StateTriggers) d’un [`VisualState`](xref:Xamarin.Forms.VisualState). Cette collection peut contenir un seul déclencheur d’État ou plusieurs déclencheurs d’État. Une [`VisualState`](xref:Xamarin.Forms.VisualState) est appliquée quand un déclencheur d’état de la collection est actif.

Lorsque vous utilisez des déclencheurs d’État pour contrôler les États visuels, Xamarin. Forms utilise les règles de précédence suivantes pour déterminer le déclencheur (et le [`VisualState`](xref:Xamarin.Forms.VisualState)correspondant) à activer :

1. Tout déclencheur qui dérive de [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase).
1. Une [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) activée en raison de la satisfaction de la condition de [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowWidth) .
1. Une [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) activée en raison de la satisfaction de la condition de [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) .

Si plusieurs déclencheurs sont actifs simultanément (par exemple, deux déclencheurs personnalisés), le premier déclencheur déclaré dans le balisage est prioritaire.

> [!NOTE]
> Les déclencheurs d’État peuvent être définis dans un [`Style`](xref:Xamarin.Forms.Style), ou directement sur des éléments.

Pour plus d’informations sur les États visuels, consultez [Xamarin. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

### <a name="state-trigger"></a>Déclencheur d’État

La classe [`StateTrigger`](xref:Xamarin.Forms.StateTrigger) , qui dérive de la classe [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) , a une propriété [`IsActive`](xref:Xamarin.Forms.StateTrigger.IsActive) pouvant être liée. Un `StateTrigger` déclenche une modification [`VisualState`](xref:Xamarin.Forms.VisualState) lorsque la propriété `IsActive` change de valeur.

La classe [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) , qui est la classe de base pour tous les déclencheurs d’État, a une propriété [`IsActive`](xref:Xamarin.Forms.StateTriggerBase.IsActive) et un événement [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) . Cet événement se déclenche chaque fois qu’une modification de [`VisualState`](xref:Xamarin.Forms.VisualState) se produit.

> [!IMPORTANT]
> La propriété pouvant être liée [`StateTrigger.IsActive`](xref:Xamarin.Forms.StateTrigger.IsActive) masque la propriété héritée [`StateTriggerBase.IsActive`](xref:Xamarin.Forms.StateTriggerBase.IsActive) .

L’exemple de code XAML suivant montre une [`Style`](xref:Xamarin.Forms.Style) qui comprend des objets [`StateTrigger`](xref:Xamarin.Forms.StateTrigger) :

```xaml
<Style TargetType="Grid">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="Checked">
                    <VisualState.StateTriggers>
                        <StateTrigger IsActive="{Binding IsToggled}"
                                      IsActiveChanged="OnCheckedStateIsActiveChanged" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Black" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Unchecked">
                    <VisualState.StateTriggers>
                        <StateTrigger IsActive="{Binding IsToggled, Converter={StaticResource inverseBooleanConverter}}"
                                      IsActiveChanged="OnUncheckedStateIsActiveChanged" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="White" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

Dans cet exemple, le [`Style`](xref:Xamarin.Forms.Style) implicite cible [`Grid`](xref:Xamarin.Forms.Grid) objets. Lorsque la propriété `IsToggled` de l’objet lié est `true`, la couleur d’arrière-plan de la `Grid` est définie sur noir. Lorsque la propriété `IsToggled` de l’objet lié devient `false`, une modification [`VisualState`](xref:Xamarin.Forms.VisualState) est déclenchée et la couleur d’arrière-plan de la `Grid` devient blanche.

En outre, chaque fois qu’une [`VisualState`](xref:Xamarin.Forms.VisualState) modification se produit, l’événement [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) pour le `VisualState` est déclenché. Chaque `VisualState` inscrit un gestionnaire d’événements pour cet événement :

```csharp
void OnCheckedStateIsActiveChanged(object sender, EventArgs e)
{
    StateTriggerBase stateTrigger = sender as StateTriggerBase;
    Console.WriteLine($"Checked state active: {stateTrigger.IsActive}");
}

void OnUncheckedStateIsActiveChanged(object sender, EventArgs e)
{
    StateTriggerBase stateTrigger = sender as StateTriggerBase;
    Console.WriteLine($"Unchecked state active: {stateTrigger.IsActive}");
}
```

Dans cet exemple, lorsqu’un gestionnaire pour l’événement [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) est déclenché, le gestionnaire indique si le [`VisualState`](xref:Xamarin.Forms.VisualState) est actif ou non. Par exemple, les messages suivants sont générés dans la fenêtre de console lors du passage de l’état visuel `Checked` à l’état visuel `Unchecked` :

```
Checked state active: False
Unchecked state active: True
```

> [!NOTE]
> Les déclencheurs d’état personnalisé peuvent être créés en dérivant de la classe [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) .

### <a name="adaptive-trigger"></a>Déclencheur adaptatif

Un [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) déclenche une modification [`VisualState`](xref:Xamarin.Forms.VisualState) quand la fenêtre est une hauteur ou une largeur spécifiée. Ce déclencheur a deux propriétés pouvant être liées :

- [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight), de type `double`, qui indique la hauteur de fenêtre minimale à laquelle la [`VisualState`](xref:Xamarin.Forms.VisualState) doit être appliquée.
- [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight), de type `double`, qui indique la largeur de fenêtre minimale à laquelle la [`VisualState`](xref:Xamarin.Forms.VisualState) doit être appliquée.

> [!NOTE]
> Le [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) dérive de la classe [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) et peut donc attacher un gestionnaire d’événements à l’événement [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) .

L’exemple de code XAML suivant montre une [`Style`](xref:Xamarin.Forms.Style) qui comprend des objets [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) :

```xaml
<Style TargetType="StackLayout">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="Vertical">
                    <VisualState.StateTriggers>
                        <AdaptiveTrigger MinWindowWidth="0" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="Orientation"
                                Value="Vertical" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Horizontal">
                    <VisualState.StateTriggers>
                        <AdaptiveTrigger MinWindowWidth="800" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="Orientation"
                                Value="Horizontal" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

Dans cet exemple, le [`Style`](xref:Xamarin.Forms.Style) implicite cible [`StackLayout`](xref:Xamarin.Forms.StackLayout) objets. Lorsque la largeur de la fenêtre est comprise entre 0 et 800 unités indépendantes du périphérique, `StackLayout` objets auxquels le `Style` est appliqué auront une orientation verticale. Lorsque la largeur de la fenêtre est > = 800 unités indépendantes du périphérique, la modification [`VisualState`](xref:Xamarin.Forms.VisualState) est déclenchée et l’orientation de la `StackLayout` passe à horizontal :

![StackLayout vertical VisualState](triggers-images/adaptivetrigger-vertical.png "Exemple AdaptiveTrigger")
![StackLayout horizontal VisualState](triggers-images/adaptivetrigger-horizontal.png "Exemple AdaptiveTrigger")

Les propriétés [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) et [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) peuvent être utilisées indépendamment ou conjointement. Le code XAML suivant montre un exemple de définition des deux propriétés :

```xaml
<AdaptiveTrigger MinWindowWidth="800"
                 MinWindowHeight="1200"/>
```

Dans cet exemple, le [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) indique que le [`VisualState`](xref:Xamarin.Forms.VisualState) correspondant sera appliqué lorsque la largeur de fenêtre active est > = 800 unités indépendantes du périphérique et que la hauteur de la fenêtre active est 1200 > unités indépendantes du périphérique.

### <a name="compare-state-trigger"></a>Déclencheur d’état de comparaison

Le [`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger) déclenche une modification de [`VisualState`](xref:Xamarin.Forms.VisualState) lorsqu’une propriété est égale à une valeur spécifique. Ce déclencheur a deux propriétés pouvant être liées :

- [`Property`](xref:Xamarin.Forms.CompareStateTrigger.Property), de type `object`, qui indique la propriété comparée par le déclencheur.
- [`Value`](xref:Xamarin.Forms.CompareStateTrigger.Value), de type `object`, qui indique la valeur à laquelle la [`VisualState`](xref:Xamarin.Forms.VisualState) doit être appliquée.

> [!NOTE]
> Le [`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger) dérive de la classe [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) et peut donc attacher un gestionnaire d’événements à l’événement [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) .

L’exemple de code XAML suivant montre une [`Style`](xref:Xamarin.Forms.Style) qui comprend des objets [`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger) :

```xaml
<Style TargetType="Grid">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="Checked">
                    <VisualState.StateTriggers>
                        <CompareStateTrigger Property="{Binding Source={x:Reference checkBox}, Path=IsChecked}"
                                             Value="True" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Black" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Unchecked">
                    <VisualState.StateTriggers>
                        <CompareStateTrigger Property="{Binding Source={x:Reference checkBox}, Path=IsChecked}"
                                             Value="False" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="White" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
...
<Grid>
    <Frame BackgroundColor="White"
           CornerRadius="12"
           Margin="24"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <StackLayout Orientation="Horizontal">
            <CheckBox x:Name="checkBox"
                      VerticalOptions="Center" />
            <Label Text="Check the CheckBox to modify the Grid background color."
                   VerticalOptions="Center" />
        </StackLayout>
    </Frame>
</Grid>
```

Dans cet exemple, le [`Style`](xref:Xamarin.Forms.Style) implicite cible [`Grid`](xref:Xamarin.Forms.Grid) objets. Lorsque la propriété [`IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) du [`CheckBox`](xref:Xamarin.Forms.CheckBox) est `false`, la couleur d’arrière-plan du `Grid` est définie sur blanc. Lorsque la propriété `CheckBox.IsChecked` devient `true`, une modification de [`VisualState`](xref:Xamarin.Forms.VisualState) est déclenchée et la couleur d’arrière-plan de la `Grid` devient noire :

[![Capture d’écran d’un changement d’état visuel déclenché, sur iOS et android](triggers-images/comparestatetrigger-unchecked.png "Exemple CompareStateTrigger")](triggers-images/comparestatetrigger-unchecked-large.png#lightbox "Exemple CompareStateTrigger")
[ ![capture d’écran d’un changement d’état visuel déclenché, sur iOS et Android](triggers-images/comparestatetrigger-checked.png "Exemple CompareStateTrigger")](triggers-images/comparestatetrigger-unchecked-large.png#lightbox "Exemple CompareStateTrigger")

### <a name="device-state-trigger"></a>Déclencheur d’état de l’appareil

Le [`DeviceStateTrigger`](xref:Xamarin.Forms.DeviceStateTrigger) déclenche une modification [`VisualState`](xref:Xamarin.Forms.VisualState) en fonction de la plateforme d’appareil sur laquelle l’application s’exécute. Ce déclencheur a une propriété pouvant être liée unique :

- [`Device`](xref:Xamarin.Forms.DeviceStateTrigger.Device), de type `string`, qui indique la plateforme de l’appareil sur laquelle la [`VisualState`](xref:Xamarin.Forms.VisualState) doit être appliquée.

> [!NOTE]
> Le [`DeviceStateTrigger`](xref:Xamarin.Forms.DeviceStateTrigger) dérive de la classe [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) et peut donc attacher un gestionnaire d’événements à l’événement [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) .

L’exemple de code XAML suivant montre une [`Style`](xref:Xamarin.Forms.Style) qui comprend des objets `DeviceStateTrigger` :

```xaml
<Style x:Key="DeviceStateTriggerPageStyle"
       TargetType="ContentPage">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="iOS">
                    <VisualState.StateTriggers>
                        <DeviceStateTrigger Device="iOS" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Silver" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Android">
                    <VisualState.StateTriggers>
                        <DeviceStateTrigger Device="Android" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="#2196F3" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="UWP">
                    <VisualState.StateTriggers>
                        <DeviceStateTrigger Device="UWP" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Aquamarine" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

Dans cet exemple, le [`Style`](xref:Xamarin.Forms.Style) explicite cible [`ContentPage`](xref:Xamarin.Forms.ContentPage) objets. `ContentPage` objets qui consomment le style ont la couleur d’arrière-plan Silver sur iOS sur iOS, bleu pâle sur Android et vert eau marine sur UWP. Les captures d’écran suivantes montrent les pages obtenues sur iOS et Android :

[![Capture d’écran d’un changement d’état visuel déclenché, sur iOS et Android](triggers-images/devicestatetrigger.png "Exemple DeviceStateTrigger")](triggers-images/devicestatetrigger-large.png#lightbox "Exemple DeviceStateTrigger")

### <a name="orientation-state-trigger"></a>Déclencheur d’état d’orientation

Le [`OrientationStateTrigger`](xref:Xamarin.Forms.OrientationStateTrigger) déclenche une modification de [`VisualState`](xref:Xamarin.Forms.VisualState) lorsque l’orientation de l’appareil change. Ce déclencheur a une propriété pouvant être liée unique :

- [`Orientation`](xref:Xamarin.Forms.OrientationStateTrigger.Orientation), de type [`DeviceOrientation`](xref:Xamarin.Forms.Internals.DeviceOrientation), qui indique l’orientation à laquelle la [`VisualState`](xref:Xamarin.Forms.VisualState) doit être appliquée.

> [!NOTE]
> Le [`OrientationStateTrigger`](xref:Xamarin.Forms.OrientationStateTrigger) dérive de la classe [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) et peut donc attacher un gestionnaire d’événements à l’événement [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) .

L’exemple de code XAML suivant montre une [`Style`](xref:Xamarin.Forms.Style) qui comprend des objets `OrientationStateTrigger` :

```xaml
<Style x:Key="OrientationStateTriggerPageStyle"
       TargetType="ContentPage">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="Portrait">
                    <VisualState.StateTriggers>
                        <OrientationStateTrigger Orientation="Portrait" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Silver" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Landscape">
                    <VisualState.StateTriggers>
                        <OrientationStateTrigger Orientation="Landscape" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="White" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

Dans cet exemple, le [`Style`](xref:Xamarin.Forms.Style) explicite cible [`ContentPage`](xref:Xamarin.Forms.ContentPage) objets. `ContentPage` objets qui consomment le style, définissez leur couleur d’arrière-plan sur Silver lorsque l’orientation est portrait, et définissez leur couleur d’arrière-plan sur blanc lorsque l’orientation est paysage.

## <a name="related-links"></a>Liens connexes

- [Exemples de déclencheurs](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)
- [Gestionnaire d’état visuel Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
- [API déclencheur Xamarin. Forms](xref:Xamarin.Forms.TriggerAction`1)
