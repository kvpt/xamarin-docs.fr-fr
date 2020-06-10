---
title : « vue d’ensemble du contenu mixte sur Android » : «les spécificités de la plateforme vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme Android qui affiche du contenu mixte dans une WebView dans des applications qui ciblent l’API 21 ou une version ultérieure.»
ms. Prod : xamarin ms. AssetID : 68F908F3-04C5-4B91-B6E5-B7E8357B4154 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 07/10/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="webview-mixed-content-on-android"></a>Contenu mixte WebView sur Android

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce contrôle spécifique à la plateforme Android indique si un [`WebView`](xref:Xamarin.Forms.WebView) peut afficher du contenu mixte dans les applications qui ciblent l’API 21 ou une version ultérieure. Le contenu mixte est un contenu qui est initialement chargé sur une connexion HTTPs, mais qui charge des ressources (telles que des images, des fichiers audio, des vidéos, des feuilles de style, des scripts) sur une connexion HTTP. Il est consommé en XAML en affectant [`WebView.MixedContentMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty) à la propriété jointe une valeur de l' [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) énumération :

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

La `WebView.On<Android>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur Android. [ `WebView.SetMixedContentMode` ] (XREF : Xamarin.Forms . PlatformConfiguration. AndroidSpecific. WebView. SetMixedContentMode ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . WebView}, Xamarin.Forms . La méthode PlatformConfiguration. AndroidSpecific. MixedContentHandling)), dans l' [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espace de noms, est utilisée pour contrôler si le contenu mixte peut être affiché, avec l' [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) énumération qui fournit trois valeurs possibles :

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow): indique que le permet [`WebView`](xref:Xamarin.Forms.WebView) à une origine https de charger le contenu à partir d’une origine http.
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow): indique que le [`WebView`](xref:Xamarin.Forms.WebView) n’autorise pas une origine HTTPS à charger le contenu à partir d’une origine http.
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode): indique que le [`WebView`](xref:Xamarin.Forms.WebView) tentera d’être compatible avec l’approche de la dernière version du navigateur Web de l’appareil. Certains contenus HTTP peuvent être autorisés à être chargés par une origine HTTPs et d’autres types de contenu seront bloqués. Les types de contenu bloqués ou autorisés peuvent changer avec chaque version du système d’exploitation.

Le résultat est qu’une [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) valeur spécifiée est appliquée au [`WebView`](xref:Xamarin.Forms.WebView) , qui contrôle si le contenu mixte peut être affiché :

[![WebView gestion du contenu mixte spécifique à la plateforme](webview-mixed-content-images/webview-mixedcontent.png "WebView gestion du contenu mixte spécifique à la plateforme")](webview-mixed-content-images/webview-mixedcontent-large.png#lightbox "WebView gestion du contenu mixte spécifique à la plateforme")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
