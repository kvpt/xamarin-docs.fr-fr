---
title : « RefreshView pull direction on Windows » Description : «la plateforme spécifique vous permet d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plate-forme Windows, qui permet de modifier la direction d’extraction d’un RefreshView.»
ms. Prod : xamarin ms. AssetID : 407A862B-281E-4384-9696-C0655830B84D ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 09/20/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="refreshview-pull-direction-on-windows"></a>Direction d’extraction de RefreshView sur Windows

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Cette plateforme Windows universelle spécifique à la plateforme permet de modifier la direction d’extraction d’un pour `RefreshView` qu’elle corresponde à l’orientation du contrôle à défilement qui affiche des données. Il est consommé en XAML en affectant `RefreshView.RefreshPullDirection` à la propriété pouvant être liée la valeur de l' `RefreshPullDirection` énumération :

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <RefreshView windows:RefreshView.RefreshPullDirection="LeftToRight"
                 IsRefreshing="{Binding IsRefreshing}"
                 Command="{Binding RefreshCommand}">
        <ScrollView>
            ...
        </ScrollView>
    </RefreshView>
 </ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...
refreshView.On<Windows>().SetRefreshPullDirection(RefreshPullDirection.LeftToRight);
```

La `RefreshView.On<Windows>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur le plateforme Windows universelle. La `RefreshView.SetRefreshPullDirection` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espace de noms, est utilisée pour définir la direction de l’extraction de `RefreshView` , avec l' `RefreshPullDirection` énumération qui fournit quatre valeurs possibles :

- `LeftToRight`indique qu’une opération d’extraction de gauche à droite lance une actualisation.
- `TopToBottom`indique qu’une opération d’extraction de haut en bas lance une actualisation et est la direction d’extraction par défaut d’un `RefreshView` .
- `RightToLeft`indique qu’une opération d’extraction de droite à gauche lance une actualisation.
- `BottomToTop`indique qu’une opération pull de bas en haut lance une actualisation.

En outre, la `GetRefreshPullDirection` méthode peut être utilisée pour retourner le actuel `RefreshPullDirection` du `RefreshView` .

Le résultat est qu’un spécifié `RefreshPullDirection` est appliqué au `RefreshView` , afin de définir la direction de l’extraction de sorte qu’elle corresponde à l’orientation du contrôle de défilement qui affiche des données. La capture d’écran suivante montre un `RefreshView` avec une `LeftToRight` direction d’extraction :

[![Capture d’écran d’un RefreshView avec une direction de glissement de gauche à droite, sur UWP](refreshview-pulldirection-images/refreshview-pulldirection.png "RefreshView avec direction de l’extraction de gauche à droite")](refreshview-pulldirection-images/refreshview-pulldirection-large.png#lightbox "RefreshView avec direction de l’extraction de gauche à droite")

> [!NOTE]
> Lorsque vous modifiez la direction de l’extraction, la position de départ du cercle de progression pivote automatiquement de sorte que la flèche commence à la position appropriée pour la direction d’extraction.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
