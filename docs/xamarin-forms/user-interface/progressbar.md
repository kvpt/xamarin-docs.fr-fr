---
title: Xamarin. Forms (ProgressBar)
description: Le ProgressBar Xamarin. Forms est un contrôle qui représente visuellement la progression sous la forme d’une barre horizontale qui est remplie en fonction d’une propriété de type float.
ms.prod: xamarin
ms.assetId: C2F85FED-797C-466B-A0FD-E73CFB79B267
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/09/2019
ms.openlocfilehash: 40f3c9a5af29d1782249775b9f3166698eb6221a
ms.sourcegitcommit: 7acff5c5a03a0351962c05beebfc347503d83fe6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73201945"
---
# <a name="xamarinforms-progressbar"></a>Xamarin. Forms (ProgressBar)
[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)

Le contrôle Xamarin. Forms [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) représente visuellement la progression sous la forme d’une barre horizontale qui est remplie à un pourcentage représenté par une valeur de `float`. La classe `ProgressBar` hérite de [`View`](xref:Xamarin.Forms.View).

Les captures d’écran suivantes montrent un `ProgressBar` sur iOS et Android :

![Capture d’écran de ProgressBar sur iOS et Android](progressbar-images/progressbars-default.png "ProgressBar sur iOS et Android")

Le contrôle `ProgressBar` définit deux propriétés :

* [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress) est une valeur `float` qui représente la progression actuelle sous la forme d’une valeur comprise entre 0 et 1. `Progress` valeurs inférieures à 0 seront ancrées à 0, les valeurs supérieures à 1 seront ancrées à 1.
* [`ProgressColor`](xref:Xamarin.Forms.ProgressBar.ProgressColor) est une `Color` qui affecte la couleur de la barre intérieure représentant la progression actuelle.

Ces propriétés sont soulignes par [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que les `ProgressBar` peuvent être stylisés et être la cible des liaisons de données.

Le contrôle `ProgressBar` définit également une méthode `ProgressTo` qui anime la barre de sa valeur actuelle à une valeur spécifiée. Pour plus d’informations, consultez [animer un ProgressBar](#animate-a-progressbar).

> [!NOTE]
> La `ProgressBar` n’accepte pas la manipulation de l’utilisateur. elle est donc ignorée lors de l’utilisation de la touche Tab pour sélectionner des contrôles.

## <a name="create-a-progressbar"></a>Créer un ProgressBar

Un `ProgressBar` peut être instancié en XAML. Sa propriété `Progress` détermine le pourcentage de remplissage de la barre de couleur interne. La valeur par défaut de la propriété `Progress` est 0. L’exemple suivant montre comment instancier un `ProgressBar` en XAML avec la propriété facultative `Progress` définie :

```xaml
<ProgressBar Progress="0.5" />
```

Vous pouvez également créer un `ProgressBar` dans le code :

```csharp
ProgressBar progressBar = new ProgressBar { Progress = 0.5f };
```

> [!WARNING]
> N’utilisez pas d’options de disposition horizontale sans contrainte comme `Center`, `Start`ou `End` avec `ProgressBar`. Sur UWP, le `ProgressBar` se réduit à une barre de zéro largeur. Conservez la valeur par défaut `HorizontalOptions` de `Fill` et n’utilisez pas une largeur de `Auto` lors de la mise en forme d’un `ProgressBar` dans une disposition `Grid`.

## <a name="progressbar-appearance-properties"></a>Propriétés d’apparence de ProgressBar

La propriété `ProgressColor` définit la couleur de la barre intérieure lorsque la propriété `Progress` est supérieure à zéro. L’exemple suivant montre comment instancier un `ProgressBar` en XAML avec la propriété `ProgressColor` définie :

```xaml
<ProgressBar ProgressColor="Orange" />
```

La propriété `ProgressColor` peut également être définie lors de la création d’un `ProgressBar` dans le code :

```csharp
ProgressBar progressBar = new ProgressBar { ProgressColor = Color.Orange };
```

Les captures d’écran suivantes montrent les `ProgressBar` avec la propriété `ProgressColor` définie sur `Color.Orange` sur iOS et Android :

![Capture d’écran d’un style ProgressBar sur iOS et Android](progressbar-images/progressbars-styled.png "ProgressBar stylisé sur iOS et Android")

## <a name="animate-a-progressbar"></a>Animer un ProgressBar

La méthode `ProgressTo` anime le `ProgressBar` de sa valeur `Progress` actuelle à une valeur fournie au fil du temps. La méthode accepte une valeur de progression `float`, une durée `uint` en millisecondes, une `Easing` valeur enum et retourne un `Task<bool>`. Le code suivant montre comment animer une `ProgressBar`:

```csharp
// animate to 75% progress over 500 milliseconds with linear easing
await progressBar.ProgressTo(0.75, 500, Easing.Linear);
```

Pour plus d’informations sur l’énumération `Easing`, consultez [fonctions d’accélération dans Xamarin. Forms](~/xamarin-forms/user-interface/animation/easing.md).

## <a name="related-links"></a>Liens connexes

* [Démonstrations de ProgressBar](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)
