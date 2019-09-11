---
title: Indicateur d’activité dans Xamarin. Forms
description: Le contrôle ActivityIndicator indique aux utilisateurs que l’application est engagée dans une activité de longue durée, sans donner une indication de la progression. Cet article explique comment utiliser un ActivityIndicator en XAML et du code.
ms.prod: xamarin
ms.assetid: 4CEED02D-5CA3-4C3A-B7ED-3193FC272261
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/10/2019
ms.openlocfilehash: 0694439f5e363399e0442c9883426c0f0bf5d989
ms.sourcegitcommit: ab51d32f4ea0e0d4701f0bf2f1465c9323cd070b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70887431"
---
# <a name="xamarinforms-activityindicator"></a>Xamarin. Forms ActivityIndicator
[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)

Le contrôle Xamarin. [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) Forms affiche une animation qui montre que l’application est engagée dans une activité longue. Contrairement à `ActivityIndicator` , le n’indique aucune progression. [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) Hérite de [`View`.](xref:Xamarin.Forms.View) `ActivityIndicator`

Les captures d’écran suivantes `ActivityIndicator` montrent un contrôle sur iOS et Android :

![Capture d’écran de ActivityIndicator sur iOS et Android](activityindicator-images/activityindicators-default.png "Capture d’écran de ActivityIndicator sur iOS et Android")

Le `ActivityIndicator` contrôle définit les propriétés suivantes :

* [`Color`](xref:Xamarin.Forms.ActivityIndicator.Color)est une `Color` valeur qui définit la couleur `ActivityIndicator`d’affichage du.
* [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning)est une `bool` valeur qui indique `ActivityIndicator` si doit être visible, animé ou masqué. Lorsque la valeur est `false` , `ActivityIndicator` n’est pas visible.

Ces propriétés sont sauvegardées par [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) des objets, ce qui signifie `ActivityIndicator` que peut être stylisé et être la cible des liaisons de données.

## <a name="create-an-activityindicator"></a>Créer un ActivityIndicator

La `ActivityIndicator` classe peut être instanciée en XAML. Sa `IsRunning` propriété détermine si le contrôle est visible et anime. La `IsRunning` valeur par défaut de `false`la propriété est. L’exemple suivant montre comment instancier un `ActivityIndicator` en XAML avec le jeu `IsRunning` de propriétés facultatif :

```xaml
<ActivityIndicator IsRunning="true" />
```

Un `ActivityIndicator` peut également être créé dans le code :

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { IsRunning = true };
```

## <a name="activityindicator-appearance-properties"></a>Propriétés d’apparence ActivityIndicator

La `Color` propriété définit la `ActivityIndicator` couleur. L’exemple suivant montre comment instancier un `ActivityIndicator` en XAML avec le `Color` jeu de propriétés :

```xaml
<ActivityIndicator Color="Orange" />
```

La `Color` propriété peut également être définie lors de la `ActivityIndicator` création d’un dans le code :

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { Color = Color.Orange };
```

Les captures d’écran suivantes `ActivityIndicator` montrent le `Color` avec la propriété `Color.Orange` définie sur iOS et Android :

![Capture d’écran des ActivityIndicator stylisés sur iOS et Android](activityindicator-images/activityindicators-styled.png "Capture d’écran des ActivityIndicator stylisés sur iOS et Android")

## <a name="related-links"></a>Liens connexes

* [Démonstrations ActivityIndicator](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)
* [ProgressBar](~/xamarin-forms/user-interface/progressbar.md)
