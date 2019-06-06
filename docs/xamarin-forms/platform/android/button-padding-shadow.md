---
title: Bouton remplissage et les ombres sur Android
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser l’Android spécifique à la plateforme qui utilise la marge intérieure par défaut et les valeurs de clichés instantanés des boutons Android.
ms.prod: xamarin
ms.assetid: BD2B60D1-DE6E-4691-A777-8EC5F560A4E9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: bcdea14ba6a4a9a09a7b35db67794709d6d36c4c
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66741047"
---
# <a name="button-padding-and-shadows-on-android"></a>Bouton remplissage et les ombres sur Android

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

Cette spécifiques à la plateforme Android contrôle si les boutons de Xamarin.Forms utilisent la marge intérieure par défaut et les valeurs de clichés instantanés des boutons Android. Elle est consommée dans XAML en définissant le [ `Button.UseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPaddingProperty) et [ `Button.UseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadowProperty) propriétés jointes à `boolean` valeurs :

```xaml
<ContentPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button ...
                android:Button.UseDefaultPadding="true"
                android:Button.UseDefaultShadow="true" />         
    </StackLayout>
</ContentPage>
```

Vous pouvez également, il peut être consommé à partir de C# à l’aide de l’API fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

button.On<Android>().SetUseDefaultPadding(true).SetUseDefaultShadow(true);
```

Le `Button.On<Android>` méthode spécifie que cette plateforme spécifique s’exécute uniquement sur Android. Le [ `Button.SetUseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.SetUseDefaultPadding(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button},System.Boolean)) et [ `Button.SetUseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.SetUseDefaultShadow(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button},System.Boolean)) méthodes, dans le [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espace de noms, permettent de contrôler si les boutons de Xamarin.Forms utilisent la valeur par défaut marge intérieure et les valeurs de clichés instantanés des boutons Android. En outre, le [ `Button.UseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPadding(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button})) et [ `Button.UseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadow(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button})) méthodes peuvent être utilisées pour retourner si un bouton utilise la valeur par défaut de remplissage de valeur et la valeur de clichés instantanés par défaut, respectivement.

Le résultat est que les boutons de Xamarin.Forms pouvez utiliser la marge intérieure par défaut et les valeurs de clichés instantanés des boutons Android :

![](button-padding-shadow-images/button-padding-and-shadow.png "Marge intérieure par défaut et les valeurs de clichés instantanés sur les boutons Android")

Notez que dans la capture d’écran au-dessus de chaque [ `Button` ](xref:Xamarin.Forms.Button) a des définitions identiques, à ceci près que le droit `Button` utilise la marge intérieure par défaut et les valeurs de clichés instantanés des boutons Android.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
