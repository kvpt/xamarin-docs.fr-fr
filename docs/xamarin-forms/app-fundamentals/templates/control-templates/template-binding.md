---
title: Liaison à partir d’un ControlTemplate Xamarin.Forms
description: Les liaisons de modèle permettent aux contrôles dans un modèle de contrôle de lier aux données des propriétés publiques et ainsi permettre de modifier facilement les valeurs de propriété sur les contrôles du modèle de contrôle. Cet article montre comment utiliser les liaisons de modèle pour effectuer une liaison de données à partir d’un modèle de contrôle.
ms.prod: xamarin
ms.assetid: 794A663C-3A8D-438A-BD02-8E97C919B55F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 41e5bbc42ccde5cdd5223a7d2cb0a77da66e10c1
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647006"
---
# <a name="binding-from-a-xamarinforms-controltemplate"></a>Liaison à partir d’un ControlTemplate Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplates-simplethemewithtemplatebinding)

_Les liaisons de modèle permettent aux contrôles dans un modèle de contrôle de lier aux données des propriétés publiques et ainsi permettre de modifier facilement les valeurs de propriété sur les contrôles du modèle de contrôle. Cet article montre comment utiliser les liaisons de modèle pour effectuer une liaison de données à partir d’un modèle de contrôle._

Un [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) est utilisé pour lier la propriété d’un contrôle dans un modèle de contrôle à une propriété pouvant être liée sur le parent de la vue *cible* propriétaire du modèle de contrôle. Par exemple, au lieu de définir le texte affiché par des instances de [`Label`](xref:Xamarin.Forms.Label) à l’intérieur du [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate), vous pouvez utiliser une liaison de modèle pour lier la propriété [`Label.Text`](xref:Xamarin.Forms.Label.Text) à des propriétés pouvant être liées qui définissent le texte à afficher.

Un [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) est similaire à un [`Binding`](xref:Xamarin.Forms.Binding), sauf que la *source* d’un `TemplateBinding` est toujours automatiquement définie sur le parent de la vue *cible* propriétaire du modèle de contrôle. Toutefois, notez que l’utilisation d’un `TemplateBinding` en dehors d’un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) n’est pas prise en charge.

## <a name="creating-a-templatebinding-in-xaml"></a>Création d’un objet TemplateBinding en XAML

En XAML, vous créez un [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) à l’aide de l’extension de balisage [`TemplateBinding`](xref:Xamarin.Forms.Xaml.TemplateBindingExtension), comme illustré dans l’exemple de code suivant :

```xaml
<ControlTemplate x:Key="TealTemplate">
  <Grid>
    ...
    <Label Text="{TemplateBinding Parent.HeaderText}" ... />
    ...
    <Label Text="{TemplateBinding Parent.FooterText}" ... />
  </Grid>
</ControlTemplate>
```

Au lieu de définir les propriétés [`Label.Text`](xref:Xamarin.Forms.Label.Text) comme texte statique, vous pouvez faire en sorte que les propriétés utilisent des liaisons de modèles afin d’établir une liaison à des propriétés pouvant être liées sur le parent de la vue *cible* propriétaire du [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). Toutefois, notez que les liaisons de modèles établissent une liaison à `Parent.HeaderText` et `Parent.FooterText`, plutôt que `HeaderText` et `FooterText`. En effet, dans cet exemple, les propriétés pouvant être liées sont définies sur le grand-parent de la vue *cible*, plutôt que sur le parent, comme illustré dans l’exemple de code suivant :

```xaml
<ContentPage ...>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
          ...
    </ContentView>
</ContentPage>
```

La *source* de la liaison de modèle est définie automatiquement sur le parent de la vue *cible* propriétaire du modèle de contrôle, qui est ici l’instance de [`ContentView`](xref:Xamarin.Forms.ContentView). La liaison de modèle utilise la propriété [`Parent`](xref:Xamarin.Forms.Element.Parent) pour retourner l’élément parent de l’instance de `ContentView`, qui est l’instance de [`ContentPage`](xref:Xamarin.Forms.ContentPage). Par conséquent, l’utilisation d’un [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) dans le [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) pour établir une liaison à `Parent.HeaderText` et à `Parent.FooterText` localise les propriétés pouvant être liées qui sont définies sur le `ContentPage`, comme illustré dans l’exemple de code suivant :

```csharp
public static readonly BindableProperty HeaderTextProperty =
  BindableProperty.Create ("HeaderText", typeof(string), typeof(HomePage), "Control Template Demo App");
public static readonly BindableProperty FooterTextProperty =
  BindableProperty.Create ("FooterText", typeof(string), typeof(HomePage), "(c) Xamarin 2016");

public string HeaderText {
  get { return (string)GetValue (HeaderTextProperty); }
}

public string FooterText {
  get { return (string)GetValue (FooterTextProperty); }
}
```

Cela donne l’affichage illustré dans les captures d’écran suivantes :

![](template-binding-images/teal-theme.png "Modèle de contrôle bleu-vert utilisant des liaisons de modèle")

## <a name="creating-a-templatebinding-in-c35"></a>Création d’un objet TemplateBinding en C&#35;

En C#, vous créez un [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) à l’aide du constructeur `TemplateBinding`, comme illustré dans l’exemple de code suivant :

```csharp
class TealTemplate : Grid
{
  public TealTemplate ()
  {
    ...
    var topLabel = new Label { TextColor = Color.White, VerticalOptions = LayoutOptions.Center };
    topLabel.SetBinding (Label.TextProperty, new TemplateBinding ("Parent.HeaderText"));
    ...
    var bottomLabel = new Label { TextColor = Color.White, VerticalOptions = LayoutOptions.Center };
    bottomLabel.SetBinding (Label.TextProperty, new TemplateBinding ("Parent.FooterText"));
    ...
  }
}
```

Au lieu de définir les propriétés [`Label.Text`](xref:Xamarin.Forms.Label.Text) comme texte statique, vous pouvez faire en sorte que les propriétés utilisent des liaisons de modèles afin d’établir une liaison à des propriétés pouvant être liées sur le parent de la vue *cible* propriétaire du [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). La liaison de modèle est créée à l’aide de la méthode [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)), en spécifiant une instance de [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) comme second paramètre. Notez que les liaisons de modèle établissent une liaison à `Parent.HeaderText` et `Parent.FooterText`, car les propriétés pouvant être liées sont définies sur le grand-parent de la vue *cible*, plutôt que sur le parent, comme illustré dans l’exemple de code suivant :

```csharp
public class HomePageCS : ContentPage
{
  ...
  public HomePageCS ()
  {
    Content = new ContentView {
      ControlTemplate = tealTemplate,
      Content = new StackLayout {
        ...
      },
      ...
    };
    ...
  }
}
```

Les propriétés pouvant être liées sont définies sur le `ContentPage`, comme indiqué précédemment.

### <a name="binding-a-bindableproperty-to-a-viewmodel-property"></a>Liaison d’un BindableProperty à une propriété ViewModel

Comme indiqué précédemment, un [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) lie la propriété d’un contrôle dans un modèle de contrôle à une propriété pouvant être liée sur le parent de la vue *cible* propriétaire du modèle de contrôle. À leur tour, ces propriétés peuvent être liées à des propriétés dans des ViewModels.

L’exemple de code suivant définit deux propriétés sur un ViewModel :

```csharp
public class HomePageViewModel
{
  public string HeaderText { get { return "Control Template Demo App"; } }
  public string FooterText { get { return "(c) Xamarin 2016"; } }
}
```

Des liaisons peuvent être établies aux propriétés de ViewModel `HeaderText` et `FooterText`, comme indiqué dans l’exemple de code XAML suivant :

```xaml
<ContentPage xmlns:local="clr-namespace:SimpleTheme;assembly=SimpleTheme"
             HeaderText="{Binding HeaderText}" FooterText="{Binding FooterText}" ...>
    <ContentPage.BindingContext>
        <local:HomePageViewModel />
    </ContentPage.BindingContext>
    <ContentView ControlTemplate="{StaticResource TealTemplate}" ...>
    ...
    </ContentView>
</ContentPage>
```

Les propriétés pouvant être liées `HeaderText` et `FooterText` sont liées aux propriétés `HomePageViewModel.HeaderText` et `HomePageViewModel.FooterText`, car le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) a été défini sur une instance de la classe `HomePageViewModel`. Globalement, cela a comme conséquence la liaison des propriétés de contrôle dans le [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) à des instances de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) sur le [`ContentPage`](xref:Xamarin.Forms.ContentPage), qui à leur tour sont liées à des propriétés du ViewModel.

Le code C# équivalent est affiché dans l’exemple de code suivant :

```csharp
public class HomePageCS : ContentPage
{
  ...
  public HomePageCS ()
  {
    BindingContext = new HomePageViewModel ();
    this.SetBinding (HeaderTextProperty, "HeaderText");
    this.SetBinding (FooterTextProperty, "FooterText");
    ...
  }
}
```

Vous pouvez également lier directement aux propriétés du modèle d’affichage, ce qui vous évite de devoir déclarer des `BindableProperty`s pour `HeaderText` et `FooterText` sur le `ContentPage`, en liant le modèle de contrôle à Parent.BindingContext._PropertyName_, par exemple :

```xaml
<ControlTemplate x:Key="TealTemplate">
  <Grid>
    ...
    <Label Text="{TemplateBinding Parent.BindingContext.HeaderText}" ... />
    ...
    <Label Text="{TemplateBinding Parent.BindingContext.FooterText}" ... />
  </Grid>
</ControlTemplate>
```

Pour plus d’informations sur la liaison de données à des ViewModels, consultez [Des liaisons de données à MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="summary"></a>Récapitulatif

Dans cet article, vous avez vu comment utiliser les liaisons de modèle pour effectuer une liaison de données à partir d’un modèle de contrôle. Les liaisons de modèle permettent aux contrôles dans un modèle de contrôle de lier aux données des propriétés publiques et ainsi permettre de modifier facilement les valeurs de propriété sur les contrôles du modèle de contrôle.

## <a name="related-links"></a>Liens associés

- [Notions de base sur la liaison de données](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Des liaisons de données à MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
- [Thème simple avec liaison de modèle (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplates-simplethemewithtemplatebinding)
- [Thème simple avec liaison de modèle et ViewModel (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplates-simplethemewithtemplatebindingandviewmodel)
- [TemplateBinding](xref:Xamarin.Forms.TemplateBinding)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentView](xref:Xamarin.Forms.ContentView)
