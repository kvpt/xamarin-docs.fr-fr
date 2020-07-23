---
title: Création d’un Xamarin.Forms DataTemplateSelector
description: Cet article explique comment créer et consommer un DataTemplateSelector, qui permet de choisir un DataTemplate au moment de l’exécution, en fonction de la valeur d’une propriété liée aux données.
ms.prod: xamarin
ms.assetid: A4629E8F-2BAF-45CE-A76E-DF225FE8D26C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7cbeeb9a0eed37ec109b2e71c46e3f04cd08822d
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936446"
---
# <a name="creating-a-xamarinforms-datatemplateselector"></a>Création d’un Xamarin.Forms DataTemplateSelector

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplateselector)

_Un DataTemplateSelector peut être utilisé pour choisir un DataTemplate au moment de l’exécution en fonction de la valeur d’une propriété liée aux données. Cela permet d’appliquer plusieurs DataTemplate au même type d’objet, afin de personnaliser l’apparence d’objets particuliers. Cet article explique comment créer et utiliser un DataTemplateSelector._

Un sélecteur de modèle de données permet des scénarios tels qu’une [`ListView`](xref:Xamarin.Forms.ListView) liaison à une collection d’objets, où l’apparence de chaque objet dans `ListView` peut être choisie au moment de l’exécution par le sélecteur de modèle de données retournant un particulier [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) .

## <a name="creating-a-datatemplateselector"></a>Création d’un DataTemplateSelector

Un sélecteur de modèle de données est implémenté par la création d’une classe qui hérite de [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) . La `OnSelectTemplate` méthode est ensuite substituée pour retourner un particulier [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , comme illustré dans l’exemple de code suivant :

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

Une instance de la classe de sélecteur de modèle de données peut ensuite être assignée aux Xamarin.Forms Propriétés de contrôle telles que [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) . Pour obtenir la liste des propriétés valides, consultez [Création d’un DataTemplate](~/xamarin-forms/app-fundamentals/templates/data-templates/creating.md).

### <a name="limitations"></a>Limites

[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)les instances présentent les limitations suivantes :

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

Ce niveau de page [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) définit deux [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) instances et une `PersonDataTemplateSelector` instance. L’instance de `PersonDataTemplateSelector` affecte aux propriétés `ValidTemplate` et `InvalidTemplate` les instances appropriées de `DataTemplate` à l’aide de l’extension de balisage `StaticResource`. Notez que même si les ressources sont définies dans la page [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , elles peuvent également être définies au niveau du contrôle ou de l’application.

L' `PersonDataTemplateSelector` instance est consommée en l’assignant à la [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) propriété, comme illustré dans l’exemple de code suivant :

```xaml
<ListView x:Name="listView" ItemTemplate="{StaticResource personDataTemplateSelector}" />
```

Lors de l’exécution, le [`ListView`](xref:Xamarin.Forms.ListView) appelle la `PersonDataTemplateSelector.OnSelectTemplate` méthode pour chacun des éléments de la collection sous-jacente, avec l’appel passant l’objet de données comme `item` paramètre. Le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) retourné par la méthode est ensuite appliqué à cet objet.

Les captures d’écran suivantes montrent le résultat de l' [`ListView`](xref:Xamarin.Forms.ListView) application de `PersonDataTemplateSelector` à chaque objet dans la collection sous-jacente :

![ListView avec un sélecteur de modèle de données](selector-images/data-template-selector.png)

Tout objet `Person` ayant une valeur de propriété `DateOfBirth` supérieure ou égale à 1980 est affiché en vert, les objets restants sont affichés en rouge.

## <a name="consuming-a-datatemplateselector-in-cnum"></a>Consommation d’un DataTemplateSelector en C&num;

En C#, l' `PersonDataTemplateSelector` instance peut être instanciée et assignée à la [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) propriété, comme illustré dans l’exemple de code suivant :

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

L' `PersonDataTemplateSelector` instance affecte `ValidTemplate` à ses `InvalidTemplate` Propriétés et les [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) instances appropriées créées par la `SetupDataTemplates` méthode. Lors de l’exécution, le [`ListView`](xref:Xamarin.Forms.ListView) appelle la `PersonDataTemplateSelector.OnSelectTemplate` méthode pour chacun des éléments de la collection sous-jacente, avec l’appel passant l’objet de données comme `item` paramètre. Le `DataTemplate` retourné par la méthode est ensuite appliqué à cet objet.

## <a name="summary"></a>Résumé

Cet article a montré comment créer et consommer un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) . Un `DataTemplateSelector` peut être utilisé pour choisir un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) au moment de l’exécution en fonction de la valeur d’une propriété liée aux données. Cela permet d’appliquer plusieurs instances de `DataTemplate` au même type d’objet, pour personnaliser l’apparence d’objets en particulier.

## <a name="related-links"></a>Liens associés

- [Sélecteur de modèle de données (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplateselector)
- [DataTemplateSelector](xref:Xamarin.Forms.DataTemplateSelector)
