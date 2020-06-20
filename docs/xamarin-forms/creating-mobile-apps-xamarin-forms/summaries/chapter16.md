---
title: Résumé du chapitre 16. Liaison de données
description: 'Création d’Mobile Apps avec Xamarin.Forms : Résumé du chapitre 16. Liaison de données'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: ED997DB0-C229-4868-A5FB-928703B377D6
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ece93730100001e8339a5f50cdb7ac437d96fa62
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136732"
---
# <a name="summary-of-chapter-16-data-binding"></a>Résumé du chapitre 16. Liaison de données

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)

> [!NOTE] 
> Les notes de cette page indiquent les zones où elles Xamarin.Forms divergent du matériel présenté dans le livre.

Les programmeurs se retrouvent souvent à écrire des gestionnaires d’événements qui détectent quand une propriété d’un objet a changé, et l’utilisent pour modifier la valeur d’une propriété dans un autre objet. Ce processus peut être automatisé à l’aide de la technique de *liaison de données*. Les liaisons de données sont généralement définies en XAML et font partie de la définition de l’interface utilisateur.

Très souvent, ces liaisons de données connectent les objets d’interface utilisateur aux données sous-jacentes. Il s’agit d’une technique qui est explorée plus en revue dans le [**chapitre 18. MVVM**](chapter18.md). Toutefois, les liaisons de données peuvent également connecter deux ou plusieurs éléments d’interface utilisateur. La plupart des premiers exemples de liaison de données dans ce chapitre illustrent cette technique.

## <a name="binding-basics"></a>Notions de base de liaison

Plusieurs propriétés, méthodes et classes sont impliquées dans la liaison de données :

- La [`Binding`](xref:Xamarin.Forms.Binding) classe dérive de [`BindingBase`](xref:Xamarin.Forms.BindingBase) et encapsule de nombreuses caractéristiques d’une liaison de données
- La [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) propriété est définie par la [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe.
- [ `SetBinding` ] (XREF : Xamarin.Forms . BindableObject. SetBinding ( Xamarin.Forms . BindableProperty, Xamarin.Forms . BindingBase)) est également définie par la [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe
- La [`BindableObjectExtensions`](xref:Xamarin.Forms.BindableObjectExtensions) classe définit trois `SetBinding` méthodes supplémentaires

Les deux classes suivantes prennent en charge les extensions de balisage XAML pour les liaisons :

- [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension)prend en charge l' `Binding` extension de balisage
- [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)prend en charge l' `x:Reference` extension de balisage

Deux interfaces sont impliquées dans la liaison de données :

- [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)dans l' `System.ComponentModel` espace de noms, la notification est implémentée lorsqu’une propriété change
- [`IValueConverter`](xref:Xamarin.Forms.IValueConverter)est utilisé pour définir des classes de petite taille qui convertissent les valeurs d’un type en un autre dans les liaisons de données

Une liaison de données connecte deux propriétés du même objet, ou (plus communément) deux objets différents. Ces deux propriétés sont appelées la *source* et la *cible*. En règle générale, une modification apportée à la propriété source provoque une modification dans la propriété cible, mais parfois la direction est inversée. Sans

- la propriété *cible* doit être sauvegardée par un[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)
- la propriété *source* est généralement un membre d’une classe qui implémente[`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)

Une classe qui implémente `INotifyPropertyChanged` déclenche un [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged) événement lorsqu’une propriété change de valeur. `BindableObject`implémente `INotifyPropertyChanged` et déclenche automatiquement un `PropertyChanged` événement quand une propriété stockée par un `BindableProperty` change des valeurs, mais vous pouvez écrire vos propres classes qui implémentent `INotifyPropertyChanged` sans dériver de `BindableObject` .

## <a name="code-and-xaml"></a>Code et XAML

L’exemple [**OpacityBindingCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode) montre comment définir une liaison de données dans le code :

- La source est la `Value` propriété d’un`Slider`
- La cible est la `Opacity` propriété d’un`Label`

Les deux objets sont connectés en affectant `BindingContext` à l’objet de l' `Label` objet `Slider` . Les deux propriétés sont connectées en appelant une [`SetBinding`](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) méthode d’extension sur le `Label` référençant la `OpacityProperty` propriété pouvant être liée et la `Value` propriété de l' `Slider` exprimée sous la forme d’une chaîne.

Si vous manipulez le, `Slider` le `Label` se met en fondu et disparaît de l’affichage.

[**OpacityBindingXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml) est le même programme que l’ensemble de liaisons de données en XAML. Le `BindingContext` du `Label` a la valeur d’une `x:Reference` extension de balisage référençant le `Slider` , et la `Opacity` propriété du `Label` a la valeur de l’extension de `Binding` balisage avec sa [`Path`](xref:Xamarin.Forms.Binding.Path) propriété référençant la `Value` propriété de `Slider` .

## <a name="source-and-bindingcontext"></a>Source et BindingContext

L’exemple [**BindingSourceCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode) illustre une autre approche dans le code. Pour `Binding` créer un objet, affectez [`Source`](xref:Xamarin.Forms.Binding.Source) à la propriété l' `Slider` objet et [`Path`](xref:Xamarin.Forms.Binding.Path) à la propriété la valeur « value ». [ `SetBinding` ] (XREF : Xamarin.Forms . BindableObject. SetBinding ( Xamarin.Forms . BindableProperty, Xamarin.Forms . BindingBase)) de `BindableObject` est ensuite appelée sur l' `Label` objet.

Le [ `Binding` constructeur] (XREF : Xamarin.Forms . Liaison .% 23ctor (System. String, Xamarin.Forms . BindingMode, Xamarin.Forms . IValueConverter, System. Object, System. String, System. Object)) peut également avoir été utilisé pour définir l' `Binding` objet.

L’exemple [**BindingSourceXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml) illustre la technique comparable en XAML. La `Opacity` propriété de `Label` a la valeur d’une `Binding` extension de balisage avec [`Path`](xref:Xamarin.Forms.Binding.Path) la valeur affectée à la `Value` propriété et la valeur d' [`Source`](xref:Xamarin.Forms.Binding.Source) une `x:Reference` extension de balisage incorporée.

En résumé, il existe deux manières de référencer l’objet de source de liaison :

- Via la `BindingContext` propriété de la cible
- Via la `Source` propriété de l' `Binding` objet lui-même

Si les deux sont spécifiés, le deuxième est prioritaire. L’avantage du `BindingContext` est qu’il est propagé via l’arborescence d’éléments visuels. C’est *très* pratique si plusieurs propriétés cibles sont liées au même objet source.

Le programme [**WebViewDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo) illustre cette technique avec l' [`WebView`](xref:Xamarin.Forms.WebView) élément. Deux `Button` éléments pour naviguer vers l’avant et vers l’arrière héritent d’un `BindingContext` de leur parent qui référence le `WebView` . Les `IsEnabled` Propriétés des deux boutons ont ensuite des `Binding` extensions de balisage simples qui ciblent les `IsEnabled` Propriétés de bouton en fonction des paramètres des [`CanGoBack`](xref:Xamarin.Forms.WebView.CanGoBack) [`CanGoForward`](xref:Xamarin.Forms.WebView.CanGoForward) Propriétés et en lecture seule du `WebView` .

## <a name="the-binding-mode"></a>Mode de liaison

Affectez [`Mode`](xref:Xamarin.Forms.BindingBase.Mode) à la propriété de `Binding` la valeur un membre de l' [`BindingMode`](xref:Xamarin.Forms.BindingMode) énumération :

- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay)afin que les modifications apportées à la propriété source affectent la cible
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource)afin que les modifications apportées à la propriété cible affectent la source
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay)afin que les modifications apportées à la source et à la cible s’affectent les unes aux autres
- [`Default`](xref:Xamarin.Forms.BindingMode.Default)pour utiliser le [`DefaultBindingMode`](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) spécifié lors de la création de la cible `BindableProperty` . Si aucune valeur n’a été spécifiée, la valeur par défaut est `OneWay` pour les propriétés pouvant être liées normales et `OneWayToSource` pour les propriétés pouvant être liées en lecture seule.

> [!NOTE]
> `BindingMode`Désormais, l’énumération comprend également l' `OnTime` application d’une liaison uniquement lorsque le contexte de liaison change et non lorsque la propriété source change.

Les propriétés qui sont susceptibles d’être les cibles des liaisons de données dans les scénarios MVVM ont généralement un `DefaultBindingMode` de `TwoWay` . Ces règles sont les suivantes :

- Propriété `Value` de `Slider` et `Stepper`
- Propriété `IsToggled` de `Switch`
- `Text`propriété de `Entry` , `Editor` et`SearchBar`
- Propriété `Date` de `DatePicker`
- Propriété `Time` de `TimePicker`

L’exemple [**BindingModes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes) illustre les quatre modes de liaison avec une liaison de données où la cible est la `FontSize` propriété d’un `Label` et la source est la `Value` propriété d’un `Slider` . Cela permet à chacun de `Slider` contrôler la taille de police du correspondant `Label` . Toutefois `Slider` , les éléments ne sont pas initialisés car le `DefaultBindingMode` de la `FontSize` propriété est `OneWay` .

L’exemple [**ReverseBinding**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding) définit les liaisons sur la `Value` propriété du `Slider` référençant la `FontSize` propriété de chaque `Label` . Cela semble être inversé, mais il fonctionne mieux dans initialzing les `Slider` éléments, car la `Value` propriété de `Slider` a un `DefaultBindingMode` de `TwoWay` .

[![Capture d’écran triple de la liaison inverse](images/ch16fg06-small.png "Liaison inverse")](images/ch16fg06-large.png#lightbox "Liaison inverse")

Cela est similaire à la façon dont les liaisons sont définies dans MVVM, et vous utiliserez fréquemment ce type de liaison.

## <a name="string-formatting"></a>Mise en forme de chaîne

Lorsque la propriété cible est de type `string` , vous pouvez utiliser la [`StringFormat`](xref:Xamarin.Forms.BindingBase.StringFormat) propriété définie par `BindingBase` pour convertir la source en `string` . Affectez `StringFormat` à la propriété une chaîne de mise en forme .net que vous utiliseriez avec le [`String.Format`](xref:System.String.Format(System.String,System.Object)) format statique pour afficher l’objet. Lors de l’utilisation de cette chaîne de mise en forme dans une extension de balisage, mettez-la entre guillemets simples afin que les accolades ne soient pas confondes pour une extension de balisage incorporée.

L’exemple [**ShowViewValues**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues) montre comment utiliser `StringFormat` en XAML.

L’exemple [**WhatSizeBindings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings) illustre l’affichage de la taille de la page avec des liaisons `Width` aux `Height` Propriétés et de `ContentPage` .

## <a name="why-is-it-called-path"></a>Pourquoi est-ce appelé « chemin d’accès » ?

La [`Path`](xref:Xamarin.Forms.Binding.Path) propriété de `Binding` est appelée, car il peut s’agir d’une série de propriétés et d’indexeurs séparés par des points. L’exemple [**BindingPathDemos**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos) illustre plusieurs exemples.

## <a name="binding-value-converters"></a>Convertisseurs de valeurs de liaison

Lorsque les propriétés source et cible d’une liaison sont des types différents, vous pouvez effectuer une conversion entre les types à l’aide d’un convertisseur de liaison. Il s’agit d’une classe qui implémente l' [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) interface et contient deux méthodes : [`Convert`](xref:Xamarin.Forms.IValueConverter.Convert(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) pour convertir la source en cible et [`ConvertBack`](xref:Xamarin.Forms.IValueConverter.ConvertBack(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) pour convertir la cible en source.

La [`IntToBoolConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs) classe de la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) est un exemple de conversion d’un `int` en `bool` . Elle est illustrée par l’exemple [**ButtonEnabler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler) , qui active uniquement `Button` si au moins un caractère a été tapé dans un `Entry` .

La [`BoolToStringConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs) classe convertit un `bool` en `string` et définit deux propriétés pour spécifier le texte qui doit être retourné pour les `false` `true` valeurs et.
[`BoolToColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs)Est similaire. L’exemple [**SwitchText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText) illustre l’utilisation de ces deux convertisseurs pour afficher différents textes dans différentes couleurs en fonction d’un `Switch` paramètre.

Le générique [`BoolToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs) peut remplacer `BoolToStringConverter` et `BoolToColorConverter` et servir de `bool` convertisseur général-à-objet de tout type.

## <a name="bindings-and-custom-views"></a>Liaisons et vues personnalisées

Vous pouvez simplifier des contrôles personnalisés à l’aide de liaisons de données. Le [`NewCheckBox.cs`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs) fichier de code définit les propriétés,,, `Text` `TextColor` `FontSize` `FontAttributes` et `IsChecked` , mais il n’a aucune logique pour les visuels du contrôle.
Au lieu [`NewCheckBox.cs.xaml`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml) de cela, le fichier contient tout le balisage des visuels du contrôle par le biais de liaisons de données sur les `Label` éléments en fonction des propriétés définies dans le fichier code-behind.

L’exemple [**NewCheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo) illustre le `NewCheckBox` contrôle personnalisé.

## <a name="related-links"></a>Liens connexes

- [Chapitre 16 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [Exemples du chapitre 16](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
- [Liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md)
