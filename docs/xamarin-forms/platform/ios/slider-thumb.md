---
titre : « robinet curseur sur iOS » Description : «les caractéristiques spécifiques à la plateforme vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui permet la définition de la propriété Slider. value en appuyant sur la barre du curseur.
ms. Prod : xamarin ms. AssetID : D0915D37-9A59-4728-BB6A-FE094A661275 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 10/24/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="slider-thumb-tap-on-ios"></a>Robinet curseur sur iOS

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme iOS permet [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) de définir la propriété en appuyant sur une position sur la [`Slider`](xref:Xamarin.Forms.Slider) barre, plutôt qu’en faisant glisser le `Slider` curseur de défilement. Il est consommé en XAML en affectant [`Slider.UpdateOnTap`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.UpdateOnTapProperty) à la propriété pouvant être liée la valeur `true` :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Slider ... ios:Slider.UpdateOnTap="true" />
        ...
    </StackLayout>
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var slider = new Xamarin.Forms.Slider();
slider.On<iOS>().SetUpdateOnTap(true);
```

La `Slider.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. [ `Slider.SetUpdateOnTap` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. Slider. SetUpdateOnTap ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . La méthode Slider}, System. Boolean)), dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, permet de contrôler si une pression sur la `Slider` barre définit la [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) propriété. En outre, le [ `Slider.GetUpdateOnTap` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. Slider. GetUpdateOnTap ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Slider}))), vous pouvez utiliser la méthode pour retourner si une pression sur la `Slider` barre définit la `Slider.Value` propriété.

Le résultat est qu’un tap sur la [`Slider`](xref:Xamarin.Forms.Slider) barre peut déplacer le `Slider` curseur et définir la [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) propriété :

![](slider-thumb-images/slider-updateontap.png "Slider Update on Tap enabled")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
