---
title: Xamarin.Forms modèles de contrôle
description: Xamarin.Forms les modèles de contrôle définissent la structure visuelle des contrôles personnalisés dérivés de ContentView et des pages dérivées ContentPage.
ms.prod: xamarin
ms.assetid: 8B8E2360-6531-44A3-A7C8-9A8808DE9B86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/13/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 575169459433725ae3f3a7db675fc65caef1494e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563443"
---
# <a name="no-locxamarinforms-control-templates"></a>Xamarin.Forms modèles de contrôle

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplatedemos)

Xamarin.Forms les modèles de contrôle vous permettent de définir la structure visuelle des [`ContentView`](xref:Xamarin.Forms.ContentView) contrôles personnalisés dérivés et des [`ContentPage`](xref:Xamarin.Forms.ContentPage) pages dérivées. Les modèles de contrôle séparent l’interface utilisateur d’un contrôle personnalisé ou d’une page, de la logique qui implémente le contrôle ou la page. Du contenu supplémentaire peut également être inséré dans le contrôle personnalisé basé sur un modèle, ou dans la page basée sur un modèle, à un emplacement prédéfini.

Par exemple, vous pouvez créer un modèle de contrôle qui redéfinit l’interface utilisateur fournie par un contrôle personnalisé. Le modèle de contrôle peut ensuite être consommé par l’instance de contrôle personnalisé requise. Vous pouvez également créer un modèle de contrôle qui définit toute interface utilisateur commune qui sera utilisée par plusieurs pages dans une application. Le modèle de contrôle peut ensuite être consommé par plusieurs pages, chacune d’elles affichant toujours son contenu unique.

## <a name="create-a-controltemplate"></a>Créer un ControlTemplate

L’exemple suivant montre le code pour un contrôle personnalisé `CardView` :

```csharp
public class CardView : ContentView
{
    public static readonly BindableProperty CardTitleProperty = BindableProperty.Create(nameof(CardTitle), typeof(string), typeof(CardView), string.Empty);
    public static readonly BindableProperty CardDescriptionProperty = BindableProperty.Create(nameof(CardDescription), typeof(string), typeof(CardView), string.Empty);
    // ...

    public string CardTitle
    {
        get => (string)GetValue(CardTitleProperty);
        set => SetValue(CardTitleProperty, value);
    }

    public string CardDescription
    {
        get => (string)GetValue(CardDescriptionProperty);
        set => SetValue(CardDescriptionProperty, value);
    }
    // ...
}
```

La `CardView` classe, qui dérive de la [`ContentView`](xref:Xamarin.Forms.ContentView) classe, représente un contrôle personnalisé qui affiche des données dans une mise en page de type carte. La classe contient des propriétés, qui sont secondées par des propriétés pouvant être liées, pour les données qu’elle affiche. Toutefois, la classe `CardView` ne définit pas d’interface utilisateur. Au lieu de cela, l’interface utilisateur sera définie avec un modèle de contrôle. Pour plus d’informations sur la création de `ContentView` contrôles personnalisés dérivés, consultez [ Xamarin.Forms ContentView](~/xamarin-forms/user-interface/layouts/contentview.md).

Un modèle de contrôle est créé avec le [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) type. Lorsque vous créez un `ControlTemplate` , vous combinez [`View`](xref:Xamarin.Forms.View) des objets pour générer l’interface utilisateur pour un contrôle personnalisé ou une page. Un `ControlTemplate` doit avoir une seule `View` comme élément racine. Toutefois, l’élément racine contient généralement d’autres objets `View`. La combinaison des objets forme la structure visuelle du contrôle.

Alors qu’un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) peut être défini en ligne, l’approche classique de la déclaration d’un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) est en tant que ressource dans un dictionnaire de ressources. Les modèles de contrôle étant des ressources, ils obéissent aux mêmes règles de portée que celles qui s’appliquent à toutes les ressources. Par exemple, si vous déclarez un modèle de contrôle dans l’élément racine de votre fichier XAML de définition d’application, le modèle peut être utilisé n’importe où dans votre application. Si vous définissez le modèle dans une page, seule cette page peut utiliser le modèle de contrôle. Pour plus d’informations sur les ressources, consultez [ Xamarin.Forms dictionnaires de ressources](~/xamarin-forms/xaml/resource-dictionaries.md).

L’exemple de code XAML suivant illustre un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) objet pour les `CardView` objets :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    <ContentPage.Resources>
      <ControlTemplate x:Key="CardViewControlTemplate">
          <Frame BindingContext="{Binding Source={RelativeSource TemplatedParent}}"
                 BackgroundColor="{Binding CardColor}"
                 BorderColor="{Binding BorderColor}"
                 CornerRadius="5"
                 HasShadow="True"
                 Padding="8"
                 HorizontalOptions="Center"
                 VerticalOptions="Center">
              <Grid>
                  <Grid.RowDefinitions>
                      <RowDefinition Height="75" />
                      <RowDefinition Height="4" />
                      <RowDefinition Height="Auto" />
                  </Grid.RowDefinitions>
                  <Grid.ColumnDefinitions>
                      <ColumnDefinition Width="75" />
                      <ColumnDefinition Width="200" />
                  </Grid.ColumnDefinitions>
                  <Frame IsClippedToBounds="True"
                         BorderColor="{Binding BorderColor}"
                         BackgroundColor="{Binding IconBackgroundColor}"
                         CornerRadius="38"
                         HeightRequest="60"
                         WidthRequest="60"
                         HorizontalOptions="Center"
                         VerticalOptions="Center">
                      <Image Source="{Binding IconImageSource}"
                             Margin="-20"
                             WidthRequest="100"
                             HeightRequest="100"
                             Aspect="AspectFill" />
                  </Frame>
                  <Label Grid.Column="1"
                         Text="{Binding CardTitle}"
                         FontAttributes="Bold"
                         FontSize="Large"
                         VerticalTextAlignment="Center"
                         HorizontalTextAlignment="Start" />
                  <BoxView Grid.Row="1"
                           Grid.ColumnSpan="2"
                           BackgroundColor="{Binding BorderColor}"
                           HeightRequest="2"
                           HorizontalOptions="Fill" />
                  <Label Grid.Row="2"
                         Grid.ColumnSpan="2"
                         Text="{Binding CardDescription}"
                         VerticalTextAlignment="Start"
                         VerticalOptions="Fill"
                         HorizontalOptions="Fill" />
              </Grid>
          </Frame>
      </ControlTemplate>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Lorsqu’un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) est déclaré en tant que ressource, il doit avoir une clé spécifiée avec l' `x:Key` attribut afin qu’il puisse être identifié dans le dictionnaire de ressources. Dans cet exemple, l’élément racine du `CardViewControlTemplate` est un objet [`Frame`](xref:Xamarin.Forms.Frame). L’objet `Frame` utilise l’extension de balisage `RelativeSource` pour définir comme `BindingContext` l’instance de l’objet runtime à laquelle le modèle sera appliqué, connu sous le nom de *parent basé sur un modèle*. L' `Frame` objet utilise une combinaison d' [`Grid`](xref:Xamarin.Forms.Grid) objets,,, `Frame` [`Image`](xref:Xamarin.Forms.Image) [`Label`](xref:Xamarin.Forms.Label) et [`BoxView`](xref:Xamarin.Forms.BoxView) pour définir la structure visuelle d’un `CardView` objet. Les expressions de liaison de ces objets sont résolues par rapport à des propriétés `CardView`, en raison de l’héritage du `BindingContext` à partir de l’élément `Frame` racine. Pour plus d’informations sur l' `RelativeSource` extension de balisage, consultez [ Xamarin.Forms liaisons relatives](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

## <a name="consume-a-controltemplate"></a>Consommer un ControlTemplate

Une [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) peut être appliquée à un [`ContentView`](xref:Xamarin.Forms.ContentView) contrôle personnalisé dérivé en affectant [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) à sa propriété l’objet de modèle de contrôle. De même, un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) peut être appliqué à une [`ContentPage`](xref:Xamarin.Forms.ContentPage) page dérivée en affectant [`ControlTemplate`](xref:Xamarin.Forms.TemplatedPage.ControlTemplate) à sa propriété l’objet de modèle de contrôle. Lors de l’exécution, lorsqu’un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) est appliqué, tous les contrôles définis dans le `ControlTemplate` sont ajoutés à l’arborescence d’éléments visuels du contrôle personnalisé basé sur un modèle ou de la page basée sur un modèle.

L’exemple suivant montre le `CardViewControlTemplate` assigné à la [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) propriété de chaque `CardView` objet :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:controls="clr-namespace:ControlTemplateDemos.Controls"
             ...>
    <StackLayout Margin="30">
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="John Doe"
                           CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Jane Doe"
                           CardDescription="Phasellus eu convallis mi. In tempus augue eu dignissim fermentum. Morbi ut lacus vitae eros lacinia."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Xamarin Monkey"
                           CardDescription="Aliquam sagittis, odio lacinia fermentum dictum, mi erat scelerisque erat, quis aliquet arcu."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
    </StackLayout>
</ContentPage>
```

Dans cet exemple, les contrôles du `CardViewControlTemplate` deviennent partie intégrante de l’arborescence d’éléments visuels pour chaque objet `CardView`. Étant donné que l’objet racine du [`Frame`](xref:Xamarin.Forms.Frame) modèle de contrôle définit son `BindingContext` sur le parent basé sur un modèle, le `Frame` et ses enfants résolvent leurs expressions de liaison par rapport aux propriétés de chaque `CardView` objet.

Les captures d’écran suivantes montrent le `CardViewControlTemplate` appliqué aux trois objets `CardView` :

[![Captures d’écran d’objets CardView basés sur un modèle, sur iOS et Android](control-template-images/relativesource-controltemplate.png "Objets CardView basés sur un modèle")](control-template-images/relativesource-controltemplate-large.png#lightbox "Objets CardView basés sur un modèle")

> [!IMPORTANT]
> Le moment auquel un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) est appliqué à une instance de contrôle peut être détecté en remplaçant la `OnApplyTemplate` méthode dans la page contrôle personnalisé basé sur un modèle ou modèle. Pour plus d’informations, consultez [Obtenir un élément nommé à partir d’un modèle](#get-a-named-element-from-a-template).

## <a name="pass-parameters-with-templatebinding"></a>Passer des paramètres avec TemplateBinding

L' `TemplateBinding` extension de balisage lie une propriété d’un élément qui se trouve dans [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) à une propriété publique définie par la page basée sur un modèle ou un contrôle personnalisé basé sur un modèle. Quand vous utilisez un `TemplateBinding`, vous permettez aux propriétés sur le contrôle de faire office de paramètres du modèle. Ainsi, quand une propriété sur un contrôle personnalisé basé sur un modèle ou une page basée sur un modèle est définie, cette valeur est passée à l’élément sur lequel se trouve le `TemplateBinding`.

> [!IMPORTANT]
> L' `TemplateBinding` extension de balisage est une alternative à la création d’un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) qui utilise l' `RelativeSource` extension de balisage pour définir le `BindingContext` de l’élément racine dans le modèle sur son parent basé sur un modèle. L’extension de balisage `TemplateBinding` élimine la liaison `RelativeSource` et remplace les expressions `Binding` par des expressions `TemplateBinding`.

L’extension de balisage `TemplateBinding` définit les propriétés suivantes :

- `Path`, de type `string`, le chemin de la propriété.
- `Mode`, de type `BindingMode`, la direction dans laquelle les modifications se propagent entre la *source* et la *cible*.
- `Converter`, de type `IValueConverter`, le convertisseur de valeur de liaison.
- `ConverterParameter`, de type `object`, le paramètre du convertisseur de valeur de liaison.
- `StringFormat`, de type `string`, le format de chaîne pour la liaison.

`ContentProperty` pour l’extension de balisage `TemplateBinding` est `Path`. Par conséquent, la partie « Path= » de l’extension de balisage peut être omise si le chemin est le premier élément de l’expression `TemplateBinding`. Pour plus d’informations sur l’utilisation de ces propriétés dans une expression de liaison, consultez [ Xamarin.Forms liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md).

> [!WARNING]
> L' `TemplateBinding` extension de balisage doit être utilisée uniquement dans un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) . Toutefois, si vous tentez d’utiliser une expression `TemplateBinding` en dehors d’un `ControlTemplate`, une erreur de génération ou une exception est levée.

L’exemple de code XAML suivant montre un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) pour les `CardView` objets, qui utilise l' `TemplateBinding` extension de balisage :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    <ContentPage.Resources>
        <ControlTemplate x:Key="CardViewControlTemplate">
            <Frame BackgroundColor="{TemplateBinding CardColor}"
                   BorderColor="{TemplateBinding BorderColor}"
                   CornerRadius="5"
                   HasShadow="True"
                   Padding="8"
                   HorizontalOptions="Center"
                   VerticalOptions="Center">
                <Grid>
                    <Grid.RowDefinitions>
                        <RowDefinition Height="75" />
                        <RowDefinition Height="4" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="75" />
                        <ColumnDefinition Width="200" />
                    </Grid.ColumnDefinitions>
                    <Frame IsClippedToBounds="True"
                           BorderColor="{TemplateBinding BorderColor}"
                           BackgroundColor="{TemplateBinding IconBackgroundColor}"
                           CornerRadius="38"
                           HeightRequest="60"
                           WidthRequest="60"
                           HorizontalOptions="Center"
                           VerticalOptions="Center">
                        <Image Source="{TemplateBinding IconImageSource}"
                               Margin="-20"
                               WidthRequest="100"
                               HeightRequest="100"
                               Aspect="AspectFill" />
                    </Frame>
                    <Label Grid.Column="1"
                           Text="{TemplateBinding CardTitle}"
                           FontAttributes="Bold"
                           FontSize="Large"
                           VerticalTextAlignment="Center"
                           HorizontalTextAlignment="Start" />
                    <BoxView Grid.Row="1"
                             Grid.ColumnSpan="2"
                             BackgroundColor="{TemplateBinding BorderColor}"
                             HeightRequest="2"
                             HorizontalOptions="Fill" />
                    <Label Grid.Row="2"
                           Grid.ColumnSpan="2"
                           Text="{TemplateBinding CardDescription}"
                           VerticalTextAlignment="Start"
                           VerticalOptions="Fill"
                           HorizontalOptions="Fill" />
                </Grid>
            </Frame>
        </ControlTemplate>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Dans cet exemple, l’extension de balisage `TemplateBinding` résout les expressions de liaison par rapport aux propriétés de chaque objet `CardView`. Les captures d’écran suivantes montrent le `CardViewControlTemplate` appliqué aux trois objets `CardView` :

[![Captures d’écran d’objets CardView basés sur un modèle, sur iOS et Android](control-template-images/templatebinding-controltemplate.png "Objets CardView basés sur un modèle")](control-template-images/templatebinding-controltemplate-large.png#lightbox "Objets CardView basés sur un modèle")

> [!IMPORTANT]
> L’utilisation de l’extension de balisage `TemplateBinding` équivaut à affecter comme `BindingContext` de l’élément racine du modèle son parent basé sur un modèle avec l’extension de balisage `RelativeSource`, puis à résoudre les liaisons des objets enfants avec l’extension de balisage `Binding`. En fait, l’extension de balisage `TemplateBinding` crée un `Binding` dont la `Source` est `RelativeBindingSource.TemplatedParent`.

## <a name="apply-a-controltemplate-with-a-style"></a>Appliquer un ControlTemplate avec un style

Les modèles de contrôle peuvent également être appliqués avec des styles. Pour ce faire, vous devez créer un style *implicite* ou *explicite* qui consomme le [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate).

L’exemple de code XAML suivant montre un style *implicite* qui consomme le `CardViewControlTemplate` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:controls="clr-namespace:ControlTemplateDemos.Controls"
             ...>
    <ContentPage.Resources>
        <ControlTemplate x:Key="CardViewControlTemplate">
            ...
        </ControlTemplate>

        <Style TargetType="controls:CardView">
            <Setter Property="ControlTemplate"
                    Value="{StaticResource CardViewControlTemplate}" />
        </Style>
    </ContentPage.Resources>
    <StackLayout Margin="30">
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="John Doe"
                           CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png" />
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Jane Doe"
                           CardDescription="Phasellus eu convallis mi. In tempus augue eu dignissim fermentum. Morbi ut lacus vitae eros lacinia."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"/>
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Xamarin Monkey"
                           CardDescription="Aliquam sagittis, odio lacinia fermentum dictum, mi erat scelerisque erat, quis aliquet arcu."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png" />
    </StackLayout>
</ContentPage>
```

Dans cet exemple, le *implicite* [`Style`](xref:Xamarin.Forms.Style) est appliqué automatiquement à chaque `CardView` objet et affecte [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) à la propriété de chacun la `CardView` valeur `CardViewControlTemplate` .

Pour plus d’informations sur les styles, consultez [ Xamarin.Forms styles](~/xamarin-forms/user-interface/styles/index.md).

## <a name="redefine-a-controls-ui"></a>Redéfinir l’interface utilisateur d’un contrôle

Quand une [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) est instanciée et assignée à la `ControlTemplate` propriété d’un [`ContentView`](xref:Xamarin.Forms.ContentView) contrôle personnalisé dérivé, ou d’une [`ContentPage`](xref:Xamarin.Forms.ContentPage) page dérivée, la structure visuelle définie pour le contrôle ou la page personnalisé est remplacée par la structure visuelle définie dans le `ControlTemplate` .

Par exemple, le contrôle personnalisé `CardViewUI` définit son interface utilisateur à l’aide du code XAML suivant :

```xaml
<ContentView xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ControlTemplateDemos.Controls.CardViewUI"
             x:Name="this">
    <Frame BindingContext="{x:Reference this}"
           BackgroundColor="{Binding CardColor}"
           BorderColor="{Binding BorderColor}"
           CornerRadius="5"
           HasShadow="True"
           Padding="8"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="75" />
                <RowDefinition Height="4" />
                <RowDefinition Height="Auto" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="75" />
                <ColumnDefinition Width="200" />
            </Grid.ColumnDefinitions>
            <Frame IsClippedToBounds="True"
                   BorderColor="{Binding BorderColor, FallbackValue='Black'}"
                   BackgroundColor="{Binding IconBackgroundColor, FallbackValue='Gray'}"
                   CornerRadius="38"
                   HeightRequest="60"
                   WidthRequest="60"
                   HorizontalOptions="Center"
                   VerticalOptions="Center">
                <Image Source="{Binding IconImageSource}"
                       Margin="-20"
                       WidthRequest="100"
                       HeightRequest="100"
                       Aspect="AspectFill" />
            </Frame>
            <Label Grid.Column="1"
                   Text="{Binding CardTitle, FallbackValue='Card title'}"
                   FontAttributes="Bold"
                   FontSize="Large"
                   VerticalTextAlignment="Center"
                   HorizontalTextAlignment="Start" />
            <BoxView Grid.Row="1"
                     Grid.ColumnSpan="2"
                     BackgroundColor="{Binding BorderColor, FallbackValue='Black'}"
                     HeightRequest="2"
                     HorizontalOptions="Fill" />
            <Label Grid.Row="2"
                   Grid.ColumnSpan="2"
                   Text="{Binding CardDescription, FallbackValue='Card description'}"
                   VerticalTextAlignment="Start"
                   VerticalOptions="Fill"
                   HorizontalOptions="Fill" />
        </Grid>
    </Frame>
</ContentView>
```

Toutefois, les contrôles qui composent cette interface utilisateur peuvent être remplacés en définissant une nouvelle structure visuelle dans un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) et en l’assignant à la [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) propriété d’un `CardViewUI` objet :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    <ContentPage.Resources>
        <ControlTemplate x:Key="CardViewCompressed">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="100" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="100" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Image Source="{TemplateBinding IconImageSource}"
                        BackgroundColor="{TemplateBinding IconBackgroundColor}"
                        WidthRequest="100"
                        HeightRequest="100"
                        Aspect="AspectFill"
                        HorizontalOptions="Center"
                        VerticalOptions="Center" />
                <StackLayout Grid.Column="1">
                    <Label Text="{TemplateBinding CardTitle}"
                           FontAttributes="Bold" />
                    <Label Text="{TemplateBinding CardDescription}" />
                </StackLayout>
            </Grid>
        </ControlTemplate>
    </ContentPage.Resources>
    <StackLayout Margin="30">
        <controls:CardViewUI BorderColor="DarkGray"
                             CardTitle="John Doe"
                             CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                             IconBackgroundColor="SlateGray"
                             IconImageSource="user.png"
                             ControlTemplate="{StaticResource CardViewCompressed}" />
        <controls:CardViewUI BorderColor="DarkGray"
                             CardTitle="Jane Doe"
                             CardDescription="Phasellus eu convallis mi. In tempus augue eu dignissim fermentum. Morbi ut lacus vitae eros lacinia."
                             IconBackgroundColor="SlateGray"
                             IconImageSource="user.png"
                             ControlTemplate="{StaticResource CardViewCompressed}" />
        <controls:CardViewUI BorderColor="DarkGray"
                             CardTitle="Xamarin Monkey"
                             CardDescription="Aliquam sagittis, odio lacinia fermentum dictum, mi erat scelerisque erat, quis aliquet arcu."
                             IconBackgroundColor="SlateGray"
                             IconImageSource="user.png"
                             ControlTemplate="{StaticResource CardViewCompressed}" />
    </StackLayout>
</ContentPage>
```

Dans cet exemple, la structure visuelle de l' `CardViewUI` objet est redéfinie dans un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) qui fournit une structure visuelle plus compacte adaptée à une liste condensée :

[![Captures d’écran d’objets CardViewUI basés sur un modèle, sur iOS et Android](control-template-images/redefine-controltemplate.png "Objets CardViewUI basés sur un modèle")](control-template-images/redefine-controltemplate-large.png#lightbox "Objets CardViewUI basés sur un modèle")

## <a name="substitute-content-into-a-contentpresenter"></a>Remplacer le contenu dans un ContentPresenter

Un [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) peut être placé dans un modèle de contrôle pour marquer le contenu à afficher par le contrôle personnalisé basé sur un modèle ou la page basée sur un modèle. Le contrôle ou la page personnalisé qui consomme le modèle de contrôle définira ensuite le contenu devant être affiché par le `ContentPresenter`. Le diagramme suivant illustre un `ControlTemplate` pour une page qui contient plusieurs contrôles, notamment un `ContentPresenter` marqué par un rectangle bleu :

![Modèle de contrôle pour un ContentPage](control-template-images/control-template.png "Modèle de contrôle pour un ContentPage")

Le code XAML suivant montre un modèle de contrôle nommé `TealTemplate` qui contient un [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) dans sa structure visuelle :

```xaml
<ControlTemplate x:Key="TealTemplate">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="0.1*" />
            <RowDefinition Height="0.8*" />
            <RowDefinition Height="0.1*" />
        </Grid.RowDefinitions>
        <BoxView Color="Teal" />
        <Label Margin="20,0,0,0"
               Text="{TemplateBinding HeaderText}"
               TextColor="White"
               FontSize="Title"
               VerticalOptions="Center" />
        <ContentPresenter Grid.Row="1" />
        <BoxView Grid.Row="2"
                 Color="Teal" />
        <Label x:Name="changeThemeLabel"
               Grid.Row="2"
               Margin="20,0,0,0"
               Text="Change Theme"
               TextColor="White"
               HorizontalOptions="Start"
               VerticalOptions="Center">
            <Label.GestureRecognizers>
                <TapGestureRecognizer Tapped="OnChangeThemeLabelTapped" />
            </Label.GestureRecognizers>
        </Label>
        <controls:HyperlinkLabel Grid.Row="2"
                                 Margin="0,0,20,0"
                                 Text="Help"
                                 TextColor="White"
                                 Url="https://docs.microsoft.com/xamarin/xamarin-forms/"
                                 HorizontalOptions="End"
                                 VerticalOptions="Center" />
    </Grid>
</ControlTemplate>
```

L’exemple suivant illustre `TealTemplate` l’assignation à la [`ControlTemplate`](xref:Xamarin.Forms.TemplatedPage.ControlTemplate) propriété d’une [`ContentPage`](xref:Xamarin.Forms.ContentPage) page dérivée :

```xaml
<controls:HeaderFooterPage xmlns="http://xamarin.com/schemas/2014/forms"
                           xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                           xmlns:controls="clr-namespace:ControlTemplateDemos.Controls"                           
                           ControlTemplate="{StaticResource TealTemplate}"
                           HeaderText="MyApp"
                           ...>
    <StackLayout Margin="10">
        <Entry Placeholder="Enter username" />
        <Entry Placeholder="Enter password"
               IsPassword="True" />
        <Button Text="Login" />
    </StackLayout>
</controls:HeaderFooterPage>
```

Lors de l’exécution, lorsque `TealTemplate` est appliqué à la page, le contenu de la page est remplacé dans le [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) défini dans le modèle de contrôle :

[![Captures d’écran d’un objet de page basé sur un modèle, sur iOS et Android](control-template-images/tealtemplate-contentpage.png "ContentPage basé sur un modèle")](control-template-images/tealtemplate-contentpage-large.png#lightbox "ContentPage basé sur un modèle")

## <a name="get-a-named-element-from-a-template"></a>Obtenir un élément nommé à partir d’un modèle

Les éléments nommés dans un modèle de contrôle peuvent être récupérés à partir du contrôle personnalisé basé sur un modèle ou de la page basée sur un modèle. Pour cela, vous pouvez utiliser la `GetTemplateChild` méthode, qui retourne l’élément nommé dans l’arborescence d’éléments visuels instanciés [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) , s’il est trouvé. Sinon, `null`est retourné.

Après qu’un modèle de contrôle a été instancié, la méthode du modèle `OnApplyTemplate` est appelée. La méthode `GetTemplateChild` doit donc être appelée à partir d’une substitution `OnApplyTemplate` dans la page ou le contrôle basé sur un modèle.

> [!IMPORTANT]
> La méthode `GetTemplateChild` doit uniquement être appelée après que la méthode `OnApplyTemplate` a été appelée.

Le code XAML suivant montre un modèle de contrôle nommé `TealTemplate` qui peut être appliqué à des [`ContentPage`](xref:Xamarin.Forms.ContentPage) pages dérivées :

```xaml
<ControlTemplate x:Key="TealTemplate">
    <Grid>
        ...
        <Label x:Name="changeThemeLabel"
               Grid.Row="2"
               Margin="20,0,0,0"
               Text="Change Theme"
               TextColor="White"
               HorizontalOptions="Start"
               VerticalOptions="Center">
            <Label.GestureRecognizers>
                <TapGestureRecognizer Tapped="OnChangeThemeLabelTapped" />
            </Label.GestureRecognizers>
        </Label>
        ...
    </Grid>
</ControlTemplate>
```

Dans cet exemple, l' [`Label`](xref:Xamarin.Forms.Label) élément est nommé et peut être récupéré dans le code de la page basée sur un modèle. Cela s’effectue en appelant la méthode `GetTemplateChild` à partir de la substitution `OnApplyTemplate` pour la page basée sur un modèle :

```csharp
public partial class AccessTemplateElementPage : HeaderFooterPage
{
    Label themeLabel;

    public AccessTemplateElementPage()
    {
        InitializeComponent();
    }

    protected override void OnApplyTemplate()
    {
        base.OnApplyTemplate();
        themeLabel = (Label)GetTemplateChild("changeThemeLabel");
        themeLabel.Text = OriginalTemplate ? "Aqua Theme" : "Teal Theme";
    }
}
```

Dans cet exemple, l' [`Label`](xref:Xamarin.Forms.Label) objet nommé `changeThemeLabel` est récupéré une fois que le `ControlTemplate` a été instancié. `changeThemeLabel` est alors accessible et peut ensuite être manipulé par la classe `AccessTemplateElementPage`. Les captures d’écran suivantes montrent que le texte affiché par le `Label` a été modifié :

[![Captures d’écran d’un objet de page basé sur un modèle, sur iOS et Android](control-template-images/get-named-element.png "ContentPage basé sur un modèle")](control-template-images/get-named-element-large.png#lightbox "ContentPage basé sur un modèle")

## <a name="bind-to-a-viewmodel"></a>Lier à un viewmodel

Un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) peut effectuer une liaison de données à un ViewModel, même lorsque le `ControlTemplate` est lié au parent basé sur des modèles (l’instance de l’objet d’exécution à laquelle le modèle est appliqué).

L’exemple de code XAML suivant montre une page qui utilise un viewmodel nommé `PeopleViewModel` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ControlTemplateDemos"
             xmlns:controls="clr-namespace:ControlTemplateDemos.Controls"
             ...>
    <ContentPage.BindingContext>
        <local:PeopleViewModel />
    </ContentPage.BindingContext>

    <ContentPage.Resources>
        <DataTemplate x:Key="PersonTemplate">
            <controls:CardView BorderColor="DarkGray"
                               CardTitle="{Binding Name}"
                               CardDescription="{Binding Description}"
                               ControlTemplate="{StaticResource CardViewControlTemplate}" />
        </DataTemplate>
    </ContentPage.Resources>

    <StackLayout Margin="10"
                 BindableLayout.ItemsSource="{Binding People}"
                 BindableLayout.ItemTemplate="{StaticResource PersonTemplate}" />
</ContentPage>
```

Dans cet exemple, le `BindingContext` de la page est défini sur une instance de `PeopleViewModel`. Ce viewmodel expose une collection `People` et un `ICommand` nommé `DeletePersonCommand`. Le [`StackLayout`](xref:Xamarin.Forms.StackLayout) sur la page utilise une disposition pouvant être liée à la liaison de données à la `People` collection, et la `ItemTemplate` de la disposition pouvant être liée est définie sur la `PersonTemplate` ressource. Cela [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) spécifie que chaque élément de la `People` collection sera affiché à l’aide d’un `CardView` objet. La structure visuelle de l' `CardView` objet est définie à l’aide d’un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) nommé `CardViewControlTemplate` :

```xaml
<ControlTemplate x:Key="CardViewControlTemplate">
    <Frame BindingContext="{Binding Source={RelativeSource TemplatedParent}}"
           BackgroundColor="{Binding CardColor}"
           BorderColor="{Binding BorderColor}"
           CornerRadius="5"
           HasShadow="True"
           Padding="8"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="75" />
                <RowDefinition Height="4" />
                <RowDefinition Height="Auto" />
            </Grid.RowDefinitions>
            <Label Text="{Binding CardTitle}"
                   FontAttributes="Bold"
                   FontSize="Large"
                   VerticalTextAlignment="Center"
                   HorizontalTextAlignment="Start" />
            <BoxView Grid.Row="1"
                     BackgroundColor="{Binding BorderColor}"
                     HeightRequest="2"
                     HorizontalOptions="Fill" />
            <Label Grid.Row="2"
                   Text="{Binding CardDescription}"
                   VerticalTextAlignment="Start"
                   VerticalOptions="Fill"
                   HorizontalOptions="Fill" />
            <Button Text="Delete"
                    Command="{Binding Source={RelativeSource AncestorType={x:Type local:PeopleViewModel}}, Path=DeletePersonCommand}"
                    CommandParameter="{Binding CardTitle}"
                    HorizontalOptions="End" />
        </Grid>
    </Frame>
</ControlTemplate>
```

Dans cet exemple, l’élément racine de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) est un [`Frame`](xref:Xamarin.Forms.Frame) objet. L’objet `Frame` utilise l’extension de balisage `RelativeSource` pour affecter le parent basé sur un modèle comme `BindingContext`. Les expressions de liaison de l’objet `Frame` et ses enfants sont résolues par rapport à des propriétés `CardView`, en raison de l’héritage du `BindingContext` à partir de l’élément `Frame` racine. Les captures d’écran suivantes montrent la page qui affiche la collection `People`, qui se compose de trois éléments :

[![Captures d’écran d’objets CardView basés sur un modèle, sur iOS et Android](control-template-images/viewmodel-controltemplate.png "Objets CardView basés sur un modèle")](control-template-images/viewmodel-controltemplate-large.png#lightbox "Objets CardView basés sur un modèle")

Tandis que les objets dans sont [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) liés aux propriétés de son parent basé sur un modèle, le dans [`Button`](xref:Xamarin.Forms.Button) le modèle de contrôle est lié à son parent basé sur un modèle et à `DeletePersonCommand` dans le ViewModel. En effet, la `Button.Command` propriété redéfinit sa source de liaison comme étant le contexte de liaison de l’ancêtre dont le type de contexte de liaison est `PeopleViewModel` , qui est le [`StackLayout`](xref:Xamarin.Forms.StackLayout) . La partie `Path` des expressions de liaison peut ensuite résoudre la propriété `DeletePersonCommand`. Toutefois, la `Button.CommandParameter` propriété ne modifie pas sa source de liaison, plutôt qu’elle l’hérite de son parent dans le [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) . Ainsi, la propriété `CommandParameter` est liée à la propriété `CardTitle` du `CardView`.

L’effet global des [`Button`](xref:Xamarin.Forms.Button) liaisons est que lorsque le `Button` est frappé, le `DeletePersonCommand` de la `PeopleViewModel` classe est exécuté, avec la valeur de la `CardName` propriété passée à l' `DeletePersonCommand` . Cela entraîne la suppression du `CardView` spécifié de la disposition pouvant être liée :

[![Captures d’écran d’objets CardView basés sur un modèle, sur iOS et Android](control-template-images/viewmodel-itemdeleted.png "Objets CardView basés sur un modèle")](control-template-images/viewmodel-itemdeleted-large.png#lightbox "Objets CardView basés sur un modèle")

Pour plus d’informations sur les liaisons relatives, consultez [ Xamarin.Forms liaisons relatives](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

## <a name="related-links"></a>Liens connexes

- [ControlTemplateDemos (exemple)](/samples/xamarin/xamarin-forms-samples/templates-controltemplatedemos)
- [Xamarin.Forms ContentView](~/xamarin-forms/user-interface/layouts/contentview.md)
- [Xamarin.Forms Liaisons relatives](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md)
- [Xamarin.Forms Dictionnaires de ressources](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Xamarin.Forms Liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin.Forms Ils](~/xamarin-forms/user-interface/styles/index.md)