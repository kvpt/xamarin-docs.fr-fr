---
title: EventToCommandBehavior réutilisable
description: Vous pouvez utiliser des comportements pour associer des commandes à des contrôles qui n’ont pas été conçus pour interagir avec des commandes. Cet article montre comment créer et consommer un comportement Xamarin.Forms pour appeler une commande quand un événement est déclenché.
ms.prod: xamarin
ms.assetid: EC7F6556-9776-40B8-9424-A8094482A2F3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/09/2018
ms.openlocfilehash: 292a6aaaea4fb0f84138e04c88f001c72ddd096d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68650910"
---
# <a name="reusable-eventtocommandbehavior"></a>EventToCommandBehavior réutilisable

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

_Vous pouvez utiliser des comportements pour associer des commandes à des contrôles qui n’ont pas été conçus pour interagir avec des commandes. Cet article montre comment créer et consommer un comportement Xamarin.Forms pour appeler une commande quand un événement est déclenché._

## <a name="overview"></a>Vue d'ensemble

La classe `EventToCommandBehavior` est un comportement personnalisé Xamarin.Forms réutilisable qui exécute une commande en réponse à *tout* déclenchement d’événement. Par défaut, les arguments d’événement de l’événement sont passés à la commande et peuvent être éventuellement convertis par une implémentation [`IValueConverter`](xref:Xamarin.Forms.IValueConverter).

Les propriétés de comportement suivantes doivent être définies pour utiliser le comportement :

- **EventName** – nom de l’événement que le comportement écoute.
- **Command** – `ICommand` à exécuter. Le comportement s’attend à trouver l’instance `ICommand` sur le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) du contrôle attaché, qui peut être héritée d’un élément parent.

Les propriétés de comportement facultatives suivantes peuvent aussi être définies :

- **CommandParameter** – `object` à passer à la commande.
- **Converter** – implémentation [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) qui change le format des données d’argument d’événement puisque celles-ci sont passées de la *source* à la *cible* par le moteur de liaison.

> [!NOTE]
> `EventToCommandBehavior` est une classe personnalisée qui peut se trouver dans l’[exemple Comportement EventToCommand](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior) et qui ne fait pas partie de Xamarin.Forms.

## <a name="creating-the-behavior"></a>Création du comportement

La classe `EventToCommandBehavior` dérive de la classe `BehaviorBase<T>`, elle-même dérivée de la classe [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1). L’objectif de la classe `BehaviorBase<T>` est de fournir une classe de base pour les comportements Xamarin.Forms qui nécessitent le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) du comportement à définir dans le contrôle attaché. Cela garantit que le comportement peut être lié à l’`ICommand` spécifiée par la propriété `Command` et peut l’exécuter quand le comportement est consommé.

La classe `BehaviorBase<T>` fournit une méthode [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) remplaçable qui définit le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) du comportement et une méthode [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) remplaçable qui nettoie le `BindingContext`. De plus, la classe stocke une référence au contrôle attaché dans la propriété `AssociatedObject`.

### <a name="implementing-bindable-properties"></a>Implémentation des propriétés pouvant être liées

La classe `EventToCommandBehavior` définit quatre instances [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) qui exécutent une commande définie par l’utilisateur quand un événement est déclenché. Ces propriétés sont présentées dans l’exemple de code suivant :

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

Quand la classe `EventToCommandBehavior` est consommée, la propriété `Command` doit être liée aux données d’une `ICommand` à exécuter en réponse au déclenchement de l’événement défini dans la propriété `EventName`. Le comportement s’attend à trouver l’`ICommand` dans le [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) du contrôle attaché.

Par défaut, les arguments d’événement de l’événement sont passés à la commande. Ces données peuvent être éventuellement converties quand elles sont passées de la *source* à la *cible* par le moteur de liaison, en spécifiant une implémentation [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) comme valeur de propriété `Converter`. Sinon, un paramètre peut être passé à la commande en spécifiant la valeur de propriété `CommandParameter`.

### <a name="implementing-the-overrides"></a>Implémentation des remplacements

La classe `EventToCommandBehavior` remplace les méthodes [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) et [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) de la classe `BehaviorBase<T>`, comme illustré dans l’exemple de code suivant :

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

La méthode [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) effectue la configuration en appelant la méthode `RegisterEvent` pour passer la valeur de la propriété `EventName` en tant que paramètre. La méthode [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) effectue le nettoyage en appelant la méthode `DeregisterEvent` pour passer la valeur de la propriété `EventName` en tant que paramètre.

### <a name="implementing-the-behavior-functionality"></a>Implémentation de la fonctionnalité de comportement

L’objectif du comportement consiste à exécuter la commande définie par la propriété `Command` en réponse au déclenchement de l’événement qui est défini par la propriété `EventName`. La fonctionnalité clé du comportement est illustrée dans l’exemple de code suivant :

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
- Sinon, si la propriété `Converter` définit une implémentation [`IValueConverter`](xref:Xamarin.Forms.IValueConverter), le convertisseur est exécuté et convertit les données d’argument d’événement puisque celles-ci sont passées de la *source* à la *cible* par le moteur de liaison.
- Sinon, les arguments d’événement sont supposés être le paramètre.

L’`ICommand` liée aux données est ensuite exécutée en passant le paramètre à la commande, sous réserve que la méthode [`CanExecute`](xref:Xamarin.Forms.Command.CanExecute(System.Object)) retourne `true`.

Bien que non illustré ici, le comportement `EventToCommandBehavior` inclut également une méthode `DeregisterEvent` qui est exécutée par la méthode [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)). La méthode `DeregisterEvent` est utilisée pour localiser et désinscrire l’événement défini dans la propriété `EventName` afin de nettoyer les fuites de mémoire potentielles.

## <a name="consuming-the-behavior"></a>Consommation du comportement

La classe `EventToCommandBehavior` peut être attachée à la collection [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) d’un contrôle, comme illustré dans l’exemple de code XAML suivant :

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

La propriété `Command` du comportement est liée aux données de la propriété `OutputAgeCommand` du ViewModel associé, tandis que la propriété `Converter` est définie sur l’instance `SelectedItemConverter`, qui retourne le [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem)du [`ListView`](xref:Xamarin.Forms.ListView) à partir de [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs).

Lors de l’exécution, le comportement répond à l’interaction avec le contrôle. Quand un élément est sélectionné dans [`ListView`](xref:Xamarin.Forms.ListView), l’événement [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) se déclenche, ce qui exécute `OutputAgeCommand` dans le ViewModel. À son tour, est mise à jour la propriété `SelectedItemText` du ViewModel à laquelle [`Label`](xref:Xamarin.Forms.Label) est lié, comme indiqué dans les captures d’écran suivantes :

[![](event-to-command-behavior-images/screenshots-sml.png "Exemple d’application avec EventToCommandBehavior")](event-to-command-behavior-images/screenshots.png#lightbox "Exemple d’application avec EventToCommandBehavior")

L’avantage d’utiliser ce comportement pour exécuter une commande quand un événement se déclenche est que les commandes peuvent être associées à des contrôles qui n’ont pas été conçus pour interagir avec les commandes. De plus, cela permet de supprimer des fichiers code-behind le code réutilisable qui gère les événements.

## <a name="summary"></a>Récapitulatif

Cet article a montré comment utiliser un comportement Xamarin.Forms pour appeler une commande quand un événement se déclenche. Vous pouvez utiliser des comportements pour associer des commandes à des contrôles qui n’ont pas été conçus pour interagir avec des commandes.

## <a name="related-links"></a>Liens associés

- [Comportement EventToCommand (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)
- [Behavior](xref:Xamarin.Forms.Behavior)
- [Behavior&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
