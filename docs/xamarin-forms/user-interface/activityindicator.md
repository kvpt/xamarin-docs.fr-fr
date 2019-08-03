---
title: Indicateur d’activité dans Xamarin. Forms
description: Le contrôle ActivityIndicator indique aux utilisateurs que l’application est engagée dans une activité de longue durée, sans donner une indication de la progression. Cet article explique comment utiliser un ActivityIndicator en XAML et du code.
ms.prod: xamarin
ms.assetid: 4CEED02D-5CA3-4C3A-B7ED-3193FC272261
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/10/2019
ms.openlocfilehash: e13a46e1022f4e33ace6f9f19bb5cea5d1ac784b
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739162"
---
# <a name="xamarinforms-activityindicator"></a>Xamarin. Forms ActivityIndicator
[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)

Xamarin. Forms[`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) est un contrôle qui affiche une animation qui montre que l’application est engagée dans une activité longue. Contrairement à `ActivityIndicator` , le n’indique aucune progression. [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) Hérite de [`View`.](xref:Xamarin.Forms.View) `ActivityIndicator`

La capture d’écran suivante `ActivityIndicator` montre un contrôle sur iOS et Android:

![Capture d’écran de ActivityIndicator sur iOS et Android](activityindicator-images/activityindicators-default.png "Capture d’écran de ActivityIndicator sur iOS et Android")

Le `ActivityIndicator` contrôle définit les propriétés suivantes:

* [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning)est une `bool` valeur qui indique `ActivityIndicator` si doit être visible, animé ou masqué. Lorsque la valeur est `false` , `ActivityIndicator` n’est pas visible.
* [`Color`](xref:Xamarin.Forms.ActivityIndicator.Color)est une `Color` valeur qui définit la couleur `ActivityIndicator`d’affichage du.

Ces propriétés sont sauvegardées par [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) des objets, ce qui signifie `ActivityIndicator` que peut être stylisé et être la cible des liaisons de données.

## <a name="create-an-activityindicator"></a>Créer un ActivityIndicator

Un `ActivityIndicator` peut être instancié en XAML. Sa `IsRunning` propriété peut être définie pour déterminer si le contrôle est visible et anime. Si la `IsRunning` propriété n’est pas définie, la `ActivityIndicator` valeur `false` par défaut est et n’est pas visible. L’exemple suivant montre comment instancier un `ActivityIndicator` en XAML avec le jeu `IsRunning` de propriétés facultatif:

```xaml
<ActivityIndicator IsRunning="true" />
```

Un `ActivityIndicator` peut également être créé dans le code:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { IsRunning = true };
```

## <a name="activityindicator-appearance-properties"></a>Propriétés d’apparence ActivityIndicator

La `Color` propriété peut être définie pour définir la `ActivityIndicator` couleur. L’exemple suivant montre comment instancier un `ActivityIndicator` en XAML avec le `Color` jeu de propriétés:

```xaml
<ActivityIndicator Color="Orange" />
```

La `Color` propriété peut également être définie lors de la `ActivityIndicator` création d’un dans le code:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { Color = Color.Orange };
```

La capture d’écran suivante `ActivityIndicator` montre le `Color` avec la propriété `Color.Orange` définie sur iOS et Android:

![Capture d’écran des ActivityIndicator stylisés sur iOS et Android](activityindicator-images/activityindicators-styled.png "Capture d’écran des ActivityIndicator stylisés sur iOS et Android")

## <a name="related-links"></a>Liens connexes

* [Démonstrations ActivityIndicator](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)
* [ProgressBar](~/xamarin-forms/user-interface/progressbar.md)
