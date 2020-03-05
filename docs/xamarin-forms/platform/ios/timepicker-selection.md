---
title: Sélection de l’élément TimePicker sur iOS
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser le spécifique à la plateforme iOS qui contrôle le moment où la sélection de l’élément se produit dans un TimePicker.
ms.prod: xamarin
ms.assetid: 554AC877-8698-4B8C-A676-80DD64305A06
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
ms.openlocfilehash: 818f368da8ebb375fbacd97d3d48185ba60470d4
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "78291516"
---
# <a name="timepicker-item-selection-on-ios"></a>Sélection de l’élément TimePicker sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce contrôle spécifique à la plateforme iOS lorsque la sélection d’éléments se produit dans un [`TimePicker`](xref:Xamarin.Forms.TimePicker), ce qui permet à l’utilisateur de spécifier la sélection de l’élément lors de l’exploration des éléments du contrôle, ou uniquement une fois que le bouton **terminé** est enfoncé. Il est consommé en XAML en affectant à la propriété jointe `TimePicker.UpdateMode` la valeur de l’énumération `UpdateMode` :

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

Vous pouvez également, il peut être consommé à partir de c# à l’aide de l’API fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

timePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

La méthode `TimePicker.On<iOS>` spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La méthode `TimePicker.SetUpdateMode`, dans l’espace de noms [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) , est utilisée pour contrôler le moment où la sélection de l’élément se produit, avec l’énumération `UpdateMode` qui fournit deux valeurs possibles :

- `Immediately` : la sélection de l’élément se produit lorsque l’utilisateur parcourt les éléments du [`TimePicker`](xref:Xamarin.Forms.TimePicker). Il s’agit du comportement par défaut dans Xamarin.Forms.
- `WhenFinished` : la sélection de l’élément n’est effectuée qu’une fois que l’utilisateur a appuyé sur le bouton **terminé** dans le [`TimePicker`](xref:Xamarin.Forms.TimePicker).

En outre, la méthode `SetUpdateMode` peut être utilisée pour basculer les valeurs d’énumération en appelant la méthode `UpdateMode`, qui retourne le `UpdateMode`actuel :

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

Le résultat est qu’un `UpdateMode` spécifié est appliqué à l' [`TimePicker`](xref:Xamarin.Forms.TimePicker), qui contrôle le moment où la sélection de l’élément se produit :

[![Capture d’écran des modes de mise à jour TimePicker](timepicker-selection-images/timepicker-updatemode.png "TimePicker UpdateMode-spécifique à la plateforme")](timepicker-selection-images/timepicker-updatemode-large.png#lightbox "TimePicker UpdateMode-spécifique à la plateforme")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
