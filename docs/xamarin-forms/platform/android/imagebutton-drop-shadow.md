---
title: Supprimer les ombres sur Android
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme Android qui active une ombre portée sur un ImageButton.
ms.prod: xamarin
ms.assetid: D3604D87-9F9F-4FE2-8B10-DF3B143C0734
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c355816ec1b89801814b217cad8a23be2f453226
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563794"
---
# <a name="imagebutton-drop-shadows-on-android"></a>Supprimer les ombres sur Android

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme Android est utilisé pour activer une ombre portée sur un `ImageButton` . Il est consommé en XAML en affectant `ImageButton.IsShadowEnabled` à la propriété pouvant être liée la valeur `true` , ainsi qu’un certain nombre de propriétés modifiables facultatives supplémentaires qui contrôlent l’ombre portée :

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
       <ImageButton ...
                    Source="XamarinLogo.png"
                    BackgroundColor="GhostWhite"
                    android:ImageButton.IsShadowEnabled="true"
                    android:ImageButton.ShadowColor="Gray"
                    android:ImageButton.ShadowRadius="12">
            <android:ImageButton.ShadowOffset>
                <Size>
                    <x:Arguments>
                        <x:Double>10</x:Double>
                        <x:Double>10</x:Double>
                    </x:Arguments>
                </Size>
            </android:ImageButton.ShadowOffset>
        </ImageButton>
        ...
    </StackLayout>
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var imageButton = new Xamarin.Forms.ImageButton { Source = "XamarinLogo.png", BackgroundColor = Color.GhostWhite, ... };
imageButton.On<Android>()
           .SetIsShadowEnabled(true)
           .SetShadowColor(Color.Gray)
           .SetShadowOffset(new Size(10, 10))
           .SetShadowRadius(12);
```

> [!IMPORTANT]
> Une ombre portée est dessinée dans le cadre de l' `ImageButton` arrière-plan, et l’arrière-plan est uniquement dessiné si la `BackgroundColor` propriété est définie. Par conséquent, une ombre portée n’est pas dessinée si la `ImageButton.BackgroundColor` propriété n’est pas définie.

La `ImageButton.On<Android>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur Android. La `ImageButton.SetIsShadowEnabled` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espace de noms, est utilisée pour contrôler si une ombre portée est activée sur le `ImageButton` . En outre, les méthodes suivantes peuvent être appelées pour contrôler l’ombre portée :

- `SetShadowColor` : définit la couleur de l’ombre portée. La couleur par défaut est [`Color.Default`](xref:Xamarin.Forms.Color.Default*) .
- `SetShadowOffset` : définit le décalage de l’ombre portée. Le décalage change le sens de conversion de l’ombre et est spécifié en tant que [`Size`](xref:Xamarin.Forms.Size) valeur. Les `Size` valeurs de la structure sont exprimées en unités indépendantes du périphérique, avec la première valeur étant la distance à gauche (valeur négative) ou droite (valeur positive), et la deuxième valeur étant la distance au-dessus (valeur négative) ou inférieure (valeur positive). La valeur par défaut de cette propriété est (0,0, 0,0), ce qui entraîne le cast de l’ombre autour de chaque côté de `ImageButton` .
- `SetShadowRadius`: définit le rayon de flou utilisé pour le rendu de l’ombre portée. La valeur par défaut du rayon est 10,0.

> [!NOTE]
> L’état d’une ombre portée peut être interrogé en appelant les `GetIsShadowEnabled` méthodes, `GetShadowColor` , `GetShadowOffset` et `GetShadowRadius` .

Le résultat est qu’une ombre portée peut être activée sur un `ImageButton` :

![ImageButton avec ombre portée](imagebutton-drop-shadow-images/imagebutton-drop-shadow.png)

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)