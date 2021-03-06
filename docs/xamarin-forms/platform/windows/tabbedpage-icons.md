---
title: TabbedPage icônes sur Windows
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plate-forme Windows qui permet d’afficher les icônes de page dans une barre d’outils TabbedPage.
ms.prod: xamarin
ms.assetid: 7C5031A5-74EE-4469-994E-BEA7BA9D33CB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3b63e66018ec96df49dfed2eb0ac6320a9585084
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368328"
---
# <a name="tabbedpage-icons-on-windows"></a>TabbedPage icônes sur Windows

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Cette plateforme Windows universelle spécifique à la plateforme permet l’affichage d’icônes de page dans une [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) barre d’outils et permet de spécifier éventuellement la taille de l’icône. Il est consommé en XAML en affectant [`TabbedPage.HeaderIconsEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsEnabledProperty) à la propriété jointe la `true` valeur, et en affectant éventuellement [`TabbedPage.HeaderIconsSize`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsSizeProperty) une valeur à la propriété jointe [`Size`](xref:Xamarin.Forms.Size) :

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
            windows:TabbedPage.HeaderIconsEnabled="true">
    <windows:TabbedPage.HeaderIconsSize>
        <Size>
            <x:Arguments>
                <x:Double>24</x:Double>
                <x:Double>24</x:Double>
            </x:Arguments>
        </Size>
    </windows:TabbedPage.HeaderIconsSize>
    <ContentPage Title="Todo" IconImageSource="todo.png">
        ...
    </ContentPage>
    <ContentPage Title="Reminders" IconImageSource="reminders.png">
        ...
    </ContentPage>
    <ContentPage Title="Contacts" IconImageSource="contacts.png">
        ...
    </ContentPage>
</TabbedPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

public class WindowsTabbedPageIconsCS : Xamarin.Forms.TabbedPage
{
  public WindowsTabbedPageIconsCS()
  {
    On<Windows>().SetHeaderIconsEnabled(true);
    On<Windows>().SetHeaderIconsSize(new Size(24, 24));

    Children.Add(new ContentPage { Title = "Todo", IconImageSource = "todo.png" });
    Children.Add(new ContentPage { Title = "Reminders", IconImageSource = "reminders.png" });
    Children.Add(new ContentPage { Title = "Contacts", IconImageSource = "contacts.png" });
  }
}
```

La `TabbedPage.On<Windows>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur le plateforme Windows universelle. [ `TabbedPage.SetHeaderIconsEnabled` ] (XREF : Xamarin.Forms . PlatformConfiguration. WindowsSpecific. TabbedPage. SetHeaderIconsEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . TabbedPage}, System. Boolean)), dans l' [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espace de noms, est utilisé pour activer ou désactiver les icônes d’en-tête. [ `TabbedPage.SetHeaderIconsSize` ] (XREF : Xamarin.Forms . PlatformConfiguration. WindowsSpecific. TabbedPage. SetHeaderIconsSize ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . TabbedPage}, Xamarin.Forms . Size)). la méthode spécifie éventuellement la taille de l’icône d’en-tête avec une [`Size`](xref:Xamarin.Forms.Size) valeur.

En outre, la `TabbedPage` classe de l' `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` espace de noms possède également une [`EnableHeaderIcons`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.EnableHeaderIcons*) méthode qui active les icônes d’en-tête, une [`DisableHeaderIcons`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.DisableHeaderIcons*) méthode qui désactive les icônes d’en-tête et une [`IsHeaderIconsEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.IsHeaderIconsEnabled*) méthode qui retourne une `boolean` valeur qui indique si les icônes d’en-tête sont activées.

Le résultat est que les icônes de page peuvent être affichées dans une [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) barre d’outils, avec la taille d’icône définie éventuellement sur une taille souhaitée :

![Icônes TabbedPage activées spécifiques à la plateforme](tabbedpage-icons-images/tabbedpage-icons.png "Icônes TabbedPage activées spécifiques à la plateforme")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)