---
title: " SearchBar Vérification orthographique sur Windows"
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plate-forme Windows qui permet à un SearchBar d’interagir avec le moteur de vérification orthographique.
ms.prod: xamarin
ms.assetid: 7974C91F-7502-4DB3-B0E9-C45E943DDA26
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b97ce2015ba632d100b946a5b287f0eae737123c
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368685"
---
# <a name="searchbar-spell-check-on-windows"></a>SearchBar Vérification orthographique sur Windows

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce plateforme Windows universelle spécifique à la plateforme permet [`SearchBar`](xref:Xamarin.Forms.SearchBar) à un d’interagir avec le moteur de vérification orthographique. Il est consommé en XAML en affectant [`SearchBar.IsSpellCheckEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.IsSpellCheckEnabledProperty) une valeur à la propriété jointe `boolean` :

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ... windows:SearchBar.IsSpellCheckEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

searchBar.On<Windows>().SetIsSpellCheckEnabled(true);
```

La `SearchBar.On<Windows>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur le plateforme Windows universelle. [ `SearchBar.SetIsSpellCheckEnabled` ] (XREF : Xamarin.Forms . PlatformConfiguration. WindowsSpecific. SearchBar. SetIsSpellCheckEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . SearchBar}, System. Boolean)), dans l' [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espace de noms, active et désactive le vérificateur d’orthographe. En outre, la `SearchBar.SetIsSpellCheckEnabled` méthode peut être utilisée pour basculer le vérificateur d’orthographe en appelant le [ `SearchBar.GetIsSpellCheckEnabled` ] (XREF : Xamarin.Forms . PlatformConfiguration. WindowsSpecific. SearchBar. GetIsSpellCheckEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . SearchBar})) pour retourner une valeur indiquant si le vérificateur d’orthographe est activé :

```csharp
searchBar.On<Windows>().SetIsSpellCheckEnabled(!searchBar.On<Windows>().GetIsSpellCheckEnabled());
```

Le résultat est que le texte entré dans le [`SearchBar`](xref:Xamarin.Forms.SearchBar) peut être vérifié avec l’orthographe, avec des orthographes incorrectes indiquées à l’utilisateur :

![Vérification orthographique SearchBar-spécifique à la plateforme](searchbar-spell-check-images/searchbar-spellcheck.png "Vérification orthographique SearchBar-spécifique à la plateforme")

> [!NOTE]
> La `SearchBar` classe de l' [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espace de noms possède également [`EnableSpellCheck`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.EnableSpellCheck*) des [`DisableSpellCheck`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.DisableSpellCheck*) méthodes et qui peuvent être utilisées pour activer et désactiver le vérificateur d’orthographe sur le `SearchBar` , respectivement.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)