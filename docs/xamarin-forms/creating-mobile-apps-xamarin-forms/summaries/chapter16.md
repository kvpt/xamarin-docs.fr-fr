---
title: Résumé du chapitre 16. Liaison de données
description: 'Création d’applications mobiles avec Xamarin.Forms : résumé du chapitre 16. Liaison de données'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: ED997DB0-C229-4868-A5FB-928703B377D6
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 2d61413fb1d8c28a3957da53601d0ad682f35518
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "70771104"
---
# <a name="summary-of-chapter-16-data-binding"></a>Résumé du chapitre 16. Liaison de données

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)

> [!NOTE] 
> Notes sur cette page indiquent des zones où Xamarin.Forms est différente de la matière présentée dans le livre.

Les programmeurs souvent se retrouvent écrire des gestionnaires d’événements qui détectent lorsqu’une propriété d’un objet a changé et l’utilisent pour modifier la valeur d’une propriété dans un autre objet. Ce processus peut être automatisé à l’aide de la technique de *liaison de données*. Liaisons de données sont généralement définis dans XAML et font partie de la définition de l’interface utilisateur.

Très souvent, ces liaisons de données connecter des objets d’interface utilisateur aux données sous-jacentes. Il s’agit d’une technique qui est explorée plus en revue dans le [**chapitre 18. MVVM**](chapter18.md). Toutefois, les liaisons de données peuvent également connecter deux ou plusieurs éléments d’interface utilisateur. La plupart des premiers exemples de liaison de données dans ce chapitre présentent cette technique.

## <a name="binding-basics"></a>Principes fondamentaux de liaison

Plusieurs propriétés, méthodes et classes sont impliqués dans la liaison de données :

- La classe [`Binding`](xref:Xamarin.Forms.Binding) dérive de [`BindingBase`](xref:Xamarin.Forms.BindingBase) et encapsule de nombreuses caractéristiques d’une liaison de données
- La propriété [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) est définie par la classe [`BindableObject`](xref:Xamarin.Forms.BindableObject)
- La méthode [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) est également définie par la classe [`BindableObject`](xref:Xamarin.Forms.BindableObject)
- La classe [`BindableObjectExtensions`](xref:Xamarin.Forms.BindableObjectExtensions) définit trois méthodes de `SetBinding` supplémentaires

Les deux classes suivantes prennent en charge les extensions de balisage XAML pour les liaisons :

- [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension) prend en charge l’extension de balisage `Binding`
- [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) prend en charge l’extension de balisage `x:Reference`

Deux interfaces sont impliqués dans la liaison de données :

- [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) dans l’espace de noms `System.ComponentModel` correspond à l’implémentation d’une notification en cas de modification d’une propriété
- [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) est utilisé pour définir de petites classes qui convertissent les valeurs d’un type en un autre dans les liaisons de données

Une liaison de données connecte à deux propriétés de l’objet de même, ou deux objets différents (le plus souvent). Ces deux propriétés sont appelées la *source* et la *cible*. En règle générale, une modification dans la propriété source provoque une modification se produise dans la propriété cible, mais parfois, la direction est inversée. Quel que soit :

- la propriété *cible* doit être sauvegardée par un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)
- la propriété *source* est généralement un membre d’une classe qui implémente [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)

Une classe qui implémente `INotifyPropertyChanged` déclenche un événement [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged) lorsqu’une propriété change de valeur. `BindableObject` implémente `INotifyPropertyChanged` et déclenche automatiquement un événement `PropertyChanged` lorsqu’une propriété stockée par un `BindableProperty` modifie des valeurs, mais vous pouvez écrire vos propres classes qui implémentent `INotifyPropertyChanged` sans dériver de `BindableObject`.

## <a name="code-and-xaml"></a>Code et XAML

L’exemple [**OpacityBindingCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode) montre comment définir une liaison de données dans le code :

- La source est la propriété `Value` d’un `Slider`
- La cible est la propriété `Opacity` d’un `Label`

Les deux objets sont connectés en définissant la `BindingContext` de l’objet `Label` sur l’objet `Slider`. Les deux propriétés sont connectées en appelant une méthode d’extension [`SetBinding`](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) sur le `Label` référençant la propriété pouvant être liée `OpacityProperty` et la propriété `Value` du `Slider` exprimée sous la forme d’une chaîne.

Si vous manipulez le `Slider`, les `Label` se estompent et s’affichent à l’écran.

[**OpacityBindingXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml) est le même programme que l’ensemble de liaisons de données en XAML. La `BindingContext` de la `Label` est définie sur une extension de balisage `x:Reference` référençant le `Slider`, et la propriété `Opacity` de la `Label` est définie sur l’extension de balisage `Binding` avec sa propriété [`Path`](xref:Xamarin.Forms.Binding.Path) référençant la propriété `Value` du `Slider`.

## <a name="source-and-bindingcontext"></a>Source et BindingContext

L’exemple [**BindingSourceCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode) illustre une autre approche dans le code. Un objet `Binding` est créé en affectant à la propriété [`Source`](xref:Xamarin.Forms.Binding.Source) la valeur de l’objet `Slider` et à la propriété [`Path`](xref:Xamarin.Forms.Binding.Path) la valeur « value ». La méthode [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) de `BindableObject` est ensuite appelée sur l’objet `Label`.

Le [constructeur`Binding`](xref:Xamarin.Forms.Binding.%23ctor(System.String,Xamarin.Forms.BindingMode,Xamarin.Forms.IValueConverter,System.Object,System.String,System.Object)) peut également avoir été utilisé pour définir l’objet `Binding`.

L’exemple [**BindingSourceXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml) illustre la technique comparable en XAML. La propriété `Opacity` du `Label` est définie sur une extension de balisage `Binding` avec [`Path`](xref:Xamarin.Forms.Binding.Path) définie sur la propriété `Value` et [`Source`](xref:Xamarin.Forms.Binding.Source) définie sur une extension de balisage `x:Reference` incorporée.

En résumé, il existe deux façons de référencer l’objet de source de liaison :

- Via la propriété `BindingContext` de la cible
- Via la propriété `Source` de l’objet `Binding` lui-même

Si les deux sont spécifiés, la seconde est prioritaire. L’avantage du `BindingContext` est qu’il est propagé via l’arborescence d’éléments visuels. C’est *très* pratique si plusieurs propriétés cibles sont liées au même objet source.

Le programme [**WebViewDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo) illustre cette technique avec l’élément [`WebView`](xref:Xamarin.Forms.WebView) . Deux éléments `Button` pour naviguer vers l’avant et vers l’arrière héritent d’une `BindingContext` de leur parent qui fait référence à la `WebView`. Les propriétés `IsEnabled` des deux boutons ont ensuite des extensions de balisage `Binding` simples qui ciblent le bouton `IsEnabled` propriétés en fonction des paramètres de l' [`CanGoBack`](xref:Xamarin.Forms.WebView.CanGoBack) et [`CanGoForward`](xref:Xamarin.Forms.WebView.CanGoForward) des propriétés en lecture seule du `WebView`.

## <a name="the-binding-mode"></a>Le mode de liaison

Affectez à la propriété [`Mode`](xref:Xamarin.Forms.BindingBase.Mode) de `Binding` la valeur d’un membre de l’énumération [`BindingMode`](xref:Xamarin.Forms.BindingMode) :

- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay) afin que les modifications apportées à la propriété source affectent la cible
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource) afin que les modifications apportées à la propriété cible affectent la source
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) afin que les modifications apportées à la source et à la cible s’affectent les unes aux autres
- [`Default`](xref:Xamarin.Forms.BindingMode.Default) d’utiliser la [`DefaultBindingMode`](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) spécifiée lors de la création de la `BindableProperty` cible. Si aucune valeur n’a été spécifiée, la valeur par défaut est `OneWay` pour les propriétés pouvant être liées normales, et `OneWayToSource` pour les propriétés pouvant être liées en lecture seule.

> [!NOTE]
> L’énumération `BindingMode` comprend maintenant également `OnTime` pour appliquer une liaison uniquement lorsque le contexte de liaison change et non lorsque la propriété source change.

Les propriétés qui sont susceptibles d’être les cibles des liaisons de données dans les scénarios MVVM ont généralement une `DefaultBindingMode` de `TwoWay`. Il s'agit des paramètres suivants :

- Propriété `Value` de `Slider` et `Stepper`
- Propriété `IsToggled` de `Switch`
- `Text` propriété de `Entry`, `Editor`et `SearchBar`
- Propriété `Date` de `DatePicker`
- Propriété `Time` de `TimePicker`

L’exemple [**BindingModes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes) illustre les quatre modes de liaison avec une liaison de données où la cible est la propriété `FontSize` d’un `Label` et la source est la propriété `Value` d’un `Slider`. Cela permet à chaque `Slider` de contrôler la taille de police des `Label`correspondants. Toutefois, les éléments `Slider` ne sont pas initialisés car le `DefaultBindingMode` de la propriété `FontSize` est `OneWay`.

L’exemple [**ReverseBinding**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding) définit les liaisons sur la propriété `Value` du `Slider` référençant la propriété `FontSize` de chaque `Label`. Cela semble être inversé, mais il fonctionne mieux dans initialzing les éléments `Slider`, car la `Value` propriété de l' `Slider` a une `DefaultBindingMode` de `TwoWay`.

[![Capture d’écran triple de la liaison inverse](images/ch16fg06-small.png "Liaison inverse")](images/ch16fg06-large.png#lightbox "Liaison inverse")

Ce comportement est analogue à la façon dont les liaisons sont définies dans MVVM, et vous utiliserez ce type de liaison fréquemment.

## <a name="string-formatting"></a>Chaîne mise en forme

Lorsque la propriété cible est de type `string`, vous pouvez utiliser la propriété [`StringFormat`](xref:Xamarin.Forms.BindingBase.StringFormat) définie par `BindingBase` pour convertir la source en `string`. Définissez la propriété `StringFormat` sur une chaîne de mise en forme .NET que vous utiliseriez avec le format statique [`String.Format`](xref:System.String.Format(System.String,System.Object)) pour afficher l’objet. Lorsque vous utilisez cette chaîne de mise en forme dans une extension de balisage, placez-la entre guillemets simples afin des accolades ne sont pas être confondue avec une extension de balisage incorporé.

L’exemple [**ShowViewValues**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues) montre comment utiliser `StringFormat` en XAML.

L’exemple [**WhatSizeBindings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings) illustre l’affichage de la taille de la page avec des liaisons aux propriétés `Width` et `Height` de la `ContentPage`.

## <a name="why-is-it-called-path"></a>Pourquoi elle est appelée « Path » ?

La propriété [`Path`](xref:Xamarin.Forms.Binding.Path) de `Binding` est appelée, car il peut s’agir d’une série de propriétés et d’indexeurs séparés par des points. L’exemple [**BindingPathDemos**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos) illustre plusieurs exemples.

## <a name="binding-value-converters"></a>Convertisseurs de valeur de liaison

Lorsque les propriétés source et cible d’une liaison de types différents, vous pouvez convertir entre les types à l’aide d’un convertisseur de liaison. Il s’agit d’une classe qui implémente l’interface [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) et contient deux méthodes : [`Convert`](xref:Xamarin.Forms.IValueConverter.Convert(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) pour convertir la source en cible, et [`ConvertBack`](xref:Xamarin.Forms.IValueConverter.ConvertBack(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) pour convertir la cible en source.

La classe [`IntToBoolConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs) de la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) est un exemple de conversion d’un `int` en `bool`. Elle est illustrée par l’exemple [**ButtonEnabler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler) , qui active uniquement le `Button` si au moins un caractère a été tapé dans une `Entry`.

La classe [`BoolToStringConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs) convertit une `bool` en `string` et définit deux propriétés pour spécifier le texte qui doit être retourné pour les valeurs `false` et `true`.
Le [`BoolToColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs) est similaire. L’exemple [**SwitchText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText) illustre l’utilisation de ces deux convertisseurs pour afficher différents textes dans différentes couleurs en fonction d’un paramètre de `Switch`.

Le [`BoolToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs) générique peut remplacer les `BoolToStringConverter` et `BoolToColorConverter` et servir de convertisseur de `bool`à objet généralisé de tout type.

## <a name="bindings-and-custom-views"></a>Liaisons et des vues personnalisées

Vous pouvez simplifier les contrôles personnalisés à l’aide de liaisons de données. Le fichier de code [`NewCheckBox.cs`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs) définit les propriétés `Text`, `TextColor`, `FontSize`, `FontAttributes`et `IsChecked`, mais n’a aucune logique pour les visuels du contrôle.
Au lieu de cela, le fichier [`NewCheckBox.cs.xaml`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml) contient tout le balisage des visuels du contrôle par le biais de liaisons de données sur les éléments `Label` en fonction des propriétés définies dans le fichier code-behind.

L’exemple [**NewCheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo) illustre le contrôle personnalisé `NewCheckBox`.

## <a name="related-links"></a>Liens connexes

- [Chapitre 16 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [Exemples du chapitre 16](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
- [Liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md)
