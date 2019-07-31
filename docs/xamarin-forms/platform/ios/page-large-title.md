---
title: Titres de page de grande taille sur iOS
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser l’objet spécifique à la plateforme iOS, qui affiche le titre de la page sous la forme d’un titre de grande taille dans la barre de navigation d’un NavigationPage.
ms.prod: xamarin
ms.assetid: 45FD9145-8319-452C-9AE6-624431A4D43C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: ab9becf2f7363674346abf004c1748cb06eb0d31
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655422"
---
# <a name="large-page-titles-on-ios"></a>Titres de page de grande taille sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme IOS est utilisé pour afficher le titre de la page sous la forme d’un grand titre [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)dans la barre de navigation d’un, pour les appareils qui utilisent iOS 11 ou une version ultérieure. Un titre volumineux est aligné à gauche et utilise une police plus grande et passe à un titre standard comme l’utilisateur commence à faire défiler un contenu, afin que l’écran est utilisé efficacement. Cependant, en mode paysage, le titre renvoie au centre de la barre de navigation pour optimiser la disposition du contenu. Elle est consommée dans XAML en définissant le `NavigationPage.PrefersLargeTitles` propriété jointe un `boolean` valeur :

```xaml
<NavigationPage xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ...
                ios:NavigationPage.PrefersLargeTitles="true">
  ...
</NavigationPage>
```

Il peut également être consommé à partir de C# à l’aide de l’API fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var navigationPage = new Xamarin.Forms.NavigationPage(new iOSLargeTitlePageCS());
navigationPage.On<iOS>().SetPrefersLargeTitles(true);
```

Le `NavigationPage.On<iOS>` méthode spécifie que cette plateforme spécifique s’exécute uniquement sur iOS. Le `NavigationPage.SetPrefersLargeTitle` (méthode), dans le [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, détermine si les grands titres sont activées.

Condition que les grands titres sont activés sur le [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage), toutes les pages de la pile de navigation affichera les grands titres. Ce comportement peut être substitué dans les pages en définissant le `Page.LargeTitleDisplay` propriété attachée à une valeur de la `LargeTitleDisplayMode` énumération :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Large Title"
             ios:Page.LargeTitleDisplay="Never">
  ...
</ContentPage>
```

Vous pouvez également le comportement de la page peut être substitué à partir de C# à l’aide de l’API fluent :

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

Le `Page.On<iOS>` méthode spécifie que cette plateforme spécifique s’exécute uniquement sur iOS. Le `Page.SetLargeTitleDisplay` (méthode), dans le [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, contrôle le comportement de titre volumineux sur le [ `Page` ](xref:Xamarin.Forms.Page), avec le `LargeTitleDisplayMode` énumération en fournissant trois possible valeurs :

- `Always` – forcer la barre de navigation et la police taille à utiliser le grand format.
- `Automatic` – Utilisez le même style (petit ou grand) en tant que l’élément précédent dans la pile de navigation.
- `Never` – forcer l’utilisation de la barre de navigation standard, petit format.

En outre, le `SetLargeTitleDisplay` méthode peut être utilisée pour activer/désactiver les valeurs d’énumération en appelant le `LargeTitleDisplay` (méthode), qui retourne actuel `LargeTitleDisplayMode`:

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

Le résultat qui est spécifié `LargeTitleDisplayMode` est appliqué à la [ `Page` ](xref:Xamarin.Forms.Page), qui contrôle le comportement de titre volumineux :

![](page-large-title-images/large-title.png "Spécifique à la plateforme effet de flou")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
