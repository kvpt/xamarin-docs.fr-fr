---
title: Passage des Arguments dans XAML
description: Cet article montre comment utiliser des attributs XAML qui peuvent être utilisées pour passer des arguments aux constructeurs non définis par défaut, pour appeler des méthodes de fabrique et pour spécifier le type d’un argument générique.
ms.prod: xamarin
ms.assetid: 8F3B267F-499E-4D79-9193-FCA99F199519
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2016
ms.openlocfilehash: 80f332e45d6c46ad49543923e85cbb2eceadb378
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78911276"
---
# <a name="passing-arguments-in-xaml"></a>Passage des Arguments dans XAML

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)

_Cet article montre comment utiliser les attributs XAML qui peuvent être utilisés pour passer des arguments à des constructeurs non définis par défaut, pour appeler des méthodes de fabrique et pour spécifier le type d’un argument générique._

## <a name="overview"></a>Overview

Il est souvent nécessaire instancier des objets avec des constructeurs qui nécessitent des arguments, ou en appelant une méthode statique de la création. Cela peut être accompli en XAML à l’aide des attributs `x:Arguments` et `x:FactoryMethod` :

- L’attribut `x:Arguments` est utilisé pour spécifier des arguments de constructeur pour un constructeur non défini par défaut, ou pour une déclaration d’objet de méthode de fabrique. Pour plus d’informations, consultez [passage des arguments de constructeur](#constructor_arguments).
- L’attribut `x:FactoryMethod` est utilisé pour spécifier une méthode de fabrique qui peut être utilisée pour initialiser un objet. Pour plus d’informations, consultez [appel des méthodes de fabrique](#factory_methods).

En outre, l’attribut `x:TypeArguments` peut être utilisé pour spécifier les arguments de type générique pour le constructeur d’un type générique. Pour plus d’informations, consultez [spécification d’un argument de type générique](#generic_type_arguments).

<a name="constructor_arguments" />

## <a name="passing-constructor-arguments"></a>Passage des Arguments de constructeur

Les arguments peuvent être passés à un constructeur non défini par défaut à l’aide de l’attribut `x:Arguments`. Chaque argument de constructeur doit être délimité au sein d’un élément XML qui représente le type de l’argument. Xamarin.Forms prend en charge les éléments suivants pour les types de base :

- `x:Object`
- `x:Boolean`
- `x:Byte`
- `x:Int16`
- `x:Int32`
- `x:Int64`
- `x:Single`
- `x:Double`
- `x:Decimal`
- `x:Char`
- `x:String`
- `x:TimeSpan`
- `x:Array`
- `x:DateTime`

L’exemple de code suivant illustre l’utilisation de l’attribut `x:Arguments` avec trois constructeurs [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.9</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.25</x:Double>
        <x:Double>0.5</x:Double>
        <x:Double>0.75</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.8</x:Double>
        <x:Double>0.5</x:Double>
        <x:Double>0.2</x:Double>
        <x:Double>0.5</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
```

Le nombre d’éléments dans la balise `x:Arguments`, ainsi que les types de ces éléments, doivent correspondre à l’un des constructeurs [`Color`](xref:Xamarin.Forms.Color) . Le [constructeur](xref:Xamarin.Forms.Color.%23ctor(System.Double)) `Color` avec un seul paramètre requiert une valeur de nuances de gris comprise entre 0 (noir) et 1 (blanc). Le [constructeur](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double)) `Color` avec trois paramètres requiert une valeur rouge, verte et bleue comprise entre 0 et 1. Le [constructeur](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double)) `Color` avec quatre paramètres ajoute un canal alpha en tant que quatrième paramètre.

Les captures d’écran suivantes montrent le résultat de l’appel de chaque constructeur [`Color`](xref:Xamarin.Forms.Color) avec les valeurs d’argument spécifiées :

![BoxView. Color spécifié avec x :Arguments](passing-arguments-images/passing-arguments.png)

<a name="factory_methods" />

## <a name="calling-factory-methods"></a>Appel des méthodes de fabrique

Les méthodes de fabrique peuvent être appelées en XAML en spécifiant le nom de la méthode à l’aide de l’attribut `x:FactoryMethod` et de ses arguments à l’aide de l’attribut `x:Arguments`. Une méthode de fabrique est une méthode `public static` qui retourne des objets ou des valeurs du même type que la classe ou la structure qui définit les méthodes.

La structure [`Color`](xref:Xamarin.Forms.Color) définit un certain nombre de méthodes de fabrique, et l’exemple de code suivant montre comment appeler trois d’entre elles :

```xaml
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromRgba">
      <x:Arguments>
        <x:Int32>192</x:Int32>
        <x:Int32>75</x:Int32>
        <x:Int32>150</x:Int32>                        
        <x:Int32>128</x:Int32>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromHsla">
      <x:Arguments>
        <x:Double>0.23</x:Double>
        <x:Double>0.42</x:Double>
        <x:Double>0.69</x:Double>
        <x:Double>0.7</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromHex">
      <x:Arguments>
        <x:String>#FF048B9A</x:String>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
```

Le nombre d’éléments dans la balise `x:Arguments`, ainsi que les types de ces éléments, doivent correspondre aux arguments de la méthode de fabrique appelée. La méthode de fabrique [`FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) nécessite quatre [`Int32`](https://docs.microsoft.com/dotnet/api/system.int32) paramètres, qui représentent respectivement les valeurs rouge, vert, bleu et alpha, entre 0 et 255. La méthode de fabrique [`FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) nécessite quatre [`Double`](https://docs.microsoft.com/dotnet/api/system.double) paramètres, qui représentent les valeurs de teinte, de saturation, de luminosité et alpha, allant de 0 à 1, respectivement. La méthode de fabrique [`FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String)) requiert un [`String`](https://docs.microsoft.com/dotnet/api/system.string) qui représente la couleur RVB hexadécimale (a).

Les captures d’écran suivantes montrent le résultat de l’appel de chaque méthode de fabrique [`Color`](xref:Xamarin.Forms.Color) avec les valeurs d’argument spécifiées :

![BoxView. Color spécifié avec x :FactoryMethod et x :Arguments](passing-arguments-images/factory-methods.png)

<a name="generic_type_arguments" />

## <a name="specifying-a-generic-type-argument"></a>En spécifiant un Argument de Type générique

Les arguments de type générique pour le constructeur d’un type générique peuvent être spécifiés à l’aide de l’attribut `x:TypeArguments`, comme illustré dans l’exemple de code suivant :

```xaml
<ContentPage ...>
  <StackLayout>
    <StackLayout.Margin>
      <OnPlatform x:TypeArguments="Thickness">
        <On Platform="iOS" Value="0,20,0,0" />
        <On Platform="Android" Value="5, 10" />
        <On Platform="UWP" Value="10" />
      </OnPlatform>
    </StackLayout.Margin>
  </StackLayout>
</ContentPage>
```

La classe [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) est une classe générique et doit être instanciée avec un attribut `x:TypeArguments` qui correspond au type cible. Dans la classe [`On`](xref:Xamarin.Forms.On) , l’attribut [`Platform`](xref:Xamarin.Forms.On.Platform) peut accepter une valeur de `string` unique ou plusieurs valeurs `string` délimitées par des virgules. Dans cet exemple, la propriété [`StackLayout.Margin`](xref:Xamarin.Forms.View.Margin) est définie sur une [`Thickness`](xref:Xamarin.Forms.Thickness)spécifique à la plateforme.

## <a name="summary"></a>Résumé

Cet article a montré en utilisant les attributs XAML qui peuvent être utilisées pour passer des arguments aux constructeurs non définis par défaut, pour appeler des méthodes de fabrique et pour spécifier le type d’un argument générique.

## <a name="related-links"></a>Liens connexes

- [Espaces de noms XAML](~/xamarin-forms/xaml/namespaces.md)
- [Passer des arguments de constructeur (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)
- [Appel des méthodes de fabrique (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-callingfactorymethods)
