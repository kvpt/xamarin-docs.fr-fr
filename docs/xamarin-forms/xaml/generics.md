---
title: Génériques dans Xamarin. Forms XAML
description: Xamarin. Forms XAML assure la prise en charge de l’utilisation des types CLR génériques en spécifiant les contraintes génériques en tant qu’arguments de type.
ms.prod: xamarin
ms.assetid: 97B73048-4F90-41AD-AB48-8EB804C4998B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/28/2020
ms.openlocfilehash: 9cda08a3bab0e25db2315c9795721e25d47d2429
ms.sourcegitcommit: 154a3e7aec775327565bb54eda1a610976af1d6f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82624707"
---
# <a name="generics-in-xamarinforms-xaml"></a>Génériques dans Xamarin. Forms XAML

[![Télécharger l'](~/media/shared/download.png) exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-generics/)

Xamarin. Forms XAML assure la prise en charge de l’utilisation des types CLR génériques en spécifiant les contraintes génériques en tant qu’arguments de type. Cette prise en charge est fournie `x:TypeArguments` par la directive, qui passe les arguments de type de restriction d’un générique au constructeur du type générique.

> [!IMPORTANT]
> La définition de classes génériques dans Xamarin. Forms XAML `x:TypeArguments` , avec la directive, n’est pas prise en charge.

Les arguments de type sont spécifiés sous la forme d’une chaîne et sont en `sys:String` général `sys:Int32`préfixés, tels que et. Le préfixe est requis car les types typiques de contraintes génériques du CLR proviennent de bibliothèques qui ne sont pas mappées à l’espace de noms Xamarin. Forms par défaut. Toutefois, les types intégrés XAML 2009, tels que `x:String` et `x:Int32`, peuvent également être spécifiés en tant qu’arguments de `x` type, où est l’espace de noms du langage XAML pour XAML 2009. Pour plus d’informations sur les types intégrés XAML 2009, consultez [primitives de langage xaml 2009](/dotnet/desktop-wpf/xaml-services/types-for-primitives#xaml-2009-language-primitives).

Plusieurs arguments de type peuvent être spécifiés à l’aide d’un délimiteur de virgule. En outre, si une contrainte générique utilise des types génériques, les arguments de type de contrainte imbriquée doivent être contenus entre parenthèses.

> [!NOTE]
> L' `x:Type` extension de balisage fournit une référence de type CLR pour un type générique et a une fonction similaire `typeof` à l’opérateur en C#. Pour plus d’informations, consultez [x :type, extension de balisage](~/xamarin-forms/xaml/markup-extensions/consuming.md#type).

## <a name="single-primitive-type-argument"></a>Argument de type primitif unique

Un argument de type primitif unique peut être spécifié en tant qu’argument de chaîne préfixé `x:TypeArguments` à l’aide de la directive :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:scg="clr-namespace:System.Collections.Generic;assembly=netstandard"
             ...>
    <CollectionView>
        <CollectionView.ItemsSource>
            <scg:List x:TypeArguments="x:String">
                <x:String>Baboon</x:String>
                <x:String>Capuchin Monkey</x:String>
                <x:String>Blue Monkey</x:String>
                <x:String>Squirrel Monkey</x:String>
                <x:String>Golden Lion Tamarin</x:String>
                <x:String>Howler Monkey</x:String>
                <x:String>Japanese Macaque</x:String>
            </scg:List>
        </CollectionView.ItemsSource>
    </CollectionView>
</ContentPage>
```

Dans cet exemple, `System.Collections.Generic` est défini en tant `scg` qu’espace de noms XAML. La `CollectionView.ItemsSource` propriété est définie sur un `List<T>` qui est instancié avec un `string` argument de type, à l’aide du `x:String` type intégré XAML 2009. La `List<string>` collection est initialisée avec plusieurs `string` éléments.

Une autre solution, mais équivalente, `List<T>` la collection peut être instanciée avec le `String` type CLR :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:scg="clr-namespace:System.Collections.Generic;assembly=netstandard"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
             ...>
    <CollectionView>
        <CollectionView.ItemsSource>
            <scg:List x:TypeArguments="sys:String">
                <sys:String>Baboon</sys:String>
                <sys:String>Capuchin Monkey</sys:String>
                <sys:String>Blue Monkey</sys:String>
                <sys:String>Squirrel Monkey</sys:String>
                <sys:String>Golden Lion Tamarin</sys:String>
                <sys:String>Howler Monkey</sys:String>
                <sys:String>Japanese Macaque</sys:String>
            </scg:List>
        </CollectionView.ItemsSource>
    </CollectionView>
</ContentPage>
```

## <a name="single-object-type-argument"></a>Argument de type d’objet unique

Un argument de type d’objet unique peut être spécifié en tant qu’argument de chaîne `x:TypeArguments` préfixé à l’aide de la directive :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:models="clr-namespace:GenericsDemo.Models"
             xmlns:scg="clr-namespace:System.Collections.Generic;assembly=netstandard"
             ...>
    <CollectionView>
        <CollectionView.ItemsSource>
            <scg:List x:TypeArguments="models:Monkey">
                <models:Monkey Name="Baboon"
                               Location="Africa and Asia"
                               ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg" />
                <models:Monkey Name="Capuchin Monkey"
                               Location="Central and South America"
                               ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg" />
                <models:Monkey Name="Blue Monkey"
                               Location="Central and East Africa"
                               ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg" />
            </scg:List>
        </CollectionView.ItemsSource>
        <CollectionView.ItemTemplate>
            <DataTemplate>
                <Grid Padding="10">
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="Auto" />
                    </Grid.ColumnDefinitions>
                    <Image Grid.RowSpan="2"
                           Source="{Binding ImageUrl}"
                           Aspect="AspectFill"
                           HeightRequest="60"
                           WidthRequest="60" />
                    <Label Grid.Column="1"
                           Text="{Binding Name}"
                           FontAttributes="Bold" />
                    <Label Grid.Row="1"
                           Grid.Column="1"
                           Text="{Binding Location}"
                           FontAttributes="Italic"
                           VerticalOptions="End" />
                </Grid>
            </DataTemplate>
        </CollectionView.ItemTemplate>
    </CollectionView>
</ContentPage>
```

Dans cet exemple, `GenericsDemo.Models` est défini en tant `models` qu’espace de noms `System.Collections.Generic` XAML et est défini `scg` en tant qu’espace de noms XAML. La `CollectionView.ItemsSource` propriété est définie sur un `List<T>` qui est instancié avec un `Monkey` argument de type. La `List<Monkey>` collection est initialisée avec plusieurs `Monkey` éléments, et une [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) qui définit l’apparence de chaque `Monkey` objet est définie en tant `ItemTemplate` que du [`CollectionView`](xref:Xamarin.Forms.CollectionView)de.

## <a name="multiple-type-arguments"></a>Plusieurs arguments de type

Plusieurs arguments de type peuvent être spécifiés en tant qu’arguments de chaîne préfixés, séparés par `x:TypeArguments` une virgule, à l’aide de la directive. Quand une contrainte générique utilise des types génériques, les arguments de type de contrainte imbriquée sont contenus entre parenthèses :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:models="clr-namespace:GenericsDemo.Models"
             xmlns:scg="clr-namespace:System.Collections.Generic;assembly=netstandard"
             ...>
    <CollectionView>
        <CollectionView.ItemsSource>
            <scg:List x:TypeArguments="scg:KeyValuePair(x:String,models:Monkey)">
                <scg:KeyValuePair x:TypeArguments="x:String,models:Monkey">
                    <x:Arguments>
                        <x:String>Baboon</x:String>
                        <models:Monkey Location="Africa and Asia"
                                       ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg" />
                    </x:Arguments>
                </scg:KeyValuePair>
                <scg:KeyValuePair x:TypeArguments="x:String,models:Monkey">
                    <x:Arguments>
                        <x:String>Capuchin Monkey</x:String>
                        <models:Monkey Location="Central and South America"
                                       ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg" />   
                    </x:Arguments>
                </scg:KeyValuePair>
                <scg:KeyValuePair x:TypeArguments="x:String,models:Monkey">
                    <x:Arguments>
                        <x:String>Blue Monkey</x:String>
                        <models:Monkey Location="Central and East Africa"
                                       ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg" />
                    </x:Arguments>
                </scg:KeyValuePair>
            </scg:List>
        </CollectionView.ItemsSource>
        <CollectionView.ItemTemplate>
            <DataTemplate>
                <Grid Padding="10">
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="Auto" />
                    </Grid.ColumnDefinitions>
                    <Image Grid.RowSpan="2"
                           Source="{Binding Value.ImageUrl}"
                           Aspect="AspectFill"
                           HeightRequest="60"
                           WidthRequest="60" />
                    <Label Grid.Column="1"
                           Text="{Binding Key}"
                           FontAttributes="Bold" />
                    <Label Grid.Row="1"
                           Grid.Column="1"
                           Text="{Binding Value.Location}"
                           FontAttributes="Italic"
                           VerticalOptions="End" />
                </Grid>
            </DataTemplate>
        </CollectionView.ItemTemplate>
    </CollectionView>
</ContentPage    
```

Dans cet exemple, `GenericsDemo.Models` est défini en tant `models` qu’espace de noms `System.Collections.Generic` XAML et est défini `scg` en tant qu’espace de noms XAML. La `CollectionView.ItemsSource` propriété est définie sur un `List<T>` qui est instancié avec une `KeyValuePair<TKey, TValue>` contrainte, avec les arguments `string` de type de contrainte interne `Monkey`et. La `List<KeyValuePair<string,Monkey>>` collection est initialisée avec plusieurs `KeyValuePair` éléments, en utilisant le constructeur non `KeyValuePair` défini par défaut, [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) et un qui définit l’apparence `Monkey` de chaque objet est défini `ItemTemplate` comme le [`CollectionView`](xref:Xamarin.Forms.CollectionView)du de. Pour plus d’informations sur le passage d’arguments à un constructeur non défini par défaut, consultez [passage des arguments de constructeur](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments).

## <a name="related-links"></a>Liens connexes

- [Génériques en XAML (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-generics/)
- [Primitives de langage XAML 2009](/dotnet/desktop-wpf/xaml-services/types-for-primitives#xaml-2009-language-primitives)
- [x :Type (extension de balisage)](~/xamarin-forms/xaml/markup-extensions/consuming.md#type)
- [Passer des arguments de constructeur](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments)
