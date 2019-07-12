---
title: Xamarin.Forms ProgressBar
description: Xamarin.Forms ProgressBar est un contrôle qui représente visuellement des cours sous la forme d’une barre horizontale qui est remplie, basée sur une propriété de type float.
ms.prod: xamarin
ms.assetId: C2F85FED-797C-466B-A0FD-E73CFB79B267
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/09/2019
ms.openlocfilehash: 2e2917077575ebc78dbdda8ae55aa230a39a7ba1
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67837009"
---
# <a name="xamarinforms-progressbar"></a>Xamarin.Forms ProgressBar
[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ProgressBarDemos)

Xamarin.Forms [ `ProgressBar` ](xref:Xamarin.Forms.ProgressBar) est un contrôle qui représente visuellement des cours sous la forme d’une barre horizontale qui est remplie par un pourcentage représenté par un `float` valeur. Le `ProgressBar` hérite de la classe [ `View` ](xref:Xamarin.Forms.View).

La capture d’écran suivante montre un `ProgressBar` sur iOS et Android :

![Capture d’écran de ProgressBar sur iOS et Android](progressbar-images/progressbars-default.png "ProgressBar sur iOS et Android")

Le `ProgressBar` contrôle définit deux propriétés :

* [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress) est un `float` valeur qui représente la progression actuelle en tant que valeur de 0 à 1. `Progress` les valeurs inférieures à 0 est ancrée à 0, les valeurs supérieures à 1 est ancrée à 1.
* [`ProgressColor`](xref:Xamarin.Forms.ProgressBar.ProgressColor) est un `Color` qui affecte à l’intérieur de la barre représentant la progression actuelle de couleur.

Ces propriétés sont secondées par [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que le `ProgressBar` styles peuvent leur être et être la cible des liaisons de données.

Le `ProgressBar` contrôle définit également un `ProgressTo` méthode qui anime la barre de sa valeur actuelle à une valeur spécifiée. Pour plus d’informations, consultez [animer un ProgressBar](#animate-a-progressbar).

> [!NOTE]
> Le `ProgressBar` n’accepte pas de manipulation de l’utilisateur afin qu’il est ignoré lors de l’utilisation de la touche Tab pour sélectionner des contrôles.

## <a name="create-a-progressbar"></a>Créer un ProgressBar

Un `ProgressBar` peut être instanciée dans XAML. Son `Progress` propriété peut être définie pour déterminer le pourcentage de remplissage de la barre de couleur interne. Si le `Progress` propriété n’est pas définie, la valeur par défaut est 0. L’exemple suivant montre comment instancier un `ProgressBar` dans XAML avec le paramètre facultatif `Progress` jeu de propriétés :

```xaml
<ProgressBar Progress="0.5" />
```

Un `ProgressBar` peut également être créé dans le code :

```csharp
ProgressBar progressBar = new ProgressBar { Progress = 0.5f };
```

> [!WARNING]
> N’utilisez pas de telles que les options de disposition horizontale sans contrainte `Center`, `Start`, ou `End` avec `ProgressBar`. Sur UWP, le `ProgressBar` réduit à une barre de largeur égale à zéro. Conservez la valeur par défaut `HorizontalOptions` valeur `Fill` et n’utilisez pas une largeur de `Auto` lors du placement un `ProgressBar` dans un `Grid` mise en page.

## <a name="progressbar-appearance-properties"></a>Propriétés d’apparence ProgressBar

Le `ProgressColor` propriété permettre être configurée pour définir la barre interne couleur quand la `Progress` propriété est supérieure à zéro. L’exemple suivant montre comment instancier un `ProgressBar` dans XAML avec la `ProgressColor` jeu de propriétés :

```xaml
<ProgressBar OnColor="Orange" />
```

Le `ProgressColor` propriété peut également être définie lors de la création un `ProgressBar` dans le code :

```csharp
ProgressBar progressBar = new ProgressBar { ProgressColor = Color.Orange };
```

La capture d’écran suivante montre le `ProgressBar` avec la `ProgressColor` propriété définie sur `Color.Orange` sur iOS et Android :

![Capture d’écran de style ProgressBar sur iOS et Android](progressbar-images/progressbars-styled.png "un style de ProgressBar sur iOS et Android")

## <a name="animate-a-progressbar"></a>Animer un ProgressBar

Le `ProgressTo` méthode anime le `ProgressBar` à partir de son cours `Progress` valeur à une valeur fournie au fil du temps. La méthode accepte un `float` progression valeur, un `uint` durée en millisecondes, un `Easing` valeur enum et retourne un `Task<bool>`. Le code suivant montre comment animer un `ProgressBar`:

```csharp
// animate to 75% progress over 500 milliseconds with linear easing
await progressBar.ProgressTo(0.75, 500, Easing.Linear);
```

Pour plus d’informations sur la `Easing` énumération, consultez [dans Xamarin.Forms, les fonctions d’accélération](~/xamarin-forms/user-interface/animation/easing.md).

## <a name="related-links"></a>Liens connexes

* [Démonstrations de ProgressBar](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ProgressBarDemos)
