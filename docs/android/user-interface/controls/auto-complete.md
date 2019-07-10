---
title: Saisie semi-automatique
ms.prod: xamarin
ms.assetid: D4C8CA49-8369-35B7-798D-B147FDC24185
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/31/2018
ms.openlocfilehash: bcbab6deddf5e1a4782cd382623f22281a0823e6
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674850"
---
# <a name="auto-complete"></a>Saisie semi-automatique

`AutoCompleteTextView` est un élément d’affichage de texte modifiable qui affiche des suggestions de saisie semi-automatique automatiquement pendant que l’utilisateur tape. La liste de suggestions s’affiche dans la liste déroulante à partir de laquelle l’utilisateur peut choisir un élément à remplacer le contenu de la zone d’édition avec.

![Exemple de saisie semi-automatique](images/auto-complete.png)

## <a name="overview"></a>Vue d'ensemble

Pour créer un widget de saisie de texte qui fournit des suggestions de saisie semi-automatique, utilisez le [`AutoCompleteTextView`](https://developer.xamarin.com/api/type/Android.Widget.AutoCompleteTextView/)
widget. Suggestions sont reçues à partir d’une collection de chaînes associées avec le widget via un [ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/).

Dans ce didacticiel, vous allez créer un [`AutoCompleteTextView`](https://developer.xamarin.com/api/type/Android.Widget.AutoCompleteTextView/)
Widget qui fournit des suggestions pour un nom de pays.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:padding="5dp">
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Country" />
    <AutoCompleteTextView android:id="@+id/autocomplete_country"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"/>
</LinearLayout>
```

Le [ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/) est une étiquette qui présente la [`AutoCompleteTextView`](https://developer.xamarin.com/api/type/Android.Widget.AutoCompleteTextView/)
widget.


## <a name="tutorial"></a>Didacticiel

Démarrer un nouveau projet nommé *HelloAutoComplete*.

Créer un fichier XML nommé `list_item.xml` et enregistrez-le à l’intérieur de la **ressources/disposition** dossier. Définir l’Action de génération de ce fichier à `AndroidResource`. Modifiez le fichier ressemble à ceci :

```xml
<?xml version="1.0" encoding="utf-8"?>

<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:padding="10dp"
    android:textSize="16sp"
    android:textColor="#000">
</TextView>
```

Ce fichier définit une simple [ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/) qui sera utilisé pour chaque élément qui apparaît dans la liste de suggestions.

Ouvrez **Resources/Layout/Main.axml** et insérez le code suivant :

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:padding="5dp">
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Country" />
    <AutoCompleteTextView android:id="@+id/autocomplete_country"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"/>
</LinearLayout>
```

Ouvrez **MainActivity.cs** et insérez le code suivant pour le [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle))
méthode :

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "Main" layout resource
    SetContentView (Resource.Layout.Main);

    AutoCompleteTextView textView = FindViewById<AutoCompleteTextView> (Resource.Id.autocomplete_country);
    var adapter = new ArrayAdapter<String> (this, Resource.Layout.list_item, COUNTRIES);

    textView.Adapter = adapter;
}
```

Une fois que l’affichage de contenu est défini sur le `main.xml` mise en page, le [`AutoCompleteTextView`](https://developer.xamarin.com/api/type/Android.Widget.AutoCompleteTextView/)
widget est capturé à partir de la mise en page avec [ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/). Un nouveau [ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/) est ensuite initialisé pour lier le `list_item.xml` mise en page à chaque élément de liste dans le `COUNTRIES` tableau de chaînes (défini à l’étape suivante). Enfin, `SetAdapter()` est appelée pour associer le [ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/) avec le [`AutoCompleteTextView`](https://developer.xamarin.com/api/type/Android.Widget.AutoCompleteTextView/)
widget afin que le tableau de chaînes rempliront la liste de suggestions.

À l’intérieur de la `MainActivity` de classe, ajoutez le tableau de chaînes :

```csharp
static string[] COUNTRIES = new string[] {
  "Afghanistan", "Albania", "Algeria", "American Samoa", "Andorra",
  "Angola", "Anguilla", "Antarctica", "Antigua and Barbuda", "Argentina",
  "Armenia", "Aruba", "Australia", "Austria", "Azerbaijan",
  "Bahrain", "Bangladesh", "Barbados", "Belarus", "Belgium",
  "Belize", "Benin", "Bermuda", "Bhutan", "Bolivia",
  "Bosnia and Herzegovina", "Botswana", "Bouvet Island", "Brazil", "British Indian Ocean Territory",
  "British Virgin Islands", "Brunei", "Bulgaria", "Burkina Faso", "Burundi",
  "Cote d'Ivoire", "Cambodia", "Cameroon", "Canada", "Cape Verde",
  "Cayman Islands", "Central African Republic", "Chad", "Chile", "China",
  "Christmas Island", "Cocos (Keeling) Islands", "Colombia", "Comoros", "Congo",
  "Cook Islands", "Costa Rica", "Croatia", "Cuba", "Cyprus", "Czech Republic",
  "Democratic Republic of the Congo", "Denmark", "Djibouti", "Dominica", "Dominican Republic",
  "East Timor", "Ecuador", "Egypt", "El Salvador", "Equatorial Guinea", "Eritrea",
  "Estonia", "Ethiopia", "Faeroe Islands", "Falkland Islands", "Fiji", "Finland",
  "Former Yugoslav Republic of Macedonia", "France", "French Guiana", "French Polynesia",
  "French Southern Territories", "Gabon", "Georgia", "Germany", "Ghana", "Gibraltar",
  "Greece", "Greenland", "Grenada", "Guadeloupe", "Guam", "Guatemala", "Guinea", "Guinea-Bissau",
  "Guyana", "Haiti", "Heard Island and McDonald Islands", "Honduras", "Hong Kong", "Hungary",
  "Iceland", "India", "Indonesia", "Iran", "Iraq", "Ireland", "Israel", "Italy", "Jamaica",
  "Japan", "Jordan", "Kazakhstan", "Kenya", "Kiribati", "Kuwait", "Kyrgyzstan", "Laos",
  "Latvia", "Lebanon", "Lesotho", "Liberia", "Libya", "Liechtenstein", "Lithuania", "Luxembourg",
  "Macau", "Madagascar", "Malawi", "Malaysia", "Maldives", "Mali", "Malta", "Marshall Islands",
  "Martinique", "Mauritania", "Mauritius", "Mayotte", "Mexico", "Micronesia", "Moldova",
  "Monaco", "Mongolia", "Montserrat", "Morocco", "Mozambique", "Myanmar", "Namibia",
  "Nauru", "Nepal", "Netherlands", "Netherlands Antilles", "New Caledonia", "New Zealand",
  "Nicaragua", "Niger", "Nigeria", "Niue", "Norfolk Island", "North Korea", "Northern Marianas",
  "Norway", "Oman", "Pakistan", "Palau", "Panama", "Papua New Guinea", "Paraguay", "Peru",
  "Philippines", "Pitcairn Islands", "Poland", "Portugal", "Puerto Rico", "Qatar",
  "Reunion", "Romania", "Russia", "Rwanda", "Sqo Tome and Principe", "Saint Helena",
  "Saint Kitts and Nevis", "Saint Lucia", "Saint Pierre and Miquelon",
  "Saint Vincent and the Grenadines", "Samoa", "San Marino", "Saudi Arabia", "Senegal",
  "Seychelles", "Sierra Leone", "Singapore", "Slovakia", "Slovenia", "Solomon Islands",
  "Somalia", "South Africa", "South Georgia and the South Sandwich Islands", "South Korea",
  "Spain", "Sri Lanka", "Sudan", "Suriname", "Svalbard and Jan Mayen", "Swaziland", "Sweden",
  "Switzerland", "Syria", "Taiwan", "Tajikistan", "Tanzania", "Thailand", "The Bahamas",
  "The Gambia", "Togo", "Tokelau", "Tonga", "Trinidad and Tobago", "Tunisia", "Turkey",
  "Turkmenistan", "Turks and Caicos Islands", "Tuvalu", "Virgin Islands", "Uganda",
  "Ukraine", "United Arab Emirates", "United Kingdom",
  "United States", "United States Minor Outlying Islands", "Uruguay", "Uzbekistan",
  "Vanuatu", "Vatican City", "Venezuela", "Vietnam", "Wallis and Futuna", "Western Sahara",
  "Yemen", "Yugoslavia", "Zambia", "Zimbabwe"
};
```

Voici la liste de suggestions qui vous sera fournie dans une liste déroulante lorsque l’utilisateur tape dans la [`AutoCompleteTextView`](https://developer.xamarin.com/api/type/Android.Widget.AutoCompleteTextView/)
widget.

Exécutez l'application. Lorsque vous tapez, vous devez voir quelque chose comme ceci :

[![Saisie semi-automatique capture d’écran répertoriant les noms qui contiennent « ca »](auto-complete-images/helloautocomplete.png)](auto-complete-images/helloautocomplete.png#lightbox)



## <a name="more-information"></a>Informations complémentaires

Notez qu’à l’aide d’un tableau de chaînes codées en dur n’est pas une pratique de conception recommandée, car votre code d’application doit se concentrer sur le comportement, pas de contenu. Contenu de l’application tels que les chaînes doit-elle être externalisé à partir du code pour faciliter les modifications apportées au contenu et de faciliter la localisation du contenu. Les chaînes codées en dur sont utilisés dans ce didacticiel uniquement à simplifier et de vous concentrer sur la [`AutoCompleteTextView`](https://developer.xamarin.com/api/type/Android.Widget.AutoCompleteTextView/)
widget. Au lieu de cela, votre application doit déclarer ces tableaux de chaînes dans un fichier XML. Cela est possible avec un `<string-array>` ressource dans votre projet `res/values/strings.xml` fichier. Exemple :

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
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

Pour utiliser ces chaînes de ressource pour le [ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/), remplacer l’original [`ArrayAdapter`](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/)
ligne de constructeur avec les éléments suivants :

```csharp
string[] countries = Resources.GetStringArray (Resource.array.countries_array);
var adapter = new ArrayAdapter<String> (this, Resource.layout.list_item, countries);
```


### <a name="references"></a>Références

-   [Recipe (Recette) AutoCompleteTextView](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/autocomplete_text_view/add_an_autocomplete_text_input) &ndash; Xamarin.Android exemple de projet pour le `AutoCompleteTextView`.
-   [`ArrayAdapter`](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/)
-   [`AutoCompleteTextView`](https://developer.xamarin.com/api/type/Android.Widget.AutoCompleteTextView/)

*Certaines parties de cette page sont des modifications basées sur le travail créé et partagé par Android Open Source Project et utilisé conformément aux conditions décrites dans le*
[*licence Creative Commons 2.5 Attribution* ](http://creativecommons.org/licenses/by/2.5/) *. Ce didacticiel est basé sur le*
[*didacticiel Android semi-automatique*](https://developer.android.com/resources/tutorials/views/hello-autocomplete.html)
 *.*
