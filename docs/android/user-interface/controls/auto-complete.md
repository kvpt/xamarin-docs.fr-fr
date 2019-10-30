---
title: Saisie semi-automatique pour Xamarin. Android
ms.prod: xamarin
ms.assetid: D4C8CA49-8369-35B7-798D-B147FDC24185
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/31/2018
ms.openlocfilehash: 5a11ab06321b890d8f1f5a35a8456b06a900fbcc
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029367"
---
# <a name="auto-complete-for-xamarinandroid"></a>Saisie semi-automatique pour Xamarin. Android

`AutoCompleteTextView` est un élément d’affichage de texte modifiable qui affiche automatiquement les suggestions de saisie semi-automatique pendant que l’utilisateur tape. La liste de suggestions s’affiche dans un menu déroulant dans lequel l’utilisateur peut choisir un élément pour remplacer le contenu de la zone d’édition par.

![Exemple de saisie semi-automatique](images/auto-complete.png)

## <a name="overview"></a>Vue d'ensemble

Pour créer un widget d’entrée de texte qui fournit des suggestions de saisie semi-automatique, utilisez la [`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
sélection. Des suggestions sont reçues à partir d’une collection de chaînes associée au widget via un [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter).

Dans ce didacticiel, vous allez créer un [`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
widget qui fournit des suggestions pour un nom de pays.

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

Le [`TextView`](xref:Android.Widget.TextView) est une étiquette qui introduit le [`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
sélection.

## <a name="tutorial"></a>Didacticiel

Démarrez un nouveau projet nommé *HelloAutoComplete*.

Créez un fichier XML nommé `list_item.xml` et enregistrez-le dans le dossier **ressources/mise en page** . Définissez l’action de génération de ce fichier sur `AndroidResource`. Modifiez le fichier pour qu’il ressemble à ceci :

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

Ce fichier définit un [`TextView`](xref:Android.Widget.TextView) simple qui sera utilisé pour chaque élément qui apparaît dans la liste de suggestions.

Ouvrez **ressources/mise en page/main. AXML** et insérez ce qui suit :

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

Ouvrez **MainActivity.cs** et insérez le code suivant pour le [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
méthode

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

Une fois que l’affichage du contenu est défini sur la disposition `main.xml`, le [`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
le widget est capturé à partir de la disposition avec [`FindViewById`](xref:Android.App.Activity.FindViewById*). Une nouvelle [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter) est ensuite initialisée pour lier la disposition `list_item.xml` à chaque élément de liste dans le tableau de chaînes `COUNTRIES` (défini à l’étape suivante). Enfin, `SetAdapter()` est appelé pour associer le [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter) à l' [`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
widget afin que le tableau de chaînes renseigne la liste des suggestions.

À l’intérieur de la classe `MainActivity`, ajoutez le tableau de chaînes :

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

Il s’agit de la liste des suggestions qui seront fournies dans une liste déroulante lorsque l’utilisateur tape dans le [`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
sélection.

Exécutez l'application. À mesure que vous tapez, vous devriez voir ce qui suit :

[![exemple de capture d’écran indiquant la saisie semi-automatique des noms contenant « ca »](auto-complete-images/helloautocomplete.png)](auto-complete-images/helloautocomplete.png#lightbox)

## <a name="more-information"></a>Informations complémentaires

Notez que l’utilisation d’un tableau de chaînes codé en dur n’est pas une pratique de conception recommandée, car votre code d’application doit se concentrer sur le comportement, et non sur le contenu. Le contenu de l’application, tel que les chaînes, doit être externalisé à partir du code pour faciliter les modifications du contenu et faciliter la localisation du contenu. Les chaînes codées en dur sont utilisées dans ce didacticiel uniquement pour simplifier et se concentrer sur les [`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
sélection. Au lieu de cela, votre application doit déclarer ces tableaux de chaînes dans un fichier XML. Pour ce faire, vous pouvez utiliser une ressource `<string-array>` dans votre fichier de `res/values/strings.xml` de projet. Exemple :

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

Pour utiliser ces chaînes de ressources pour le [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter), remplacez le [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter) d’origine
ligne du constructeur avec les éléments suivants :

```csharp
string[] countries = Resources.GetStringArray (Resource.array.countries_array);
var adapter = new ArrayAdapter<String> (this, Resource.layout.list_item, countries);
```

### <a name="references"></a>Références

- [AutoCompleteTextView Recipe](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/autocomplete_text_view/add_an_autocomplete_text_input) &ndash; Xamarin. Android exemple de projet pour le `AutoCompleteTextView`
- [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)
- [`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)

_Certaines parties de cette page sont des modifications basées sur le travail créé et partagé par le projet open source Android et utilisées conformément aux termes décrits dans la [licence d’attribution de Creative-2,5](https://creativecommons.org/licenses/by/2.5/). Ce didacticiel est basé sur le [didacticiel de saisie semi-automatique Android *](https://developer.android.com/resources/tutorials/views/hello-autocomplete.html)._
