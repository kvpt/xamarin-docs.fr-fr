---
title: GridView
ms.prod: xamarin
ms.assetid: 6992C4FF-ECBB-3493-AEE6-3E063C1A8C54
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: f71c275dd2beee6aedf41ecd19c8a4a39ab5a36f
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522644"
---
# <a name="xamarinandroid-gridview"></a>Xamarin. Android, GridView

[`GridView`](xref:Android.Widget.GridView)est un[`ViewGroup`](xref:Android.Views.ViewGroup)
qui affiche des éléments dans une grille à défilement double. Les éléments de la grille sont automatiquement insérés dans la [`ListAdapter`](xref:Android.App.ListActivity.ListAdapter)disposition à l’aide d’un.

Dans ce didacticiel, vous allez créer une grille de miniatures d’images. Lorsqu’un élément est sélectionné, un message Toast affiche la position de l’image.

Démarrez un nouveau projet nommé **HelloGridView**.

Recherchez des photos que vous souhaitez utiliser ou [Téléchargez ces exemples d’images](https://developer.android.com/shareables/sample_images.zip). Ajoutez les fichiers image au répertoire Resources **/drawn** du projet. Dans la fenêtre **Propriétés** , définissez l’action de génération pour chaque sur **AndroidResource**.

Ouvrez le fichier Resources **/Layout/main. AXML** et insérez ce qui suit:

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gridview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:columnWidth="90dp"
    android:numColumns="auto_fit"
    android:verticalSpacing="10dp"
    android:horizontalSpacing="10dp"
    android:stretchMode="columnWidth"
    android:gravity="center"
/>
```

La totalité de l’écran [s’affiche.`GridView`](xref:Android.Widget.GridView) Les attributs sont plutôt explicites. Pour plus d’informations sur les attributs valides [`GridView`](xref:Android.Widget.GridView) , consultez la référence.

Ouvrez `HelloGridView.cs` et insérez le code suivant pour le[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
méthode

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);

    var gridview = FindViewById<GridView> (Resource.Id.gridview);
    gridview.Adapter = new ImageAdapter (this);

    gridview.ItemClick += delegate (object sender, AdapterView.ItemClickEventArgs args) {
        Toast.MakeText (this, args.Position.ToString (), ToastLength.Short).Show ();
    };
}
```

Une fois que la disposition **main. AXML** est définie pour l’affichage du [`GridView`](xref:Android.Widget.GridView) contenu, le est capturé à [`FindViewById`](xref:Android.App.Activity.FindViewById*)partir de la disposition avec. La[`Adapter`](xref:Android.Widget.AdapterView.RawAdapter)
la propriété est ensuite utilisée pour définir un adaptateur personnalisé`ImageAdapter`() comme source de tous les éléments à afficher dans la grille. Le `ImageAdapter` est créé à l’étape suivante.

Pour effectuer une opération quand l’utilisateur clique sur un élément de la grille, un délégué anonyme est abonné [`ItemClick`](xref:Android.Widget.AdapterView.ItemClick) à l’événement.
Elle montre un [`Toast`](xref:Android.Widget.Toast) qui affiche la position d’index (de base zéro) de l’élément sélectionné (dans un scénario réel, la position peut être utilisée pour obtenir l’image de taille complète d’une autre tâche). Notez que les classes d’écouteur de style Java peuvent être utilisées à la place des événements .NET.

Créez une nouvelle classe appelée `ImageAdapter` sous- [`BaseAdapter`](xref:Android.Widget.BaseAdapter)classes:

```csharp
public class ImageAdapter : BaseAdapter
{
    Context context;

    public ImageAdapter (Context c)
    {
        context = c;
    }

    public override int Count {
        get { return thumbIds.Length; }
    }

    public override Java.Lang.Object GetItem (int position)
    {
        return null;
    }

    public override long GetItemId (int position)
    {
        return 0;
    }

    // create a new ImageView for each item referenced by the Adapter
    public override View GetView (int position, View convertView, ViewGroup parent)
    {
        ImageView imageView;

        if (convertView == null) {  // if it's not recycled, initialize some attributes
            imageView = new ImageView (context);
            imageView.LayoutParameters = new GridView.LayoutParams (85, 85);
            imageView.SetScaleType (ImageView.ScaleType.CenterCrop);
            imageView.SetPadding (8, 8, 8, 8);
        } else {
            imageView = (ImageView)convertView;
        }

        imageView.SetImageResource (thumbIds[position]);
        return imageView;
    }

    // references to our images
    int[] thumbIds = {
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7,
        Resource.Drawable.sample_0, Resource.Drawable.sample_1,
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7,
        Resource.Drawable.sample_0, Resource.Drawable.sample_1,
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7
    };
}
```

Tout d’abord, cette méthode implémente certaines méthodes [`BaseAdapter`](xref:Android.Widget.BaseAdapter)requises héritées de. Le constructeur et la [`Count`](xref:Android.Widget.BaseAdapter.Count) propriété sont explicites. Règle[`GetItem(int)`](xref:Android.Widget.BaseAdapter.GetItem*)
doit retourner l’objet réel à la position spécifiée dans l’adaptateur, mais il est ignoré pour cet exemple. Egale[`GetItemId(int)`](xref:Android.Widget.BaseAdapter.GetItemId*)
doit retourner l’ID de ligne de l’élément, mais il n’est pas nécessaire ici.

La première méthode nécessaire est [`GetView()`](xref:Android.Widget.BaseAdapter.GetView*).
Cette méthode crée un nouveau[`View`](xref:Android.Views.View)
pour chaque image ajoutée à `ImageAdapter`. Quand cette méthode est appelée, un[`View`](xref:Android.Views.View)
est passé, ce qui correspond normalement à un objet recyclé (au moins une fois qu’il a été appelé une fois). il y a donc un contrôle pour déterminer si l’objet est null. Si la valeur *est* null, une[`ImageView`](xref:Android.Widget.ImageView)
est instancié et configuré avec les propriétés souhaitées pour la présentation de l’image:

- [`LayoutParams`](xref:Android.Views.View.LayoutParameters)définit la hauteur et la largeur de la&mdash;vue. ainsi, quelle que soit la taille du dessin, chaque image est redimensionnée et rognée pour tenir dans ces dimensions, le cas échéant.

- [`SetScaleType()`](xref:Android.Widget.ImageView.SetScaleType*)déclare que les images doivent être rognées vers le Centre (si nécessaire).

- [`SetPadding(int, int, int, int)`](xref:Android.Views.View.SetPadding*)définit le remplissage de tous les côtés. (Notez que, si les images ont des ratios d’aspect différents, alors moins de remplissage entraînera un plus grand rognage de l’image si elle ne correspond pas aux dimensions fournies au ImageView.)

Si le [`View`](xref:Android.Views.View) passé à [`GetView()`](xref:Android.Widget.BaseAdapter.GetView*) n’a *pas* la valeur null, le local[`ImageView`](xref:Android.Widget.ImageView)
est initialisé avec l’objet recyclé [`View`](xref:Android.Views.View) .

À la fin de la[`GetView()`](xref:Android.Widget.BaseAdapter.GetView*)
méthode, l' `position` entier passé dans la méthode est utilisé pour sélectionner une image à partir `thumbIds` du tableau, qui est définie comme ressource image pour le [`ImageView`](xref:Android.Widget.ImageView).

Tout cela reste à définir le `thumbIds` tableau de ressources pouvant être dessinées.

Exécutez l'application. La disposition de la grille doit ressembler à ceci:

[![Exemple de capture d’écran de GridView affichant 15 images](grid-view-images/helloviews4.png)](grid-view-images/helloviews4.png#lightbox)

Essayez d’expérimenter les comportements de [`GridView`](xref:Android.Widget.GridView) et[`ImageView`](xref:Android.Widget.ImageView)
éléments en ajustant leurs propriétés. Par exemple, au lieu d' [`LayoutParams`](xref:Android.Views.View.LayoutParameters) utiliser, [`SetAdjustViewBounds()`](xref:Android.Widget.ImageView.SetAdjustViewBounds*)essayez d’utiliser.

## <a name="references"></a>Références

- [`GridView`](xref:Android.Widget.GridView)
- [`ImageView`](xref:Android.Widget.ImageView)
- [`BaseAdapter`](xref:Android.Widget.BaseAdapter)

_Certaines parties de cette page sont des modifications basées sur le travail créé et partagé par le projet open source Android et utilisées conformément aux termes décrits dans la [licence d’attribution de Creative-2,5](http://creativecommons.org/licenses/by/2.5/)._
