---
title: Contrôle de la Galerie Android
ms.prod: xamarin
ms.assetid: 3112E68A-7853-B147-90A6-6295CA2C4CB5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: 04fac4084328c0af962282dea7a31e3c00457bef
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523007"
---
# <a name="xamarinandroid-gallery-control"></a>Contrôle de la Galerie Xamarin. Android

[`Gallery`](xref:Android.Widget.Gallery)est un widget de disposition utilisé pour afficher des éléments dans une liste de défilement horizontalement et positionne la sélection actuelle au centre de la vue.

> [!IMPORTANT]
> Ce widget a été déconseillé dans Android 4,1 (niveau d’API 16). 

Dans ce didacticiel, vous allez créer une galerie de photos, puis afficher un message Toast chaque fois qu’un élément de la Galerie est sélectionné.

Une fois `Main.axml` que la disposition est définie pour l’affichage du `Gallery` contenu, le est capturé à [`FindViewById`](xref:Android.App.Activity.FindViewById*)partir de la disposition avec.
La[`Adapter`](xref:Android.Widget.AdapterView.RawAdapter)
la propriété est ensuite utilisée pour définir un adaptateur personnalisé `ImageAdapter`() comme source de tous les éléments à afficher dans le Dallery. Le `ImageAdapter` est créé à l’étape suivante.

Pour effectuer une opération quand l’utilisateur clique sur un élément de la Galerie, un délégué anonyme est abonné au[`ItemClick`](xref:Android.Widget.AdapterView.ItemClick)
. Elle montre un[`Toast`](xref:Android.Widget.Toast)
qui affiche la position d’index (de base zéro) de l’élément theselected (dans un scénario réel, la position peut être utilisée pour obtenir l’image de taille complète d’une autre tâche).

Tout d’abord, il y a quelques variables membres, y compris un tableau d’ID qui référencent les images enregistrées dans le répertoire des ressources pouvant être dessinées (**ressources/dessinables**).

Ensuite, il s’agit du constructeur de classe, où[`Context`](xref:Android.Content.Context)
pour une `ImageAdapter` instance est définie et enregistrée dans un champ local.
Ensuite, cette méthode implémente certaines méthodes requises héritées de [`BaseAdapter`](xref:Android.Widget.BaseAdapter).
Le constructeur et le[`Count`](xref:Android.Widget.BaseAdapter.Count)
les propriétés sont explicites. Règle[`GetItem(int)`](xref:Android.Widget.BaseAdapter.GetItem*)
doit retourner l’objet réel à la position spécifiée dans l’adaptateur, mais il est ignoré pour cet exemple. Egale[`GetItemId(int)`](xref:Android.Widget.BaseAdapter.GetItemId*)
doit retourner l’ID de ligne de l’élément, mais il n’est pas nécessaire ici.

La méthode effectue le travail pour appliquer une image à un[`ImageView`](xref:Android.Widget.ImageView)
qui sera incorporé dans le[`Gallery`](xref:Android.Widget.Gallery)
Dans cette méthode, le membre[`Context`](xref:Android.Content.Context)
est utilisé pour créer un nouveau [`ImageView`](xref:Android.Widget.ImageView).
La[`ImageView`](xref:Android.Widget.ImageView)
est préparé en appliquant une image à partir du tableau local de ressources dessinables, en définissant l’option[`Gallery.LayoutParams`](xref:Android.Widget.Gallery.LayoutParams)
hauteur et largeur de l’image, définition de l’échelle de façon à ce qu’elle corresponde à[`ImageView`](xref:Android.Widget.ImageView)
Dimensions, puis en définissant l’arrière-plan pour utiliser l’attribut styleable acquis dans le constructeur.

Pour [`ImageView.ScaleType`](xref:Android.Widget.ImageView.ScaleType) obtenir d’autres options de mise à l’échelle des images, consultez.

## <a name="walkthrough"></a>Procédure pas à pas

Démarrez un nouveau projet nommé *HelloGallery*.

[![Capture d’écran du nouveau projet Android dans la boîte de dialogue nouvelle solution](gallery-images/hellogallery1-sml.png)](gallery-images/hellogallery1.png#lightbox)

Recherchez des photos que vous souhaitez utiliser ou [Téléchargez ces exemples d’images](https://developer.android.com/shareables/sample_images.zip).
Ajoutez les fichiers image au répertoire Resources **/drawn** du projet. Dans la fenêtre **Propriétés** , définissez l’action de génération pour chaque sur **AndroidResource**.

Ouvrez **ressources/mise en page/main. AXML** et insérez ce qui suit:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Gallery xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gallery"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
/>
```

Ouvrez `MainActivity.cs` et insérez le code suivant pour le[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
méthode

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "main" layout resource
    SetContentView (Resource.Layout.Main);

    Gallery gallery = (Gallery) FindViewById<Gallery>(Resource.Id.gallery);

    gallery.Adapter = new ImageAdapter (this);

    gallery.ItemClick += delegate (object sender, Android.Widget.AdapterView.ItemClickEventArgs args) {
        Toast.MakeText (this, args.Position.ToString (), ToastLength.Short).Show ();
    };
}
```

Créez une nouvelle classe appelée `ImageAdapter` sous- [`BaseAdapter`](xref:Android.Widget.BaseAdapter)classes:

```csharp
public class ImageAdapter : BaseAdapter
{
    Context context;

    public ImageAdapter (Context c)
    {
          context = c;
    }

    public override int Count { get { return thumbIds.Length; } }

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
          ImageView i = new ImageView (context);

          i.SetImageResource (thumbIds[position]);
          i.LayoutParameters = new Gallery.LayoutParams (150, 100);
          i.SetScaleType (ImageView.ScaleType.FitXy);

          return i;
    }

    // references to our images
    int[] thumbIds = {
            Resource.Drawable.sample_1,
            Resource.Drawable.sample_2,
            Resource.Drawable.sample_3,
            Resource.Drawable.sample_4,
            Resource.Drawable.sample_5,
            Resource.Drawable.sample_6,
            Resource.Drawable.sample_7
     };
}

```

Exécutez l'application. Elle doit ressembler à la capture d’écran ci-dessous:

![Capture d’écran de HelloGallery affichant des exemples d’images](gallery-images/hellogallery3.png)

## <a name="references"></a>Références

- [`BaseAdapter`](xref:Android.Widget.BaseAdapter)
- [`Gallery`](xref:Android.Widget.Gallery)
- [`ImageView`](xref:Android.Widget.ImageView)

_Certaines parties de cette page sont des modifications basées sur le travail créé et partagé par le projet open source Android et utilisées conformément aux termes décrits dans la [licence d’attribution de Creative-2,5](http://creativecommons.org/licenses/by/2.5/)._
