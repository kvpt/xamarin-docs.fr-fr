---
title : « répertoire d’images par défaut sur Windows » Description : «les spécificités de la plateforme vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme Windows qui définit le répertoire du projet à partir duquel les ressources d’images seront chargées.
ms. Prod : xamarin ms. AssetID : 537A032B-74DD-4D43-864E-7D7113286D0D ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 01/16/2020 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="default-image-directory-on-windows"></a>Répertoire d’images par défaut sur Windows

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce plateforme Windows universelle spécifique à la plateforme définit le répertoire du projet à partir duquel les ressources d’images seront chargées. Il est consommé en XAML en définissant `Application.ImageDirectory` sur un `string` qui représente le répertoire du projet qui contient les ressources de l’image :

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
             ...
             windows:Application.ImageDirectory="Assets">
    ...
</Application>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...
Application.Current.On<Windows>().SetImageDirectory("Assets");
```

La `Application.On<Windows>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur le plateforme Windows universelle. La `Application.SetImageDirectory` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espace de noms, est utilisée pour spécifier le répertoire du projet à partir duquel les images seront chargées. En outre, la `GetImageDirectory` méthode peut être utilisée pour retourner un `string` qui représente le répertoire du projet qui contient les ressources de l’image d’application.

Le résultat est que toutes les images utilisées dans une application sont chargées à partir du répertoire de projet spécifié.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
