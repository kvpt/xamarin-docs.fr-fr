---
title: Fonctions d’accélération dans Xamarin. Forms
description: Xamarin. Forms comprend une classe d’accélération qui vous permet de spécifier une fonction de transfert qui contrôle la vitesse à laquelle les animations augmentent ou ralentissent lorsqu’elles sont en cours d’exécution. Cet article montre comment utiliser les fonctions d’accélération prédéfinies et comment créer des fonctions d’accélération personnalisées.
ms.prod: xamarin
ms.assetid: E6F124C7-A161-4C1F-AF40-52F0935E54DE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 56ea31d1e1be8bbad4a27dd7ffd844aa03f75bbb
ms.sourcegitcommit: efbc69acf4ea484d8815311b058114379c9db8a2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73842829"
---
# <a name="easing-functions-in-xamarinforms"></a>Fonctions d’accélération dans Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)

_Xamarin. Forms comprend une classe d’accélération qui vous permet de spécifier une fonction de transfert qui contrôle la vitesse à laquelle les animations augmentent ou ralentissent lorsqu’elles sont en cours d’exécution. Cet article montre comment utiliser les fonctions d’accélération prédéfinies et comment créer des fonctions d’accélération personnalisées._

La classe [`Easing`](xref:Xamarin.Forms.Easing) définit un certain nombre de fonctions d’accélération qui peuvent être consommées par les animations :

- La fonction d’accélération [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn) rebondit au début de l’animation.
- La fonction d’accélération [`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut) rebondit à la fin de l’animation.
- La fonction d’accélération [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn) accélère lentement l’animation.
- La fonction d’accélération [`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut) accélère l’animation au début et ralentit l’animation à la fin.
- La fonction d’accélération [`CubicOut`](xref:Xamarin.Forms.Easing.CubicOut) ralentit rapidement l’animation.
- La fonction d’accélération [`Linear`](xref:Xamarin.Forms.Easing.Linear) utilise une vélocité constante et est la fonction d’accélération par défaut.
- La fonction d’accélération [`SinIn`](xref:Xamarin.Forms.Easing.SinIn) accélère de manière fluide l’animation.
- La fonction d’accélération [`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut) accélère facilement l’animation au début, et décélère lisse l’animation à la fin.
- La fonction d’accélération [`SinOut`](xref:Xamarin.Forms.Easing.SinOut) ralentit harmonieusement l’animation.
- La fonction d’accélération [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) amène l’animation à s’accélérer très rapidement vers la fin.
- La fonction d’accélération [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) entraîne une décélération rapide de l’animation vers la fin.

Les suffixes `In` et `Out` indiquent si l’effet fourni par la fonction d’accélération est visible au début de l’animation, à la fin, ou les deux.

En outre, les fonctions d’accélération personnalisées peuvent être créées. Pour plus d’informations, consultez [fonctions d’accélération personnalisée](#customeasing).

## <a name="consuming-an-easing-function"></a>Consommation d’une fonction d’accélération

Les méthodes d’extension d’animation de la classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) autorisent la spécification d’une fonction d’accélération comme paramètre de méthode final, comme illustré dans l’exemple de code suivant :

```csharp
await image.TranslateTo(0, 200, 2000, Easing.BounceIn);
await image.ScaleTo(2, 2000, Easing.CubicIn);
await image.RotateTo(360, 2000, Easing.SinInOut);
await image.ScaleTo(1, 2000, Easing.CubicOut);
await image.TranslateTo(0, -200, 2000, Easing.BounceOut);
```

En spécifiant une fonction d’accélération pour une animation, la vélocité d’animation devient non linéaire et produit l’effet fourni par la fonction d’accélération. Si vous omettez une fonction d’accélération lors de la création d’une animation, l’animation utilise la fonction d’accélération [`Linear`](xref:Xamarin.Forms.Easing.Linear) par défaut, ce qui produit une vélocité linéaire.

Pour plus d’informations sur l’utilisation des méthodes d’extension d’animation dans la classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) , consultez [animations simples](~/xamarin-forms/user-interface/animation/simple.md). Les fonctions d’accélération peuvent également être consommées par la classe [`Animation`](xref:Xamarin.Forms.Animation) . Pour plus d’informations, consultez [animations personnalisées](~/xamarin-forms/user-interface/animation/custom.md).

<a name="customeasing" />

## <a name="custom-easing-functions"></a>Fonctions d’accélération personnalisées

Il existe trois approches principales pour créer une fonction d’accélération personnalisée :

1. Créez une méthode qui accepte un argument `double` et retourne un `double` résultat.
1. Créer un `Func<double, double>`.
1. Spécifiez la fonction d’accélération comme argument du constructeur [`Easing`](xref:Xamarin.Forms.Easing) .

Dans les trois cas, la fonction d’accélération personnalisée doit retourner 0 pour un argument de 0, et 1 pour un argument de 1. Toutefois, toute valeur peut être retournée entre les valeurs d’argument 0 et 1. Chaque approche sera maintenant abordée dans son tour.

### <a name="custom-easing-method"></a>Méthode d’accélération personnalisée

Une fonction d’accélération personnalisée peut être définie en tant que méthode qui prend un argument `double` et retourne un résultat `double`, comme illustré dans l’exemple de code suivant :

```csharp
double CustomEase (double t)
{
  return t == 0 || t == 1 ? t : (int)(5 * t) / 5.0;
}

await image.TranslateTo(0, 200, 2000, (Easing)CustomEase);
```

La méthode `CustomEase` tronque la valeur entrante aux valeurs 0, 0,2, 0,4, 0,6, 0,8 et 1. Par conséquent, l’instance de [`Image`](xref:Xamarin.Forms.Image) est convertie en sauts discrets, plutôt qu’en douceur.

### <a name="custom-easing-func"></a>Fonction d’accélération personnalisée

Une fonction d’accélération personnalisée peut également être définie en tant que `Func<double, double>`, comme illustré dans l’exemple de code suivant :

```csharp
Func<double, double> CustomEaseFunc = t => 9 * t * t * t - 13.5 * t * t + 5.5 * t;
await image.TranslateTo(0, 200, 2000, CustomEaseFunc);
```

Le `CustomEaseFunc` représente une fonction d’accélération qui démarre rapidement, ralentit et inverse le cours, puis rétablit le cours pour accélérer rapidement la fin. Par conséquent, bien que le déplacement global de l’instance [`Image`](xref:Xamarin.Forms.Image) soit vers le bas, il inverse également momentanément le cours à mi-chemin de l’animation.

### <a name="custom-easing-constructor"></a>Constructeur d’accélération personnalisé

Une fonction d’accélération personnalisée peut également être définie comme argument du constructeur [`Easing`](xref:Xamarin.Forms.Easing) , comme illustré dans l’exemple de code suivant :

```csharp
await image.TranslateTo (0, 200, 2000, new Easing (t => 1 - Math.Cos (10 * Math.PI * t) * Math.Exp (-5 * t)));
```

La fonction d’accélération personnalisée est spécifiée en tant qu’argument de fonction lambda pour le constructeur [`Easing`](xref:Xamarin.Forms.Easing) et utilise la méthode `Math.Cos` pour créer un effet de déplacement lent qui est amorti par la méthode `Math.Exp`. Par conséquent, l’instance de [`Image`](xref:Xamarin.Forms.Image) est convertie afin d’être supprimée à son emplacement de repos final.

## <a name="summary"></a>Récapitulatif

Cet article a montré comment utiliser les fonctions d’accélération prédéfinies et comment créer des fonctions d’accélération personnalisées. Xamarin. Forms comprend une classe [`Easing`](xref:Xamarin.Forms.Easing) qui vous permet de spécifier une fonction de transfert qui contrôle la vitesse à laquelle les animations augmentent ou ralentissent lorsqu’elles sont en cours d’exécution.

## <a name="related-links"></a>Liens associés

- [Vue d’ensemble de la prise en charge asynchrone](~/cross-platform/platform/async.md)
- [Fonctions d’accélération (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)
- [Simplifier](xref:Xamarin.Forms.Easing)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
