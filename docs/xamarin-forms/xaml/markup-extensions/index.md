---
title: Extensions de balisage XAML
description: L’article explique comment utiliser les Xamarin.Forms extensions de balisage XAML pour étendre la puissance et la flexibilité du XAML en permettant de définir des attributs d’élément à partir de sources autres que des chaînes de texte littéral.
ms.prod: xamarin
ms.assetid: EB06C8B7-3FD5-47B7-A09C-A13063BD110F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 98eb35697aee9022a837a7b0b531edb0c53b2239
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562143"
---
# <a name="xaml-markup-extensions"></a>Extensions de balisage XAML

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

Les extensions de balisage XAML permettent d’étendre la puissance et la flexibilité du code XAML en permettant de définir des attributs d’élément à partir de sources autres que des chaînes de texte littéral.

Par exemple, normalement, vous définissez la `Color` propriété de la `BoxView` façon suivante :

```xaml
<BoxView Color="Blue" />
```

Vous pouvez le définir sur une valeur de couleur RVB hexadécimale :

```xaml
<BoxView Color="#FF0080" />
```

Dans les deux cas, la chaîne de texte définie sur l' `Color` attribut est convertie en `Color` valeur par la [`ColorTypeConverter`](xref:Xamarin.Forms.ColorTypeConverter) classe.

Vous préférerez peut-être plutôt définir l' `Color` attribut à partir d’une valeur stockée dans un dictionnaire de ressources, ou à partir de la valeur d’une propriété statique d’une classe que vous avez créée, ou d’une propriété de type `Color` d’un autre élément de la page, ou construite à partir de valeurs de teinte, de saturation et de luminosité distinctes.

Toutes ces options sont possibles à l’aide d’extensions de balisage XAML. Mais ne laissez pas l’expression « extensions de balisage » vous effrayer : les extensions de balisage XAML ne sont *pas* des extensions à XML. Même avec les extensions de balisage XAML, XAML est toujours un XML légal.

Une extension de balisage est simplement une autre façon d’exprimer un attribut d’un élément. Les extensions de balisage XAML sont généralement identifiables par un paramètre d’attribut qui est placé entre accolades :

```xaml
<BoxView Color="{StaticResource themeColor}" />
```

Tout paramètre d’attribut entre accolades est *toujours* une extension de balisage XAML. Toutefois, comme vous le verrez, les extensions de balisage XAML peuvent également être référencées sans l’utilisation d’accolades.

Cet article est divisé en deux parties :

## <a name="consuming-xaml-markup-extensions"></a>[Consommation des extensions de balisage XAML](consuming.md)  

Utilisez les extensions de balisage XAML définies dans Xamarin.Forms .

## <a name="creating-xaml-markup-extensions"></a>[Création d’extensions de balisage XAML](creating.md)

Écrivez vos propres extensions de balisage XAML personnalisées.

## <a name="related-links"></a>Liens associés

- [Extensions de balisage (exemple)](/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Chapitre sur les extensions de balisage XAML du Xamarin.Forms livre](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Dictionnaires de ressources](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Styles dynamiques](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md)