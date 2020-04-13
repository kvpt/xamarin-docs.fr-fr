---
title: Résumé du chapitre 16. Liaison de données
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 16. Liaison de données'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: ED997DB0-C229-4868-A5FB-928703B377D6
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 2d61413fb1d8c28a3957da53601d0ad682f35518
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771104"
---
# <a name="summary-of-chapter-16-data-binding"></a>Résumé du chapitre 16. Liaison de données

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)

> [!NOTE] 
> Les notes sur cette page indiquent les zones où Xamarin.Forms a divergé du matériel présenté dans le livre.

Les programmeurs se retrouvent souvent à écrire des gestionnaires d’événements qui détectent quand une propriété d’un objet a changé, et l’utilisent pour changer la valeur d’une propriété dans un autre objet. Ce processus peut être automatisé avec la technique de liaison de *données*. Les liaisons de données sont généralement définies dans XAML et font partie de la définition de l’interface utilisateur.

Très souvent, ces liaisons de données relient les objets d’interface utilisateur aux données sous-jacentes. Il s’agit d’une technique qui est explorée plus dans [**le chapitre 18. MVVM**](chapter18.md). Toutefois, les liaisons de données peuvent également connecter deux éléments ou plus d’interface utilisateur. La plupart des premiers exemples de liaison de données dans ce chapitre démontrent cette technique.

## <a name="binding-basics"></a>Bases contraignantes

Plusieurs propriétés, méthodes et classes sont impliquées dans la liaison de données :

- La [`Binding`](xref:Xamarin.Forms.Binding) classe dérive [`BindingBase`](xref:Xamarin.Forms.BindingBase) et encapsule de nombreuses caractéristiques d’une liaison de données
- La [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) propriété est [`BindableObject`](xref:Xamarin.Forms.BindableObject) définie par la classe
- La [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) méthode est également [`BindableObject`](xref:Xamarin.Forms.BindableObject) définie par la classe
- La [`BindableObjectExtensions`](xref:Xamarin.Forms.BindableObjectExtensions) classe définit `SetBinding` trois méthodes supplémentaires

Les deux classes suivantes prennent en charge les extensions de balisage XAML pour les fixations :

- [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension)soutient `Binding` l’extension de la majoration
- [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)soutient `x:Reference` l’extension de la majoration

Deux interfaces sont impliquées dans la liaison de données :

- [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)dans `System.ComponentModel` l’espace nom est pour la mise en œuvre de notification quand une propriété change
- [`IValueConverter`](xref:Xamarin.Forms.IValueConverter)est utilisé pour définir les petites classes qui convertissent les valeurs d’un type à l’autre dans les liaisons de données

Une liaison de données relie deux propriétés d’un même objet, ou (plus généralement) deux objets différents. Ces deux propriétés sont appelées la *source* et la *cible*. En général, un changement dans la propriété source provoque un changement dans la propriété cible, mais parfois la direction est inversée. Indépendamment:

- la propriété *cible* doit être adossée à un[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)
- la propriété *source* est généralement membre d’une classe qui met en œuvre[`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)

Une classe qui `INotifyPropertyChanged` met [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged) en œuvre déclenche un événement lorsqu’une propriété change de valeur. `BindableObject``INotifyPropertyChanged` implémente et `PropertyChanged` déclenche automatiquement un `BindableProperty` événement lorsqu’une propriété soutenue par `INotifyPropertyChanged` une propriété change `BindableObject`de valeur, mais vous pouvez écrire vos propres classes qui mettent en œuvre sans dérivé de .

## <a name="code-and-xaml"></a>Code et XAML

[**L’échantillon OpacityBindingCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode) montre comment définir une liaison de données dans le code :

- La source `Value` est la propriété d’un`Slider`
- La cible `Opacity` est la propriété d’un`Label`

Les deux objets sont `BindingContext` connectés `Label` en définissant l’objet à l’objet. `Slider` Les deux propriétés sont [`SetBinding`](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) reliées `Label` en appelant `OpacityProperty` une méthode `Value` d’extension `Slider` sur le référencement de la propriété liant et la propriété de l’exprimé comme une chaîne.

Manipuler le `Slider` provoque `Label` alors le s’estomper dans et hors de la vue.

[**L’OpacityBindingXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml) est le même programme avec l’ensemble de liaison de données dans XAML. Le `BindingContext` de `Label` la est `x:Reference` fixé à une `Slider`extension `Opacity` de balisage faisant référence à la , et la propriété `Label` de l’est fixé à l’extension `Binding` de balisage avec sa [`Path`](xref:Xamarin.Forms.Binding.Path) propriété faisant référence à la `Value` propriété de la `Slider`.

## <a name="source-and-bindingcontext"></a>Source et BindingContext

[**L’échantillon BindingSourceCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode) affiche une approche alternative dans le code. Un `Binding` objet est créé [`Source`](xref:Xamarin.Forms.Binding.Source) en `Slider` définissant [`Path`](xref:Xamarin.Forms.Binding.Path) la propriété à l’objet et la propriété à "Valeur". La [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) méthode `BindableObject` de est `Label` alors appelée sur l’objet.

Le [ `Binding` constructeur](xref:Xamarin.Forms.Binding.%23ctor(System.String,Xamarin.Forms.BindingMode,Xamarin.Forms.IValueConverter,System.Object,System.String,System.Object)) aurait également pu être `Binding` utilisé pour définir l’objet.

[**L’échantillon bindingSourceXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml) montre la technique comparable dans XAML. La `Opacity` propriété `Label` de la `Binding` est définie [`Path`](xref:Xamarin.Forms.Binding.Path) à une `Value` extension [`Source`](xref:Xamarin.Forms.Binding.Source) de balisage avec réglé à la propriété et réglé à une extension de balisage intégré. `x:Reference`

En résumé, il existe deux façons de référencer l’objet source contraignante :

- Par `BindingContext` la propriété de la cible
- Par `Source` la propriété `Binding` de l’objet lui-même

Si les deux sont spécifiés, le second prime. L’avantage `BindingContext` de la est qu’il est propagé à travers l’arbre visuel. Ceci est *très* pratique si plusieurs propriétés cibles sont liées au même objet source.

Le programme [**WebViewDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo) démontre cette [`WebView`](xref:Xamarin.Forms.WebView) technique avec l’élément. Deux `Button` éléments pour naviguer vers l’arrière et vers l’avant héritent d’un `BindingContext` de leurs parents qui fait référence à la `WebView`. Les `IsEnabled` propriétés des deux boutons `Binding` ont alors des extensions de balisage simples qui ciblent les propriétés bouton `IsEnabled` en fonction des paramètres de la et [`CanGoBack`](xref:Xamarin.Forms.WebView.CanGoBack) [`CanGoForward`](xref:Xamarin.Forms.WebView.CanGoForward) la lecture seulement des propriétés de la `WebView`.

## <a name="the-binding-mode"></a>Le mode de liaison

Définissez [`Mode`](xref:Xamarin.Forms.BindingBase.Mode) la `Binding` propriété d’un membre de l’énumération [`BindingMode`](xref:Xamarin.Forms.BindingMode) :

- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay)de sorte que les changements dans la propriété source affectent la cible
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource)de sorte que les changements dans la propriété cible affectent la source
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay)de sorte que les changements dans la source et la cible affectent les uns les autres
- [`Default`](xref:Xamarin.Forms.BindingMode.Default)d’utiliser [`DefaultBindingMode`](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) le spécifié `BindableProperty` lorsque la cible a été créée. Si aucune n’a `OneWay` été spécifiée, la `OneWayToSource` valeur par défaut est pour les propriétés li bindables normales, et pour les propriétés li bindables lisibles.

> [!NOTE]
> Le `BindingMode` recensement comprend `OnTime` maintenant également pour l’application d’une liaison seulement lorsque le contexte contraignant change et non lorsque la propriété source change.

Les propriétés qui sont susceptibles d’être la cible de `DefaultBindingMode` liaisons de données dans les scénarios MVVM ont généralement un de `TwoWay`. Ces règles sont les suivantes :

- Propriété `Value` de `Slider` et `Stepper`
- Propriété `IsToggled` de `Switch`
- `Text`propriété `Entry`de `Editor`, , et`SearchBar`
- Propriété `Date` de `DatePicker`
- Propriété `Time` de `TimePicker`

L’échantillon [**BindingModes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes) démontre les quatre modes contraignants avec `FontSize` une `Label` liaison de données `Value` où `Slider`la cible est la propriété d’un et la source est la propriété d’un . Cela permet `Slider` à chacun de contrôler `Label`la taille de la police de la correspondante . Mais `Slider` les éléments ne sont `DefaultBindingMode` pas `FontSize` parasésés parce que la propriété est `OneWay`.

L’échantillon [**ReverseBinding**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding) fixe les `Value` liaisons `Slider` sur `FontSize` la propriété `Label`du référencement de la propriété de chacun . Cela semble être à l’envers, mais `Slider` il fonctionne `Value` mieux `Slider` dans `DefaultBindingMode` l’initialzing les éléments parce que la propriété de l’a un de `TwoWay`.

[![Triple capture d’écran de Reverse Binding](images/ch16fg06-small.png "Liaison inversée")](images/ch16fg06-large.png#lightbox "Liaison inversée")

Ceci est analogue à la façon dont les fixations sont définies dans MVVM, et vous utiliserez ce type de liaison fréquemment.

## <a name="string-formatting"></a>Formatage de cordes

Lorsque la propriété cible `string`est de [`StringFormat`](xref:Xamarin.Forms.BindingBase.StringFormat) type, `BindingBase` vous pouvez utiliser `string`la propriété définie par pour convertir la source en un . Définissez `StringFormat` la propriété sur une chaîne de formatage [`String.Format`](xref:System.String.Format(System.String,System.Object)) .NET que vous utiliseriez avec le format statique pour afficher l’objet. Lors de l’utilisation de cette chaîne de formatage dans une extension de balisage, l’entourer de guillemets simples afin que les accolades bouclées ne seront pas confondues avec une extension de balisage intégrée.

[**L’échantillon ShowViewValues**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues) montre `StringFormat` comment utiliser XAML.

[**L’échantillon WhatSizeBindings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings) montre l’affichage de la `Width` taille `Height` de `ContentPage`la page avec des fixations à la et les propriétés de la .

## <a name="why-is-it-called-path"></a>Pourquoi s’appelle-t-il "Path" ?

La [`Path`](xref:Xamarin.Forms.Binding.Path) propriété `Binding` de est ainsi appelé parce qu’il peut être une série de propriétés et d’indexeurs séparés par des périodes. [**L’échantillon BindingPathDemos**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos) présente plusieurs exemples.

## <a name="binding-value-converters"></a>Convertisseurs de valeur contraignants

Lorsque les propriétés source et cible d’une liaison sont des types différents, vous pouvez convertir entre les types à l’aide d’un convertisseur de liaison. Il s’agit d’une classe qui implémente l’interface [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) et contient deux méthodes: [`Convert`](xref:Xamarin.Forms.IValueConverter.Convert(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) convertir la source à la cible, et [`ConvertBack`](xref:Xamarin.Forms.IValueConverter.ConvertBack(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) de convertir la cible à la source.

La [`IntToBoolConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs) classe de la bibliothèque [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) est `int` un `bool`exemple pour convertir un . Il est démontré par l’échantillon [**ButtonEnabler,**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler) qui ne permet que `Button` `Entry`si au moins un personnage a été tapé dans un .

La [`BoolToStringConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs) classe `bool` convertit `string` un en a et définit deux propriétés `true` pour spécifier le texte à retourner et les `false` valeurs.
Le [`BoolToColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs) est similaire. [**L’échantillon SwitchText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText) démontre l’utilisation de ces deux convertisseurs pour afficher des textes différents dans des couleurs différentes basées sur un `Switch` paramètre.

Le [`BoolToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs) générique peut `BoolToStringConverter` `BoolToColorConverter` remplacer le convertisseur `bool`généralisé à l’objet et servir de convertisseur généralisé à l’objet de tout type.

## <a name="bindings-and-custom-views"></a>Liaisons et vues personnalisées

Vous pouvez simplifier les contrôles personnalisés à l’aide de liaisons de données. Le [`NewCheckBox.cs`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs) fichier de `Text` `TextColor`code `FontSize` `FontAttributes`définit `IsChecked` , , , et les propriétés, mais n’a aucune logique du tout pour les visuels du contrôle.
Au [`NewCheckBox.cs.xaml`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml) lieu de cela, le fichier contient toutes les balisages pour les visuels du contrôle grâce à des liaisons de données sur les `Label` éléments basés sur les propriétés définies dans le fichier de code derrière.

[**L’échantillon NewCheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo) `NewCheckBox` démontre le contrôle personnalisé.

## <a name="related-links"></a>Liens connexes

- [Chapitre 16 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [Échantillons du chapitre 16](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
- [Liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md)
