---
title: Indicateur d’activité dans Xamarin.Forms
description: Le contrôle ActivityIndicator indique aux utilisateurs que l’application est engagée dans une activité de longue durée, sans perdre aucune indication de progression. Cet article explique comment utiliser un ActivityIndicator dans XAML et le code.
ms.prod: xamarin
ms.assetid: 4CEED02D-5CA3-4C3A-B7ED-3193FC272261
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/10/2019
ms.openlocfilehash: abd8150e3aa4ec347c8d956004993340630208bf
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67837059"
---
# <a name="xamarinforms-activityindicator"></a>Xamarin.Forms ActivityIndicator
[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ActivityIndicatorDemos)

Xamarin.Forms[ `ActivityIndicator` ](xref:Xamarin.Forms.ActivityIndicator) est un contrôle qui affiche une animation pour montrer que l’application est engagée dans une activité de longue durée. Contrairement à la [ `ProgressBar` ](xref:Xamarin.Forms.ProgressBar), le `ActivityIndicator` ne donne aucune indication de la progression. Le `ActivityIndicator` hérite [ `View` ](xref:Xamarin.Forms.View).

La capture d’écran suivante montre un `ActivityIndicator` contrôle sur iOS et Android :

![Capture d’écran de ActivityIndicator sur iOS et Android](activityindicator-images/activityindicators-default.png "capture d’écran de ActivityIndicator sur iOS et Android")

Le `ActivityIndicator` contrôle définit les propriétés suivantes :

* [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) est un `bool` valeur qui indique si le `ActivityIndicator` doit être visible et animation ou masqué. Lorsque la valeur est `false` le `ActivityIndicator` ne seront pas visibles.
* [`Color`](xref:Xamarin.Forms.ActivityIndicator.Color) est un `Color` valeur qui définit la couleur d’affichage de la `ActivityIndicator`.

Ces propriétés sont secondées par [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que le `ActivityIndicator` styles peuvent leur être et être la cible des liaisons de données.

## <a name="create-an-activityindicator"></a>Créer un ActivityIndicator

Un `ActivityIndicator` peut être instanciée dans XAML. Son `IsRunning` propriété peut être définie pour déterminer si le contrôle est visible et animation. Si le `IsRunning` propriété n’est pas définie, les valeurs par défaut `false` et `ActivityIndicator` ne seront pas visibles. L’exemple suivant montre comment instancier un `ActivityIndicator` dans XAML avec le paramètre facultatif `IsRunning` jeu de propriétés :

```xaml
<ActivityIndicator IsRunning="true" />
```

Un `ActivityIndicator` peut également être créé dans le code :

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { IsRunning = true };
```

## <a name="activityindicator-appearance-properties"></a>Propriétés d’apparence ActivityIndicator

Le `Color` propriété permettre être configurée pour définir le `ActivityIndicator` couleur. L’exemple suivant montre comment instancier un `ActivityIndicator` dans XAML avec la `Color` jeu de propriétés :

```xaml
<ActivityIndicator Color="Orange" />
```

Le `Color` propriété peut également être définie lors de la création un `ActivityIndicator` dans le code :

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { Color = Color.Orange };
```

La capture d’écran suivante montre le `ActivityIndicator` avec la `Color` propriété définie sur `Color.Orange` sur iOS et Android :

![Capture d’écran de style ActivityIndicator sur iOS et Android](activityindicator-images/activityindicators-styled.png "capture d’écran de style ActivityIndicator sur iOS et Android")

## <a name="related-links"></a>Liens connexes

* [Démonstrations de ActivityIndicator](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ActivityIndicatorDemos)
* [ProgressBar](~/xamarin-forms/user-interface/progressbar.md)
