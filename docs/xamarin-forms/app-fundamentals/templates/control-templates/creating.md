---
title: Créer un ControlTemplate
description: Les modèles de contrôle peuvent être définis au niveau de l’application ou de la page. Cet article montre comment créer et utiliser des modèles de contrôle.
ms.prod: xamarin
ms.assetid: A9AEB052-FBF5-4589-9BD4-6D6F62BED7F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/14/2019
ms.openlocfilehash: fbc966fdf1d79ecc9794d2156db81b583694ce36
ms.sourcegitcommit: 84764b9c51e769d6d6570a362af8451607c7e0d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68665685"
---
# <a name="create-a-controltemplate"></a>Créer un ControlTemplate

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplates-simpletheme)

_Les modèles de contrôle peuvent être définis au niveau de l’application ou au niveau de la page. Cet article montre comment créer et utiliser des modèles de contrôle._

## <a name="create-a-controltemplate-in-xaml"></a>Créer un ControlTemplate en XAML

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

### <a name="set-a-controltemplate-with-a-style"></a>Définir un ControlTemplate avec un style

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

### <a name="create-a-controltemplate-at-page-level"></a>Créer un ControlTemplate au niveau de la page

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

## <a name="create-a-controltemplate-in-c35"></a>Créer un ControlTemplate in C&#35;

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

## <a name="get-a-named-element-from-a-template"></a>Obtenir un élément nommé à partir d’un modèle

Les éléments nommés dans un modèle de contrôle peuvent être récupérés après que le modèle a été instancié. Ceci est possible avec la méthode `GetTemplateChild`, qui retourne l’élément nommé dans l’arborescence d’éléments visuels instancié [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate).

Après qu’un modèle de contrôle a été instancié, la méthode du modèle `OnApplyTemplate` est appelée. La méthode `GetTemplateChild` doit donc être appelée à partir du remplacement `OnApplyTemplate` dans une page dérivée [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage), telle que [`ContentPage`](xref:Xamarin.Forms.ContentPage) ou une vue dérivée [`TemplatedView`](xref:Xamarin.Forms.TemplatedView), telle que [`ContentView`](xref:Xamarin.Forms.ContentView).

> [!IMPORTANT]
> La méthode `GetTemplateChild` doit uniquement être appelée après que la méthode `OnApplyTemplate` a été appelée.

L’exemple suivant montre le modèle de contrôle pour un contrôle personnalisé :

```xaml
<controls:MyCustomControl ...>
    <controls:MyCustomControl.ControlTemplate>
         <ControlTemplate>
              <Label x:Name="myLabel" />
         </ControlTemplate>
    <controls:MyCustomControl.ControlTemplate>
</controls:MyCustomControl>
```

L’élément [`Label`](xref:Xamarin.Forms.Label) est nommé et peut, par conséquent, être récupéré dans le code-behind pour le contrôle personnalisé. Cela s’effectue en appelant la méthode `GetTemplateChild` à partir du remplacement `OnApplyTemplate` pour le contrôle personnalisé :

```csharp
class MyCustomControl : ContentView
{
    Label myLabel;

    protected override void OnApplyTemplate()
    {  
        myLabel = GetTemplateChild("myLabel");
    }
    //...
}
```

Dans cet exemple, l’objet [`Label`](xref:Xamarin.Forms.Label) nommé `myLabel` est récupéré. `myLabel` est alors accessible et peut ensuite être manipulé par la classe `MyCustomControl`.

## <a name="related-links"></a>Liens connexes

- [Styles](~/xamarin-forms/user-interface/styles/index.md)
- [Thème simple (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplates-simpletheme)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)
- [ContentView](xref:Xamarin.Forms.ContentView)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
