---
title: Extension de l’exemple RecyclerView
description: Ajout d’un gestionnaire d’événements de clic d’élément à l’application exemple RecyclerView.
ms.prod: xamarin
ms.assetid: 707EE1CE-C164-485B-944C-82C6795E8A24
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/13/2018
ms.openlocfilehash: d50971c1ef0064463e576a895729ad84577e1788
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028881"
---
# <a name="extending-the-recyclerview-example"></a>Extension de l’exemple RecyclerView

L’application de base décrite dans [un exemple RecyclerView de base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) n’est pas très &ndash; elle fait simplement défiler et affiche une liste fixe d’éléments de photographie pour faciliter la navigation. Dans les applications réelles, les utilisateurs s’attendent à pouvoir interagir avec l’application en appuyant sur les éléments de l’affichage. En outre, la source de données sous-jacente peut changer (ou être modifiée par l’application) et le contenu de l’affichage doit rester cohérent avec ces modifications. Dans les sections suivantes, vous apprendrez à gérer les événements de clic d’élément et à mettre à jour les `RecyclerView` lorsque la source de données sous-jacente change.

### <a name="handling-item-click-events"></a>Gestion des événements de clic d’élément

Lorsqu’un utilisateur touche un élément dans le `RecyclerView`, un événement de clic d’élément est généré pour notifier l’application de l’élément touché. Cet événement n’est pas généré par `RecyclerView` &ndash; à la place, la vue d’élément (qui est encapsulée dans le détenteur de la vue) détecte la touche et signale ces touches comme des événements de clic.

Pour illustrer comment gérer des événements de clic d’élément, les étapes suivantes expliquent comment l’application de visualisation de base est modifiée pour signaler la photographie touchée par l’utilisateur. Lorsqu’un événement de clic d’élément se produit dans l’exemple d’application, la séquence suivante a lieu :

1. Le `CardView` de la photographie détecte l’événement de clic d’élément et avertit l’adaptateur.

2. L’adaptateur transfère l’événement (avec les informations de position de l’élément) au gestionnaire de clic d’élément de l’activité.

3. Le gestionnaire de clics d’élément de l’activité répond à l’événement de clic d’élément.

Tout d’abord, un membre de gestionnaire d’événements appelé `ItemClick` est ajouté à la définition de classe `PhotoAlbumAdapter` :

```csharp
public event EventHandler<int> ItemClick;
```

Ensuite, une méthode de gestionnaire d’événements de clic d’élément est ajoutée à `MainActivity`.
Ce gestionnaire affiche brièvement un toast qui indique l’élément de photographie touché :

```csharp
void OnItemClick (object sender, int position)
{
    int photoNum = position + 1;
    Toast.MakeText(this, "This is photo number " + photoNum, ToastLength.Short).Show();
}

```

Ensuite, une ligne de code est nécessaire pour inscrire le gestionnaire de `OnItemClick` avec `PhotoAlbumAdapter`. Un bon endroit pour effectuer cette opération est immédiatement après la création de `PhotoAlbumAdapter` : 

```csharp
mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
mAdapter.ItemClick += OnItemClick;

```

Dans cet exemple de base, l’inscription du gestionnaire a lieu dans la méthode `OnCreate` de l’activité principale, mais une application de production peut inscrire le gestionnaire dans `OnResume` et annuler son inscription dans `OnPause` &ndash; consultez [cycle de vie des activités](~/android/app-fundamentals/activity-lifecycle/index.md) pour plus d’informations.

`PhotoAlbumAdapter` appelle alors `OnItemClick` lorsqu’il reçoit un événement de clic d’élément. L’étape suivante consiste à créer un gestionnaire dans l’adaptateur qui déclenche cet événement `ItemClick`. La méthode suivante, `OnClick`, est ajoutée immédiatement après la méthode `ItemCount` de l’adaptateur :

```csharp
void OnClick (int position)
{
    if (ItemClick != null)
        ItemClick (this, position);
}
```

Cette méthode `OnClick` est l' *écouteur* de l’adaptateur pour les événements de clic d’élément dans les vues d’élément. Avant que cet écouteur puisse être inscrit avec un affichage d’élément (via le détenteur de vue de la vue d’élément), le constructeur de `PhotoViewHolder` doit être modifié pour accepter cette méthode comme argument supplémentaire et inscrire `OnClick` avec la vue d’élément `Click` événement.
Voici le constructeur de `PhotoViewHolder` modifié :

```csharp
public PhotoViewHolder (View itemView, Action<int> listener)
    : base (itemView)
{
    Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
    Caption = itemView.FindViewById<TextView> (Resource.Id.textView);

    itemView.Click += (sender, e) => listener (base.LayoutPosition);
}

```

Le paramètre `itemView` contient une référence à la `CardView` qui a été touchée par l’utilisateur. Notez que la classe de base du détenteur de la vue connaît la position de la disposition de l’élément (`CardView`) qu’elle représente (via la propriété `LayoutPosition`), et cette position est transmise à la méthode `OnClick` de l’adaptateur lorsqu’un événement élément-clic se produit. La méthode `OnCreateViewHolder` de l’adaptateur est modifiée pour passer la méthode `OnClick` de l’adaptateur au constructeur du titulaire de la vue :

```csharp
PhotoViewHolder vh = new PhotoViewHolder (itemView, OnClick);
```

Désormais, lorsque vous générez et exécutez l’exemple d’application de visualisation de photos, si vous appuyez sur une photo dans l’affichage, un toast s’affiche pour signaler la photographie touchée :

[![exemple de Toast qui apparaît lorsqu’une carte photo est frappée](extending-the-example-images/01-photo-selected-sml.png)](extending-the-example-images/01-photo-selected.png#lightbox)

Cet exemple illustre une seule approche pour implémenter des gestionnaires d’événements avec `RecyclerView`. Une autre approche qui pourrait être utilisée ici consiste à placer les événements sur le détenteur de la vue et à faire en sorte que l’adaptateur s’abonne à ces événements. Si l’exemple d’application photo a fourni une fonctionnalité de modification de photos, des événements distincts seraient requis pour la `ImageView` et le `TextView` dans chaque `CardView`: la touche sur le `TextView` lancerait une boîte de dialogue de `EditView` qui permet à l’utilisateur de modifier la légende et s’appuient sur le `ImageView` lanceraient un outil de retouche de photo qui permet à l’utilisateur de rogner ou de faire pivoter la photo. En fonction des besoins de votre application, vous devez concevoir la meilleure approche pour la gestion et la réponse aux événements tactiles.

Pour montrer comment les `RecyclerView` peuvent être mises à jour lors de la modification du jeu de données, l’exemple d’application de visionnage de photos peut être modifié pour choisir de manière aléatoire une photo dans la source de données et l’échanger avec la première photo. Tout d’abord, un bouton de **sélection aléatoire** est ajouté à la disposition **main. AXML** de l’exemple d’application photo :

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <Button
        android:id="@+id/randPickButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:gravity="center_horizontal"
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:text="Random Pick" />
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:scrollbars="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```

Ensuite, le code est ajouté à la fin de la méthode `OnCreate` de l’activité principale pour localiser le bouton `Random Pick` dans la disposition et y attacher un gestionnaire :

```csharp
Button randomPickBtn = FindViewById<Button>(Resource.Id.randPickButton);

randomPickBtn.Click += delegate
{
    if (mPhotoAlbum != null)
    {
        // Randomly swap a photo with the first photo:
        int idx = mPhotoAlbum.RandomSwap();
    }
};

```

Ce gestionnaire appelle la méthode de `RandomSwap` de l’album photo lorsque le bouton de **sélection aléatoire** est enfoncé. La méthode `RandomSwap` permute de façon aléatoire une photo avec la première photo dans la source de données, puis retourne l’index de la photo échangée aléatoirement. Quand vous compilez et exécutez l’exemple d’application avec ce code, le fait de cliquer sur le bouton de **sélection aléatoire** n’entraîne pas de modification de l’affichage, car le `RecyclerView` n’est pas informé de la modification apportée à la source de données.

Pour conserver `RecyclerView` mis à jour après la modification de la source de données, le gestionnaire de clics de **sélection aléatoire** doit être modifié pour appeler la méthode `NotifyItemChanged` de l’adaptateur pour chaque élément de la collection qui a changé (dans ce cas, deux éléments ont changé : la première photo et la photo permutée). Dans ce cas, `RecyclerView` met à jour son affichage afin qu’il soit cohérent avec le nouvel état de la source de données :

```csharp
Button randomPickBtn = FindViewById<Button>(Resource.Id.randPickButton);

randomPickBtn.Click += delegate
{
    if (mPhotoAlbum != null)
    {
        int idx = mPhotoAlbum.RandomSwap();

        // First photo has changed:
        mAdapter.NotifyItemChanged(0);

        // Swapped photo has changed:
        mAdapter.NotifyItemChanged(idx);
    }
};

```

Désormais, quand le bouton de **sélection aléatoire est sélectionné** , `RecyclerView` met à jour l’affichage pour montrer qu’une photo plus loin dans la collection a été permutée avec la première photo de la collection :

[![première capture d’écran avant l’échange, deuxième capture d’écran après l’échange](extending-the-example-images/02-random-pick-sml.png)](extending-the-example-images/02-random-pick.png#lightbox)

Bien entendu, `NotifyDataSetChanged` aurait pu être appelée au lieu d’effectuer les deux appels à `NotifyItemChanged`, mais cela entraînerait l’actualisation de `RecyclerView` de la collection entière, même si seulement deux éléments de la collection avaient changé. L’appel de `NotifyItemChanged` est beaucoup plus efficace que l’appel de `NotifyDataSetChanged`.

## <a name="related-links"></a>Liens associés

- [RecyclerViewer (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Composants et fonctionnalités RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [Exemple de RecyclerView de base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
