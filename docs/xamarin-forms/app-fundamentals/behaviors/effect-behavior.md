---
title: EffectBehavior réutilisable
description: Les comportements sont une approche utile pour ajouter un effet à un contrôle, tout en supprimant le code de gestion de l’effet réutilisable dans les fichiers code-behind. Cet article décrit la création et l’utilisation d’un Xamarin.Forms comportement pour ajouter un effet à un contrôle.
ms.prod: xamarin
ms.assetid: A909B24D-960A-4023-AFF6-4B9256C55ADD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3a085226da3c63f4f151bc657976bba384c02170
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561142"
---
# <a name="reusable-effectbehavior"></a>EffectBehavior réutilisable

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)

_Les comportements sont une approche utile pour ajouter un effet à un contrôle, en supprimant le code de gestion de l’effet de la plaque de chaudière des fichiers code-behind. Cet article décrit la création et l’utilisation d’un Xamarin.Forms comportement pour ajouter un effet à un contrôle._

## <a name="overview"></a>Vue d’ensemble

La `EffectBehavior` classe est un comportement personnalisé réutilisable Xamarin.Forms qui ajoute une [`Effect`](xref:Xamarin.Forms.Effect) instance à un contrôle lorsque le comportement est attaché au contrôle et supprime l' `Effect` instance lorsque le comportement est détaché du contrôle.

Les propriétés de comportement suivantes doivent être définies pour utiliser le comportement :

- **Group** – valeur de l’attribut [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) pour la classe de l’effet.
- **Name** – valeur de l’attribut [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) pour la classe de l’effet.

Pour plus d’informations sur les effets, consultez [Effets](~/xamarin-forms/app-fundamentals/effects/index.md).

> [!NOTE]
> `EffectBehavior`Est une classe personnalisée qui peut se trouver dans l' [exemple de comportement d’effet](/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)et qui ne fait pas partie de Xamarin.Forms .

## <a name="creating-the-behavior"></a>Création du comportement

La `EffectBehavior` classe dérive de la [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe, où `T` est un [`View`](xref:Xamarin.Forms.View) . Cela signifie que la `EffectBehavior` classe peut être attachée à n’importe quel Xamarin.Forms contrôle.

### <a name="implementing-bindable-properties"></a>Implémentation des propriétés pouvant être liées

La `EffectBehavior` classe définit deux [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) instances, qui sont utilisées pour ajouter un [`Effect`](xref:Xamarin.Forms.Effect) à un contrôle lorsque le comportement est attaché au contrôle. Ces propriétés sont présentées dans l’exemple de code suivant :

```csharp
public class EffectBehavior : Behavior<View>
{
  public static readonly BindableProperty GroupProperty =
    BindableProperty.Create ("Group", typeof(string), typeof(EffectBehavior), null);
  public static readonly BindableProperty NameProperty =
    BindableProperty.Create ("Name", typeof(string), typeof(EffectBehavior), null);

  public string Group {
    get { return (string)GetValue (GroupProperty); }
    set { SetValue (GroupProperty, value); }
  }

  public string Name {
    get { return(string)GetValue (NameProperty); }
    set { SetValue (NameProperty, value); }
  }
  ...
}
```

Lorsque `EffectBehavior` est consommé, la `Group` propriété doit être définie sur la valeur de l' [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) attribut pour l’effet. En outre, la `Name` propriété doit être définie sur la valeur de l' [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) attribut pour la classe Effect.

### <a name="implementing-the-overrides"></a>Implémentation des remplacements

La `EffectBehavior` classe se substitue à [ `OnAttachedTo` ] (XREF : Xamarin.Forms . Comportement `1.OnAttachedTo(Xamarin.Forms.BindableObject)) and [` OnDetachingFrom `](xref:Xamarin.Forms.Behavior` 1. OnDetachingFrom ( Xamarin.Forms . BindableObject)) de la [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe, comme indiqué dans l’exemple de code suivant :

```csharp
public class EffectBehavior : Behavior<View>
{
  ...
  protected override void OnAttachedTo (BindableObject bindable)
  {
    base.OnAttachedTo (bindable);
    AddEffect (bindable as View);
  }

  protected override void OnDetachingFrom (BindableObject bindable)
  {
    RemoveEffect (bindable as View);
    base.OnDetachingFrom (bindable);
  }
  ...
}
```

[ `OnAttachedTo` ] (XREF : Xamarin.Forms . Comportement `1.OnAttachedTo(Xamarin.Forms.BindableObject)) method performs setup by calling the ` AddEffect ` method, passing in the attached control as a parameter. The [` OnDetachingFrom `](xref:Xamarin.Forms.Behavior` 1. OnDetachingFrom ( Xamarin.Forms . BindableObject)). la méthode effectue le nettoyage en appelant la `RemoveEffect` méthode, en passant le contrôle attaché en tant que paramètre.

### <a name="implementing-the-behavior-functionality"></a>Implémentation de la fonctionnalité de comportement

L’objectif du comportement est d’ajouter le [`Effect`](xref:Xamarin.Forms.Effect) défini dans les `Group` Propriétés et `Name` à un contrôle lorsque le comportement est attaché au contrôle, et de supprimer `Effect` lorsque le comportement est détaché du contrôle. La fonctionnalité de comportement clé est illustrée dans l’exemple de code suivant :

```csharp
public class EffectBehavior : Behavior<View>
{
  ...
  void AddEffect (View view)
  {
    var effect = GetEffect ();
    if (effect != null) {
      view.Effects.Add (GetEffect ());
    }
  }

  void RemoveEffect (View view)
  {
    var effect = GetEffect ();
    if (effect != null) {
      view.Effects.Remove (GetEffect ());
    }
  }

  Effect GetEffect ()
  {
    if (!string.IsNullOrWhiteSpace (Group) && !string.IsNullOrWhiteSpace (Name)) {
      return Effect.Resolve (string.Format ("{0}.{1}", Group, Name));
    }
    return null;
  }
}
```

La méthode `AddEffect` est exécutée en réponse à l’attachement de l’`EffectBehavior` à un contrôle et reçoit le contrôle attaché en tant que paramètre. La méthode ajoute ensuite l’effet récupéré à la collection du contrôle [`Effects`](xref:Xamarin.Forms.Element.Effects) . La méthode `RemoveEffect` est exécutée en réponse au détachement de l’`EffectBehavior` d’un contrôle et reçoit le contrôle détaché en tant que paramètre. La méthode supprime ensuite l’effet de la collection du contrôle [`Effects`](xref:Xamarin.Forms.Element.Effects) .

La `GetEffect` méthode utilise la [`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) méthode pour récupérer [`Effect`](xref:Xamarin.Forms.Effect) . L’effet se trouve dans une concaténation des valeurs de la propriété `Group` et `Name`. Si une plateforme ne fournit pas l’effet, la méthode `Effect.Resolve` retourne une valeur non `null`.

## <a name="consuming-the-behavior"></a>Consommation du comportement

La `EffectBehavior` classe peut être attachée à la [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) collection d’un contrôle, comme illustré dans l’exemple de code XAML suivant :

```xaml
<Label Text="Label Shadow Effect" ...>
  <Label.Behaviors>
    <local:EffectBehavior Group="Xamarin" Name="LabelShadowEffect" />
  </Label.Behaviors>
</Label>
```

Le code C# équivalent est affiché dans l’exemple de code suivant :

```csharp
var label = new Label {
  Text = "Label Shadow Effect",
  ...
};
label.Behaviors.Add (new EffectBehavior {
  Group = "Xamarin",
  Name = "LabelShadowEffect"
});
```

Les `Group` `Name` Propriétés et du comportement sont définies sur les valeurs des [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) attributs et de [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) la classe Effect dans chaque projet spécifique à la plateforme.

Lors de l’exécution, lorsque le comportement est attaché au [`Label`](xref:Xamarin.Forms.Label) contrôle, le est `Xamarin.LabelShadowEffect` ajouté à la collection du contrôle [`Effects`](xref:Xamarin.Forms.Element.Effects) . Il en résulte l’ajout d’une ombre au texte affiché par le contrôle `Label`, comme illustré dans les captures d’écran suivantes :

![Exemple d’application avec EffectsBehavior](effect-behavior-images/screenshots.png)

L’avantage d’utiliser ce comportement pour ajouter et supprimer des effets dans des contrôles est que le code réutilisable qui gère les effets peut être supprimé des fichiers code-behind.

## <a name="summary"></a>Résumé

Cet article a décrit l’utilisation d’un comportement pour ajouter un effet à un contrôle. La `EffectBehavior` classe est un comportement personnalisé réutilisable Xamarin.Forms qui ajoute une [`Effect`](xref:Xamarin.Forms.Effect) instance à un contrôle lorsque le comportement est attaché au contrôle et supprime l' `Effect` instance lorsque le comportement est détaché du contrôle.

## <a name="related-links"></a>Liens associés

- [Effects (Effets)](~/xamarin-forms/app-fundamentals/effects/index.md)
- [EffectBehavior (exemple)](/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)
- [Comportement](xref:Xamarin.Forms.Behavior)
- [Comportement &lt; T&gt;](xref:Xamarin.Forms.Behavior`1)