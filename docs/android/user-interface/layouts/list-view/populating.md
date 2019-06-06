---
title: Remplissage d’un ListView avec des données
ms.prod: xamarin
ms.assetid: AC4F95C8-EC3F-D960-7D44-8D55D0E4F1B6
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2017
ms.openlocfilehash: f3162c4da092048cd409f7b32438bc85dcedff19
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66740811"
---
# <a name="populating-a-listview-with-data"></a>Remplissage d’un ListView avec des données


## <a name="overview"></a>Vue d'ensemble

Pour ajouter des lignes à une `ListView` vous avez besoin pour l’ajouter à votre disposition et à implémenter un `IListAdapter` avec des méthodes qui le `ListView` appels à se remplir lui-même. Android inclut intégré `ListActivity` et `ArrayAdapter` classes que vous pouvez utiliser sans définir de disposition personnalisée XML ou de code. Le `ListActivity` classe crée automatiquement un `ListView` et expose un `ListAdapter` propriété permettant de fournir des affichages de ligne à afficher via une carte.

Les adaptateurs intégrés prennent un ID de ressource de vue en tant que paramètre qui est utilisé pour chaque ligne. Vous pouvez utiliser des ressources intégrées telles que celles dans `Android.Resource.Layout` vous n’avez pas besoin d’écrire votre propre.


## <a name="using-listactivity-and-arrayadapterltstringgt"></a>À l’aide de ListActivity et ArrayAdapter&lt;chaîne&gt;

L’exemple **BasicTable/HomeScreen.cs** montre comment utiliser ces classes pour afficher un `ListView` dans seulement quelques lignes de code :

```csharp
[Activity(Label = "BasicTable", MainLauncher = true, Icon = "@drawable/icon")]
public class HomeScreen : ListActivity {
   string[] items;
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       items = new string[] { "Vegetables","Fruits","Flower Buds","Legumes","Bulbs","Tubers" };
       ListAdapter = new ArrayAdapter<String>(this, Android.Resource.Layout.SimpleListItem1, items);
   }
   protected override void OnListItemClick(ListView l, View v, int position, long id)
}
```


### <a name="handling-row-clicks"></a>Gestion des ligne clique sur

Généralement un `ListView` permet également à l’utilisateur à toucher une ligne pour effectuer une action (par exemple, écouter une chanson ou appeler un contact présentant un autre écran). Pour répondre aux finales utilisateur il doit exister une méthode plus implémentée dans le `ListActivity` &ndash; `OnListItemClick` &ndash; comme suit :

[![Capture d’écran d’un SimpleListItem](populating-images/simplelistitem1.png)](populating-images/simplelistitem1.png#lightbox)

```csharp
protected override void OnListItemClick(ListView l, View v, int position, long id)
{
   var t = items[position];
   Android.Widget.Toast.MakeText(this, t, Android.Widget.ToastLength.Short).Show();
}
```

Maintenant l’utilisateur peut se toucher une ligne et qu’un `Toast` alerte s’affiche :

[![Capture d’écran de Toast qui apparaît lorsqu’une ligne est touchée.](populating-images/basictable2.png)](populating-images/basictable2.png#lightbox)


## <a name="implementing-a-listadapter"></a>Implémentation d’un ListAdapter

`ArrayAdapter<string>` est très utile en raison de sa simplicité, mais il extrêmement limité. Toutefois, il arrive souvent que vous avez une collection d’entités métier, plutôt que simplement des chaînes que vous voulez lier.
Par exemple, si vos données se composent d’une collection de classes d’employés, vous pouvez ensuite la liste pour afficher les noms de chaque employé. Pour personnaliser le comportement d’un `ListView` pour contrôler les données qui s’affiche vous devez implémenter une sous-classe de `BaseAdapter` remplaçant les quatre éléments suivants :

-   **Nombre** &ndash; pour indiquer le contrôle le nombre de lignes dans les données.

-   **GetView** &ndash; pour retourner une vue pour chaque ligne, rempli avec les données.
    Cette méthode a un paramètre pour le `ListView` à passer dans une ligne existante et inutilisée pour une réutilisation.

-   **GetItemId** &ndash; retourner un identificateur de ligne (généralement la ligne numéro, bien qu’il peut être toute valeur long que vous le souhaitez).

-   **Cet [int]** indexeur &ndash; pour retourner les données associées à un numéro de ligne particulière.

L’exemple de code dans **BasicTableAdapter/HomeScreenAdapter.cs** montre comment créer une sous-classe `BaseAdapter`:

```csharp
public class HomeScreenAdapter : BaseAdapter<string> {
   string[] items;
   Activity context;
   public HomeScreenAdapter(Activity context, string[] items) : base() {
       this.context = context;
       this.items = items;
   }
   public override long GetItemId(int position)
  {
       return position;
   }
   public override string this[int position] {  
       get { return items[position]; }
   }
   public override int Count {
       get { return items.Length; }
   }
   public override View GetView(int position, View convertView, ViewGroup parent)
   {
       View view = convertView; // re-use an existing view, if one is available
      if (view == null) // otherwise create a new one
           view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
       view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = items[position];
       return view;
   }
}
```


### <a name="using-a-custom-adapter"></a>À l’aide d’un adaptateur personnalisé

À l’aide de l’adaptateur personnalisé est similaire à intégrés `ArrayAdapter`, en passant un `context` et `string[]` des valeurs à afficher :

```csharp
ListAdapter = new HomeScreenAdapter(this, items);
```

Étant donné que cet exemple utilise la même disposition de ligne (`SimpleListItem1`) l’application résultante sera identique à l’exemple précédent.


### <a name="row-view-re-use"></a>Réutilisation d’affichage ligne

Dans cet exemple, il existe seulement six éléments. Étant donné que l’écran peut être contenue à huit, aucune ligne réutiliser ne requis. Lors de l’affichage des centaines voire des milliers de lignes, toutefois, il serait une perte de mémoire pour créer des centaines voire des milliers de `View` objets lorsque que huit tenir sur l’écran à la fois. Pour éviter cette situation, lorsqu’une ligne disparaît de l’écran, que son affichage est placé dans une file d’attente pour une réutilisation. Comme l’utilisateur fait défiler, le `ListView` appels `GetView` pour demander de nouvelles vues pour afficher &ndash; si disponible Il passe un affichage non utilisé dans le `convertView` paramètre. Si cette valeur est null, votre code doit créer une nouvelle instance de la vue, sinon vous pouvez ré-définir les propriétés de cet objet et les réutiliser.

Le `GetView` méthode doit suivre le modèle pour réutiliser des affichages de ligne :

```csharp
public override View GetView(int position, View convertView, ViewGroup parent)
{
   View view = convertView; // re-use an existing view, if one is supplied
   if (view == null) // otherwise create a new one
       view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
   // set view properties to reflect data for the given row
   view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = items[position];
   // return the view, populated with data, for display
   return view;
}
```

Implémentations d’adaptateur personnalisé doivent *toujours* réutiliser le `convertView` objet avant de créer de nouveaux affichages pour vous assurer qu’ils ne s’exécutent pas mémoire insuffisante lors de l’affichage de longues listes.

Certaines implémentations d’adaptateur (tels que le `CursorAdapter`) n’avez pas un `GetView` (méthode), au lieu de cela, ils nécessitent deux méthodes différentes `NewView` et `BindView` lequel appliquer la ligne réutiliser en séparant les responsabilités de `GetView` en deux méthodes. Il existe un `CursorAdapter` exemple plus loin dans le document.


## <a name="enabling-fast-scrolling"></a>Activer le défilement rapide

Défilement rapide vous aide à l’utilisateur de faire défiler les longues listes en fournissant un handle supplémentaire qui agit comme une barre de défilement pour accéder directement à une partie de la liste. Cette capture d’écran montre le handle de défilement rapide :

[![Capture d’écran de défilement rapide avec un descripteur de défilement](populating-images/fastscroll.png)](populating-images/fastscroll.png#lightbox)

À l’origine de la poignée de défilement rapide apparaissent est aussi simple que le paramètre le `FastScrollEnabled` propriété `true`:

```csharp
ListView.FastScrollEnabled = true;
```


### <a name="adding-a-section-index"></a>Ajout d’un Index de Section

Un index de la section fournit des commentaires supplémentaires pour les utilisateurs lorsqu’ils sont le défilement rapide via une longue liste &ndash; il montre quels qu’ils se trouvent à 'section'. À l’index de la section s’affichent la sous-classe de l’adaptateur doit implémenter la `ISectionIndexer` interface pour fournir le texte de l’index selon les lignes affichées :

[![Capture d’écran de H figurant au-dessus de section qui commence par H](populating-images/sectionindex.png)](populating-images/sectionindex.png#lightbox)

Pour implémenter `ISectionIndexer` vous devez ajouter trois méthodes à un adaptateur :

-   **GetSections** &ndash; fournit la liste complète de la section titres d’index qui peuvent être affichées. Cette méthode requiert un tableau d’objets Java par conséquent, le code doit créer un `Java.Lang.Object[]` à partir d’une collection .NET. Dans notre exemple, elle retourne une liste des premiers caractères dans la liste en tant que `Java.Lang.String` .

-   **GetPositionForSection** &ndash; retourne la première position de ligne pour un index de la section donné.

-   **GetSectionForPosition** &ndash; retourne l’index de la section à afficher pour une ligne donnée.


L’exemple `SectionIndex/HomeScreenAdapter.cs` fichier implémente ces méthodes, ainsi que du code supplémentaire dans le constructeur. Le constructeur construit l’index de la section par une boucle sur chaque ligne et extraire le premier caractère du titre de la (les éléments doivent déjà être triés pour que cela fonctionne).

```csharp
alphaIndex = new Dictionary<string, int>();
for (int i = 0; i < items.Length; i++) { // loop through items
   var key = items[i][0].ToString();
   if (!alphaIndex.ContainsKey(key))
       alphaIndex.Add(key, i); // add each 'new' letter to the index
}
sections = new string[alphaIndex.Keys.Count];
alphaIndex.Keys.CopyTo(sections, 0); // convert letters list to string[]

// Interface requires a Java.Lang.Object[], so we create one here
sectionsObjects = new Java.Lang.Object[sections.Length];
for (int i = 0; i < sections.Length; i++) {
   sectionsObjects[i] = new Java.Lang.String(sections[i]);
}
```

Avec les structures de données créés, le `ISectionIndexer` méthodes sont très simples :

```csharp
public Java.Lang.Object[] GetSections()
{
   return sectionsObjects;
}
public int GetPositionForSection(int section)
{
   return alphaIndexer[sections[section]];
}
public int GetSectionForPosition(int position)
{   // this method isn't called in this example, but code is provided for completeness
    int prevSection = 0;
    for (int i = 0; i < sections.Length; i++)
    {
        if (GetPositionForSection(i) > position)
        {
            break;
        }
        prevSection = i;
    }
    return prevSection;
}
```

Les titres de vos index de section n’avez pas besoin de mapper 1:1 à vos sections réelles. C’est pourquoi la `GetPositionForSection` méthode existe.
`GetPositionForSection` vous donne la possibilité de mapper les index se trouvent dans votre liste d’index pour toutes les sections sont dans votre affichage de liste. Par exemple, vous pouvez avoir un « z » dans votre index, mais vous ne disposez pas d’une section de la table pour chaque lettre, au lieu de mappage de « z » à 26, il peut mapper à 25 ou 24 ou quel que soit l’index de section « z » doit correspondre au.



## <a name="related-links"></a>Liens associés

- [BasicTableAndroid (sample)](https://developer.xamarin.com/samples/monodroid/BasicTableAndroid/)
- [BasicTableAdapter (sample)](https://developer.xamarin.com/samples/monodroid/BasicTableAdapter/)
- [FastScroll (sample)](https://developer.xamarin.com/samples/monodroid/FastScroll/)
