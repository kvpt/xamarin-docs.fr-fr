---
title: Fonctions d’accélération dansXamarin.Forms
description: Xamarin.Formscomprend une classe d’accélération qui vous permet de spécifier une fonction de transfert qui contrôle la vitesse à laquelle les animations augmentent ou ralentissent lorsqu’elles sont en cours d’exécution. Cet article montre comment utiliser les fonctions d’accélération prédéfinies et comment créer des fonctions d’accélération personnalisées.
ms.prod: xamarin
ms.assetid: E6F124C7-A161-4C1F-AF40-52F0935E54DE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 574b6f82aea3c71d43799c6ca86997d895eed4c5
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84573805"
---
# <a name="easing-functions-in-xamarinforms"></a>Fonctions d’accélération dansXamarin.Forms

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)

_Xamarin. Forms comprend une classe d’accélération qui vous permet de spécifier une fonction de transfert qui contrôle la vitesse à laquelle les animations augmentent ou ralentissent lorsqu’elles sont en cours d’exécution. Cet article montre comment utiliser les fonctions d’accélération prédéfinies et comment créer des fonctions d’accélération personnalisées._

La [`Easing`](xref:Xamarin.Forms.Easing) classe définit un certain nombre de fonctions d’accélération qui peuvent être consommées par les animations :

- La [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn) fonction d’accélération rebondit l’animation au début.
- La [`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut) fonction d’accélération rebondit l’animation à la fin.
- La [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn) fonction d’accélération accélère lentement l’animation.
- La [`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut) fonction d’accélération accélère l’animation au début, et ralentit l’animation à la fin.
- La [`CubicOut`](xref:Xamarin.Forms.Easing.CubicOut) fonction d’accélération ralentit rapidement l’animation.
- La [`Linear`](xref:Xamarin.Forms.Easing.Linear) fonction d’accélération utilise une vélocité constante et est la fonction d’accélération par défaut.
- La [`SinIn`](xref:Xamarin.Forms.Easing.SinIn) fonction d’accélération accélère facilement l’animation.
- La [`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut) fonction d’accélération accélère en douceur l’animation au début, et décélère lisse l’animation à la fin.
- La [`SinOut`](xref:Xamarin.Forms.Easing.SinOut) fonction d’accélération décélère lisse l’animation.
- La [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) fonction d’accélération permet d’accélérer très rapidement l’animation vers la fin.
- La [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) fonction d’accélération entraîne une décélération rapide de l’animation vers la fin.

Les `In` `Out` suffixes et indiquent si l’effet fourni par la fonction d’accélération est visible au début de l’animation, à la fin, ou les deux.

En outre, les fonctions d’accélération personnalisées peuvent être créées. Pour plus d’informations, consultez [fonctions d’accélération personnalisée](#custom-easing-functions).

## <a name="consuming-an-easing-function"></a>Consommation d’une fonction d’accélération

Les méthodes d’extension d’animation de la [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe permettent de spécifier une fonction d’accélération comme paramètre de méthode final, comme illustré dans l’exemple de code suivant :

```csharp
await image.TranslateTo(0, 200, 2000, Easing.BounceIn);
await image.ScaleTo(2, 2000, Easing.CubicIn);
await image.RotateTo(360, 2000, Easing.SinInOut);
await image.ScaleTo(1, 2000, Easing.CubicOut);
await image.TranslateTo(0, -200, 2000, Easing.BounceOut);
```

En spécifiant une fonction d’accélération pour une animation, la vélocité d’animation devient non linéaire et produit l’effet fourni par la fonction d’accélération. Si vous omettez une fonction d’accélération lors de la création d’une animation, celle-ci utilise la fonction d’accélération par défaut [`Linear`](xref:Xamarin.Forms.Easing.Linear) , qui produit une vélocité linéaire.

Pour plus d’informations sur l’utilisation des méthodes d’extension d’animation dans la [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe, consultez [animations simples](~/xamarin-forms/user-interface/animation/simple.md). Les fonctions d’accélération peuvent également être consommées par la [`Animation`](xref:Xamarin.Forms.Animation) classe. Pour plus d’informations, consultez [animations personnalisées](~/xamarin-forms/user-interface/animation/custom.md).

## <a name="custom-easing-functions"></a>Fonctions d’accélération personnalisées

Il existe trois approches principales pour créer une fonction d’accélération personnalisée :

1. Crée une méthode qui prend un `double` argument et retourne un `double` résultat.
1. Créez un `Func<double, double>`.
1. Spécifiez la fonction d’accélération comme argument pour le [`Easing`](xref:Xamarin.Forms.Easing) constructeur.

Dans les trois cas, la fonction d’accélération personnalisée doit retourner 0 pour un argument de 0, et 1 pour un argument de 1. Toutefois, toute valeur peut être retournée entre les valeurs d’argument 0 et 1. Chaque approche sera maintenant abordée dans son tour.

### <a name="custom-easing-method"></a>Méthode d’accélération personnalisée

Une fonction d’accélération personnalisée peut être définie en tant que méthode qui prend un `double` argument et retourne un `double` résultat, comme illustré dans l’exemple de code suivant :

```csharp
double CustomEase (double t)
{
  return t == 0 || t == 1 ? t : (int)(5 * t) / 5.0;
}

await image.TranslateTo(0, 200, 2000, (Easing)CustomEase);
```

La `CustomEase` méthode tronque la valeur entrante aux valeurs 0, 0,2, 0,4, 0,6, 0,8 et 1. Par conséquent, l' [`Image`](xref:Xamarin.Forms.Image) instance est convertie en sauts discrets, plutôt qu’en douceur.

### <a name="custom-easing-func"></a>Fonction d’accélération personnalisée

Une fonction d’accélération personnalisée peut également être définie en tant que `Func<double, double>` , comme illustré dans l’exemple de code suivant :

```csharp
Func<double, double> CustomEaseFunc = t => 9 * t * t * t - 13.5 * t * t + 5.5 * t;
await image.TranslateTo(0, 200, 2000, CustomEaseFunc);
```

Le `CustomEaseFunc` représente une fonction d’accélération qui démarre rapidement, ralentit et inverse le cours, puis rétablit le cours pour accélérer l’accélération vers la fin. Par conséquent, alors que le déplacement global de l' [`Image`](xref:Xamarin.Forms.Image) instance est vers le bas, elle inverse également temporairement le cours à mi-chemin de l’animation.

### <a name="custom-easing-constructor"></a>Constructeur d’accélération personnalisé

Une fonction d’accélération personnalisée peut également être définie comme argument du [`Easing`](xref:Xamarin.Forms.Easing) constructeur, comme illustré dans l’exemple de code suivant :

```csharp
await image.TranslateTo (0, 200, 2000, new Easing (t => 1 - Math.Cos (10 * Math.PI * t) * Math.Exp (-5 * t)));
```

La fonction d’accélération personnalisée est spécifiée en tant qu’argument de fonction lambda pour le [`Easing`](xref:Xamarin.Forms.Easing) constructeur et utilise la `Math.Cos` méthode pour créer un effet de déplacement lent qui est amorti par la `Math.Exp` méthode. Par conséquent, l' [`Image`](xref:Xamarin.Forms.Image) instance est traduite afin d’être supprimée à son emplacement de repos final.

## <a name="summary"></a>Résumé

Cet article a montré comment utiliser les fonctions d’accélération prédéfinies et comment créer des fonctions d’accélération personnalisées. Xamarin.Formscomprend une [`Easing`](xref:Xamarin.Forms.Easing) classe qui vous permet de spécifier une fonction de transfert qui contrôle la vitesse à laquelle les animations augmentent ou ralentissent lorsqu’elles sont en cours d’exécution.

## <a name="related-links"></a>Liens connexes

- [Vue d’ensemble de la prise en charge Async](~/cross-platform/platform/async.md)
- [Fonctions d’accélération (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)
- [Simplifier](xref:Xamarin.Forms.Easing)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
