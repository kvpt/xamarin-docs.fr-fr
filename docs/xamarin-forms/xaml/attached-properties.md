---
title: Propriétés attachées
description: Cet article fournit une introduction aux propriétés jointes et montre comment les créer et les utiliser.
ms.prod: xamarin
ms.assetid: 6E9DCDC3-A0E4-46A6-BAA9-4FEB6DF8A5A8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/02/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b3db63018bc8d927b9e9041c762b1989cfb17679
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374093"
---
# <a name="attached-properties"></a>Propriétés attachées

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)


Les propriétés jointes permettent à un objet d’assigner une valeur pour une propriété qui n’est pas définie par sa propre classe. Par exemple, les éléments enfants peuvent utiliser des propriétés jointes pour informer leur élément parent de la façon dont ils doivent être présentés dans l’interface utilisateur. Le [`Grid`](xref:Xamarin.Forms.Grid) contrôle permet de spécifier la ligne et la colonne d’un enfant en définissant `Grid.Row` les `Grid.Column` propriétés jointes et. `Grid.Row` et `Grid.Column` sont des propriétés jointes, car elles sont définies sur les éléments qui sont des enfants d’un `Grid` , plutôt que sur le `Grid` lui-même.

Les propriétés pouvant être liées doivent être implémentées en tant que propriétés jointes dans les scénarios suivants :

- Quand un mécanisme de définition de propriété doit être disponible pour les classes autres que la classe de définition.
- Lorsque la classe représente un service qui doit être facilement intégré à d’autres classes.

Pour plus d’informations sur les propriétés pouvant être liées, consultez [propriétés pouvant être liées](~/xamarin-forms/xaml/bindable-properties.md).

## <a name="create-an-attached-property"></a>Créer une propriété jointe

Le processus de création d’une propriété jointe est le suivant :

1. Créez une [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) instance de avec l’une des [`CreateAttached`](xref:Xamarin.Forms.BindableProperty.CreateAttached*) surcharges de méthode.
1. Fournissez `static` `Get` les méthodes *PropertyName* et `Set` *PropertyName* comme accesseurs de la propriété jointe.

### <a name="create-a-property"></a>Créer une propriété

Lors de la création d’une propriété jointe pour une utilisation sur d’autres types, la classe dans laquelle la propriété est créée n’a pas besoin d’être dérivée de [`BindableObject`](xref:Xamarin.Forms.BindableObject) . Toutefois, la propriété *cible* pour les accesseurs doit être ou dériver de [`BindableObject`](xref:Xamarin.Forms.BindableObject) .

Une propriété jointe peut être créée en déclarant une `public static readonly` propriété de type [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) . La propriété pouvant être liée doit être définie sur la valeur retournée de l’un des [ `BindableProperty.CreateAttached` ] (XREF : Xamarin.Forms . BindableProperty. CreateAttached (System. String, System. type, System. type, System. Object, Xamarin.Forms . BindingMode, Xamarin.Forms . BindableProperty. ValidateValueDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangedDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangingDelegate, Xamarin.Forms . BindableProperty. CoerceValueDelegate, Xamarin.Forms . BindableProperty. CreateDefaultValueDelegate)) surcharges de la méthode. La déclaration doit se trouver dans le corps de la classe propriétaire, mais en dehors de toutes les définitions de membre.

> [!IMPORTANT]
> La Convention d’affectation de noms pour les propriétés jointes est que l’identificateur de la propriété jointe doit correspondre au nom de propriété spécifié dans la `CreateAttached` méthode, avec « propriété » ajoutée.

Le code suivant illustre un exemple de propriété jointe :

```csharp
public static readonly BindableProperty HasShadowProperty =
  BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false);
```

Cela crée une propriété jointe nommée `HasShadowProperty` , de type `bool` . La propriété appartient à la `ShadowEffect` classe et a une valeur par défaut de `false` .

Pour plus d’informations sur la création de propriétés pouvant être liées, y compris sur les paramètres qui peuvent être spécifiés lors de la création, consultez [créer une propriété pouvant être liée](~/xamarin-forms/xaml/bindable-properties.md#consume-a-bindable-property).

### <a name="create-accessors"></a>Créer des accesseurs

Les `Get` méthodes static *PropertyName* et `Set` *PropertyName* sont requises comme accesseurs pour la propriété jointe ; sinon, le système de propriétés ne peut pas utiliser la propriété jointe. L' `Get` accesseur *PropertyName* doit se conformer à la signature suivante :

```csharp
public static valueType GetPropertyName(BindableObject target)
```

L' `Get` accesseur *PropertyName* doit retourner la valeur contenue dans le `BindableProperty` champ correspondant pour la propriété jointe. Pour ce faire, vous pouvez appeler la [ `GetValue` ] (XREF : Xamarin.Forms . BindableObject. GetValue ( Xamarin.Forms . BindableProperty)), en passant l’identificateur de propriété pouvant être lié sur lequel obtenir la valeur, puis en effectuant un cast de la valeur résultante vers le type requis.

L' `Set` accesseur *PropertyName* doit se conformer à la signature suivante :

```csharp
public static void SetPropertyName(BindableObject target, valueType value)
```

L' `Set` accesseur *PropertyName* doit définir la valeur du `BindableProperty` champ correspondant pour la propriété jointe. Pour ce faire, vous pouvez appeler la [ `SetValue` ] (XREF : Xamarin.Forms . BindableObject. SetValue ( Xamarin.Forms . BindableProperty, System. Object)), en passant l’identificateur de propriété pouvant être lié sur lequel définir la valeur, et la valeur à définir.

Pour les deux accesseurs, l’objet *cible* doit être ou dériver de [`BindableObject`](xref:Xamarin.Forms.BindableObject) .

L’exemple de code suivant montre des accesseurs pour la `HasShadow` propriété jointe :

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

### <a name="consume-an-attached-property"></a>Consommer une propriété jointe

Une fois qu’une propriété jointe a été créée, elle peut être consommée à partir du code XAML ou du code. En XAML, cela est obtenu en déclarant un espace de noms avec un préfixe, avec la déclaration d’espace de noms qui indique le nom de l’espace de noms CLR (Common Language Runtime) et éventuellement un nom d’assembly. Pour plus d’informations, consultez [espaces de noms XAML](~/xamarin-forms/xaml/namespaces.md).

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

### <a name="consume-an-attached-property-with-a-style"></a>Utiliser une propriété jointe avec un style

Les propriétés jointes peuvent également être ajoutées à un contrôle par un style. L’exemple de code XAML suivant montre un style *explicite* qui utilise la `HasShadow` propriété jointe, qui peut être appliquée à des [`Label`](xref:Xamarin.Forms.Label) contrôles :

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="true" />
  </Style.Setters>
</Style>
```

[`Style`](xref:Xamarin.Forms.Style)Peut être appliqué à un [`Label`](xref:Xamarin.Forms.Label) en affectant à sa [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propriété l’instance à l’aide de `Style` l’extension de `StaticResource` balisage, comme illustré dans l’exemple de code suivant :

```xaml
<Label Text="Label Shadow Effect" Style="{StaticResource ShadowEffectStyle}" />
```

Pour plus d’informations sur les styles, consultez [Styles](~/xamarin-forms/user-interface/styles/index.md).

## <a name="advanced-scenarios"></a>Scénarios avancés

Lors de la création d’une propriété jointe, il existe un certain nombre de paramètres facultatifs qui peuvent être définis pour activer les scénarios de propriétés jointes avancés. Cela comprend la détection des modifications de propriété, la validation des valeurs de propriété et la conversion des valeurs de propriété. Pour plus d’informations, consultez [scénarios avancés](~/xamarin-forms/xaml/bindable-properties.md#advanced-scenarios).

## <a name="related-links"></a>Liens connexes

- [Propriétés pouvant être liées](~/xamarin-forms/xaml/bindable-properties.md)
- [Espaces de noms XAML](~/xamarin-forms/xaml/namespaces.md)
- [Effet d’ombre (exemple)](/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)
- [API BindableProperty](xref:Xamarin.Forms.BindableProperty)
- [API BindableObject](xref:Xamarin.Forms.BindableObject)