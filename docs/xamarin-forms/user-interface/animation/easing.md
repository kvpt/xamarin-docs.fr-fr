---
title: Fonctions d’accélération dans Xamarin.Forms
description: Xamarin.Forms inclut une classe d’accélération qui vous permet de spécifier une fonction de transfert qui contrôle comment les animations accélèrent ou ralentissent car ils s’exécutent. Cet article montre comment utiliser les fonctions d’accélération prédéfinies et comment créer des fonctions d’accélération personnalisées.
ms.prod: xamarin
ms.assetid: E6F124C7-A161-4C1F-AF40-52F0935E54DE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 0be72e29ebfed9a3f43d96fd66b7f4597459a834
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656767"
---
# <a name="easing-functions-in-xamarinforms"></a>Fonctions d’accélération dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)

_Xamarin.Forms inclut une classe d’accélération qui vous permet de spécifier une fonction de transfert qui contrôle comment les animations accélèrent ou ralentissent car ils s’exécutent. Cet article montre comment utiliser les fonctions d’accélération prédéfinies et comment créer des fonctions d’accélération personnalisées._


Le [ `Easing` ](xref:Xamarin.Forms.Easing) classe définit un nombre de fonctions d’accélération qui peuvent être consommées par des animations :

- Le [ `BounceIn` ](xref:Xamarin.Forms.Easing.BounceIn) fonction d’accélération rebondit l’animation au début.
- Le [ `BounceOut` ](xref:Xamarin.Forms.Easing.BounceOut) fonction d’accélération rebondit l’animation à la fin.
- Le [ `CubicIn` ](xref:Xamarin.Forms.Easing.CubicIn) fonction d’accélération lentement accélère l’animation.
- Le [ `CubicInOut` ](xref:Xamarin.Forms.Easing.CubicInOut) fonction d’accélération accélère l’animation au début et ralentit l’animation à la fin.
- Le [ `CubicOut` ](xref:Xamarin.Forms.Easing.CubicOut) fonction d’accélération rapidement ralentit l’animation.
- Le [ `Linear` ](xref:Xamarin.Forms.Easing.Linear) fonction d’accélération utilise une vitesse constante et est fonction d’accélération par défaut.
- Le [ `SinIn` ](xref:Xamarin.Forms.Easing.SinIn) fonction d’accélération sans heurts accélère l’animation.
- Le [ `SinInOut` ](xref:Xamarin.Forms.Easing.SinInOut) fonction d’accélération sans heurts accélère l’animation au début et sans heurts ralentit l’animation à la fin.
- Le [ `SinOut` ](xref:Xamarin.Forms.Easing.SinOut) fonction d’accélération sans heurts ralentit l’animation.
- Le [ `SpringIn` ](xref:Xamarin.Forms.Easing.SpringIn) fonction d’accélération entraîne l’animation accélérer très rapidement vers la fin.
- Le [ `SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut) fonction d’accélération entraîne l’animation de décélérer rapidement vers la fin.

Le `In` et `Out` suffixes indiquent si l’effet fournie par la fonction d’accélération est visible au début de l’animation, à la fin, ou les deux.

En outre, les fonctions d’accélération personnalisées peuvent être créées. Pour plus d’informations, consultez [fonctions d’accélération personnalisée](#customeasing).

## <a name="consuming-an-easing-function"></a>Utilisation d’une fonction d’accélération

Les méthodes d’extension de l’animation dans le [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe autoriser une fonction d’accélération de la définir en tant que le paramètre de la méthode finale, comme illustré dans l’exemple de code suivant :

```csharp
await image.TranslateTo(0, 200, 2000, Easing.BounceIn);
await image.ScaleTo(2, 2000, Easing.CubicIn);
await image.RotateTo(360, 2000, Easing.SinInOut);
await image.ScaleTo(1, 2000, Easing.CubicOut);
await image.TranslateTo(0, -200, 2000, Easing.BounceOut);
```

En spécifiant une fonction d’accélération pour une animation, la vitesse d’animation devient non linéaires et a pour effet fournies par la fonction d’accélération. L’omission d’une fonction d’accélération lors de la création d’une animation entraîne l’animation à utiliser la valeur par défaut [ `Linear` ](xref:Xamarin.Forms.Easing.Linear) fonction, ce qui produit une vitesse linéaire d’accélération.

Pour plus d’informations sur les méthodes d’extension de l’animation dans le [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) de classe, consultez [Animations simples](~/xamarin-forms/user-interface/animation/simple.md). Fonctions d’accélération peuvent également être utilisées par le [ `Animation` ](xref:Xamarin.Forms.Animation) classe. Pour plus d’informations, consultez [personnalisé Animations](~/xamarin-forms/user-interface/animation/custom.md).

<a name="customeasing" />

## <a name="custom-easing-functions"></a>Fonctions d’accélération de personnalisé

Il existe trois approches principales à la création d’une fonction d’accélération personnalisée :

1. Créer une méthode qui prend un `double` argument et retourne un `double` résultat.
1. Créer un `Func<double, double>`.
1. Spécifiez la fonction d’accélération comme argument à la [ `Easing` ](xref:Xamarin.Forms.Easing) constructeur.

Dans les trois cas, la fonction d’accélération personnalisée doit retourner 0 pour un argument de 0 et 1 pour un argument de 1. Toutefois, n’importe quelle valeur peut être retournée entre les valeurs d’argument de 0 et 1. Chaque approche maintenant nous aborderons tour.

### <a name="custom-easing-method"></a>Personnalisé d’accélération (méthode)

Une fonction d’accélération personnalisée peut être définie comme une méthode qui prend un `double` argument et retourne un `double` résultat, comme illustré dans l’exemple de code suivant :

```csharp
await image.TranslateTo(0, 200, 2000, CustomEase);

double CustomEase (double t)
{
  return t == 0 || t == 1 ? t : (int)(5 * t) / 5.0;
}
```

Le `CustomEase` méthode tronque la valeur entrante pour les valeurs 0, 0,2, 0,4, 0,6, 0,8 et 1. Par conséquent, le [ `Image` ](xref:Xamarin.Forms.Image) instance est traduite en sauts discrets, plutôt que sans heurts.

### <a name="custom-easing-func"></a>Custom Func d’accélération

Une fonction d’accélération personnalisée peut également être définie comme un `Func<double, double>`, comme illustré dans l’exemple de code suivant :

```csharp
Func<double, double> CustomEase = t => 9 * t * t * t - 13.5 * t * t + 5.5 * t;
await image.TranslateTo(0, 200, 2000, CustomEase));
```

Le `CustomEase` `Func` représente une fonction d’accélération qui a commencé par rapides et ralentit et inverse les cours, puis inverse de cours à nouveau afin d’accélérer rapidement vers la fin. Par conséquent, tandis que le mouvement global de la [ `Image` ](xref:Xamarin.Forms.Image) instance est vers le bas, il annule également temporairement cours milieu de l’animation.

### <a name="custom-easing-constructor"></a>Constructeur d’accélération de personnalisé

Une fonction d’accélération personnalisée peut également être définie comme l’argument à la [ `Easing` ](xref:Xamarin.Forms.Easing) constructeur, comme illustré dans l’exemple de code suivant :

```csharp
await image.TranslateTo (0, 200, 2000, new Easing (t => 1 - Math.Cos (10 * Math.PI * t) * Math.Exp (-5 * t)));
```

La fonction d’accélération personnalisée est spécifiée comme un argument de fonction lambda à la [ `Easing` ](xref:Xamarin.Forms.Easing) constructeur et utilise le `Math.Cos` méthode pour créer un effet lente qui est ralenti par la `Math.Exp` (méthode). Par conséquent, le [ `Image` ](xref:Xamarin.Forms.Image) instance est traduite afin qu’il apparaisse à supprimer à la place de repos finale.

## <a name="summary"></a>Récapitulatif

Cet article a montré comment utiliser les fonctions d’accélération prédéfinies et comment créer des fonctions d’accélération personnalisées. Xamarin.Forms inclut un [ `Easing` ](xref:Xamarin.Forms.Easing) classe qui vous permet de spécifier une fonction de transfert qui contrôle comment animations accélèrent ou ralentir car ils s’exécutent.



## <a name="related-links"></a>Liens associés

- [Vue d’ensemble de la prise en charge asynchrone](~/cross-platform/platform/async.md)
- [Fonctions d’accélération (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)
- [Accélération](xref:Xamarin.Forms.Easing)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
