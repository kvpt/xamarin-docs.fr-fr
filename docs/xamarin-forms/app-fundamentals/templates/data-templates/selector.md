---
title: Création d’un DataTemplateSelector Xamarin.Forms
description: Cet article explique comment créer et consommer un DataTemplateSelector, qui permet de choisir un DataTemplate au moment de l’exécution, en fonction de la valeur d’une propriété liée aux données.
ms.prod: xamarin
ms.assetid: A4629E8F-2BAF-45CE-A76E-DF225FE8D26C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: e9a17bff9bd0a23d59faf7602544b25c7ec05a86
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771255"
---
# <a name="creating-a-xamarinforms-datatemplateselector"></a>Création d’un DataTemplateSelector Xamarin.Forms

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplateselector)

_Un DataTemplateSelector peut être utilisé pour choisir un DataTemplate à l’exécution en fonction de la valeur d’une propriété liée aux données. Cela permet d’appliquer plusieurs DataTemplates au même type d’objet, pour personnaliser l’apparence d’objets particuliers. Cet article montre comment créer et consommer un DataTemplateSelector._

Un sélecteur de modèles [`ListView`](xref:Xamarin.Forms.ListView) de données permet des scénarios tels qu’une `ListView` liaison à une collection d’objets, où l’apparence de chaque objet dans le peut être choisi au moment de l’exécution par le sélecteur de modèle de données retournant un particulier [`DataTemplate`](xref:Xamarin.Forms.DataTemplate).

## <a name="creating-a-datatemplateselector"></a>Création d’un DataTemplateSelector

Un sélecteur de modèle de données [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)est implémenté en créant une classe qui hérite de . La `OnSelectTemplate` méthode est alors remplacée pour [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)retourner un particulier, comme le montre l’exemple de code suivant :

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

Une instance de la classe de sélectionneur de modèle de données [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1)peut alors être assignée aux propriétés de contrôle de Xamarin.Forms telles que . Pour obtenir la liste des propriétés valides, consultez [Création d’un DataTemplate](~/xamarin-forms/app-fundamentals/templates/data-templates/creating.md).

### <a name="limitations"></a>Limites

[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)les cas ont les limites suivantes :

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

Ce niveau [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) page définit `PersonDataTemplateSelector` deux instances et une instance. L’instance de `PersonDataTemplateSelector` affecte aux propriétés `ValidTemplate` et `InvalidTemplate` les instances appropriées de `DataTemplate` à l’aide de l’extension de balisage `StaticResource`. Notez que si les ressources sont [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)définies dans la page, elles peuvent également être définies au niveau de contrôle ou au niveau de l’application.

L’instance `PersonDataTemplateSelector` est consommée en [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) l’assignant à la propriété, comme le montre l’exemple de code suivant :

```xaml
<ListView x:Name="listView" ItemTemplate="{StaticResource personDataTemplateSelector}" />
```

Au moment de [`ListView`](xref:Xamarin.Forms.ListView) l’exécution, les appels de la `PersonDataTemplateSelector.OnSelectTemplate` méthode pour chacun des `item` éléments de la collection sous-jacente, avec l’appel passant l’objet de données comme paramètre. Le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) qui est retourné par la méthode est ensuite appliqué à cet objet.

Les captures d’écran [`ListView`](xref:Xamarin.Forms.ListView) suivantes `PersonDataTemplateSelector` montrent le résultat de l’application de l’objet à chaque objet de la collection sous-jacente :

![](selector-images/data-template-selector.png "ListView with a Data Template Selector")

Tout objet `Person` ayant une valeur de propriété `DateOfBirth` supérieure ou égale à 1980 est affiché en vert, les objets restants sont affichés en rouge.

## <a name="consuming-a-datatemplateselector-in-cnum"></a>Consommation d’un DataTemplateSelector en C&num;

Dans le C, le `PersonDataTemplateSelector` peut être instantané [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) et attribué à la propriété, comme indiqué dans l’exemple de code suivant:

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

`PersonDataTemplateSelector` L’instance `ValidTemplate` définit `InvalidTemplate` ses et [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ses propriétés aux instances appropriées créées par la `SetupDataTemplates` méthode. Au moment de [`ListView`](xref:Xamarin.Forms.ListView) l’exécution, les appels de la `PersonDataTemplateSelector.OnSelectTemplate` méthode pour chacun des `item` éléments de la collection sous-jacente, avec l’appel passant l’objet de données comme paramètre. Le `DataTemplate` retourné par la méthode est ensuite appliqué à cet objet.

## <a name="summary"></a>Récapitulatif

Cet article a démontré comment [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)créer et consommer un . A `DataTemplateSelector` peut être utilisé [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) pour choisir un à l’heure d’exécution en fonction de la valeur d’une propriété liée aux données. Cela permet d’appliquer plusieurs instances de `DataTemplate` au même type d’objet, pour personnaliser l’apparence d’objets en particulier.

## <a name="related-links"></a>Liens connexes

- [Sélecteur de modèle de données (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplateselector)
- [DataTemplateSelector](xref:Xamarin.Forms.DataTemplateSelector)
