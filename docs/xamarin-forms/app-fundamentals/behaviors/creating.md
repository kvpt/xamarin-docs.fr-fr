---
title: Créer des comportements Xamarin.Forms
description: Les comportements Xamarin.Forms sont créés par dérivation de la classe Behavior ou Behavior<T>. Cet article montre comment créer et consommer des comportements Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 300C16FE-A7E0-445B-9099-8E93ABB6F73D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 37403b9e33ccd54baac4fd36afbe50504e0380f0
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528953"
---
# <a name="create-xamarinforms-behaviors"></a>Créer des comportements Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehavior)

_Les comportements Xamarin.Forms sont créés par dérivation de la classe Behavior ou Behavior&lt;T&gt;. Cet article montre comment créer et consommer des comportements Xamarin.Forms._

## <a name="overview"></a>Vue d'ensemble

Le processus de création d’un comportement Xamarin.Forms est le suivant :

1. Créez une classe qui hérite de la classe [`Behavior`](xref:Xamarin.Forms.Behavior) ou [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1), où `T` est le type du contrôle auquel le comportement doit s’appliquer.
1. Remplacez la méthode [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) pour effectuer toute configuration nécessaire.
1. Remplacez la méthode [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) pour effectuer tout nettoyage nécessaire.
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

La méthode [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) est déclenchée immédiatement après l’attachement du comportement à un contrôle. Cette méthode reçoit une référence au contrôle auquel elle est attachée et peut être utilisée pour inscrire des gestionnaires d’événements ou effectuer une autre configuration nécessaire à la prise en charge de la fonctionnalité du comportement. Par exemple, vous pouvez vous abonner à un événement sur un contrôle. La fonctionnalité du comportement serait alors implémentée dans le gestionnaire d’événements pour l’événement en question.

La méthode [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) est déclenchée quand le comportement est supprimé du contrôle. Cette méthode reçoit une référence au contrôle auquel elle est attachée et est utilisée pour effectuer tout nettoyage nécessaire. Par exemple, vous pouvez vous désabonner d’un événement sur un contrôle pour empêcher les fuites de mémoire.

Le comportement peut ensuite être consommé en l’attachant à la collection [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) du contrôle approprié.

## <a name="creating-a-xamarinforms-behavior"></a>Création d’un comportement Xamarin.Forms

L’exemple d’application montre un `NumericValidationBehavior`, qui met en évidence la valeur entrée par l’utilisateur dans un contrôle [`Entry`](xref:Xamarin.Forms.Entry) en rouge, si elle n’est pas un `double`. Le comportement est illustré dans l’exemple de code suivant :

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

`NumericValidationBehavior` dérive de la classe [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1), où `T` est une [`Entry`](xref:Xamarin.Forms.Entry). La méthode [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) inscrit un gestionnaire d’événements pour l’événement [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged), tandis que la méthode [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) le désinscrit de l’événement `TextChanged` pour empêcher les fuites de mémoire. La fonctionnalité clé du comportement est fournie par la méthode `OnEntryTextChanged`, qui analyse la valeur entrée par l’utilisateur dans l’`Entry` et définit la propriété [`TextColor`](xref:Xamarin.Forms.Entry.TextColor) sur rouge si la valeur n’est pas un `double`.

> [!NOTE]
> Xamarin.Forms ne définit pas le `BindingContext` d’un comportement parce que les comportements peuvent être partagés et appliqués à plusieurs contrôles en utilisant des styles.

## <a name="consuming-a-xamarinforms-behavior"></a>Consommation d’un comportement Xamarin.Forms

Chaque contrôle Xamarin.Forms présente une collection [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) à laquelle un ou plusieurs comportements peuvent être ajoutés, comme illustré dans l’exemple de code XAML suivant :

```xaml
<Entry Placeholder="Enter a System.Double">
    <Entry.Behaviors>
        <local:NumericValidationBehavior />
    </Entry.Behaviors>
</Entry>
```

Le contrôle [`Entry`](xref:Xamarin.Forms.Entry) équivalent en C# est présenté dans l’exemple de code suivant :

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
entry.Behaviors.Add (new NumericValidationBehavior ());
```

Lors de l’exécution, le comportement répond à l’interaction avec le contrôle en fonction de l’implémentation de comportement. Les captures d’écran suivantes illustrent le comportement répondant à une entrée non valide :

[![](creating-images/screenshots-sml.png "Exemple d’application avec un comportement Xamarin.Forms")](creating-images/screenshots.png#lightbox "Exemple d’application avec un comportement Xamarin.Forms")

> [!NOTE]
> Les comportements sont écrits pour un type de contrôle spécifique (ou une superclasse qui peut s’appliquer à de nombreux contrôles) et doivent être ajoutés à un contrôle compatible uniquement. La tentative d’attachement d’un comportement à un contrôle incompatible entraîne la levée d’une exception.

### <a name="consuming-a-xamarinforms-behavior-with-a-style"></a>Consommation d’un comportement Xamarin.Forms avec un style

Les comportements peuvent aussi être consommés par un style explicite ou implicite. Toutefois, la création d’un style qui définit la propriété [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) d’un contrôle n’est pas possible parce que la propriété est en lecture seule. La solution consiste à ajouter une propriété attachée à la classe de comportement qui contrôle l’ajout et la suppression du comportement. Le processus est le suivant :

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

La classe `NumericValidationBehavior` contient une propriété attachée nommée `AttachBehavior` avec une méthode getter ou setter `static`, qui contrôle l’ajout ou la suppression du comportement dans le contrôle auquel il est attaché. Cette propriété attachée inscrit la méthode `OnAttachBehaviorChanged` qui est exécutée au changement de la valeur de la propriété. Cette méthode ajoute ou supprime le comportement dans le contrôle en fonction de la valeur de la propriété attachée `AttachBehavior`.

L’exemple de code suivant montre un style *explicite* pour le `NumericValidationBehavior` qui utilise la propriété attachée `AttachBehavior` et qui peut être appliqué aux contrôles [`Entry`](xref:Xamarin.Forms.Entry) :

```xaml
<Style x:Key="NumericValidationStyle" TargetType="Entry">
    <Style.Setters>
        <Setter Property="local:NumericValidationBehavior.AttachBehavior" Value="true" />
    </Style.Setters>
</Style>
```

Le [`Style`](xref:Xamarin.Forms.Style) peut être appliqué à un contrôle [`Entry`](xref:Xamarin.Forms.Entry) en définissant sa propriété [`Style`](xref:Xamarin.Forms.NavigableElement.Style) sur l’instance `Style` à l’aide de l’extension de balisage `StaticResource`, comme illustré dans l’exemple de code suivant :

```xaml
<Entry Placeholder="Enter a System.Double" Style="{StaticResource NumericValidationStyle}">
```

Pour plus d’informations sur les styles, consultez [Styles](~/xamarin-forms/user-interface/styles/index.md).

> [!NOTE]
> Vous pouvez ajouter des propriétés pouvant être liées à un comportement qui est défini ou interrogé en XAML, mais si vous créez des comportements qui ont un état, ceux-ci ne doivent pas être partagés entre les contrôles d’un `Style` dans un `ResourceDictionary`.

### <a name="removing-a-behavior-from-a-control"></a>Suppression d’un comportement d’un contrôle

La méthode [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) est déclenchée quand un comportement est supprimé d’un contrôle et est utilisée pour effectuer tout nettoyage nécessaire, comme le désabonnement d’un événement pour empêcher une fuite de mémoire. Toutefois, les comportements ne sont pas supprimés implicitement des contrôles, sauf si la collection [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) du contrôle est modifiée par une méthode `Remove` ou `Clear`. L’exemple de code suivant illustre la suppression d’un comportement spécifique de la collection `Behaviors` d’un contrôle :

```csharp
var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
if (toRemove != null) {
    entry.Behaviors.Remove (toRemove);
}
```

Sinon, la collection [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) du contrôle peut être effacée, comme montré dans l’exemple de code suivant :

```csharp
entry.Behaviors.Clear();
```

De plus, notez que les comportements ne sont pas supprimés implicitement des contrôles quand les pages sont dépilées de la pile de navigation. Ils doivent être explicitement supprimés avant que les pages ne soient hors de portée.

## <a name="summary"></a>Récapitulatif

Cet article a montré comment créer et consommer des comportements Xamarin.Forms. Les comportements Xamarin.Forms sont créés en dérivant de la classe [`Behavior`](xref:Xamarin.Forms.Behavior) ou [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1).


## <a name="related-links"></a>Liens associés

- [Comportement Xamarin.Forms (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehavior)
- [Comportement Xamarin.Forms appliqué avec un style (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehaviorstyle)
- [Behavior](xref:Xamarin.Forms.Behavior)
- [Behavior\<T>](xref:Xamarin.Forms.Behavior`1)
