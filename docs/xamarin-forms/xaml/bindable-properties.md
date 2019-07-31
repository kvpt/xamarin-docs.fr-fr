---
title: Propriétés pouvant être liées
description: Cet article fournit une introduction aux propriétés pouvant être liées et montre comment créer et de les consommer.
ms.prod: xamarin
ms.assetid: 1EE869D8-6FE1-45CA-A0AD-26EC7D032AD7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/02/2016
ms.openlocfilehash: 59ec82c222208ff0e73b0b7b5226d416d3a53398
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656599"
---
# <a name="bindable-properties"></a>Propriétés pouvant être liées

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

_Dans Xamarin.Forms, les fonctionnalités des common language runtime (CLR) sont étendue par les propriétés pouvant être liées. Une propriété est un type spécial de propriété, où la valeur de propriété est suivie par le système de propriétés de Xamarin.Forms. Cet article fournit une introduction aux propriétés pouvant être liées et montre comment créer et de les consommer._

## <a name="overview"></a>Vue d'ensemble

Propriétés pouvant être liées étendent les fonctionnalités de propriété CLR grâce à la sauvegarde d’une propriété avec une [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) type, au lieu d’une propriété avec un champ de stockage. L’objectif de propriétés pouvant être liées est de fournir un système de propriétés qui prend en charge la liaison de données, les styles, modèles, et les valeurs définies via des relations parent-enfant. En outre, les propriétés pouvant être liées peuvent fournir des valeurs par défaut, validation des valeurs de propriété et les rappels qui surveillent les modifications apportées aux propriétés.

Propriétés doivent être implémentées en tant que propriétés pouvant être liées pour prendre en charge un ou plusieurs des fonctionnalités suivantes :

- Agissant comme un valide *cible* propriété pour la liaison de données.
- Définition de la propriété via un [style](~/xamarin-forms/user-interface/styles/index.md).
- En fournissant une valeur de propriété par défaut qui est différente de la valeur par défaut pour le type de la propriété.
- Validation de la valeur de la propriété.
- Surveillance des modifications de propriété.

Exemples de propriétés pouvant être liées Xamarin.Forms [ `Label.Text` ](xref:Xamarin.Forms.Label.Text), [ `Button.BorderRadius` ](xref:Xamarin.Forms.Button.BorderRadius), et [ `StackLayout.Orientation` ](xref:Xamarin.Forms.StackLayout.Orientation). Chaque propriété pouvant être liée correspond un `public static readonly` propriété de type [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) qui est exposé sur la même classe et qui est l’identificateur de la propriété peut être liée. Par exemple, l’identificateur de propriété pouvant être liée correspondant pour le `Label.Text` propriété est [ `Label.TextProperty` ](xref:Xamarin.Forms.Label.TextProperty).

<a name="consuming-bindable-property" />

## <a name="creating-and-consuming-a-bindable-property"></a>Création et utilisation d’une propriété pouvant être liée

Le processus de création d’une propriété pouvant être liée est comme suit :

1. Créer un [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instance avec l’un de le [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create*) surcharges de méthode.
1. Définir des accesseurs de propriété pour le [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instance.

Notez que tous les [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instances doivent être créées sur le thread d’interface utilisateur. Cela signifie que seul le code qui s’exécute sur le thread d’interface utilisateur peut obtenir ou définir la valeur d’une propriété pouvant être liée. Toutefois, `BindableProperty` instances accessibles à partir d’autres threads du marshaling sur le thread d’interface utilisateur avec le [ `Device.BeginInvokeOnMainThread` ](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) (méthode).

### <a name="creating-a-property"></a>Création d’une propriété

Pour créer un `BindableProperty` instance, la classe conteneur doit dériver de la [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) classe. Toutefois, la `BindableObject` classe est élevée dans la hiérarchie de classes, donc la majorité des classes utilisées pour les propriétés pouvant être liées prise en charge des fonctionnalités d’interface utilisateur.

Une propriété pouvant être liée peut être créée en déclarant un `public static readonly` propriété de type [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty). La propriété peut être liée doit être définie sur la valeur retournée d’un de la [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) surcharges de méthode. La déclaration doit être dans le corps de [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) classe dérivée, mais en dehors de toutes les définitions de membre.

Au minimum, un identificateur doit être spécifié lorsque vous créez un [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty), ainsi que les paramètres suivants :

- Le nom de la [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty).
- Type de la propriété.
- Le type de l’objet propriétaire.
- Valeur par défaut de la propriété. Cela garantit que la propriété retourne toujours une valeur par défaut particulier lorsqu’il n’est pas définie, et il peut être différent de la valeur par défaut pour le type de la propriété. La valeur par défaut seront restaurées lorsque la [ `ClearValue` ](xref:Xamarin.Forms.BindableObject.ClearValue(Xamarin.Forms.BindableProperty)) méthode est appelée sur la propriété peut être liée.

Le code suivant montre un exemple d’une propriété pouvant être liée, avec un identificateur et les valeurs pour les quatre paramètres obligatoires :

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null);
```

Cette opération crée un [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instance nommée `EventName`, de type `string`. La propriété est possédée par le `EventToCommandBehavior` classe, et a la valeur par défaut `null`. La convention d’affectation de noms pour les propriétés pouvant être liées est que l’identificateur de propriété pouvant être liée doit correspondre au nom de propriété spécifié dans le `Create` méthode, avec « Property » est ajoutée. Par conséquent, dans l’exemple ci-dessus, l’identificateur de propriété pouvant être liée est `EventNameProperty`.

Si vous le souhaitez, lorsque vous créez un [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) de l’instance, ce qui suit paramètres peuvent être spécifiés :

- Mode de liaison. Cela est utilisé pour spécifier la direction dans laquelle seront propager les modifications de valeur de propriété. Dans le mode de liaison par défaut, les modifications seront propagées à partir de la *source* à la *cible*.
- Un délégué de validation qui sera appelé lorsque la valeur de propriété est définie. Pour plus d’informations, consultez [les rappels de Validation](#validation).
- Un délégué de modification de propriété qui sera appelé lorsque la valeur de propriété a changé. Pour plus d’informations, consultez [détecter les modifications de propriété](#propertychanges).
- Une propriété de modification de délégué qui sera appelé lorsque la valeur de propriété est modifiée. Ce délégué a la même signature que le délégué de modification de propriété.
- Un délégué de valeur forcée qui sera appelé lorsque la valeur de propriété a changé. Pour plus d’informations, consultez [forcer des rappels de valeur](#coerce).
- Un `Func` qui est utilisé pour initialiser une valeur de propriété par défaut. Pour plus d’informations, consultez [création d’une valeur par défaut avec une variable Func](#defaultfunc).

### <a name="creating-accessors"></a>Création d’accesseurs

Les accesseurs de propriété sont requis pour utiliser la syntaxe de la propriété pour accéder à une propriété pouvant être liée. Le `Get` accesseur doit retourner la valeur qui est contenue dans la propriété correspondante peut être liée. Cela peut être obtenue en appelant le [ `GetValue` ](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)) (méthode), en passant l’identificateur de propriété pouvant être liée sur laquelle obtenir la valeur, puis en convertissant le résultat au type requis. Le `Set` accesseur doit définir la valeur de la propriété correspondante peut être liée. Cela peut être obtenue en appelant le [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) méthode, en passant l’identificateur de propriété pouvant être liée sur lequel définir la valeur et la valeur à définir.

L’exemple de code suivant montre des accesseurs pour le `EventName` propriété à lier :

```csharp
public string EventName {
  get { return (string)GetValue (EventNameProperty); }
  set { SetValue (EventNameProperty, value); }
}
```

### <a name="consuming-a-bindable-property"></a>Utilisation d’une propriété pouvant être liée

Une fois qu’une propriété peut être liée a été créée, il peut être consommé à partir de XAML ou de code. Dans XAML, cela est accompli en déclarant un espace de noms avec un préfixe, avec la déclaration d’espace de noms indiquant l’espace de noms CLR et éventuellement, un nom d’assembly. Pour plus d’informations, consultez [espaces de noms XAML](~/xamarin-forms/xaml/namespaces.md).

L’exemple de code suivant montre un espace de noms XAML pour un type personnalisé qui contient une propriété pouvant être liée, ce qui est définie dans le même assembly que le code d’application qui référence le type personnalisé :

```xaml
<ContentPage ... xmlns:local="clr-namespace:EventToCommandBehavior" ...>
  ...
</ContentPage>
```

La déclaration d’espace de noms est utilisée lors de la définition du `EventName` propriété pouvant être liée, comme illustré dans l’exemple de code XAML suivant :

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
listView.Behaviors.Add (new EventToCommandBehavior {
  EventName = "ItemSelected",
  ...
});
```

<a name="advanced" />

## <a name="advanced-scenarios"></a>Scénarios avancés

Lorsque vous créez un [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) d’une instance, il existe un nombre de paramètres facultatifs qui peuvent être définies pour permettre les scénarios avancés de propriété pouvant être liée. Cette section explore ces scénarios.

<a name="propertychanges" />

### <a name="detecting-property-changes"></a>Détection des modifications de propriété

Un `static` méthode de rappel de modification de propriété peut être enregistrée avec une propriété pouvant être liée en spécifiant le `propertyChanged` paramètre pour le [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) (méthode). La méthode de rappel spécifiée sera être appelée lorsque la valeur de la propriété peut être liée change.

Le code suivant montre l’exemple comment la `EventName` registres de propriété à lier le `OnEventNameChanged` méthode comme méthode de rappel de modification de propriété :

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

Dans la méthode de rappel à propriétés modifiées, le [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) paramètre est utilisé pour indiquer quelle instance de la classe propriétaire a signalé une modification et les valeurs des deux `object` paramètres représentent les valeurs anciennes et nouvelles de la propriété peut être liée.

<a name="validation" />

### <a name="validation-callbacks"></a>Rappels de validation

Un `static` méthode de rappel de validation peut être enregistrée avec une propriété pouvant être liée en spécifiant le `validateValue` paramètre pour le [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) (méthode). La méthode de rappel spécifiée sera être appelée lorsque la valeur de la propriété peut être liée.

Le code suivant montre l’exemple comment la `Angle` registres de propriété à lier le `IsValidValue` méthode comme méthode de rappel de validation :

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

Les rappels de validation sont fournis avec une valeur et doit retourner `true` si la valeur est valide pour la propriété, `false`. Une exception sera déclenchée si un rappel de validation retourne `false`, qui doit être géré par le développeur. Une utilisation typique d’une méthode de rappel de validation est restriction des valeurs d’entiers ou valeurs doubles lorsque la valeur de la propriété peut être liée. Par exemple, le `IsValidValue` méthode vérifie que la valeur de propriété est un `double` dans la plage 0 à 360.

<a name="coerce" />

### <a name="coerce-value-callbacks"></a>Forcer des rappels de valeur

Un `static` forçage de valeurs méthode de rappel peut être inscrit avec une propriété pouvant être liée en spécifiant le `coerceValue` paramètre pour le [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) (méthode). La méthode de rappel spécifiée sera être appelée lorsque la valeur de la propriété peut être liée change.

Forcer des rappels sont utilisés pour forcer une réévaluation d’une propriété pouvant être liée lorsque la valeur de la propriété change de valeur. Par exemple, un rappel de forçage de valeur peut être utilisé pour vous assurer que la valeur d’une propriété pouvant être liée n’est pas supérieure à la valeur d’une autre propriété pouvant être liée.

Le code suivant montre l’exemple comment la `Angle` registres de propriété à lier le `CoerceAngle` méthode comme méthode de rappel de valeur forcée :

```csharp
public static readonly BindableProperty AngleProperty = BindableProperty.Create (
  "Angle", typeof(double), typeof(HomePage), 0.0, coerceValue: CoerceAngle);
public static readonly BindableProperty MaximumAngleProperty = BindableProperty.Create (
  "MaximumAngle", typeof(double), typeof(HomePage), 360.0);
...

static object CoerceAngle (BindableObject bindable, object value)
{
  var homePage = bindable as HomePage;
  double input = (double)value;

  if (input > homePage.MaximumAngle) {
    input = homePage.MaximumAngle;
  }

  return input;
}
```

Le `CoerceAngle` méthode vérifie la valeur de la `MaximumAngle` propriété et si le `Angle` valeur de propriété est supérieure à elle, il force la valeur dans la `MaximumAngle` valeur de propriété.

<a name="defaultfunc" />

### <a name="creating-a-default-value-with-a-func"></a>Création d’une valeur par défaut avec une variable Func

Un `Func` peut être utilisée pour initialiser la valeur par défaut d’une propriété pouvant être liée, comme illustré dans l’exemple de code suivant :

```csharp
public static readonly BindableProperty SizeProperty =
  BindableProperty.Create ("Size", typeof(double), typeof(HomePage), 0.0,
  defaultValueCreator: bindable => Device.GetNamedSize (NamedSize.Large, (Label)bindable));
```

Le `defaultValueCreator` paramètre est défini sur un `Func` qui appelle le [ `Device.GetNamedSize` ](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type)) méthode pour retourner un `double` qui représente la taille nommée pour la police utilisée sur un [ `Label` ](xref:Xamarin.Forms.Label) sur la plateforme native.

## <a name="summary"></a>Récapitulatif

Cet article propose une introduction à propriétés pouvant être liées et vous a montré comment créer et de les consommer. Une propriété est un type spécial de propriété, où la valeur de propriété est suivie par le système de propriétés de Xamarin.Forms.


## <a name="related-links"></a>Liens associés

- [Espaces de noms XAML](~/xamarin-forms/xaml/namespaces.md)
- [Événement au comportement de la commande (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)
- [Rappel de validation (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-validationcallback)
- [Forcer le rappel de valeur (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-coercevaluecallback)
- [BindableProperty](xref:Xamarin.Forms.BindableProperty)
- [BindableObject](xref:Xamarin.Forms.BindableObject)
