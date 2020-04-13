---
title: EventToCommandBehavior réutilisable
description: Les comportements peuvent être utilisés pour associer des commandes à des contrôles qui n’ont pas été conçus pour interagir avec les commandes. Cet article montre comment créer et utiliser un comportement Xamarin.Forms pour appeler une commande quand un événement se déclenche.
ms.prod: xamarin
ms.assetid: EC7F6556-9776-40B8-9424-A8094482A2F3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/09/2018
ms.openlocfilehash: 292a6aaaea4fb0f84138e04c88f001c72ddd096d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "68650910"
---
# <a name="reusable-eventtocommandbehavior"></a>EventToCommandBehavior réutilisable

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

_Les comportements peuvent être utilisés pour associer des commandes à des commandes qui n’ont pas été conçues pour interagir avec les commandes. Cet article démontre la création et la consommation d’un comportement Xamarin.Forms pour invoquer une commande quand un événement tire._

## <a name="overview"></a>Vue d’ensemble

La classe `EventToCommandBehavior` est un comportement personnalisé Xamarin.Forms réutilisable qui exécute une commande en réponse à *tout* déclenchement d’événement. Par défaut, les arguments de l’événement seront transmis à la commande [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) et peuvent être convertis en option par une implémentation.

Les propriétés de comportement suivantes doivent être définies pour utiliser le comportement :

- **EventName** – nom de l’événement que le comportement écoute.
- **Command** – `ICommand` à exécuter. Le comportement s’attend à trouver l’instance `ICommand` sur le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) contrôle ci-joint, qui peut être héritée d’un élément parent.

Les propriétés de comportement facultatives suivantes peuvent aussi être définies :

- **CommandParameter** – `object` à passer à la commande.
- **Converter** – implémentation [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) qui change le format des données d’argument d’événement puisque celles-ci sont passées de la *source* à la *cible* par le moteur de liaison.

> [!NOTE]
> `EventToCommandBehavior` est une classe personnalisée qui peut se trouver dans l’[exemple Comportement EventToCommand](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior) et qui ne fait pas partie de Xamarin.Forms.

## <a name="creating-the-behavior"></a>Création du comportement

La `EventToCommandBehavior` classe dérive `BehaviorBase<T>` de la classe, qui [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) dérive à son tour de la classe. Le but `BehaviorBase<T>` de la classe est de fournir une classe de base [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) pour tous les comportements Xamarin.Forms qui exigent que le comportement soit réglé sur le contrôle ci-joint. Cela garantit que le comportement peut être lié à l’`ICommand` spécifiée par la propriété `Command` et peut l’exécuter quand le comportement est consommé.

La `BehaviorBase<T>` classe fournit une [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) méthode primordiale qui définit le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) comportement et une `BindingContext`méthode primordiale qui nettoie le . De plus, la classe stocke une référence au contrôle attaché dans la propriété `AssociatedObject`.

### <a name="implementing-bindable-properties"></a>Implémentation des propriétés pouvant être liées

La `EventToCommandBehavior` classe définit [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) quatre instances qui exécutent une commande définie par l’utilisateur lorsqu’un événement s’allume. Ces propriétés sont présentées dans l’exemple de code suivant :

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

Quand la classe `EventToCommandBehavior` est consommée, la propriété `Command` doit être liée aux données d’une `ICommand` à exécuter en réponse au déclenchement de l’événement défini dans la propriété `EventName`. Le comportement s’attendra à trouver le `ICommand` sur le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) contrôle ci-joint.

Par défaut, les arguments d’événement de l’événement sont passés à la commande. Ces données peuvent être éventuellement converties quand elles sont passées de la *source* à la *cible* par le moteur de liaison, en spécifiant une implémentation [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) comme valeur de propriété `Converter`. Sinon, un paramètre peut être passé à la commande en spécifiant la valeur de propriété `CommandParameter`.

### <a name="implementing-the-overrides"></a>Implémentation des remplacements

La `EventToCommandBehavior` classe l’emporte [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) sur `BehaviorBase<T>` les [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) méthodes et les méthodes de la classe, comme le montre l’exemple de code suivant :

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

La [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) méthode effectue la `RegisterEvent` configuration en appelant la `EventName` méthode, en passant dans la valeur de la propriété comme un paramètre. La [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) méthode effectue le nettoyage `DeregisterEvent` en appelant la méthode, en passant dans la valeur de la `EventName` propriété comme un paramètre.

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
- Sinon, si `Converter` la propriété [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) définit une implémentation, le convertisseur est exécuté et convertit les données de l’argument de l’événement car il est passé entre la *source* et la *cible* par le moteur de liaison.
- Sinon, les arguments d’événement sont supposés être le paramètre.

Les données `ICommand` liées sont ensuite exécutées, en passant dans [`CanExecute`](xref:Xamarin.Forms.Command.CanExecute(System.Object)) le `true`paramètre à la commande, à condition que la méthode retourne .

Bien que n’est pas montré ici, le `EventToCommandBehavior` comprend également une `DeregisterEvent` méthode qui est exécuté par la [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) méthode. La méthode `DeregisterEvent` est utilisée pour localiser et désinscrire l’événement défini dans la propriété `EventName` afin de nettoyer les fuites de mémoire potentielles.

## <a name="consuming-the-behavior"></a>Consommation du comportement

La `EventToCommandBehavior` classe peut être [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) jointe à la collection d’un contrôle, comme le démontre l’exemple de code XAML suivant :

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

La `Command` propriété du comportement est `OutputAgeCommand` des données liées à la `Converter` propriété de l’associé ViewModel, tandis que la propriété est réglée à l’instance, `SelectedItemConverter` qui renvoie le [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) de la [`ListView`](xref:Xamarin.Forms.ListView) . [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs)

Lors de l’exécution, le comportement répond à l’interaction avec le contrôle. Lorsqu’un élément est [`ListView`](xref:Xamarin.Forms.ListView)sélectionné [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) dans le , l’événement tire, qui exécutera le `OutputAgeCommand` dans le ViewModel. À son tour, cela `SelectedItemText` met [`Label`](xref:Xamarin.Forms.Label) à jour la propriété ViewModel que les liaisons à, comme indiqué dans les captures d’écran suivantes:

[![](event-to-command-behavior-images/screenshots-sml.png "Sample Application with EventToCommandBehavior")](event-to-command-behavior-images/screenshots.png#lightbox "Sample Application with EventToCommandBehavior")

L’avantage d’utiliser ce comportement pour exécuter une commande quand un événement se déclenche est que les commandes peuvent être associées à des contrôles qui n’ont pas été conçus pour interagir avec les commandes. De plus, cela permet de supprimer des fichiers code-behind le code réutilisable qui gère les événements.

## <a name="summary"></a>Récapitulatif

Cet article a montré comment utiliser un comportement Xamarin.Forms pour appeler une commande quand un événement se déclenche. Les comportements peuvent être utilisés pour associer des commandes à des contrôles qui n’ont pas été conçus pour interagir avec les commandes.

## <a name="related-links"></a>Liens connexes

- [Comportement EventToCommand (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)
- [Comportement](xref:Xamarin.Forms.Behavior)
- [Comportement&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
