---
title: Xamarin.Forms CollectionView Grouping
description: CollectionView peut afficher des données correctement regroupées en définissant sa propriété IsGrouped.
ms.prod: xamarin
ms.assetid: 7E494245-FDBD-49D6-B7FA-CEF976EB59BB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/17/2019
ms.openlocfilehash: 8360123b01f36bde084b4dc315109e6bdaef2207
ms.sourcegitcommit: 99aa05bd9b5e3f66d134066b860f41b54fa2d850
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103280"
---
# <a name="xamarinforms-collectionview-grouping"></a>Xamarin.Forms CollectionView Grouping

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

Les grands ensembles de données peuvent souvent devenir lourds lorsqu’ils sont présentés dans une liste de défilement continu. Dans ce scénario, l’organisation des données en groupes peut améliorer l’expérience utilisateur en facilitant la navigation des données.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)prend en charge l’affichage des données groupées et définit les propriétés suivantes qui contrôlent la façon dont elles seront présentées :

- `IsGrouped`, du `bool`type , indique si les données sous-jacentes doivent être affichées en groupes. La valeur par défaut de cette propriété est `false`.
- `GroupHeaderTemplate`, de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)type , le modèle à utiliser pour l’en-tête de chaque groupe.
- `GroupFooterTemplate`, de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)type , le modèle à utiliser pour le pied de chaque groupe.

Ces propriétés [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) sont sauvegardées par des objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données.

Les captures d’écran suivantes montrent une [`CollectionView`](xref:Xamarin.Forms.CollectionView) affichage des données groupées:

[![Capture d’écran d’une donnée groupée dans un CollectionView, sur iOS et Android](grouping-images/grouped-data.png "CollectionView avec données groupées")](grouping-images/grouped-data-large.png#lightbox "CollectionView avec données groupées")

Pour plus d’informations sur les modèles de données, consultez [Modèles de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="group-data"></a>Données de groupe

Les données doivent être regroupées avant de pouvoir être affichées. Cela peut être accompli en créant une liste de groupes, où chaque groupe est une liste d’éléments. La liste des groupes `IEnumerable<T>` devrait `T` être une collection, où définit deux éléments de données :

- Un nom de groupe.
- Une `IEnumerable` collection qui définit les objets appartenant au groupe.

Le processus de regroupement des données consiste donc à :

- Créez un type qui modélise un seul élément.
- Créez un type qui modélise un seul groupe d’éléments.
- Créez `IEnumerable<T>` une collection, où `T` est le type qui modélise un seul groupe d’objets. Cette collection est donc une collection de groupes, qui stocke les données regroupées.
- Ajoutez des `IEnumerable<T>` données à la collecte.

### <a name="example"></a>Exemple

Lors du regroupement des données, la première étape consiste à créer un type qui modélise un seul élément. L’exemple suivant `Animal` montre la classe de l’application de l’échantillon :

```csharp
public class Animal
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

La `Animal` classe modèle un seul élément. Un type qui modélise un groupe d’éléments peut alors être créé. L’exemple suivant `AnimalGroup` montre la classe de l’application de l’échantillon :

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

La `AnimalGroup` classe hérite `List<T>` de la `Name` classe et ajoute une propriété qui représente le nom du groupe.

Une `IEnumerable<T>` collection de groupes peut alors être créée :

```csharp
public List<AnimalGroup> Animals { get; private set; } = new List<AnimalGroup>();
```

Ce code définit une `Animals`collection nommée , où `AnimalGroup` chaque élément de la collection est un objet. Chaque `AnimalGroup` objet comprend un nom `List<Animal>` et une `Animal` collection qui définit les objets du groupe.

Les données groupées peuvent `Animals` ensuite être ajoutées à la collecte :

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
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
    },
    new Animal
    {
        Name = "Capuchin Monkey",
        Location = "Central & South America",
        Details = "Details about the monkey go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg"
    },
    new Animal
    {
        Name = "Blue Monkey",
        Location = "Central and East Africa",
        Details = "Details about the monkey go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg"
    },
    // ...
}));
```

Ce code crée deux `Animals` groupes dans la collection. Le `AnimalGroup` premier `Bears`est nommé `List<Animal>` , et contient une collection de détails d’ours. Le `AnimalGroup` second `Monkeys`est nommé `List<Animal>` , et contient une collection de détails de singe.

## <a name="display-grouped-data"></a>Afficher les données groupées

[`CollectionView`](xref:Xamarin.Forms.CollectionView)affichera les données groupées, à condition que les `IsGrouped` données `true`ont été regroupées correctement, en définissant la propriété à :

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

L’apparence de chaque [`CollectionView`](xref:Xamarin.Forms.CollectionView) élément dans [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) le est [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)défini en fixant la propriété à un . Pour plus d’informations, voir [Définir l’apparence de l’article](~/xamarin-forms/user-interface/collectionview/populate-data.md#define-item-appearance).

> [!NOTE]
> Par défaut, [`CollectionView`](xref:Xamarin.Forms.CollectionView) affichera le nom du groupe dans l’en-tête et le pied du groupe. Ce comportement peut être modifié en personnalisant l’en-tête du groupe et le pied de groupe.

## <a name="customize-the-group-header"></a>Personnaliser l’en-tête du groupe

L’apparence de chaque en-tête de `CollectionView.GroupHeaderTemplate` groupe [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)peut être personnalisée en fixant la propriété à un :

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

Dans cet exemple, chaque en-tête de groupe est réglé sur un [`Label`](xref:Xamarin.Forms.Label) qui affiche le nom du groupe, et qui a d’autres propriétés d’apparence définies. Les captures d’écran suivantes montrent l’en-tête du groupe personnalisé :

[![Capture d’écran d’un en-tête de groupe personnalisé dans un CollectionView, sur iOS et Android](grouping-images/customized-header.png "CollectionView avec en-tête de groupe personnalisé")](grouping-images/customized-header-large.png#lightbox "CollectionView avec en-tête de groupe personnalisé")

## <a name="customize-the-group-footer"></a>Personnaliser le pied de groupe

L’apparence de chaque pied de groupe `CollectionView.GroupFooterTemplate` peut être [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)personnalisée en fixant la propriété à un :

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

Dans cet exemple, chaque pied de [`Label`](xref:Xamarin.Forms.Label) groupe est réglé sur un qui affiche le nombre d’éléments dans le groupe. Les captures d’écran suivantes montrent le pied de groupe personnalisé:

[![Capture d’écran d’un membre du groupe personnalisé dans une CollectionView, sur iOS et Android](grouping-images/customized-footer.png "CollectionView avec pied de groupe personnalisé")](grouping-images/customized-footer-large.png#lightbox "CollectionView avec pied de groupe personnalisé")

## <a name="empty-groups"></a>Groupes vides

Lorsqu’une [`CollectionView`](xref:Xamarin.Forms.CollectionView) donnée groupée affiche, elle affiche tous les groupes vides. Ces groupes seront affichés avec un en-tête de groupe et un pied, ce qui indique que le groupe est vide. Les captures d’écran suivantes montrent un groupe vide :

[![Capture d’écran d’un groupe vide dans un CollectionView, sur iOS et Android](grouping-images/empty-group.png "CollectionView avec un groupe vide")](grouping-images/empty-group-large.png#lightbox "CollectionView avec un groupe vide")

> [!NOTE]
> Sur iOS 10 et plus bas, les en-têtes de groupe et `CollectionView`les pieds pour les groupes vides peuvent tous être affichés en haut de la .

## <a name="group-without-templates"></a>Groupe sans modèles

[`CollectionView`](xref:Xamarin.Forms.CollectionView)peut afficher correctement les données [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) regroupées [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)sans définir la propriété à un :

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true" />
```

Dans ce scénario, des données significatives `ToString` peuvent être affichées en prépondant la méthode dans le type qui modèle un seul élément, et le type qui modélise un seul groupe d’éléments.

## <a name="related-links"></a>Liens connexes

- [CollectionView (échantillon)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Modèles de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
