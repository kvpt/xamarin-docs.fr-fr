---
title: Remplissage d’un ListView Xamarin. Android avec des données
ms.prod: xamarin
ms.assetid: AC4F95C8-EC3F-D960-7D44-8D55D0E4F1B6
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2017
ms.openlocfilehash: e92aada7be8a296baeaa9eebfb18fe906b5c3b63
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522541"
---
# <a name="populating-a-xamarinandroid-listview-with-data"></a>Remplissage d’un ListView Xamarin. Android avec des données

Pour ajouter des lignes à `ListView` un, vous devez l’ajouter à votre disposition et implémenter un `IListAdapter` avec des `ListView` méthodes que le appelle pour s’alimenter. Android comprend des classes intégrées `ListActivity` et `ArrayAdapter` que vous pouvez utiliser sans définir de code XML ou de code de disposition personnalisé. La `ListActivity` classe crée automatiquement un `ListView` et expose une `ListAdapter` propriété pour fournir les vues de ligne à afficher via un adaptateur.

Les adaptateurs intégrés prennent un ID de ressource de vue en tant que paramètre qui est utilisé pour chaque ligne. Vous pouvez utiliser des ressources intégrées, telles que celles `Android.Resource.Layout` -ci, pour ne pas avoir à écrire les vôtres.

## <a name="using-listactivity-and-arrayadapterltstringgt"></a>Utilisation de la chaîne&lt;ListActivity et ArrayAdapter&gt;

L’exemple **BasicTable/homescreen. cs** montre comment utiliser ces classes pour afficher un `ListView` en seulement quelques lignes de code:

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


### <a name="handling-row-clicks"></a>Gestion des clics de ligne

En général `ListView` , un permet également à l’utilisateur de toucher une ligne pour effectuer une action (par exemple, la diffusion d’une chanson, l’appel d’un contact ou l’affichage d’un autre écran). Pour répondre aux touches de l’utilisateur, il doit y avoir une autre méthode `ListActivity` implémentée de &ndash; la `OnListItemClick` &ndash; manière suivante:

[![Capture d’écran d’un SimpleListItem](populating-images/simplelistitem1.png)](populating-images/simplelistitem1.png#lightbox)

```csharp
protected override void OnListItemClick(ListView l, View v, int position, long id)
{
   var t = items[position];
   Android.Widget.Toast.MakeText(this, t, Android.Widget.ToastLength.Short).Show();
}
```

L’utilisateur peut maintenant toucher une ligne et une `Toast` alerte s’affiche:

[![Capture d’écran du Toast qui apparaît lorsqu’une ligne est touchée](populating-images/basictable2.png)](populating-images/basictable2.png#lightbox)


## <a name="implementing-a-listadapter"></a>Implémentation d’un ListAdapter

`ArrayAdapter<string>`est parfait en raison de sa simplicité, mais il est extrêmement limité. Toutefois, il arrive souvent que vous ayez une collection d’entités métier, plutôt que simplement des chaînes que vous souhaitez lier.
Par exemple, si vos données se composent d’une collection de classes Employee, vous souhaiterez peut-être que la liste affiche simplement les noms de chaque employé. Pour personnaliser le comportement d’un `ListView` objet afin de contrôler les données affichées, vous devez implémenter une `BaseAdapter` sous-classe de remplacement des quatre éléments suivants:

- **Nombre** &ndash; Pour indiquer au contrôle le nombre de lignes dans les données.

- **GetView** &ndash; Pour retourner une vue pour chaque ligne, remplie avec des données.
    Cette méthode a un paramètre pour que `ListView` le transmette une ligne existante inutilisée pour une réutilisation.

- **GetItemID** &ndash; Retourne un identificateur de ligne (en général, le numéro de ligne, bien qu’il puisse s’agir de toute valeur de type long que vous aimez).

- Cet indexeur &ndash; **[int]** retourne les données associées à un numéro de ligne particulier.

L’exemple de code dans **BasicTableAdapter/HomeScreenAdapter. cs** montre comment effectuer une `BaseAdapter`sous-classe:

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


### <a name="using-a-custom-adapter"></a>Utilisation d’un adaptateur personnalisé

L’utilisation de l’adaptateur personnalisé est similaire à celle intégrée `ArrayAdapter`, en passant un `context` et les `string[]` valeurs de à afficher:

```csharp
ListAdapter = new HomeScreenAdapter(this, items);
```

Étant donné que cet exemple utilise la même disposition`SimpleListItem1`de ligne (), l’application obtenue sera identique à l’exemple précédent.


### <a name="row-view-re-use"></a>Réutilisation de la vue de ligne

Dans cet exemple, il n’y a que six éléments. Étant donné que l’écran peut contenir huit, aucune réutilisation de ligne n’est requise. Toutefois, lors de l’affichage de centaines ou de milliers de lignes, il s’agit d’un gaspillage de mémoire `View` pour créer des centaines ou des milliers d’objets lorsque seulement huit s’adaptent à l’écran à la fois. Pour éviter cette situation, lorsqu’une ligne disparaît de l’écran, sa vue est placée dans une file d’attente en vue de sa réutilisation. Au fur et à mesure que l' `ListView` utilisateur `GetView` fait défiler, les &ndash; appels pour demander de nouvelles vues s’affichent s' `convertView` ils sont disponibles et passent une vue inutilisée dans le paramètre. Si cette valeur est null, votre code doit créer une nouvelle instance de vue, sinon vous pouvez redéfinir les propriétés de cet objet et le réutiliser.

La `GetView` méthode doit suivre ce modèle pour réutiliser les vues de lignes:

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

Les implémentations d’adaptateur personnalisées doivent *toujours* réutiliser l' `convertView` objet avant de créer de nouvelles vues pour s’assurer qu’elles ne manquent pas de mémoire lors de l’affichage de longues listes.

Certaines implémentations d’adaptateur (telles que `CursorAdapter`) n’ont pas `GetView` de méthode, mais elles nécessitent deux méthodes `NewView` différentes `BindView` et qui appliquent la réutilisation de lignes en séparant `GetView` les responsabilités de en deux leurs. Un `CursorAdapter` exemple se trouve plus loin dans le document.


## <a name="enabling-fast-scrolling"></a>Activation du défilement rapide

Le défilement rapide aide l’utilisateur à faire défiler les longues listes en fournissant un «handle» supplémentaire qui agit comme une barre de défilement pour accéder directement à une partie de la liste. Cette capture d’écran montre la poignée de défilement rapide:

[![Capture d’écran du défilement rapide avec une poignée de défilement](populating-images/fastscroll.png)](populating-images/fastscroll.png#lightbox)

L’affichage de la poignée de défilement rapide est aussi simple que l’affectation `FastScrollEnabled` de la `true`valeur à la propriété:

```csharp
ListView.FastScrollEnabled = true;
```


### <a name="adding-a-section-index"></a>Ajout d’un index de section

Un index de section fournit des commentaires supplémentaires aux utilisateurs lorsqu’ils défilent rapidement dans une longue &ndash; liste, il affiche la «section» vers laquelle ils ont fait défiler. Pour que l’index de section apparaisse, la sous-classe d' `ISectionIndexer` adaptateur doit implémenter l’interface pour fournir le texte de l’index en fonction des lignes affichées:

[![Capture d’écran de l’affichage de la section ci-dessus commençant par H](populating-images/sectionindex.png)](populating-images/sectionindex.png#lightbox)

Pour implémenter `ISectionIndexer` , vous devez ajouter trois méthodes à un adaptateur:

- **GetSections** &ndash; Fournit la liste complète des titres d’index de section qui peuvent être affichés. Cette méthode nécessite un tableau d’objets Java afin que le code doive créer un `Java.Lang.Object[]` à partir d’une collection .net. Dans notre exemple, elle retourne une liste des caractères initiaux de la liste `Java.Lang.String` sous la forme.

- **GetPositionForSection** &ndash; Retourne la position de la première ligne d’un index de section donné.

- **GetSectionForPosition** &ndash; Retourne l’index de la section à afficher pour une ligne donnée.


L’exemple `SectionIndex/HomeScreenAdapter.cs` de fichier implémente ces méthodes, ainsi que du code supplémentaire dans le constructeur. Le constructeur génère l’index de section en effectuant une boucle sur chaque ligne et en extrayant le premier caractère du titre (les éléments doivent déjà être triés pour que cela fonctionne).

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

Avec les structures de données créées, `ISectionIndexer` les méthodes sont très simples:

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

Vos titres d’index de section n’ont pas besoin de mapper 1:1 à vos sections réelles. C’est la raison `GetPositionForSection` pour laquelle la méthode existe.
`GetPositionForSection`vous donne la possibilité de mapper tous les index de votre liste d’index vers les sections qui sont en mode liste. Par exemple, vous pouvez avoir un «z» dans votre index, mais vous ne disposez peut-être pas d’une section de table pour chaque lettre. par conséquent, au lieu de mapper «z» à 26, il peut être mappé à 25 ou 24, ou à tout index de section «z».



## <a name="related-links"></a>Liens associés

- [BasicTableAndroid (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/basictableandroid)
- [BasicTableAdapter (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/basictableadapter)
- [FastScroll (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fastscroll)
