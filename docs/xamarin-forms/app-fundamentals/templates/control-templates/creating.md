---
title: Création d’un ControlTemplate
description: Les modèles de contrôle peuvent être définis au niveau de l’application ou de la page. Cet article montre comment créer et utiliser des modèles de contrôle.
ms.prod: xamarin
ms.assetid: A9AEB052-FBF5-4589-9BD4-6D6F62BED7F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: dc26084b94956ea9bc87384e5fdb79695bc8c2b5
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53051780"
---
# <a name="creating-a-controltemplate"></a>Création d’un ControlTemplate

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simpletheme/)

_Les modèles de contrôle peuvent être définis au niveau de l’application ou au niveau de la page. Cet article montre comment créer et utiliser des modèles de contrôle._

## <a name="creating-a-controltemplate-in-xaml"></a>Création d’un ControlTemplate en XAML

Pour définir un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) au niveau de l’application, vous devez ajouter un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) à la classe `App`. Par défaut, toutes les applications Xamarin.Forms créées à partir d’un modèle utilisent la classe **App** pour implémenter la sous-classe [`Application`](xref:Xamarin.Forms.Application). Pour déclarer un `ControlTemplate` au niveau de l’application, dans le `ResourceDictionary` de l’application en XAML, vous devez remplacer la classe **App** par défaut par une classe **App** XAML et par le code-behind associé, comme indiqué dans l’exemple de code suivant :

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.App">
    <Application.Resources>
        <ResourceDictionary>
            <ControlTemplate x:Key="TealTemplate">
                <Grid>
                    ...
                    <BoxView ... />
                    <Label Text="Control Template Demo App"
                           TextColor="White"
                           VerticalOptions="Center" ... />
                    <ContentPresenter ... />
                    <BoxView Color="Teal" ... />
                    <Label Text="(c) Xamarin 2016"
                           TextColor="White"
                           VerticalOptions="Center" ... />
                </Grid>
            </ControlTemplate>
            <ControlTemplate x:Key="AquaTemplate">
                ...
            </ControlTemplate>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

Chaque instance de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) est créée en tant qu’objet réutilisable dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary).  Pour cela, on attribue à chaque déclaration un attribut `x:Key` unique, qui lui fournit une clé descriptive dans le `ResourceDictionary`.

L’exemple de code suivant montre le code-behind `App` associé :

```csharp
public partial class App : Application
{
  public App ()
  {
    InitializeComponent ();
    MainPage = new HomePage ();
  }
}
```

En plus de définir la propriété [`MainPage`](xref:Xamarin.Forms.Application.MainPage), le code-behind doit également appeler la méthode `InitializeComponent` pour charger et analyser le XAML associé.

L’exemple de code suivant montre un [`ContentPage`](xref:Xamarin.Forms.ContentPage) appliquant le `TealTemplate` au [`ContentView`](xref:Xamarin.Forms.ContentView) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentView x:Name="contentView" Padding="0,20,0,0"
                 ControlTemplate="{StaticResource TealTemplate}">
        <StackLayout VerticalOptions="CenterAndExpand">
            <Label Text="Welcome to the app!" HorizontalOptions="Center" />
            <Button Text="Change Theme" Clicked="OnButtonClicked" />
        </StackLayout>
    </ContentView>
</ContentPage>
```

Le `TealTemplate` est affecté à la propriété [`ContentView.ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) à l’aide de l’extension de balisage `StaticResource`. La propriété [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) est définie sur un [`StackLayout`](xref:Xamarin.Forms.StackLayout) qui définit le contenu à afficher sur le [`ContentPage`](xref:Xamarin.Forms.ContentPage). Ce contenu sera affiché par le [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) contenu dans le `TealTemplate`. Cela donne l’affichage illustré dans les captures d’écran suivantes :

![](creating-images/teal-theme.png "Modèle de contrôle bleu-vert")

### <a name="re-theming-an-application-at-runtime"></a>Changement du thème d’application au moment de l’exécution

Un clic sur le bouton **Changer de thème** exécute la méthode `OnButtonClicked`, ce qui est illustré dans l’exemple de code suivant :

```csharp
void OnButtonClicked (object sender, EventArgs e)
{
  originalTemplate = !originalTemplate;
  contentView.ControlTemplate = (originalTemplate) ? tealTemplate : aquaTemplate;
}
```

Cette méthode remplace l’instance active de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) par l’instance alternative de `ControlTemplate`, ce qui génère la capture d’écran suivante :

![](creating-images/aqua-theme.png "Modèle de contrôle Bleu azur")

> [!NOTE]
> Sur un `ContentPage`, la propriété `Content` peut être affectée et la propriété `ControlTemplate` peut également être définie. Quand cela se produit, si le `ControlTemplate` contient une instance de `ContentPresenter`, le contenu affecté à la propriété `Content` est présenté par le `ContentPresenter` dans le `ControlTemplate`.

### <a name="setting-a-controltemplate-with-a-style"></a>Définition d’un ControlTemplate avec un Style

Un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) peut également être appliqué par le biais d’un [`Style`](xref:Xamarin.Forms.Style) afin d’accroître encore davantage la capacité de thème. Pour cela, vous devez créer un style *implicite* ou *explicite* pour la vue cible dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), et définir la propriété `ControlTemplate` de la vue cible dans l’instance de [`Style`](xref:Xamarin.Forms.Style). L’exemple de code suivant montre un style *implicite* qui a été ajouté au [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) au niveau de l’application :

```xaml
<Style TargetType="ContentView">
    <Setter Property="ControlTemplate" Value="{StaticResource TealTemplate}" />
</Style>
```

L’instance de [`Style`](xref:Xamarin.Forms.Style) étant *implicite*, elle sera appliquée à tous les instances de `ContentView` dans l’application. Ainsi, il n’est plus nécessaire de définir la propriété [`ContentView.ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate), comme illustré dans l’exemple de code suivant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentView x:Name="contentView" Padding="0,20,0,0">
      ...
    </ContentView>
</ContentPage>
```

Pour plus d’informations sur les styles, consultez [Styles](~/xamarin-forms/user-interface/styles/index.md).

### <a name="creating-a-controltemplate-at-page-level"></a>Création d’un ControlTemplate au niveau de la page

Vous pouvez également créer des instances de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) au niveau de la page, comme indiqué dans l’exemple de code suivant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentPage.Resources>
        <ResourceDictionary>
            <ControlTemplate x:Key="TealTemplate">
                ...
            </ControlTemplate>
            <ControlTemplate x:Key="AquaTemplate">
                ...
            </ControlTemplate>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
        ...
    </ContentView>
</ContentPage>
```

Quand vous ajoutez un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) au niveau de la page, un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) est ajouté au [`ContentPage`](xref:Xamarin.Forms.ContentPage), puis les instances de `ControlTemplate` sont incluses dans le `ResourceDictionary`.

## <a name="creating-a-controltemplate-in-c35"></a>Création d’un ControlTemplate en C&#35;

Pour définir un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) au niveau de l’application, vous devez créer un `class` qui représente le `ControlTemplate`. La classe doit dériver de la [disposition](~/xamarin-forms/user-interface/layouts/index.md) utilisée pour le modèle, comme illustré dans l’exemple de code suivant :

```csharp
class TealTemplate : Grid
{
  public TealTemplate ()
  {
    ...
    var contentPresenter = new ContentPresenter ();
    Children.Add (contentPresenter, 0, 1);
    Grid.SetColumnSpan (contentPresenter, 2);
    ...
  }
}

class AquaTemplate : Grid
{
  ...
}
```

La classe `AquaTemplate` est identique à la classe `TealTemplate`, à ceci près que des couleurs différentes sont utilisées pour les propriétés [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) et [`Label.TextColor`](xref:Xamarin.Forms.Label.TextColor).

L’exemple de code suivant montre un [`ContentPage`](xref:Xamarin.Forms.ContentPage) appliquant le `TealTemplate` au [`ContentView`](xref:Xamarin.Forms.ContentView) :

```csharp
public class HomePageCS : ContentPage
{
  ...
  ControlTemplate tealTemplate = new ControlTemplate (typeof(TealTemplate));
  ControlTemplate aquaTemplate = new ControlTemplate (typeof(AquaTemplate));

  public HomePageCS ()
  {
    var button = new Button { Text = "Change Theme" };
    var contentView = new ContentView {
      Padding = new Thickness (0, 20, 0, 0),
      Content = new StackLayout {
        VerticalOptions = LayoutOptions.CenterAndExpand,
        Children = {
          new Label { Text = "Welcome to the app!", HorizontalOptions = LayoutOptions.Center },
          button
        }
      },
      ControlTemplate = tealTemplate
    };
    ...
    Content = contentView;
  }
}
```

Les instances de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) sont créées en spécifiant le type des classes qui définissent les modèles de contrôle, dans le constructeur `ControlTemplate`.

La propriété [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) est définie sur un [`StackLayout`](xref:Xamarin.Forms.StackLayout) qui définit le contenu à afficher sur le [`ContentPage`](xref:Xamarin.Forms.ContentPage). Ce contenu sera affiché par le [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) contenu dans le `TealTemplate`. Le même mécanisme que celui décrit précédemment est utilisé pour appliquer le thème `AquaTheme` au moment de l’exécution.

## <a name="summary"></a>Récapitulatif

Vous avez vu dans cet article comment créer et utiliser des modèles de contrôle. Les modèles de contrôle peuvent être définis au niveau de l’application ou de la page.


## <a name="related-links"></a>Liens associés

- [Styles](~/xamarin-forms/user-interface/styles/index.md)
- [Thème simple (exemple)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simpletheme/)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)
- [ContentView](xref:Xamarin.Forms.ContentView)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
