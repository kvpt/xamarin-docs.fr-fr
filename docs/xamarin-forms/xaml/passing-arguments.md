---
title: Passage d’arguments en XAML
description: Cet article montre comment utiliser les attributs XAML qui peuvent être utilisés pour passer des arguments à des constructeurs non définis par défaut, pour appeler des méthodes de fabrique et pour spécifier le type d’un argument générique.
ms.prod: xamarin
ms.assetid: 8F3B267F-499E-4D79-9193-FCA99F199519
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 84d8901b7f8dee8ffd6c3ba22d30c76b456555f0
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84571504"
---
# <a name="passing-arguments-in-xaml"></a>Passage d’arguments en XAML

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)

_Cet article montre comment utiliser les attributs XAML qui peuvent être utilisés pour passer des arguments à des constructeurs non définis par défaut, pour appeler des méthodes de fabrique et pour spécifier le type d’un argument générique._

## <a name="overview"></a>Vue d’ensemble

Il est souvent nécessaire d’instancier des objets avec des constructeurs qui requièrent des arguments, ou en appelant une méthode de création statique. Cela peut être effectué en XAML à l’aide `x:Arguments` des `x:FactoryMethod` attributs et :

- L' `x:Arguments` attribut est utilisé pour spécifier des arguments de constructeur pour un constructeur non défini par défaut, ou pour une déclaration d’objet de méthode de fabrique. Pour plus d’informations, consultez [passage des arguments de constructeur](#passing-constructor-arguments).
- L' `x:FactoryMethod` attribut est utilisé pour spécifier une méthode de fabrique qui peut être utilisée pour initialiser un objet. Pour plus d’informations, consultez [appel des méthodes de fabrique](#calling-factory-methods).

En outre, l' `x:TypeArguments` attribut peut être utilisé pour spécifier les arguments de type générique pour le constructeur d’un type générique. Pour plus d’informations, consultez [spécification d’un argument de type générique](#specifying-a-generic-type-argument).

## <a name="passing-constructor-arguments"></a>Passer des arguments de constructeur

Les arguments peuvent être passés à un constructeur non défini par défaut à l’aide de l' `x:Arguments` attribut. Chaque argument de constructeur doit être délimité dans un élément XML qui représente le type de l’argument. Xamarin.Formsprend en charge les éléments suivants pour les types de base :

- `x:Array`
- `x:Boolean`
- `x:Byte`
- `x:Char`
- `x:DateTime`
- `x:Decimal`
- `x:Double`
- `x:Int16`
- `x:Int32`
- `x:Int64`
- `x:Object`
- `x:Single`
- `x:String`
- `x:TimeSpan`

L’exemple de code suivant illustre l’utilisation de l' `x:Arguments` attribut avec trois [`Color`](xref:Xamarin.Forms.Color) constructeurs :

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

Le nombre d’éléments dans la `x:Arguments` balise, ainsi que les types de ces éléments, doivent correspondre à l’un des [`Color`](xref:Xamarin.Forms.Color) constructeurs. Le `Color` [constructeur](xref:Xamarin.Forms.Color.%23ctor(System.Double)) avec un seul paramètre requiert une valeur de nuances de gris comprise entre 0 (noir) et 1 (blanc). Le `Color` [constructeur](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double)) avec trois paramètres requiert une valeur rouge, verte et bleue comprise entre 0 et 1. Le `Color` [constructeur](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double)) avec quatre paramètres ajoute un canal alpha en tant que quatrième paramètre.

Les captures d’écran suivantes montrent le résultat de l’appel de chaque [`Color`](xref:Xamarin.Forms.Color) constructeur avec les valeurs d’argument spécifiées :

![BoxView. Color spécifié avec x :Arguments](passing-arguments-images/passing-arguments.png)

## <a name="calling-factory-methods"></a>Appel des méthodes de fabrique

Les méthodes de fabrique peuvent être appelées en XAML en spécifiant le nom de la méthode à l’aide de l' `x:FactoryMethod` attribut et de ses arguments à l’aide de l' `x:Arguments` attribut. Une méthode de fabrique est une `public static` méthode qui retourne des objets ou des valeurs du même type que la classe ou la structure qui définit les méthodes.

La [`Color`](xref:Xamarin.Forms.Color) structure définit un certain nombre de méthodes de fabrique, et l’exemple de code suivant illustre l’appel de trois d’entre elles :

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

Le nombre d’éléments dans la `x:Arguments` balise, ainsi que les types de ces éléments, doivent correspondre aux arguments de la méthode de fabrique appelée. La [`FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) méthode de fabrique requiert quatre [`Int32`](https://docs.microsoft.com/dotnet/api/system.int32) paramètres, qui représentent les valeurs rouge, vert, bleu et alpha, entre 0 et 255 respectivement. La [`FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) méthode de fabrique requiert quatre [`Double`](https://docs.microsoft.com/dotnet/api/system.double) paramètres, qui représentent les valeurs de teinte, de saturation, de luminosité et alpha, allant de 0 à 1, respectivement. La [`FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String)) méthode de fabrique requiert un [`String`](https://docs.microsoft.com/dotnet/api/system.string) qui représente la couleur RVB hexadécimale (a).

Les captures d’écran suivantes montrent le résultat de l’appel de chaque [`Color`](xref:Xamarin.Forms.Color) méthode de fabrique avec les valeurs d’argument spécifiées :

![BoxView. Color spécifié avec x :FactoryMethod et x :Arguments](passing-arguments-images/factory-methods.png)

## <a name="specifying-a-generic-type-argument"></a>Spécification d’un argument de type générique

Les arguments de type générique pour le constructeur d’un type générique peuvent être spécifiés à l’aide de l' `x:TypeArguments` attribut, comme illustré dans l’exemple de code suivant :

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

La [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) classe est une classe générique et doit être instanciée avec un `x:TypeArguments` attribut qui correspond au type cible. Dans la [`On`](xref:Xamarin.Forms.On) classe, l' [`Platform`](xref:Xamarin.Forms.On.Platform) attribut peut accepter une `string` valeur unique ou plusieurs valeurs délimitées par des virgules `string` . Dans cet exemple, la [`StackLayout.Margin`](xref:Xamarin.Forms.View.Margin) propriété est définie sur un spécifique à la plateforme [`Thickness`](xref:Xamarin.Forms.Thickness) .

Pour plus d’informations sur les arguments de type générique, consultez [génériques en Xamarin.Forms XAML](generics.md).

## <a name="related-links"></a>Liens connexes

- [Passer des arguments de constructeur (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)
- [Appel des méthodes de fabrique (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-callingfactorymethods)
- [Espaces de noms XAML](~/xamarin-forms/xaml/namespaces.md)
- [Génériques en Xamarin.Forms XAML](generics.md)
