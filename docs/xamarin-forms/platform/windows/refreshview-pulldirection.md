---
title: Direction d’extraction RefreshView sur Windows
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plate-forme Windows, qui permet de modifier la direction d’extraction d’un RefreshView.
ms.prod: xamarin
ms.assetid: 407A862B-281E-4384-9696-C0655830B84D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2019
ms.openlocfilehash: cf2ab38bed7b45a48fcf0b5f86add49c0d4cc21f
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697317"
---
# <a name="refreshview-pull-direction-on-windows"></a>Direction d’extraction RefreshView sur Windows

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Cette plateforme Windows universelle spécifique à la plateforme permet de modifier la direction d’extraction d’une `RefreshView` pour qu’elle corresponde à l’orientation du contrôle de défilement qui affiche des données. Il est consommé en XAML en affectant à la `RefreshView.RefreshPullDirection` propriété pouvant être liée la valeur de l’énumération `RefreshPullDirection` :

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <RefreshView windows:RefreshView.RefreshPullDirection="LeftToRight"
                 IsRefreshing="{Binding IsRefreshing}"
                 Command="{Binding RefreshCommand}">
        <ScrollView>
            ...
        </ScrollView>
    </RefreshView>
 </ContentPage>
```

Elle peut également être utilisée à partir de C# l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...
refreshView.On<Windows>().SetRefreshPullDirection(RefreshPullDirection.LeftToRight);
```

La méthode `RefreshView.On<Windows>` spécifie que ce spécifique à la plateforme s’exécutera uniquement sur le plateforme Windows universelle. La méthode `RefreshView.SetRefreshPullDirection`, dans l’espace de noms [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) , est utilisée pour définir la direction d’extraction du `RefreshView`, avec l’énumération `RefreshPullDirection` qui fournit quatre valeurs possibles :

- `LeftToRight` indique qu’une opération pull de gauche à droite lance une actualisation.
- `TopToBottom` indique qu’une opération d’extraction de haut en bas lance une actualisation et est la direction d’extraction par défaut d’un `RefreshView`.
- `RightToLeft` indique qu’une opération d’extraction de droite à gauche lance une actualisation.
- `BottomToTop` indique qu’une opération d’extraction de bas en haut lance une actualisation.

En outre, la méthode `GetRefreshPullDirection` peut être utilisée pour retourner le `RefreshPullDirection` actuel du `RefreshView`.

Le résultat est qu’un `RefreshPullDirection` spécifié est appliqué à la `RefreshView`, afin de définir la direction de l’extraction de sorte qu’elle corresponde à l’orientation du contrôle à défilement qui affiche les données. La capture d’écran suivante montre une `RefreshView` avec une direction d’extraction `LeftToRight` :

[![Capture d’écran d’un RefreshView avec une direction de glissement de gauche à droite, sur UWP](refreshview-pulldirection-images/refreshview-pulldirection.png "RefreshView avec direction de l’extraction de gauche à droite")](refreshview-pulldirection-images/refreshview-pulldirection-large.png#lightbox "RefreshView avec direction de l’extraction de gauche à droite")

> [!NOTE]
> Lorsque vous modifiez la direction de l’extraction, la position de départ du cercle de progression pivote automatiquement de sorte que la flèche commence à la position appropriée pour la direction d’extraction.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
