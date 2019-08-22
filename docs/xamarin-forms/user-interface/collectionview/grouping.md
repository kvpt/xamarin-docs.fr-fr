---
title: Xamarin. Forms CollectionView, regroupement
description: CollectionView peut afficher des données regroupées correctement en affectant à sa propriété IsGrouped la valeur true.
ms.prod: xamarin
ms.assetid: 7E494245-FDBD-49D6-B7FA-CEF976EB59BB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: 8fd37999428c2813bbf96de3bcbd6ebd1fe0879d
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69894026"
---
# <a name="xamarinforms-collectionview-grouping"></a>Xamarin. Forms CollectionView, regroupement

![](~/media/shared/preview.png "Cette API est actuellement en préversion")

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos)

Les jeux de données volumineux peuvent souvent devenir difficiles à manier lorsqu’ils sont présentés dans une liste de défilement continu. Dans ce scénario, l’Organisation des données en groupes peut améliorer l’expérience utilisateur en facilitant la navigation dans les données.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)prend en charge l’affichage des données groupées et définit les propriétés suivantes qui contrôlent la façon dont elles sont présentées:

- `IsGrouped`, de type `bool`, indique si les données sous-jacentes doivent être affichées dans des groupes. La valeur par défaut de cette propriété est `false`.
- `GroupHeaderTemplate`, de type [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), le modèle à utiliser pour l’en-tête de chaque groupe.
- `GroupFooterTemplate`, de type [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), le modèle à utiliser pour le pied de page de chaque groupe.

Ces propriétés sont sauvegardées par [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) des objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données.

Pour plus d’informations sur les modèles de données, consultez [Modèles de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

> [!IMPORTANT]
> Le regroupement de données [`CollectionView`](xref:Xamarin.Forms.CollectionView) avec est actuellement pris en charge uniquement sur iOS.

## <a name="group-data"></a>Grouper les données

Les données doivent être regroupées pour pouvoir être affichées. Pour ce faire, vous pouvez créer une liste de groupes, où chaque groupe est une liste d’éléments. La liste des groupes doit être une `IEnumerable<T>` collection, où `T` définit deux éléments de données:

- Nom de groupe.
- `IEnumerable` Collection qui définit les éléments appartenant au groupe.

Le processus de regroupement de données consiste donc à:

- Créez un type qui modélise un seul élément.
- Créez un type qui modélise un seul groupe d’éléments.
- Créez une `IEnumerable<T>` collection, où `T` est le type qui modélise un seul groupe d’éléments. Ce regroupement est donc un ensemble de groupes, qui stocke les données groupées.
- Ajoutez des données à `IEnumerable<T>` la collection.

### <a name="example"></a>Exemple

Lors du regroupement de données, la première étape consiste à créer un type qui modélise un seul élément. L’exemple suivant illustre la `Animal` classe de l’exemple d’application:

```csharp
public class Animal
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

La `Animal` classe modélise un élément unique. Un type qui modélise un groupe d’éléments peut ensuite être créé. L’exemple suivant illustre la `AnimalGroup` classe de l’exemple d’application:

```csharp
public class AnimalGroup : List<Animal>
{
    public string Name { get; private set; }

    public AnimalGroup(string name, List<Animal> animals) : base(animals)
    {
        Name = name;
    }
}
```

La `AnimalGroup` classe hérite de la `List<T>` classe et ajoute une `Name` propriété qui représente le nom du groupe.

Une `IEnumerable<T>` collection de groupes peut ensuite être créée:

```csharp
public List<AnimalGroup> Animals { get; private set; } = new List<AnimalGroup>();
```

Ce code définit une collection nommée `Animals`, où chaque élément de la collection est un `AnimalGroup` objet. Chaque `AnimalGroup` objet comprend un nom et une `List<Animal>` collection qui définit les `Animal` objets dans le groupe.

Les données groupées peuvent ensuite être ajoutées à `Animals` la collection:

```csharp
Animals.Add(new AnimalGroup("Bears", new List<Animal>
{
    new Animal
    {
        Name = "American Black Bear",
        Location = "North America",
        Details = "Details about the bear go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/0/08/01_Schwarzbär.jpg"
    },
    new Animal
    {
        Name = "Asian Black Bear",
        Location = "Asia",
        Details = "Details about the bear go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/b/b7/Ursus_thibetanus_3_%28Wroclaw_zoo%29.JPG/180px-Ursus_thibetanus_3_%28Wroclaw_zoo%29.JPG"
    },
    // ...
}));

Animals.Add(new AnimalGroup("Monkeys", new List<Animal>
{
    new Animal
    {
        Name = "Baboon",
        Location = "Africa & Asia",
        Details = "Details about the monkey go here.",
        ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
    },
    new Animal
    {
        Name = "Capuchin Monkey",
        Location = "Central & South America",
        Details = "Details about the monkey go here.",
        ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg"
    },
    new Animal
    {
        Name = "Blue Monkey",
        Location = "Central and East Africa",
        Details = "Details about the monkey go here.",
        ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg"
    },
    // ...
}));
```

Ce code crée deux groupes dans la `Animals` collection. Le premier `AnimalGroup` est nommé `Bears`et contient une `List<Animal>` collection de détails de support. Le deuxième `AnimalGroup` est nommé `Monkeys`et contient une `List<Animal>` collection de détails de singe.

## <a name="display-grouped-data"></a>Afficher des données groupées

[`CollectionView`](xref:Xamarin.Forms.CollectionView)affiche des données groupées, à condition que les données aient été regroupées correctement, en `IsGrouped` affectant `true`à la propriété la valeur:

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                ...
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView
{
    IsGrouped = true
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
// ...
```

L’apparence de chaque élément dans [`CollectionView`](xref:Xamarin.Forms.CollectionView) est définie en affectant à [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)la propriété la valeur. Pour plus d’informations, consultez [définir l’apparence des éléments](~/xamarin-forms/user-interface/collectionview/populate-data.md#define-item-appearance).

> [!NOTE]
> Par défaut, [`CollectionView`](xref:Xamarin.Forms.CollectionView) affiche le nom du groupe dans l’en-tête et le pied de page de groupe. Ce comportement peut être modifié en personnalisant l’en-tête de groupe et le pied de page de groupe.

## <a name="customize-the-group-header"></a>Personnaliser l’en-tête de groupe

L’apparence de chaque en-tête de groupe peut être personnalisée `CollectionView.GroupHeaderTemplate` en affectant [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)à la propriété:

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true">
    ...
    <CollectionView.GroupHeaderTemplate>
        <DataTemplate>
            <Label Text="{Binding Name}"
                   BackgroundColor="LightGray"
                   FontSize="Large"
                   FontAttributes="Bold" />
        </DataTemplate>
    </CollectionView.GroupHeaderTemplate>
</CollectionView>
```

Dans cet exemple, chaque en-tête de groupe est [`Label`](xref:Xamarin.Forms.Label) défini sur un qui affiche le nom du groupe et qui a d’autres propriétés d’apparence définies.

## <a name="customize-the-group-footer"></a>Personnaliser le pied de page du groupe

L’apparence de chaque pied de page de groupe peut être personnalisée en `CollectionView.GroupFooterTemplate` affectant [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)à la propriété:

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true">
    ...
    <CollectionView.GroupFooterTemplate>
        <DataTemplate>
            <Label Text="{Binding Count, StringFormat='Total animals: {0:D}'}"
                   Margin="0,0,0,10" />
        </DataTemplate>
    </CollectionView.GroupFooterTemplate>
</CollectionView>
```

Dans cet exemple, chaque pied de page de groupe est défini [`Label`](xref:Xamarin.Forms.Label) sur un qui affiche le nombre d’éléments dans le groupe.

## <a name="empty-groups"></a>Groupes vides

Quand un [`CollectionView`](xref:Xamarin.Forms.CollectionView) affiche des données groupées, il affiche tous les groupes qui sont vides. Ces groupes s’affichent avec un en-tête et un pied de page de groupe, indiquant que le groupe est vide.

> [!NOTE]
> Sur iOS 10 et les versions antérieures, les en-têtes et pieds de page de groupe pour les groupes vides peuvent tous `CollectionView`être affichés en haut de.

## <a name="group-without-templates"></a>Groupe sans modèles

[`CollectionView`](xref:Xamarin.Forms.CollectionView)peut afficher des données regroupées correctement sans [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) affecter [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)à la propriété la valeur:

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true" />
```

Dans ce scénario, des données significatives peuvent être affichées en substituant la `ToString` méthode dans le type qui modélise un seul élément, et le type qui modélise un seul groupe d’éléments.

## <a name="related-links"></a>Liens connexes

- [CollectionView (exemple)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/)
- [Modèles de données Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
