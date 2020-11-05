---
title: Sélection de l’élément DatePicker sur iOS
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui contrôle le moment où la sélection de l’élément se produit dans un DatePicker.
ms.prod: xamarin
ms.assetid: 847E69D1-6AE0-4E82-B9C8-919E009C2014
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8668ef01e7fac02243934f145eb2e3f4ff4a6a8a
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373209"
---
# <a name="datepicker-item-selection-on-ios"></a>Sélection de l’élément DatePicker sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce contrôle spécifique à la plateforme iOS lorsque la sélection d’élément se produit dans un [`DatePicker`](xref:Xamarin.Forms.DatePicker) , ce qui permet à l’utilisateur de spécifier la sélection de cet élément lors de l’exploration des éléments du contrôle, ou uniquement une fois que le bouton **terminé** est enfoncé. Il est consommé en XAML en affectant `DatePicker.UpdateMode` à la propriété jointe une valeur de l' `UpdateMode` énumération :

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

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

datePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

La `DatePicker.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La `DatePicker.SetUpdateMode` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisée pour contrôler le moment où la sélection de l’élément se produit, avec l' `UpdateMode` énumération qui fournit deux valeurs possibles :

- `Immediately` : la sélection de l’élément se produit lorsque l’utilisateur parcourt des éléments dans le [`DatePicker`](xref:Xamarin.Forms.DatePicker) . Il s’agit du comportement par défaut dans Xamarin.Forms .
- `WhenFinished` : la sélection de l’élément n’est effectuée qu’une fois que l’utilisateur a appuyé sur le bouton **terminé** dans le [`DatePicker`](xref:Xamarin.Forms.DatePicker) .

En outre, la `SetUpdateMode` méthode peut être utilisée pour basculer les valeurs d’énumération en appelant la `UpdateMode` méthode, qui retourne le actuel `UpdateMode` :

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

Le résultat est qu’un spécifié `UpdateMode` est appliqué au [`DatePicker`](xref:Xamarin.Forms.DatePicker) , qui contrôle le moment où la sélection de l’élément se produit :

[![Capture d’écran des modes de mise à jour de DatePicker](datepicker-selection-images/datepicker-updatemode.png "DatePicker UpdateMode Platform-Specific")](datepicker-selection-images/datepicker-updatemode-large.png#lightbox "DatePicker UpdateMode Platform-Specific")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)