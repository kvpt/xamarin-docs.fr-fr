---
title: Mise en route avec les pages de données
description: Cet article explique comment commencer à créer une page simple piloté par les données à l’aide des pages de données de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 6416E5FA-6384-4298-BAA1-A89381E47210
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 1f7917784ea66c31979b87f43639a7d03756692c
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292356"
---
# <a name="getting-started-with-datapages"></a>Mise en route avec les pages de données

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)

![](~/media/shared/preview.png "This API is currently in preview")

> [!IMPORTANT]
> DataPages requiert une référence de thème Xamarin. Forms à restituer. Cela implique d’installer le package NuGet [Xamarin. Forms. Theme. base](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/) dans votre projet, suivi des packages NuGet [Xamarin. Forms. Theme. Light](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/) ou [Xamarin. Forms. Theme. Dark](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/) .

Pour commencer la création d’une page simple piloté par les données à l’aide de l’aperçu de pages de données, suivez les étapes ci-dessous. Ce utilise démonstration un style codée en dur (« événements ») dans la version préliminaire de builds qui fonctionne uniquement avec le format JSON spécifique dans le code.

[![](get-started-images/demo-sml.png "DataPages Sample Application")](get-started-images/demo.png#lightbox "DataPages Sample Application")

## <a name="1-add-nuget-packages"></a>1. ajouter des packages NuGet

Ajoutez ces packages NuGet à vos projets de bibliothèque et d’application .NET Standard Xamarin. Forms :

- Xamarin.Forms.Pages
- Xamarin.Forms.Theme.Base
- Une implémentation de thème NuGet (par exemple, Xamarin.Forms.Theme.Light)

## <a name="2-add-theme-reference"></a>2. Ajouter une référence de thème

Dans le fichier **app. Xaml** , ajoutez un `xmlns:mytheme` personnalisé pour le thème et assurez-vous que le thème est fusionné dans le dictionnaire de ressources de l’application :

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
> Vous devez également suivre les étapes de [chargement des assemblys de thème (ci-dessous)](#loadtheme) en ajoutant du code réutilisable au `AppDelegate` iOS et aux `MainActivity`Android. Cela sera améliorée dans une version de la prochaine version d’évaluation.

## <a name="3-add-a-xaml-page"></a>3. Ajouter une page XAML

Ajoutez une nouvelle page XAML à l’application Xamarin. Forms, puis *Remplacez la classe de base* `ContentPage` par `Xamarin.Forms.Pages.ListDataPage`. Cela doit être effectuée dans le c# et le XAML :

**C#txt**

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

Outre la modification de l’élément racine en `<p:ListDataPage>` l’espace de noms personnalisé pour `xmlns:p` doit également être ajouté :

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

Modifiez le constructeur de classe `App` afin que le `MainPage` soit défini sur un `NavigationPage` contenant le nouvel `SessionDataPage`. Une page de navigation *doit* être utilisée.

```csharp
MainPage = new NavigationPage (new SessionDataPage ());
```

## <a name="3-add-the-datasource"></a>3. Ajoutez la source de source

Supprimez l’élément `Content` et remplacez-le par un `p:ListDataPage.DataSource` pour remplir la page avec des données. Fichier de données est chargé dans l’exemple ci-dessous Json à distance à partir d’une URL.

> [!NOTE]
> La version préliminaire *requiert* un attribut `StyleClass` pour fournir des indicateurs de rendu pour la source de données. Le `StyleClass="Events"` fait référence à une disposition prédéfinie dans l’aperçu et contient des styles *codés en dur* pour correspondre à la source de données JSON utilisée.

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

Les étapes ci-dessus doivent provoquer une page de données de travail :

[![](get-started-images/demo-sml.png "DataPages Sample Application")](get-started-images/demo.png#lightbox "DataPages Sample Application")

Cela fonctionne parce que le style prédéfini **« événements »** existe dans le package NuGet de thème clair et que des styles définis correspondent à la source de données (par exemple, « title », « image », « présentateur »).

La `StyleClass` « Events » est créée pour afficher le contrôle `ListDataPage` avec un contrôle `CardView` personnalisé défini dans Xamarin. Forms. pages. Le contrôle `CardView` possède trois propriétés : `ImageSource`, `Text`et `Detail`. Le thème est codé en dur pour lier trois champs la source de données (à partir du fichier JSON) à ces propriétés pour l’affichage.

## <a name="5-customize"></a>5. personnaliser

Le style hérité peut être remplacé en spécifiant un modèle et à l’aide de liaisons de sources de données. Le code XAML ci-dessous déclare un modèle personnalisé pour chaque ligne à l’aide de la nouvelle `ListItemControl` et `{p:DataSourceBinding}` syntaxe qui est incluse dans le NuGet **Xamarin. Forms. pages** :

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

En fournissant une `DataTemplate` ce code remplace le `StyleClass` et utilise à la place la disposition par défaut d’un `ListItemControl`.

[![](get-started-images/custom-sml.png "DataPages Sample Application")](get-started-images/custom.png#lightbox "DataPages Sample Application")

Les développeurs qui C# préfèrent XAML peuvent créer des liaisons de source de données (n’oubliez pas d’inclure une instruction `using Xamarin.Forms.Pages;`) :

```csharp
SetBinding (TitleProperty, new DataSourceBinding ("title"));
```

Il s’agit d’un peu plus de travail pour créer des thèmes de toutes pièces, mais les futures versions préliminaires faciliteront cette tâche.

## <a name="troubleshooting"></a>Dépannage

<a name="loadtheme" />

## <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>Impossible de charger le fichier ou l’assembly 'Xamarin.Forms.Theme.Light' ou une de ses dépendances

Dans la version préliminaire, thèmes n’est peut-être pas en mesure de charger lors de l’exécution. Ajoutez le code ci-dessous dans les projets appropriés pour corriger cette erreur.

**iOS**

Dans le **AppDelegate.cs** , ajoutez les lignes suivantes après `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

Dans le **MainActivity.cs** , ajoutez les lignes suivantes après `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```

## <a name="related-links"></a>Liens connexes

- [Exemple DataPagesDemo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)
