---
title: Xamarin. Forms ContentView
description: Cet article explique comment utiliser la classe ContentView pour créer un contrôle personnalisé, tel que l’exemple CardView.
ms.prod: xamarin
ms.assetid: 638402E7-CA44-456B-863B-791F6B6B561D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/14/2019
ms.openlocfilehash: e340b45148c7528eff1aa511ee9902a4ac2658c0
ms.sourcegitcommit: 9178e2e689f027212ea3e623b556b312985d79fe
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69658156"
---
# <a name="xamarinforms-contentview"></a>Xamarin. Forms ContentView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/en-us/samples/xamarin/xamarin-forms-samples/userinterface-cardview/)

La classe Xamarin. [`ContentView`](xref:Xamarin.Forms.ContentView) Forms est un type `Layout` de qui contient un seul élément enfant et est généralement utilisé pour créer des contrôles personnalisés et réutilisables. La `ContentView` classe hérite de [`TemplatedView`](xref:Xamarin.Forms.TemplatedView). Cet article, ainsi que l’exemple associé, expliquent comment créer `CardView` un contrôle personnalisé basé `ContentView` sur la classe.

La capture d’écran suivante `CardView` montre un contrôle qui dérive `ContentView` de la classe:

[![Capture d’écran de l’exemple d’application CardView](contentview-images/cardview-list-cropped.png)](contentview-images/cardview-list.png#lightbox)

La `ContentView` classe définit une propriété unique:

* [`Content`](xref:Xamarin.Forms.ContentView.Content)est un `View` objet. Cette propriété est stockée par un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objet afin de pouvoir être la cible des liaisons de données.

Hérite `TemplatedView` également d’une propriété de la classe: `ContentView`

* [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate)est un `ControlTemplate` qui peut définir ou substituer l’apparence du contrôle.

Pour plus d’informations sur `ControlTemplate` la propriété, consultez [personnaliser l’apparence avec un ControlTemplate](#customize-appearance-with-a-controltemplate).

## <a name="create-a-custom-control"></a>Créer un contrôle personnalisé

La `ContentView` classe offre peu de fonctionnalités, mais elle peut être utilisée pour créer un contrôle personnalisé. L’exemple de projet définit `CardView` un contrôle (un élément d’interface utilisateur qui affiche une image, un titre et une description dans une disposition de type carte).

Le processus de création d’un contrôle personnalisé consiste à:

1. Créez une nouvelle classe à l' `ContentView` aide du modèle dans Visual Studio 2019.
1. Définissez des propriétés ou des événements uniques dans le fichier code-behind pour le nouveau contrôle personnalisé.
1. Créez l’interface utilisateur pour le contrôle personnalisé.

> [!NOTE]
> Il est possible de créer un contrôle personnalisé dont la disposition est définie dans le code au lieu de XAML. Par souci de simplicité, l’exemple d’application définit `CardView` uniquement une seule classe avec une disposition XAML. Toutefois, l’exemple d’application contient une classe **CardViewCodePage** qui montre le processus de consommation du contrôle personnalisé dans le code.

### <a name="create-code-behind-properties"></a>Créer des propriétés code-behind

Le `CardView` contrôle personnalisé définit les propriétés suivantes:

* `CardTitle``string` : objet qui représente le titre affiché sur la carte.
* `CardDescription``string` : objet qui représente la description affichée sur la carte.
* `IconImageSource``ImageSource` : objet qui représente l’image affichée sur la carte.
* `IconBackgroundColor``Color` : objet qui représente la couleur d’arrière-plan de l’image affichée sur la carte.
* `BorderColor``Color` : objet qui représente la couleur de la bordure de la carte, de la bordure de l’image et de la ligne de séparation.
* `CardColor``Color` : objet qui représente la couleur d’arrière-plan de la carte.

> [!NOTE]
> La `BorderColor` propriété affecte plusieurs éléments dans le cadre de la démonstration. Cette propriété peut être divisée en trois propriétés si nécessaire.

Chaque propriété est stockée par une `BindableProperty` instance de. Le stockage `BindableProperty` permet à chaque propriété d’être stylisée, et liée, à l’aide du modèle MVVM. Pour plus d’informations, consultez [lier des données avec MVVM](#bind-data-with-mvvm).

L’exemple suivant montre comment créer une sauvegarde `BindableProperty`:

```csharp
public static readonly BindableProperty CardTitleProperty = BindableProperty.Create(
    "CardTitle",        // the name of the bindable property
    typeof(string),     // the bindable property type
    typeof(CardView),   // the parent object type
    string.Empty);      // the default value for the property
```

La propriété personnalisée utilise les `GetValue` méthodes `SetValue` et pour récupérer et définir les `BindableProperty` valeurs de l’objet:

```csharp
public string CardTitle
{
    get => (string)GetValue(CardView.CardTitleProperty);
    set => SetValue(CardView.CardTitleProperty, value);
}
```

Pour plus d’informations `BindableProperty` sur les objets, consultez [propriétés pouvant être liées](~/xamarin-forms/xaml/bindable-properties.md).

### <a name="define-ui"></a>Définir l’interface utilisateur

L’interface utilisateur du contrôle personnalisé `ContentView` utilise comme élément racine pour le `CardView` contrôle. L’exemple suivant montre le `CardView` code XAML:

```XAML
<ContentView ...
             x:Name="this"
             x:Class="CardViewDemo.Controls.CardView">
    <Frame BindingContext="{x:Reference this}"
            BackgroundColor="{Binding CardColor}"
            BorderColor="{Binding BorderColor}"
            ...>
        <Grid>
            ...
            <Frame BorderColor="{Binding BorderColor}"
                   BackgroundColor="{Binding IconBackgroundColor}"
                   ...>
                <Image Source="{Binding IconImageSource}"
                       .. />
            </Frame>
            <Label Text="{Binding CardTitle}"
                   ... />
            <BoxView BackgroundColor="{Binding BorderColor}"
                     ... />
            <Label Text="{Binding CardDescription}"
                   ... />
        </Grid>
    </Frame>
</ContentView>
```

L' `ContentView` élément affecte la `x:Name` valeur àla propriété, qui peut être utilisée pour accéder à l’objet lié `CardView` à l’instance. Les éléments de la disposition définissent des liaisons sur leurs propriétés aux valeurs définies sur l’objet lié.

Pour plus d’informations sur la liaison de données, consultez la page [Liaison de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="instantiate-a-custom-control"></a>Instancier un contrôle personnalisé

Une référence à l’espace de noms de contrôle personnalisé doit être ajoutée à une page qui instancie le contrôle personnalisé. L’exemple suivant montre une référence d’espace de noms appelée contrôles `ContentPage` ajoutés à une instance en XAML:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CardViewDemo.Controls" >
```

Une fois la référence ajoutée, le `CardView` peut être instancié en XAML et ses propriétés définies:

```xaml
<controls:CardView BorderColor="DarkGray"
                   CardTitle="Slavko Vlasic"
                   CardDescription="Lorem ipsum dolor sit..."
                   IconBackgroundColor="SlateGray"
                   IconImageSource="user.png"/>
```

Un `CardView` peut également être instancié dans le code:

```csharp
CardView card = new CardView
{
    BorderColor = Color.DarkGray,
    CardTitle = "Slavko Vlasic",
    CardDescription = "Lorem ipsum dolor sit...",
    IconBackgroundColor = Color.SlateGray,
    IconImageSource = ImageSource.FromFile("user.png")
};
```

### <a name="bind-data-with-mvvm"></a>Lier des données avec MVVM

Les `BindableProperty` objets de la `CardView` classe autorisent les liaisons de style MVVM (Model-View-ViewModel). L’exemple d’application contient `PersonCollectionViewModel` une classe qui définit une propriété de collection unique:

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{
    ...
    public List<PersonViewModel> Items
    {
        get
        {
            return items;
        }
        set
        {
            items = value;
            NotifyPropertyChanged();
        }
    }
    ...
}
```

La `PersonViewModel` classe représente un profil personnel:

```csharp
public class PersonViewModel : INotifyPropertyChanged
{
    ...

    string photo;
    public string Photo
    {
        get
        {
            return photo;
        }
        set
        {
            photo = value;
            NotifyPropertyChanged();
        }
    }

    string name;
    public string Name
    {
        get
        {
            return name;
        }
        set
        {
            name = value;
            NotifyPropertyChanged();
        }
    }

    string bio;
    public string Bio
    {
        get
        {
            return bio;
        }
        set
        {
            bio = value;
            NotifyPropertyChanged();
        }
    }
    ...
}
```

Le `CardView` peut être utilisé pour restituer la collection `PersonViewModel` d’objets sous la forme d’une liste de cartes. L’exemple suivant montre comment lier une `PersonViewCollection` instance à une `StackLayout` instance en XAML:

```xaml
<StackLayout HorizontalOptions="Fill"
             VerticalOptions="Fill"
             BindableLayout.ItemsSource="{Binding Items}">
    <BindableLayout.ItemTemplate>
        <DataTemplate>
            <controls:CardView Margin="4"
                               BorderColor="DarkGray"
                               IconBackgroundColor="SlateGray"
                               BindingContext="{Binding .}"
                               CardTitle="{Binding Name}"
                               CardDescription="{Binding Bio}"
                               IconImageSource="{Binding Photo}"/>
        </DataTemplate>
    </BindableLayout.ItemTemplate>
</StackLayout>
```

La `Items` propriété sur une `PersonViewCollection` `StackLayout` instance est liée à à l’aide d’une disposition pouvant être liée. Définit l’apparence de chaque `CardView` objet et les liaisons de données aux propriétés sur un `PersonViewModel`. `DataTemplate` Lorsque est défini, un `CardView` objet est créé pour chaque `PersonView` objet de la `Items` collection. `BindingContext` `BindingContext` Est défini comme indiqué dans l’exemple suivant:

```csharp
public partial class CardViewMvvmPage : ContentPage
{
    public CardViewMvvmPage()
    {
        InitializeComponent();
        BindingContext = DataService.GetPersonCollection();
    }
}
```

Pour plus d’informations sur la liaison de données, consultez [liaison de données Xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md). Pour plus d’informations `BindableProperty` sur les objets, consultez [propriétés pouvant être liées](~/xamarin-forms/xaml/bindable-properties.md).

## <a name="customize-appearance-with-a-controltemplate"></a>Personnaliser l’apparence avec un ControlTemplate

Un contrôle personnalisé qui dérive de la `ContentView` classe peut définir l’apparence à l’aide de XAML, du code ou ne pas définir l’apparence du tout. Quelle que soit la façon dont l’apparence `ControlTemplate` est définie, un objet peut remplacer l’apparence par une disposition personnalisée.

La `CardView` disposition peut occuper trop d’espace pour certains cas d’usage. Un `ControlTemplate` peut remplacer la `CardView` disposition pour fournir une vue plus compacte, adaptée à une liste condensée:

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <ControlTemplate x:Key="CardViewCompressed">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="100" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="100" />
                    <ColumnDefinition Width="100*" />
                </Grid.ColumnDefinitions>
                <Image Grid.Row="0"
                       Grid.Column="0"
                       Source="{TemplateBinding IconImageSource}"
                       BackgroundColor="{TemplateBinding IconBackgroundColor}"
                       WidthRequest="100"
                       HeightRequest="100"
                       Aspect="AspectFill"
                       HorizontalOptions="Center"
                       VerticalOptions="Center"/>
                <StackLayout Grid.Row="0"
                             Grid.Column="1">
                    <Label Text="{TemplateBinding CardTitle}"
                           FontAttributes="Bold" />
                    <Label Text="{TemplateBinding CardDescription}" />
                </StackLayout>
            </Grid>
        </ControlTemplate>
    </ResourceDictionary>
</ContentPage.Resources>
```

La liaison de données `ControlTemplate` dans un `TemplateBinding` utilise l’extension de balisage pour spécifier des liaisons. La `ControlTemplate` propriété peut ensuite être définie sur l’objet ControlTemplate défini, à l’aide `x:Key` de sa valeur. L’exemple suivant montre le `ControlTemplate` jeu de propriétés sur `CardView` une instance:

```xaml
<controls:CardView ControlTemplate="{StaticResource CardViewCompressed}"/>
```

Les captures d’écran suivantes montrent `CardView` une instance `CardView` standard `ControlTemplate` et dont a été remplacé:

[![Capture d’écran de ControlTemplate ControlTemplate](contentview-images/cardview-controltemplates-cropped.png)](contentview-images/cardview-controltemplates.png#lightbox)

Pour plus d’informations sur les modèles de contrôle, consultez [modèles de contrôle Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md).

## <a name="related-links"></a>Liens connexes

* [Exemple d’application CardView](https://docs.microsoft.com/en-us/samples/xamarin/xamarin-forms-samples/userinterface-cardview/)
* [Liaison de données Xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
* [Propriétés pouvant être liées](~/xamarin-forms/xaml/bindable-properties.md).
* [Modèles de contrôle Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)
