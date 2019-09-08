---
title: EffectBehavior réutilisable
description: Les comportements constituent une approche utile pour ajouter un effet à un contrôle, en supprimant des fichiers code-behind le code réutilisable qui gère les effets. Cet article montre comment créer et consommer un comportement Xamarin.Forms pour ajouter un effet à un contrôle.
ms.prod: xamarin
ms.assetid: A909B24D-960A-4023-AFF6-4B9256C55ADD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: ca03dce3bd39664a07b7bf56d22d7c2e000e931f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771996"
---
# <a name="reusable-effectbehavior"></a>EffectBehavior réutilisable

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)

_Les comportements constituent une approche utile pour ajouter un effet à un contrôle, en supprimant des fichiers code-behind le code réutilisable qui gère les effets. Cet article montre comment créer et utiliser un comportement Xamarin.Forms pour ajouter un effet à un contrôle._

## <a name="overview"></a>Présentation

La classe `EffectBehavior` est un comportement personnalisé Xamarin.Forms réutilisable qui ajoute une instance [ `Effect` ](xref:Xamarin.Forms.Effect) à un contrôle quand le comportement est attaché au contrôle et supprime l’instance `Effect` quand le comportement est détaché du contrôle.

Les propriétés de comportement suivantes doivent être définies pour utiliser le comportement :

- **Group** – valeur de l’attribut [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute) pour la classe de l’effet.
- **Name** – valeur de l’attribut [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute) pour la classe de l’effet.

Pour plus d’informations sur les effets, consultez [Effets](~/xamarin-forms/app-fundamentals/effects/index.md).

> [!NOTE]
> `EffectBehavior` est une classe personnalisée qui peut se trouver dans l’[exemple Comportement Effet](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior) et qui ne fait pas partie de Xamarin.Forms.

## <a name="creating-the-behavior"></a>Création du comportement

La classe `EffectBehavior` dérive de la classe [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1), où `T` est une [`View`](xref:Xamarin.Forms.View). Cela signifie que la classe `EffectBehavior` peut être attachée à n’importe quel contrôle Xamarin.Forms.

### <a name="implementing-bindable-properties"></a>Implémentation des propriétés pouvant être liées

La classe `EffectBehavior` définit deux instances [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), qui sont utilisées pour ajouter un [`Effect`](xref:Xamarin.Forms.Effect) à un contrôle quand le comportement est attaché au contrôle. Ces propriétés sont présentées dans l’exemple de code suivant :

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

Quand l’`EffectBehavior` est consommé, la propriété `Group` doit être définie avec la valeur de l’attribut [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) de l’effet. De plus, la propriété `Name` doit être définie avec la valeur de l’attribut [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) de la classe de l’effet.

### <a name="implementing-the-overrides"></a>Implémentation des remplacements

La classe `EffectBehavior` remplace les méthodes [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) et [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) de la classe [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1), comme illustré dans l’exemple de code suivant :

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

La méthode [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) effectue la configuration en appelant la méthode `AddEffect` pour passer le contrôle attaché en tant que paramètre. La méthode [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) effectue le nettoyage en appelant la méthode `RemoveEffect` pour passer le contrôle attaché en tant que paramètre.

### <a name="implementing-the-behavior-functionality"></a>Implémentation de la fonctionnalité de comportement

L’objectif du comportement consiste à ajouter l’[`Effect`](xref:Xamarin.Forms.Effect) défini dans les propriétés `Group` et `Name` à un contrôle quand le comportement est attaché au contrôle et à supprimer l’`Effect` quand le comportement est détaché du contrôle. La fonctionnalité de comportement clé est illustrée dans l’exemple de code suivant :

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

La méthode `AddEffect` est exécutée en réponse à l’attachement de l’`EffectBehavior` à un contrôle et reçoit le contrôle attaché en tant que paramètre. La méthode ajoute ensuite l’effet récupéré à la collection [`Effects`](xref:Xamarin.Forms.Element.Effects) du contrôle. La méthode `RemoveEffect` est exécutée en réponse au détachement de l’`EffectBehavior` d’un contrôle et reçoit le contrôle détaché en tant que paramètre. La méthode supprime ensuite l’effet de la collection [`Effects`](xref:Xamarin.Forms.Element.Effects) du contrôle.

La méthode `GetEffect` utilise la méthode [`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) pour récupérer l’[`Effect`](xref:Xamarin.Forms.Effect). L’effet se trouve dans une concaténation des valeurs de la propriété `Group` et `Name`. Si une plateforme ne fournit pas l’effet, la méthode `Effect.Resolve` retourne une valeur non `null`.

## <a name="consuming-the-behavior"></a>Consommation du comportement

La classe `EffectBehavior` peut être attachée à la collection [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) d’un contrôle, comme illustré dans l’exemple de code XAML suivant :

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

Les propriétés `Group` et `Name` du comportement sont définies avec les valeurs des attributs [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) et [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) de la classe de l’effet dans chaque projet spécifique à une plateforme.

Lors de l’exécution, quand le comportement est attaché au contrôle [`Label`](xref:Xamarin.Forms.Label), le `Xamarin.LabelShadowEffect` est ajouté à la collection [ `Effects` ](xref:Xamarin.Forms.Element.Effects) du contrôle. Il en résulte l’ajout d’une ombre au texte affiché par le contrôle `Label`, comme illustré dans les captures d’écran suivantes :

![](effect-behavior-images/screenshots.png "Exemple d’application avec EffectsBehavior")

L’avantage d’utiliser ce comportement pour ajouter et supprimer des effets dans des contrôles est que le code réutilisable qui gère les effets peut être supprimé des fichiers code-behind.

## <a name="summary"></a>Récapitulatif

Cet article a décrit l’utilisation d’un comportement pour ajouter un effet à un contrôle. La classe `EffectBehavior` est un comportement personnalisé Xamarin.Forms réutilisable qui ajoute une instance [ `Effect` ](xref:Xamarin.Forms.Effect) à un contrôle quand le comportement est attaché au contrôle et supprime l’instance `Effect` quand le comportement est détaché du contrôle.

## <a name="related-links"></a>Liens associés

- [Effets](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Comportement Effect (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)
- [Behavior](xref:Xamarin.Forms.Behavior)
- [Behavior&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
