---
title : « présentation Xamarin.Forms des modèles de données » Description : «les Xamarin.Forms modèles de données permettent de définir la présentation des données sur les contrôles pris en charge. Cet article fournit une introduction aux modèles de données, en examinant pourquoi ils sont nécessaires.»
ms. Prod : xamarin ms. AssetID : 4ED4ACF4-BE4A-44ED-8EAF-C03947B8663B ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 09/11/2017 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="introduction-to-xamarinforms-data-templates"></a>Présentation des Xamarin.Forms modèles de données

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Les modèles de données Xamarin. Forms permettent de définir la présentation des données sur les contrôles pris en charge. Cet article fournit une introduction aux modèles de données, en examinant pourquoi ils sont nécessaires._

Prenons l’exemple d’un [`ListView`](xref:Xamarin.Forms.ListView) qui affiche une collection d' `Person` objets. L’exemple de code suivant montre la définition de la classe `Person` :

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string Location { get; set; }
}
```

La classe `Person` définit les propriétés `Name`, `Age` et `Location`, qui peuvent être définies durant la création d’un objet `Person`. [`ListView`](xref:Xamarin.Forms.ListView)Est utilisé pour afficher la collection d' `Person` objets, comme indiqué dans l’exemple de code XAML suivant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataTemplates"
             ...>
    <StackLayout Margin="20">
        ...
        <ListView Margin="0,20,0,0">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:Person}">
                    <local:Person Name="Steve" Age="21" Location="USA" />
                    <local:Person Name="John" Age="37" Location="USA" />
                    <local:Person Name="Tom" Age="42" Location="UK" />
                    <local:Person Name="Lucas" Age="29" Location="Germany" />
                    <local:Person Name="Tariq" Age="39" Location="UK" />
                    <local:Person Name="Jane" Age="30" Location="USA" />
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </StackLayout>
</ContentPage>
```

Les éléments sont ajoutés au [`ListView`](xref:Xamarin.Forms.ListView) en XAML en initialisant la [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propriété à partir d’un tableau d' `Person` instances.

> [!NOTE]
> Notez que l’élément `x:Array` nécessite un attribut `Type` qui indique le type des éléments du tableau.

La page C# équivalente est présentée dans l’exemple de code suivant, qui initialise la [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propriété à un `List` des `Person` instances :

```csharp
public WithoutDataTemplatePageCS()
{
    ...
    var people = new List<Person>
    {
        new Person { Name = "Steve", Age = 21, Location = "USA" },
        new Person { Name = "John", Age = 37, Location = "USA" },
        new Person { Name = "Tom", Age = 42, Location = "UK" },
        new Person { Name = "Lucas", Age = 29, Location = "Germany" },
        new Person { Name = "Tariq", Age = 39, Location = "UK" },
        new Person { Name = "Jane", Age = 30, Location = "USA" }
    };

    Content = new StackLayout
    {
        Margin = new Thickness(20),
        Children = {
            ...
            new ListView { ItemsSource = people, Margin = new Thickness(0, 20, 0, 0) }
        }
    };
}
```

[`ListView`](xref:Xamarin.Forms.ListView)Appelle `ToString` lors de l’affichage des objets dans la collection. Dans la mesure où il n’existe pas de substitution de `Person.ToString`, `ToString` retourne le nom du type de chaque objet, comme indiqué dans les captures d’écran suivantes :

![](introduction-images/no-data-template.png "ListView without a Data Template")

L’objet `Person` peut remplacer la méthode `ToString` pour afficher les données significatives, comme indiqué dans l’exemple de code suivant :

```csharp
public class Person
{
  ...
  public override string ToString ()
  {
    return Name;
  }
}
```

Cela entraîne l' [`ListView`](xref:Xamarin.Forms.ListView) affichage de la `Person.Name` valeur de la propriété pour chaque objet de la collection, comme illustré dans les captures d’écran suivantes :

![](introduction-images/override-tostring.png "ListView with a Data Template")

La substitution de `Person.ToString` peut retourner une chaîne mise en forme composée des propriétés `Name`, `Age` et `Location`. Toutefois, cette approche offre uniquement un contrôle limité sur l’apparence de chaque élément de données. Pour plus de flexibilité, vous [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) pouvez créer un qui définit l’apparence des données.

## <a name="creating-a-datatemplate"></a>Création d’un DataTemplate

Un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) est utilisé pour spécifier l’apparence des données et utilise généralement la liaison de données pour afficher les données. Son scénario d’utilisation courant est lors de l’affichage de données à partir d’une collection d’objets dans un [`ListView`](xref:Xamarin.Forms.ListView) . Par exemple, quand `ListView` est lié à une collection d’objets `Person`, la propriété `ListView.ItemTemplate` a la valeur d’un `DataTemplate` qui définit l’apparence de chaque objet `Person` dans `ListView`. `DataTemplate` contient des éléments qui se lient aux valeurs de propriété de chaque objet `Person`. Pour plus d’informations sur la liaison de données, consultez [Notions de base de la liaison de données](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

Un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) peut être utilisé comme valeur pour les propriétés suivantes :

- [`ListView.HeaderTemplate`](xref:Xamarin.Forms.ListView.HeaderTemplate)
- [`ListView.FooterTemplate`](xref:Xamarin.Forms.ListView.FooterTemplate)
- [`ListView.GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate)
- [`ItemsView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1), qui est héritée par [`ListView`](xref:Xamarin.Forms.ListView) .
- [`MultiPage.ItemTemplate`](xref:Xamarin.Forms.MultiPage`1), qui est hérité par [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) , [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) et [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) .

> [!NOTE]
> Notez que, bien que [`TableView`](xref:Xamarin.Forms.TableView) effectue des utilisations d' [`Cell`](xref:Xamarin.Forms.Cell) objets, il n’utilise pas de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . En effet, les liaisons de données sont toujours définies directement sur les objets `Cell`.

Un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) qui est placé en tant qu’enfant direct des propriétés répertoriées ci-dessus est connu sous le nom de *modèle Inline*. Vous pouvez également définir un `DataTemplate` en tant que ressource au niveau du contrôle, au niveau de la page ou au niveau de l’application. Choix de l’emplacement de définition d’un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) impact à utiliser :

- Un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) défini au niveau du contrôle ne peut être appliqué qu’au contrôle.
- Un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) défini au niveau de la page peut être appliqué à plusieurs contrôles valides sur la page.
- Un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) défini au niveau de l’application peut être appliqué à des contrôles valides dans l’application.

Les modèles de données situés plus bas dans la hiérarchie de vues sont prioritaires par rapport à ceux définis plus haut quand ils partagent des attributs `x:Key`. Par exemple, un modèle de données au niveau de l’application est remplacé par un modèle de données au niveau de la page, et un modèle de données au niveau de la page est remplacé par un modèle de données au niveau du contrôle ou par un modèle de données inline.

## <a name="related-links"></a>Liens connexes

- [Apparence de cellule](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Modèles de données (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
