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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79305128"
---
# <a name="xamarinforms-triggers"></a>Déclencheurs Xamarin.Forms

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)

Les déclencheurs vous permettent d’exprimer des actions de manière déclarative en XAML. Les actions en question modifient l’apparence des contrôles en fonction des événements ou des modifications apportées aux propriétés. En outre, les déclencheurs de l’État, qui [`VisualState`](xref:Xamarin.Forms.VisualState) sont un groupe spécialisé de déclencheurs, définissent quand un doit être appliqué.

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

### <a name="applying-a-trigger-using-a-style"></a>Appliquer un déclencheur à l’aide d’un style

Les déclencheurs peuvent également être ajoutés à une déclaration `Style` dans le `ResourceDictionary` d’un contrôle, d’une page ou d’une d’application. Cet exemple déclare un style implicite (c’est-à-dire qu’aucun n’est `Key` réglé) ce qui signifie qu’il s’appliquera à tous les `Entry` contrôles de la page.

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

En plus de `Setter`spécifier s, vous pouvez également fournir [ `EnterActions` et `ExitActions` ](#enteractions-and-exitactions).

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

Notez que les déclencheurs d’événements ne prennent pas en charge `EnterActions` et  qui sont `ExitActions`    [décrits plus bas](#enteractions-and-exitactions).

## <a name="multi-triggers"></a>Déclencheurs multi

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

La [`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions) collection est utilisée `IList` [`TriggerAction`](xref:Xamarin.Forms.TriggerAction) pour définir un des objets qui seront invoqués lorsque la condition de déclenchement est remplie. La [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions) collection est utilisée `IList` `TriggerAction` pour définir un des objets qui seront invoqués après que la condition de déclenchement n’est plus remplie.

> [!NOTE]
> Les [`TriggerAction`](xref:Xamarin.Forms.TriggerAction) objets définis `ExitActions` dans le [`EventTrigger`](xref:Xamarin.Forms.EventTrigger) et les `EnterActions` collections sont ignorés par la classe.    

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

## <a name="state-triggers"></a>Déclencheurs de l’État

Les déclencheurs de l’État ont été introduits dans Xamarin.Forms 4.5, [`VisualState`](xref:Xamarin.Forms.VisualState) et sont un groupe spécialisé de déclencheurs qui définissent les conditions dans lesquelles un doit être appliqué. Cependant, ils sont actuellement expérimentaux et ne peuvent être utilisés qu’en ajoutant la ligne de code suivante à votre fichier *App.xaml.cs* :

```csharp
Device.SetFlags(new string[]{ "StateTriggers_Experimental" });
```

Les déclencheurs d’état sont ajoutés à la [`StateTriggers`](xref:Xamarin.Forms.VisualState.StateTriggers) collection d’un [`VisualState`](xref:Xamarin.Forms.VisualState). Cette collection peut contenir un déclencheur d’état unique, ou plusieurs déclencheurs d’état. A [`VisualState`](xref:Xamarin.Forms.VisualState) sera appliqué lorsque les déclencheurs d’état de la collection sont actifs.

Lors de l’utilisation des déclencheurs d’état pour contrôler les états visuels, [`VisualState`](xref:Xamarin.Forms.VisualState)Xamarin.Forms utilise les règles de préséance suivantes pour déterminer quel déclencheur (et correspondant) sera actif :

1. Tout déclencheur qui [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase)dérive de .
1. Un [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) activé en [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowWidth) raison de la condition étant remplie.
1. Un [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) activé en [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) raison de la condition étant remplie.

Si plusieurs déclencheurs sont simultanément actifs (par exemple, deux déclencheurs personnalisés), le premier déclencheur déclaré dans la balisage prime.

> [!NOTE]
> Les déclencheurs d’état [`Style`](xref:Xamarin.Forms.Style)peuvent être réglés dans un, ou directement sur des éléments.

Pour plus d’informations sur les états visuels, voir [Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

### <a name="state-trigger"></a>Déclencheur de l’État

La [`StateTrigger`](xref:Xamarin.Forms.StateTrigger) classe, qui dérive [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) de la [`IsActive`](xref:Xamarin.Forms.StateTrigger.IsActive) classe, a une propriété liant. Un `StateTrigger` déclenche [`VisualState`](xref:Xamarin.Forms.VisualState) un changement `IsActive` lorsque la propriété change de valeur.

La [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) classe, qui est la classe de base [`IsActive`](xref:Xamarin.Forms.StateTriggerBase.IsActive) pour [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) tous les déclencheurs de l’État, a une propriété et un événement. Cet événement s’déclenche chaque fois qu’un [`VisualState`](xref:Xamarin.Forms.VisualState) changement se produit.

> [!IMPORTANT]
> La [`StateTrigger.IsActive`](xref:Xamarin.Forms.StateTrigger.IsActive) propriété liant cache [`StateTriggerBase.IsActive`](xref:Xamarin.Forms.StateTriggerBase.IsActive) la propriété héritée.

L’exemple XAML [`Style`](xref:Xamarin.Forms.Style) suivant [`StateTrigger`](xref:Xamarin.Forms.StateTrigger) montre un qui inclut des objets :

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

Dans cet exemple, [`Style`](xref:Xamarin.Forms.Style) [`Grid`](xref:Xamarin.Forms.Grid) les cibles implicites s’opposent. Lorsque `IsToggled` la propriété de `true`l’objet lié `Grid` est, la couleur de fond de la est réglée au noir. Lorsque `IsToggled` la propriété de `false`l’objet lié devient , un [`VisualState`](xref:Xamarin.Forms.VisualState) `Grid` changement est déclenché, et la couleur de fond de la devient blanche.

En outre, chaque [`VisualState`](xref:Xamarin.Forms.VisualState) fois qu’un changement se produit, l’événement [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) pour le `VisualState` est tiré. Chacun `VisualState` enregistre un gestionnaire d’événements pour cet événement :

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

Dans cet exemple, lorsqu’un gestionnaire de l’événement [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) [`VisualState`](xref:Xamarin.Forms.VisualState) est congédié, le gestionnaire sort si le gestionnaire est actif ou non. Par exemple, les messages suivants sont la sortie `Checked` vers la `Unchecked` fenêtre de la console lors du passage de l’état visuel à l’état visuel :

```
Checked state active: False
Unchecked state active: True
```

> [!NOTE]
> Les déclencheurs d’état personnalisés peuvent [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) être créés en provenant de la classe.

### <a name="adaptive-trigger"></a>Déclencheur adaptatif

Un [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) déclenche [`VisualState`](xref:Xamarin.Forms.VisualState) un changement lorsque la fenêtre est une hauteur ou une largeur spécifiée. Ce déclencheur a deux propriétés liantes :

- [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight), du `double`type , ce qui indique [`VisualState`](xref:Xamarin.Forms.VisualState) la hauteur minimale de la fenêtre à laquelle le doit être appliqué.
- [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight), du `double`type , ce qui indique [`VisualState`](xref:Xamarin.Forms.VisualState) la largeur minimale de la fenêtre à laquelle le doit être appliqué.

> [!NOTE]
> Le [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) dérive de [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) la classe et peut donc [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) joindre un gestionnaire d’événements à l’événement.

L’exemple XAML [`Style`](xref:Xamarin.Forms.Style) suivant [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) montre un qui inclut des objets :

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

Dans cet exemple, [`Style`](xref:Xamarin.Forms.Style) [`StackLayout`](xref:Xamarin.Forms.StackLayout) les cibles implicites s’opposent. Lorsque la largeur de la fenêtre se situe entre `StackLayout` 0 et `Style` 800 unités indépendantes de l’appareil, les objets auxquels l’appareil est appliqué auront une orientation verticale. Lorsque la largeur de la fenêtre est >800 [`VisualState`](xref:Xamarin.Forms.VisualState) unités indépendantes de `StackLayout` l’appareil, le changement est déclenché, et l’orientation change à l’horizontale :

![Vertical StackLayout VisualState](triggers-images/adaptivetrigger-vertical.png "AdaptiveTrigger exemple")
![Horizontal StackLayout VisualState VisualState](triggers-images/adaptivetrigger-horizontal.png "AdaptiveTrigger exemple")

Les [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) propriétés et les propriétés peuvent être utilisées indépendamment ou en conjonction les unes avec les autres. Le XAML suivant montre un exemple de réglage des deux propriétés:

```xaml
<AdaptiveTrigger MinWindowWidth="800"
                 MinWindowHeight="1200"/>
```

Dans cet exemple, l’indique [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) que le correspondant [`VisualState`](xref:Xamarin.Forms.VisualState) sera appliqué lorsque la largeur actuelle de la fenêtre est >800 unités indépendantes de l’appareil et que la hauteur actuelle de la fenêtre est >unités indépendantes de l’appareil.

### <a name="compare-state-trigger"></a>Comparer le déclencheur de l’état

Le [`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger) déclenche [`VisualState`](xref:Xamarin.Forms.VisualState) une modification lorsqu’une propriété est égale à une valeur spécifique. Ce déclencheur a deux propriétés liantes :

- [`Property`](xref:Xamarin.Forms.CompareStateTrigger.Property), de `object`type , ce qui indique que la propriété est comparée par le déclencheur.
- [`Value`](xref:Xamarin.Forms.CompareStateTrigger.Value), du `object`type , ce qui [`VisualState`](xref:Xamarin.Forms.VisualState) indique la valeur à laquelle le doit être appliqué.

> [!NOTE]
> Le [`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger) dérive de [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) la classe et peut donc [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) joindre un gestionnaire d’événements à l’événement.

L’exemple XAML [`Style`](xref:Xamarin.Forms.Style) suivant [`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger) montre un qui inclut des objets :

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

Dans cet exemple, [`Style`](xref:Xamarin.Forms.Style) [`Grid`](xref:Xamarin.Forms.Grid) les cibles implicites s’opposent. Lorsque [`IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) la propriété [`CheckBox`](xref:Xamarin.Forms.CheckBox) `false`de l’est `Grid` , la couleur de fond de la est réglée au blanc. Lorsque `CheckBox.IsChecked` la `true`propriété [`VisualState`](xref:Xamarin.Forms.VisualState) devient , un changement est `Grid` déclenché, et la couleur de fond de la devient noire:

[![Capture d’écran d’un changement d’état visuel déclenché, sur iOS et Android](triggers-images/comparestatetrigger-unchecked.png "Comparez l’exemple deStateTrigger")](triggers-images/comparestatetrigger-unchecked-large.png#lightbox "Comparez l’exemple deStateTrigger")
[![Capture d’écran d’un changement d’état visuel déclenché, sur iOS et Android](triggers-images/comparestatetrigger-checked.png "Comparez l’exemple deStateTrigger")](triggers-images/comparestatetrigger-unchecked-large.png#lightbox "Comparez l’exemple deStateTrigger")

### <a name="device-state-trigger"></a>Déclencheur de l’état de l’appareil

Les [`DeviceStateTrigger`](xref:Xamarin.Forms.DeviceStateTrigger) déclencheurs [`VisualState`](xref:Xamarin.Forms.VisualState) d’un changement basé sur la plate-forme de l’appareil de l’application est en cours d’exécution sur. Ce déclencheur a une propriété liant unique :

- [`Device`](xref:Xamarin.Forms.DeviceStateTrigger.Device), du `string`type , qui indique [`VisualState`](xref:Xamarin.Forms.VisualState) la plate-forme de l’appareil sur laquelle le doit être appliqué.

> [!NOTE]
> Le [`DeviceStateTrigger`](xref:Xamarin.Forms.DeviceStateTrigger) dérive de [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) la classe et peut donc [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) joindre un gestionnaire d’événements à l’événement.

L’exemple XAML [`Style`](xref:Xamarin.Forms.Style) suivant `DeviceStateTrigger` montre un qui inclut des objets :

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

Dans cet exemple, [`Style`](xref:Xamarin.Forms.Style) [`ContentPage`](xref:Xamarin.Forms.ContentPage) les objets cibles explicites. `ContentPage`objets qui consomment le style définir leur couleur de fond à l’argent sur iOS, à bleu pâle sur Android, et à l’aigue-marine sur UWP. Les captures d’écran suivantes montrent les pages résultantes sur iOS et Android :

[![Capture d’écran d’un changement d’état visuel déclenché, sur iOS et Android](triggers-images/devicestatetrigger.png "Exemple de DeviceStateTrigger")](triggers-images/devicestatetrigger-large.png#lightbox "Exemple de DeviceStateTrigger")

### <a name="orientation-state-trigger"></a>Déclencheur de l’état d’orientation

Le [`OrientationStateTrigger`](xref:Xamarin.Forms.OrientationStateTrigger) déclenche [`VisualState`](xref:Xamarin.Forms.VisualState) un changement lorsque l’orientation de l’appareil change. Ce déclencheur a une propriété liant unique :

- [`Orientation`](xref:Xamarin.Forms.OrientationStateTrigger.Orientation), du [`DeviceOrientation`](xref:Xamarin.Forms.Internals.DeviceOrientation)type , ce qui [`VisualState`](xref:Xamarin.Forms.VisualState) indique l’orientation à laquelle le doit être appliqué.

> [!NOTE]
> Le [`OrientationStateTrigger`](xref:Xamarin.Forms.OrientationStateTrigger) dérive de [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) la classe et peut donc [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) joindre un gestionnaire d’événements à l’événement.

L’exemple XAML [`Style`](xref:Xamarin.Forms.Style) suivant `OrientationStateTrigger` montre un qui inclut des objets :

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

Dans cet exemple, [`Style`](xref:Xamarin.Forms.Style) [`ContentPage`](xref:Xamarin.Forms.ContentPage) les objets cibles explicites. `ContentPage`les objets qui consomment le modèle placent leur couleur de fond à l’argent lorsque l’orientation est portrait, et placent leur couleur de fond au blanc lorsque l’orientation est le paysage.

## <a name="related-links"></a>Liens connexes

- [Exemples de déclencheurs](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)
- [Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)
- [Xamarin.Forms Déclencher API](xref:Xamarin.Forms.TriggerAction`1)
