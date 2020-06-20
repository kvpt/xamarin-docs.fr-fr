---
title: EventToCommandBehavior réutilisable
description: Les comportements peuvent être utilisés pour associer des commandes à des contrôles qui n’ont pas été conçus pour interagir avec les commandes. Cet article décrit la création et l’utilisation d’un Xamarin.Forms comportement pour appeler une commande lorsqu’un événement se déclenche.
ms.prod: xamarin
ms.assetid: EC7F6556-9776-40B8-9424-A8094482A2F3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/09/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 46d1566c89de763a469f30ce8ed2c6ef919f1426
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135796"
---
# <a name="reusable-eventtocommandbehavior"></a>EventToCommandBehavior réutilisable

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

_Les comportements peuvent être utilisés pour associer des commandes à des contrôles qui n’ont pas été conçus pour interagir avec des commandes. Cet article décrit la création et l’utilisation d’un Xamarin.Forms comportement pour appeler une commande lorsqu’un événement se déclenche._

## <a name="overview"></a>Vue d’ensemble

La `EventToCommandBehavior` classe est un comportement personnalisé réutilisable Xamarin.Forms qui exécute une commande en réponse à *tout* déclenchement d’événement. Par défaut, les arguments d’événement pour l’événement sont passés à la commande et peuvent éventuellement être convertis par une [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) implémentation.

Les propriétés de comportement suivantes doivent être définies pour utiliser le comportement :

- **EventName** – nom de l’événement que le comportement écoute.
- **Command** – `ICommand` à exécuter. Le comportement s’attend à trouver l' `ICommand` instance sur le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) du contrôle attaché, qui peut être hérité d’un élément parent.

Les propriétés de comportement facultatives suivantes peuvent aussi être définies :

- **CommandParameter** – `object` à passer à la commande.
- **Converter** – implémentation [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) qui change le format des données d’argument d’événement puisque celles-ci sont passées de la *source* à la *cible* par le moteur de liaison.

> [!NOTE]
> `EventToCommandBehavior`Est une classe personnalisée qui peut se trouver dans l' [exemple de comportement EventToCommand](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)et ne fait pas partie de Xamarin.Forms .

## <a name="creating-the-behavior"></a>Création du comportement

La `EventToCommandBehavior` classe dérive de la `BehaviorBase<T>` classe, qui, à son tour, dérive de la [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe. L’objectif de la `BehaviorBase<T>` classe est de fournir une classe de base pour tous les Xamarin.Forms comportements qui requièrent que le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) du comportement soit défini sur le contrôle attaché. Cela garantit que le comportement peut être lié à l’`ICommand` spécifiée par la propriété `Command` et peut l’exécuter quand le comportement est consommé.

La `BehaviorBase<T>` classe fournit un substituable [ `OnAttachedTo` ] (XREF : Xamarin.Forms . Comportement `1.OnAttachedTo(Xamarin.Forms.BindableObject)) method that sets the [` BindingContext `](xref:Xamarin.Forms.BindableObject.BindingContext) of the behavior and an overridable [` OnDetachingFrom `](xref:Xamarin.Forms.Behavior` 1. OnDetachingFrom ( Xamarin.Forms . BindableObject)) qui nettoie le `BindingContext` . De plus, la classe stocke une référence au contrôle attaché dans la propriété `AssociatedObject`.

### <a name="implementing-bindable-properties"></a>Implémentation des propriétés pouvant être liées

La `EventToCommandBehavior` classe définit quatre [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) instances, qui exécutent une commande définie par l’utilisateur lorsqu’un événement se déclenche. Ces propriétés sont présentées dans l’exemple de code suivant :

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  public static readonly BindableProperty EventNameProperty =
    BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null, propertyChanged: OnEventNameChanged);
  public static readonly BindableProperty CommandProperty =
    BindableProperty.Create ("Command", typeof(ICommand), typeof(EventToCommandBehavior), null);
  public static readonly BindableProperty CommandParameterProperty =
    BindableProperty.Create ("CommandParameter", typeof(object), typeof(EventToCommandBehavior), null);
  public static readonly BindableProperty InputConverterProperty =
    BindableProperty.Create ("Converter", typeof(IValueConverter), typeof(EventToCommandBehavior), null);

  public string EventName { ... }
  public ICommand Command { ... }
  public object CommandParameter { ... }
  public IValueConverter Converter { ...  }
  ...
}
```

Quand la classe `EventToCommandBehavior` est consommée, la propriété `Command` doit être liée aux données d’une `ICommand` à exécuter en réponse au déclenchement de l’événement défini dans la propriété `EventName`. Le comportement s’attend à trouver le `ICommand` sur le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) du contrôle attaché.

Par défaut, les arguments d’événement de l’événement sont passés à la commande. Ces données peuvent être éventuellement converties quand elles sont passées de la *source* à la *cible* par le moteur de liaison, en spécifiant une implémentation [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) comme valeur de propriété `Converter`. Sinon, un paramètre peut être passé à la commande en spécifiant la valeur de propriété `CommandParameter`.

### <a name="implementing-the-overrides"></a>Implémentation des remplacements

La `EventToCommandBehavior` classe se substitue à [ `OnAttachedTo` ] (XREF : Xamarin.Forms . Comportement `1.OnAttachedTo(Xamarin.Forms.BindableObject)) and [` OnDetachingFrom `](xref:Xamarin.Forms.Behavior` 1. OnDetachingFrom ( Xamarin.Forms . BindableObject)) de la `BehaviorBase<T>` classe, comme indiqué dans l’exemple de code suivant :

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  ...
  protected override void OnAttachedTo (View bindable)
  {
    base.OnAttachedTo (bindable);
    RegisterEvent (EventName);
  }

  protected override void OnDetachingFrom (View bindable)
  {
    DeregisterEvent (EventName);
    base.OnDetachingFrom (bindable);
  }
  ...
}
```

[ `OnAttachedTo` ] (XREF : Xamarin.Forms . Comportement `1.OnAttachedTo(Xamarin.Forms.BindableObject)) method performs setup by calling the ` RegisterEvent ` method, passing in the value of the ` EventName ` property as a parameter. The [` OnDetachingFrom `](xref:Xamarin.Forms.Behavior` 1. OnDetachingFrom ( Xamarin.Forms . BindableObject)). la méthode effectue le nettoyage en appelant la `DeregisterEvent` méthode, en passant la valeur de la `EventName` propriété en tant que paramètre.

### <a name="implementing-the-behavior-functionality"></a>Implémentation de la fonctionnalité de comportement

L’objectif du comportement consiste à exécuter la commande définie par la propriété `Command` en réponse au déclenchement de l’événement qui est défini par la propriété `EventName`. La fonctionnalité de comportement clé est illustrée dans l’exemple de code suivant :

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  ...
  void RegisterEvent (string name)
  {
    if (string.IsNullOrWhiteSpace (name)) {
      return;
    }

    EventInfo eventInfo = AssociatedObject.GetType ().GetRuntimeEvent (name);
    if (eventInfo == null) {
      throw new ArgumentException (string.Format ("EventToCommandBehavior: Can't register the '{0}' event.", EventName));
    }
    MethodInfo methodInfo = typeof(EventToCommandBehavior).GetTypeInfo ().GetDeclaredMethod ("OnEvent");
    eventHandler = methodInfo.CreateDelegate (eventInfo.EventHandlerType, this);
    eventInfo.AddEventHandler (AssociatedObject, eventHandler);
  }

  void OnEvent (object sender, object eventArgs)
  {
    if (Command == null) {
      return;
    }

    object resolvedParameter;
    if (CommandParameter != null) {
      resolvedParameter = CommandParameter;
    } else if (Converter != null) {
      resolvedParameter = Converter.Convert (eventArgs, typeof(object), null, null);
    } else {
      resolvedParameter = eventArgs;
    }        

    if (Command.CanExecute (resolvedParameter)) {
      Command.Execute (resolvedParameter);
    }
  }
  ...
}
```

La méthode `RegisterEvent` est exécutée en réponse à l’attachement de l’`EventToCommandBehavior` à un contrôle et reçoit la valeur de la propriété `EventName` en tant que paramètre. La méthode tente ensuite de localiser l’événement défini dans la propriété `EventName` sur le contrôle attaché. Si l’événement arrive à être localisé, la méthode `OnEvent` est inscrite pour être la méthode de gestionnaire de l’événement.

La méthode `OnEvent` est exécutée en réponse au déclenchement de l’événement qui est défini dans la propriété `EventName`. Si la propriété `Command` référence une `ICommand` valide, la méthode tente de récupérer un paramètre à passer à l’`ICommand` comme suit :

- Si la propriété `CommandParameter` définit un paramètre, celui-ci est récupéré.
- Sinon, si la `Converter` propriété définit une [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) implémentation, le convertisseur est exécuté et convertit les données d’argument d’événement lors de leur passage entre la *source* et la *cible* par le moteur de liaison.
- Sinon, les arguments d’événement sont supposés être le paramètre.

La liaison de données `ICommand` est ensuite exécutée, en passant le paramètre à la commande, à condition que la [`CanExecute`](xref:Xamarin.Forms.Command.CanExecute(System.Object)) méthode retourne `true` .

Bien que cela ne soit pas illustré ici, le `EventToCommandBehavior` comprend également une `DeregisterEvent` méthode qui est exécutée par le [ `OnDetachingFrom` ] (XREF : Xamarin.Forms . `1.OnDetachingFrom(Xamarin.Forms.BindableObject)) method. The `Propriété Behavior DeregisterEvent ` method is used to locate and deregister the event defined in the ` EventName', pour nettoyer les fuites de mémoire potentielles.

## <a name="consuming-the-behavior"></a>Consommation du comportement

La `EventToCommandBehavior` classe peut être attachée à la [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) collection d’un contrôle, comme illustré dans l’exemple de code XAML suivant :

```xaml
<ListView ItemsSource="{Binding People}">
    <ListView.ItemTemplate>
        <DataTemplate>
            <TextCell Text="{Binding Name}" />
        </DataTemplate>
    </ListView.ItemTemplate>
    <ListView.Behaviors>
        <local:EventToCommandBehavior EventName="ItemSelected" Command="{Binding OutputAgeCommand}" Converter="{StaticResource SelectedItemConverter}" />
    </ListView.Behaviors>
</ListView>
<Label Text="{Binding SelectedItemText}" />
```

Le code C# équivalent est affiché dans l’exemple de code suivant :

```csharp
var listView = new ListView();
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "People");
listView.ItemTemplate = new DataTemplate(() =>
{
    var textCell = new TextCell();
    textCell.SetBinding(TextCell.TextProperty, "Name");
    return textCell;
});
listView.Behaviors.Add(new EventToCommandBehavior
{
    EventName = "ItemSelected",
    Command = ((HomePageViewModel)BindingContext).OutputAgeCommand,
    Converter = new SelectedItemEventArgsToSelectedItemConverter()
});

var selectedItemLabel = new Label();
selectedItemLabel.SetBinding(Label.TextProperty, "SelectedItemText");
```

La `Command` propriété du comportement est liée aux données de la `OutputAgeCommand` propriété du ViewModel associé, tandis que la `Converter` propriété a la valeur de l' `SelectedItemConverter` instance, qui retourne le [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) du de [`ListView`](xref:Xamarin.Forms.ListView) la [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) .

Lors de l’exécution, le comportement répond à l’interaction avec le contrôle. Lorsqu’un élément est sélectionné dans le [`ListView`](xref:Xamarin.Forms.ListView) , l' [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) événement se déclenche, ce qui entraîne l’exécution `OutputAgeCommand` de dans le ViewModel. À son tour, cette commande met à jour la `SelectedItemText` propriété ViewModel à laquelle est [`Label`](xref:Xamarin.Forms.Label) lié, comme illustré dans les captures d’écran suivantes :

[![](event-to-command-behavior-images/screenshots-sml.png "Sample Application with EventToCommandBehavior")](event-to-command-behavior-images/screenshots.png#lightbox "Sample Application with EventToCommandBehavior")

L’avantage d’utiliser ce comportement pour exécuter une commande quand un événement se déclenche est que les commandes peuvent être associées à des contrôles qui n’ont pas été conçus pour interagir avec les commandes. De plus, cela permet de supprimer des fichiers code-behind le code réutilisable qui gère les événements.

## <a name="summary"></a>Résumé

Cet article a montré l’utilisation Xamarin.Forms d’un comportement pour appeler une commande lorsqu’un événement se déclenche. Les comportements peuvent être utilisés pour associer des commandes à des contrôles qui n’ont pas été conçus pour interagir avec les commandes.

## <a name="related-links"></a>Liens connexes

- [Comportement EventToCommand (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)
- [Comportement](xref:Xamarin.Forms.Behavior)
- [Comportement &lt; T&gt;](xref:Xamarin.Forms.Behavior`1)
