---
title: Titres de page de grande taille sur iOS
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser l’objet spécifique à la plateforme iOS, qui affiche le titre de la page sous la forme d’un titre de grande taille dans la barre de navigation d’un NavigationPage.
ms.prod: xamarin
ms.assetid: 45FD9145-8319-452C-9AE6-624431A4D43C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0db20620870340386ccd0cedf7f98cb2975527ba
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84128040"
---
# <a name="large-page-titles-on-ios"></a>Titres de page de grande taille sur iOS

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme iOS est utilisé pour afficher le titre de la page sous la forme d’un grand titre dans la barre de navigation d’un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) , pour les appareils qui utilisent iOS 11 ou une version ultérieure. Un titre de grande taille est aligné à gauche et utilise une police plus grande, et passe à un titre standard lorsque l’utilisateur commence à faire défiler le contenu, afin que l’ensemble de l’écran soit utilisé efficacement. Toutefois, dans l’orientation paysage, le titre revient au centre de la barre de navigation pour optimiser la disposition du contenu. Il est consommé en XAML en affectant `NavigationPage.PrefersLargeTitles` une valeur à la propriété jointe `boolean` :

```xaml
<NavigationPage xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ...
                ios:NavigationPage.PrefersLargeTitles="true">
  ...
</NavigationPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var navigationPage = new Xamarin.Forms.NavigationPage(new iOSLargeTitlePageCS());
navigationPage.On<iOS>().SetPrefersLargeTitles(true);
```

La `NavigationPage.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La `NavigationPage.SetPrefersLargeTitle` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, contrôle si les titres de grande taille sont activés.

À condition que les titres de grande taille soient activés sur le [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) , toutes les pages de la pile de navigation afficheront des titres de grande taille. Ce comportement peut être substitué sur les pages en affectant `Page.LargeTitleDisplay` à la propriété jointe une valeur de l' `LargeTitleDisplayMode` énumération :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Large Title"
             ios:Page.LargeTitleDisplay="Never">
  ...
</ContentPage>
```

Le comportement de la page peut également être substitué à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSLargeTitlePageCS : ContentPage
{
    public iOSLargeTitlePageCS(ICommand restore)
    {
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        ...
    }
    ...
}
```

La `Page.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La `Page.SetLargeTitleDisplay` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, contrôle le comportement de grand titre sur le [`Page`](xref:Xamarin.Forms.Page) , avec l' `LargeTitleDisplayMode` énumération qui fournit trois valeurs possibles :

- `Always`: force la barre de navigation et la taille de police à utiliser le grand format.
- `Automatic`: utilisez le même style (grand ou petit) que l’élément précédent dans la pile de navigation.
- `Never`: force l’utilisation de la barre de navigation de petite taille standard.

En outre, la `SetLargeTitleDisplay` méthode peut être utilisée pour basculer les valeurs d’énumération en appelant la `LargeTitleDisplay` méthode, qui retourne le actuel `LargeTitleDisplayMode` :

```csharp
switch (On<iOS>().LargeTitleDisplay())
{
    case LargeTitleDisplayMode.Always:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Automatic);
        break;
    case LargeTitleDisplayMode.Automatic:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        break;
    case LargeTitleDisplayMode.Never:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Always);
        break;
}
```

Le résultat est qu’un spécifié `LargeTitleDisplayMode` est appliqué au [`Page`](xref:Xamarin.Forms.Page) , qui contrôle le comportement du titre volumineux :

![](page-large-title-images/large-title.png "Blur Effect Platform-Specific")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
