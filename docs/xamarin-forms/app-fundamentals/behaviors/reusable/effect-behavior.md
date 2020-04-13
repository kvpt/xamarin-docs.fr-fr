---
title: EffectBehavior réutilisable
description: Les comportements sont une approche utile pour ajouter un effet à un contrôle, tout en supprimant le code de gestion de l’effet réutilisable dans les fichiers code-behind. Cet article montre comment créer et utiliser un comportement Xamarin.Forms pour ajouter un effet à un contrôle.
ms.prod: xamarin
ms.assetid: A909B24D-960A-4023-AFF6-4B9256C55ADD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: ca03dce3bd39664a07b7bf56d22d7c2e000e931f
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771996"
---
# <a name="reusable-effectbehavior"></a>EffectBehavior réutilisable

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)

_Les comportements sont une approche utile pour ajouter un effet à un contrôle, en supprimant le code de manipulation des effets de la plaque de chaudière des fichiers de code.derrière. Cet article démontre la création et la consommation d’un comportement Xamarin.Forms pour ajouter un effet à un contrôle._

## <a name="overview"></a>Vue d’ensemble

La `EffectBehavior` classe est un comportement personnalisé réutilisable [`Effect`](xref:Xamarin.Forms.Effect) Xamarin.Forms qui ajoute une instance à un `Effect` contrôle lorsque le comportement est attaché au contrôle, et supprime l’instance lorsque le comportement est détaché du contrôle.

Les propriétés de comportement suivantes doivent être définies pour utiliser le comportement :

- **Group** – valeur de l’attribut [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) pour la classe de l’effet.
- **Name** – valeur de l’attribut [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) pour la classe de l’effet.

Pour plus d’informations sur les effets, consultez [Effets](~/xamarin-forms/app-fundamentals/effects/index.md).

> [!NOTE]
> `EffectBehavior` est une classe personnalisée qui peut se trouver dans l’[exemple Comportement Effet](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior) et qui ne fait pas partie de Xamarin.Forms.

## <a name="creating-the-behavior"></a>Création du comportement

La `EffectBehavior` classe dérive [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) de la `T` classe, où est un [`View`](xref:Xamarin.Forms.View). Cela signifie que la classe `EffectBehavior` peut être attachée à n’importe quel contrôle Xamarin.Forms.

### <a name="implementing-bindable-properties"></a>Implémentation des propriétés pouvant être liées

La `EffectBehavior` classe définit [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) deux instances, qui [`Effect`](xref:Xamarin.Forms.Effect) sont utilisées pour ajouter un contrôle lorsque le comportement est attaché au contrôle. Ces propriétés sont présentées dans l’exemple de code suivant :

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

Lorsque `EffectBehavior` la propriété est `Group` consommée, la propriété [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) doit être réglée à la valeur de l’attribut pour l’effet. En outre, `Name` la propriété doit être [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) réglée à la valeur de l’attribut pour la classe d’effet.

### <a name="implementing-the-overrides"></a>Implémentation des remplacements

La `EffectBehavior` classe l’emporte [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) sur [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) les [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) méthodes et les méthodes de la classe, comme le montre l’exemple de code suivant :

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

La [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) méthode effectue la `AddEffect` configuration en appelant la méthode, en passant dans le contrôle ci-joint comme un paramètre. La [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) méthode effectue le nettoyage `RemoveEffect` en appelant la méthode, en passant dans le contrôle ci-joint comme un paramètre.

### <a name="implementing-the-behavior-functionality"></a>Implémentation de la fonctionnalité de comportement

Le but du comportement est [`Effect`](xref:Xamarin.Forms.Effect) d’ajouter `Name` le défini dans le et les `Group` propriétés `Effect` à un contrôle lorsque le comportement est attaché au contrôle, et supprimer le lorsque le comportement est détaché du contrôle. La fonctionnalité de comportement clé est illustrée dans l’exemple de code suivant :

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

La méthode `AddEffect` est exécutée en réponse à l’attachement de l’`EffectBehavior` à un contrôle et reçoit le contrôle attaché en tant que paramètre. La méthode ajoute ensuite l’effet récupéré [`Effects`](xref:Xamarin.Forms.Element.Effects) à la collection du contrôle. La méthode `RemoveEffect` est exécutée en réponse au détachement de l’`EffectBehavior` d’un contrôle et reçoit le contrôle détaché en tant que paramètre. La méthode supprime ensuite l’effet [`Effects`](xref:Xamarin.Forms.Element.Effects) de la collection du contrôle.

La `GetEffect` méthode [`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) utilise la [`Effect`](xref:Xamarin.Forms.Effect)méthode pour récupérer le . L’effet se trouve dans une concaténation des valeurs de la propriété `Group` et `Name`. Si une plateforme ne fournit pas l’effet, la méthode `Effect.Resolve` retourne une valeur non `null`.

## <a name="consuming-the-behavior"></a>Consommation du comportement

La `EffectBehavior` classe peut être [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) jointe à la collection d’un contrôle, comme le démontre l’exemple de code XAML suivant :

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

Les `Group` `Name` propriétés et les propriétés du [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) comportement sont définies aux valeurs et aux attributs pour la classe d’effet dans chaque projet spécifique à chaque plate-forme.

Au moment de l’exécution, [`Label`](xref:Xamarin.Forms.Label) lorsque le `Xamarin.LabelShadowEffect` comportement est attaché au [`Effects`](xref:Xamarin.Forms.Element.Effects) contrôle, le sera ajouté à la collection du contrôle. Il en résulte l’ajout d’une ombre au texte affiché par le contrôle `Label`, comme illustré dans les captures d’écran suivantes :

![](effect-behavior-images/screenshots.png "Sample Application with EffectsBehavior")

L’avantage d’utiliser ce comportement pour ajouter et supprimer des effets dans des contrôles est que le code réutilisable qui gère les effets peut être supprimé des fichiers code-behind.

## <a name="summary"></a>Récapitulatif

Cet article a décrit l’utilisation d’un comportement pour ajouter un effet à un contrôle. La `EffectBehavior` classe est un comportement personnalisé réutilisable [`Effect`](xref:Xamarin.Forms.Effect) Xamarin.Forms qui ajoute une instance à un `Effect` contrôle lorsque le comportement est attaché au contrôle, et supprime l’instance lorsque le comportement est détaché du contrôle.

## <a name="related-links"></a>Liens connexes

- [Effets](~/xamarin-forms/app-fundamentals/effects/index.md)
- [EffectBehavior (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)
- [Comportement](xref:Xamarin.Forms.Behavior)
- [Comportement&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
