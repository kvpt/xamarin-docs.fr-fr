---
title: Créer des comportements Xamarin.Forms
description: Les comportements Xamarin.Forms sont créés par dérivation de la classe Behavior ou Behavior<T>. Cet article montre comment créer et consommer des comportements Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 300C16FE-A7E0-445B-9099-8E93ABB6F73D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 42ad56a7ae34bcef638ed25bea267dcabd21e20c
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "77131090"
---
# <a name="create-xamarinforms-behaviors"></a>Créer des comportements Xamarin.Forms

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehavior)

_Les comportements Xamarin.Forms sont créés en dérivé&lt;&gt; de la classe Behavior or Behavior T. Cet article démontre comment créer et consommer des comportements Xamarin.Forms._

## <a name="overview"></a>Vue d’ensemble

Le processus de création d’un comportement Xamarin.Forms est le suivant :

1. Créez une classe qui [`Behavior`](xref:Xamarin.Forms.Behavior) hérite de la classe ou [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) de la classe, où `T` est le type de contrôle auquel le comportement doit s’appliquer.
1. Remplacer la [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) méthode pour effectuer n’importe quelle configuration requise.
1. Remplacer la [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) méthode pour effectuer le nettoyage requis.
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

La [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) méthode est allumée immédiatement après que le comportement est attaché à un contrôle. Cette méthode reçoit une référence au contrôle auquel elle est attachée et peut être utilisée pour inscrire des gestionnaires d’événements ou effectuer une autre configuration nécessaire à la prise en charge de la fonctionnalité du comportement. Par exemple, vous pouvez vous abonner à un événement sur un contrôle. La fonctionnalité du comportement serait alors implémentée dans le gestionnaire d’événements pour l’événement en question.

La [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) méthode est alimentée lorsque le comportement est retiré du contrôle. Cette méthode reçoit une référence au contrôle auquel elle est attachée et est utilisée pour effectuer tout nettoyage nécessaire. Par exemple, vous pouvez vous désabonner d’un événement sur un contrôle pour empêcher les fuites de mémoire.

Le comportement peut alors être consommé en [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) l’attachant à la collecte du contrôle approprié.

## <a name="creating-a-xamarinforms-behavior"></a>Création d’un comportement Xamarin.Forms

L’application d’échantillon démontre un `NumericValidationBehavior`, qui met [`Entry`](xref:Xamarin.Forms.Entry) en évidence la valeur saisie `double`par l’utilisateur dans un contrôle en rouge, si ce n’est pas un . Le comportement est illustré dans l’exemple de code suivant :

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

Les `NumericValidationBehavior` dérives [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) de la `T` classe, où est un [`Entry`](xref:Xamarin.Forms.Entry). La [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) méthode enregistre un gestionnaire [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) d’événements [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) pour l’événement, avec la méthode de désenregistrement de l’événement `TextChanged` pour prévenir les fuites de mémoire. La fonctionnalité de base du comportement `OnEntryTextChanged` est fournie par la méthode, qui `Entry`analyse la [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) valeur saisie par l’utilisateur `double`dans le , et définit la propriété au rouge si la valeur n’est pas un .

> [!NOTE]
> Xamarin.Forms ne définit pas le `BindingContext` d’un comportement parce que les comportements peuvent être partagés et appliqués à plusieurs contrôles en utilisant des styles.

## <a name="consuming-a-xamarinforms-behavior"></a>Consommation d’un comportement Xamarin.Forms

Chaque contrôle Xamarin.Forms [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) a une collection, à laquelle un ou plusieurs comportements peuvent être ajoutés, comme démontré dans l’exemple de code XAML suivant:

```xaml
<Entry Placeholder="Enter a System.Double">
    <Entry.Behaviors>
        <local:NumericValidationBehavior />
    </Entry.Behaviors>
</Entry>
```

L’équivalent [`Entry`](xref:Xamarin.Forms.Entry) dans le C est indiqué dans l’exemple de code suivant :

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
entry.Behaviors.Add (new NumericValidationBehavior ());
```

Lors de l’exécution, le comportement répond à l’interaction avec le contrôle en fonction de l’implémentation de comportement. Les captures d’écran suivantes illustrent le comportement répondant à une entrée non valide :

[![](creating-images/screenshots-sml.png "Sample Application with Xamarin.Forms Behavior")](creating-images/screenshots.png#lightbox "Sample Application with Xamarin.Forms Behavior")

> [!NOTE]
> Les comportements sont écrits pour un type de contrôle spécifique (ou une superclasse qui peut s’appliquer à de nombreux contrôles) et doivent être ajoutés à un contrôle compatible uniquement. La tentative d’attachement d’un comportement à un contrôle incompatible entraîne la levée d’une exception.

### <a name="consuming-a-xamarinforms-behavior-with-a-style"></a>Consommation d’un comportement Xamarin.Forms avec un style

Les comportements peuvent aussi être consommés par un style explicite ou implicite. Cependant, la création d’un style qui définit la [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) propriété d’un contrôle n’est pas possible parce que la propriété est lue seulement. La solution consiste à ajouter une propriété attachée à la classe de comportement qui contrôle l’ajout et la suppression du comportement. Pour ce faire, procédez comme suit :

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

Le [`Style`](xref:Xamarin.Forms.Style) peut être [`Entry`](xref:Xamarin.Forms.Entry) appliqué à [`Style`](xref:Xamarin.Forms.NavigableElement.Style) un contrôle `Style` en `StaticResource` définissant sa propriété à l’instance en utilisant l’extension de balisage, comme démontré dans l’exemple de code suivant:

```xaml
<Entry Placeholder="Enter a System.Double" Style="{StaticResource NumericValidationStyle}">
```

Pour plus d’informations sur les styles, consultez [Styles](~/xamarin-forms/user-interface/styles/index.md).

> [!NOTE]
> Vous pouvez ajouter des propriétés pouvant être liées à un comportement qui est défini ou interrogé en XAML, mais si vous créez des comportements qui ont un état, ceux-ci ne doivent pas être partagés entre les contrôles d’un `Style` dans un `ResourceDictionary`.

### <a name="removing-a-behavior-from-a-control"></a>Suppression d’un comportement d’un contrôle

La [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) méthode est mise à feu lorsqu’un comportement est retiré d’un contrôle, et est utilisée pour effectuer tout nettoyage requis comme le désabonnement d’un événement pour éviter une fuite de mémoire. Cependant, les comportements ne sont pas implicitement supprimés des contrôles à moins que la collection du contrôle ne soit [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) modifiée par une méthode ou `Remove` `Clear` une méthode. L’exemple de code suivant illustre la suppression d’un comportement spécifique de la collection `Behaviors` d’un contrôle :

```csharp
var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
if (toRemove != null) {
    entry.Behaviors.Remove (toRemove);
}
```

Alternativement, la collection [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) du contrôle peut être effacée, comme le démontre l’exemple de code suivant :

```csharp
entry.Behaviors.Clear();
```

De plus, notez que les comportements ne sont pas supprimés implicitement des contrôles quand les pages sont dépilées de la pile de navigation. Ils doivent être explicitement supprimés avant que les pages ne soient hors de portée.

## <a name="summary"></a>Récapitulatif

Cet article a montré comment créer et consommer des comportements Xamarin.Forms. Les comportements Xamarin.Forms sont créés [`Behavior`](xref:Xamarin.Forms.Behavior) en [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) dérivé de la classe ou de la classe.

## <a name="related-links"></a>Liens connexes

- [Comportement Xamarin.Forms (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehavior)
- [Comportement Xamarin.Forms appliqué avec un style (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehaviorstyle)
- [Comportement](xref:Xamarin.Forms.Behavior)
- [Behavior\<T>](xref:Xamarin.Forms.Behavior`1)
