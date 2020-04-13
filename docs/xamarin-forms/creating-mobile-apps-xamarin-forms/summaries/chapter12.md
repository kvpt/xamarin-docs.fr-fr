---
title: Résumé du chapitre 12. Styles
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 12. Styles'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3EAE6BDC-8EFB-464B-A87B-1C35B8387BB3
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 408f171a3c7c690b700f7be21a3dcaff503467d9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "65926911"
---
# <a name="summary-of-chapter-12-styles"></a>Résumé du chapitre 12. Styles

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)

Dans Xamarin.Forms, les styles permettent à plusieurs vues de partager une collection de paramètres de propriété. Cela réduit la balisage et permet de maintenir des thèmes visuels cohérents.

Les styles sont presque toujours définis et consommés en marge. Un objet [`Style`](xref:Xamarin.Forms.Style) de type est instantané dans un dictionnaire [`Style`](xref:Xamarin.Forms.NavigableElement.Style) de ressources, puis `StaticResource` `DynamicResource` réglé à la propriété d’un élément visuel à l’aide d’une extension ou d’une extension de balisage.

## <a name="the-basic-style"></a>Le style de base

A `Style` exige [`TargetType`](xref:Xamarin.Forms.Style.TargetType) que son être réglé au type de l’objet visuel qu’il s’applique à. Lorsqu’un `Style` est instantané dans un dictionnaire de ressources (comme c’est courant), il nécessite également un `x:Key` attribut.

Le `Style` a une propriété [`Setters`](xref:Xamarin.Forms.Style.Setters)de contenu de [`Setter`](xref:Xamarin.Forms.Setter) type , qui est une collection d’objets. Chacun `Setter` associe [`Property`](xref:Xamarin.Forms.Setter.Property) a [`Value`](xref:Xamarin.Forms.Setter.Value)avec un .

Dans XAML `Property` le cadre est le nom d’une propriété CLR (comme la `Text` propriété de `Button`) mais la propriété de style doit être soutenu par une propriété liant. De plus, la propriété doit être `TargetType` définie dans la classe indiquée par le paramètre ou héritée par cette classe.

Vous pouvez `Value` spécifier `<Setter.Value>`le paramètre à l’aide de l’élément propriété . Cela vous permet `Value` de régler un objet qui ne peut pas `OnPlatform` être exprimé dans une chaîne `x:Arguments` de `x:FactoryMethod`texte, ou à un objet, ou à un objet instantané à l’aide ou . La `Value` propriété peut également `StaticResource` être définie avec une expression à un autre élément dans le dictionnaire.

Le programme [**BasicStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyle) démontre la syntaxe de `Style` base `StaticResource` et montre comment faire référence à l’extension de balisage :

[![Triple capture d’écran du style de base](images/ch12fg01-small.png "Styles de base")](images/ch12fg01-large.png#lightbox "Styles de base")

`Style` L’objet et tout `Style` objet créé `Value` dans l’objet comme `Style`un paramètre sont partagés entre tous les points de vue faisant référence à celui . Le `Style` ne peut contenir quoi que `View` ce soit qui ne peut pas être partagé, comme un dérivé.

Les gestionnaires d’événements `Style`ne peuvent pas être placés dans un . La `GestureRecognizers` propriété ne peut `Style` pas être placée dans un parce qu’elle n’est pas soutenue par une propriété liant.

## <a name="styles-in-code"></a>Styles dans le code

Bien qu’il ne soit pas commun, `Style` vous pouvez instantanéiser et initialiser les objets dans le code. Ceci est démontré par l’échantillon [**BasicStyleCode.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyleCode)

## <a name="style-inheritance"></a>Héritage de style

`Style`a [`BasedOn`](xref:Xamarin.Forms.Style.BasedOn) une propriété que vous `StaticResource` pouvez définir à une extension de balisage faisant référence à un autre style. Cela permet aux styles d’hériter des styles précédents, et d’ajouter ou de remplacer les paramètres de propriété. [**L’échantillon StyleInheritance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleInheritance) le démontre.

Si `Style2` est `Style1`basé `TargetType` sur `Style2` , le `Style1` de doit `Style1`être le même que ou dérivé de . Le dictionnaire des `Style1` ressources dans lequel est `Style2` stocké doit être le même dictionnaire de ressources que ou un dictionnaire de ressources plus élevé dans l’arbre visuel.

## <a name="implicit-styles"></a>Styles implicites

Si `Style` un dans un dictionnaire `x:Key` de ressources n’a pas de paramètre d’attribut, il est attribué automatiquement une clé de dictionnaire, et l’objet `Style` devient un style *implicite.* Une vue `Style` sans réglage et `TargetType` dont le type correspond exactement à ce style, comme le démontre l’échantillon [**ImplicitStyle.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/ImplicitStyle)

Un style implicite peut `Style` dériver d’un avec un `x:Key` réglage, mais pas l’inverse. Vous ne pouvez pas explicitement référencer un style implicite.

Vous pouvez implémenter trois `BasedOn`types de hiérarchie avec des styles et :

- Des styles définis `Page` sur le et vers le `Application` bas aux styles définis sur des dispositions plus basses dans l’arbre visuel.
- Des styles définis pour `VisualElement` `View` les classes de base telles que et à des styles définis pour des classes spécifiques.
- Des styles avec des touches de dictionnaire explicites aux styles implicites.

Ces hiérarchies sont démontrées dans l’échantillon [**styleHierarchy.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleHierarchy)

## <a name="dynamic-styles"></a>Styles dynamiques

Un style dans un dictionnaire de `DynamicResource` ressources `StaticResource`peut être référencé par plutôt que . Cela fait du style un *style dynamique.* Si ce style est remplacé dans le dictionnaire des ressources par un `DynamicResource` autre style avec la même clé, les vues faisant référence à ce style avec changer automatiquement. En outre, l’absence d’une entrée `StaticResource` de dictionnaire avec `DynamicResource`la clé spécifiée entraînera de soulever une exception, mais pas .

Vous pouvez utiliser cette technique pour changer dynamiquement de style ou de thèmes comme le démontre l’échantillon [**DynamicStyles.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynamicStyles)

Cependant, vous ne `BasedOn` pouvez `DynamicResource` pas définir `BasedOn` la propriété à une extension de maquillage parce qu’elle n’est pas soutenue par une propriété liantable. Pour dériver un style dynamiquement, ne pas définir `BasedOn`. Au lieu [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) de cela, définissez la propriété à la clé de dictionnaire du modèle que vous voulez dériver de. [**L’échantillon DynamicStylesInheritance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynaStylesInh) démontre cette technique.

## <a name="device-styles"></a>Styles d’appareils

La [`Device.Styles`](xref:Xamarin.Forms.Device.Styles) classe imbriquée définit douze champs statiques `TargetType` `Label` de lecture seulement pour six styles avec un de ce que vous pouvez utiliser pour les types communs d’utilisations de texte.

Six de ces champs `Style` sont de type `Style` que vous pouvez définir directement à une propriété dans le code:

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)

Les six autres champs `string` sont de type et peuvent être utilisés comme clés de dictionnaire pour les styles dynamiques:

- [`BodyStyleKey`](xref:Xamarin.Forms.Device.Styles.BodyStyleKey)égal à "BodyStyle"
- [`TitleStyleKey`](xref:Xamarin.Forms.Device.Styles.TitleStyleKey)égal à "TitleStyle"
- [`SubtitleStyleKey`](xref:Xamarin.Forms.Device.Styles.SubtitleStyleKey)égal à "SubtitleStyle"
- [`CaptionStyleKey`](xref:Xamarin.Forms.Device.Styles.CaptionStyleKey)égal à "CaptionStyle"
- [`ListItemTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyleKey)égal à "ListItemTextStyle"
- [`ListItemDetailTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyleKey)égal à "ListItemDetailTextStyle"

Ces styles sont illustrés par l’échantillon [**DeviceStylesList.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DeviceStylesList)

## <a name="related-links"></a>Liens connexes

- [Chapitre 12 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch12-Apr2016.pdf)
- [Échantillons du chapitre 12](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)
- [Styles](~/xamarin-forms/user-interface/styles/index.md)
