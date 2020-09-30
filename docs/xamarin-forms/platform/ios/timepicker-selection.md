---
title: Sélection de l’élément TimePicker sur iOS
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui contrôle le moment où la sélection de l’élément se produit dans un TimePicker.
ms.prod: xamarin
ms.assetid: 554AC877-8698-4B8C-A676-80DD64305A06
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 372c268c13c50719953ac63bcc43cc8d9bff4bdd
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563716"
---
# <a name="timepicker-item-selection-on-ios"></a>Sélection de l’élément TimePicker sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce contrôle spécifique à la plateforme iOS lorsque la sélection d’élément se produit dans un [`TimePicker`](xref:Xamarin.Forms.TimePicker) , ce qui permet à l’utilisateur de spécifier la sélection de cet élément lors de l’exploration des éléments du contrôle, ou uniquement une fois que le bouton **terminé** est enfoncé. Il est consommé en XAML en affectant `TimePicker.UpdateMode` à la propriété jointe une valeur de l' `UpdateMode` énumération :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
       <TimePicker Time="14:00:00"
                   ios:TimePicker.UpdateMode="WhenFinished" />
       ...
    </StackLayout>
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

timePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

La `TimePicker.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La `TimePicker.SetUpdateMode` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisée pour contrôler le moment où la sélection de l’élément se produit, avec l' `UpdateMode` énumération qui fournit deux valeurs possibles :

- `Immediately` : la sélection de l’élément se produit lorsque l’utilisateur parcourt des éléments dans le [`TimePicker`](xref:Xamarin.Forms.TimePicker) . Il s’agit du comportement par défaut dans Xamarin.Forms .
- `WhenFinished` : la sélection de l’élément n’est effectuée qu’une fois que l’utilisateur a appuyé sur le bouton **terminé** dans le [`TimePicker`](xref:Xamarin.Forms.TimePicker) .

En outre, la `SetUpdateMode` méthode peut être utilisée pour basculer les valeurs d’énumération en appelant la `UpdateMode` méthode, qui retourne le actuel `UpdateMode` :

```csharp
switch (timePicker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        timePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        timePicker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

Le résultat est qu’un spécifié `UpdateMode` est appliqué au [`TimePicker`](xref:Xamarin.Forms.TimePicker) , qui contrôle le moment où la sélection de l’élément se produit :

[![Capture d’écran des modes de mise à jour TimePicker](timepicker-selection-images/timepicker-updatemode.png "TimePicker UpdateMode-spécifique à la plateforme")](timepicker-selection-images/timepicker-updatemode-large.png#lightbox "TimePicker UpdateMode-spécifique à la plateforme")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)