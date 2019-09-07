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
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70767594"
---
# <a name="passing-arguments-in-xaml"></a>Passage des Arguments dans XAML

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)

_Cet article montre comment utiliser des attributs XAML qui peuvent être utilisées pour passer des arguments aux constructeurs non définis par défaut, pour appeler des méthodes de fabrique et pour spécifier le type d’un argument générique._

## <a name="overview"></a>Vue d'ensemble

Il est souvent nécessaire instancier des objets avec des constructeurs qui nécessitent des arguments, ou en appelant une méthode statique de la création. Vous pouvez le faire dans XAML à l’aide de la `x:Arguments` et `x:FactoryMethod` attributs :

- Le `x:Arguments` attribut est utilisé pour spécifier des arguments de constructeur pour un constructeur non défini par défaut, ou pour une déclaration d’objet de fabrique (méthode). Pour plus d’informations, consultez [en passant les Arguments de constructeur](#constructor_arguments).
- Le `x:FactoryMethod` attribut est utilisé pour spécifier une méthode de fabrique qui peut être utilisée pour initialiser un objet. Pour plus d’informations, consultez [appelant des méthodes de fabrique](#factory_methods).

En outre, le `x:TypeArguments` attribut peut être utilisé pour spécifier les arguments de type générique au constructeur d’un type générique. Pour plus d’informations, consultez [spécifiant un Argument de Type générique](#generic_type_arguments).

<a name="constructor_arguments" />

## <a name="passing-constructor-arguments"></a>Passage des Arguments de constructeur

Arguments peuvent être passés à un constructeur personnalisé en utilisant le `x:Arguments` attribut. Chaque argument de constructeur doit être délimité au sein d’un élément XML qui représente le type de l’argument. Xamarin.Forms prend en charge les éléments suivants pour les types de base :

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

L’exemple de code suivant montre comment utiliser le `x:Arguments` attribut avec trois [ `Color` ](xref:Xamarin.Forms.Color) constructeurs :

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

Le nombre d’éléments dans le `x:Arguments` balise et les types de ces éléments, doivent correspondre à l’un de le [ `Color` ](xref:Xamarin.Forms.Color) constructeurs. Le `Color` [constructeur](xref:Xamarin.Forms.Color.%23ctor(System.Double)) avec un seul paramètre requiert une valeur de nuances de gris de 0 (noir) à 1 (blanc). Le `Color` [constructeur](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double)) avec trois paramètres requiert une valeur de rouge, vert et bleue comprises entre 0 et 1. Le `Color` [constructeur](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double)) avec quatre paramètres ajoute un canal alpha en tant que quatrième paramètre.

Les captures d’écran suivantes affichent le résultat de l’appel de chaque [ `Color` ](xref:Xamarin.Forms.Color) constructeur avec les valeurs d’argument spécifié :

![BoxView. Color spécifié avec x :Arguments](passing-arguments-images/passing-arguments.png)

<a name="factory_methods" />

## <a name="calling-factory-methods"></a>Appel des méthodes de fabrique

Méthodes de fabrique peuvent être appelées dans XAML en spécifiant la méthode nom en utilisant le `x:FactoryMethod` attribut et ses arguments à l’aide de la `x:Arguments` attribut. Une méthode de fabrique est une `public static` méthode qui retourne des objets ou des valeurs du même type que la classe ou structure qui définit les méthodes.

Le [ `Color` ](xref:Xamarin.Forms.Color) structure définit un nombre de méthodes de fabrique et l’exemple de code suivant illustre l’appel trois d'entre eux :

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

Le nombre d’éléments dans le `x:Arguments` balise et les types de ces éléments, doivent correspondre à des arguments de la méthode de fabrique appelée. Le [ `FromRgba` ](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) méthode de fabrique requiert quatre [ `Int32` ](https://docs.microsoft.com/dotnet/api/system.int32) paramètres, qui représentent les valeurs rouges, verts, bleu et alphabétiques, compris entre 0 et 255. Le [ `FromHsla` ](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) méthode de fabrique requiert quatre [ `Double` ](https://docs.microsoft.com/dotnet/api/system.double) paramètres, qui représentent la teinte, saturation, luminosité et les valeurs alpha, compris entre 0 et 1, respectivement. Le [ `FromHex` ](xref:Xamarin.Forms.Color.FromHex(System.String)) requiert de méthode de fabrique un [ `String` ](https://docs.microsoft.com/dotnet/api/system.string) qui représente le format hexadécimal (A) couleur RVB.

Les captures d’écran suivantes affichent le résultat de l’appel de chaque [ `Color` ](xref:Xamarin.Forms.Color) méthode de fabrique avec les valeurs d’argument spécifié :

![BoxView. Color spécifié avec x :FactoryMethod et x :Arguments](passing-arguments-images/factory-methods.png)

<a name="generic_type_arguments" />

## <a name="specifying-a-generic-type-argument"></a>En spécifiant un Argument de Type générique

Arguments de type générique pour le constructeur d’un type générique peuvent être spécifiés à l’aide de la `x:TypeArguments` d’attribut, comme illustré dans l’exemple de code suivant :

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

Le [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) classe est une classe générique et doit être instancié avec une `x:TypeArguments` attribut qui correspond au type de cible. Dans le [ `On` ](xref:Xamarin.Forms.On) (classe), le [ `Platform` ](xref:Xamarin.Forms.On.Platform) attribut peut accepter un seul `string` valeur ou plusieurs virgules `string` valeurs. Dans cet exemple, le [ `StackLayout.Margin` ](xref:Xamarin.Forms.View.Margin) propriété a la valeur spécifique à la plateforme [ `Thickness` ](xref:Xamarin.Forms.Thickness).

## <a name="summary"></a>Récapitulatif

Cet article a montré en utilisant les attributs XAML qui peuvent être utilisées pour passer des arguments aux constructeurs non définis par défaut, pour appeler des méthodes de fabrique et pour spécifier le type d’un argument générique.

## <a name="related-links"></a>Liens associés

- [Espaces de noms XAML](~/xamarin-forms/xaml/namespaces.md)
- [Passage des Arguments de constructeur (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)
- [Appel des méthodes de fabrique (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-callingfactorymethods)
