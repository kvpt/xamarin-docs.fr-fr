---
title: Résumé du chapitre 10. Extensions de balisage XAML
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 10. Extensions de balisage XAML'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 575EAE55-BD4D-470F-A583-3D065FA102E2
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 076e9f5155492e5a69d906c587b24495fe39d3f1
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "61334330"
---
# <a name="summary-of-chapter-10-xaml-markup-extensions"></a>Résumé du chapitre 10. Extensions de balisage XAML

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)

Normalement, le parser XAML convertit n’importe quel ensemble de chaîne comme une valeur d’attribut au [`TypeConverter`](xref:Xamarin.Forms.TypeConverter) type de propriété basé sur [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute)des conversions standard pour les types de données de base .NET, ou un dérivé attaché à la propriété ou son type avec un .

Mais il est parfois pratique de définir un attribut à partir d’une autre source, par exemple, un élément dans un dictionnaire, ou la valeur d’une propriété ou d’un champ statique, ou à partir d’un calcul d’une sorte quelconque.

C’est le travail d’une *extension de balisage XAML*. Malgré le nom, les extensions de balisage XAML ne sont *pas* une extension de XML. XAML est toujours légal XML.

## <a name="the-code-infrastructure"></a>L’infrastructure de code

Une extension de balisage XAML [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) est une classe qui implémente l’interface. Une telle classe a `Extension` souvent le mot à la fin de son nom, mais apparaît généralement dans XAML sans ce suffixe.

Les extensions de balisage XAML suivantes sont prises en charge par toutes les implémentations de XAML :

- `x:Static`soutenus par[`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension)
- `x:Reference`soutenus par[`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)
- `x:Type`soutenus par[`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension)
- `x:Null`soutenus par[`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension)
- `x:Array`soutenus par[`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension)

Ces quatre extensions de balisage XAML sont soutenues par de nombreuses implémentations de XAML, y compris Xamarin.Forms:

- `StaticResource`soutenus par[`StaticResourceExtension`](xref:Xamarin.Forms.Xaml.StaticResourceExtension)
- `DynamicResource`soutenus par[`DynamicResourceExtension`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)
- `Binding`soutenu [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension) &mdash;par discuté dans [le chapitre 16. Liaison de données](chapter16.md)
- `TemplateBinding`soutenu [`TemplateBindingExtension`](xref:Xamarin.Forms.Xaml.TemplateBindingExtension) &mdash;par non couvert dans le livre

Une extension de balisage XAML supplémentaire est incluse [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)dans Xamarin.Forms dans le cadre de :

- [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)&mdash;non couvert dans le livre

## <a name="accessing-static-members"></a>Accès aux membres statiques

Utilisez [`x:Static`](xref:Xamarin.Forms.Xaml.StaticExtension) l’élément pour définir un attribut à la valeur d’un membre de la propriété, du champ ou du recensement statique public. Réglez la [`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member) propriété au membre statique. Il est généralement plus `x:Static` facile de spécifier et le nom du membre dans des accolades bouclées. Le nom `Member` de la propriété n’a pas besoin d’être inclus, juste le membre lui-même. Cette syntaxe commune est indiquée dans l’échantillon [**SharedStatics.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SharedStatics) Les champs statiques eux-mêmes sont définis dans la [`AppConstants`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter10/SharedStatics/SharedStatics/SharedStatics/AppConstants.cs) classe. Cette technique vous permet d’établir des constantes utilisées par le biais d’un programme.

Avec une déclaration supplémentaire d’espace de nom XML, vous pouvez faire référence aux propriétés statiques publiques, aux champs ou aux membres de recensement définis dans le cadre .NET, comme le démontre l’échantillon [**SystemStatics.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SystemStatics)

## <a name="resource-dictionaries"></a>Dictionnaires de ressources

La `VisualElement` classe définit une [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) propriété nommée que vous [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)pouvez définir à un objet de type . Au sein de XAML, vous pouvez stocker `x:Key` des articles dans ce dictionnaire et les identifier avec l’attribut. Les éléments stockés dans le dictionnaire des ressources sont partagés entre toutes les références à l’article.

### <a name="staticresource-for-most-purposes"></a>StaticResource pour la plupart des fins

Dans la plupart des [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) cas, vous utiliserez l’extension de balisage pour référencer un élément du dictionnaire des ressources, comme le démontre [**l’échantillon de partage de ressources.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceSharing) Vous pouvez `StaticResourceExtension` utiliser `StaticResource` un élément ou dans des accolades bouclées :

[![Triple capture d’écran du partage des ressources](images/ch10fg03-small.png "Partage de ressources")](images/ch10fg03-large.png#lightbox "Partage de ressources")

Ne confondez `x:Static` pas l’extension de balisage et l’extension de `StaticResource` balisage.

### <a name="a-tree-of-dictionaries"></a>Un arbre de dictionnaires

Lorsque le parsier XAML rencontre un `StaticResource`, il commence à chercher l’arbre visuel pour une clé assortie, puis regarde dans la classe de `ResourceDictionary` `App` l’application. Cela permet aux éléments d’un dictionnaire de ressources plus profonds dans l’arbre visuel de remplacer un dictionnaire de ressources plus haut dans l’arbre visuel. Ceci est démontré dans l’échantillon [**ResourceTrees.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceTrees)

### <a name="dynamicresource-for-special-purposes"></a>DynamicResource à des fins spéciales

L’extension `StaticResource` de balisage provoque un élément à récupérer dans `InitializeComponent` le dictionnaire lorsqu’un arbre visuel est construit pendant l’appel. Une alternative `StaticResource` [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)à est , qui maintient un lien vers la clé du dictionnaire et met à jour la cible lorsque l’élément référencé par les modifications clés.

La différence `StaticResource` `DynamicResource` entre et est démontrée dans l’échantillon [**DynamicVsStatic.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic)

Une propriété `DynamicResource` établie par doit être soutenue par une propriété liant comme discuté dans [le chapitre 11, L’infrastructure liant](chapter11.md).

## <a name="lesser-used-markup-extensions"></a>Extensions de balisage moins utilisées

Utilisez [`x:Null`](xref:Xamarin.Forms.Xaml.NullExtension) l’extension de balisage pour définir une propriété à `null`.

Utilisez [`x:Type`](xref:Xamarin.Forms.Xaml.TypeExtension) l’extension de balisage pour `Type` définir une propriété à un objet .NET.

Utiliser [`x:Array`](xref:Xamarin.Forms.Xaml.ArrayExtension) pour définir un tableau. Spécifier le type de`Type`membres du `x:Type` tableau en fixant la propriété [ ] à une extension de balisage.

## <a name="a-custom-markup-extension"></a>Une extension de balisage personnalisée

Vous pouvez créer vos propres extensions de balisage XAML en écrivant une classe qui implémente l’interface [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) avec une [`ProvideValue`](xref:Xamarin.Forms.Xaml.IMarkupExtension.ProvideValue(System.IServiceProvider)) méthode.

La [`HslColorExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/HslColorExtension.cs) classe satisfait à cette exigence. Il crée une `Color` valeur de type `H`basée `S` `L`sur `A`les valeurs des propriétés nommées , , , et . Cette classe est le premier élément d’une bibliothèque Xamarin.Forms nommée [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) qui est construit et utilisé au cours de ce livre.

[**L’échantillon CustomExtensionDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/CustomExtensionDemo) montre comment faire référence à cette bibliothèque et utiliser l’extension de balisage personnalisée.

## <a name="related-links"></a>Liens connexes

- [Chapitre 10 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf)
- [Échantillons du chapitre 10](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)
- [XAML Markup Extensions](~/xamarin-forms/xaml/markup-extensions/index.md)
