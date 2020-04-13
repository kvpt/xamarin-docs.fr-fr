---
title: Personnalisation d’un ListView
description: Un ListView Xamarin.Forms est une vue qui affiche une collection de données sous la forme d’une liste verticale. Cet article montre comment créer un renderer personnalisé qui encapsule des contrôles de liste spécifiques à la plateforme et des dispositions de cellule natives afin de mieux contrôler les performances des contrôles de liste natifs.
ms.prod: xamarin
ms.assetid: 2FBCB8C8-4F32-45E7-954F-63AD29D5F1B5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 384ad20cc1456f3de01ddbe241bf2d8b58de387f
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771928"
---
# <a name="customizing-a-listview"></a>Personnalisation d’un ListView

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-listview)

_Un Xamarin.Forms ListView est une vue qui affiche une collection de données comme une liste verticale. Cet article montre comment créer un rendu personnalisé qui encapsule les commandes de liste spécifiques à la plate-forme et les dispositions cellulaires natives, permettant plus de contrôle sur les performances de contrôle de liste native._

Chaque vue Xamarin.Forms est accompagnée d’un renderer pour chaque plateforme qui crée une instance d’un contrôle natif. Lorsqu’un [`ListView`](xref:Xamarin.Forms.ListView) est rendu par une application Xamarin.Forms, dans iOS la `ListViewRenderer` classe est `UITableView` instantanée, ce qui à son tour instantané un contrôle natif. Sur la plateforme Android, la classe `ListViewRenderer` instancie un contrôle `ListView` natif. Sur la plateforme Windows universelle (UWP), la classe `ListViewRenderer` instancie un contrôle `ListView` natif. Pour plus d’informations sur le renderer et les classes de contrôle natif auxquels les contrôles Xamarin.Forms sont mappés, consultez [Classes de base de renderer et contrôles natifs](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Le diagramme suivant illustre la [`ListView`](xref:Xamarin.Forms.ListView) relation entre le contrôle et les contrôles indigènes correspondants qui l’implémenter :

![](listview-images/listview-classes.png "Relationship Between the ListView Control and the Implementing Native Controls")

Le processus de rendu peut être exploité pour implémenter des personnalisations spécifiques à la plate-forme en créant un rendu personnalisé pour un [`ListView`](xref:Xamarin.Forms.ListView) sur chaque plate-forme. Le processus pour y parvenir est le suivant :

1. [Créez](#Creating_the_Custom_ListView_Control) un contrôle personnalisé Xamarin.Forms.
1. [Consommez](#Consuming_the_Custom_Control) le contrôle personnalisé à partir de Xamarin.Forms.
1. [Créez](#Creating_the_Custom_Renderer_on_each_Platform) le renderer personnalisé pour le contrôle sur chaque plateforme.

Nous allons à présent présenter chaque élément à tour de rôle pour implémenter un renderer `NativeListView` qui tire parti de contrôles de liste spécifiques à la plateforme et de dispositions de cellule natives. Ce scénario est utile dans le cadre du portage d’une application native existante qui contient du code de liste et de cellule pouvant être réutilisé. Il est également possible de personnaliser en détail les fonctionnalités de contrôle de liste qui peuvent affecter les performances, notamment la virtualisation des données.

<a name="Creating_the_Custom_ListView_Control" />

## <a name="creating-the-custom-listview-control"></a>Création du contrôle ListView personnalisé

Un [`ListView`](xref:Xamarin.Forms.ListView) contrôle personnalisé peut être créé `ListView` en sous-classant la classe, comme le montre l’exemple de code suivant :

```csharp
public class NativeListView : ListView
{
  public static readonly BindableProperty ItemsProperty =
    BindableProperty.Create ("Items", typeof(IEnumerable<DataSource>), typeof(NativeListView), new List<DataSource> ());

  public IEnumerable<DataSource> Items {
    get { return (IEnumerable<DataSource>)GetValue (ItemsProperty); }
    set { SetValue (ItemsProperty, value); }
  }

  public event EventHandler<SelectedItemChangedEventArgs> ItemSelected;

  public void NotifyItemSelected (object item)
  {
    if (ItemSelected != null) {
      ItemSelected (this, new SelectedItemChangedEventArgs (item));
    }
  }
}
```

`NativeListView` est créée dans le projet de bibliothèque .NET Standard et définit l’API pour le contrôle personnalisé. Ce contrôle expose une propriété `Items` qui est utilisée pour remplir `ListView` de données et qui peut faire l’objet d’une liaison de données à des fins d’affichage. Il expose également un événement `ItemSelected` qui est déclenché chaque fois qu’un élément est sélectionné dans un contrôle de liste natif spécifique à la plateforme. Pour plus d’informations sur la liaison de données, consultez [Notions de base de la liaison de données](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Consommation du contrôle personnalisé

Vous pouvez référencer le contrôle personnalisé `NativeListView` en XAML dans le projet de bibliothèque .NET Standard en déclarant un espace de noms pour son emplacement et en utilisant le préfixe d’espace de noms sur le contrôle. L’exemple de code suivant montre comment le contrôle personnalisé `NativeListView` peut être consommé par une page XAML :

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <ContentPage.Content>
          <Grid>
            <Grid.RowDefinitions>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="*" />
            </Grid.RowDefinitions>
          <Label Text="{x:Static local:App.Description}" HorizontalTextAlignment="Center" />
            <local:NativeListView Grid.Row="1" x:Name="nativeListView" ItemSelected="OnItemSelected" VerticalOptions="FillAndExpand" />
          </Grid>
      </ContentPage.Content>
</ContentPage>
```

Le préfixe d’espace de noms `local` peut porter n’importe quel nom. Toutefois, les valeurs `clr-namespace` et `assembly` doivent correspondre aux détails du contrôle personnalisé. Une fois l’espace de noms déclaré, le préfixe est utilisé pour référencer le contrôle personnalisé.

L’exemple de code suivant montre comment le contrôle personnalisé `NativeListView` peut être consommé par une page C# :

```csharp
public class MainPageCS : ContentPage
{
    NativeListView nativeListView;

    public MainPageCS()
    {
        nativeListView = new NativeListView
        {
            Items = DataSource.GetList(),
            VerticalOptions = LayoutOptions.FillAndExpand
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

        Content = new Grid
        {
            RowDefinitions = {
                new RowDefinition { Height = GridLength.Auto },
                new RowDefinition { Height = new GridLength (1, GridUnitType.Star) }
            },
            Children = {
                new Label { Text = App.Description, HorizontalTextAlignment = TextAlignment.Center },
                nativeListView
            }
        };
        nativeListView.ItemSelected += OnItemSelected;
    }
    ...
}
```

Le contrôle personnalisé `NativeListView` utilise des renderers personnalisés spécifiques à la plateforme pour afficher une liste de données, laquelle est remplie par le biais de la propriété `Items`. Chaque ligne dans la liste contient trois éléments de données : un nom, une catégorie et un nom de fichier image. La disposition de chaque ligne dans la liste est définie par le renderer personnalisé spécifique à la plateforme.

> [!NOTE]
> Étant `NativeListView` donné que le contrôle personnalisé sera rendu à l’aide de contrôles de liste spécifiques à la [`ScrollView`](xref:Xamarin.Forms.ScrollView)plate-forme qui incluent la capacité de défilement, le contrôle personnalisé ne doit pas être hébergé dans des contrôles de mise en page défilementables tels que le .

Un renderer personnalisé peut maintenant être ajouté à chaque projet d’application pour créer des contrôles de liste spécifiques à la plateforme et des dispositions de cellule natives.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Création du renderer personnalisé sur chaque plateforme

Le processus de création de la classe de renderer personnalisé est le suivant :

1. Créez une sous-classe de la classe `ListViewRenderer` qui restitue le contrôle personnalisé.
1. Remplacez la méthode `OnElementChanged` qui restitue le contrôle personnalisé et écrivez une logique pour le personnaliser. Cette méthode est appelée lorsque le [`ListView`](xref:Xamarin.Forms.ListView) Xamarin.Forms correspondant est créé.
1. Ajoutez un attribut `ExportRenderer` à la classe de renderer personnalisé afin de spécifier qu’il sera utilisé pour restituer le contrôle personnalisé Xamarin.Forms. Cet attribut est utilisé pour inscrire le renderer personnalisé auprès de Xamarin.Forms.

> [!NOTE]
> Il est facultatif de fournir un renderer personnalisé dans chaque projet de plateforme. Si un renderer personnalisé n’est pas inscrit, le renderer par défaut de la classe de base de la cellule est utilisé.

Le diagramme suivant illustre les responsabilités de chaque projet dans l’exemple d’application ainsi que les relations qu’ils entretiennent les uns avec les autres :

![](listview-images/solution-structure.png "NativeListView Custom Renderer Project Responsibilities")

Le contrôle personnalisé `NativeListView` est restitué par des classes de renderer spécifiques à la plateforme qui dérivent toutes de la classe `ListViewRenderer` pour chaque plateforme. Chaque contrôle personnalisé `NativeListView` est donc restitué avec des contrôles de liste spécifiques à la plateforme et des dispositions de cellule natives, comme le montrent les captures d’écran suivantes :

![](listview-images/screenshots.png "NativeListView on each Platform")

La classe `ListViewRenderer` expose la méthode `OnElementChanged`, qui est appelée quand le contrôle personnalisé Xamarin.Forms est créé pour restituer le contrôle natif correspondant. Cette méthode prend un paramètre `ElementChangedEventArgs` qui contient les propriétés `OldElement` et `NewElement`. Ces propriétés représentent respectivement l’élément Xamarin.Forms auquel le renderer *était* attaché et l’élément Xamarin.Forms auquel le renderer *est* attaché. Dans l’exemple d’application, la propriété `OldElement` sera `null` et la propriété `NewElement` contiendra une référence à l’instance `NativeListView`.

Une version substituée de la méthode `OnElementChanged`, dans chaque classe de renderer spécifique à la plateforme, est l’emplacement où effectuer la personnalisation du contrôle natif. Une référence typée au contrôle natif en cours d’utilisation sur la plateforme est accessible par le biais de la propriété `Control`. De plus, une référence au contrôle Xamarin.Forms en cours de restitution peut être obtenue par le biais de la propriété `Element`.

Faites preuve de vigilance quand vous vous abonnez à des gestionnaires d’événements dans la méthode `OnElementChanged`, comme le montre l’exemple de code suivant :

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.ListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    // Configure the native control and subscribe to event handlers
  }
}
```

Vous devez uniquement configurer le contrôle natif et vous abonner aux gestionnaires d’événements quand le renderer personnalisé est attaché à un nouvel élément Xamarin.Forms. De même, vous devez vous désabonner des gestionnaires d’événements auxquels vous vous êtes abonné uniquement quand l’élément auquel le renderer est attaché change. L’adoption de cette approche permet de créer un renderer personnalisé qui n’est pas affecté par des fuites de mémoire.

Une version substituée de la méthode `OnElementPropertyChanged`, dans la classe de renderer spécifique à la plateforme, est l’emplacement où répondre aux changements de la propriété pouvant être liée sur le contrôle personnalisé Xamarin.Forms. Une vérification de la propriété changée doit toujours être effectuée, car cette substitution peut être appelée plusieurs fois.

Chaque classe de renderer personnalisé est décorée avec un attribut `ExportRenderer` qui inscrit le renderer auprès de Xamarin.Forms. L’attribut accepte deux paramètres : le nom de type du contrôle personnalisé Xamarin.Forms en cours de restitution et le nom de type du renderer personnalisé. Le préfixe `assembly` de l’attribut spécifie que l’attribut s’applique à la totalité de l’assembly.

Les sections suivantes décrivent l’implémentation de chaque classe de renderer personnalisé spécifique à la plateforme.

### <a name="creating-the-custom-renderer-on-ios"></a>Création du renderer personnalisé sur iOS

L’exemple de code suivant illustre le renderer personnalisé pour la plateforme iOS :

```csharp
[assembly: ExportRenderer (typeof(NativeListView), typeof(NativeiOSListViewRenderer))]
namespace CustomRenderer.iOS
{
    public class NativeiOSListViewRenderer : ListViewRenderer
    {
        protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.ListView> e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null) {
                // Unsubscribe
            }

            if (e.NewElement != null) {
                Control.Source = new NativeiOSListViewSource (e.NewElement as NativeListView);
            }
        }
    }
}
```

Le contrôle `UITableView` est créé en créant une instance de la classe `NativeiOSListViewSource`, sous réserve que le renderer personnalisé soit attaché à un nouvel élément Xamarin.Forms. Cette classe fournit des données au contrôle `UITableView` en substituant les méthodes `RowsInSection` et `GetCell` de la classe `UITableViewSource` et en exposant une propriété `Items` qui contient la liste des données à afficher. La classe fournit également une substitution de méthode `RowSelected` qui appelle l’événement `ItemSelected` fourni par le contrôle personnalisé `NativeListView`. Pour plus d’informations sur les substitutions de méthode, consultez [Utilisation d’une sous-classe de UITableViewSource](~/ios/user-interface/controls/tables/populating-a-table-with-data.md). La méthode `GetCell` retourne un `UITableCellView` dont chaque ligne dans la liste est remplie de données, comme illustré dans l’exemple de code suivant :

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
  // request a recycled cell to save memory
  NativeiOSListViewCell cell = tableView.DequeueReusableCell (cellIdentifier) as NativeiOSListViewCell;

  // if there are no cells to reuse, create a new one
  if (cell == null) {
    cell = new NativeiOSListViewCell (cellIdentifier);
  }

  if (String.IsNullOrWhiteSpace (tableItems [indexPath.Row].ImageFilename)) {
    cell.UpdateCell (tableItems [indexPath.Row].Name
      , tableItems [indexPath.Row].Category
      , null);
  } else {
    cell.UpdateCell (tableItems [indexPath.Row].Name
      , tableItems [indexPath.Row].Category
      , UIImage.FromFile ("Images/" + tableItems [indexPath.Row].ImageFilename + ".jpg"));
  }

  return cell;
}
```

Cette méthode crée une instance `NativeiOSListViewCell` pour chaque ligne de données affichée à l’écran. L’instance `NativeiOSCell` définit la disposition de chaque cellule et les données de la cellule. Quand une cellule disparaît de l’écran pour cause de défilement, elle est disponible pour réutilisation. Le fait d’avoir uniquement des instances `NativeiOSCell` pour les données affichées à l’écran, et non pour toutes les données dans la liste, évite le gaspillage de mémoire. Pour plus d’informations sur la réutilisation de cellules, consultez [Réutilisation de cellules](~/ios/user-interface/controls/tables/populating-a-table-with-data.md). La méthode `GetCell` lit également la propriété `ImageFilename` de chaque ligne de données, à condition qu’elle existe, puis lit l’image et la stocke en tant qu’instance `UIImage` avant de mettre à jour l’instance `NativeiOSListViewCell` avec les données (nom, catégorie et image) pour la ligne.

La classe `NativeiOSListViewCell` définit la disposition pour chaque cellule et est illustrée dans l’exemple de code suivant :

```csharp
public class NativeiOSListViewCell : UITableViewCell
{
  UILabel headingLabel, subheadingLabel;
  UIImageView imageView;

  public NativeiOSListViewCell (NSString cellId) : base (UITableViewCellStyle.Default, cellId)
  {
    SelectionStyle = UITableViewCellSelectionStyle.Gray;

    ContentView.BackgroundColor = UIColor.FromRGB (218, 255, 127);

    imageView = new UIImageView ();

    headingLabel = new UILabel () {
      Font = UIFont.FromName ("Cochin-BoldItalic", 22f),
      TextColor = UIColor.FromRGB (127, 51, 0),
      BackgroundColor = UIColor.Clear
    };

    subheadingLabel = new UILabel () {
      Font = UIFont.FromName ("AmericanTypewriter", 12f),
      TextColor = UIColor.FromRGB (38, 127, 0),
      TextAlignment = UITextAlignment.Center,
      BackgroundColor = UIColor.Clear
    };

    ContentView.Add (headingLabel);
    ContentView.Add (subheadingLabel);
    ContentView.Add (imageView);
  }

  public void UpdateCell (string caption, string subtitle, UIImage image)
  {
    headingLabel.Text = caption;
    subheadingLabel.Text = subtitle;
    imageView.Image = image;
  }

  public override void LayoutSubviews ()
  {
    base.LayoutSubviews ();

    headingLabel.Frame = new CoreGraphics.CGRect (5, 4, ContentView.Bounds.Width - 63, 25);
    subheadingLabel.Frame = new CoreGraphics.CGRect (100, 18, 100, 20);
    imageView.Frame = new CoreGraphics.CGRect (ContentView.Bounds.Width - 63, 5, 33, 33);
  }
}
```

Cette classe définit les contrôles permettant de restituer le contenu de la cellule et sa disposition. Le constructeur `NativeiOSListViewCell` crée des instances des contrôles `UILabel` et `UIImageView`, et initialise leur apparence. Ces contrôles sont utilisés pour afficher les données de chaque ligne, la méthode `UpdateCell` étant utilisée pour définir ces données sur les instances `UILabel` et `UIImageView`. L’emplacement de ces instances est défini par la méthode substituée `LayoutSubviews` en spécifiant leurs coordonnées au sein de la cellule.

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Réponse à un changement de propriété sur le contrôle personnalisé

Si la propriété `NativeListView.Items` change en raison de l’ajout d’éléments à la liste ou de leur suppression, le renderer personnalisé doit répondre en affichant les changements. Cet objectif peut être atteint par la substitution de la méthode `OnElementPropertyChanged`, ce qu’illustre l’exemple de code suivant :

```csharp
protected override void OnElementPropertyChanged (object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
  base.OnElementPropertyChanged (sender, e);

  if (e.PropertyName == NativeListView.ItemsProperty.PropertyName) {
    Control.Source = new NativeiOSListViewSource (Element as NativeListView);
  }
}
```

La méthode crée une instance de la classe `NativeiOSListViewSource` qui fournit des données au contrôle `UITableView`, à condition que la propriété pouvant être liée `NativeListView.Items` ait changé.

### <a name="creating-the-custom-renderer-on-android"></a>Création du renderer personnalisé sur Android

L’exemple de code suivant illustre le renderer personnalisé pour la plateforme Android :

```csharp
[assembly: ExportRenderer(typeof(NativeListView), typeof(NativeAndroidListViewRenderer))]
namespace CustomRenderer.Droid
{
    public class NativeAndroidListViewRenderer : ListViewRenderer
    {
        Context _context;

        public NativeAndroidListViewRenderer(Context context) : base(context)
        {
            _context = context;
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.ListView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // unsubscribe
                Control.ItemClick -= OnItemClick;
            }

            if (e.NewElement != null)
            {
                // subscribe
                Control.Adapter = new NativeAndroidListViewAdapter(_context as Android.App.Activity, e.NewElement as NativeListView);
                Control.ItemClick += OnItemClick;
            }
        }
        ...

        void OnItemClick(object sender, Android.Widget.AdapterView.ItemClickEventArgs e)
        {
            ((NativeListView)Element).NotifyItemSelected(((NativeListView)Element).Items.ToList()[e.Position - 1]);
        }
    }
}
```

Le contrôle natif `ListView` est configuré sous réserve que le renderer personnalisé soit attaché à un nouvel élément Xamarin.Forms. Cette configuration implique la création d’une instance de la classe `NativeAndroidListViewAdapter` qui fournit des données au contrôle natif `ListView` et l’inscription d’un gestionnaire d’événements pour traiter l’événement `ItemClick`. Ce gestionnaire appelle à son tour l’événement `ItemSelected` fourni par le contrôle personnalisé `NativeListView`. L’utilisateur se désabonne de l’événement `ItemClick` si l’élément Xamarin.Forms auquel le renderer est attaché change.

Le `NativeAndroidListViewAdapter` dérive de la classe `BaseAdapter`. Il expose une propriété `Items` qui contient la liste des données à afficher et substitue les méthodes `Count`, `GetView`, `GetItemId` et `this[int]`. Pour plus d’informations sur ces substitutions de méthode, consultez [Implémentation de ListAdapter](~/android/user-interface/layouts/list-view/populating.md). La méthode `GetView` retourne une vue pour chaque ligne, remplie de données, comme illustré dans l’exemple de code suivant :

```csharp
public override View GetView (int position, View convertView, ViewGroup parent)
{
  var item = tableItems [position];

  var view = convertView;
  if (view == null) {
    // no view to re-use, create new
    view = context.LayoutInflater.Inflate (Resource.Layout.NativeAndroidListViewCell, null);
  }
  view.FindViewById<TextView> (Resource.Id.Text1).Text = item.Name;
  view.FindViewById<TextView> (Resource.Id.Text2).Text = item.Category;

  // grab the old image and dispose of it
  if (view.FindViewById<ImageView> (Resource.Id.Image).Drawable != null) {
    using (var image = view.FindViewById<ImageView> (Resource.Id.Image).Drawable as BitmapDrawable) {
      if (image != null) {
        if (image.Bitmap != null) {
          //image.Bitmap.Recycle ();
          image.Bitmap.Dispose ();
        }
      }
    }
  }

  // If a new image is required, display it
  if (!String.IsNullOrWhiteSpace (item.ImageFilename)) {
    context.Resources.GetBitmapAsync (item.ImageFilename).ContinueWith ((t) => {
      var bitmap = t.Result;
      if (bitmap != null) {
        view.FindViewById<ImageView> (Resource.Id.Image).SetImageBitmap (bitmap);
        bitmap.Dispose ();
      }
    }, TaskScheduler.FromCurrentSynchronizationContext ());
  } else {
    // clear the image
    view.FindViewById<ImageView> (Resource.Id.Image).SetImageBitmap (null);
  }

  return view;
}
```

La méthode `GetView` est appelée pour retourner la cellule à restituer, en tant que `View`, pour chaque ligne de données dans la liste. Une instance `View` est créée pour chaque ligne de données affichée à l’écran, l’apparence de l’instance `View` étant définie dans un fichier de disposition. Quand une cellule disparaît de l’écran pour cause de défilement, elle est disponible pour réutilisation. Le fait d’avoir uniquement des instances `View` pour les données affichées à l’écran, et non pour toutes les données dans la liste, évite le gaspillage de mémoire. Pour plus d’informations sur la réutilisation de vues, consultez [Réutilisation de vues de ligne](~/android/user-interface/layouts/list-view/populating.md).

La méthode `GetView` remplit également l’instance `View`. Elle lit notamment les données d’image à partir du fichier dont le nom est spécifié dans la propriété `ImageFilename`.

La disposition de chaque cellule affichée par le `ListView` natif est définie dans le fichier de disposition `NativeAndroidListViewCell.axml`, lequel est augmenté par la méthode `LayoutInflater.Inflate`. L’exemple de code suivant illustre la définition de disposition :

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
            android:id="@+id/Text1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="#FF7F3300"
            android:textSize="20dip"
            android:textStyle="italic" />
        <TextView
            android:id="@+id/Text2"
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

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Réponse à un changement de propriété sur le contrôle personnalisé

Si la propriété `NativeListView.Items` change en raison de l’ajout d’éléments à la liste ou de leur suppression, le renderer personnalisé doit répondre en affichant les changements. Cet objectif peut être atteint par la substitution de la méthode `OnElementPropertyChanged`, ce qu’illustre l’exemple de code suivant :

```csharp
protected override void OnElementPropertyChanged (object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
  base.OnElementPropertyChanged (sender, e);

  if (e.PropertyName == NativeListView.ItemsProperty.PropertyName) {
    Control.Adapter = new NativeAndroidListViewAdapter (_context as Android.App.Activity, Element as NativeListView);
  }
}
```

La méthode crée une instance de la classe `NativeAndroidListViewAdapter` qui fournit des données au contrôle natif `ListView`, à condition que la propriété pouvant être liée `NativeListView.Items` ait changé.

### <a name="creating-the-custom-renderer-on-uwp"></a>Création du renderer personnalisé sur UWP

L’exemple de code suivant illustre le renderer personnalisé pour UWP :

```csharp
[assembly: ExportRenderer(typeof(NativeListView), typeof(NativeUWPListViewRenderer))]
namespace CustomRenderer.UWP
{
    public class NativeUWPListViewRenderer : ListViewRenderer
    {
        ListView listView;

        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.ListView> e)
        {
            base.OnElementChanged(e);

            listView = Control as ListView;

            if (e.OldElement != null)
            {
                // Unsubscribe
                listView.SelectionChanged -= OnSelectedItemChanged;
            }

            if (e.NewElement != null)
            {
                listView.SelectionMode = ListViewSelectionMode.Single;
                listView.IsItemClickEnabled = false;
                listView.ItemsSource = ((NativeListView)e.NewElement).Items;             
                listView.ItemTemplate = App.Current.Resources["ListViewItemTemplate"] as Windows.UI.Xaml.DataTemplate;
                // Subscribe
                listView.SelectionChanged += OnSelectedItemChanged;
            }  
        }

        void OnSelectedItemChanged(object sender, SelectionChangedEventArgs e)
        {
            ((NativeListView)Element).NotifyItemSelected(listView.SelectedItem);
        }
    }
}
```

Le contrôle natif `ListView` est configuré sous réserve que le renderer personnalisé soit attaché à un nouvel élément Xamarin.Forms. Cette configuration nécessite la définition du mode de réponse du contrôle natif `ListView` aux éléments sélectionnés, le remplissage des données affichées par le contrôle, la définition de l’apparence et du contenu de chaque cellule, et l’inscription d’un gestionnaire d’événements pour traiter l’événement `SelectionChanged`. Ce gestionnaire appelle à son tour l’événement `ItemSelected` fourni par le contrôle personnalisé `NativeListView`. L’utilisateur se désabonne de l’événement `SelectionChanged` si l’élément Xamarin.Forms auquel le renderer est attaché change.

L’apparence et le contenu de chaque cellule native `ListView` sont définis par un `DataTemplate` nommé `ListViewItemTemplate`. `DataTemplate` est stocké dans le dictionnaire de ressources de niveau application et est illustré dans l’exemple de code suivant :

```xaml
<DataTemplate x:Key="ListViewItemTemplate">
    <Grid Background="#DAFF7F">
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

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Réponse à un changement de propriété sur le contrôle personnalisé

Si la propriété `NativeListView.Items` change en raison de l’ajout d’éléments à la liste ou de leur suppression, le renderer personnalisé doit répondre en affichant les changements. Cet objectif peut être atteint par la substitution de la méthode `OnElementPropertyChanged`, ce qu’illustre l’exemple de code suivant :

```csharp
protected override void OnElementPropertyChanged(object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
    base.OnElementPropertyChanged(sender, e);

    if (e.PropertyName == NativeListView.ItemsProperty.PropertyName)
    {
        listView.ItemsSource = ((NativeListView)Element).Items;
    }
}
```

La méthode remplit à nouveau le contrôle natif `ListView` des données ayant changé, à condition que la propriété pouvant être liée `NativeListView.Items` ait changé.

## <a name="summary"></a>Récapitulatif

Dans cet article, nous avons vu comment créer un renderer personnalisé qui encapsule des contrôles de liste spécifiques à la plateforme et des dispositions de cellule natives afin de mieux contrôler les performances des contrôles de liste natifs.

## <a name="related-links"></a>Liens connexes

- [CustomRendererListView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-listview)
