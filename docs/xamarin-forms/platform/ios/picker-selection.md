---
title: Sélection de l’élément de sélecteur sur iOS
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser le spécifique à la plateforme iOS qui contrôle le moment où la sélection de l’élément se produit dans un sélecteur.
ms.prod: xamarin
ms.assetid: 26B0604A-BD30-49FD-83A6-F0EDFBB0524B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 57420921100c99db1e2c3a5259ece30cfda719f2
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68651841"
---
# <a name="picker-item-selection-on-ios"></a>Sélection de l’élément de sélecteur sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce contrôle spécifique à la plateforme iOS lorsque la sélection d’élément [`Picker`](xref:Xamarin.Forms.Picker)se produit dans un, ce qui permet à l’utilisateur de spécifier la sélection de cet élément lors de l’exploration des éléments du contrôle, ou uniquement une fois que le bouton **terminé** est enfoncé. Elle est consommée dans XAML en définissant le `Picker.UpdateMode` propriété attachée à une valeur de la `UpdateMode` énumération :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <Picker ... Title="Select a monkey" ios:Picker.UpdateMode="WhenFinished">
          ...
        </Picker>
        ...
    </StackLayout>
</ContentPage>
```

Vous pouvez également, il peut être consommé à partir de C# à l’aide de l’API fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

Le `Picker.On<iOS>` méthode spécifie que cette plateforme spécifique s’exécute uniquement sur iOS. Le `Picker.SetUpdateMode` (méthode), dans le [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, permet de contrôler en cas de sélection d’élément, avec la `UpdateMode` énumération fournissant deux valeurs possibles :

- `Immediately` : sélection d’éléments se produit lorsque l’utilisateur parcourt des éléments dans le [ `Picker` ](xref:Xamarin.Forms.Picker). Il s’agit du comportement par défaut dans Xamarin.Forms.
- `WhenFinished` : sélection d’éléments se produit uniquement une fois que l’utilisateur a appuyé sur le **fait** situé dans le [ `Picker` ](xref:Xamarin.Forms.Picker).

En outre, le `SetUpdateMode` méthode peut être utilisée pour activer/désactiver les valeurs d’énumération en appelant le `UpdateMode` (méthode), qui retourne actuel `UpdateMode`:

```csharp
switch (picker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        picker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

Le résultat qui est spécifié `UpdateMode` est appliqué à la [ `Picker` ](xref:Xamarin.Forms.Picker), qui contrôle lorsque la sélection d’éléments se produit :

[![](picker-selection-images/picker-updatemode.png "Sélecteur UpdateMode spécifiques à la plateforme")](picker-selection-images/picker-updatemode-large.png#lightbox "sélecteur UpdateMode spécifiques à la plateforme")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
