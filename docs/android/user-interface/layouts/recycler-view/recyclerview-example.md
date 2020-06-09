---
title: Exemple de RecyclerView de base
description: Exemple d’application qui montre comment utiliser RecyclerView.
ms.prod: xamarin
ms.assetid: A50520D2-1214-40E1-9B27-B0891FE11584
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/30/2018
ms.openlocfilehash: 6093c983a80c53b4900bb26c3a7020724a59c06d
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571290"
---
# <a name="a-basic-recyclerview-example"></a>Exemple de RecyclerView de base

Pour comprendre comment `RecyclerView` fonctionne dans une application classique, cette rubrique explore l’exemple d’application [RecyclerViewer](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer) , un exemple de code simple qui utilise `RecyclerView` pour afficher une grande collection de photos : 

[![Deux captures d’écran d’une application RecyclerView qui utilise CardViews pour afficher des photos](recyclerview-example-images/01-recyclerviewer-sml.png)](recyclerview-example-images/01-recyclerviewer.png#lightbox)

**RecyclerViewer** utilise [CardView](~/android/user-interface/controls/card-view.md) pour implémenter chaque élément photo dans la `RecyclerView` disposition. En raison des `RecyclerView` avantages en termes de performances de, cet exemple d’application est en mesure de faire défiler rapidement une grande collection de photos et sans retards perceptibles.

### <a name="an-example-data-source"></a>Exemple de source de données

Dans cet exemple d’application, une source de données « album photo » (représentée par la `PhotoAlbum` classe) fournit le `RecyclerView` contenu de l’élément.
`PhotoAlbum`est une collection de photos avec des légendes ; Lorsque vous l’instanciez, vous bénéficiez d’une collection de photos 32 prête à l’emploi :

```csharp
PhotoAlbum mPhotoAlbum = new PhotoAlbum ();
```

Chaque instance de photo dans `PhotoAlbum` expose des propriétés qui vous permettent de lire son ID de ressource d’image, `PhotoID` , et sa chaîne de légende, `Caption` . La collection de photos est organisée de telle sorte que chaque photo est accessible par un indexeur. Par exemple, les lignes de code suivantes accèdent à l’ID de ressource d’image et à la légende de la dixième photo dans la collection :

```csharp
int imageId = mPhotoAlbum[9].ImageId;
string caption = mPhotoAlbum[9].Caption;
```

`PhotoAlbum`fournit également une `RandomSwap` méthode que vous pouvez appeler pour permuter la première photo de la collection avec une photo choisie de manière aléatoire ailleurs dans la collection :

```csharp
mPhotoAlbum.RandomSwap ();
```

Étant donné que les détails d’implémentation de ne `PhotoAlbum` sont pas pertinents pour `RecyclerView` la compréhension, le `PhotoAlbum` code source n’est pas présenté ici. Le code source `PhotoAlbum` est disponible sur [PhotoAlbum.cs](https://github.com/xamarin/monodroid-samples/blob/master/android5.0/RecyclerViewer/RecyclerViewer/PhotoAlbum.cs) dans l’exemple d’application [RecyclerViewer](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer) .

### <a name="layout-and-initialization"></a>Disposition et initialisation

Le fichier de disposition, **main. AXML**, se compose d’un unique `RecyclerView` dans un `LinearLayout` :

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:scrollbars="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```

Notez que vous devez utiliser le nom complet **Android. support. v7. widget. RecyclerView** , car `RecyclerView` est empaqueté dans une bibliothèque de prise en charge. La `OnCreate` méthode d' `MainActivity` Initialise cette disposition, instancie l’adaptateur et prépare la source de données sous-jacente :

```csharp
public class MainActivity : Activity
{
    RecyclerView mRecyclerView;
    RecyclerView.LayoutManager mLayoutManager;
    PhotoAlbumAdapter mAdapter;
    PhotoAlbum mPhotoAlbum;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Prepare the data source:
        mPhotoAlbum = new PhotoAlbum ();

        // Instantiate the adapter and pass in its data source:
        mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);

        // Set our view from the "main" layout resource:
        SetContentView (Resource.Layout.Main);

        // Get our RecyclerView layout:
        mRecyclerView = FindViewById<RecyclerView> (Resource.Id.recyclerView);

        // Plug the adapter into the RecyclerView:
        mRecyclerView.SetAdapter (mAdapter);
```

Ce code effectue les actions suivantes :

1. Instancie la `PhotoAlbum` source de données.

2. Transmet la source de données de l’album photo au constructeur de l’adaptateur, `PhotoAlbumAdapter` (qui est défini plus loin dans ce guide). 
   Notez qu’il est considéré comme une bonne pratique de passer la source de données en tant que paramètre au constructeur de l’adaptateur. 

3. Obtient le `RecyclerView` à partir de la disposition.

4. Connecte l’adaptateur à l' `RecyclerView` instance en appelant la `RecyclerView` `SetAdapter` méthode comme indiqué ci-dessus.

### <a name="layout-manager"></a>Gestionnaire de disposition

Chaque élément de la `RecyclerView` est constitué d’un `CardView` qui contient une image de photo et une légende de photo (les détails sont couverts dans la section [afficher le détenteur](#view-holder) ci-dessous). Le prédéfini `LinearLayoutManager` est utilisé pour disposer chaque `CardView` dans une disposition de défilement vertical :

```csharp
mLayoutManager = new LinearLayoutManager (this);
mRecyclerView.SetLayoutManager (mLayoutManager);
```

Ce code réside dans la méthode de l’activité principale `OnCreate` . Le constructeur du gestionnaire de présentation requiert un *contexte*, donc le `MainActivity` est passé à l’aide `this` de, comme indiqué ci-dessus.

Au lieu d’utiliser la prédéfinie `LinearLayoutManager` , vous pouvez intégrer un gestionnaire de disposition personnalisé qui affiche deux `CardView` éléments côte à côte, en implémentant un effet d’animation de rotation de page pour parcourir la collection de photos. Plus loin dans ce guide, vous verrez un exemple de modification de la disposition en échangeant dans un autre gestionnaire de disposition.

<a name="view-holder"></a>

### <a name="view-holder"></a>Afficher le détenteur

La classe du détenteur de la vue est appelée `PhotoViewHolder` . Chaque `PhotoViewHolder` instance contient des références à `ImageView` l' `TextView` objet et d’un élément de ligne associé, qui est présenté dans un `CardView` comme diagramme ici :

[![Diagramme de CardView contenant un ImageView et un TextView](recyclerview-example-images/02-cardview-layout-sml.png)](recyclerview-example-images/02-cardview-layout.png#lightbox)

`PhotoViewHolder`dérive de `RecyclerView.ViewHolder` et contient des propriétés pour stocker les références à `ImageView` et `TextView` affichées dans la disposition ci-dessus.
`PhotoViewHolder`se compose de deux propriétés et d’un constructeur :

```csharp
public class PhotoViewHolder : RecyclerView.ViewHolder
{
    public ImageView Image { get; private set; }
    public TextView Caption { get; private set; }

    public PhotoViewHolder (View itemView) : base (itemView)
    {
        // Locate and cache view references:
        Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
        Caption = itemView.FindViewById<TextView> (Resource.Id.textView);
    }
}
```

Dans cet exemple de code, `PhotoViewHolder` une référence à la vue de l’élément parent (le) qui est encapsulé est passée au constructeur `CardView` `PhotoViewHolder` . Notez que vous transférez toujours la vue de l’élément parent au constructeur de base. Le `PhotoViewHolder` constructeur appelle `FindViewById` sur la vue de l’élément parent pour localiser chacune de ses références de vue enfants, `ImageView` puis `TextView` , en stockant les résultats dans les `Image` `Caption` Propriétés et, respectivement. L’adaptateur récupère ensuite les références de vue à partir de ces propriétés lorsqu’il met à jour les vues enfants de ces dernières `CardView` avec de nouvelles données.

Pour plus d’informations sur `RecyclerView.ViewHolder` , consultez la référence de la [classe RecyclerView. ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).

### <a name="adapter"></a>Adaptateur

L’adaptateur charge chaque `RecyclerView` ligne avec des données pour une photographie particulière. Pour une photographie donnée à la position de ligne *p*, par exemple, l’adaptateur localise les données associées à la position *p* dans la source de données et copie ces données vers l’élément de ligne à la position *p* dans la `RecyclerView` collection. L’adaptateur utilise le détenteur de la vue pour rechercher les références de `ImageView` et `TextView` à cette position afin qu’il n’ait pas à appeler à plusieurs reprises `FindViewById` pour ces vues lorsque l’utilisateur fait défiler la collection de photos et réutilise les vues.

Dans **RecyclerViewer**, une classe d’adaptateur est dérivée de `RecyclerView.Adapter` pour créer `PhotoAlbumAdapter` :

```csharp
public class PhotoAlbumAdapter : RecyclerView.Adapter
{
    public PhotoAlbum mPhotoAlbum;

    public PhotoAlbumAdapter (PhotoAlbum photoAlbum)
    {
        mPhotoAlbum = photoAlbum;
    }
    ...
}
```

Le `mPhotoAlbum` membre contient la source de données (l’album photo) qui est passée dans le constructeur ; le constructeur copie l’album photo dans cette variable membre. Les méthodes requises suivantes `RecyclerView.Adapter` sont implémentées :

- **`OnCreateViewHolder`**&ndash;Instancie le fichier de disposition de l’élément et le détenteur de l’affichage.

- **`OnBindViewHolder`**&ndash;Charge les données à la position spécifiée dans les vues dont les références sont stockées dans le détenteur de vue donné.

- **`ItemCount`**&ndash;Retourne le nombre d’éléments dans la source de données.

Le gestionnaire de disposition appelle ces méthodes pendant qu’il positionne des éléments dans le `RecyclerView` . L’implémentation de ces méthodes est examinée dans les sections suivantes.

#### <a name="oncreateviewholder"></a>OnCreateViewHolder

Le gestionnaire de disposition appelle `OnCreateViewHolder` lorsque le `RecyclerView` a besoin d’un nouveau conteneur de vue pour représenter un élément. `OnCreateViewHolder`augmente la vue de l’élément du fichier de disposition de la vue et encapsule la vue dans une nouvelle `PhotoViewHolder` instance. Le `PhotoViewHolder` constructeur localise et stocke les références aux vues enfants dans la disposition, comme décrit précédemment dans le [conteneur de vue](#view-holder).

Chaque élément de ligne est représenté par un `CardView` qui contient un `ImageView` (pour la photo) et un `TextView` (pour la légende). Cette disposition réside dans le fichier **PhotoCardView. AXML**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:card_view="http://schemas.android.com/apk/res-auto"
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content">
    <android.support.v7.widget.CardView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        card_view:cardElevation="4dp"
        card_view:cardUseCompatPadding="true"
        card_view:cardCornerRadius="5dp">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="8dp">
            <ImageView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:id="@+id/imageView"
                android:scaleType="centerCrop" />
            <TextView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?android:attr/textAppearanceMedium"
                android:textColor="#333333"
                android:text="Caption"
                android:id="@+id/textView"
                android:layout_gravity="center_horizontal"
                android:layout_marginLeft="4dp" />
        </LinearLayout>
    </android.support.v7.widget.CardView>
</FrameLayout>
```

Cette disposition représente un élément de ligne unique dans `RecyclerView` . La `OnBindViewHolder` méthode (décrite ci-dessous) copie les données de la source de données dans `ImageView` et `TextView` de cette disposition.
`OnCreateViewHolder`déflate cette disposition pour un emplacement de photo donné dans `RecyclerView` et instancie une nouvelle `PhotoViewHolder` instance (qui localise et met en cache les références `ImageView` aux `TextView` vues enfants et dans la `CardView` mise en page associée) :

```csharp
public override RecyclerView.ViewHolder
    OnCreateViewHolder (ViewGroup parent, int viewType)
{
    // Inflate the CardView for the photo:
    View itemView = LayoutInflater.From (parent.Context).
                Inflate (Resource.Layout.PhotoCardView, parent, false);

    // Create a ViewHolder to hold view references inside the CardView:
    PhotoViewHolder vh = new PhotoViewHolder (itemView);
    return vh;
}

```

L’instance de conteneur d’affichage résultante, `vh` , est retournée à l’appelant (le gestionnaire de disposition).

#### <a name="onbindviewholder"></a>OnBindViewHolder

Quand le gestionnaire de disposition est prêt à afficher une vue particulière dans la `RecyclerView` zone visible de l’écran, il appelle la méthode de l’adaptateur `OnBindViewHolder` pour remplir l’élément à la position de ligne spécifiée avec le contenu de la source de données. `OnBindViewHolder`Obtient les informations de photo pour la position de ligne spécifiée (la ressource image de la photo et la chaîne pour la légende de la photo) et copie ces données dans les vues associées. Les vues sont localisées à l’aide de références stockées dans l’objet de conteneur d’affichage (qui est transmis par le biais du `holder` paramètre) :

```csharp
public override void
    OnBindViewHolder (RecyclerView.ViewHolder holder, int position)
{
    PhotoViewHolder vh = holder as PhotoViewHolder;

    // Load the photo image resource from the photo album:
    vh.Image.SetImageResource (mPhotoAlbum[position].PhotoID);

    // Load the photo caption from the photo album:
    vh.Caption.Text = mPhotoAlbum[position].Caption;
}
```

L’objet de conteneur de vue passé doit d’abord être casté en type de conteneur de vue dérivée (dans ce cas, `PhotoViewHolder` ) avant d’être utilisé.
L’adaptateur charge la ressource d’image dans la vue référencée par la propriété du titulaire de la vue `Image` , et copie le texte de la légende dans la vue référencée par la propriété du titulaire de la vue `Caption` . Cela *lie* la vue associée à ses données.

Notez que `OnBindViewHolder` est le code qui traite directement la structure des données. Dans ce cas, `OnBindViewHolder` comprend comment mapper la `RecyclerView` position de l’élément à l’élément de données qui lui est associé dans la source de données. Dans ce cas, le mappage est simple, car la position peut être utilisée comme index de tableau dans l’album photo. Toutefois, des sources de données plus complexes peuvent nécessiter du code supplémentaire pour établir un tel mappage.

#### <a name="itemcount"></a>ItemCount

La `ItemCount` méthode retourne le nombre d’éléments dans la collection de données. Dans l’exemple d’application de visionneuse photo, le nombre d’éléments est le nombre de photos dans l’album photo :

```csharp
public override int ItemCount
{
    get { return mPhotoAlbum.NumPhotos; }
}
```

Pour plus d’informations sur `RecyclerView.Adapter` , consultez la [référence de classe RecyclerView. adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).

### <a name="putting-it-all-together"></a>Ensemble

L' `RecyclerView` implémentation obtenue pour l’exemple d’application photo est constituée de `MainActivity` code qui crée la source de données, le gestionnaire de présentation et l’adaptateur. `MainActivity`crée l' `mRecyclerView` instance, instancie la source de données et l’adaptateur, et se connecte au gestionnaire de disposition et à l’adaptateur :

```csharp
public class MainActivity : Activity
{
    RecyclerView mRecyclerView;
    RecyclerView.LayoutManager mLayoutManager;
    PhotoAlbumAdapter mAdapter;
    PhotoAlbum mPhotoAlbum;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        mPhotoAlbum = new PhotoAlbum();
        SetContentView (Resource.Layout.Main);
        mRecyclerView = FindViewById<RecyclerView> (Resource.Id.recyclerView);

        // Plug in the linear layout manager:
        mLayoutManager = new LinearLayoutManager (this);
        mRecyclerView.SetLayoutManager (mLayoutManager);

        // Plug in my adapter:
        mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
        mRecyclerView.SetAdapter (mAdapter);
    }
}

```

`PhotoViewHolder`localise et met en cache les références de vue :

```csharp
public class PhotoViewHolder : RecyclerView.ViewHolder
{
    public ImageView Image { get; private set; }
    public TextView Caption { get; private set; }

    public PhotoViewHolder (View itemView) : base (itemView)
    {
        // Locate and cache view references:
        Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
        Caption = itemView.FindViewById<TextView> (Resource.Id.textView);
    }
}
```

`PhotoAlbumAdapter`implémente les trois remplacements de méthode requis :

```csharp
public class PhotoAlbumAdapter : RecyclerView.Adapter
{
    public PhotoAlbum mPhotoAlbum;
    public PhotoAlbumAdapter (PhotoAlbum photoAlbum)
    {
        mPhotoAlbum = photoAlbum;
    }

    public override RecyclerView.ViewHolder
        OnCreateViewHolder (ViewGroup parent, int viewType)
    {
        View itemView = LayoutInflater.From (parent.Context).
                    Inflate (Resource.Layout.PhotoCardView, parent, false);
        PhotoViewHolder vh = new PhotoViewHolder (itemView);
        return vh;
    }

    public override void
        OnBindViewHolder (RecyclerView.ViewHolder holder, int position)
    {
        PhotoViewHolder vh = holder as PhotoViewHolder;
        vh.Image.SetImageResource (mPhotoAlbum[position].PhotoID);
        vh.Caption.Text = mPhotoAlbum[position].Caption;
    }

    public override int ItemCount
    {
        get { return mPhotoAlbum.NumPhotos; }
    }
}
```

Lorsque ce code est compilé et exécuté, il crée l’application de visionnage de photos de base, comme illustré dans les captures d’écran suivantes :

[![Deux captures d’écran de l’application d’affichage photo avec défilement vertical des cartes photo](recyclerview-example-images/03-recyclerviewer-basic-sml.png)](recyclerview-example-images/03-recyclerviewer-basic.png#lightbox)

Si les ombres ne sont pas dessinées (comme indiqué dans la capture d’écran ci-dessus), modifiez **Properties/fichier AndroidManifest. xml** et ajoutez le paramètre d’attribut suivant à l' `<application>` élément :

```xml
android:hardwareAccelerated="true"
```

Cette application de base ne prend en charge que l’exploration de l’album photo. Elle ne répond pas aux événements d’élément tactile et ne gère pas les modifications apportées aux données sous-jacentes. Cette fonctionnalité est ajoutée à [l’extension de l’exemple RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md).

### <a name="changing-the-layoutmanager"></a>Modification du LayoutManager

En raison de la `RecyclerView` flexibilité de, il est facile de modifier l’application pour utiliser un autre gestionnaire de disposition. Dans l’exemple suivant, il est modifié pour afficher l’album photo avec une disposition en grille qui fait défiler horizontalement plutôt qu’avec une disposition linéaire verticale. Pour ce faire, l’instanciation du gestionnaire de disposition est modifiée pour utiliser le `GridLayoutManager` comme suit :

```csharp
mLayoutManager = new GridLayoutManager(this, 2, GridLayoutManager.Horizontal, false);
```

Cette modification du code remplace la verticale `LinearLayoutManager` par un `GridLayoutManager` qui présente une grille composée de deux lignes qui défilent dans le sens horizontal. Quand vous compilez et exécutez à nouveau l’application, vous verrez que les photographies sont affichées dans une grille et que le défilement est horizontal plutôt que vertical :

[![Exemple de capture d’écran d’une application avec défilement horizontal des photos dans une grille](recyclerview-example-images/04-gridlayoutmanager-sml.png)](recyclerview-example-images/04-gridlayoutmanager.png#lightbox)

En ne modifiant qu’une seule ligne de code, il est possible de modifier l’application d’affichage des photos pour utiliser une disposition différente avec un comportement différent.
Notez que ni le code de l’adaptateur ni le XML de disposition ne devaient être modifiés pour modifier le style de disposition. 

Dans la rubrique suivante, [extension de l’exemple RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md), cet exemple d’application de base est étendu pour gérer les événements de clic d’élément et mettre à jour `RecyclerView` lorsque la source de données sous-jacente est modifiée.

## <a name="related-links"></a>Liens connexes

- [RecyclerViewer (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Composants et fonctionnalités RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [Extension de l’exemple RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
