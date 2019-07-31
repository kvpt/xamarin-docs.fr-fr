---
title: Ordre de lecture InputView sur Windows
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser le spécifique à la plateforme Windows, qui permet de détecter de manière dynamique le texte bidirectionnel.
ms.prod: xamarin
ms.assetid: E61BAEE0-C8B7-4F33-8DDC-FA1B9CA8E81D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: c184424a982aa82712685dbc33ad57422f2f8338
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68651424"
---
# <a name="inputview-reading-order-on-windows"></a>Ordre de lecture InputView sur Windows

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce plateforme Windows universelle spécifique à la plateforme active l’ordre de lecture (de gauche à droite ou de droite à gauche) du texte bidirectionnel dans [`Entry`](xref:Xamarin.Forms.Entry) [`Editor`](xref:Xamarin.Forms.Editor)les instances, et [`Label`](xref:Xamarin.Forms.Label) à détecter de manière dynamique. Elle est consommée dans XAML en définissant le [ `InputView.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.DetectReadingOrderFromContentProperty) (pour `Entry` et `Editor` instances) ou [ `Label.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Label.DetectReadingOrderFromContentProperty) propriété jointe un `boolean` valeur :

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Editor ... windows:InputView.DetectReadingOrderFromContent="true" />
        ...
    </StackLayout>
</ContentPage>
```

Vous pouvez également, il peut être consommé à partir de C# à l’aide de l’API fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

editor.On<Windows>().SetDetectReadingOrderFromContent(true);
```

Le `Editor.On<Windows>` méthode spécifie que cette plateforme spécifique s’exécute uniquement sur la plateforme Windows universelle. Le [ `InputView.SetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.SetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView},System.Boolean)) (méthode), dans le [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espace de noms, est utilisé pour contrôler si l’ordre de lecture est détectée à partir du contenu dans le [ `InputView` ](xref:Xamarin.Forms.InputView). En outre, le `InputView.SetDetectReadingOrderFromContent` méthode peut être utilisée pour activer/désactiver si l’ordre de lecture est détectée à partir du contenu en appelant le [ `InputView.GetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.GetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView})) méthode pour retourner la valeur actuelle :

```csharp
editor.On<Windows>().SetDetectReadingOrderFromContent(!editor.On<Windows>().GetDetectReadingOrderFromContent());
```

Le résultat est que [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), et [ `Label` ](xref:Xamarin.Forms.Label) instances peuvent avoir l’ordre de lecture de leur contenu détecté dynamiquement :

[![InputView détection de l’ordre de lecture à partir du contenu spécifique à la plateforme](inputview-reading-order-images/editor-readingorder.png "InputView de détection de l’ordre de lecture à partir du contenu spécifique à la plateforme")](inputview-reading-order-images/editor-readingorder-large.png#lightbox "InputView l’ordre de lecture à partir de détection contenu spécifique à la plateforme")

> [!NOTE]
> Contrairement au paramètre la [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriété, la logique pour les vues qui détectent l’ordre de lecture à partir de leur contenu de texte n’affectera pas l’alignement du texte dans la vue. Au lieu de cela, il ajuste l’ordre dans lequel les blocs de texte bidirectionnel sont disposées.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
