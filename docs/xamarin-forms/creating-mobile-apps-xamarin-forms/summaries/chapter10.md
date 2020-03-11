---
title: Résumé du chapitre 10. Extensions de balisage XAML
description: 'Création d’applications mobiles avec Xamarin.Forms : résumé du chapitre 10. Extensions de balisage XAML'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 575EAE55-BD4D-470F-A583-3D065FA102E2
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 076e9f5155492e5a69d906c587b24495fe39d3f1
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "61334330"
---
# <a name="summary-of-chapter-10-xaml-markup-extensions"></a>Résumé du chapitre 10. Extensions de balisage XAML

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)

Normalement, l’analyseur XAML convertit n’importe quelle chaîne définie en tant que valeur d’attribut en type de la propriété en fonction des conversions standard pour les types de données .NET de base, ou d’une [`TypeConverter`](xref:Xamarin.Forms.TypeConverter) dérivée attachée à la propriété ou à son type avec une [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute).

Mais il est parfois utile de définir un attribut à partir d’une autre source, par exemple, un élément dans un dictionnaire, ou la valeur d’une propriété statique ou d’un champ, ou d’un calcul quelconque.

Il s’agit du travail d’une *extension de balisage XAML*. Malgré le nom, les extensions de balisage XAML ne sont *pas* une extension de XML. XAML est toujours juridique XML.

## <a name="the-code-infrastructure"></a>L’infrastructure de code

Une extension de balisage XAML est une classe qui implémente l’interface [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) . Ce type de classe a souvent le mot `Extension` à la fin de son nom, mais il apparaît généralement en XAML sans ce suffixe.

Les extensions de balisage XAML suivantes sont prises en charge par toutes les implémentations de XAML :

- `x:Static` pris en charge par [`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension)
- `x:Reference` pris en charge par [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)
- `x:Type` pris en charge par [`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension)
- `x:Null` pris en charge par [`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension)
- `x:Array` pris en charge par [`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension)

Ces quatre extensions de balisage XAML sont prises en charge par de nombreuses implémentations du XAML, y compris Xamarin.Forms :

- `StaticResource` pris en charge par [`StaticResourceExtension`](xref:Xamarin.Forms.Xaml.StaticResourceExtension)
- `DynamicResource` pris en charge par [`DynamicResourceExtension`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)
- `Binding` pris en charge par [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension)&mdash;abordé dans le [chapitre 16. Liaison de données](chapter16.md)
- `TemplateBinding` pris en charge par [`TemplateBindingExtension`](xref:Xamarin.Forms.Xaml.TemplateBindingExtension)&mdash;non couverts dans le livre

Une extension de balisage XAML supplémentaire est incluse dans Xamarin. Forms dans le cadre de la connexion à [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout):

- [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)&mdash;pas traité dans le livre

## <a name="accessing-static-members"></a>L’accès aux membres statiques

Utilisez l’élément [`x:Static`](xref:Xamarin.Forms.Xaml.StaticExtension) pour affecter à un attribut la valeur d’une propriété statique publique, d’un champ ou d’un membre d’énumération. Définissez la propriété [`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member) sur le membre statique. Il est généralement plus facile de spécifier `x:Static` et le nom de membre entre accolades. Il n’est pas nécessaire d’inclure le nom de la propriété `Member`, mais uniquement le membre lui-même. Cette syntaxe courante est présentée dans l’exemple [**SharedStatics**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SharedStatics) . Les champs statiques sont eux-mêmes définis dans la classe [`AppConstants`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter10/SharedStatics/SharedStatics/SharedStatics/AppConstants.cs) . Cette technique vous permet d’établir des constantes utilisées via un programme.

Avec une déclaration d’espace de noms XML supplémentaire, vous pouvez référencer des propriétés statiques publiques, des champs ou des membres d’énumération définis dans le .NET Framework, comme illustré dans l’exemple [**SystemStatics**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SystemStatics) .

## <a name="resource-dictionaries"></a>Dictionnaires de ressources

La classe `VisualElement` définit une propriété nommée [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) que vous pouvez définir sur un objet de type [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). Dans XAML, vous pouvez stocker des éléments dans ce dictionnaire et les identifier avec l’attribut `x:Key`. Les éléments stockés dans le dictionnaire de ressources sont partagées entre toutes les références à l’élément.

### <a name="staticresource-for-most-purposes"></a>StaticResource pour la plupart des cas

Dans la plupart des cas, vous utiliserez l’extension de balisage [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) pour référencer un élément du dictionnaire de ressources, comme le montre l’exemple [**ResourceSharing**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceSharing) . Vous pouvez utiliser un élément `StaticResourceExtension` ou `StaticResource` entre accolades :

[![Capture d’écran triple du partage des ressources](images/ch10fg03-small.png "Partage des ressources")](images/ch10fg03-large.png#lightbox "Partage des ressources")

Ne confondez pas l’extension de balisage `x:Static` et l’extension de balisage `StaticResource`.

### <a name="a-tree-of-dictionaries"></a>Une arborescence de dictionnaires

Lorsque l’analyseur XAML rencontre un `StaticResource`, il commence à rechercher une clé correspondante dans l’arborescence d’éléments visuels, puis recherche dans le `ResourceDictionary` de la classe de `App` de l’application. Cela permet des éléments dans un dictionnaire de ressources plus en détail l’arborescence visuelle pour remplacer un dictionnaire de ressources plus haut dans l’arborescence visuelle. Cela est illustré dans l’exemple [**ResourceTrees**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceTrees) .

### <a name="dynamicresource-for-special-purposes"></a>DynamicResource à des fins spéciales

L’extension de balisage `StaticResource` provoque la récupération d’un élément à partir du dictionnaire lors de la génération d’une arborescence d’éléments visuels pendant l’appel de `InitializeComponent`. Une alternative à `StaticResource` est [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension), qui maintient un lien vers la clé de dictionnaire et met à jour la cible lorsque l’élément référencé par la clé change.

La différence entre `StaticResource` et `DynamicResource` est illustrée dans l’exemple [**DynamicVsStatic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) .

Une propriété définie par `DynamicResource` doit être sauvegardée par une propriété pouvant être liée, comme indiqué dans [le chapitre 11, infrastructure pouvant être liée](chapter11.md).

## <a name="lesser-used-markup-extensions"></a>Extensions de balisage moins utilisée

Utilisez l’extension de balisage [`x:Null`](xref:Xamarin.Forms.Xaml.NullExtension) pour affecter à une propriété la valeur `null`.

Utilisez l’extension de balisage [`x:Type`](xref:Xamarin.Forms.Xaml.TypeExtension) pour définir une propriété sur un objet .net `Type`.

Utilisez [`x:Array`](xref:Xamarin.Forms.Xaml.ArrayExtension) pour définir un tableau. Spécifiez le type des membres du tableau en affectant à la propriété [`Type`] une extension de balisage `x:Type`.

## <a name="a-custom-markup-extension"></a>Une extension de balisage personnalisée

Vous pouvez créer vos propres extensions de balisage XAML en écrivant une classe qui implémente l’interface [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) avec une méthode [`ProvideValue`](xref:Xamarin.Forms.Xaml.IMarkupExtension.ProvideValue(System.IServiceProvider)) .

La classe [`HslColorExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/HslColorExtension.cs) répond à ces exigences. Elle crée une valeur de type `Color` en fonction des valeurs des propriétés nommées `H`, `S`, `L`et `A`. Cette classe est le premier élément d’une bibliothèque Xamarin. Forms nommée [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) qui est créée et utilisée au cours de ce livre.

L’exemple [**CustomExtensionDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/CustomExtensionDemo) montre comment référencer cette bibliothèque et utiliser l’extension de balisage personnalisée.

## <a name="related-links"></a>Liens connexes

- [Chapitre 10 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf)
- [Exemples du chapitre 10](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)
- [Extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
