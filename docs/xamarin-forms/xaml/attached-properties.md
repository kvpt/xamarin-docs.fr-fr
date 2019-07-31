---
title: Propriétés jointes
description: Cet article fournit une introduction aux propriétés jointes et montre comment créer et de les consommer.
ms.prod: xamarin
ms.assetid: 6E9DCDC3-A0E4-46A6-BAA9-4FEB6DF8A5A8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/02/2016
ms.openlocfilehash: c8be9220f17a0f0f046f09ff61ae76d1fed7e15f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644799"
---
# <a name="attached-properties"></a>Propriétés jointes

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)

_Une propriété jointe est un type spécial de propriété pouvant être liée, définie dans une classe mais associé à d’autres objets et reconnaissable dans XAML en tant qu’attribut qui contient une classe et un nom de propriété séparés par un point. Cet article fournit une introduction aux propriétés jointes et montre comment créer et de les consommer._

## <a name="overview"></a>Vue d'ensemble

Joint des propriétés, activer un objet pour affecter une valeur pour une propriété qui ne définit pas sa propre classe. Par exemple, enfants d’éléments peuvent utiliser des propriétés pour informer de leur élément parent de la façon dont ils doivent être présentés dans l’interface utilisateur attachées. Le [ `Grid` ](xref:Xamarin.Forms.Grid) contrôle permet à la ligne et colonne d’un enfant d’être spécifiée en définissant le `Grid.Row` et `Grid.Column` propriétés jointes. `Grid.Row` et `Grid.Column` sont des propriétés jointes car ils sont définis sur les éléments qui sont des enfants d’un `Grid`, plutôt que sur le `Grid` lui-même.

Propriétés pouvant être liées doivent être implémentées en tant que propriétés jointes dans les scénarios suivants :

- Lorsqu’il est nécessaire d’avoir un mécanisme de définition des propriétés disponible pour les classes autres que la définition de classe.
- Lorsque la classe représente un service qui doit être intégré facilement avec d’autres classes.

Pour plus d’informations sur les propriétés pouvant être liées, consultez [propriétés pouvant être liées](~/xamarin-forms/xaml/bindable-properties.md).

## <a name="creating-and-consuming-an-attached-property"></a>Création et utilisation d’une propriété jointe

Le processus de création d’une propriété jointe est comme suit :

1. Créer un [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instance avec l’un de le [ `CreateAttached` ](xref:Xamarin.Forms.BindableProperty.CreateAttached*) surcharges de méthode.
1. Fournir `static` `Get` *PropertyName* et `Set` *PropertyName* méthodes comme accesseurs pour la propriété jointe.

### <a name="creating-a-property"></a>Création d’une propriété

Lorsque vous créez une propriété jointe pour une utilisation sur d’autres types, la classe où la propriété est créée ne devra pas dériver de [ `BindableObject` ](xref:Xamarin.Forms.BindableObject). Toutefois, le *cible* propriété pour les accesseurs doit-elle être d’ou dérivent, [ `BindableObject` ](xref:Xamarin.Forms.BindableObject).

Une propriété jointe peut être créée en déclarant un `public static readonly` propriété de type [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty). La propriété peut être liée doit être définie sur la valeur retournée d’un de la [ `BindableProperty.CreateAttached` ](xref:Xamarin.Forms.BindableProperty.CreateAttached(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) surcharges de méthode. La déclaration doit être dans le corps de la classe propriétaire, mais en dehors de toutes les définitions de membre.

Le code suivant montre un exemple d’une propriété jointe :

```csharp
public static readonly BindableProperty HasShadowProperty =
  BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false);
```

Cette opération crée une propriété jointe nommée `HasShadow`, de type `bool`. La propriété est possédée par le `ShadowEffect` classe, et a la valeur par défaut `false`. La convention d’affectation de noms pour les propriétés jointes est que l’identificateur de la propriété jointe doit correspondre au nom de propriété spécifié dans le `CreateAttached` méthode, avec « Property » est ajoutée. Par conséquent, dans l’exemple ci-dessus, l’identificateur de la propriété jointe est `HasShadowProperty`.

Pour plus d’informations sur la création de propriétés pouvant être liées, y compris les paramètres qui peuvent être spécifiés lors de la création, consultez [création et utilisation d’une propriété peut être liée](~/xamarin-forms/xaml/bindable-properties.md#consuming-bindable-property).

### <a name="creating-accessors"></a>Création d’accesseurs

Statique `Get` *PropertyName* et `Set` *PropertyName* méthodes sont nécessaires comme accesseurs pour la propriété jointe, sinon le système de propriétés ne peut pas utiliser la propriété jointe. Le `Get` *PropertyName* accesseur doit être conforme à la signature suivante :

```csharp
public static valueType GetPropertyName(BindableObject target)
```

Le `Get` *PropertyName* accesseur doit retourner la valeur qui est contenue dans le correspondantes `BindableProperty` champ pour la propriété jointe. Cela peut être obtenue en appelant le [ `GetValue` ](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)) (méthode), en passant l’identificateur de propriété pouvant être liée sur laquelle obtenir la valeur et puis cast la valeur résultante dans le type requis.

Le `Set` *PropertyName* accesseur doit être conforme à la signature suivante :

```csharp
public static void SetPropertyName(BindableObject target, valueType value)
```

Le `Set` *PropertyName* accesseur doit définir la valeur correspondante `BindableProperty` champ pour la propriété jointe. Cela peut être obtenue en appelant le [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) méthode, en passant l’identificateur de propriété pouvant être liée sur lequel définir la valeur et la valeur à définir.

Pour les accesseurs, le *cible* objet doit être de, ou dériver, [ `BindableObject` ](xref:Xamarin.Forms.BindableObject).

L’exemple de code suivant montre des accesseurs pour le `HasShadow` propriété jointe :

```csharp
public static bool GetHasShadow (BindableObject view)
{
  return (bool)view.GetValue (HasShadowProperty);
}

public static void SetHasShadow (BindableObject view, bool value)
{
  view.SetValue (HasShadowProperty, value);
}
```

### <a name="consuming-an-attached-property"></a>Utilisation d’une propriété jointe

Une fois qu’une propriété jointe a été créée, il peut être consommé à partir de XAML ou de code. Dans XAML, cela est accompli en déclarant un espace de noms avec un préfixe, avec la déclaration d’espace de noms indiquant l’espace de noms Common Language Runtime (CLR) et éventuellement un nom d’assembly. Pour plus d’informations, consultez [espaces de noms XAML](~/xamarin-forms/xaml/namespaces.md).

L’exemple de code suivant montre un espace de noms XAML pour un type personnalisé qui contient une propriété jointe, qui est définie dans le même assembly que le code d’application qui référence le type personnalisé :

```xaml
<ContentPage ... xmlns:local="clr-namespace:EffectsDemo" ...>
  ...
</ContentPage>
```

La déclaration d’espace de noms est ensuite utilisée lors de la définition de la propriété jointe sur un contrôle spécifique, comme illustré dans l’exemple de code XAML suivant :

```xaml
<Label Text="Label Shadow Effect" local:ShadowEffect.HasShadow="true" />
```

Le code C# équivalent est affiché dans l’exemple de code suivant :

```csharp
var label = new Label { Text = "Label Shadow Effect" };
ShadowEffect.SetHasShadow (label, true);
```

### <a name="consuming-an-attached-property-with-a-style"></a>Utilisation d’une propriété jointe avec un Style

Propriétés jointes peuvent également être ajoutées à un contrôle par un style. L’exemple de code XAML suivant montre une *explicite* style qui utilise le `HasShadow` propriété jointe, qui peut être appliquée à [ `Label` ](xref:Xamarin.Forms.Label) contrôles :

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="true" />
  </Style.Setters>
</Style>
```

Le [ `Style` ](xref:Xamarin.Forms.Style) peut être appliqué à un [ `Label` ](xref:Xamarin.Forms.Label) en définissant son [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) propriété le `Style` instance à l’aide de la `StaticResource`extension de balisage, comme illustré dans l’exemple de code suivant :

```xaml
<Label Text="Label Shadow Effect" Style="{StaticResource ShadowEffectStyle}" />
```

Pour plus d’informations sur les styles, consultez [Styles](~/xamarin-forms/user-interface/styles/index.md).

## <a name="advanced-scenarios"></a>Scénarios avancés

Lorsque vous créez une propriété jointe, il existe un nombre de paramètres facultatifs qui peuvent être définies pour permettre les scénarios avancés de propriété jointe. Cela inclut la détection des modifications apportées aux propriétés, valider des valeurs de propriété et forçage de valeurs de propriété. Pour plus d’informations, consultez [scénarios avancés](~/xamarin-forms/xaml/bindable-properties.md#advanced).

## <a name="summary"></a>Récapitulatif

Cet article propose une introduction à des propriétés jointes et vous a montré comment créer et de les consommer. Une propriété jointe est un type spécial de propriété pouvant être liée, définie dans une classe mais jointe à d’autres objets et reconnaissable dans XAML en tant qu’attributs qui contiennent une classe et un nom de propriété séparés par un point.


## <a name="related-links"></a>Liens associés

- [Propriétés pouvant être liées](~/xamarin-forms/xaml/bindable-properties.md)
- [Espaces de noms XAML](~/xamarin-forms/xaml/namespaces.md)
- [Effet d’ombre portée (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)
- [BindableProperty](xref:Xamarin.Forms.BindableProperty)
- [BindableObject](xref:Xamarin.Forms.BindableObject)
