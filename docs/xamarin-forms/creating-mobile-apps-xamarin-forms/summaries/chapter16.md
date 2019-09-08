---
title: Résumé du chapitre 16. Liaison de données
description: 'Création d’Mobile Apps avec Xamarin. Forms : Résumé du chapitre 16. Liaison de données'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: ED997DB0-C229-4868-A5FB-928703B377D6
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 2d61413fb1d8c28a3957da53601d0ad682f35518
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771104"
---
# <a name="summary-of-chapter-16-data-binding"></a>Résumé du chapitre 16. Liaison de données

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)

> [!NOTE] 
> Notes sur cette page indiquent des zones où Xamarin.Forms est différente de la matière présentée dans le livre.

Les programmeurs souvent se retrouvent écrire des gestionnaires d’événements qui détectent lorsqu’une propriété d’un objet a changé et l’utilisent pour modifier la valeur d’une propriété dans un autre objet. Ce processus peut être automatisé avec la technique de *liaison de données*. Liaisons de données sont généralement définis dans XAML et font partie de la définition de l’interface utilisateur.

Très souvent, ces liaisons de données connecter des objets d’interface utilisateur aux données sous-jacentes. Il s’agit d’une technique qui est expliquée plus en [ **chapitre 18. MVVM**](chapter18.md). Toutefois, les liaisons de données peuvent également connecter deux ou plusieurs éléments d’interface utilisateur. La plupart des premiers exemples de liaison de données dans ce chapitre présentent cette technique.

## <a name="binding-basics"></a>Principes fondamentaux de liaison

Plusieurs propriétés, méthodes et classes sont impliqués dans la liaison de données :

- Le [ `Binding` ](xref:Xamarin.Forms.Binding) dérive de la classe [ `BindingBase` ](xref:Xamarin.Forms.BindingBase) et encapsule de nombreuses caractéristiques d’une liaison de données
- Le [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) propriété est définie par le [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) classe
- Le [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) méthode est également définie par le [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) classe
- Le [ `BindableObjectExtensions` ](xref:Xamarin.Forms.BindableObjectExtensions) classe définit trois supplémentaires `SetBinding` méthodes

Les deux classes suivantes prennent en charge les extensions de balisage XAML pour les liaisons :

- [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension) prend en charge le `Binding` extension de balisage
- [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) prend en charge le `x:Reference` extension de balisage

Deux interfaces sont impliqués dans la liaison de données :

- [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) dans le `System.ComponentModel` espace de noms est pour l’implémentation de notification lorsqu’une propriété change
- [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) permet de définir de petites classes qui convertissent les valeurs d’un type vers un autre dans les liaisons de données

Une liaison de données connecte à deux propriétés de l’objet de même, ou deux objets différents (le plus souvent). Ces deux propriétés sont appelées les *source* et *cible*. En règle générale, une modification dans la propriété source provoque une modification se produise dans la propriété cible, mais parfois, la direction est inversée. Quel que soit :

- le *cible* propriété doit être stockée par un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)
- le *source* propriété est généralement un membre d’une classe qui implémente [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)

Une classe qui implémente `INotifyPropertyChanged` se déclenche un [ `PropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged) événement lorsqu’une propriété change de valeur. `BindableObject` implémente `INotifyPropertyChanged` et déclenche automatiquement un `PropertyChanged` événement lorsqu’une propriété stockée par un `BindableProperty` modifie les valeurs, mais vous pouvez écrire vos propres classes qui implémentent `INotifyPropertyChanged` sans dériver `BindableObject`.

## <a name="code-and-xaml"></a>Code et XAML

Le [ **OpacityBindingCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode) exemple montre comment définir une liaison de données dans le code :

- La source est le `Value` propriété d’un `Slider`
- La cible est le `Opacity` propriété d’un `Label`

Les deux objets sont connectés en définissant le `BindingContext` de la `Label` de l’objet à le `Slider` objet. Les deux propriétés sont connectées en appelant un [ `SetBinding` ](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) méthode d’extension sur le `Label` faisant référence à la `OpacityProperty` propriété pouvant être liée et le `Value` propriété de la `Slider` exprimée sous la forme un chaîne.

Manipulation de la `Slider` provoque ensuite la `Label` à apparaître et disparaître.

Le [ **OpacityBindingXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml) est le même programme avec la liaison de données définie dans XAML. Le `BindingContext` du `Label` est définie sur une `x:Reference` extension de balisage faisant référence à la `Slider`et le `Opacity` propriété de la `Label` est défini sur le `Binding` extension de balisage avec son [ `Path` ](xref:Xamarin.Forms.Binding.Path) propriété faisant référence à la `Value` propriété de la `Slider`.

## <a name="source-and-bindingcontext"></a>Source et BindingContext

Le [ **BindingSourceCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode) exemple montre une autre approche dans le code. Un `Binding` objet est créé en définissant le [ `Source` ](xref:Xamarin.Forms.Binding.Source) propriété le `Slider` objet et le [ `Path` ](xref:Xamarin.Forms.Binding.Path) la propriété « Valeur ». Le [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) méthode de `BindableObject` est ensuite appelée sur le `Label` objet.

Le [ `Binding` constructeur](xref:Xamarin.Forms.Binding.%23ctor(System.String,Xamarin.Forms.BindingMode,Xamarin.Forms.IValueConverter,System.Object,System.String,System.Object)) pourrait également avoir été utilisé pour définir le `Binding` objet.

Le [ **BindingSourceXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml) exemple illustre la technique comparable dans XAML. Le `Opacity` propriété de la `Label` est définie sur une `Binding` extension de balisage avec [ `Path` ](xref:Xamarin.Forms.Binding.Path) défini sur le `Value` propriété et [ `Source` ](xref:Xamarin.Forms.Binding.Source) définie sur une Embedded `x:Reference` extension de balisage.

En résumé, il existe deux façons de référencer l’objet de source de liaison :

- Via le `BindingContext` propriété de la cible
- Via le `Source` propriété de la `Binding` objet lui-même

Si les deux sont spécifiés, la seconde est prioritaire. L’avantage de la `BindingContext` est qu’elle est propagée à travers l’arborescence visuelle. Il s’agit de *très* pratique si plusieurs propriétés de la cible sont liées au même objet source.

Le [ **WebViewDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo) programme illustre cette technique avec le [ `WebView` ](xref:Xamarin.Forms.WebView) élément. Deux `Button` héritent des éléments de navigation et en amont un `BindingContext` à partir de leur parent qui fait référence à la `WebView`. Le `IsEnabled` propriétés des deux boutons ont puis simple `Binding` des extensions de balisage qui ciblent le bouton `IsEnabled` propriétés basées sur les paramètres de la [ `CanGoBack` ](xref:Xamarin.Forms.WebView.CanGoBack) et [ `CanGoForward` ](xref:Xamarin.Forms.WebView.CanGoForward) propriétés en lecture seule de la `WebView`.

## <a name="the-binding-mode"></a>Le mode de liaison

Définir le [ `Mode` ](xref:Xamarin.Forms.BindingBase.Mode) propriété du `Binding` à un membre de la [ `BindingMode` ](xref:Xamarin.Forms.BindingMode) énumération :

- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay) afin que les modifications dans la propriété source affectent la cible
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource) afin que les modifications dans la propriété cible affecte la source
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) afin que les modifications dans la source et la cible affecte les uns des autres
- [`Default`](xref:Xamarin.Forms.BindingMode.Default) Pour utiliser le [ `DefaultBindingMode` ](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) spécifié lorsque la cible `BindableProperty` a été créé. Si aucun n’a été spécifié, la valeur par défaut est `OneWay` pour les propriétés pouvant être liées normales, et `OneWayToSource` pour les propriétés pouvant être liées en lecture seule.

> [!NOTE]
> Le `BindingMode` énumération maintenant inclut également `OnTime` pour appliquer une liaison uniquement lorsque le contexte de liaison change et non lorsque la propriété source change.

Les propriétés qui sont susceptibles d’être des cibles de liaisons de données dans les scénarios MVVM en général ont un `DefaultBindingMode` de `TwoWay`. Ces équivalents sont :

- `Value` propriété de `Slider` et `Stepper`
- `IsToggled` propriété de `Switch`
- `Text` propriété de `Entry`, `Editor`, et `SearchBar`
- `Date` propriété de `DatePicker`
- `Time` propriété de `TimePicker`

Le [ **BindingModes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes) exemple montre les modes de quatre liaison avec une liaison de données où la cible est le `FontSize` propriété d’un `Label` et la source est le `Value` propriété d’un `Slider`. Cela permet à chaque `Slider` pour contrôler la taille de police correspondantes `Label`. Mais le `Slider` éléments ne sont pas initialisés, car le `DefaultBindingMode` de la `FontSize` propriété est `OneWay`.

Le [ **ReverseBinding** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding) exemple définit les liaisons sur le `Value` propriété de la `Slider` faisant référence à la `FontSize` propriété de chaque `Label`. Cela semble être descendante, mais il fonctionne mieux dans l’initialisation le `Slider` éléments, car le `Value` propriété de la `Slider` a un `DefaultBindingMode` de `TwoWay`.

[![Capture d’écran triple d’inverser la liaison](images/ch16fg06-small.png "inverse liaison")](images/ch16fg06-large.png#lightbox "inverse de liaison")

Ce comportement est analogue à la façon dont les liaisons sont définies dans MVVM, et vous utiliserez ce type de liaison fréquemment.

## <a name="string-formatting"></a>Chaîne mise en forme

Lorsque la propriété cible est de type `string`, vous pouvez utiliser la [ `StringFormat` ](xref:Xamarin.Forms.BindingBase.StringFormat) propriété définie par `BindingBase` pour convertir la source en un `string`. Définir le `StringFormat` propriété à une mise en forme de chaîne que vous utiliseriez avec la méthode statique de .NET [ `String.Format` ](xref:System.String.Format(System.String,System.Object)) format pour afficher l’objet. Lorsque vous utilisez cette chaîne de mise en forme dans une extension de balisage, placez-la entre guillemets simples afin des accolades ne sont pas être confondue avec une extension de balisage incorporé.

Le [ **ShowViewValues** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues) exemple montre comment utiliser `StringFormat` dans XAML.

Le [ **WhatSizeBindings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings) exemple illustre l’affichage de la taille de la page avec des liaisons à le `Width` et `Height` propriétés de la `ContentPage`.

## <a name="why-is-it-called-path"></a>Pourquoi elle est appelée « Path » ?

Le [ `Path` ](xref:Xamarin.Forms.Binding.Path) propriété du `Binding` est ce qu’on appelle, car elle peut être une série de propriétés et indexeurs séparés par des points. Le [ **BindingPathDemos** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos) montre plusieurs exemples.

## <a name="binding-value-converters"></a>Convertisseurs de valeur de liaison

Lorsque les propriétés source et cible d’une liaison de types différents, vous pouvez convertir entre les types à l’aide d’un convertisseur de liaison. Il s’agit d’une classe qui implémente le [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter) interface et contient deux méthodes : [ `Convert` ](xref:Xamarin.Forms.IValueConverter.Convert(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) pour convertir la source à la cible, et [ `ConvertBack` ](xref:Xamarin.Forms.IValueConverter.ConvertBack(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) Pour convertir la cible à la source.

Le [ `IntToBoolConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs) classe dans le [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) bibliothèque est un exemple de conversion d’un `int` à un `bool`. Est illustrée par la [ **ButtonEnabler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler) exemple, ce qui permet uniquement la `Button` si au moins un caractère a été tapé dans un `Entry`.

Le [ `BoolToStringConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs) classe convertit un `bool` à un `string` et définit deux propriétés pour spécifier que le texte doit être retourné pour `false` et `true` valeurs.
Le [ `BoolToColorConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs) est similaire. Le [ **SwitchText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText) exemple illustre l’utilisation de ces deux convertisseurs pour afficher des textes différents dans différentes couleurs selon un `Switch` paramètre.

Générique [ `BoolToObjectConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs) peut remplacer le `BoolToStringConverter` et `BoolToColorConverter` et agissent comme généralisée `bool`-à-convertisseur d’objet de tout type.

## <a name="bindings-and-custom-views"></a>Liaisons et des vues personnalisées

Vous pouvez simplifier les contrôles personnalisés à l’aide de liaisons de données. Le [ `NewCheckBox.cs` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs) fichier de code définit `Text`, `TextColor`, `FontSize`, `FontAttributes`, et `IsChecked` propriétés, mais n’a aucune logique pour les éléments visuels du contrôle.
À la place la [ `NewCheckBox.cs.xaml` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml) fichier contient tout le balisage pour les visuels du contrôle par le biais des liaisons de données sur le `Label` les éléments basés sur les propriétés définies dans le fichier code-behind.

Le [ **NewCheckBoxDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo) exemple illustre la `NewCheckBox` contrôle personnalisé.

## <a name="related-links"></a>Liens connexes

- [Chapitre 16 de texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [Exemples de chapitre 16](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
- [Liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md)
