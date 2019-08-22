---
title: Utilisation de ListView dans Xamarin. Android
description: ListView est un composant d’interface utilisateur important des applications Android. elle est utilisée partout dans des listes courtes d’options de menu jusqu’à de longues listes de contacts ou de favoris Internet. Il fournit un moyen simple de présenter une liste déroulante de lignes qui peuvent être mises en forme avec un style intégré ou personnalisées de manière intensive.
ms.prod: xamarin
ms.assetid: C2BA2705-9B20-01C2-468D-860BDFEDC157
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: c40d21a95db564d0fdcbea5772a88e94446c07ce
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887525"
---
# <a name="xamarinandroid-listview"></a>Xamarin. Android ListView

_ListView est un composant d’interface utilisateur important des applications Android. elle est utilisée partout dans des listes courtes d’options de menu jusqu’à de longues listes de contacts ou de favoris Internet. Il fournit un moyen simple de présenter une liste déroulante de lignes qui peuvent être mises en forme avec un style intégré ou personnalisées de manière intensive._

## <a name="overview"></a>Présentation

Les affichages de liste et les adaptateurs sont inclus dans les blocs de construction les plus fondamentaux des applications Android. La `ListView` classe offre un moyen flexible de présenter des données, qu’il s’agisse d’un menu abrégé ou d’une liste de défilement longue. Il offre des fonctionnalités d’utilisation telles que le défilement rapide, les index et la sélection unique ou multiple pour vous aider à créer des interfaces utilisateur compatibles avec les appareils mobiles pour vos applications. Une instance de `ListView` requiert un *Adapter* qui le remplit avec des données contenues dans les affichages de ligne.

Ce guide explique comment implémenter `ListView` et les différentes `Adapter` classes dans Xamarin. Android. Il montre également comment personnaliser l’apparence d’un `ListView`et décrit l’importance de la réutilisation de ligne pour réduire la consommation de mémoire. Il y a également une explication de l’impact `ListView` et `Adapter` de l’utilisation du cycle de vie de l’activité. Si vous travaillez sur des applications multiplateformes avec Xamarin. iOS, le `ListView` contrôle est structurellement similaire à IOS `UITableView` (et `UITableViewSource`Android `Adapter` est semblable à).

Tout d’abord, un bref didacticiel `ListView` présente le avec un exemple de code de base. Vous trouverez ensuite des liens vers des rubriques plus avancées qui vous aideront à utiliser `ListView` dans des applications réelles.

> [!NOTE]
> Le `RecyclerView` widget est une version plus avancée et flexible de `ListView`. Étant `RecyclerView` donné que est conçu pour être le `ListView` successeur de `GridView`(et), nous vous recommandons `RecyclerView` d’utiliser `ListView` plutôt que pour le nouveau développement d’applications. Pour plus d’informations, consultez [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).

## <a name="listview-tutorial"></a>Didacticiel de ListView

[`ListView`](xref:Android.Widget.ListView)est un[`ViewGroup`](xref:Android.Views.ViewGroup)
qui crée une liste d’éléments déroulants. Les éléments de liste sont automatiquement insérés dans la liste [`IListAdapter`](xref:Android.Widget.IListAdapter)à l’aide d’un.

Dans ce didacticiel, vous allez créer une liste déroulante de noms de pays qui sont lus à partir d’un tableau de chaînes. Lorsqu’un élément de liste est sélectionné, un message Toast affiche la position de l’élément dans la liste.

Démarrez un nouveau projet nommé **HelloListView**.

Créez un fichier XML nommé **list_item. xml** et enregistrez-le dans le dossier Resources **/Layout/** . Insérez ce qui suit:

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

Ouvrez `MainActivity.cs` et modifiez la classe pour étendre [`ListActivity`](xref:Android.App.ListActivity) (au lieu [`Activity`](xref:Android.App.Activity)de):

```csharp
public class MainActivity : ListActivity
{
```

Insérez le code suivant pour la [`OnCreate()`](xref:Android.App.Activity.OnCreate*)méthode):

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

Notez que cela ne charge pas un fichier de disposition pour l’activité (ce que vous faites [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)généralement avec)).
Au lieu de cela, la définition de[`ListAdapter`](xref:Android.App.ListActivity.ListAdapter)
la propriété ajoute automatiquement un[`ListView`](xref:Android.Widget.ListView)
pour remplir l’écran entier du [`ListActivity`](xref:Android.App.ListActivity).
Cette méthode prend un [`ArrayAdapter<T>`](xref:Android.Widget.ArrayAdapter`1), qui gère le tableau d’éléments de liste qui sera placé dans le [`ListView`](xref:Android.Widget.ListView).
La[`ArrayAdapter<T>`](xref:Android.Widget.ArrayAdapter`1)
le constructeur prend l' [`Context`](xref:Android.Content.Context)application, la description de la disposition pour chaque élément de liste (créé à l’étape précédente `T[]` ) et un ou[`Java.Util.IList<T>`](xref:Java.Util.IList)
Tableau d’objets à insérer dans[`ListView`](xref:Android.Widget.ListView)
(suivant).

La[`TextFilterEnabled`](xref:Android.Widget.AbsListView.TextFilterEnabled)
la propriété active le filtrage du texte [`ListView`](xref:Android.Widget.ListView)pour le, de sorte que lorsque l’utilisateur commence à taper, la liste est filtrée.

La[`ItemClick`](xref:Android.Widget.AdapterView.ItemClick)
l’événement peut être utilisé pour s’abonner à des gestionnaires de clics. Lorsqu’un élément de la[`ListView`](xref:Android.Widget.ListView)
l’utilisateur a cliqué dessus, le gestionnaire est appelé et un[`Toast`](xref:Android.Widget.Toast)
le message s’affiche, à l’aide du texte de l’élément sur lequel vous avez cliqué.

Vous pouvez utiliser des conceptions d’éléments de liste fournies par la plateforme au lieu de définir votre propre [`ListAdapter`](xref:Android.App.ListActivity.ListAdapter)fichier de disposition pour le.
Par exemple, essayez d' `Android.Resource.Layout.SimpleListItem1` utiliser à `Resource.Layout.list_item`la place de.

Ajoutez l'instruction `using` suivante :

```csharp
using System;
```

Ensuite, ajoutez le tableau de chaînes suivant en tant que `MainActivity`membre de:

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

Exécutez l'application. Vous pouvez faire défiler la liste ou taper pour la filtrer, puis cliquer sur un élément pour afficher un message. Le résultat suivant devrait s'afficher :

[![Exemple de capture d’écran de ListView avec les noms de pays](images/01-listview-example-sml.png)](images/01-listview-example.png#lightbox)

Notez que l’utilisation d’un tableau de chaînes codées en dur n’est pas la meilleure pratique de conception. L’un est utilisé dans ce didacticiel pour des raisons de simplicité, afin d’illustrer le[`ListView`](xref:Android.Widget.ListView)
sélection. La meilleure pratique consiste à référencer un tableau de chaînes défini par une ressource externe, par exemple `string-array` avec une ressource dans le fichier Resources **/values/Strings. xml** du projet. Par exemple :

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

Pour utiliser ces chaînes de ressources pour [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter`1)le, remplacez l’original[`ListAdapter`](xref:Android.App.ListActivity.ListAdapter)
ligne avec les éléments suivants:

```csharp
string[] countries = Resources.GetStringArray (Resource.Array.countries_array);
ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);
```

Exécutez l'application. Le résultat suivant devrait s'afficher :

[![Exemple de capture d’écran de ListView avec une liste de noms plus petite](images/02-smaller-example-sml.png)](images/02-smaller-example.png#lightbox)

## <a name="going-further-with-listview"></a>Aller plus loin avec ListView

Les rubriques restantes (liées ci-dessous) présentent une vue d' `ListView` ensemble complète de l’utilisation de la classe et des différents types de types d’adaptateurs que vous pouvez utiliser avec celle-ci. La structure est la suivante :

- **Apparence visuelle** &ndash; Les`ListView` parties du contrôle et leur fonctionnement.

- **Classes** Vue d’ensemble des classes utilisées pour afficher `ListView`un. &ndash;

- **Affichage des données dans un ListView** Comment afficher une simple liste de données, comment implémenter `ListView's` des fonctionnalités d’utilisation, comment utiliser différentes dispositions de ligne intégrées et comment les adaptateurs enregistrent la mémoire en réutilisant les vues de lignes. &ndash;

- **Apparence personnalisée** Modification du style `ListView` de avec des dispositions personnalisées, des polices et des couleurs. &ndash;

- **Utilisation de SQLite** Comment afficher les données d’une base de données SQLite `CursorAdapter`avec un. &ndash;

- **Cycle de vie des activités** Considérations relatives à la `ListView` conception lors de l’implémentation d’activités, y compris où dans le cycle de vie vous devez remplir vos données et quand libérer des ressources. &ndash;

La discussion (divisée en six parties) commence par une vue d' `ListView` ensemble de la classe elle-même avant d’introduire des exemples plus complexes de l’utilisation de la classe.

- [Parties de ListView et fonctionnalité](~/android/user-interface/layouts/list-view/parts-and-functionality.md)
- [Remplissage d’un ListView avec des données](~/android/user-interface/layouts/list-view/populating.md)
- [Personnalisation de l’apparence d’une ListView](~/android/user-interface/layouts/list-view/customizing-appearance.md)
- [Utilisation de CursorAdapters](~/android/user-interface/layouts/list-view/cursor-adapters.md)
- [Utilisation d’un ContentProvider](~/android/user-interface/layouts/list-view/content-provider.md)
- [ListView et le cycle de vie des activités](~/android/user-interface/layouts/list-view/activity-lifecycle.md)


## <a name="summary"></a>Récapitulatif

Cet ensemble de rubriques a `ListView` présenté et fourni des exemples d’utilisation des fonctionnalités intégrées `ListActivity`de. Il a abordé les implémentations `ListView` personnalisées de qui étaient autorisées pour les dispositions colorées et l’utilisation d’une base de données SQLite, et elle a rapidement `ListView` touché la pertinence du cycle de vie de l’activité sur votre implémentation.


## <a name="related-links"></a>Liens associés

- [AccessoryViews (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/accessoryviews)
- [BasicTableAndroid (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/basictableandroid)
- [BasicTableAdapter (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/basictableadapter)
- [BuiltInViews (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/builtinviews)
- [CustomRowView (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/customrowview)
- [FastScroll (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fastscroll)
- [SectionIndex (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sectionindex)
- [SimpleCursorTableAdapter (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/simplecursortableadapter)
- [CursorTableAdapter (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/cursortableadapter)
- [Didacticiel sur le cycle de vie des activités](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Utilisation des tables et des cellules (dans Xamarin. iOS)](~/ios/user-interface/controls/tables/index.md)
- [Référence de classe ListView](xref:Android.Widget.ListView)
- [Référence de la classe ListActivity](xref:Android.App.ListActivity)
- [Référence de la classe BaseAdapter](xref:Android.Widget.BaseAdapter)
- [Référence de la classe ArrayAdapter](xref:Android.Widget.ArrayAdapter)
- [Informations de référence sur la classe CursorAdapter](xref:Android.Widget.CursorAdapter)
