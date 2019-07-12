---
title: Mise en route avec les pages de données
description: Cet article explique comment commencer à créer une page simple piloté par les données à l’aide des pages de données de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 6416E5FA-6384-4298-BAA1-A89381E47210
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: a77ebd8a6c64d2ee44011e8d51977adf88b52be9
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832439"
---
# <a name="getting-started-with-datapages"></a>Mise en route avec les pages de données

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)

![](~/media/shared/preview.png "Cette API est actuellement en version préliminaire")

> [!IMPORTANT]
> Pages de données nécessite un [Xamarin.Forms thème](~/xamarin-forms/user-interface/themes/index.md) référence à restituer.


Pour commencer la création d’une page simple piloté par les données à l’aide de l’aperçu de pages de données, suivez les étapes ci-dessous. Ce utilise démonstration un style codée en dur (« événements ») dans la version préliminaire de builds qui fonctionne uniquement avec le format JSON spécifique dans le code.

[![](get-started-images/demo-sml.png "Exemple de pages de données d’Application")](get-started-images/demo.png#lightbox "exemple des pages de données d’Application")

## <a name="1-add-nuget-packages"></a>1. Ajout de Packages NuGet

Ajoutez ces packages Nuget à vos projets de bibliothèque et d’application Xamarin.Forms .NET Standard :

* Xamarin.Forms.Pages
* Xamarin.Forms.Theme.Base
* Une implémentation de thème Nuget (par ex. Xamarin.Forms.Theme.Light)

## <a name="2-add-theme-reference"></a>2. Ajouter une référence de thème

Dans le **App.xaml** , ajoutez un personnalisé `xmlns:mytheme` pour le thème et vérifiez que le thème est fusionné dans le dictionnaire de ressources de l’application :

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
> Vous devez également suivre les étapes à [charger des assemblys de thème (ci-dessous)](#loadtheme) en ajoutant du code réutilisable à iOS `AppDelegate` et Android `MainActivity`. Cela sera améliorée dans une version de la prochaine version d’évaluation.


## <a name="3-add-a-xaml-page"></a>3. Ajouter une Page XAML

Ajouter une nouvelle page XAML à l’application Xamarin.Forms, et *modifier la classe de base* de `ContentPage` à `Xamarin.Forms.Pages.ListDataPage`. Cela doit être effectuée dans le c# et le XAML :

**Fichier c#**

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

Outre la modification de l’élément racine à `<p:ListDataPage>` l’espace de noms personnalisé `xmlns:p` doivent également être ajoutés :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<p:ListDataPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:p="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
             x:Class="DataPagesDemo.SessionDataPage">

    <ContentPage.Content></ContentPage.Content>

</p:ListDataPage>
```

**Sous-classe de l’application**

Modifier le `App` constructeur de classe afin que le `MainPage` est définie sur une `NavigationPage` contenant le nouveau `SessionDataPage`. Une page de navigation *doit* être utilisé.

```csharp
MainPage = new NavigationPage (new SessionDataPage ());
```

## <a name="3-add-the-datasource"></a>3. Ajouter la source de données

Supprimer le `Content` élément et remplacez-le par un `p:ListDataPage.DataSource` pour remplir la page avec des données. Fichier de données est chargé dans l’exemple ci-dessous Json à distance à partir d’une URL.

> [!NOTE]
> La version préliminaire *requiert* un `StyleClass` attribut pour fournir des indications de rendu pour la source de données. Le `StyleClass="Events"` fait référence à une disposition qui est prédéfinie dans la version préliminaire et contient les styles *codée en dur* pour correspondre à la source de données JSON qui est utilisée.

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

Un exemple des données JSON à partir de la [source demo](http://demo3143189.mockable.io/sessions) est indiqué ci-dessous :

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

## <a name="4-run"></a>4. Exécuter !

Les étapes ci-dessus doivent provoquer une page de données de travail :

[![](get-started-images/demo-sml.png "Exemple de pages de données d’Application")](get-started-images/demo.png#lightbox "exemple des pages de données d’Application")

Cela fonctionne parce que le style prédéfini **« Événements »** existe dans le package Nuget de thème clair et possède les styles définis qui correspondent à la source de données (par exemple). « title », « image », « présentateur »).

Les « événements » `StyleClass` est conçu pour afficher le `ListDataPage` contrôle avec un personnalisé `CardView` contrôle qui est défini dans Xamarin.Forms.Pages. Le `CardView` contrôle possède trois propriétés : `ImageSource`, `Text`, et `Detail`. Le thème est codé en dur pour lier trois champs la source de données (à partir du fichier JSON) à ces propriétés pour l’affichage.

## <a name="5-customize"></a>5. Personnaliser

Le style hérité peut être remplacé en spécifiant un modèle et à l’aide de liaisons de sources de données. Le XAML ci-dessous déclare un modèle personnalisé pour chaque ligne à l’aide de la nouvelle `ListItemControl` et `{p:DataSourceBinding}` syntaxe qui est inclus dans le **Xamarin.Forms.Pages** Nuget :

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

En fournissant un `DataTemplate` ce code remplace le `StyleClass` et utilise à la place de la disposition par défaut pour un `ListItemControl`.

[![](get-started-images/custom-sml.png "Exemple de pages de données d’Application")](get-started-images/custom.png#lightbox "exemple des pages de données d’Application")

Les développeurs qui préfèrent c# pour XAML peuvent créer des données de source de liaisons trop (n’oubliez pas d’inclure un `using Xamarin.Forms.Pages;` instruction) :

```csharp
SetBinding (TitleProperty, new DataSourceBinding ("title"));
```


Il s’agit d’un peu plus de travail pour créer des thèmes à partir de zéro (consultez le [guide de thèmes](~/xamarin-forms/user-interface/themes/index.md)), mais les versions préliminaires de futurs rend cela plus facile à faire.


## <a name="troubleshooting"></a>Résolution des problèmes

<a name="loadtheme" />

## <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>Impossible de charger le fichier ou l’assembly 'Xamarin.Forms.Theme.Light' ou une de ses dépendances

Dans la version préliminaire, thèmes n’est peut-être pas en mesure de charger lors de l’exécution. Ajoutez le code ci-dessous dans les projets appropriés pour corriger cette erreur.

**iOS**

Dans le **AppDelegate.cs** ajoutez les lignes suivantes après `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

Dans le **MainActivity.cs** ajoutez les lignes suivantes après `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```



## <a name="related-links"></a>Liens associés

- [Exemple de DataPagesDemo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)
