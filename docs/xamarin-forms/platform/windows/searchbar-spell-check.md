---
title: " SearchBar Vérification orthographique sur Windows"
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser le spécifique à la plate-forme Windows qui permet à un SearchBar d’interagir avec le moteur de vérification orthographique.
ms.prod: xamarin
ms.assetid: 7974C91F-7502-4DB3-B0E9-C45E943DDA26
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: da75d42e0e0bcf38dd2ff50999b705125dc4e2b5
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292569"
---
# <a name="searchbar-spell-check-on-windows"></a>SearchBar Vérification orthographique sur Windows

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce plateforme Windows universelle spécifique à la plateforme permet à un [`SearchBar`](xref:Xamarin.Forms.SearchBar) d’interagir avec le moteur de vérification orthographique. Il est consommé en XAML en affectant à la propriété jointe [`SearchBar.IsSpellCheckEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.IsSpellCheckEnabledProperty) la valeur `boolean` :

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ... windows:SearchBar.IsSpellCheckEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

Vous pouvez également, il peut être consommé à partir de c# à l’aide de l’API fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

searchBar.On<Windows>().SetIsSpellCheckEnabled(true);
```

La méthode `SearchBar.On<Windows>` spécifie que ce spécifique à la plateforme s’exécutera uniquement sur le plateforme Windows universelle. La méthode [`SearchBar.SetIsSpellCheckEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.SetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar},System.Boolean)) , dans l’espace de noms [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) , active ou désactive le vérificateur d’orthographe. En outre, la méthode `SearchBar.SetIsSpellCheckEnabled` peut être utilisée pour activer ou désactiver le vérificateur d’orthographe en appelant la méthode [`SearchBar.GetIsSpellCheckEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.GetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar})) pour retourner si le vérificateur d’orthographe est activé :

```csharp
searchBar.On<Windows>().SetIsSpellCheckEnabled(!searchBar.On<Windows>().GetIsSpellCheckEnabled());
```

Le résultat est que le texte entré dans le [`SearchBar`](xref:Xamarin.Forms.SearchBar) peut être vérifié avec l’orthographe, avec des orthographes incorrectes indiquées à l’utilisateur :

![Vérification orthographique SearchBar-spécifique à la plateforme](searchbar-spell-check-images/searchbar-spellcheck.png "Vérification orthographique SearchBar-spécifique à la plateforme")

> [!NOTE]
> La classe `SearchBar` de l’espace de noms [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) possède également des méthodes [`EnableSpellCheck`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.EnableSpellCheck*) et [`DisableSpellCheck`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.DisableSpellCheck*) qui peuvent être utilisées pour activer et désactiver le vérificateur d’orthographe sur le `SearchBar`, respectivement.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
