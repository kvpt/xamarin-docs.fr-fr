---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 12. Styles''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 22f7272751c30bed350fe7aada8d5dacf0acc809
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136784"
---
# <a name="summary-of-chapter-12-styles"></a>Résumé du chapitre 12. Styles

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)

Dans Xamarin.Forms , les styles permettent à plusieurs vues de partager une collection de paramètres de propriété. Cela réduit le balisage et permet de conserver des thèmes visuels cohérents.

Les styles sont presque toujours définis et utilisés dans le balisage. Un objet de type [`Style`](xref:Xamarin.Forms.Style) est instancié dans un dictionnaire de ressources, puis défini sur la [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propriété d’un élément visuel à l’aide d’une `StaticResource` `DynamicResource` extension de balisage ou.

## <a name="the-basic-style"></a>Le style de base

Un `Style` requiert que [`TargetType`](xref:Xamarin.Forms.Style.TargetType) soit défini sur le type de l’objet visuel auquel il s’applique. Lorsqu’un `Style` est instancié dans un dictionnaire de ressources (comme c’est le cas), il requiert également un `x:Key` attribut.

`Style`A une propriété de contenu de type [`Setters`](xref:Xamarin.Forms.Style.Setters) , qui est une collection d' [`Setter`](xref:Xamarin.Forms.Setter) objets. Chaque `Setter` associe un [`Property`](xref:Xamarin.Forms.Setter.Property) à un [`Value`](xref:Xamarin.Forms.Setter.Value) .

En XAML, le `Property` paramètre est le nom d’une propriété CLR (telle que la `Text` propriété de `Button` ), mais la propriété de style doit être sauvegardée par une propriété pouvant être liée. En outre, la propriété doit être définie dans la classe indiquée par le `TargetType` paramètre, ou héritée par cette classe.

Vous pouvez spécifier le `Value` paramètre à l’aide de l’élément Property `<Setter.Value>` . Cela vous permet `Value` de définir sur un objet qui ne peut pas être exprimé dans une chaîne de texte, ou sur un objet `OnPlatform` , ou sur un objet instancié à l’aide de `x:Arguments` ou de `x:FactoryMethod` . La `Value` propriété peut également être définie avec une `StaticResource` expression à un autre élément dans le dictionnaire.

Le programme [**BasicStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyle) illustre la syntaxe de base et montre comment faire référence à `Style` avec une `StaticResource` extension de balisage :

[![Capture d’écran triple du style de base](images/ch12fg01-small.png "Styles de base")](images/ch12fg01-large.png#lightbox "Styles de base")

L' `Style` objet et tout objet créé dans l' `Style` objet en tant que `Value` paramètre sont partagés entre toutes les vues qui font référence à ce `Style` . Le `Style` ne peut pas contenir d’éléments qui ne peuvent pas être partagés, tels qu’un `View` dérivé.

Les gestionnaires d’événements ne peuvent pas être définis dans un `Style` . La `GestureRecognizers` propriété ne peut pas être définie dans un `Style` , car elle n’est pas sauvegardée par une propriété pouvant être liée.

## <a name="styles-in-code"></a>Styles dans le code

Bien qu’il ne soit pas courant, vous pouvez instancier et initialiser `Style` des objets dans le code. Cela est illustré par l’exemple [**BasicStyleCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyleCode) .

## <a name="style-inheritance"></a>Héritage de style

`Style`a une [`BasedOn`](xref:Xamarin.Forms.Style.BasedOn) propriété que vous pouvez définir sur une `StaticResource` extension de balisage référençant un autre style. Cela permet aux styles d’hériter des styles précédents et d’ajouter ou de remplacer des paramètres de propriété. L’exemple [**StyleInheritance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleInheritance) illustre cela.

Si `Style2` est basé sur `Style1` , le `TargetType` de `Style2` doit être le même que `Style1` ou dérivé de `Style1` . Le dictionnaire de ressources dans lequel `Style1` est stocké doit être le même dictionnaire de ressources que `Style2` ou un dictionnaire de ressources plus haut dans l’arborescence d’éléments visuels.

## <a name="implicit-styles"></a>Styles implicites

Si un `Style` dans un dictionnaire de ressources n’a pas de `x:Key` paramètre d’attribut, il reçoit automatiquement une clé de dictionnaire, et l' `Style` objet devient un *style implicite*. Une vue sans `Style` paramètre et dont le type correspond `TargetType` exactement à ce style, comme le montre l’exemple [**ImplicitStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/ImplicitStyle) .

Un style implicite peut dériver d’un `Style` avec un `x:Key` paramètre, mais pas d’inverse. Vous ne pouvez pas explicitement référencer un style implicite.

Vous pouvez implémenter trois types de hiérarchie avec des styles et `BasedOn` :

- À partir de styles définis sur le et vers le `Application` `Page` bas pour les styles définis sur les dispositions plus bas dans l’arborescence d’éléments visuels.
- À partir de styles définis pour les classes de base telles que `VisualElement` et `View` pour les styles définis pour des classes spécifiques.
- Des styles avec des clés de dictionnaire explicites à des styles implicites.

Ces hiérarchies sont illustrées dans l’exemple [**StyleHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleHierarchy) .

## <a name="dynamic-styles"></a>Styles dynamiques

Un style dans un dictionnaire de ressources peut être référencé par `DynamicResource` plutôt que par `StaticResource` . Le style est alors un *style dynamique*. Si ce style est remplacé dans le dictionnaire de ressources par un autre style avec la même clé, les vues qui font référence à ce style sont `DynamicResource` automatiquement modifiées. En outre, l’absence d’une entrée de dictionnaire avec la clé spécifiée lève `StaticResource` une exception, mais pas `DynamicResource` .

Vous pouvez utiliser cette technique pour modifier dynamiquement le style ou les thèmes comme le montre l’exemple [**DynamicStyles**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynamicStyles) .

Toutefois, vous ne pouvez pas définir `BasedOn` une extension de maquillage pour la propriété `DynamicResource` , car elle `BasedOn` n’est pas sauvegardée par une propriété pouvant être liée. Pour dériver un style dynamiquement, ne définissez pas `BasedOn` . Au lieu de cela, affectez [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) à la propriété la clé de dictionnaire du style à partir duquel vous souhaitez dériver. L’exemple [**DynamicStylesInheritance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynaStylesInh) illustre cette technique.

## <a name="device-styles"></a>Styles de périphérique

La [`Device.Styles`](xref:Xamarin.Forms.Device.Styles) classe imbriquée définit douze champs statiques en lecture seule pour six styles avec un `TargetType` de `Label` que vous pouvez utiliser pour les types courants d’utilisation de texte.

Six de ces champs sont de type `Style` que vous pouvez définir directement sur une `Style` propriété dans le code :

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)

Les six autres champs sont de type `string` et peuvent être utilisés comme clés de dictionnaire pour les styles dynamiques :

- [`BodyStyleKey`](xref:Xamarin.Forms.Device.Styles.BodyStyleKey)égal à « BodyStyle »
- [`TitleStyleKey`](xref:Xamarin.Forms.Device.Styles.TitleStyleKey)égal à « TitleStyle »
- [`SubtitleStyleKey`](xref:Xamarin.Forms.Device.Styles.SubtitleStyleKey)égal à « SubtitleStyle »
- [`CaptionStyleKey`](xref:Xamarin.Forms.Device.Styles.CaptionStyleKey)égal à « CaptionStyle »
- [`ListItemTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyleKey)égal à « ListItemTextStyle »
- [`ListItemDetailTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyleKey)égal à « ListItemDetailTextStyle »

Ces styles sont illustrés par l’exemple [**DeviceStylesList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DeviceStylesList) .

## <a name="related-links"></a>Liens connexes

- [Chapitre 12 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch12-Apr2016.pdf)
- [Chapitre 12 exemples](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)
- [Styles](~/xamarin-forms/user-interface/styles/index.md)
