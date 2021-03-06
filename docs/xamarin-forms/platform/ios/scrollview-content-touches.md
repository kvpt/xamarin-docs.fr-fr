---
title: Le contenu ScrollView touche sur iOS
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui contrôle si un ScrollView gère un mouvement tactile ou le transmet à son contenu.
ms.prod: xamarin
ms.assetid: 99F823DB-B379-40F0-A343-A9783C341120
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 072f5db9115069fad547bb363865a609e5167ce8
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97939938"
---
# <a name="scrollview-content-touches-on-ios"></a>Le contenu ScrollView touche sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Un minuteur implicite est déclenché lorsqu’un mouvement tactile commence dans un [`ScrollView`](xref:Xamarin.Forms.ScrollView) sur iOS et que le `ScrollView` décide, en fonction de l’action de l’utilisateur dans l’étendue du minuteur, s’il doit gérer le mouvement ou le passer à son contenu. Par défaut, iOS `ScrollView` retarde le contenu, mais cela peut provoquer des problèmes dans certaines circonstances, avec le `ScrollView` contenu qui n’a pas remporté le geste quand c’est le cas. Par conséquent, ce contrôle spécifique à la plateforme détermine si un `ScrollView` gère un mouvement tactile ou le passe à son contenu. Il est consommé en XAML en affectant `ScrollView.ShouldDelayContentTouches` une valeur à la propriété jointe `boolean` :

```xaml
<FlyoutPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <FlyoutPage.Flyout>
        <ContentPage Title="Menu" BackgroundColor="Blue" />
    </FlyoutPage.Flyout>
    <FlyoutPage.Detail>
        <ContentPage>
            <ScrollView x:Name="scrollView" ios:ScrollView.ShouldDelayContentTouches="false">
                <StackLayout Margin="0,20">
                    <Slider />
                    <Button Text="Toggle ScrollView DelayContentTouches" Clicked="OnButtonClicked" />
                </StackLayout>
            </ScrollView>
        </ContentPage>
    </FlyoutPage.Detail>
</FlyoutPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

scrollView.On<iOS>().SetShouldDelayContentTouches(false);
```

La `ScrollView.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La `ScrollView.SetShouldDelayContentTouches` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisée pour déterminer si un [`ScrollView`](xref:Xamarin.Forms.ScrollView) gère un mouvement tactile ou le passe à son contenu. En outre, la `SetShouldDelayContentTouches` méthode peut être utilisée pour basculer entre les touches de décalage de contenu en appelant la `ShouldDelayContentTouches` méthode pour retourner si les touches de contenu sont retardées :

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

Le résultat est qu’un [`ScrollView`](xref:Xamarin.Forms.ScrollView) peut désactiver le retardement de la réception du contenu, de sorte que dans ce scénario, le [`Slider`](xref:Xamarin.Forms.Slider) reçoit le geste plutôt que la [`Detail`](xref:Xamarin.Forms.FlyoutPage.Detail) page du [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) :

[![ScrollView retard du contenu est spécifique à la plateforme](scrollview-content-touches-images/scrollview-delay-content-touches.png)](scrollview-content-touches-images/scrollview-delay-content-touches-large.png#lightbox "Platform-Specific du contenu de délai ScrollView")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)