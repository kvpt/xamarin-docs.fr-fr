---
title: Introduction aux modèles de contrôle Xamarin.Forms
description: Les modèles de contrôle Xamarin.Forms permettent de créer et changer facilement les thèmes des pages d’application au moment de l’exécution. Cet article présente les modèles de contrôle.
ms.prod: xamarin
ms.assetid: 8B8E2360-6531-44A3-A7C8-9A8808DE9B86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 70646999154297592137c6966626b318fb73897c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771270"
---
# <a name="introduction-to-xamarinforms-control-templates"></a>Introduction aux modèles de contrôle Xamarin.Forms

_Les modèles de contrôle Xamarin.Forms permettent de créer et changer facilement les thèmes des pages d’application au moment de l’exécution. Cet article présente les modèles de contrôle._

Les contrôles ont des propriétés différentes, telles que `BackgroundColor` et `TextColor`, qui peuvent définir différents aspects de l’apparence du contrôle. Ces propriétés peuvent être définies à l’aide de [styles](~/xamarin-forms/user-interface/styles/index.md), que vous pouvez changer au moment de l’exécution pour implémenter des thèmes de base. Toutefois, les styles ne maintiennent pas une séparation nette entre l’apparence d’une page et son contenu, et les changements qui peuvent être effectués en définissant ces propriétés sont limités.

Les modèles de contrôle fournissent une séparation nette entre l’apparence d’une page et son contenu, et permettent ainsi de créer des pages auxquelles il est facile d’affecter des thèmes. Par exemple, une application peut contenir des modèles de contrôle au niveau de l’application qui fournissent un thème sombre et un thème clair. Vous pouvez affecter un thème à chaque [`ContentPage`](xref:Xamarin.Forms.ContentPage) de l’application en appliquant l’un des modèles de contrôle sans modifier le contenu affiché par chaque page. En outre, les thèmes fournis par les modèles de contrôle ne se limitent pas à la modification des propriétés des contrôles. Ils peuvent également modifier les contrôles utilisés pour implémenter le thème.

## <a name="creating-a-controltemplate"></a>Création d’un ControlTemplate

Un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) spécifie l’apparence d’une page ou d’une vue et contient une disposition racine et, dans la disposition, les contrôles qui implémentent le modèle. En règle générale, un `ControlTemplate` utilise un [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) pour marquer où apparaît le contenu devant être affiché par la page ou la vue. La page ou la vue qui consomme le `ControlTemplate` définit ensuite le contenu devant être affiché par le `ContentPresenter`. Le diagramme suivant illustre un `ControlTemplate` pour une page qui contient plusieurs contrôles, notamment un `ContentPresenter` marqué par un rectangle bleu :

![](introduction-images/control-template.png "Modèle de contrôle pour une page")

Vous pouvez appliquer un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) aux types suivants en définissant leurs propriétés `ControlTemplate` :

- [`ContentPage`](xref:Xamarin.Forms.ContentPage)
- [`ContentView`](xref:Xamarin.Forms.ContentView)
- [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)
- [`TemplatedView`](xref:Xamarin.Forms.TemplatedView)

Quand un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) est créé et affecté à ces types, toute apparence existante est remplacée par l’apparence définie dans le `ControlTemplate`. Vous pouvez non seulement définir l’apparence à l’aide de la propriété `ControlTemplate`, mais également appliquer des modèles de contrôle à l’aide de styles afin d’accroître encore davantage la capacité de thème.

> [!NOTE]
> *Présentation des types `TemplatedPage` et `TemplatedView`* `TemplatedPage` est la classe de base pour `ContentPage`. Il s’agit du type de page le plus basique fourni par Xamarin.Forms. Contrairement à `ContentPage`, `TemplatedPage` n’a pas de propriété `Content`. Ainsi, le contenu ne peut pas être ajouté directement à une instance de `TemplatedPage`. Au lieu de cela, il est ajouté en définissant le modèle de contrôle pour l’instance de `TemplatedPage`. De même, `TemplatedView` est la classe de base pour `ContentView`. Contrairement à `ContentView`, `TemplatedView` n’a pas de propriété `Content`. Ainsi, le contenu ne peut pas être ajouté directement à une instance de `TemplatedView`. Au lieu de cela, il est ajouté en définissant le modèle de contrôle pour l’instance de `TemplatedView`.

Les modèles de contrôle peuvent être créés en XAML et en C# :

- Les modèles de contrôle créés en XAML sont définis dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) qui est affecté à la collection [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) d’une page, ou plus généralement à la collection [`Resources`](xref:Xamarin.Forms.Application.Resources) de l’application.
- Les modèles de contrôle créés en C# sont généralement définis dans la classe de la page, ou dans une classe qui est accessible globalement.

Le choix de l’emplacement de définition d’une instance de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) a un impact sur l’endroit où elle peut être utilisée :

- Les instances de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) définies au niveau de la page peuvent être appliquées uniquement à la page.
- Les instances de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) définies au niveau de l’application peuvent être appliquées à toutes les pages de l’application.

Les modèles de contrôle qui se trouvent plus bas dans la hiérarchie d’affichage sont prioritaires sur ceux définis plus haut. Par exemple, un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) nommé `DarkTheme` qui est défini au niveau de la page est prioritaire sur un modèle portant le même nom défini au niveau de l’application. Ainsi, un modèle de contrôle qui définit un thème à appliquer à chaque page d’une application doit être défini au niveau de l’application.

## <a name="related-links"></a>Liens associés

- [Styles](~/xamarin-forms/user-interface/styles/index.md)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)
