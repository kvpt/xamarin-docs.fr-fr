---
title: Utilisation de ListView dans Xamarin. Android
description: ListView est un composant d’interface utilisateur important des applications Android. elle est utilisée partout dans des listes courtes d’options de menu jusqu’à de longues listes de contacts ou de favoris Internet. Il fournit un moyen simple de présenter une liste déroulante de lignes qui peuvent être mises en forme avec un style intégré ou personnalisées de manière intensive.
ms.prod: xamarin
ms.assetid: C2BA2705-9B20-01C2-468D-860BDFEDC157
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: f6579e3b70e3788046916db12e201550e7fd5f16
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028893"
---
# <a name="xamarinandroid-listview"></a>Xamarin. Android ListView

_ListView est un composant d’interface utilisateur important des applications Android. elle est utilisée partout dans des listes courtes d’options de menu jusqu’à de longues listes de contacts ou de favoris Internet. Il fournit un moyen simple de présenter une liste déroulante de lignes qui peuvent être mises en forme avec un style intégré ou personnalisées de manière intensive._

## <a name="overview"></a>Vue d'ensemble

Les affichages de liste et les adaptateurs sont inclus dans les blocs de construction les plus fondamentaux des applications Android. La classe `ListView` offre un moyen flexible de présenter des données, qu’il s’agisse d’un menu abrégé ou d’une liste de défilement longue. Il offre des fonctionnalités d’utilisation telles que le défilement rapide, les index et la sélection unique ou multiple pour vous aider à créer des interfaces utilisateur compatibles avec les appareils mobiles pour vos applications. Une instance de `ListView` requiert un *Adapter* qui le remplit avec des données contenues dans les affichages de ligne.

Ce guide explique comment implémenter `ListView` et les différentes classes `Adapter` dans Xamarin. Android. Il montre également comment personnaliser l’apparence d’un `ListView`, et il aborde l’importance de la réutilisation des lignes pour réduire la consommation de mémoire. Il y a également une explication sur la façon dont le cycle de vie de l’activité affecte `ListView` et `Adapter` utilisation. Si vous travaillez sur des applications multiplateformes avec Xamarin. iOS, le contrôle `ListView` est structurellement similaire à l' `UITableView` iOS (et le `Adapter` Android est semblable au `UITableViewSource`).

Tout d’abord, un bref didacticiel présente l' `ListView` avec un exemple de code de base. Vous trouverez ensuite des liens vers des rubriques plus avancées qui vous aideront à utiliser des `ListView` dans des applications réelles.

> [!NOTE]
> Le widget `RecyclerView` est une version plus avancée et flexible de `ListView`. Étant donné que `RecyclerView` est conçu pour être le successeur de `ListView` (et `GridView`), nous vous recommandons d’utiliser `RecyclerView` plutôt que `ListView` pour le développement de nouvelles applications. Pour plus d’informations, consultez [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).

## <a name="listview-tutorial"></a>Didacticiel de ListView

[`ListView`](xref:Android.Widget.ListView) est un [`ViewGroup`](xref:Android.Views.ViewGroup)
qui crée une liste d’éléments déroulants. Les éléments de la liste sont automatiquement insérés dans la liste à l’aide d’un [`IListAdapter`](xref:Android.Widget.IListAdapter).

Dans ce didacticiel, vous allez créer une liste déroulante de noms de pays qui sont lus à partir d’un tableau de chaînes. Lorsqu’un élément de liste est sélectionné, un message Toast affiche la position de l’élément dans la liste.

Démarrez un nouveau projet nommé **HelloListView**.

Créez un fichier XML nommé **list_item. xml** et enregistrez-le dans le dossier **Resources/layout/** . Insérez ce qui suit :

```xml
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:padding="10dp"
    android:textSize="16sp">
</TextView>
```

Ce fichier définit la disposition de chaque élément qui sera placé dans le [`ListView`](xref:Android.Widget.ListView).

Ouvrez `MainActivity.cs` et modifiez la classe pour étendre [`ListActivity`](xref:Android.App.ListActivity) (au lieu de [`Activity`](xref:Android.App.Activity)) :

```csharp
public class MainActivity : ListActivity
{
```

Insérez le code suivant pour la méthode [`OnCreate()`](xref:Android.App.Activity.OnCreate*)) :

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

Notez que cela ne charge pas un fichier de disposition pour l’activité (ce que vous faites généralement avec [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*))).
Au lieu de cela, la définition de la [`ListAdapter`](xref:Android.App.ListActivity.ListAdapter)
la propriété ajoute automatiquement un [`ListView`](xref:Android.Widget.ListView)
pour remplir l’écran entier du [`ListActivity`](xref:Android.App.ListActivity).
Cette méthode prend un [`ArrayAdapter<T>`](xref:Android.Widget.ArrayAdapter`1), qui gère le tableau d’éléments de liste qui sera placé dans le [`ListView`](xref:Android.Widget.ListView).
[`ArrayAdapter<T>`](xref:Android.Widget.ArrayAdapter`1)
le constructeur prend le [`Context`](xref:Android.Content.Context)d’application, la description de la disposition pour chaque élément de liste (créé à l’étape précédente) et un `T[]` ou [`Java.Util.IList<T>`](xref:Java.Util.IList)
Tableau d’objets à insérer dans le [`ListView`](xref:Android.Widget.ListView)
(suivant).

[`TextFilterEnabled`](xref:Android.Widget.AbsListView.TextFilterEnabled)
la propriété active le filtrage du texte pour le [`ListView`](xref:Android.Widget.ListView), de sorte que lorsque l’utilisateur commence à taper, la liste est filtrée.

[`ItemClick`](xref:Android.Widget.AdapterView.ItemClick)
l’événement peut être utilisé pour s’abonner à des gestionnaires de clics. Lorsqu’un élément de l' [`ListView`](xref:Android.Widget.ListView)
l’utilisateur a cliqué dessus, le gestionnaire est appelé et un [`Toast`](xref:Android.Widget.Toast)
le message s’affiche, à l’aide du texte de l’élément sur lequel vous avez cliqué.

Vous pouvez utiliser des conceptions d’éléments de liste fournies par la plateforme au lieu de définir votre propre fichier de disposition pour le [`ListAdapter`](xref:Android.App.ListActivity.ListAdapter).
Par exemple, essayez d’utiliser `Android.Resource.Layout.SimpleListItem1` au lieu de `Resource.Layout.list_item`.

Ajoutez l’instruction `using` suivante :

```csharp
using System;
```

Ensuite, ajoutez le tableau de chaînes suivant en tant que membre de `MainActivity`:

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

Il s’agit du tableau de chaînes qui sera placé dans le [`ListView`](xref:Android.Widget.ListView).

Exécutez l'application. Vous pouvez faire défiler la liste ou taper pour la filtrer, puis cliquer sur un élément pour afficher un message. Vous devez voir quelque chose de similaire à :

[![exemple de capture d’écran de ListView avec les noms de pays](images/01-listview-example-sml.png)](images/01-listview-example.png#lightbox)

Notez que l’utilisation d’un tableau de chaînes codées en dur n’est pas la meilleure pratique de conception. L’un est utilisé dans ce didacticiel pour des raisons de simplicité, afin d’illustrer les [`ListView`](xref:Android.Widget.ListView)
sélection. La meilleure pratique consiste à référencer un tableau de chaînes défini par une ressource externe, par exemple avec une ressource `string-array` dans le fichier **Resources/values/Strings. xml** du projet. Exemple :

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

Pour utiliser ces chaînes de ressources pour le [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter`1), remplacez le [`ListAdapter`](xref:Android.App.ListActivity.ListAdapter) d’origine
ligne avec les éléments suivants :

```csharp
string[] countries = Resources.GetStringArray (Resource.Array.countries_array);
ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);
```

Exécutez l'application. Vous devez voir quelque chose de similaire à :

[![exemple de capture d’écran de ListView avec une liste de noms plus petite](images/02-smaller-example-sml.png)](images/02-smaller-example.png#lightbox)

## <a name="going-further-with-listview"></a>Aller plus loin avec ListView

Les rubriques restantes (liées ci-dessous) présentent une vue complète de l’utilisation de la classe `ListView` et des différents types de types d’adaptateurs que vous pouvez utiliser avec celle-ci. La structure est la suivante :

- L' **apparence visuelle** &ndash; parties du contrôle `ListView` et leur fonctionnement.

- Les **classes** &ndash; vue d’ensemble des classes utilisées pour afficher une `ListView`.

- **Affichage des données dans un ListView** &ndash; comment afficher une simple liste de données ; comment implémenter des fonctionnalités d’utilisation de `ListView's` ; Comment utiliser différentes dispositions de ligne intégrées ; et comment les adaptateurs enregistrent la mémoire en réutilisant les vues de lignes.

- **Apparence personnalisée** &ndash; la modification du style du `ListView` avec des dispositions personnalisées, des polices et des couleurs.

- **L’utilisation de SQLite** &ndash; comment afficher des données d’une base de données SQLite avec un `CursorAdapter`.

- **Cycle de vie des activités** &ndash; considérations relatives à la conception lors de l’implémentation d' `ListView` activités, y compris où dans le cycle de vie vous devez remplir vos données et quand libérer des ressources.

La discussion (divisée en six parties) commence par une vue d’ensemble de la `ListView` classe elle-même avant d’introduire des exemples progressivement plus complexes de son utilisation.

- [Parties de ListView et fonctionnalité](~/android/user-interface/layouts/list-view/parts-and-functionality.md)
- [Remplissage d’un ListView avec des données](~/android/user-interface/layouts/list-view/populating.md)
- [Personnalisation de l’apparence d’une ListView](~/android/user-interface/layouts/list-view/customizing-appearance.md)
- [Utilisation de CursorAdapters](~/android/user-interface/layouts/list-view/cursor-adapters.md)
- [Utilisation d’un ContentProvider](~/android/user-interface/layouts/list-view/content-provider.md)
- [ListView et le cycle de vie des activités](~/android/user-interface/layouts/list-view/activity-lifecycle.md)

## <a name="summary"></a>Récapitulatif

Cet ensemble de rubriques a présenté `ListView` et fourni des exemples d’utilisation des fonctionnalités intégrées du `ListActivity`. Il a abordé les implémentations personnalisées de `ListView` qui permettaient des mises en page colorées et l’utilisation d’une base de données SQLite, et il a rapidement touché la pertinence du cycle de vie des activités sur votre implémentation de `ListView`.

## <a name="related-links"></a>Liens associés

- [AccessoryViews (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/accessoryviews)
- [BasicTableAndroid (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/basictableandroid)
- [BasicTableAdapter (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/basictableadapter)
- [BuiltInViews (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/builtinviews)
- [CustomRowView (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/customrowview)
- [FastScroll (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fastscroll)
- [SectionIndex (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sectionindex)
- [SimpleCursorTableAdapter (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/simplecursortableadapter)
- [CursorTableAdapter (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/cursortableadapter)
- [Didacticiel sur le cycle de vie des activités](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Utilisation des tables et des cellules (dans Xamarin. iOS)](~/ios/user-interface/controls/tables/index.md)
- [Référence de classe ListView](xref:Android.Widget.ListView)
- [Référence de la classe ListActivity](xref:Android.App.ListActivity)
- [Référence de la classe BaseAdapter](xref:Android.Widget.BaseAdapter)
- [Référence de la classe ArrayAdapter](xref:Android.Widget.ArrayAdapter)
- [Informations de référence sur la classe CursorAdapter](xref:Android.Widget.CursorAdapter)
