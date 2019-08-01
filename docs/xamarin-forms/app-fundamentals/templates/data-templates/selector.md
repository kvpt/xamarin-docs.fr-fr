---
title: Création d’un DataTemplateSelector Xamarin.Forms
description: Cet article explique comment créer et consommer un DataTemplateSelector, qui permet de choisir un DataTemplate au moment de l’exécution, en fonction de la valeur d’une propriété liée aux données.
ms.prod: xamarin
ms.assetid: A4629E8F-2BAF-45CE-A76E-DF225FE8D26C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 684820a646527b382b1dbb936b9256fd4457803e
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68651895"
---
# <a name="creating-a-xamarinforms-datatemplateselector"></a>Création d’un DataTemplateSelector Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplateselector)

_Vous pouvez utiliser un DataTemplateSelector pour choisir un DataTemplate au moment de l’exécution, en fonction de la valeur d’une propriété liée aux données. Cela permet d’appliquer plusieurs DataTemplates au même type d’objet pour personnaliser l’apparence d’objets particuliers. Cet article explique comment créer et consommer un DataTemplateSelector._

Avec un sélecteur de modèle de données, vous pouvez lier [`ListView`](xref:Xamarin.Forms.ListView) à une collection d’objets, où l’apparence de chaque objet du `ListView` peut être choisie au moment de l’exécution par le sélecteur de modèle de données qui retourne un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) particulier.

## <a name="creating-a-datatemplateselector"></a>Création d’un DataTemplateSelector

Vous implémentez un sélecteur de modèle de données en créant une classe qui hérite de [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector). La méthode `OnSelectTemplate` est ensuite substituée pour retourner un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) particulier, comme indiqué dans l’exemple de code suivant :

```csharp
public class PersonDataTemplateSelector : DataTemplateSelector
{
  public DataTemplate ValidTemplate { get; set; }
  public DataTemplate InvalidTemplate { get; set; }

  protected override DataTemplate OnSelectTemplate (object item, BindableObject container)
  {
    return ((Person)item).DateOfBirth.Year >= 1980 ? ValidTemplate : InvalidTemplate;
  }
}
```

La méthode `OnSelectTemplate` retourne le modèle approprié en fonction de la valeur de la propriété `DateOfBirth`. Le modèle à retourner représente la valeur de la propriété `ValidTemplate` ou de la propriété `InvalidTemplate`, qui sont définies durant la consommation de `PersonDataTemplateSelector`.

Vous pouvez ensuite affecter une instance de la classe de sélecteur de modèle de données à des propriétés de contrôle Xamarin.Forms telles que [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1). Pour obtenir la liste des propriétés valides, consultez [Création d’un DataTemplate](~/xamarin-forms/app-fundamentals/templates/data-templates/creating.md).

### <a name="limitations"></a>Limitations

Les instances de [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) ont les limitations suivantes :

- La sous-classe `DataTemplateSelector` doit toujours retourner le même modèle pour les mêmes données si elle est interrogée plusieurs fois.
- La sous-classe `DataTemplateSelector` ne doit pas retourner une autre sous-classe `DataTemplateSelector`.
- La sous-classe `DataTemplateSelector` ne doit pas retourner de nouvelles instances de `DataTemplate` à chaque appel. À la place, la même instance doit être retournée. Sinon, une fuite de mémoire se produit et la virtualisation est désactivée.
- Sur Android, il ne peut exister plus de 20 modèles de données différents par `ListView`.

## <a name="consuming-a-datatemplateselector-in-xaml"></a>Consommation d’un DataTemplateSelector en XAML

En XAML, vous pouvez instancier `PersonDataTemplateSelector` en le déclarant en tant que ressource, comme indiqué dans l’exemple de code suivant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Selector;assembly=Selector" x:Class="Selector.HomePage">
    <ContentPage.Resources>
        <ResourceDictionary>
            <DataTemplate x:Key="validPersonTemplate">
                <ViewCell>
                   ...
                </ViewCell>
            </DataTemplate>
            <DataTemplate x:Key="invalidPersonTemplate">
                <ViewCell>
                   ...
                </ViewCell>
            </DataTemplate>
            <local:PersonDataTemplateSelector x:Key="personDataTemplateSelector"
                ValidTemplate="{StaticResource validPersonTemplate}"
                InvalidTemplate="{StaticResource invalidPersonTemplate}" />
        </ResourceDictionary>
    </ContentPage.Resources>
  ...
</ContentPage>
```

Le niveau de page [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) définit deux instances de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) et une instance de `PersonDataTemplateSelector`. L’instance de `PersonDataTemplateSelector` affecte aux propriétés `ValidTemplate` et `InvalidTemplate` les instances appropriées de `DataTemplate` à l’aide de l’extension de balisage `StaticResource`. Notez que même si les ressources sont définies dans le [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de la page, elles peuvent également l’être au niveau du contrôle ou de l’application.

Vous consommez l’instance de `PersonDataTemplateSelector` en l’affectant à la propriété [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1), comme indiqué dans l’exemple de code suivant :

```xaml
<ListView x:Name="listView" ItemTemplate="{StaticResource personDataTemplateSelector}" />
```

Au moment de l’exécution, [`ListView`](xref:Xamarin.Forms.ListView) appelle la méthode `PersonDataTemplateSelector.OnSelectTemplate` pour chacun des éléments de la collection sous-jacente. Cet appel passe l’objet de données en tant que paramètre `item`. Le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) retourné par la méthode est ensuite appliqué à cet objet.

Les captures d’écran suivantes montrent le résultat de [`ListView`](xref:Xamarin.Forms.ListView) appliquant `PersonDataTemplateSelector` à chaque objet de la collection sous-jacente :

![](selector-images/data-template-selector.png "ListView avec un sélecteur de modèle de données")

Tout objet `Person` ayant une valeur de propriété `DateOfBirth` supérieure ou égale à 1980 est affiché en vert, les objets restants sont affichés en rouge.

## <a name="consuming-a-datatemplateselector-in-cnum"></a>Consommation d’un DataTemplateSelector en C&num;

En C#, vous pouvez instancier `PersonDataTemplateSelector` et l’affecter à la propriété [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1), comme indiqué dans l’exemple de code suivant :

```csharp
public class HomePageCS : ContentPage
{
  DataTemplate validTemplate;
  DataTemplate invalidTemplate;

  public HomePageCS ()
  {
    ...
    SetupDataTemplates ();
    var listView = new ListView {
      ItemsSource = people,
      ItemTemplate = new PersonDataTemplateSelector {
        ValidTemplate = validTemplate,
        InvalidTemplate = invalidTemplate }
    };

    Content = new StackLayout {
      Margin = new Thickness (20),
      Children = {
        ...
        listView
      }
    };
  }
  ...  
}
```

L’instance de `PersonDataTemplateSelector` affecte aux propriétés `ValidTemplate` et `InvalidTemplate` les instances appropriées de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) créées par la méthode `SetupDataTemplates`. Au moment de l’exécution, [`ListView`](xref:Xamarin.Forms.ListView) appelle la méthode `PersonDataTemplateSelector.OnSelectTemplate` pour chacun des éléments de la collection sous-jacente. Cet appel passe l’objet de données en tant que paramètre `item`. Le `DataTemplate` retourné par la méthode est ensuite appliqué à cet objet.

## <a name="summary"></a>Récapitulatif

Cet article a montré comment créer et consommer [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector). Vous pouvez utiliser un `DataTemplateSelector` pour choisir un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) au moment de l’exécution, en fonction de la valeur d’une propriété liée aux données. Cela permet d’appliquer plusieurs instances de `DataTemplate` au même type d’objet, pour personnaliser l’apparence d’objets particuliers.


## <a name="related-links"></a>Liens associés

- [Sélecteur de modèle de données (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplateselector)
- [DataTemplateSelector](xref:Xamarin.Forms.DataTemplateSelector)
