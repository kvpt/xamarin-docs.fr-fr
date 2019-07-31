---
title: Extensions de balisage XAML
description: L’article explique comment utiliser les extensions de balisage XAML de Xamarin.Forms pour étendre la puissance et la flexibilité de XAML en autorisant les attributs de l’élément à définir à partir de sources autres que des chaînes de texte littéral.
ms.prod: xamarin
ms.assetid: EB06C8B7-3FD5-47B7-A09C-A13063BD110F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 3b135f916225f896680cfb73175ba0568928be06
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655757"
---
# <a name="xaml-markup-extensions"></a>Extensions de balisage XAML

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

Extensions de balisage XAML vous aider à étendre la puissance et la flexibilité de XAML en autorisant les attributs de l’élément à définir à partir de sources autres que des chaînes de texte littéral.

Par exemple, normalement vous définissez le `Color` propriété de `BoxView` comme suit :

```xaml
<BoxView Color="Blue" />
```

Ou bien, vous pouvez la définir sur une valeur de couleur RVB hexadécimale :

```xaml
<BoxView Color="#FF0080" />
```

Dans les deux cas, la chaîne de texte la valeur est la `Color` attribut est converti en un `Color` valeur par le [ `ColorTypeConverter` ](xref:Xamarin.Forms.ColorTypeConverter) classe.

Vous préférerez peut-être à la place définir le `Color` attribut à partir d’une valeur stockée dans un dictionnaire de ressources, ou à partir de la valeur d’une propriété statique d’une classe que vous avez créée ou à partir d’une propriété de type `Color` d’un autre élément dans la page, ou il est construit à partir de Séparez les valeurs de teinte, saturation et de luminosité.

Toutes ces options sont possibles à l’aide des extensions de balisage XAML. Mais ne laissez pas l’expression «extensions de balisage» vous effrayant: Les extensions de balisage XAML ne sont *pas* des extensions à XML. Même avec les extensions de balisage XAML, XAML est toujours XML juridique.

Une extension de balisage est simplement un moyen différent pour exprimer un attribut d’un élément. Extensions de balisage XAML sont généralement identifiables par un paramètre d’attribut qui est placé entre accolades :

```xaml
<BoxView Color="{StaticResource themeColor}" />
```

N’importe quel paramètre d’attribut entre accolades est *toujours* une extension de balisage XAML. Toutefois, comme vous le verrez, les extensions de balisage XAML peuvent également être référencées sans l’utilisation d’accolades.

Cet article est divisé en deux parties :

## <a name="consuming-xaml-markup-extensionsconsumingmd"></a>[Consommation des extensions de balisage XAML](consuming.md)  

Utilisez les extensions de balisage XAML définies dans Xamarin.Forms.

## <a name="creating-xaml-markup-extensionscreatingmd"></a>[Création d’extensions de balisage XAML](creating.md)

Écrire vos propres extensions de balisage XAML personnalisées.



## <a name="related-links"></a>Liens associés

- [Extensions de balisage (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Chapitre d’extensions de balisage XAML de Xamarin.Forms livre](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Dictionnaires de ressources](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Styles dynamiques](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md)
