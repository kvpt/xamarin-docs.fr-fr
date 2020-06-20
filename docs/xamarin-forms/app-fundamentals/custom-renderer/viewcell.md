---
title: Personnalisation d’une ViewCell
description: Un Xamarin.Forms ViewCell est une cellule qui peut être ajoutée à un ListView ou un TableView, qui contient une vue définie par le développeur. Cet article montre comment créer un convertisseur personnalisé pour un ViewCell qui est hébergé dans un Xamarin.Forms contrôle ListView.
ms.prod: xamarin
ms.assetid: 61F378C9-6DEF-436B-ACC3-2324B25D404E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c908da816352e8b3790ded0bef932e1485170abd
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84573870"
---
# <a name="customizing-a-viewcell"></a>Personnalisation d’une ViewCell

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)

_Un Xamarin.Forms ViewCell est une cellule qui peut être ajoutée à un ListView ou un TableView, qui contient une vue définie par le développeur. Cet article montre comment créer un convertisseur personnalisé pour un ViewCell qui est hébergé dans un Xamarin.Forms contrôle ListView. Cela empêche les Xamarin.Forms calculs de disposition d’être appelés de façon répétée pendant le défilement de ListView._

Chaque Xamarin.Forms cellule a un convertisseur associé pour chaque plateforme qui crée une instance d’un contrôle natif. Lorsqu’un [`ViewCell`](xref:Xamarin.Forms.ViewCell) est rendu par une Xamarin.Forms application, dans iOS, la `ViewCellRenderer` classe est instanciée, qui à son tour instancie un `UITableViewCell` contrôle natif. Sur la plateforme Android, la classe `ViewCellRenderer` instancie un contrôle `View` natif. Sur la plateforme Windows universelle (UWP), la classe `ViewCellRenderer` instancie un `DataTemplate` natif. Pour plus d’informations sur le convertisseur et les classes de contrôle natifs sur lesquelles les Xamarin.Forms contrôles sont mappés, consultez [classes de base de convertisseur et contrôles natifs](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Le diagramme suivant illustre la relation entre le [`ViewCell`](xref:Xamarin.Forms.ViewCell) et les contrôles natifs correspondants qui l’implémentent :

![](viewcell-images/viewcell-classes.png "Relationship Between the ViewCell Control and the Implementing Native Controls")

Le processus de rendu peut être utilisé pour implémenter des personnalisations spécifiques à la plateforme en créant un convertisseur personnalisé pour un [`ViewCell`](xref:Xamarin.Forms.ViewCell) sur chaque plateforme. Le processus pour y parvenir est le suivant :

1. [Créez](#creating-the-custom-cell) une Xamarin.Forms cellule personnalisée.
1. [Utilise](#consuming-the-custom-cell) la cellule personnalisée de Xamarin.Forms .
1. [Créez](#creating-the-custom-renderer-on-each-platform) le renderer personnalisé pour la cellule sur chaque plateforme.

Chaque élément sera maintenant abordé à son tour, pour implémenter un `NativeCell` convertisseur qui tire parti d’une disposition spécifique à la plateforme pour chaque cellule hébergée à l’intérieur d’un Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) contrôle. Cela empêche les Xamarin.Forms calculs de disposition d’être appelés de façon répétée pendant le `ListView` défilement.

## <a name="creating-the-custom-cell"></a>Création de la cellule personnalisée

Un contrôle de cellule personnalisé peut être créé en sous-classant la [`ViewCell`](xref:Xamarin.Forms.ViewCell) classe, comme indiqué dans l’exemple de code suivant :

```csharp
public class NativeCell : ViewCell
{
  public static readonly BindableProperty NameProperty =
    BindableProperty.Create ("Name", typeof(string), typeof(NativeCell), "");

  public string Name {
    get { return (string)GetValue (NameProperty); }
    set { SetValue (NameProperty, value); }
  }

  public static readonly BindableProperty CategoryProperty =
    BindableProperty.Create ("Category", typeof(string), typeof(NativeCell), "");

  public string Category {
    get { return (string)GetValue (CategoryProperty); }
    set { SetValue (CategoryProperty, value); }
  }

  public static readonly BindableProperty ImageFilenameProperty =
    BindableProperty.Create ("ImageFilename", typeof(string), typeof(NativeCell), "");

  public string ImageFilename {
    get { return (string)GetValue (ImageFilenameProperty); }
    set { SetValue (ImageFilenameProperty, value); }
  }
}
```

La classe `NativeCell` est créée dans le projet de bibliothèque .NET Standard et définit l’API pour la cellule personnalisée. La cellule personnalisée expose les propriétés `Name`, `Category` et `ImageFilename` qui peuvent être affichées par le biais de la liaison de données. Pour plus d’informations sur la liaison de données, consultez [Notions de base de la liaison de données](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

## <a name="consuming-the-custom-cell"></a>Consommation de la cellule personnalisée

Vous pouvez référencer la cellule personnalisée `NativeCell` en Xaml dans le projet de bibliothèque .NET Standard en déclarant un espace de noms pour son emplacement et en utilisant le préfixe d’espace de noms sur l’élément de cellule personnalisée. L’exemple de code suivant montre comment la cellule personnalisée `NativeCell` peut être consommée par une page XAML :

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <ContentPage.Content>
          <StackLayout>
              <Label Text="Xamarin.Forms native cell" HorizontalTextAlignment="Center" />
              <ListView x:Name="listView" CachingStrategy="RecycleElement" ItemSelected="OnItemSelected">
                  <ListView.ItemTemplate>
                      <DataTemplate>
                          <local:NativeCell Name="{Binding Name}" Category="{Binding Category}" ImageFilename="{Binding ImageFilename}" />
                      </DataTemplate>
                  </ListView.ItemTemplate>
              </ListView>
          </StackLayout>
      </ContentPage.Content>
</ContentPage>
```

Le préfixe d’espace de noms `local` peut porter n’importe quel nom. Toutefois, les valeurs `clr-namespace` et `assembly` doivent correspondre aux détails du contrôle personnalisé. Une fois l’espace de noms déclaré, le préfixe est utilisé pour référencer la cellule personnalisée.

L’exemple de code suivant montre comment la cellule personnalisée `NativeCell` peut être consommé par une page C# :

```csharp
public class NativeCellPageCS : ContentPage
{
    ListView listView;

    public NativeCellPageCS()
    {
        listView = new ListView(ListViewCachingStrategy.RecycleElement)
        {
            ItemsSource = DataSource.GetList(),
            ItemTemplate = new DataTemplate(() =>
            {
                var nativeCell = new NativeCell();
                nativeCell.SetBinding(NativeCell.NameProperty, "Name");
                nativeCell.SetBinding(NativeCell.CategoryProperty, "Category");
                nativeCell.SetBinding(NativeCell.ImageFilenameProperty, "ImageFilename");

                return nativeCell;
            })
        };

        switch (Device.RuntimePlatform)
        {
            case Device.iOS:
                Padding = new Thickness(0, 20, 0, 0);
                break;
            case Device.Android:
            case Device.UWP:
                Padding = new Thickness(0);
                break;
        }

        Content = new StackLayout
        {
            Children = {
                new Label { Text = "Xamarin.Forms native cell", HorizontalTextAlignment = TextAlignment.Center },
                listView
            }
        };
        listView.ItemSelected += OnItemSelected;
    }
    ...
}
```

Un Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) contrôle est utilisé pour afficher une liste de données, qui est remplie par la [`ItemSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propriété. La [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) stratégie de mise en cache tente de réduire l' `ListView` encombrement mémoire et la vitesse d’exécution en recyclant les cellules de la liste. Pour plus d’informations, consultez [Stratégie de mise en cache](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy).

Chaque ligne dans la liste contient trois éléments de données : un nom, une catégorie et un nom de fichier image. La disposition de chaque ligne de la liste est définie par le `DataTemplate` référencé par le biais de la [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) propriété pouvant être liée. Le `DataTemplate` définit que chaque ligne de données dans la liste est un `NativeCell` qui affiche ses propriétés `Name`, `Category` et `ImageFilename` par le biais de la liaison de données. Pour plus d’informations sur le contrôle `ListView`, consultez [ListView](~/xamarin-forms/user-interface/listview/index.md).

Un renderer personnalisé peut maintenant être ajouté à chaque projet d’application afin de personnaliser la disposition spécifique à la plateforme pour chaque cellule.

## <a name="creating-the-custom-renderer-on-each-platform"></a>Création du renderer personnalisé sur chaque plateforme

Le processus de création de la classe de renderer personnalisé est le suivant :

1. Créez une sous-classe de la classe `ViewCellRenderer` qui affiche la cellule personnalisée.
1. Remplacez la méthode spécifique à la plateforme qui affiche le contrôle personnalisé et écrivez la logique pour le personnaliser.
1. Ajoutez un `ExportRenderer` attribut à la classe de convertisseur personnalisée pour spécifier qu’il sera utilisé pour restituer la Xamarin.Forms cellule personnalisée. Cet attribut est utilisé pour inscrire le convertisseur personnalisé avec Xamarin.Forms .

> [!NOTE]
> Pour la plupart des Xamarin.Forms éléments, il est facultatif de fournir un convertisseur personnalisé dans chaque projet de plateforme. Si un renderer personnalisé n’est pas inscrit, le renderer par défaut de la classe de base du contrôle est utilisé. Toutefois, les renderers personnalisés sont nécessaires dans chaque projet de plateforme durant la restitution d’un élément [ViewCell](xref:Xamarin.Forms.ViewCell).

Le diagramme suivant illustre les responsabilités de chaque projet dans l’exemple d’application ainsi que les relations qu’ils entretiennent les uns avec les autres :

![](viewcell-images/solution-structure.png "NativeCell Custom Renderer Project Responsibilities")

La cellule personnalisée `NativeCell` est restituée par des classes de renderer spécifiques à la plateforme qui dérivent toutes de la classe `ViewCellRenderer` pour chaque plateforme. Il en résulte la restitution de chaque cellule personnalisée `NativeCell` avec une disposition spécifique à la plateforme, comme le montrent les captures d’écran suivantes :

![](viewcell-images/screenshots.png "NativeCell on each Platform")

La classe `ViewCellRenderer` expose des méthodes spécifiques à la plateforme pour le rendu de la cellule personnalisée. Il s’agit de la méthode `GetCell` sur la plateforme iOS, de la méthode `GetCellCore` sur la plateforme Android et de la méthode `GetTemplate` sur UWP.

Chaque classe de convertisseur personnalisé est décorée avec un `ExportRenderer` attribut qui inscrit le convertisseur auprès de Xamarin.Forms . L’attribut accepte deux paramètres : le nom de type de la Xamarin.Forms cellule rendue et le nom de type du convertisseur personnalisé. Le préfixe `assembly` de l’attribut spécifie que l’attribut s’applique à la totalité de l’assembly.

Les sections suivantes décrivent l’implémentation de chaque classe de renderer personnalisé spécifique à la plateforme.

### <a name="creating-the-custom-renderer-on-ios"></a>Création du renderer personnalisé sur iOS

L’exemple de code suivant illustre le renderer personnalisé pour la plateforme iOS :

```csharp
[assembly: ExportRenderer(typeof(NativeCell), typeof(NativeiOSCellRenderer))]
namespace CustomRenderer.iOS
{
    public class NativeiOSCellRenderer : ViewCellRenderer
    {
        NativeiOSCell cell;

        public override UITableViewCell GetCell(Cell item, UITableViewCell reusableCell, UITableView tv)
        {
            var nativeCell = (NativeCell)item;

            cell = reusableCell as NativeiOSCell;
            if (cell == null)
                cell = new NativeiOSCell(item.GetType().FullName, nativeCell);
            else
                cell.NativeCell.PropertyChanged -= OnNativeCellPropertyChanged;

            nativeCell.PropertyChanged += OnNativeCellPropertyChanged;
            cell.UpdateCell(nativeCell);
            return cell;
        }
        ...
    }
}
```

La méthode `GetCell` est appelée pour générer chaque cellule à afficher. Chaque cellule est une instance `NativeiOSCell`, qui définit la disposition de la cellule et de ses données. Le fonctionnement de la `GetCell` méthode dépend de la [`ListView`](xref:Xamarin.Forms.ListView) stratégie de mise en cache :

- Quand la [`ListView`](xref:Xamarin.Forms.ListView) stratégie de mise en cache est [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) , la `GetCell` méthode est appelée pour chaque cellule. Une instance `NativeiOSCell` est créée pour chaque instance `NativeCell` initialement affichée sur l’écran. Quand l’utilisateur fait défiler la `ListView`, les instances `NativeiOSCell` sont réutilisées. Pour plus d’informations sur la réutilisation de cellules iOS, consultez [Réutilisation de cellule](~/ios/user-interface/controls/tables/populating-a-table-with-data.md).

  > [!NOTE]
  > Ce code de convertisseur personnalisé effectue une réutilisation de cellule même lorsque [`ListView`](xref:Xamarin.Forms.ListView) est défini pour conserver les cellules.

  Les données affichées par chaque instance `NativeiOSCell`, qu’elle soit créée ou réutilisée, sont actualisées avec les données de chaque instance `NativeCell` par la méthode `UpdateCell`.

  > [!NOTE]
  > La `OnNativeCellPropertyChanged` méthode ne sera jamais appelée quand la [`ListView`](xref:Xamarin.Forms.ListView) stratégie de mise en cache est définie pour conserver les cellules.

- Quand la [`ListView`](xref:Xamarin.Forms.ListView) stratégie de mise en cache est [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) , la `GetCell` méthode est appelée pour chaque cellule qui est initialement affichée à l’écran. Une instance `NativeiOSCell` est créée pour chaque instance `NativeCell` initialement affichée sur l’écran. Les données affichées par chaque instance `NativeiOSCell` sont actualisées avec les données de l’instance `NativeCell` par la méthode `UpdateCell`. Toutefois, la méthode `GetCell` n’est pas appelée quand l’utilisateur fait défiler la `ListView`. Au lieu de cela, les instances `NativeiOSCell` sont réutilisées. Des événements `PropertyChanged` sont déclenchés sur l’instance `NativeCell` quand ses données changent, et le gestionnaire d’événements `OnNativeCellPropertyChanged` met à jour les données dans chaque instance `NativeiOSCell` réutilisée.

L’exemple de code suivant montre la méthode `OnNativeCellPropertyChanged` qui est appelée quand un événement `PropertyChanged` est déclenché :

```csharp
namespace CustomRenderer.iOS
{
    public class NativeiOSCellRenderer : ViewCellRenderer
    {
        ...

        void OnNativeCellPropertyChanged(object sender, PropertyChangedEventArgs e)
        {
            var nativeCell = (NativeCell)sender;
            if (e.PropertyName == NativeCell.NameProperty.PropertyName)
            {
                cell.HeadingLabel.Text = nativeCell.Name;
            }
            else if (e.PropertyName == NativeCell.CategoryProperty.PropertyName)
            {
                cell.SubheadingLabel.Text = nativeCell.Category;
            }
            else if (e.PropertyName == NativeCell.ImageFilenameProperty.PropertyName)
            {
                cell.CellImageView.Image = cell.GetImage(nativeCell.ImageFilename);
            }
        }
    }
}
```

Cette méthode met à jour les données affichées par des instances `NativeiOSCell` réutilisées. La méthode pouvant être appelée plusieurs fois, la propriété qui est changée fait l’objet d’une recherche.

La classe `NativeiOSCell` définit la disposition pour chaque cellule et est illustrée dans l’exemple de code suivant :

```csharp
internal class NativeiOSCell : UITableViewCell, INativeElementView
{
  public UILabel HeadingLabel { get; set; }
  public UILabel SubheadingLabel { get; set; }
  public UIImageView CellImageView { get; set; }

  public NativeCell NativeCell { get; private set; }
  public Element Element => NativeCell;

  public NativeiOSCell(string cellId, NativeCell cell) : base(UITableViewCellStyle.Default, cellId)
  {
    NativeCell = cell;

    SelectionStyle = UITableViewCellSelectionStyle.Gray;
    ContentView.BackgroundColor = UIColor.FromRGB(255, 255, 224);
    CellImageView = new UIImageView();

    HeadingLabel = new UILabel()
    {
      Font = UIFont.FromName("Cochin-BoldItalic", 22f),
      TextColor = UIColor.FromRGB(127, 51, 0),
      BackgroundColor = UIColor.Clear
    };

    SubheadingLabel = new UILabel()
    {
      Font = UIFont.FromName("AmericanTypewriter", 12f),
      TextColor = UIColor.FromRGB(38, 127, 0),
      TextAlignment = UITextAlignment.Center,
      BackgroundColor = UIColor.Clear
    };

    ContentView.Add(HeadingLabel);
    ContentView.Add(SubheadingLabel);
    ContentView.Add(CellImageView);
  }

  public void UpdateCell(NativeCell cell)
  {
    HeadingLabel.Text = cell.Name;
    SubheadingLabel.Text = cell.Category;
    CellImageView.Image = GetImage(cell.ImageFilename);
  }

  public UIImage GetImage(string filename)
  {
    return (!string.IsNullOrWhiteSpace(filename)) ? UIImage.FromFile("Images/" + filename + ".jpg") : null;
  }

  public override void LayoutSubviews()
  {
    base.LayoutSubviews();

    HeadingLabel.Frame = new CGRect(5, 4, ContentView.Bounds.Width - 63, 25);
    SubheadingLabel.Frame = new CGRect(100, 18, 100, 20);
    CellImageView.Frame = new CGRect(ContentView.Bounds.Width - 63, 5, 33, 33);
  }
}
```

Cette classe définit les contrôles permettant de restituer le contenu de la cellule et sa disposition. La classe implémente l' [`INativeElementView`](xref:Xamarin.Forms.INativeElementView) interface, qui est requise lorsque [`ListView`](xref:Xamarin.Forms.ListView) utilise la [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) stratégie de mise en cache. Cette interface spécifie que la classe doit implémenter la [`Element`](xref:Xamarin.Forms.INativeElementView.Element) propriété, qui doit retourner les données de cellule personnalisées pour les cellules recyclées.

Le constructeur `NativeiOSCell` initialise l’apparence des propriétés `HeadingLabel`, `SubheadingLabel` et `CellImageView`. Ces propriétés sont utilisées pour afficher les données stockées dans l’instance `NativeCell`, la méthode `UpdateCell` étant appelée pour définir la valeur de chaque propriété. En outre, lorsque [`ListView`](xref:Xamarin.Forms.ListView) utilise la [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) stratégie de mise en cache, les données affichées par les `HeadingLabel` `SubheadingLabel` Propriétés, et `CellImageView` peuvent être mises à jour par la `OnNativeCellPropertyChanged` méthode dans le convertisseur personnalisé.

La disposition des cellules est effectuée par le remplacement de `LayoutSubviews`, qui définit les coordonnées de `HeadingLabel`, `SubheadingLabel` et `CellImageView` au sein de la cellule.

### <a name="creating-the-custom-renderer-on-android"></a>Création du renderer personnalisé sur Android

L’exemple de code suivant illustre le renderer personnalisé pour la plateforme Android :

```csharp
[assembly: ExportRenderer(typeof(NativeCell), typeof(NativeAndroidCellRenderer))]
namespace CustomRenderer.Droid
{
    public class NativeAndroidCellRenderer : ViewCellRenderer
    {
        NativeAndroidCell cell;

        protected override Android.Views.View GetCellCore(Cell item, Android.Views.View convertView, ViewGroup parent, Context context)
        {
            var nativeCell = (NativeCell)item;
            Console.WriteLine("\t\t" + nativeCell.Name);

            cell = convertView as NativeAndroidCell;
            if (cell == null)
            {
                cell = new NativeAndroidCell(context, nativeCell);
            }
            else
            {
                cell.NativeCell.PropertyChanged -= OnNativeCellPropertyChanged;
            }

            nativeCell.PropertyChanged += OnNativeCellPropertyChanged;

            cell.UpdateCell(nativeCell);
            return cell;
        }
        ...
    }
}
```

La méthode `GetCellCore` est appelée pour générer chaque cellule à afficher. Chaque cellule est une instance `NativeAndroidCell`, qui définit la disposition de la cellule et de ses données. Le fonctionnement de la `GetCellCore` méthode dépend de la [`ListView`](xref:Xamarin.Forms.ListView) stratégie de mise en cache :

- Quand la [`ListView`](xref:Xamarin.Forms.ListView) stratégie de mise en cache est [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) , la `GetCellCore` méthode est appelée pour chaque cellule. Une `NativeAndroidCell` est créée pour chaque instance `NativeCell` initialement affichée sur l’écran. Quand l’utilisateur fait défiler la `ListView`, les instances `NativeAndroidCell` sont réutilisées. Pour plus d’informations sur la réutilisation de cellules Android, consultez [Réutilisation de vues de ligne](~/android/user-interface/layouts/list-view/populating.md).

  > [!NOTE]
  > Notez que ce code de convertisseur personnalisé effectue une réutilisation de cellule même lorsque [`ListView`](xref:Xamarin.Forms.ListView) est défini pour conserver les cellules.

  Les données affichées par chaque instance `NativeAndroidCell`, qu’elle soit créée ou réutilisée, sont actualisées avec les données de chaque instance `NativeCell` par la méthode `UpdateCell`.

  > [!NOTE]
  > Notez que, bien que la `OnNativeCellPropertyChanged` méthode soit appelée lorsque [`ListView`](xref:Xamarin.Forms.ListView) est défini pour conserver les cellules, elle ne met pas à jour les `NativeAndroidCell` valeurs de propriété.

- Quand la [`ListView`](xref:Xamarin.Forms.ListView) stratégie de mise en cache est [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) , la `GetCellCore` méthode est appelée pour chaque cellule qui est initialement affichée à l’écran. Une instance `NativeAndroidCell` est créée pour chaque instance `NativeCell` initialement affichée sur l’écran. Les données affichées par chaque instance `NativeAndroidCell` sont actualisées avec les données de l’instance `NativeCell` par la méthode `UpdateCell`. Toutefois, la méthode `GetCellCore` n’est pas appelée quand l’utilisateur fait défiler la `ListView`. Au lieu de cela, les instances `NativeAndroidCell` sont réutilisées.  Des événements `PropertyChanged` sont déclenchés sur l’instance `NativeCell` quand ses données changent, et le gestionnaire d’événements `OnNativeCellPropertyChanged` met à jour les données dans chaque instance `NativeAndroidCell` réutilisée.

L’exemple de code suivant montre la méthode `OnNativeCellPropertyChanged` qui est appelée quand un événement `PropertyChanged` est déclenché :

```csharp
namespace CustomRenderer.Droid
{
    public class NativeAndroidCellRenderer : ViewCellRenderer
    {
        ...

        void OnNativeCellPropertyChanged(object sender, PropertyChangedEventArgs e)
        {
            var nativeCell = (NativeCell)sender;
            if (e.PropertyName == NativeCell.NameProperty.PropertyName)
            {
                cell.HeadingTextView.Text = nativeCell.Name;
            }
            else if (e.PropertyName == NativeCell.CategoryProperty.PropertyName)
            {
                cell.SubheadingTextView.Text = nativeCell.Category;
            }
            else if (e.PropertyName == NativeCell.ImageFilenameProperty.PropertyName)
            {
                cell.SetImage(nativeCell.ImageFilename);
            }
        }
    }
}
```

Cette méthode met à jour les données affichées par des instances `NativeAndroidCell` réutilisées. La méthode pouvant être appelée plusieurs fois, la propriété qui est changée fait l’objet d’une recherche.

La classe `NativeAndroidCell` définit la disposition pour chaque cellule et est illustrée dans l’exemple de code suivant :

```csharp
internal class NativeAndroidCell : LinearLayout, INativeElementView
{
  public TextView HeadingTextView { get; set; }
  public TextView SubheadingTextView { get; set; }
  public ImageView ImageView { get; set; }

  public NativeCell NativeCell { get; private set; }
  public Element Element => NativeCell;

  public NativeAndroidCell(Context context, NativeCell cell) : base(context)
  {
    NativeCell = cell;

    var view = (context as Activity).LayoutInflater.Inflate(Resource.Layout.NativeAndroidCell, null);
    HeadingTextView = view.FindViewById<TextView>(Resource.Id.HeadingText);
    SubheadingTextView = view.FindViewById<TextView>(Resource.Id.SubheadingText);
    ImageView = view.FindViewById<ImageView>(Resource.Id.Image);

    AddView(view);
  }

  public void UpdateCell(NativeCell cell)
  {
    HeadingTextView.Text = cell.Name;
    SubheadingTextView.Text = cell.Category;

    // Dispose of the old image
    if (ImageView.Drawable != null)
    {
      using (var image = ImageView.Drawable as BitmapDrawable)
      {
        if (image != null)
        {
          if (image.Bitmap != null)
          {
            image.Bitmap.Dispose();
          }
        }
      }
    }

    SetImage(cell.ImageFilename);
  }

  public void SetImage(string filename)
  {
    if (!string.IsNullOrWhiteSpace(filename))
    {
      // Display new image
      Context.Resources.GetBitmapAsync(filename).ContinueWith((t) =>
      {
        var bitmap = t.Result;
        if (bitmap != null)
        {
          ImageView.SetImageBitmap(bitmap);
          bitmap.Dispose();
        }
      }, TaskScheduler.FromCurrentSynchronizationContext());
    }
    else
    {
      // Clear the image
      ImageView.SetImageBitmap(null);
    }
  }
}
```

Cette classe définit les contrôles permettant de restituer le contenu de la cellule et sa disposition. La classe implémente l' [`INativeElementView`](xref:Xamarin.Forms.INativeElementView) interface, qui est requise lorsque [`ListView`](xref:Xamarin.Forms.ListView) utilise la [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) stratégie de mise en cache. Cette interface spécifie que la classe doit implémenter la [`Element`](xref:Xamarin.Forms.INativeElementView.Element) propriété, qui doit retourner les données de cellule personnalisées pour les cellules recyclées.

Le constructeur `NativeAndroidCell` agrandit la disposition `NativeAndroidCell` et initialise les propriétés `HeadingTextView`, `SubheadingTextView` et `ImageView` sur les contrôles dans la disposition agrandie. Ces propriétés sont utilisées pour afficher les données stockées dans l’instance `NativeCell`, la méthode `UpdateCell` étant appelée pour définir la valeur de chaque propriété. En outre, lorsque [`ListView`](xref:Xamarin.Forms.ListView) utilise la [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) stratégie de mise en cache, les données affichées par les `HeadingTextView` `SubheadingTextView` Propriétés, et `ImageView` peuvent être mises à jour par la `OnNativeCellPropertyChanged` méthode dans le convertisseur personnalisé.

L’exemple de code suivant montre la définition de la disposition pour le fichier de disposition `NativeAndroidCell.axml` :

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:padding="8dp"
    android:background="@drawable/CustomSelector">
    <LinearLayout
        android:id="@+id/Text"
        android:orientation="vertical"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:paddingLeft="10dip">
        <TextView
            android:id="@+id/HeadingText"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="#FF7F3300"
            android:textSize="20dip"
            android:textStyle="italic" />
        <TextView
            android:id="@+id/SubheadingText"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textSize="14dip"
            android:textColor="#FF267F00"
            android:paddingLeft="100dip" />
    </LinearLayout>
    <ImageView
        android:id="@+id/Image"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:padding="5dp"
        android:src="@drawable/icon"
        android:layout_alignParentRight="true" />
</RelativeLayout>
```

Cette disposition spécifie que deux contrôles `TextView` et un contrôle `ImageView` sont utilisés pour afficher le contenu de la cellule. Les deux contrôles `TextView` sont orientés verticalement dans un contrôle `LinearLayout`, tandis que tous les contrôles sont contenus dans un `RelativeLayout`.

### <a name="creating-the-custom-renderer-on-uwp"></a>Création du renderer personnalisé sur UWP

L’exemple de code suivant illustre le renderer personnalisé pour UWP :

```csharp
[assembly: ExportRenderer(typeof(NativeCell), typeof(NativeUWPCellRenderer))]
namespace CustomRenderer.UWP
{
    public class NativeUWPCellRenderer : ViewCellRenderer
    {
        public override Windows.UI.Xaml.DataTemplate GetTemplate(Cell cell)
        {
            return App.Current.Resources["ListViewItemTemplate"] as Windows.UI.Xaml.DataTemplate;
        }
    }
}
```

La méthode `GetTemplate` est appelée pour retourner la cellule à restituer pour chaque ligne de données dans la liste. Elle crée un `DataTemplate` pour chaque instance `NativeCell` qui s’affiche sur l’écran, le `DataTemplate` définissant l’apparence et le contenu de la cellule.

Le `DataTemplate` est stocké dans le dictionnaire de ressources de niveau application et est illustré dans l’exemple de code suivant :

```xaml
<DataTemplate x:Key="ListViewItemTemplate">
    <Grid Background="LightYellow">
        <Grid.Resources>
            <local:ConcatImageExtensionConverter x:Name="ConcatImageExtensionConverter" />
        </Grid.Resources>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.40*" />
            <ColumnDefinition Width="0.40*"/>
            <ColumnDefinition Width="0.20*" />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.ColumnSpan="2" Foreground="#7F3300" FontStyle="Italic" FontSize="22" VerticalAlignment="Top" Text="{Binding Name}" />
        <TextBlock Grid.RowSpan="2" Grid.Column="1" Foreground="#267F00" FontWeight="Bold" FontSize="12" VerticalAlignment="Bottom" Text="{Binding Category}" />
        <Image Grid.RowSpan="2" Grid.Column="2" HorizontalAlignment="Left" VerticalAlignment="Center" Source="{Binding ImageFilename, Converter={StaticResource ConcatImageExtensionConverter}}" Width="50" Height="50" />
        <Line Grid.Row="1" Grid.ColumnSpan="3" X1="0" X2="1" Margin="30,20,0,0" StrokeThickness="1" Stroke="LightGray" Stretch="Fill" VerticalAlignment="Bottom" />
    </Grid>
</DataTemplate>
```

`DataTemplate` spécifie les contrôles permettant d’afficher le contenu de la cellule ainsi que leurs disposition et apparence. Deux contrôles `TextBlock` et un contrôle `Image` sont utilisés pour afficher le contenu de la cellule par le biais de la liaison de données. En outre, une instance de `ConcatImageExtensionConverter` est utilisée pour concaténer l’extension de fichier `.jpg` à chaque nom de fichier image. Ainsi, le contrôle `Image` peut charger et afficher l’image quand sa propriété `Source` est définie.

## <a name="summary"></a>Résumé

Cet article a montré comment créer un convertisseur personnalisé pour un [`ViewCell`](xref:Xamarin.Forms.ViewCell) qui est hébergé à l’intérieur d’un Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) contrôle. Cela empêche les Xamarin.Forms calculs de disposition d’être appelés de façon répétée pendant le `ListView` défilement.

## <a name="related-links"></a>Liens connexes

- [Performances de ListView](~/xamarin-forms/user-interface/listview/performance.md)
- [CustomRendererViewCell (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)
