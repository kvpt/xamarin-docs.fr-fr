---
title: Prise en main avec DataPages
description: Cet article explique comment prendre en main la création d’une page simple pilotée par les données à l’aide de Xamarin.Forms DataPages.
ms.prod: xamarin
ms.assetid: 6416E5FA-6384-4298-BAA1-A89381E47210
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bb08d359048d53639a700cc5ff526f26d6b077b6
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84571465"
---
# <a name="getting-started-with-datapages"></a>Prise en main avec DataPages

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)

![](~/media/shared/preview.png "This API is currently in preview")

> [!IMPORTANT]
> DataPages requiert une Xamarin.Forms référence de thème à restituer. Cela implique l’installation de [ Xamarin.Forms . Package. base](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/) NuGet dans votre projet, suivi de l’un ou l’autre [ Xamarin.Forms . Theme. Light](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/) ou [ Xamarin.Forms . Packages NuGet Theme. Dark](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/) .

Pour commencer à créer une page simple pilotée par les données à l’aide de la version préliminaire de DataPages, suivez les étapes ci-dessous. Cette démonstration utilise un style codé en dur (« Events ») dans la version préliminaire des builds qui fonctionne uniquement avec le format JSON spécifique dans le code.

[![](get-started-images/demo-sml.png "DataPages Sample Application")](get-started-images/demo.png#lightbox "DataPages Sample Application")

## <a name="1-add-nuget-packages"></a>1. ajouter des packages NuGet

Ajoutez ces packages NuGet à votre Xamarin.Forms bibliothèque .NET standard et à vos projets d’application :

- Xamarin.Forms. Pages
- Xamarin.Forms. Thème. base
- Une implémentation de thème NuGet (par exemple, Xamarin.Forms. Theme. Light)

## <a name="2-add-theme-reference"></a>2. Ajouter une référence de thème

Dans le fichier **app. Xaml** , ajoutez un personnalisé `xmlns:mytheme` pour le thème et assurez-vous que le thème est fusionné dans le dictionnaire de ressources de l’application :

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
  xmlns:mytheme="clr-namespace:Xamarin.Forms.Themes;assembly=Xamarin.Forms.Theme.Light"
  x:Class="DataPagesDemo.App">
    <Application.Resources>
        <ResourceDictionary MergedWith="mytheme:LightThemeResources" />
    </Application.Resources>
</Application>
```

> [!IMPORTANT]
> Vous devez également suivre les étapes de [chargement des assemblys de thème (ci-dessous)](#troubleshooting) en ajoutant du code réutilisable aux appareils iOS `AppDelegate` et Android `MainActivity` . Cela sera amélioré dans une future version préliminaire.

## <a name="3-add-a-xaml-page"></a>3. Ajouter une page XAML

Ajoutez une nouvelle page XAML à l' Xamarin.Forms application et *Remplacez la classe de base* par `ContentPage` `Xamarin.Forms.Pages.ListDataPage` . Cela doit être fait à la fois en C# et en XAML :

**Fichier C#**

```csharp
public partial class SessionDataPage : Xamarin.Forms.Pages.ListDataPage // was ContentPage
{
  public SessionDataPage ()
  {
    InitializeComponent ();
  }
}
```

**Fichier XAML**

En plus de remplacer l’élément racine `<p:ListDataPage>` par l’espace de noms personnalisé pour `xmlns:p` doit également être ajouté :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<p:ListDataPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:p="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
             x:Class="DataPagesDemo.SessionDataPage">

    <ContentPage.Content></ContentPage.Content>

</p:ListDataPage>
```

**Sous-classe d’application**

Modifiez le `App` constructeur de classe afin que le `MainPage` soit défini sur un `NavigationPage` qui contient le nouveau `SessionDataPage` . Une page de navigation *doit* être utilisée.

```csharp
MainPage = new NavigationPage (new SessionDataPage ());
```

## <a name="3-add-the-datasource"></a>3. Ajoutez la source de source

Supprimez l' `Content` élément et remplacez-le par un `p:ListDataPage.DataSource` pour remplir la page avec des données. Dans l’exemple ci-dessous, un fichier de données JSON distant est chargé à partir d’une URL.

> [!NOTE]
> La version préliminaire *requiert* un `StyleClass` attribut pour fournir des indicateurs de rendu pour la source de données. Le `StyleClass="Events"` fait référence à une disposition prédéfinie dans l’aperçu et contient des styles *codés en dur* pour correspondre à la source de données JSON utilisée.

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<p:ListDataPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:p="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
             x:Class="DataPagesDemo.SessionDataPage"
             Title="Sessions" StyleClass="Events">

    <p:ListDataPage.DataSource>
        <p:JsonDataSource Source="http://demo3143189.mockable.io/sessions" />
    </p:ListDataPage.DataSource>

</p:ListDataPage>
```

**Données JSON**

Voici un exemple de données JSON provenant de la source de la démonstration :

```json
[{
  "end": "2016-04-27T18:00:00Z",
  "start": "2016-04-27T17:15:00Z",
  "abstract": "The new Apple TV has been released, and YOU can be one of the first developers to write apps for it. To make things even better, you can build these apps in C#! This session will introduce the basics of how to create a tvOS app with Xamarin, including: differences between tvOS and iOS APIs, TV user interface best practices, responding to user input, as well as the capabilities and limitations of building apps for a television. Grab some popcorn—this is going to be good!",
  "title": "As Seen On TV … Bringing C# to the Living Room",
  "presenter": "Matthew Soucoup",
  "biography": "Matthew is a Xamarin MVP and Certified Xamarin Developer from Madison, WI. He founded his company Code Mill Technologies and started the Madison Mobile .Net Developers Group.  Matt regularly speaks on .Net and Xamarin development at user groups, code camps and conferences throughout the Midwest. Matt gardens hot peppers, rides bikes, and loves Wisconsin micro-brews and cheese.",
  "image": "http://i.imgur.com/ASj60DP.jpg",
  "avatar": "http://i.imgur.com/ASj60DP.jpg",
  "room": "Crick"
}]
```

## <a name="4-run"></a>4. Exécutez !

Les étapes ci-dessus doivent aboutir à une page de données de travail :

[![](get-started-images/demo-sml.png "DataPages Sample Application")](get-started-images/demo.png#lightbox "DataPages Sample Application")

Cela fonctionne parce que le style prédéfini **« événements »** existe dans le package NuGet de thème clair et que des styles définis correspondent à la source de données (par exemple, "title", "image", "presenter").

Le « Events » `StyleClass` est créé pour afficher le `ListDataPage` contrôle avec un `CardView` contrôle personnalisé défini dans Xamarin.Forms . Pages. Le `CardView` contrôle a trois propriétés : `ImageSource` , `Text` et `Detail` . Le thème est codé en dur pour lier les trois champs de la source de source (à partir du fichier JSON) à ces propriétés pour l’affichage.

## <a name="5-customize"></a>5. personnaliser

Le style hérité peut être substitué en spécifiant un modèle et en utilisant des liaisons de source de données. Le code XAML ci-dessous déclare un modèle personnalisé pour chaque ligne à l’aide de la nouvelle `ListItemControl` syntaxe et, `{p:DataSourceBinding}` incluse dans le ** Xamarin.Forms . Pages** NuGet :

```xaml
<p:ListDataPage.DefaultItemTemplate>
    <DataTemplate>
        <ViewCell>
            <p:ListItemControl
                Title="{p:DataSourceBinding title}"
                Detail="{p:DataSourceBinding room}"
                ImageSource="{p:DataSourceBinding image}"
                DataSource="{Binding Value}"
                HeightRequest="90"
            >
            </p:ListItemControl>
        </ViewCell>
    </DataTemplate>
</p:ListDataPage.DefaultItemTemplate>
```

En fournissant un `DataTemplate` , ce code remplace `StyleClass` et utilise à la place la disposition par défaut pour un `ListItemControl` .

[![](get-started-images/custom-sml.png "DataPages Sample Application")](get-started-images/custom.png#lightbox "DataPages Sample Application")

Les développeurs qui préfèrent C# à XAML peuvent créer des liaisons de source de données (n’oubliez pas d’inclure une `using Xamarin.Forms.Pages;` instruction) :

```csharp
SetBinding (TitleProperty, new DataSourceBinding ("title"));
```

Il s’agit d’un peu plus de travail pour créer des thèmes de toutes pièces, mais les futures versions préliminaires faciliteront cette tâche.

## <a name="troubleshooting"></a>Dépannage

## <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>Impossible de charger le fichier ou l’assembly Xamarin.Forms . Theme. Light» ou l’une de ses dépendances

Dans la version préliminaire, les thèmes peuvent ne pas pouvoir être chargés au moment de l’exécution. Ajoutez le code indiqué ci-dessous dans les projets appropriés pour corriger cette erreur.

**iOS**

Dans le **AppDelegate.cs** , ajoutez les lignes suivantes après`LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

Dans le **MainActivity.cs** , ajoutez les lignes suivantes après`LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```

## <a name="related-links"></a>Liens connexes

- [Exemple DataPagesDemo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)
