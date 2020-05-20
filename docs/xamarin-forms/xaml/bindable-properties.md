---
title: Propriétés pouvant être liées par Xamarin. Forms
description: Cet article fournit une introduction aux propriétés pouvant être liées et montre comment les créer et les utiliser.
ms.prod: xamarin
ms.assetid: 1EE869D8-6FE1-45CA-A0AD-26EC7D032AD7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
ms.openlocfilehash: 4151ac6f8cd9d860251ce1f27c7b342e0caa465c
ms.sourcegitcommit: bc0c1740aa0708459729c0e671ab3ff7de3e2eee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/15/2020
ms.locfileid: "83425775"
---
# <a name="xamarinforms-bindable-properties"></a>Propriétés pouvant être liées par Xamarin. Forms

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

Les propriétés pouvant être liées étendent la fonctionnalité de propriété CLR en sauvegardant une propriété avec un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) type, au lieu de sauvegarder une propriété avec un champ. L’objectif des propriétés pouvant être liées est de fournir un système de propriétés qui prend en charge la liaison de données, les styles, les modèles et les valeurs définis par le biais de relations parent-enfant. En outre, les propriétés pouvant être liées peuvent fournir des valeurs par défaut, la validation des valeurs de propriété et les rappels qui surveillent les modifications de propriété.

Les propriétés doivent être implémentées en tant que propriétés pouvant être liées afin de prendre en charge une ou plusieurs des fonctionnalités suivantes :

- Agissant comme une propriété *cible* valide pour la liaison de données.
- Définition de la propriété à l’aide d’un [style](~/xamarin-forms/user-interface/styles/index.md).
- En fournissant une valeur de propriété par défaut qui est différente de la valeur par défaut pour le type de la propriété.
- Validation de la valeur de la propriété.
- Analyse des modifications des propriétés.

Exemples de propriétés pouvant être liées par Xamarin. Forms : [`Label.Text`](xref:Xamarin.Forms.Label.Text) , [`Button.BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius) et [`StackLayout.Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) . Chaque propriété pouvant être liée possède un `public static readonly` champ de type correspondant [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) qui est exposé sur la même classe et qui est l’identificateur de la propriété pouvant être liée. Par exemple, l’identificateur de propriété pouvant être lié correspondant à la `Label.Text` propriété est [`Label.TextProperty`](xref:Xamarin.Forms.Label.TextProperty) .

## <a name="create-a-bindable-property"></a>Créer une propriété pouvant être liée

Le processus de création d’une propriété pouvant être liée est le suivant :

1. Créez une [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) instance de avec l’une des [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create*) surcharges de méthode.
1. Définissez les accesseurs de propriété pour l' [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) instance.

Toutes les [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) instances doivent être créées sur le thread d’interface utilisateur. Cela signifie que seul le code qui s’exécute sur le thread d’interface utilisateur peut obtenir ou définir la valeur d’une propriété pouvant être liée. Toutefois, `BindableProperty` les instances sont accessibles à partir d’autres threads en marshalant vers le thread d’interface utilisateur avec la [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) méthode.

### <a name="create-a-property"></a>Créer une propriété

Pour créer une `BindableProperty` instance, la classe conteneur doit dériver de la [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe. Toutefois, la `BindableObject` classe est élevée dans la hiérarchie de classes, donc la majorité des classes utilisées pour les fonctionnalités de l’interface utilisateur prennent en charge les propriétés pouvant être liées.

Une propriété pouvant être liée peut être créée en déclarant une `public static readonly` propriété de type [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) . La valeur retournée de l’une des surcharges de la méthode doit être affectée à la propriété pouvant être liée [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) . La déclaration doit se trouver dans le corps de la [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe dérivée, mais en dehors de toutes les définitions de membre.

Au minimum, un identificateur doit être spécifié lors de la création d’un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , avec les paramètres suivants :

- Nom du [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) .
- Type de la propriété.
- Type de l’objet propriétaire.
- Valeur par défaut de la propriété. Cela garantit que la propriété retourne toujours une valeur par défaut particulière lorsqu’elle est non définie et qu’elle peut être différente de la valeur par défaut pour le type de la propriété. La valeur par défaut est restaurée lorsque la [`ClearValue`](xref:Xamarin.Forms.BindableObject.ClearValue(Xamarin.Forms.BindableProperty)) méthode est appelée sur la propriété pouvant être liée.

Le code suivant illustre un exemple de propriété pouvant être liée, avec un identificateur et des valeurs pour les quatre paramètres requis :

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null);
```

Cela crée une [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) instance nommée `EventName` , de type `string` . La propriété appartient à la `EventToCommandBehavior` classe et a une valeur par défaut de `null` . La Convention d’affectation de noms pour les propriétés pouvant être liées est que l’identificateur de propriété pouvant être lié doit correspondre au nom de propriété spécifié dans la `Create` méthode, avec « propriété » ajoutée. Par conséquent, dans l’exemple ci-dessus, l’identificateur de propriété pouvant être lié est `EventNameProperty` .

Si vous le souhaitez, lors de la création d’une [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) instance, vous pouvez spécifier les paramètres suivants :

- Mode de liaison. Utilisé pour spécifier la direction dans laquelle les modifications de valeur de propriété sont propagées. Dans le mode de liaison par défaut, les modifications sont propagées de la *source* à la *cible*.
- Délégué de validation qui sera appelé lorsque la valeur de propriété est définie. Pour plus d’informations, consultez [rappels de validation](#validation-callbacks).
- Délégué de propriété modifié qui sera appelé lorsque la valeur de propriété a été modifiée. Pour plus d’informations, consultez [détecter les modifications de propriété](#detect-property-changes).
- Délégué de modification de propriété qui sera appelé lorsque la valeur de propriété sera modifiée. Ce délégué a la même signature que le délégué property changed.
- Délégué de valeur forcée qui sera appelé lorsque la valeur de propriété a été modifiée. Pour plus d’informations, consultez [forçage des rappels de valeur](#coerce-value-callbacks).
- `Func`Utilisé pour initialiser une valeur de propriété par défaut. Pour plus d’informations, consultez [créer une valeur par défaut avec un Func](#create-a-default-value-with-a-func).

### <a name="create-accessors"></a>Créer des accesseurs

Les accesseurs de propriété sont requis pour utiliser la syntaxe de propriété pour accéder à une propriété pouvant être liée. L' `Get` accesseur doit retourner la valeur contenue dans la propriété pouvant être liée correspondante. Pour ce faire, appelez la [`GetValue`](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)) méthode, en passant l’identificateur de propriété pouvant être lié sur lequel obtenir la valeur, puis effectuez un cast du résultat vers le type requis. L' `Set` accesseur doit définir la valeur de la propriété pouvant être liée correspondante. Pour cela, vous devez appeler la [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) méthode, en passant l’identificateur de propriété pouvant être lié sur lequel définir la valeur, et la valeur à définir.

L’exemple de code suivant montre des accesseurs pour la `EventName` propriété pouvant être liée :

```csharp
public string EventName
{
  get { return (string)GetValue (EventNameProperty); }
  set { SetValue (EventNameProperty, value); }
}
```

## <a name="consume-a-bindable-property"></a>Utiliser une propriété pouvant être liée

Une fois qu’une propriété pouvant être liée a été créée, elle peut être consommée à partir du code XAML ou. En XAML, cela est obtenu en déclarant un espace de noms avec un préfixe, avec la déclaration d’espace de noms qui indique le nom de l’espace de noms CLR et, éventuellement, un nom d’assembly. Pour plus d’informations, consultez [espaces de noms XAML](~/xamarin-forms/xaml/namespaces.md).

L’exemple de code suivant montre un espace de noms XAML pour un type personnalisé qui contient une propriété pouvant être liée, qui est définie dans le même assembly que le code d’application qui référence le type personnalisé :

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

Lors de la création d’une [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) instance, il existe un certain nombre de paramètres facultatifs qui peuvent être définis pour activer les scénarios de propriété avancés pouvant être liés. Cette section explore ces scénarios.

### <a name="detect-property-changes"></a>Détecter les modifications de propriété

Une `static` méthode de rappel de propriété modifiée peut être inscrite avec une propriété pouvant être liée en spécifiant le `propertyChanged` paramètre de la [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) méthode. La méthode de rappel spécifiée sera appelée lorsque la valeur de la propriété pouvant être liée sera modifiée.

L’exemple de code suivant montre comment la `EventName` propriété pouvant être liée enregistre la `OnEventNameChanged` méthode en tant que méthode de rappel de modification de propriété :

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

Dans la méthode de rappel de modification de propriété, le [`BindableObject`](xref:Xamarin.Forms.BindableObject) paramètre est utilisé pour désigner l’instance de la classe propriétaire qui a signalé une modification, et les valeurs des deux `object` paramètres représentent les anciennes et nouvelles valeurs de la propriété pouvant être liée.

### <a name="validation-callbacks"></a>Rappels de validation

Une `static` méthode de rappel de validation peut être inscrite avec une propriété pouvant être liée en spécifiant le `validateValue` paramètre de la [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) méthode. La méthode de rappel spécifiée sera appelée lorsque la valeur de la propriété pouvant être liée est définie.

L’exemple de code suivant montre comment la `Angle` propriété pouvant être liée enregistre la `IsValidValue` méthode comme une méthode de rappel de validation :

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

Les rappels de validation sont fournis avec une valeur et doivent retourner `true` si la valeur est valide pour la propriété ; sinon, `false` . Une exception est levée si un rappel de validation est retourné `false` , ce qui doit être géré par le développeur. Une utilisation classique d’une méthode de rappel de validation consiste à contraindre les valeurs des entiers ou des valeurs de type double lorsque la propriété pouvant être liée est définie. Par exemple, la `IsValidValue` méthode vérifie que la valeur de la propriété est `double` comprise entre 0 et 360.

### <a name="coerce-value-callbacks"></a>Forcer les rappels de valeur

Une `static` méthode de rappel de valeur forcée peut être inscrite avec une propriété pouvant être liée en spécifiant le `coerceValue` paramètre de la [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) méthode. La méthode de rappel spécifiée sera appelée lorsque la valeur de la propriété pouvant être liée sera modifiée.

> [!IMPORTANT]
> Le `BindableObject` type a une `CoerceValue` méthode qui peut être appelée pour forcer une réévaluation de la valeur de son `BindableProperty` argument, en appelant son rappel de valeur forcée.

Les rappels de valeur forcée sont utilisés pour forcer une réévaluation d’une propriété pouvant être liée lorsque la valeur de la propriété change. Par exemple, un rappel de valeur forcée peut être utilisé pour s’assurer que la valeur d’une propriété pouvant être liée n’est pas supérieure à la valeur d’une autre propriété pouvant être liée.

L’exemple de code suivant montre comment la `Angle` propriété pouvant être liée inscrit la `CoerceAngle` méthode en tant que méthode de rappel de valeur forcée :

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

La `CoerceAngle` méthode vérifie la valeur de la `MaximumAngle` propriété, et si la `Angle` valeur de propriété est supérieure à, elle convertit la valeur en `MaximumAngle` valeur de propriété. En outre, lorsque la `MaximumAngle` propriété change, le rappel de la valeur forcée est appelé sur la `Angle` propriété en appelant la `CoerceValue` méthode.

### <a name="create-a-default-value-with-a-func"></a>Créer une valeur par défaut avec un Func

Un `Func` peut être utilisé pour initialiser la valeur par défaut d’une propriété pouvant être liée, comme illustré dans l’exemple de code suivant :

```csharp
public static readonly BindableProperty SizeProperty =
  BindableProperty.Create ("Size", typeof(double), typeof(HomePage), 0.0,
  defaultValueCreator: bindable => Device.GetNamedSize (NamedSize.Large, (Label)bindable));
```

Le `defaultValueCreator` paramètre est défini sur un `Func` qui appelle la [`Device.GetNamedSize`](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type)) méthode pour retourner un `double` qui représente la taille nommée pour la police utilisée sur une [`Label`](xref:Xamarin.Forms.Label) sur la plateforme native.

## <a name="related-links"></a>Liens connexes

- [Espaces de noms XAML](~/xamarin-forms/xaml/namespaces.md)
- [Comportement de l’événement à la commande (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)
- [Rappel de validation (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-validationcallback)
- [Forcer le rappel de la valeur (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-coercevaluecallback)
- [API BindableProperty](xref:Xamarin.Forms.BindableProperty)
- [API BindableObject](xref:Xamarin.Forms.BindableObject)
