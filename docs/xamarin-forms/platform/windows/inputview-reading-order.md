---
title: Ordre de lecture InputView sur Windows
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme Windows, qui permet de détecter de manière dynamique le texte bidirectionnel.
ms.prod: xamarin
ms.assetid: E61BAEE0-C8B7-4F33-8DDC-FA1B9CA8E81D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 55907b5878a112d0ad640cc95049183dd68d89f3
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374691"
---
# <a name="inputview-reading-order-on-windows"></a>Ordre de lecture InputView sur Windows

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce plateforme Windows universelle spécifique à la plateforme active l’ordre de lecture (de gauche à droite ou de droite à gauche) du texte bidirectionnel dans [`Entry`](xref:Xamarin.Forms.Entry) [`Editor`](xref:Xamarin.Forms.Editor) les instances, et [`Label`](xref:Xamarin.Forms.Label) à détecter de manière dynamique. Il est consommé en XAML en affectant [`InputView.DetectReadingOrderFromContent`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.DetectReadingOrderFromContentProperty) `Entry` `Editor` une valeur à la propriété (instances et) ou [`Label.DetectReadingOrderFromContent`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Label.DetectReadingOrderFromContentProperty) à la propriété jointe `boolean` :

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Editor ... windows:InputView.DetectReadingOrderFromContent="true" />
        ...
    </StackLayout>
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

editor.On<Windows>().SetDetectReadingOrderFromContent(true);
```

La `Editor.On<Windows>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur le plateforme Windows universelle. [ `InputView.SetDetectReadingOrderFromContent` ] (XREF : Xamarin.Forms . PlatformConfiguration. WindowsSpecific. InputView. SetDetectReadingOrderFromContent ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . InputView}, System. Boolean)), dans l' [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espace de noms, permet de contrôler si l’ordre de lecture est détecté à partir du contenu de [`InputView`](xref:Xamarin.Forms.InputView) . En outre, la `InputView.SetDetectReadingOrderFromContent` méthode peut être utilisée pour déterminer si l’ordre de lecture est détecté à partir du contenu en appelant le [ `InputView.GetDetectReadingOrderFromContent` ] (XREF : Xamarin.Forms . PlatformConfiguration. WindowsSpecific. InputView. GetDetectReadingOrderFromContent ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . InputView})) pour retourner la valeur actuelle :

```csharp
editor.On<Windows>().SetDetectReadingOrderFromContent(!editor.On<Windows>().GetDetectReadingOrderFromContent());
```

Le résultat est que [`Entry`](xref:Xamarin.Forms.Entry) [`Editor`](xref:Xamarin.Forms.Editor) [`Label`](xref:Xamarin.Forms.Label) les instances, et peuvent avoir l’ordre de lecture de leur contenu détecté dynamiquement :

[![InputView détection de l’ordre de lecture à partir du contenu spécifique à la plateforme](inputview-reading-order-images/editor-readingorder.png "InputView détection de l’ordre de lecture à partir du contenu spécifique à la plateforme")](inputview-reading-order-images/editor-readingorder-large.png#lightbox "InputView détection de l’ordre de lecture à partir du contenu spécifique à la plateforme")

> [!NOTE]
> Contrairement à la définition de la [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriété, la logique des vues qui détectent l’ordre de lecture à partir de leur contenu de texte n’affecte pas l’alignement du texte dans la vue. Au lieu de cela, il ajuste l’ordre dans lequel les blocs de texte bidirectionnel sont disposés.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)