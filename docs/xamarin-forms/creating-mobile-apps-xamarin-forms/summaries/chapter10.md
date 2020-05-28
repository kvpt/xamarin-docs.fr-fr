---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 10. XAML markup extensions''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8f23034df684e778677e4f2e480e1c41807536fb
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136810"
---
# <a name="summary-of-chapter-10-xaml-markup-extensions"></a>Résumé du chapitre 10. Extensions de balisage XAML

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)

Normalement, l’analyseur XAML convertit n’importe quelle chaîne définie en tant que valeur d’attribut en type de la propriété selon les conversions standard pour les types de données .NET de base, ou un [`TypeConverter`](xref:Xamarin.Forms.TypeConverter) dérivé attaché à la propriété ou son type avec un [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute) .

Toutefois, il est parfois commode de définir un attribut à partir d’une source différente, par exemple, un élément dans un dictionnaire, ou la valeur d’une propriété ou d’un champ statique, ou à partir d’un calcul d’un certain type.

Il s’agit du travail d’une *extension de balisage XAML*. Malgré le nom, les extensions de balisage XAML ne sont *pas* une extension de XML. XAML est toujours un XML légal.

## <a name="the-code-infrastructure"></a>Infrastructure du code

Une extension de balisage XAML est une classe qui implémente l' [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) interface. Une telle classe contient souvent le mot `Extension` à la fin de son nom, mais apparaît généralement en XAML sans ce suffixe.

Les extensions de balisage XAML suivantes sont prises en charge par toutes les implémentations de XAML :

- `x:Static`pris en charge par[`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension)
- `x:Reference`pris en charge par[`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)
- `x:Type`pris en charge par[`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension)
- `x:Null`pris en charge par[`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension)
- `x:Array`pris en charge par[`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension)

Ces quatre extensions de balisage XAML sont prises en charge par de nombreuses implémentations de XAML, notamment Xamarin.Forms :

- `StaticResource`pris en charge par[`StaticResourceExtension`](xref:Xamarin.Forms.Xaml.StaticResourceExtension)
- `DynamicResource`pris en charge par[`DynamicResourceExtension`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)
- `Binding`pris en charge par [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension) &mdash; décrit dans le [chapitre 16. Liaison de données](chapter16.md)
- `TemplateBinding`pris en charge par [`TemplateBindingExtension`](xref:Xamarin.Forms.Xaml.TemplateBindingExtension) &mdash; non couvert dans le livre

Une extension de balisage XAML supplémentaire est incluse dans dans le cadre de la Xamarin.Forms connexion à [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) :

- [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)&mdash;non couverts dans le livre

## <a name="accessing-static-members"></a>Accès aux membres statiques

Utilisez l' [`x:Static`](xref:Xamarin.Forms.Xaml.StaticExtension) élément pour définir un attribut sur la valeur d’une propriété statique publique, d’un champ ou d’un membre d’énumération. Définissez la [`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member) propriété sur le membre statique. Il est généralement plus facile de spécifier `x:Static` et le nom de membre entre accolades. Il n’est pas nécessaire d’inclure le nom de la `Member` propriété, mais uniquement le membre lui-même. Cette syntaxe courante est présentée dans l’exemple [**SharedStatics**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SharedStatics) . Les champs statiques sont eux-mêmes définis dans la [`AppConstants`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter10/SharedStatics/SharedStatics/SharedStatics/AppConstants.cs) classe. Cette technique vous permet d’établir des constantes utilisées par le biais d’un programme.

Avec une déclaration d’espace de noms XML supplémentaire, vous pouvez référencer des propriétés statiques publiques, des champs ou des membres d’énumération définis dans le .NET Framework, comme illustré dans l’exemple [**SystemStatics**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SystemStatics) .

## <a name="resource-dictionaries"></a>Dictionnaires de ressources

La `VisualElement` classe définit une propriété nommée [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) que vous pouvez affecter à un objet de type [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . Dans XAML, vous pouvez stocker des éléments dans ce dictionnaire et les identifier avec l' `x:Key` attribut. Les éléments stockés dans le dictionnaire de ressources sont partagés entre toutes les références à l’élément.

### <a name="staticresource-for-most-purposes"></a>StaticResource dans la plupart des cas

Dans la plupart des cas, vous allez utiliser l' [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) extension de balisage pour référencer un élément du dictionnaire de ressources, comme le montre l’exemple [**ResourceSharing**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceSharing) . Vous pouvez utiliser un `StaticResourceExtension` élément ou `StaticResource` entre accolades :

[![Capture d’écran triple du partage des ressources](images/ch10fg03-small.png "Partage de ressources")](images/ch10fg03-large.png#lightbox "Partage de ressources")

Ne confondez pas l' `x:Static` extension de balisage et l' `StaticResource` extension de balisage.

### <a name="a-tree-of-dictionaries"></a>Une arborescence de dictionnaires

Quand l’analyseur XAML rencontre un `StaticResource` , il commence à rechercher une clé correspondante dans l’arborescence d’éléments visuels, puis il recherche dans la `ResourceDictionary` classe de l’application `App` . Cela permet aux éléments d’un dictionnaire de ressources d’être plus profond dans l’arborescence d’éléments visuels de remplacer un dictionnaire de ressources plus haut dans l’arborescence d’éléments visuels. Cela est illustré dans l’exemple [**ResourceTrees**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceTrees) .

### <a name="dynamicresource-for-special-purposes"></a>DynamicResource à des fins spéciales

L' `StaticResource` extension de balisage provoque la récupération d’un élément à partir du dictionnaire lorsqu’une arborescence d’éléments visuels est générée pendant l' `InitializeComponent` appel. Une alternative à `StaticResource` est [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension) , qui maintient un lien vers la clé de dictionnaire et met à jour la cible lorsque l’élément référencé par la clé change.

La différence entre `StaticResource` et `DynamicResource` est illustrée dans l’exemple [**DynamicVsStatic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) .

Une propriété définie par `DynamicResource` doit être sauvegardée par une propriété pouvant être liée, comme indiqué dans [le chapitre 11, infrastructure pouvant être liée](chapter11.md).

## <a name="lesser-used-markup-extensions"></a>Extensions de balisage moins utilisées

Utilisez l' [`x:Null`](xref:Xamarin.Forms.Xaml.NullExtension) extension de balisage pour affecter à une propriété `null` .

Utilisez l' [`x:Type`](xref:Xamarin.Forms.Xaml.TypeExtension) extension de balisage pour définir une propriété sur un `Type` objet .net.

Utilisez [`x:Array`](xref:Xamarin.Forms.Xaml.ArrayExtension) pour définir un tableau. Spécifiez le type des membres du tableau en affectant `Type` à la propriété [] une `x:Type` extension de balisage.

## <a name="a-custom-markup-extension"></a>Une extension de balisage personnalisée

Vous pouvez créer vos propres extensions de balisage XAML en écrivant une classe qui implémente l' [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) interface à l’aide d’une [`ProvideValue`](xref:Xamarin.Forms.Xaml.IMarkupExtension.ProvideValue(System.IServiceProvider)) méthode.

La [`HslColorExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/HslColorExtension.cs) classe satisfait ces exigences. Elle crée une valeur de type `Color` en fonction des valeurs des propriétés nommées `H` ,, `S` `L` et `A` . Cette classe est le premier élément d’une Xamarin.Forms bibliothèque nommée [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) , qui est créé et utilisé au cours de ce document.

L’exemple [**CustomExtensionDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/CustomExtensionDemo) montre comment référencer cette bibliothèque et utiliser l’extension de balisage personnalisée.

## <a name="related-links"></a>Liens connexes

- [Chapitre 10 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf)
- [Exemples du chapitre 10](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)
- [Extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
