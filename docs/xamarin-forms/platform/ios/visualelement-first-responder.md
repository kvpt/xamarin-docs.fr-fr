---
title: VisualElement premier répondeur sur iOS
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser le spécifique à la plateforme iOS qui permet à un objet VisualElement de devenir le premier répondeur aux événements tactiles.
ms.prod: xamarin
ms.assetid: 3A77BA02-B87A-44EC-AC51-9D3130EF314C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
ms.openlocfilehash: be6c233b63d172d2fcacb1cea7f5e9aeeb7faed1
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "78292512"
---
# <a name="visualelement-first-responder-on-ios"></a>VisualElement premier répondeur sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme iOS permet à un objet [`VisualElement`](xref:Xamarin.Forms.VisualElement) de devenir le premier répondeur aux événements tactiles, plutôt que la page contenant l’élément. Il est consommé en XAML en affectant à la propriété `VisualElement.CanBecomeFirstResponder` pouvant être liée la valeur `true`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Entry Placeholder="Enter text" />
        <Button ios:VisualElement.CanBecomeFirstResponder="True"
                Text="OK" />
    </StackLayout>
</ContentPage>
```

Vous pouvez également, il peut être consommé à partir de c# à l’aide de l’API fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Entry entry = new Entry { Placeholder = "Enter text" };
Button button = new Button { Text = "OK" };
button.On<iOS>().SetCanBecomeFirstResponder(true);
```

La méthode `VisualElement.On<iOS>` spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La méthode `VisualElement.SetCanBecomeFirstResponder`, dans l’espace de noms [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) , est utilisée pour définir le `VisualElement` pour qu’il devienne le premier répondeur pour les événements tactiles. En outre, la méthode `VisualElement.CanBecomeFirstResponder` peut être utilisée pour retourner si le `VisualElement` est le premier répondeur aux événements tactiles.

Le résultat est qu’une [`VisualElement`](xref:Xamarin.Forms.VisualElement) peut devenir le premier répondeur pour les événements tactiles, plutôt que la page contenant l’élément. Cela permet des scénarios tels que les applications de conversation qui n’ignorent pas un clavier quand un [`Button`](xref:Xamarin.Forms.Button) est frappé.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
