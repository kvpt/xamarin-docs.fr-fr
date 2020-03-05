---
title: Propriétés pouvant être liées par Xamarin. Forms
description: Cet article fournit une introduction aux propriétés pouvant être liées et montre comment créer et de les consommer.
ms.prod: xamarin
ms.assetid: 1EE869D8-6FE1-45CA-A0AD-26EC7D032AD7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
ms.openlocfilehash: 56583aa0df676ae55e1b283f1a8e151b69a13d28
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "78291767"
---
# <a name="xamarinforms-bindable-properties"></a>Propriétés pouvant être liées par Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

Les propriétés pouvant être liées étendent la fonctionnalité de propriété CLR en sauvegardant une propriété avec un type de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , au lieu de sauvegarder une propriété avec un champ. L’objectif de propriétés pouvant être liées est de fournir un système de propriétés qui prend en charge la liaison de données, les styles, modèles, et les valeurs définies via des relations parent-enfant. En outre, les propriétés pouvant être liées peuvent fournir des valeurs par défaut, validation des valeurs de propriété et les rappels qui surveillent les modifications apportées aux propriétés.

Propriétés doivent être implémentées en tant que propriétés pouvant être liées pour prendre en charge un ou plusieurs des fonctionnalités suivantes :

- Agissant comme une propriété *cible* valide pour la liaison de données.
- Définition de la propriété à l’aide d’un [style](~/xamarin-forms/user-interface/styles/index.md).
- En fournissant une valeur de propriété par défaut qui est différente de la valeur par défaut pour le type de la propriété.
- Validation de la valeur de la propriété.
- Surveillance des modifications de propriété.

[`Label.Text`](xref:Xamarin.Forms.Label.Text), [`Button.BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius)et [`StackLayout.Orientation`](xref:Xamarin.Forms.StackLayout.Orientation)sont des exemples de propriétés pouvant être liées par Xamarin. Forms. Chaque propriété pouvant être liée a une propriété `public static readonly` correspondante de type [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) qui est exposée sur la même classe et qui est l’identificateur de la propriété pouvant être liée. Par exemple, l’identificateur de propriété pouvant être lié correspondant à la propriété `Label.Text` est [`Label.TextProperty`](xref:Xamarin.Forms.Label.TextProperty).

## <a name="create-a-bindable-property"></a>Créer une propriété pouvant être liée

Le processus de création d’une propriété pouvant être liée est comme suit :

1. Créez une instance [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) avec l’une des surcharges de méthode [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create*) .
1. Définissez les accesseurs de propriété pour l’instance [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) .

Toutes les instances de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) doivent être créées sur le thread d’interface utilisateur. Cela signifie que seul le code qui s’exécute sur le thread d’interface utilisateur peut obtenir ou définir la valeur d’une propriété pouvant être liée. Toutefois, les instances `BindableProperty` sont accessibles à partir d’autres threads en marshalant vers le thread d’interface utilisateur avec la méthode [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) .

### <a name="create-a-property"></a>Créer une propriété

Pour créer une instance `BindableProperty`, la classe conteneur doit dériver de la classe [`BindableObject`](xref:Xamarin.Forms.BindableObject) . Toutefois, la classe `BindableObject` est élevée dans la hiérarchie de classes, donc la majorité des classes utilisées pour les fonctionnalités de l’interface utilisateur prennent en charge les propriétés pouvant être liées.

Une propriété pouvant être liée peut être créée en déclarant une propriété `public static readonly` de type [`BindableProperty`](xref:Xamarin.Forms.BindableProperty). La propriété pouvant être liée doit être définie sur la valeur retournée de l’une des surcharges de la méthode [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) . La déclaration doit se trouver dans le corps d' [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe dérivée, mais en dehors de toutes les définitions de membre.

Au minimum, un identificateur doit être spécifié lors de la création d’un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), avec les paramètres suivants :

- Nom de l' [`BindableProperty`](xref:Xamarin.Forms.BindableProperty).
- Type de la propriété.
- Le type de l’objet propriétaire.
- Valeur par défaut de la propriété. Cela garantit que la propriété retourne toujours une valeur par défaut particulier lorsqu’il n’est pas définie, et il peut être différent de la valeur par défaut pour le type de la propriété. La valeur par défaut est restaurée lorsque la méthode [`ClearValue`](xref:Xamarin.Forms.BindableObject.ClearValue(Xamarin.Forms.BindableProperty)) est appelée sur la propriété pouvant être liée.

Le code suivant montre un exemple d’une propriété pouvant être liée, avec un identificateur et les valeurs pour les quatre paramètres obligatoires :

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null);
```

Cela crée une instance [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) nommée `EventName`, de type `string`. La propriété appartient à la classe `EventToCommandBehavior` et a une valeur par défaut de `null`. La Convention d’affectation de noms pour les propriétés pouvant être liées est que l’identificateur de propriété pouvant être lié doit correspondre au nom de propriété spécifié dans la méthode `Create`, avec « propriété » ajoutée. Par conséquent, dans l’exemple ci-dessus, l’identificateur de propriété pouvant être lié est `EventNameProperty`.

Si vous le souhaitez, lors de la création d’une instance de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , vous pouvez spécifier les paramètres suivants :

- Mode de liaison. Cela est utilisé pour spécifier la direction dans laquelle seront propager les modifications de valeur de propriété. Dans le mode de liaison par défaut, les modifications sont propagées de la *source* à la *cible*.
- Un délégué de validation qui sera appelé lorsque la valeur de propriété est définie. Pour plus d’informations, consultez [rappels de validation](#validation-callbacks).
- Un délégué de modification de propriété qui sera appelé lorsque la valeur de propriété a changé. Pour plus d’informations, consultez [détecter les modifications de propriété](#detect-property-changes).
- Une propriété de modification de délégué qui sera appelé lorsque la valeur de propriété est modifiée. Ce délégué a la même signature que le délégué de modification de propriété.
- Un délégué de valeur forcée qui sera appelé lorsque la valeur de propriété a changé. Pour plus d’informations, consultez [forçage des rappels de valeur](#coerce-value-callbacks).
- `Func` utilisé pour initialiser une valeur de propriété par défaut. Pour plus d’informations, consultez [créer une valeur par défaut avec un Func](#create-a-default-value-with-a-func).

### <a name="create-accessors"></a>Créer des accesseurs

Les accesseurs de propriété sont requis pour utiliser la syntaxe de la propriété pour accéder à une propriété pouvant être liée. L’accesseur `Get` doit retourner la valeur contenue dans la propriété pouvant être liée correspondante. Pour ce faire, appelez la méthode [`GetValue`](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)) , en passant l’identificateur de propriété pouvant être lié sur lequel obtenir la valeur, puis effectuez un cast du résultat vers le type requis. L’accesseur `Set` doit définir la valeur de la propriété pouvant être liée correspondante. Pour ce faire, vous pouvez appeler la méthode [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) , en passant l’identificateur de propriété pouvant être lié sur lequel définir la valeur, et la valeur à définir.

L’exemple de code suivant montre des accesseurs pour la propriété `EventName` pouvant être liée :

```csharp
public string EventName
{
  get { return (string)GetValue (EventNameProperty); }
  set { SetValue (EventNameProperty, value); }
}
```

## <a name="consume-a-bindable-property"></a>Utiliser une propriété pouvant être liée

Une fois qu’une propriété peut être liée a été créée, il peut être consommé à partir de XAML ou de code. Dans XAML, cela est accompli en déclarant un espace de noms avec un préfixe, avec la déclaration d’espace de noms indiquant l’espace de noms CLR et éventuellement, un nom d’assembly. Pour plus d’informations, consultez [espaces de noms XAML](~/xamarin-forms/xaml/namespaces.md).

L’exemple de code suivant montre un espace de noms XAML pour un type personnalisé qui contient une propriété pouvant être liée, ce qui est définie dans le même assembly que le code d’application qui référence le type personnalisé :

```xaml
<ContentPage ... xmlns:local="clr-namespace:EventToCommandBehavior" ...>
  ...
</ContentPage>
```

La déclaration d’espace de noms est utilisée lors de la définition de la `EventName` propriété pouvant être liée, comme illustré dans l’exemple de code XAML suivant :

```xaml
<ListView ...>
  <ListView.Behaviors>
    <local:EventToCommandBehavior EventName="ItemSelected" ... />
  </ListView.Behaviors>
</ListView>
```

Le code C# équivalent est affiché dans l’exemple de code suivant :

```csharp
var listView = new ListView ();
listView.Behaviors.Add (new EventToCommandBehavior
{
  EventName = "ItemSelected",
  ...
});
```

## <a name="advanced-scenarios"></a>Scénarios avancés

Lors de la création d’une instance de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il existe un certain nombre de paramètres facultatifs qui peuvent être définis pour activer les scénarios avancés de propriétés pouvant être liées. Cette section explore ces scénarios.

### <a name="detect-property-changes"></a>Détecter les modifications de propriété

Une méthode de rappel de modification de propriété `static` peut être inscrite avec une propriété pouvant être liée en spécifiant le paramètre `propertyChanged` pour la méthode [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) . La méthode de rappel spécifiée sera être appelée lorsque la valeur de la propriété peut être liée change.

L’exemple de code suivant montre comment la propriété pouvant être liée `EventName` inscrit la méthode `OnEventNameChanged` en tant que méthode de rappel de modification de propriété :

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create (
    "EventName", typeof(string), typeof(EventToCommandBehavior), null, propertyChanged: OnEventNameChanged);
...

static void OnEventNameChanged (BindableObject bindable, object oldValue, object newValue)
{
  // Property changed implementation goes here
}
```

Dans la méthode de rappel de modification de propriété, le paramètre [`BindableObject`](xref:Xamarin.Forms.BindableObject) est utilisé pour désigner l’instance de la classe propriétaire qui a signalé une modification, et les valeurs des deux paramètres de `object` représentent les anciennes et nouvelles valeurs de la propriété pouvant être liée.

### <a name="validation-callbacks"></a>Rappels de validation

Une `static` méthode de rappel de validation peut être inscrite avec une propriété pouvant être liée en spécifiant le paramètre `validateValue` pour la méthode [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) . La méthode de rappel spécifiée sera être appelée lorsque la valeur de la propriété peut être liée.

L’exemple de code suivant montre comment la propriété pouvant être liée `Angle` inscrit la méthode `IsValidValue` en tant que méthode de rappel de validation :

```csharp
public static readonly BindableProperty AngleProperty =
  BindableProperty.Create ("Angle", typeof(double), typeof(HomePage), 0.0, validateValue: IsValidValue);
...

static bool IsValidValue (BindableObject view, object value)
{
  double result;
  bool isDouble = double.TryParse (value.ToString (), out result);
  return (result >= 0 && result <= 360);
}
```

Les rappels de validation sont fournis avec une valeur et doivent retourner `true` si la valeur est valide pour la propriété, sinon `false`. Une exception est levée si un rappel de validation retourne `false`, qui doit être gérée par le développeur. Une utilisation typique d’une méthode de rappel de validation est restriction des valeurs d’entiers ou valeurs doubles lorsque la valeur de la propriété peut être liée. Par exemple, la méthode `IsValidValue` vérifie que la valeur de la propriété est une `double` dans la plage comprise entre 0 et 360.

### <a name="coerce-value-callbacks"></a>Forcer les rappels de valeur

Une méthode de rappel de valeur de forçage de `static` peut être inscrite avec une propriété pouvant être liée en spécifiant le paramètre `coerceValue` pour la méthode [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) . La méthode de rappel spécifiée sera être appelée lorsque la valeur de la propriété peut être liée change.

> [!IMPORTANT]
> Le type de `BindableObject` a une méthode `CoerceValue` qui peut être appelée pour forcer une réévaluation de la valeur de son argument `BindableProperty`, en appelant son rappel de valeur forcée.

Forcer des rappels sont utilisés pour forcer une réévaluation d’une propriété pouvant être liée lorsque la valeur de la propriété change de valeur. Par exemple, un rappel de forçage de valeur peut être utilisé pour vous assurer que la valeur d’une propriété pouvant être liée n’est pas supérieure à la valeur d’une autre propriété pouvant être liée.

L’exemple de code suivant montre comment la propriété pouvant être liée `Angle` inscrit la méthode `CoerceAngle` en tant que méthode de rappel de valeur forcée :

```csharp
public static readonly BindableProperty AngleProperty = BindableProperty.Create (
  "Angle", typeof(double), typeof(HomePage), 0.0, coerceValue: CoerceAngle);
public static readonly BindableProperty MaximumAngleProperty = BindableProperty.Create (
  "MaximumAngle", typeof(double), typeof(HomePage), 360.0, propertyChanged: ForceCoerceValue);
...

static object CoerceAngle (BindableObject bindable, object value)
{
  var homePage = bindable as HomePage;
  double input = (double)value;

  if (input > homePage.MaximumAngle)
  {
    input = homePage.MaximumAngle;
  }
  return input;
}

static void ForceCoerceValue(BindableObject bindable, object oldValue, object newValue)
{
  bindable.CoerceValue(AngleProperty);
}
```

La méthode `CoerceAngle` vérifie la valeur de la propriété `MaximumAngle` et, si la valeur de la propriété `Angle` est supérieure, elle convertit la valeur en valeur de la propriété `MaximumAngle`. En outre, lorsque la propriété `MaximumAngle` change le rappel de la valeur forcée est appelé sur la propriété `Angle` en appelant la méthode `CoerceValue`.

### <a name="create-a-default-value-with-a-func"></a>Créer une valeur par défaut avec un Func

Un `Func` peut être utilisé pour initialiser la valeur par défaut d’une propriété pouvant être liée, comme illustré dans l’exemple de code suivant :

```csharp
public static readonly BindableProperty SizeProperty =
  BindableProperty.Create ("Size", typeof(double), typeof(HomePage), 0.0,
  defaultValueCreator: bindable => Device.GetNamedSize (NamedSize.Large, (Label)bindable));
```

Le paramètre `defaultValueCreator` a la valeur d’une `Func` qui appelle la méthode [`Device.GetNamedSize`](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type)) pour retourner une `double` qui représente la taille nommée de la police utilisée sur un [`Label`](xref:Xamarin.Forms.Label) sur la plateforme native.

## <a name="related-links"></a>Liens connexes

- [Espaces de noms XAML](~/xamarin-forms/xaml/namespaces.md)
- [Comportement de l’événement à la commande (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)
- [Rappel de validation (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-validationcallback)
- [Forcer le rappel de la valeur (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-coercevaluecallback)
- [API BindableProperty](xref:Xamarin.Forms.BindableProperty)
- [API BindableObject](xref:Xamarin.Forms.BindableObject)
