---
title: Calendrier Xamarin. Android
ms.prod: xamarin
ms.assetid: 78666541-CA14-4CD8-A94A-A9621C57813E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 3d74e2db541e1f30c7626cd1b08228c1e8f57a42
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029315"
---
# <a name="xamarinandroid-calendar"></a>Calendrier Xamarin. Android

## <a name="calendar-api"></a>API de calendrier

Un nouvel ensemble d’API de calendrier introduites dans Android 4 prend en charge les applications conçues pour lire ou écrire des données dans le fournisseur de calendrier. Ces API prennent en charge une multitude d’options d’interaction avec les données de calendrier, notamment la possibilité de lire et d’écrire des événements, des participants et des rappels. En utilisant le fournisseur de calendrier dans votre application, les données que vous ajoutez à l’aide de l’API s’affichent dans l’application de calendrier intégrée fournie avec Android 4.

## <a name="adding-permissions"></a>Ajout d’autorisations

Lorsque vous travaillez avec les nouvelles API de calendrier de votre application, la première chose à faire est d’ajouter les autorisations appropriées au manifeste Android. Les autorisations que vous devez ajouter sont `android.permisson.READ_CALENDAR` et `android.permission.WRITE_CALENDAR`, selon que vous lisez ou écrivez des données de calendrier.

## <a name="using-the-calendar-contract"></a>Utilisation du contrat de calendrier

Une fois que vous avez défini les autorisations, vous pouvez interagir avec les données de calendrier à l’aide de la classe `CalendarContract`. Cette classe fournit un modèle de données que les applications peuvent utiliser lorsqu’elles interagissent avec le fournisseur de calendrier. Le `CalendarContract` permet aux applications de résoudre les URI en entités de calendrier, telles que les calendriers et les événements. Il offre également un moyen d’interagir avec différents champs de chaque entité, tels que le nom et l’ID d’un calendrier, ou la date de début et de fin d’un événement.

Examinons un exemple qui utilise l’API de calendrier. Dans cet exemple, nous allons examiner comment énumérer les calendriers et leurs événements, et comment ajouter un nouvel événement à un calendrier.

## <a name="listing-calendars"></a>Liste des calendriers

Tout d’abord, examinons comment énumérer les calendriers qui ont été inscrits dans l’application Calendrier. Pour ce faire, nous pouvons instancier un `CursorLoader`. Introduite dans Android 3,0 (API 11), `CursorLoader` est la meilleure façon de consommer une `ContentProvider`. Au minimum, nous devrons spécifier l’URI de contenu pour les calendriers et les colonnes que vous souhaitez retourner. Cette spécification de colonne est connue sous le nom de _projection_.

L’appel de la méthode `CursorLoader.LoadInBackground` permet d’interroger un fournisseur de contenu à la recherche de données, telles que le fournisseur de calendrier.
`LoadInBackground` effectue l’opération de chargement réelle et retourne un `Cursor` avec les résultats de la requête.

Le `CalendarContract` nous aide à spécifier à la fois le contenu `Uri` et la projection. Pour obtenir le contenu `Uri` pour l’interrogation des calendriers, nous pouvons simplement utiliser la propriété `CalendarContract.Calendars.ContentUri` comme suit :

```csharp
var calendarsUri = CalendarContract.Calendars.ContentUri;
```

L’utilisation de la `CalendarContract` pour spécifier les colonnes de calendrier que nous souhaitons utiliser est tout aussi simple. Nous ajoutons simplement des champs dans la classe `CalendarContract.Calendars.InterfaceConsts` à un tableau. Par exemple, le code suivant comprend l’ID du calendrier, le nom complet et le nom du compte :

```csharp
string[] calendarsProjection = {
    CalendarContract.Calendars.InterfaceConsts.Id,
    CalendarContract.Calendars.InterfaceConsts.CalendarDisplayName,
    CalendarContract.Calendars.InterfaceConsts.AccountName
};
```

La `Id` est importante à inclure si vous utilisez un `SimpleCursorAdapter` pour lier les données à l’interface utilisateur, comme nous le verrons bientôt. Avec l’URI et la projection de contenu en place, nous instancions le `CursorLoader` et appelons la méthode `CursorLoader.LoadInBackground` pour retourner un curseur avec les données de calendrier, comme indiqué ci-dessous :

```csharp
var loader = new CursorLoader(this, calendarsUri, calendarsProjection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();

```

L’interface utilisateur de cet exemple contient un `ListView`, chaque élément de la liste représentant un seul calendrier. Le code XML suivant montre le balisage qui comprend les `ListView`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:orientation="vertical"
android:layout_width="fill_parent"
android:layout_height="fill_parent">
  <ListView
    android:id="@android:id/android:list"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content" />
</LinearLayout>
```

Nous devons également spécifier l’interface utilisateur pour chaque élément de liste, que nous allons placer dans un fichier XML distinct comme suit :

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:orientation="vertical"
android:layout_width="fill_parent"
android:layout_height="wrap_content">
  <TextView android:id="@+id/calDisplayName"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:textSize="16dip" />
  <TextView android:id="@+id/calAccountName"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:textSize="12dip" />
</LinearLayout>
```

À partir de ce point, il s’agit simplement d’un code Android normal pour lier les données du curseur à l’interface utilisateur. Nous allons utiliser un `SimpleCursorAdapter` comme suit :

```csharp
string[] sourceColumns = {
    CalendarContract.Calendars.InterfaceConsts.CalendarDisplayName,
    CalendarContract.Calendars.InterfaceConsts.AccountName };

int[] targetResources = {
    Resource.Id.calDisplayName, Resource.Id.calAccountName };      

SimpleCursorAdapter adapter = new SimpleCursorAdapter (this,
    Resource.Layout.CalListItem, cursor, sourceColumns, targetResources);

ListAdapter = adapter;
```

Dans le code ci-dessus, l’adaptateur prend les colonnes spécifiées dans le tableau `sourceColumns` et les écrit dans les éléments de l’interface utilisateur du tableau `targetResources` pour chaque entrée de calendrier dans le curseur. L’activité utilisée ici est une sous-classe de `ListActivity`; Il comprend la propriété `ListAdapter` sur laquelle nous définissons l’adaptateur.

Voici une capture d’écran montrant le résultat final, avec les informations de calendrier affichées dans le `ListView`:

[![CalendarDemo s’exécutant dans l’émulateur, affichant deux entrées de calendrier](calendar-images/11-calendar.png)](calendar-images/11-calendar.png#lightbox)

## <a name="listing-calendar-events"></a>Liste des événements du calendrier

Voyons ensuite comment énumérer les événements pour un calendrier donné.
En s’appuyant sur l’exemple ci-dessus, nous présenterons une liste d’événements lorsque l’utilisateur sélectionne l’un des calendriers. Par conséquent, nous devrons gérer la sélection de l’élément dans le code précédent :

```csharp
ListView.ItemClick += (sender, e) => {
    int i = (e as ItemEventArgs).Position;

    cursor.MoveToPosition(i);
    int calId =
        cursor.GetInt (cursor.GetColumnIndex (calendarsProjection [0]));

    var showEvents = new Intent(this, typeof(EventListActivity));
    showEvents.PutExtra("calId", calId);
    StartActivity(showEvents);
};
```

Dans ce code, nous créons une intention d’ouvrir une activité de type `EventListActivity`, en transmettant l’ID du calendrier dans l’objectif. Nous aurons besoin de l’ID pour savoir à quel calendrier Rechercher les événements. Dans la méthode `OnCreate` de `EventListActivity`, nous pouvons récupérer l’ID de la `Intent` comme indiqué ci-dessous :

```csharp
_calId = Intent.GetIntExtra ("calId", -1);
```

Nous allons maintenant interroger les événements pour cet ID de calendrier. Le processus d’interrogation des événements est similaire à la façon dont nous avons interrogé une liste de calendriers plus tôt, mais cette fois, nous allons travailler avec la classe `CalendarContract.Events`. Le code suivant crée une requête pour récupérer des événements :

```csharp
var eventsUri = CalendarContract.Events.ContentUri;

string[] eventsProjection = {
    CalendarContract.Events.InterfaceConsts.Id,
    CalendarContract.Events.InterfaceConsts.Title,
    CalendarContract.Events.InterfaceConsts.Dtstart
};

var loader = new CursorLoader(this, eventsUri, eventsProjection,
                   String.Format ("calendar_id={0}", _calId), null, "dtstart ASC");
var cursor = (ICursor)loader.LoadInBackground();
```

Dans ce code, nous obtenons d’abord le contenu `Uri` pour les événements de la propriété `CalendarContract.Events.ContentUri`. Ensuite, nous spécifions les colonnes d’événements que vous souhaitez récupérer dans le tableau eventsProjection.
Enfin, nous instancions un `CursorLoader` avec ces informations et appelons la méthode `LoadInBackground` du chargeur pour retourner un `Cursor` avec les données d’événement.

Pour afficher les données d’événement dans l’interface utilisateur, nous pouvons utiliser le balisage et le code comme nous l’avons fait avant d’afficher la liste des calendriers. Là encore, nous utilisons `SimpleCursorAdapter` pour lier les données à un `ListView` comme indiqué dans le code suivant :

```csharp
string[] sourceColumns = {
    CalendarContract.Events.InterfaceConsts.Title,
    CalendarContract.Events.InterfaceConsts.Dtstart };

int[] targetResources = {
    Resource.Id.eventTitle,
    Resource.Id.eventStartDate };

var adapter = new SimpleCursorAdapter (this, Resource.Layout.EventListItem,
    cursor, sourceColumns, targetResources);

adapter.ViewBinder = new ViewBinder ();       
ListAdapter = adapter;
```

La principale différence entre ce code et le code que nous avons utilisé précédemment pour afficher la liste de calendrier est l’utilisation d’un `ViewBinder`, qui est défini sur la ligne :

```csharp
adapter.ViewBinder = new ViewBinder ();
```

La classe `ViewBinder` permet de mieux contrôler la façon dont nous lions les valeurs aux vues. Dans ce cas, nous l’utilisons pour convertir l’heure de début de l’événement entre les millisecondes et une chaîne de date, comme le montre l’implémentation suivante :

```csharp
class ViewBinder : Java.Lang.Object, SimpleCursorAdapter.IViewBinder
{    
    public bool SetViewValue (View view, Android.Database.ICursor cursor,
        int columnIndex)
    {
        if (columnIndex == 2) {
            long ms = cursor.GetLong (columnIndex);

            DateTime date = new DateTime (1970, 1, 1, 0, 0, 0,
                DateTimeKind.Utc).AddMilliseconds (ms).ToLocalTime ();

            TextView textView = (TextView)view;
            textView.Text = date.ToLongDateString ();

            return true;
        }
        return false;
    }    
}
```

Une liste d’événements s’affiche, comme indiqué ci-dessous :

[Capture d’écran ![de l’exemple d’application affichant trois événements de calendrier](calendar-images/12-events.png)](calendar-images/12-events.png#lightbox)

## <a name="adding-a-calendar-event"></a>Ajout d’un événement de calendrier

Nous avons vu comment lire les données de calendrier. Voyons maintenant comment ajouter un événement à un calendrier. Pour que cela fonctionne, veillez à inclure l’autorisation `android.permission.WRITE_CALENDAR` que nous avons mentionnée précédemment. Pour ajouter un événement à un calendrier, nous allons :

1. Créez une instance de `ContentValues`.
1. Utilisez les clés de la classe `CalendarContract.Events.InterfaceConsts` pour remplir l’instance `ContentValues`.
1. Définissez les fuseaux horaires pour les heures de début et de fin de l’événement.
1. Utilisez un `ContentResolver` pour insérer les données d’événement dans le calendrier.

Le code ci-dessous illustre ces étapes :

```csharp
ContentValues eventValues = new ContentValues ();

eventValues.Put (CalendarContract.Events.InterfaceConsts.CalendarId,
    _calId);
eventValues.Put (CalendarContract.Events.InterfaceConsts.Title,
    "Test Event from M4A");
eventValues.Put (CalendarContract.Events.InterfaceConsts.Description,
    "This is an event created from Xamarin.Android");
eventValues.Put (CalendarContract.Events.InterfaceConsts.Dtstart,
    GetDateTimeMS (2011, 12, 15, 10, 0));
eventValues.Put (CalendarContract.Events.InterfaceConsts.Dtend,
    GetDateTimeMS (2011, 12, 15, 11, 0));

eventValues.Put(CalendarContract.Events.InterfaceConsts.EventTimezone,
    "UTC");
eventValues.Put(CalendarContract.Events.InterfaceConsts.EventEndTimezone,
    "UTC");

var uri = ContentResolver.Insert (CalendarContract.Events.ContentUri,
    eventValues);
```

Notez que si nous ne définissons pas le fuseau horaire, une exception de type `Java.Lang.IllegalArgumentException` sera levée. Étant donné que les valeurs d’heure de l’événement doivent être exprimées en millisecondes depuis l’époque, nous créons une méthode `GetDateTimeMS` (dans `EventListActivity`) pour convertir nos spécifications de date au format « milliseconde » :

```csharp
long GetDateTimeMS (int yr, int month, int day, int hr, int min)
{
    Calendar c = Calendar.GetInstance (Java.Util.TimeZone.Default);

    c.Set (Java.Util.CalendarField.DayOfMonth, 15);
    c.Set (Java.Util.CalendarField.HourOfDay, hr);
    c.Set (Java.Util.CalendarField.Minute, min);
    c.Set (Java.Util.CalendarField.Month, Calendar.December);
    c.Set (Java.Util.CalendarField.Year, 2011);

    return c.TimeInMillis;
}
```

Si nous ajoutons un bouton à l’interface utilisateur de la liste d’événements et que vous exécutez le code ci-dessus dans le gestionnaire d’événements Click du bouton, l’événement est ajouté au calendrier et mis à jour dans notre liste comme indiqué ci-dessous :

[![capture d’écran de l’exemple d’application avec les événements de calendrier suivis du bouton Ajouter un exemple d’événement](calendar-images/13.png)](calendar-images/13.png#lightbox)

Si nous allons ouvrir l’application de calendrier, nous voyons que l’événement y est également écrit :

[Capture d’écran ![de l’application de calendrier affichant l’événement de calendrier sélectionné](calendar-images/14.png)](calendar-images/14.png#lightbox)

Comme vous pouvez le voir, Android offre un accès puissant et facile pour récupérer et conserver des données de calendrier, ce qui permet aux applications d’intégrer en toute transparence les fonctionnalités de calendrier.

## <a name="related-links"></a>Liens associés

- [Démonstration du calendrier (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/calendardemo)
- [Présentation du sandwich glacé](https://www.android.com/about/ice-cream-sandwich/)
- [Plateforme Android 4,0](https://developer.android.com/sdk/android-4.0.html)
