---
title : « Button padding and Shadows on Android » Description : «la plateforme spécifique vous permet d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme Android qui utilise les valeurs de remplissage par défaut et les valeurs Shadow des boutons Android.
ms. Prod : xamarin ms. AssetID : BD2B60D1-DE6E-4691-A777-8EC5F560A4E9 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 07/10/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="button-padding-and-shadows-on-android"></a>Remplissage de bouton et ombres sur Android

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce contrôle spécifique à la plateforme Android indique si Xamarin.Forms les boutons utilisent les valeurs de remplissage par défaut et les valeurs Shadow des boutons Android. Il est consommé en XAML en affectant [`Button.UseDefaultPadding`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPaddingProperty) [`Button.UseDefaultShadow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadowProperty) aux propriétés jointes la valeur `boolean` :

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

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

button.On<Android>().SetUseDefaultPadding(true).SetUseDefaultShadow(true);
```

La `Button.On<Android>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur Android. [ `Button.SetUseDefaultPadding` ] (XREF : Xamarin.Forms . PlatformConfiguration. AndroidSpecific. Button. SetUseDefaultPadding ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Button}, System. Boolean)) et [ `Button.SetUseDefaultShadow` ] (XREF : Xamarin.Forms . PlatformConfiguration. AndroidSpecific. Button. SetUseDefaultShadow ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Button}, System. Boolean)), dans l' [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espace de noms, sont utilisés pour contrôler si Xamarin.Forms les boutons utilisent les valeurs de remplissage par défaut et les valeurs Shadow des boutons Android. En outre, le [ `Button.UseDefaultPadding` ] (XREF : Xamarin.Forms . PlatformConfiguration. AndroidSpecific. Button. UseDefaultPadding ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Button})) et [ `Button.UseDefaultShadow` ] (XREF : Xamarin.Forms . PlatformConfiguration. AndroidSpecific. Button. UseDefaultShadow ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Button})). vous pouvez utiliser les méthodes pour retourner une valeur indiquant si un bouton utilise la valeur de remplissage par défaut et la valeur d’ombre par défaut, respectivement.

Le résultat est que les Xamarin.Forms boutons peuvent utiliser les valeurs de remplissage par défaut et les valeurs d’ombre des boutons Android :

![](button-padding-shadow-images/button-padding-and-shadow.png "Default Padding and Shadow Values on Android Buttons")

Notez que, dans la capture d’écran ci-dessus [`Button`](xref:Xamarin.Forms.Button) , chacune a des définitions identiques, sauf que la main droite `Button` utilise les valeurs de remplissage par défaut et les valeurs Shadow des boutons Android.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
