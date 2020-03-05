---
title: Sélection de l’élément DatePicker sur iOS
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser le spécifique à la plateforme iOS qui contrôle le moment où la sélection de l’élément se produit dans un DatePicker.
ms.prod: xamarin
ms.assetid: 847E69D1-6AE0-4E82-B9C8-919E009C2014
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
ms.openlocfilehash: df84cf01909cec564edc9c6c8bb55382a2b9dfe3
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "78291713"
---
# <a name="datepicker-item-selection-on-ios"></a>Sélection de l’élément DatePicker sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce contrôle spécifique à la plateforme iOS lorsque la sélection d’éléments se produit dans un [`DatePicker`](xref:Xamarin.Forms.DatePicker), ce qui permet à l’utilisateur de spécifier la sélection de l’élément lors de l’exploration des éléments du contrôle, ou uniquement une fois que le bouton **terminé** est enfoncé. Il est consommé en XAML en affectant à la propriété jointe `DatePicker.UpdateMode` la valeur de l’énumération `UpdateMode` :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
       <DatePicker MinimumDate="01/01/2020"
                   MaximumDate="12/31/2020"
                   ios:DatePicker.UpdateMode="WhenFinished" />
       ...
    </StackLayout>
</ContentPage>
```

Vous pouvez également, il peut être consommé à partir de c# à l’aide de l’API fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

datePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

La méthode `DatePicker.On<iOS>` spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La méthode `DatePicker.SetUpdateMode`, dans l’espace de noms [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) , est utilisée pour contrôler le moment où la sélection de l’élément se produit, avec l’énumération `UpdateMode` qui fournit deux valeurs possibles :

- `Immediately` : la sélection de l’élément se produit lorsque l’utilisateur parcourt les éléments du [`DatePicker`](xref:Xamarin.Forms.DatePicker). Il s’agit du comportement par défaut dans Xamarin.Forms.
- `WhenFinished` : la sélection de l’élément n’est effectuée qu’une fois que l’utilisateur a appuyé sur le bouton **terminé** dans le [`DatePicker`](xref:Xamarin.Forms.DatePicker).

En outre, la méthode `SetUpdateMode` peut être utilisée pour basculer les valeurs d’énumération en appelant la méthode `UpdateMode`, qui retourne le `UpdateMode`actuel :

```csharp
switch (datePicker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        datePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        datePicker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

Le résultat est qu’un `UpdateMode` spécifié est appliqué à l' [`DatePicker`](xref:Xamarin.Forms.DatePicker), qui contrôle le moment où la sélection de l’élément se produit :

[![Capture d’écran des modes de mise à jour de DatePicker](datepicker-selection-images/datepicker-updatemode.png "DatePicker UpdateMode, spécifique à la plateforme")](datepicker-selection-images/datepicker-updatemode-large.png#lightbox "DatePicker UpdateMode, spécifique à la plateforme")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
