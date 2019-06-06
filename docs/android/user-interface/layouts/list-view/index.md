---
title: À l’aide de la ListView dans Xamarin.Android
description: ListView est un composant important de l’interface utilisateur des applications Android ; Il est utilisé partout dans les listes de courte des options de menu à longues listes de contacts ou favoris internet. Il fournit un moyen simple pour présenter une liste déroulante de lignes qui peuvent être mis en forme avec un style prédéfini ou largement personnalisée.
ms.prod: xamarin
ms.assetid: C2BA2705-9B20-01C2-468D-860BDFEDC157
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 2560a451f3a6e7dd09b687f9db8c0c070598def6
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66740662"
---
# <a name="listview"></a>Affichage de liste

_ListView est un composant important de l’interface utilisateur des applications Android ; Il est utilisé partout dans les listes de courte des options de menu à longues listes de contacts ou favoris internet. Il fournit un moyen simple pour présenter une liste déroulante de lignes qui peuvent être mis en forme avec un style prédéfini ou largement personnalisée._


## <a name="overview"></a>Vue d'ensemble

Liste des vues et adaptateurs sont inclus dans les blocs de construction fondamentales des Applications Android. Le `ListView` classe fournit un moyen souple pour présenter vos données, qu’il s’agisse d’un menu court ou une liste déroulante de long. Il fournit des fonctionnalités de facilité d’utilisation tels que les index de défilement, rapides et une sélection unique ou multiple pour vous aider à créer des interfaces utilisateur d’appareils mobiles pour vos applications. Une instance de `ListView` requiert un *Adapter* qui le remplit avec des données contenues dans les affichages de ligne.

Ce guide explique comment implémenter `ListView` et les différentes `Adapter` classes dans Xamarin.Android. Il montre également comment personnaliser l’apparence d’un `ListView`, et aborde l’importance de la ligne réutiliser pour réduire la consommation de mémoire. Il existe également quelques explications de l’impact de l’activité du cycle de vie `ListView` et `Adapter` utiliser. Si vous travaillez sur des applications multiplateformes avec Xamarin.iOS, le `ListView` contrôle est analogue à iOS `UITableView` (et le Android `Adapter` est similaire à la `UITableViewSource`).

Tout d’abord, un court didacticiel présente la `ListView` avec un exemple de code base. Ensuite, des liens vers des rubriques plus avancées sont fournies pour vous aider à utiliser `ListView` dans les applications du monde réel.


> [!NOTE]
> Le `RecyclerView` widget est une version plus avancée et flexible de `ListView`. Étant donné que `RecyclerView` est conçu pour être le successeur de `ListView` (et `GridView`), nous vous recommandons d’utiliser `RecyclerView` plutôt que `ListView` nouveau développement d’applications. Pour plus d’informations, consultez [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).



## <a name="listview-tutorial"></a>Didacticiel de ListView

[`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/) is a [`ViewGroup`](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)
qui crée une liste d’éléments à défilement. Les éléments de liste sont automatiquement insérés à la liste en utilisant un [ `IListAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.IListAdapter/).

Dans ce didacticiel, vous allez créer une liste déroulante de noms de pays qui sont lus à partir d’un tableau de chaînes. Lorsqu’un élément de liste est sélectionné, un message toast affichera la position de l’élément dans la liste.


Démarrer un nouveau projet nommé **HelloListView**.

Créer un fichier XML nommé **list_item.xml** et enregistrez-le à l’intérieur de la **disposition/ressources/** dossier. Insérer les éléments suivants :

```xml
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:padding="10dp"
    android:textSize="16sp">
</TextView>
```

Ce fichier définit la disposition pour chaque élément qui sera placée dans le [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/).

Ouvrez `MainActivity.cs` et modifiez la classe pour étendre [ `ListActivity` ](https://developer.xamarin.com/api/type/Android.App.ListActivity/) (au lieu de [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/)) :

```csharp
public class MainActivity : ListActivity
{
```

Insérez le code suivant pour le [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle))
méthode :

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);

    ListView.TextFilterEnabled = true;

    ListView.ItemClick += delegate (object sender, AdapterView.ItemClickEventArgs args)
    {
        Toast.MakeText(Application, ((TextView)args.View).Text, ToastLength.Short).Show();
    };
}
```

Notez que cela ne charge pas un fichier de disposition pour l’activité (qui vous le faites habituellement avec [ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32))).
Au lieu de cela, définissant le [`ListAdapter`](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/)
propriété ajoute automatiquement un [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
pour remplir tout l’écran de la [ `ListActivity` ](https://developer.xamarin.com/api/type/Android.App.ListActivity/).
Cette méthode prend un [ `ArrayAdapter<T>` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/), qui gère le tableau des éléments de liste qui seront placés dans le [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/).
Le [`ArrayAdapter<T>`](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/)
le constructeur prend l’application [ `Context` ](https://developer.xamarin.com/api/type/Android.Content.Context/), la description de la mise en page pour chaque élément de liste (créé à l’étape précédente) et un `T[]` ou [`Java.Util.IList<T>`](https://developer.xamarin.com/api/type/Java.Util.IList/)
tableau d’objets à insérer dans le [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
(définie ensuite).

Le [`TextFilterEnabled`](https://developer.xamarin.com/api/property/Android.Widget.AbsListView.TextFilterEnabled/)
propriété active sur le texte de filtrage pour le [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/), de sorte que lorsque l’utilisateur commence à taper, la liste sera filtrée.

Le [`ItemClick`](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/)
événement peut être utilisé pour s’abonner des gestionnaires de clics. Lorsqu’un élément dans le [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
est activé, le gestionnaire est appelé et un [`Toast`](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
message s’affiche, en utilisant le texte à partir de l’élément cliqué.

Vous pouvez utiliser des conceptions d’élément de liste fournies par la plate-forme au lieu de définir votre propre fichier de disposition de la [ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/).
Par exemple, essayez d’utiliser `Android.Resource.Layout.SimpleListItem1` au lieu de `Resource.Layout.list_item`.

Ajoutez le code suivant `using` instruction :

```csharp
using System;
```
Ensuite, ajoutez le tableau de chaînes suivantes en tant que membre de `MainActivity`:

```csharp
static readonly string[] countries = new String[] {
    "Afghanistan","Albania","Algeria","American Samoa","Andorra",
    "Angola","Anguilla","Antarctica","Antigua and Barbuda","Argentina",
    "Armenia","Aruba","Australia","Austria","Azerbaijan",
    "Bahrain","Bangladesh","Barbados","Belarus","Belgium",
    "Belize","Benin","Bermuda","Bhutan","Bolivia",
    "Bosnia and Herzegovina","Botswana","Bouvet Island","Brazil","British Indian Ocean Territory",
    "British Virgin Islands","Brunei","Bulgaria","Burkina Faso","Burundi",
    "Cote d'Ivoire","Cambodia","Cameroon","Canada","Cape Verde",
    "Cayman Islands","Central African Republic","Chad","Chile","China",
    "Christmas Island","Cocos (Keeling) Islands","Colombia","Comoros","Congo",
    "Cook Islands","Costa Rica","Croatia","Cuba","Cyprus","Czech Republic",
    "Democratic Republic of the Congo","Denmark","Djibouti","Dominica","Dominican Republic",
    "East Timor","Ecuador","Egypt","El Salvador","Equatorial Guinea","Eritrea",
    "Estonia","Ethiopia","Faeroe Islands","Falkland Islands","Fiji","Finland",
    "Former Yugoslav Republic of Macedonia","France","French Guiana","French Polynesia",
    "French Southern Territories","Gabon","Georgia","Germany","Ghana","Gibraltar",
    "Greece","Greenland","Grenada","Guadeloupe","Guam","Guatemala","Guinea","Guinea-Bissau",
    "Guyana","Haiti","Heard Island and McDonald Islands","Honduras","Hong Kong","Hungary",
    "Iceland","India","Indonesia","Iran","Iraq","Ireland","Israel","Italy","Jamaica",
    "Japan","Jordan","Kazakhstan","Kenya","Kiribati","Kuwait","Kyrgyzstan","Laos",
    "Latvia","Lebanon","Lesotho","Liberia","Libya","Liechtenstein","Lithuania","Luxembourg",
    "Macau","Madagascar","Malawi","Malaysia","Maldives","Mali","Malta","Marshall Islands",
    "Martinique","Mauritania","Mauritius","Mayotte","Mexico","Micronesia","Moldova",
    "Monaco","Mongolia","Montserrat","Morocco","Mozambique","Myanmar","Namibia",
    "Nauru","Nepal","Netherlands","Netherlands Antilles","New Caledonia","New Zealand",
    "Nicaragua","Niger","Nigeria","Niue","Norfolk Island","North Korea","Northern Marianas",
    "Norway","Oman","Pakistan","Palau","Panama","Papua New Guinea","Paraguay","Peru",
    "Philippines","Pitcairn Islands","Poland","Portugal","Puerto Rico","Qatar",
    "Reunion","Romania","Russia","Rwanda","Sqo Tome and Principe","Saint Helena",
    "Saint Kitts and Nevis","Saint Lucia","Saint Pierre and Miquelon",
    "Saint Vincent and the Grenadines","Samoa","San Marino","Saudi Arabia","Senegal",
    "Seychelles","Sierra Leone","Singapore","Slovakia","Slovenia","Solomon Islands",
    "Somalia","South Africa","South Georgia and the South Sandwich Islands","South Korea",
    "Spain","Sri Lanka","Sudan","Suriname","Svalbard and Jan Mayen","Swaziland","Sweden",
    "Switzerland","Syria","Taiwan","Tajikistan","Tanzania","Thailand","The Bahamas",
    "The Gambia","Togo","Tokelau","Tonga","Trinidad and Tobago","Tunisia","Turkey",
    "Turkmenistan","Turks and Caicos Islands","Tuvalu","Virgin Islands","Uganda",
    "Ukraine","United Arab Emirates","United Kingdom",
    "United States","United States Minor Outlying Islands","Uruguay","Uzbekistan",
    "Vanuatu","Vatican City","Venezuela","Vietnam","Wallis and Futuna","Western Sahara",
    "Yemen","Yugoslavia","Zambia","Zimbabwe"
  };
```

Il s’agit du tableau de chaînes qui seront placés dans le [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/).

Exécutez l'application. Vous pouvez faire défiler la liste, ou tapez pour filtrer, puis cliquez sur un élément pour afficher un message. Vous devez voir quelque chose de similaire à :

[![Capture d’écran de ListView avec des noms de pays](images/01-listview-example-sml.png)](images/01-listview-example.png#lightbox)

Notez que n’est pas la meilleure pratique de conception à l’aide d’un tableau de chaînes codées en dur. Un est utilisé dans ce didacticiel par souci de simplicité, pour illustrer le [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
widget. La meilleure pratique consiste à référencer un tableau de chaînes défini par une ressource externe, comme avec un `string-array` ressource dans votre projet **Resources/Values/Strings.xml** fichier. Exemple :

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <string name="app_name">HelloListView</string>
  <string-array name="countries_array">
    <item>Bahrain</item>
    <item>Bangladesh</item>
    <item>Barbados</item>
    <item>Belarus</item>
    <item>Belgium</item>
    <item>Belize</item>
    <item>Benin</item>
  </string-array>
</resources>
```

Pour utiliser ces chaînes de ressource pour le [ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/), remplacer l’original [`ListAdapter`](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/)
ligne avec les éléments suivants :

```csharp
string[] countries = Resources.GetStringArray (Resource.Array.countries_array);
ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);
```
Exécutez l'application. Vous devez voir quelque chose de similaire à :

[![Capture d’écran de ListView avec la plus petite liste de noms](images/02-smaller-example-sml.png)](images/02-smaller-example.png#lightbox)


## <a name="going-further-with-listview"></a>Pour aller plus loin avec ListView

Les autres rubriques (via les liens ci-dessous) prennent une vision détaillée de travailler avec la `ListView` classe et les différents types de types de cartes que vous pouvez utiliser avec lui. La structure est la suivante :

-   **Apparence visuelle** &ndash; parties de la `ListView` contrôle et leur fonctionnement.

-   **Classes** &ndash; vue d’ensemble des classes utilisées pour afficher un `ListView`.

-   **Affichage des données dans un ListView** &ndash; comment afficher une liste simple de données, comment implémenter `ListView's` facilité d’utilisation ; comment utiliser ligne intégré différentes dispositions ; et comment adaptateurs économiser de la mémoire en réutilisant les affichages de ligne.

-   **Apparence personnalisée** &ndash; modifier le style de la `ListView` avec des dispositions personnalisées, polices et couleurs.

-   **À l’aide de SQLite** &ndash; comment afficher des données à partir d’une base de données SQLite avec une `CursorAdapter`.

-   **Cycle de vie des activités** &ndash; considérations conceptuelles lors de l’implémentation `ListView` activités, y compris où dans le cycle de vie, vous devez remplir vos données et le moment libérer les ressources.

La discussion (divisée en six parties) commence par une vue d’ensemble de la `ListView` classe lui-même avant d’introduire des exemples plus complexes de son utilisation.

-   [Parties de ListView et fonctionnalité](~/android/user-interface/layouts/list-view/parts-and-functionality.md)
-   [Remplissage d’un ListView avec des données](~/android/user-interface/layouts/list-view/populating.md)
-   [Personnalisation de l’apparence d’une ListView](~/android/user-interface/layouts/list-view/customizing-appearance.md)
-   [Utilisation de CursorAdapters](~/android/user-interface/layouts/list-view/cursor-adapters.md)
-   [Utilisation d’un ContentProvider](~/android/user-interface/layouts/list-view/content-provider.md)
-   [ListView et le cycle de vie des activités](~/android/user-interface/layouts/list-view/activity-lifecycle.md)


## <a name="summary"></a>Récapitulatif

Cet ensemble de rubriques introduit `ListView` et fourni quelques exemples montrant comment utiliser les fonctionnalités intégrées de la `ListActivity`. Il décrit les implémentations personnalisées de `ListView` qu’autorisée pour les dispositions en couleur et à l’aide d’une base de données SQLite et abordé brièvement la pertinence de l’activité du cycle de vie votre `ListView` implémentation.


## <a name="related-links"></a>Liens associés

- [AccessoryViews (exemple)](https://developer.xamarin.com/samples/monodroid/AccessoryViews/)
- [BasicTableAndroid (sample)](https://developer.xamarin.com/samples/monodroid/BasicTableAndroid/)
- [BasicTableAdapter (sample)](https://developer.xamarin.com/samples/monodroid/BasicTableAdapter/)
- [BuiltInViews (sample)](https://developer.xamarin.com/samples/monodroid/BuiltInViews/)
- [CustomRowView (exemple)](https://developer.xamarin.com/samples/monodroid/CustomRowView/)
- [FastScroll (sample)](https://developer.xamarin.com/samples/monodroid/FastScroll/)
- [SectionIndex (sample)](https://developer.xamarin.com/samples/monodroid/SectionIndex/)
- [SimpleCursorTableAdapter (sample)](https://developer.xamarin.com/samples/monodroid/SimpleCursorTableAdapter/)
- [CursorTableAdapter (sample)](https://developer.xamarin.com/samples/monodroid/CursorTableAdapter/)
- [Didacticiel de cycle de vie de l’activité](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Utilisation des Tables et des cellules (Xamarin.iOS)](~/ios/user-interface/controls/tables/index.md)
- [Référence de classe de ListView](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
- [Référence de classe de ListActivity](https://developer.xamarin.com/api/type/Android.App.ListActivity/)
- [Référence de classe de BaseAdapter](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)
- [Référence de classe de ArrayAdapter](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/)
- [Référence de classe CursorAdapter](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/)
