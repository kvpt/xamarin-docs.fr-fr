---
title: Styles globaux dans Xamarin.Forms
description: Les styles peuvent être rendus disponibles globalement en les ajoutant au dictionnaire de ressources de l’application. Cela permet d’éviter la duplication des styles entre les pages ou les contrôles.
ms.prod: xamarin
ms.assetid: BDC65F82-65E0-4C8E-BB91-8E340EB2D15A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4b32c4b3ac4dea9dcdf52e3b9316f5f82aaad6ef
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558334"
---
# <a name="global-styles-in-no-locxamarinforms"></a>Styles globaux dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Les styles peuvent être rendus disponibles globalement en les ajoutant au dictionnaire de ressources de l’application. Cela permet d’éviter la duplication des styles entre les pages ou les contrôles._

## <a name="create-a-global-style-in-xaml"></a>Créer un style global en XAML

Par défaut, toutes les Xamarin.Forms applications créées à partir d’un modèle utilisent la classe **app** pour implémenter la sous- [`Application`](xref:Xamarin.Forms.Application) classe. Pour déclarer un [`Style`](xref:Xamarin.Forms.Style) au niveau de l’application, dans l’application [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) à l’aide de XAML **App** , la classe d’application par défaut doit être remplacée par une classe d' **application** XAML et le code-behind associé. Pour plus d’informations, consultez [utilisation de la classe App](~/xamarin-forms/app-fundamentals/application-class.md).

L’exemple de code suivant montre un [`Style`](xref:Xamarin.Forms.Style) déclaré au niveau de l’application :

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.App">
    <Application.Resources>
        <ResourceDictionary>
            <Style x:Key="buttonStyle" TargetType="Button">
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                <Setter Property="BorderColor" Value="Lime" />
                <Setter Property="BorderRadius" Value="5" />
                <Setter Property="BorderWidth" Value="5" />
                <Setter Property="WidthRequest" Value="200" />
                <Setter Property="TextColor" Value="Teal" />
            </Style>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

Cela [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) définit un seul style *explicite* , `buttonStyle` , qui sera utilisé pour définir l’apparence des [`Button`](xref:Xamarin.Forms.Button) instances. Toutefois, les styles globaux peuvent être *explicites* ou *implicites*.

L’exemple de code suivant montre une page XAML appliquant `buttonStyle` à l’instance de la page [`Button`](xref:Xamarin.Forms.Button) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ApplicationStylesPage" Title="Application" IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Button Text="These buttons" Style="{StaticResource buttonStyle}" />
            <Button Text="are demonstrating" Style="{StaticResource buttonStyle}" />
            <Button Text="application style overrides" Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Cela donne l’affichage illustré dans les captures d’écran suivantes :

[![Exemple de styles globaux](application-images/application-styles-1.png)](application-images/application-styles-1-large.png#lightbox "Exemple de styles globaux")

Pour plus d’informations sur la création de styles dans une page [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , consultez [styles explicites](~/xamarin-forms/user-interface/styles/explicit.md) et [styles implicites](~/xamarin-forms/user-interface/styles/implicit.md).

### <a name="override-styles"></a>Remplacer les styles

Les styles inférieurs dans la hiérarchie d’affichage ont priorité sur ceux définis plus haut. Par exemple, la définition [`Style`](xref:Xamarin.Forms.Style) d’un qui affecte [`Button.TextColor`](xref:Xamarin.Forms.Button.TextColor) à au `Red` niveau de l’application est remplacée par un style de niveau page qui affecte à la valeur `Button.TextColor` `Green` . De même, un style de niveau page est remplacé par un style de niveau contrôle. En outre, si `Button.TextColor` est défini directement sur une propriété de contrôle, il est prioritaire sur tous les styles. Cette priorité est illustrée dans l’exemple de code suivant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ApplicationStylesPage" Title="Application" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="buttonStyle" TargetType="Button">
                ...
                <Setter Property="TextColor" Value="Red" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="buttonStyle" TargetType="Button">
                        ...
                        <Setter Property="TextColor" Value="Blue" />
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            <Button Text="These buttons" Style="{StaticResource buttonStyle}" />
            <Button Text="are demonstrating" Style="{StaticResource buttonStyle}" />
            <Button Text="application style overrides" Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

L’original `buttonStyle` , défini au niveau de l’application, est remplacé par l' `buttonStyle` instance définie au niveau de la page. En outre, le style de niveau page est remplacé par le niveau de contrôle `buttonStyle` . Par conséquent, les [`Button`](xref:Xamarin.Forms.Button) instances sont affichées avec du texte en bleu, comme illustré dans les captures d’écran suivantes :

[![Exemple de substitution de styles](application-images/application-styles-2.png)](application-images/application-styles-2-large.png#lightbox "Exemple de substitution de styles")

## <a name="create-a-global-style-in-c35"></a>Créer un style global en C&#35;

[`Style`](xref:Xamarin.Forms.Style) les instances peuvent être ajoutées à la collection de l’application [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) en C# en créant un nouveau [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , puis en ajoutant les `Style` instances à `ResourceDictionary` , comme illustré dans l’exemple de code suivant :

```csharp
public class App : Application
{
    public App ()
    {
        var buttonStyle = new Style (typeof(Button)) {
            Setters = {
                ...
                new Setter { Property = Button.TextColorProperty,    Value = Color.Teal }
            }
        };

        Resources = new ResourceDictionary ();
        Resources.Add ("buttonStyle", buttonStyle);
        ...
    }
    ...
}
```

Le constructeur définit un style *explicite* unique pour l’application aux [`Button`](xref:Xamarin.Forms.Button) instances de l’ensemble de l’application. *Explicite* [`Style`](xref:Xamarin.Forms.Style) les instances sont ajoutées au à l' [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) aide de la [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) méthode, en spécifiant une `key` chaîne pour faire référence à l' `Style` instance. L' `Style` instance peut ensuite être appliquée à tous les contrôles du type approprié dans l’application. Toutefois, les styles globaux peuvent être *explicites* ou *implicites*.

L’exemple de code suivant affiche une page C# `buttonStyle` qui applique à l’instance de la page [`Button`](xref:Xamarin.Forms.Button) :

```csharp
public class ApplicationStylesPageCS : ContentPage
{
    public ApplicationStylesPageCS ()
    {
        ...
        Content = new StackLayout {
            Children = {
                new Button { Text = "These buttons", Style = (Style)Application.Current.Resources ["buttonStyle"] },
                new Button { Text = "are demonstrating", Style = (Style)Application.Current.Resources ["buttonStyle"] },
                new Button { Text = "application styles", Style = (Style)Application.Current.Resources ["buttonStyle"]
                }
            }
        };
    }
}
```

Le `buttonStyle` est appliqué aux [`Button`](xref:Xamarin.Forms.Button) instances en définissant leurs [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Propriétés et contrôle l’apparence des `Button` instances.

## <a name="related-links"></a>Liens connexes

- [Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Styles de base (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [Utilisation des styles (exemple)](/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Style](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)