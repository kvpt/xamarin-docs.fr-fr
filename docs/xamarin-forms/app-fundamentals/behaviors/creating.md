---
title: Créer des Xamarin.Forms comportements
description: Xamarin.Formsles comportements sont créés en dérivant de la classe Behavior ou Behavior <T> . Cet article montre comment créer et utiliser des Xamarin.Forms comportements.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 67db30b5caadce75a41755530db2b245562d0304
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135822"
---
# <a name="create-xamarinforms-behaviors"></a>Créer des Xamarin.Forms comportements

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehavior)

_Les comportements Xamarin. Forms sont créés en dérivant de la classe Behavior ou Behavior &lt; T &gt; . Cet article montre comment créer et utiliser des Xamarin.Forms comportements._

## <a name="overview"></a>Vue d'ensemble

Le processus de création d’un Xamarin.Forms comportement est le suivant :

1. Créez une classe qui hérite de la [`Behavior`](xref:Xamarin.Forms.Behavior) [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe ou, où `T` est le type du contrôle auquel le comportement doit s’appliquer.
1. Remplacez [ `OnAttachedTo` ] (XREF : Xamarin.Forms . Behavior'1. OnAttachedTo ( Xamarin.Forms . BindableObject)) pour effectuer toutes les configurations requises.
1. Remplacez [ `OnDetachingFrom` ] (XREF : Xamarin.Forms . Behavior'1. OnDetachingFrom ( Xamarin.Forms . BindableObject)) pour effectuer les nettoyages requis.
1. Implémentez la fonctionnalité clé du comportement.

La structure qui en résulte est illustrée dans l’exemple de code suivant :

```csharp
public class CustomBehavior : Behavior<View>
{
    protected override void OnAttachedTo (View bindable)
    {
        base.OnAttachedTo (bindable);
        // Perform setup
    }

    protected override void OnDetachingFrom (View bindable)
    {
        base.OnDetachingFrom (bindable);
        // Perform clean up
    }

    // Behavior implementation
}
```

[ `OnAttachedTo` ] (XREF : Xamarin.Forms . Behavior'1. OnAttachedTo ( Xamarin.Forms . BindableObject)) est déclenchée immédiatement après que le comportement est attaché à un contrôle. Cette méthode reçoit une référence au contrôle auquel elle est attachée et peut être utilisée pour inscrire des gestionnaires d’événements ou effectuer une autre configuration nécessaire à la prise en charge de la fonctionnalité du comportement. Par exemple, vous pouvez vous abonner à un événement sur un contrôle. La fonctionnalité du comportement serait alors implémentée dans le gestionnaire d’événements pour l’événement en question.

[ `OnDetachingFrom` ] (XREF : Xamarin.Forms . Behavior'1. OnDetachingFrom ( Xamarin.Forms . BindableObject)) est déclenchée lorsque le comportement est supprimé du contrôle. Cette méthode reçoit une référence au contrôle auquel elle est attachée et est utilisée pour effectuer tout nettoyage nécessaire. Par exemple, vous pouvez vous désabonner d’un événement sur un contrôle pour empêcher les fuites de mémoire.

Le comportement peut ensuite être consommé en l’attachant à la [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) collection du contrôle approprié.

## <a name="creating-a-xamarinforms-behavior"></a>Création d’un Xamarin.Forms comportement

L’exemple d’application illustre un `NumericValidationBehavior` , qui met en surbrillance la valeur entrée par l’utilisateur dans un [`Entry`](xref:Xamarin.Forms.Entry) contrôle en rouge, s’il ne s’agit pas d’un `double` . Le comportement est illustré dans l’exemple de code suivant :

```csharp
public class NumericValidationBehavior : Behavior<Entry>
{
    protected override void OnAttachedTo(Entry entry)
    {
        entry.TextChanged += OnEntryTextChanged;
        base.OnAttachedTo(entry);
    }

    protected override void OnDetachingFrom(Entry entry)
    {
        entry.TextChanged -= OnEntryTextChanged;
        base.OnDetachingFrom(entry);
    }

    void OnEntryTextChanged(object sender, TextChangedEventArgs args)
    {
        double result;
        bool isValid = double.TryParse (args.NewTextValue, out result);
        ((Entry)sender).TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

Le `NumericValidationBehavior` dérive de la [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe, où `T` est un [`Entry`](xref:Xamarin.Forms.Entry) . [ `OnAttachedTo` ] (XREF : Xamarin.Forms . Comportement `1.OnAttachedTo(Xamarin.Forms.BindableObject)) method registers an event handler for the [` TextChanged `](xref:Xamarin.Forms.InputView.TextChanged) event, with the [` OnDetachingFrom `](xref:Xamarin.Forms.Behavior` 1. OnDetachingFrom ( Xamarin.Forms . BindableObject)). méthode de désinscription de l' `TextChanged` événement pour empêcher les fuites de mémoire. La fonctionnalité principale du comportement est fournie par la `OnEntryTextChanged` méthode, qui analyse la valeur entrée par l’utilisateur dans le `Entry` et définit la [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) propriété sur rouge si la valeur n’est pas un `double` .

> [!NOTE]
> Xamarin.Formsne définit pas le `BindingContext` d’un comportement, car les comportements peuvent être partagés et appliqués à plusieurs contrôles par le biais de styles.

## <a name="consuming-a-xamarinforms-behavior"></a>Utilisation d’un Xamarin.Forms comportement

Chaque Xamarin.Forms contrôle possède une [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) collection, à laquelle un ou plusieurs comportements peuvent être ajoutés, comme illustré dans l’exemple de code XAML suivant :

```xaml
<Entry Placeholder="Enter a System.Double">
    <Entry.Behaviors>
        <local:NumericValidationBehavior />
    </Entry.Behaviors>
</Entry>
```

L’équivalent [`Entry`](xref:Xamarin.Forms.Entry) en C# est illustré dans l’exemple de code suivant :

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
entry.Behaviors.Add (new NumericValidationBehavior ());
```

Lors de l’exécution, le comportement répond à l’interaction avec le contrôle en fonction de l’implémentation de comportement. Les captures d’écran suivantes illustrent le comportement répondant à une entrée non valide :

[![](creating-images/screenshots-sml.png "Sample Application with Xamarin.Forms Behavior")](creating-images/screenshots.png#lightbox "Sample Application with Xamarin.Forms Behavior")

> [!NOTE]
> Les comportements sont écrits pour un type de contrôle spécifique (ou une superclasse qui peut s’appliquer à de nombreux contrôles) et doivent être ajoutés à un contrôle compatible uniquement. La tentative d’attachement d’un comportement à un contrôle incompatible entraîne la levée d’une exception.

### <a name="consuming-a-xamarinforms-behavior-with-a-style"></a>Utilisation d’un Xamarin.Forms comportement avec un style

Les comportements peuvent aussi être consommés par un style explicite ou implicite. Toutefois, la création d’un style qui définit la [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) propriété d’un contrôle n’est pas possible, car la propriété est en lecture seule. La solution consiste à ajouter une propriété attachée à la classe de comportement qui contrôle l’ajout et la suppression du comportement. Pour ce faire, procédez comme suit :

1. Ajoutez une propriété attachée à la classe de comportement qui est utilisée pour contrôler l’ajout ou la suppression du comportement dans le contrôle auquel le comportement est attaché. Assurez-vous que la propriété attachée inscrit un délégué `propertyChanged` qui sera exécuté au changement de la valeur de la propriété.
1. Créez une méthode getter ou setter `static` pour la propriété attachée.
1. Dans le délégué `propertyChanged`, implémentez la logique qui ajoute ou supprime le comportement.

L’exemple de code suivant montre une propriété attachée qui contrôle l’ajout et la suppression de `NumericValidationBehavior` :

```csharp
public class NumericValidationBehavior : Behavior<Entry>
{
    public static readonly BindableProperty AttachBehaviorProperty =
        BindableProperty.CreateAttached ("AttachBehavior", typeof(bool), typeof(NumericValidationBehavior), false, propertyChanged: OnAttachBehaviorChanged);

    public static bool GetAttachBehavior (BindableObject view)
    {
        return (bool)view.GetValue (AttachBehaviorProperty);
    }

    public static void SetAttachBehavior (BindableObject view, bool value)
    {
        view.SetValue (AttachBehaviorProperty, value);
    }

    static void OnAttachBehaviorChanged (BindableObject view, object oldValue, object newValue)
    {
        var entry = view as Entry;
        if (entry == null) {
            return;
        }

        bool attachBehavior = (bool)newValue;
        if (attachBehavior) {
            entry.Behaviors.Add (new NumericValidationBehavior ());
        } else {
            var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
            if (toRemove != null) {
                entry.Behaviors.Remove (toRemove);
            }
        }
    }
    ...
}
```

La classe `NumericValidationBehavior` contient une propriété attachée nommée `AttachBehavior` avec une méthode getter ou setter `static`, qui contrôle l’ajout ou la suppression du comportement dans le contrôle auquel il est attaché. Cette propriété jointe inscrit la méthode `OnAttachBehaviorChanged` qui est exécutée au changement de la valeur de la propriété. Cette méthode ajoute ou supprime le comportement dans le contrôle en fonction de la valeur de la propriété attachée `AttachBehavior`.

L’exemple de code suivant montre un style *explicite* pour le `NumericValidationBehavior` qui utilise la propriété attachée `AttachBehavior` et qui peut être appliqué aux contrôles [`Entry`](xref:Xamarin.Forms.Entry) :

```xaml
<Style x:Key="NumericValidationStyle" TargetType="Entry">
    <Style.Setters>
        <Setter Property="local:NumericValidationBehavior.AttachBehavior" Value="true" />
    </Style.Setters>
</Style>
```

[`Style`](xref:Xamarin.Forms.Style)Peut être appliqué à un [`Entry`](xref:Xamarin.Forms.Entry) contrôle en affectant [`Style`](xref:Xamarin.Forms.NavigableElement.Style) à sa propriété l’instance à l’aide de `Style` l’extension de `StaticResource` balisage, comme illustré dans l’exemple de code suivant :

```xaml
<Entry Placeholder="Enter a System.Double" Style="{StaticResource NumericValidationStyle}">
```

Pour plus d’informations sur les styles, consultez [Styles](~/xamarin-forms/user-interface/styles/index.md).

> [!NOTE]
> Vous pouvez ajouter des propriétés pouvant être liées à un comportement qui est défini ou interrogé en XAML, mais si vous créez des comportements qui ont un état, ceux-ci ne doivent pas être partagés entre les contrôles d’un `Style` dans un `ResourceDictionary`.

### <a name="removing-a-behavior-from-a-control"></a>Suppression d’un comportement d’un contrôle

[ `OnDetachingFrom` ] (XREF : Xamarin.Forms . `1.OnDetachingFrom(Xamarin.Forms.BindableObject)) method is fired when a behavior is removed from a control, and is used to perform any required cleanup such as unsubscribing from an event to prevent a memory leak. However, behaviors are not implicitly removed from controls unless the control's [`Comportements de comportement `](xref:Xamarin.Forms.VisualElement.Behaviors) collection is modified by a ` Supprimer la collection de ` or ` ` method. The following code example demonstrates removing a specific behavior from a control's ` comportements clairs :

```csharp
var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
if (toRemove != null) {
    entry.Behaviors.Remove (toRemove);
}
```

La collection du contrôle peut également [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) être désactivée, comme illustré dans l’exemple de code suivant :

```csharp
entry.Behaviors.Clear();
```

De plus, notez que les comportements ne sont pas supprimés implicitement des contrôles quand les pages sont dépilées de la pile de navigation. Ils doivent être explicitement supprimés avant que les pages ne soient hors de portée.

## <a name="summary"></a>Résumé

Cet article a montré comment créer et utiliser des Xamarin.Forms comportements. Xamarin.Formsles comportements sont créés par dérivation à partir de [`Behavior`](xref:Xamarin.Forms.Behavior) la [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe ou.

## <a name="related-links"></a>Liens connexes

- [Xamarin.FormsComportement (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehavior)
- [Xamarin.FormsComportement appliqué avec un style (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehaviorstyle)
- [Comportement](xref:Xamarin.Forms.Behavior)
- [Comportement\<T>](xref:Xamarin.Forms.Behavior`1)
