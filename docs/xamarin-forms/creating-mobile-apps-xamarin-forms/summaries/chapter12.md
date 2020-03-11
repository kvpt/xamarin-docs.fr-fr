---
title: Résumé du chapitre 12. cellule
description: 'Création d’applications mobiles avec Xamarin.Forms : résumé du chapitre 12. cellule'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3EAE6BDC-8EFB-464B-A87B-1C35B8387BB3
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 408f171a3c7c690b700f7be21a3dcaff503467d9
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "65926911"
---
# <a name="summary-of-chapter-12-styles"></a>Résumé du chapitre 12. cellule

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)

Dans Xamarin.Forms, les styles permettent plusieurs vues partager une collection de paramètres de propriété. Cela réduit le balisage et que vous permet de maintenir cohérentes thèmes visuels.

Les styles sont presque toujours définis et utilisés dans le balisage. Un objet de type [`Style`](xref:Xamarin.Forms.Style) est instancié dans un dictionnaire de ressources, puis défini sur la propriété [`Style`](xref:Xamarin.Forms.NavigableElement.Style) d’un élément visuel à l’aide d’une extension de balisage `StaticResource` ou `DynamicResource`.

## <a name="the-basic-style"></a>Le Style de base

Un `Style` nécessite que son [`TargetType`](xref:Xamarin.Forms.Style.TargetType) soit défini sur le type de l’objet visuel auquel il s’applique. Lorsqu’un `Style` est instancié dans un dictionnaire de ressources (comme c’est le cas), il requiert également un attribut `x:Key`.

La `Style` a une propriété de contenu de type [`Setters`](xref:Xamarin.Forms.Style.Setters), qui est une collection d’objets [`Setter`](xref:Xamarin.Forms.Setter) . Chaque `Setter` associe un [`Property`](xref:Xamarin.Forms.Setter.Property) à un [`Value`](xref:Xamarin.Forms.Setter.Value).

En XAML, le paramètre `Property` est le nom d’une propriété CLR (telle que la propriété `Text` de `Button`), mais la propriété de style doit être sauvegardée par une propriété pouvant être liée. En outre, la propriété doit être définie dans la classe indiquée par le paramètre `TargetType`, ou héritée par cette classe.

Vous pouvez spécifier le paramètre `Value` à l’aide de l’élément de propriété `<Setter.Value>`. Cela vous permet de définir des `Value` à un objet qui ne peut pas être exprimé dans une chaîne de texte, ou à un objet `OnPlatform`, ou à un objet instancié à l’aide d' `x:Arguments` ou `x:FactoryMethod`. La propriété `Value` peut également être définie avec une expression `StaticResource` à un autre élément dans le dictionnaire.

Le programme [**BasicStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyle) illustre la syntaxe de base et montre comment référencer le `Style` avec une extension de balisage `StaticResource` :

[![Capture d’écran triple du style de base](images/ch12fg01-small.png "Styles de base")](images/ch12fg01-large.png#lightbox "Styles de base")

L’objet `Style` et tout objet créé dans l’objet `Style` en tant que paramètre `Value` sont partagés entre toutes les vues qui font référence à ce `Style`. La `Style` ne peut pas contenir tout ce qui ne peut pas être partagé, tel qu’un `View` dérivé.

Les gestionnaires d’événements ne peuvent pas être définis dans un `Style`. Impossible de définir la propriété `GestureRecognizers` dans un `Style`, car elle n’est pas sauvegardée par une propriété pouvant être liée.

## <a name="styles-in-code"></a>Styles de code

Bien qu’il ne soit pas courant, vous pouvez instancier et initialiser des objets `Style` dans le code. Cela est illustré par l’exemple [**BasicStyleCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/BasicStyleCode) .

## <a name="style-inheritance"></a>Héritage de style

`Style` a une propriété [`BasedOn`](xref:Xamarin.Forms.Style.BasedOn) que vous pouvez définir sur une extension de balisage `StaticResource` référençant un autre style. Ainsi, les styles hériter des styles précédentes et ajouter ou remplacer les paramètres de propriété. L’exemple [**StyleInheritance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleInheritance) illustre cela.

Si `Style2` est basé sur `Style1`, le `TargetType` de `Style2` doit être identique à `Style1` ou dérivé de `Style1`. Le dictionnaire de ressources dans lequel `Style1` est stocké doit être le même dictionnaire de ressources que `Style2` ou un dictionnaire de ressources supérieur dans l’arborescence d’éléments visuels.

## <a name="implicit-styles"></a>Styles implicites

Si un `Style` dans un dictionnaire de ressources n’a pas de paramètre d’attribut `x:Key`, il reçoit automatiquement une clé de dictionnaire, et l’objet `Style` devient un *style implicite*. Une vue sans paramètre `Style` et dont le type correspond à la `TargetType` trouvera exactement ce style, comme le montre l’exemple [**ImplicitStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/ImplicitStyle) .

Un style implicite peut dériver d’un `Style` avec un paramètre `x:Key`, mais pas l’inverse inverse. Vous ne pouvez pas référencer explicitement un style implicite.

Vous pouvez implémenter trois types de hiérarchie avec des styles et des `BasedOn`:

- À partir de styles définis sur le `Application` et `Page` bas aux styles définis sur les dispositions plus bas dans l’arborescence d’éléments visuels.
- À partir de styles définis pour les classes de base telles que `VisualElement` et `View` aux styles définis pour des classes spécifiques.
- À partir de styles avec des clés de dictionnaire explicite pour les styles implicites.

Ces hiérarchies sont illustrées dans l’exemple [**StyleHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/StyleHierarchy) .

## <a name="dynamic-styles"></a>Styles dynamiques

Un style dans un dictionnaire de ressources peut être référencé par `DynamicResource` plutôt que `StaticResource`. Le style est alors un *style dynamique*. Si ce style est remplacé dans le dictionnaire de ressources par un autre style avec la même clé, les vues qui font référence à ce style avec `DynamicResource` automatiquement modifiées. En outre, l’absence d’une entrée de dictionnaire avec la clé spécifiée amène `StaticResource` à lever une exception, mais pas `DynamicResource`.

Vous pouvez utiliser cette technique pour modifier dynamiquement le style ou les thèmes comme le montre l’exemple [**DynamicStyles**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynamicStyles) .

Toutefois, vous ne pouvez pas définir la propriété `BasedOn` sur une extension de maquillage `DynamicResource`, car `BasedOn` n’est pas sauvegardée par une propriété pouvant être liée. Pour dériver un style dynamiquement, ne définissez pas `BasedOn`. Au lieu de cela, affectez à la propriété [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) la clé de dictionnaire du style à partir duquel vous souhaitez dériver. L’exemple [**DynamicStylesInheritance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DynaStylesInh) illustre cette technique.

## <a name="device-styles"></a>Styles d’appareils

La classe imbriquée [`Device.Styles`](xref:Xamarin.Forms.Device.Styles) définit douze champs statiques en lecture seule pour six styles avec une `TargetType` de `Label` que vous pouvez utiliser pour les types courants d’utilisation de texte.

Six de ces champs sont de type `Style` que vous pouvez définir directement sur une `Style` propriété dans le code :

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)

Les six autres champs sont de type `string` et peuvent être utilisés comme clés de dictionnaire pour les styles dynamiques :

- [`BodyStyleKey`](xref:Xamarin.Forms.Device.Styles.BodyStyleKey) égal à « BodyStyle »
- [`TitleStyleKey`](xref:Xamarin.Forms.Device.Styles.TitleStyleKey) égal à « TitleStyle »
- [`SubtitleStyleKey`](xref:Xamarin.Forms.Device.Styles.SubtitleStyleKey) égal à « SubtitleStyle »
- [`CaptionStyleKey`](xref:Xamarin.Forms.Device.Styles.CaptionStyleKey) égal à « CaptionStyle »
- [`ListItemTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyleKey) égal à « ListItemTextStyle »
- [`ListItemDetailTextStyleKey`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyleKey) égal à « ListItemDetailTextStyle »

Ces styles sont illustrés par l’exemple [**DeviceStylesList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12/DeviceStylesList) .

## <a name="related-links"></a>Liens connexes

- [Chapitre 12 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch12-Apr2016.pdf)
- [Chapitre 12 exemples](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter12)
- [Styles](~/xamarin-forms/user-interface/styles/index.md)
