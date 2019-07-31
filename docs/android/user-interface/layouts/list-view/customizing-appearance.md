---
title: Personnalisation de l’apparence d’un ListView
ms.prod: xamarin
ms.assetid: B09AD282-2C4F-D71E-6806-9B1EF05C2CD4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/26/2018
ms.openlocfilehash: 9307e440a780d60a8301c58d70ee882fbbdebab1
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646399"
---
# <a name="customizing-a-listviews-appearance-with-xamarinandroid"></a>Personnalisation de l’apparence d’un ListView avec Xamarin. Android

L’apparence d’un ListView est dictée par la disposition des lignes affichées. Pour modifier l’apparence d’un `ListView`, utilisez une disposition de ligne différente.


## <a name="built-in-row-views"></a>Vues de lignes intégrées

Il existe douze vues intégrées qui peuvent être référencées à l’aide d' **Android. Resource. Layout**:

- **TestListItem** &ndash; Une seule ligne de texte avec une mise en forme minimale.

- **SimpleListItem1** &ndash; Une seule ligne de texte.

- **SimpleListItem2** &ndash; Deux lignes de texte.

- **SimpleSelectableListItem** &ndash; Une seule ligne de texte qui prend en charge la sélection d’un ou de plusieurs éléments (ajouté au niveau de l’API 11).

- **SimpleListItemActivated1** &ndash; Semblable à SimpleListItem1, mais la couleur d’arrière-plan indique qu’une ligne est sélectionnée (ajoutée au niveau de l’API 11).

- **SimpleListItemActivated2** &ndash; Semblable à SimpleListItem2, mais la couleur d’arrière-plan indique qu’une ligne est sélectionnée (ajoutée au niveau de l’API 11).

- **SimpleListItemChecked** &ndash; Affiche des coches pour indiquer la sélection.

- **SimpleListItemMultipleChoice** &ndash; Affiche des cases à cocher pour indiquer une sélection à choix multiple.

- **SimpleListItemSingleChoice** &ndash; Affiche des cases d’option pour indiquer une sélection mutuellement exclusive.

- **TwoLineListItem** &ndash; Deux lignes de texte.

- **ActivityListItem** &ndash; Une seule ligne de texte avec une image.

- **SimpleExpandableListItem** &ndash; Regroupe les lignes par catégories et chaque groupe peut être développé ou réduit.

Chaque vue de ligne intégrée est associée à un style intégré. Ces captures d’écran montrent comment chaque vue s’affiche:

[![Captures d’écran de TestListItem, SimpleSelectableListItem, SimpleListitem1 et SimpleListItem2](customizing-appearance-images/builtinviews.png)](customizing-appearance-images/builtinviews.png#lightbox)

[![Captures d’écran de SimpleListItemActivated1, SimpleListItemActivated2, SimpleListItemChecked et SimpleListItemMultipleChecked](customizing-appearance-images/builtinviews-2.png)](customizing-appearance-images/builtinviews-2.png#lightbox)

[![Captures d’écran de SimpleListItemSingleChoice, TwoLineListItem, ActivityListItem et SimpleExpandableListItem](customizing-appearance-images/builtinviews-3.png)](customizing-appearance-images/builtinviews-3.png#lightbox)

L’exemple de fichier **BuiltInViews/HomeScreenAdapter. cs** (dans la solution **BuiltInViews** ) contient le code permettant de générer les écrans d’éléments de liste non développables. La vue est définie dans la `GetView` méthode comme suit:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
```

Les propriétés de la vue peuvent ensuite être définies en référençant les identificateurs `Text1`de `Text2` contrôle standard `Android.Resource.Id` , et `Icon` sous (ne pas définir les propriétés que la vue ne contient pas ou une exception sera levée):

```csharp
view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = item.Heading;
view.FindViewById<TextView>(Android.Resource.Id.Text2).Text = item.SubHeading;
view.FindViewById<ImageView>(Android.Resource.Id.Icon).SetImageResource(item.ImageResourceId); // only use with ActivityListItem
```

L’exemple de fichier **BuiltInExpandableViews/ExpandableScreenAdapter. cs** (dans la solution **BuiltInViews** ) contient le code permettant de générer l’écran SimpleExpandableListItem. La vue de groupe est définie dans `GetGroupView` la méthode comme suit:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem1, null);
```

La vue enfant est définie dans la `GetChildView` méthode comme suit:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem2, null);
```

Les propriétés de la vue de groupe et de la vue enfant peuvent ensuite être définies en référençant `Text2` les identificateurs standard `Text1` et de contrôle comme indiqué ci-dessus. La capture d’écran SimpleExpandableListItem (illustrée ci-dessus) fournit un exemple de vue de groupe d’une ligne (SimpleExpandableListItem1) et d’une vue enfant de deux lignes (SimpleExpandableListItem2). La vue de groupe peut également être configurée pour deux lignes (SimpleExpandableListItem2) et la vue enfant peut être configurée pour une seule ligne (SimpleExpandableListItem1), ou les deux affichages groupe et enfant peuvent avoir le même nombre de lignes. 



## <a name="accessories"></a>Automobiles

Les lignes peuvent avoir des accessoires ajoutés à droite de la vue pour indiquer l’état de sélection:

- **SimpleListItemChecked** &ndash; Crée une liste à sélection unique avec un contrôle en tant qu’indicateur.

- **SimpleListItemSingleChoice** &ndash; Crée des listes de types de boutons radio où un seul choix est possible.

- **SimpleListItemMultipleChoice** &ndash; Crée des listes de types de cases à cocher là où plusieurs choix sont possibles.

Les accessoires susmentionnés sont illustrés dans les écrans suivants, dans leur ordre respectif:

[![Captures d’écran de SimpleListItemChecked, SimpleListItemSingleChoice et SimpleListItemMultipleChoice avec accessoires](customizing-appearance-images/accessories.png)](customizing-appearance-images/accessories.png#lightbox)

Pour afficher l’un de ces accessoires, transmettez l’ID de ressource de disposition requis à l’adaptateur, puis définissez manuellement l’état de sélection pour les lignes requises. Cette ligne de code montre comment créer et assigner `Adapter` un à l’aide de l’une de ces dispositions:

```csharp
ListAdapter = new ArrayAdapter<String>(this, Android.Resource.Layout.SimpleListItemChecked, items);
```

Le `ListView` lui-même prend en charge différents modes de sélection, quel que soit l’accessoire affiché. Pour éviter toute confusion, `Single` utilisez le mode `SingleChoice` de sélection avec `Checked` accessoires `Multiple` et le mode `MultipleChoice` ou avec le style. Le mode `ChoiceMode` `ListView`de sélection est contrôlé par la propriété de.


### <a name="handling-api-level"></a>Gestion du niveau de l’API

Les versions antérieures de Xamarin. Android implémentaient des énumérations en tant que propriétés entières. La dernière version a introduit des types d’énumération .NET appropriés, ce qui facilite grandement la détection des options potentielles.

Selon le niveau d’API que vous ciblez, `ChoiceMode` est soit un entier, soit une énumération. L’exemple de fichier **AccessoryViews/homescreen. cs** comporte un bloc commenté si vous souhaitez cibler l’API Gingerbread:

```csharp
// For targeting Gingerbread the ChoiceMode is an int, otherwise it is an
// enumeration.

lv.ChoiceMode = Android.Widget.ChoiceMode.Single; // 1
//lv.ChoiceMode = Android.Widget.ChoiceMode.Multiple; // 2
//lv.ChoiceMode = Android.Widget.ChoiceMode.None; // 0

// Use this block if targeting Gingerbread or lower
/*
lv.ChoiceMode = 1; // Single
//lv.ChoiceMode = 0; // none
//lv.ChoiceMode = 2; // Multiple
//lv.ChoiceMode = 3; // MultipleModal
*/
```


### <a name="selecting-items-programmatically"></a>Sélection d’éléments par programmation

La définition manuelle des éléments sélectionnés est effectuée à l’aide de `SetItemChecked` la méthode (elle peut être appelée plusieurs fois pour une sélection multiple):

```csharp
// Set the initially checked row ("Fruits")
lv.SetItemChecked(1, true);
```

Le code doit également détecter des sélections uniques différemment de plusieurs sélections. Pour déterminer la ligne qui a été sélectionnée `Single` en mode, `CheckedItemPosition` utilisez la propriété Integer:

```csharp
FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPosition
```

Pour déterminer les lignes qui ont été sélectionnées `Multiple` en mode, vous devez effectuer une `CheckedItemPositions` boucle `SparseBooleanArray`dans. Un tableau fragmenté est comme un dictionnaire qui contient uniquement des entrées pour lesquelles la valeur a été modifiée. vous devez donc traverser l’intégralité du tableau `true` à la recherche de valeurs pour savoir ce qui a été sélectionné dans la liste, comme illustré dans l’extrait de code suivant:

```csharp
var sparseArray = FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPositions;
for (var i = 0; i < sparseArray.Size(); i++ )
{
   Console.Write(sparseArray.KeyAt(i) + "=" + sparseArray.ValueAt(i) + ",");
}
Console.WriteLine();
```


## <a name="creating-custom-row-layouts"></a>Création de dispositions de ligne personnalisées

Les quatre vues de lignes intégrées sont très simples. Pour afficher des dispositions plus complexes (par exemple, une liste d’e-mails ou des tweets, ou des informations de contact), vous devez disposer d’une vue personnalisée. Les vues personnalisées sont généralement déclarées en tant que fichiers AXML dans le répertoire Resources **/Layout** , puis chargées à l’aide de leur ID de ressource par un adaptateur personnalisé. La vue peut contenir n’importe quel nombre de classes d’affichage (telles que TextViews, ImageViews et d’autres contrôles) avec des couleurs, des polices et une disposition personnalisées.

Cet exemple diffère des exemples précédents de plusieurs façons:

-  Hérite de `Activity` , et `ListActivity` non de. Vous pouvez personnaliser des lignes pour `ListView` tout, mais d’autres contrôles peuvent également être inclus `Activity` dans une disposition (par exemple, un en-tête, des boutons ou d’autres éléments d’interface utilisateur). Cet exemple ajoute un en-tête `ListView` au-dessus du à illustrer.

-  Requiert un fichier de disposition AXML pour l’écran; dans les exemples précédents, `ListActivity` le ne requiert pas de fichier de disposition. Ce AXML contient une `ListView` déclaration de contrôle.

-  Requiert un fichier de disposition AXML pour afficher chaque ligne. Ce fichier AXML contient les contrôles texte et image avec des paramètres de couleur et de police personnalisés.

-  Utilise un fichier XML de sélecteur personnalisé facultatif pour définir l’apparence de la ligne lorsqu’elle est sélectionnée.

-  L' `Adapter` implémentation retourne une disposition personnalisée à partir `GetView` du remplacement.

-  `ItemClick`doit être déclaré différemment (un gestionnaire d’événements est attaché `ListView.ItemClick` à plutôt qu’à une `OnListItemClick` substitution `ListActivity`dans).


Ces modifications sont détaillées ci-dessous, en commençant par la création de l’affichage de l’activité et de la vue de ligne personnalisée, puis en couvrant les modifications apportées à l’adaptateur et à l’activité pour les afficher.


### <a name="adding-a-listview-to-an-activity-layout"></a>Ajout d’un ListView à une disposition d’activité

Étant `HomeScreen` donné qu’il n’hérite plus de `ListActivity` ce mode par défaut, un fichier de mise en page AXML doit être créé pour l’affichage du homescreen. Pour cet exemple, la vue aura un en-tête (à `TextView`l’aide de `ListView` ) et un pour afficher les données. La disposition est définie dans le fichier Resources **/Layout/homescreen. AXML,** qui est indiqué ici:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
   android:orientation="vertical"
   android:layout_width="fill_parent"
   android:layout_height="fill_parent">
    <TextView android:id="@+id/Heading"
        android:text="Vegetable Groups"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:background="#00000000"
        android:textSize="30dp"
        android:textColor="#FF267F00"
        android:textStyle="bold"
        android:padding="5dp"
    />
    <ListView android:id="@+id/List"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:cacheColorHint="#FFDAFF7F"
    />
</LinearLayout>
```

L’avantage de l’utilisation `Activity` d’un avec une disposition personnalisée (au `ListActivity`lieu d’un) consiste à pouvoir ajouter des contrôles supplémentaires à l’écran, tels que `TextView` l’en-tête de cet exemple.


### <a name="creating-a-custom-row-layout"></a>Création d’une disposition de ligne personnalisée

Un autre fichier de disposition AXML est requis pour contenir la disposition personnalisée pour chaque ligne qui s’affichera en mode liste. Dans cet exemple, la ligne aura un arrière-plan vert, un texte brun et une image alignée à droite. Le balisage Android XML permettant de déclarer cette disposition est décrit dans Resources **/Layout/CustomView. AXML**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout  xmlns:android="http://schemas.android.com/apk/res/android"
   android:layout_width="fill_parent"
   android:layout_height="wrap_content"
   android:background="#FFDAFF7F"
   android:padding="8dp">
    <LinearLayout android:id="@+id/Text"
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
         android:textStyle="italic"
         />
        <TextView
         android:id="@+id/Text2"
         android:layout_width="wrap_content"
         android:layout_height="wrap_content"
         android:textSize="14dip"
         android:textColor="#FF267F00"
         android:paddingLeft="100dip"
         />
    </LinearLayout>
    <ImageView
        android:id="@+id/Image"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:padding="5dp"
        android:src="@drawable/icon"
        android:layout_alignParentRight="true" />
</RelativeLayout >
```

Alors qu’une disposition de ligne personnalisée peut contenir de nombreux contrôles différents, les performances de défilement peuvent être affectées par des conceptions complexes et l’utilisation d’images (surtout si elles doivent être chargées sur le réseau). Pour plus d’informations sur l’adressage des problèmes de performances de défilement, consultez l’article de Google.


### <a name="referencing-a-custom-row-view"></a>Référencement d’une vue de ligne personnalisée

L’implémentation de l’exemple d’adaptateur personnalisé est `HomeScreenAdapter.cs`dans. La méthode clé est `GetView` l’emplacement où elle charge le AXML personnalisé à l' `Resource.Layout.CustomView`aide de l’ID de ressource, puis définit les propriétés sur chacun des contrôles de la vue avant de la retourner. La classe d’adaptateur complète est illustrée ci-dessous:

```csharp
public class HomeScreenAdapter : BaseAdapter<TableItem> {
   List<TableItem> items;
   Activity context;
   public HomeScreenAdapter(Activity context, List<TableItem> items)
       : base()
   {
       this.context = context;
       this.items = items;
   }
   public override long GetItemId(int position)
   {
       return position;
   }
   public override TableItem this[int position]
   {
       get { return items[position]; }
   }
   public override int Count
   {
       get { return items.Count; }
   }
   public override View GetView(int position, View convertView, ViewGroup parent)
   {
       var item = items[position];
       View view = convertView;
       if (view == null) // no view to re-use, create new
           view = context.LayoutInflater.Inflate(Resource.Layout.CustomView, null);
       view.FindViewById<TextView>(Resource.Id.Text1).Text = item.Heading;
       view.FindViewById<TextView>(Resource.Id.Text2).Text = item.SubHeading;
       view.FindViewById<ImageView>(Resource.Id.Image).SetImageResource(item.ImageResourceId);
       return view;
   }
}
```


### <a name="referencing-the-custom-listview-in-the-activity"></a>Référencement du ListView personnalisé dans l’activité

Étant donné `HomeScreen` que la classe hérite `Activity`désormais de `ListView` , un champ est déclaré dans la classe pour contenir une référence au contrôle déclaré dans AXML:

```csharp
ListView listView;
```

La classe doit ensuite charger le AXML de disposition personnalisé de l’activité `SetContentView` à l’aide de la méthode. Il peut alors trouver le `ListView` contrôle dans la disposition, puis crée et assigne l’adaptateur et assigne le gestionnaire de clics. Le code de la méthode OnCreate est illustré ici:

```csharp
SetContentView(Resource.Layout.HomeScreen); // loads the HomeScreen.axml as this activity's view
listView = FindViewById<ListView>(Resource.Id.List); // get reference to the ListView in the layout

// populate the listview with data
listView.Adapter = new HomeScreenAdapter(this, tableItems);
listView.ItemClick += OnListItemClick;  // to be defined
```

Enfin, `ItemClick` le gestionnaire doit être défini. dans ce cas, il affiche `Toast` simplement un message:

```csharp
void OnListItemClick(object sender, AdapterView.ItemClickEventArgs e)
{
   var listView = sender as ListView;
   var t = tableItems[e.Position];
   Android.Widget.Toast.MakeText(this, t.Heading, Android.Widget.ToastLength.Short).Show();
}
```

L’écran résultant ressemble à ceci:

[![Capture d’écran de l’CustomRowView résultant](customizing-appearance-images/customrowview.png)](customizing-appearance-images/customrowview.png#lightbox)



### <a name="customizing-the-row-selector-color"></a>Personnalisation de la couleur du sélecteur de lignes

Lorsqu’une ligne est touchée, elle doit être mise en surbrillance pour les commentaires des utilisateurs. Quand une vue personnalisée spécifie comme couleur d’arrière-plan comme **CustomView. AXML** , elle remplace également la mise en surbrillance de la sélection. Cette ligne de code dans **CustomView. AXML** définit l’arrière-plan sur le vert clair, mais cela signifie également qu’il n’y a pas d’indicateur visuel lorsque la ligne est touchée:

```xml
android:background="#FFDAFF7F"
```

Pour réactiver le comportement de mise en surbrillance, ainsi que pour personnaliser la couleur utilisée, affectez à l’attribut background un sélecteur personnalisé à la place. Le sélecteur déclare la couleur d’arrière-plan par défaut ainsi que la couleur de surbrillance. Le fichier **Resources/Drawable/CustomSelector. xml** contient la déclaration suivante:

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
<item android:state_pressed="false"
  android:state_selected="false"
  android:drawable="@color/cellback" />
<item android:state_pressed="true" >
  <shape>
     <gradient
      android:startColor="#E77A26"
        android:endColor="#E77A26"
        android:angle="270" />
  </shape>
</item>
<item android:state_selected="true"
  android:state_pressed="false"
  android:drawable="@color/cellback" />
</selector>
```

Pour référencer le sélecteur personnalisé, remplacez l’attribut d’arrière-plan dans **CustomView. AXML** par:

```xml
android:background="@drawable/CustomSelector"
```

Une ligne sélectionnée et le message `Toast` correspondant se présente désormais comme suit:

[![Une ligne sélectionnée en orange, avec un message Toast affichant le nom de la ligne sélectionnée](customizing-appearance-images/customselectcolor.png)](customizing-appearance-images/customselectcolor.png#lightbox)



### <a name="preventing-flickering-on-custom-layouts"></a>Empêcher le scintillement sur les dispositions personnalisées

Android tente d’améliorer les performances de `ListView` défilement en mettant en cache les informations de disposition. Si vous avez des listes de données à défilement long, vous devez également `android:cacheColorHint` définir la propriété `ListView` sur la déclaration dans la définition AXML de l’activité (sur la même valeur de couleur que l’arrière-plan de votre disposition de ligne personnalisée). Si cet indicateur n’est pas inclus, l’utilisateur fait défiler une liste avec les couleurs d’arrière-plan des lignes personnalisées.



## <a name="related-links"></a>Liens associés

- [BuiltInViews (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/builtinviews)
- [AccessoryViews (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/accessoryviews)
- [CustomRowView (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/customrowview)
