---
title: Utiliser des données au moment du design avec le générateur d’aperçu XAML
description: Cet article explique comment utiliser les données au moment du design pour afficher des dispositions lourdes en données dans le générateur d’aperçu XAML sans exécuter votre application.
ms.prod: xamarin
ms.assetid: 0F608019-5951-4BE6-80E0-9EEE1733D642
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/27/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4221cd313ca8fbc3f3f9f886e3bd3f774c7472f7
ms.sourcegitcommit: 27f33b0a83b9dc871ad9fb207a1c9960ef197d9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91897945"
---
# <a name="use-design-time-data-with-the-xaml-previewer"></a>Utiliser des données au moment du design avec le générateur d’aperçu XAML

_Certaines dispositions sont difficiles à visualiser sans données. Utilisez ces conseils pour tirer le meilleur parti de l’aperçu de vos pages à données volumineuses dans le générateur d’aperçu XAML._

> [!NOTE]
> Si vous utilisez Windows Presentation Foundation (WPF) ou UWP, consultez [utilisation des données au moment du design avec le concepteur XAML pour les applications de bureau](/visualstudio/xaml-tools/xaml-designtime-data)

## <a name="design-time-data-basics"></a>Notions de base des données au moment du design

Les données au moment de la conception sont des données factices que vous définissez pour faciliter la visualisation de vos contrôles dans le générateur d’aperçu XAML. Pour commencer, ajoutez les lignes de code suivantes à l’en-tête de votre page XAML :

```xaml
xmlns:d="http://xamarin.com/schemas/2014/forms/design"
xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
mc:Ignorable="d"
```

Après avoir ajouté les espaces de noms, vous pouvez placer `d:` devant un attribut ou un contrôle pour l’afficher dans le générateur d’aperçu XAML. Les éléments avec `d:` ne sont pas affichés au moment de l’exécution.

Par exemple, vous pouvez ajouter du texte à une étiquette qui est généralement liée à des données.

```xaml
<Label Text="{Binding Name}" d:Text="Name!" />
```

[![Données au moment de la conception avec du texte dans une étiquette](xaml-previewer-images/designtimedata-label-sm.png "Données au moment de la conception avec texte d’une étiquette")](xaml-previewer-images/designtimedata-label-lg.png#lightbox)

Dans cet exemple, sans `d:Text` , le générateur d’aperçu XAML n’affichera rien pour l’étiquette. Au lieu de cela, il affiche « Name ! » où l’étiquette aura des données réelles au moment de l’exécution.

Vous pouvez utiliser `d:` avec n’importe quel attribut pour un Xamarin.Forms contrôle, comme les couleurs, les tailles de police et l’espacement. Vous pouvez même l’ajouter au contrôle lui-même :

```xaml
<d:Button Text="Design Time Button" />
```

[![Données au moment du design avec un contrôle Button](xaml-previewer-images/designtimedata-controls-sm.png "Données au moment du design avec un contrôle Button")](xaml-previewer-images/designtimedata-controls-lg.png#lightbox)

Dans cet exemple, le bouton s’affiche uniquement au moment du Design. Utilisez cette méthode pour placer un espace réservé dans pour un [contrôle personnalisé non pris en charge par le générateur d’aperçu XAML](render-custom-controls.md).

## <a name="preview-images-at-design-time"></a>Aperçu des images au moment du design

Vous pouvez définir une source au moment de la conception pour les images qui sont liées à la page ou chargées dans dynamiquement. Dans votre projet Android, ajoutez l’image que vous souhaitez afficher dans le générateur d’aperçu XAML aux **ressources > dossier dessinable** . Dans votre projet iOS, ajoutez l’image au dossier **Resources** . Vous pouvez ensuite afficher cette image dans le générateur d’aperçu XAML au moment de la conception :

```xaml
<Image Source={Binding ProfilePicture} d:Source="DesignTimePicture.jpg" />
```

[![Données au moment de la conception avec des images](xaml-previewer-images/designtimedata-image-sm.png "Données au moment de la conception avec iamges")](xaml-previewer-images/designtimedata-image-lg.png#lightbox)

## <a name="design-time-data-for-listviews"></a>Données au moment de la conception pour les ListView

Les ListViews sont un moyen couramment utilisé pour afficher des données dans une application mobile. Toutefois, ils sont difficiles à visualiser sans données réelles. Pour utiliser des données au moment du design avec eux, vous devez créer un tableau au moment de la conception à utiliser comme ItemsSource. Le générateur d’aperçu XAML affiche ce qui se trouve dans ce tableau dans votre ListView au moment du Design.

```xaml
<StackLayout>
    <ListView ItemsSource="{Binding Items}">
        <d:ListView.ItemsSource>
            <x:Array Type="{x:Type x:String}">
                <x:String>Item One</x:String>
                <x:String>Item Two</x:String>
                <x:String>Item Three</x:String>
            </x:Array>
        </d:ListView.ItemsSource>
        <ListView.ItemTemplate>
            <DataTemplate>
                <TextCell Text="{Binding ItemName}"
                          d:Text="{Binding .}" />
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</StackLayout>
```

[![Données au moment de la conception avec un ListView](xaml-previewer-images/designtimedata-itemssource-sm.png "Données au moment de la conception avec un ListView")](xaml-previewer-images/designtimedata-itemssource-lg.png#lightbox)

Cet exemple affiche une liste de trois TextCells dans le générateur d’aperçu XAML. Vous pouvez passer `x:String` à un modèle de données existant dans votre projet.

Vous pouvez également créer un tableau d’objets de données. Par exemple, les propriétés publiques d’un `Monkey` objet de données peuvent être construites en tant que données au moment de la conception :

```csharp
namespace Monkeys.Models
{
    public class Monkey
    {
        public string Name { get; set; }
        public string Location { get; set; }
    }
}
```

Pour utiliser la classe dans XAML, vous devez importer l’espace de noms dans le nœud racine :

```xaml
xmlns:models="clr-namespace:Monkeys.Models"
```

```xaml
<StackLayout>
    <ListView ItemsSource="{Binding Items}">
        <d:ListView.ItemsSource>
            <x:Array Type="{x:Type models:Monkey}">
                <models:Monkey Name="Baboon" Location="Africa and Asia"/>
                <models:Monkey Name="Capuchin Monkey" Location="Central and South America"/>
                <models:Monkey Name="Blue Monkey" Location="Central and East Africa"/>
            </x:Array>
        </d:ListView.ItemsSource>
        <ListView.ItemTemplate>
            <DataTemplate x:DataType="models:Monkey">
                <TextCell Text="{Binding Name}"
                          Detail="{Binding Location}" />
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</StackLayout>
```

L’avantage est que vous pouvez lier le modèle réel que vous prévoyez d’utiliser.

## <a name="alternative-hardcode-a-static-viewmodel"></a>Alternative : coder en dur un ViewModel statique

Si vous ne souhaitez pas ajouter de données au moment de la conception à des contrôles individuels, vous pouvez configurer un magasin de données factices à lier à votre page. Reportez-vous au billet de blog de James Montemagno [sur l’ajout de données au moment](https://montemagno.com/xamarin-forms-design-time-data-tips-best-practices/) de la conception pour voir comment établir une liaison à un ViewModel statique en XAML.

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="requirements"></a>Spécifications

Les données au moment de la conception requièrent une version minimale de Xamarin.Forms 3,6.

### <a name="intellisense-shows-squiggly-lines-under-my-design-time-data"></a>IntelliSense affiche des lignes ondulées sous mes données au moment de la conception

Il s’agit d’un problème connu qui sera résolu dans une prochaine version de Visual Studio. Le projet sera toujours généré sans erreur.

### <a name="the-xaml-previewer-stopped-working"></a>Le générateur d’aperçu XAML a cessé de fonctionner

Essayez de fermer et de rouvrir le fichier XAML, puis de nettoyer et de reconstruire votre projet.
