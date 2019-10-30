---
title: Xamarin. Android
ms.prod: xamarin
ms.assetid: 004089E9-7C1D-2285-765A-B69143091F2A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: ba4a83eb997b879e8a2398f9857e2fd80221f8ef
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029164"
---
# <a name="xamarinandroid-spinner"></a>Xamarin. Android

[`Spinner`](xref:Android.Widget.Spinner) est un widget qui présente une liste déroulante permettant de sélectionner des éléments. Ce guide explique comment créer une application simple qui affiche une liste de choix dans un compteur, suivi par des modifications qui affichent d’autres valeurs associées au choix sélectionné.

## <a name="basic-spinner"></a>Compteur de base

Dans la première partie de ce didacticiel, vous allez créer un widget de compteur simple qui affiche une liste de planètes. Quand une planète est sélectionnée, un message Toast affiche l’élément sélectionné :

[![des exemples de captures d’écran de l’application HelloSpinner](spinner-images/01-example-screenshots-sml.png)](spinner-images/01-example-screenshots.png#lightbox)

Démarrez un nouveau projet nommé **HelloSpinner**.

Ouvrez **ressources/mise en page/main. AXML** et insérez le code XML suivant :

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:padding="10dip"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content">
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="10dip"
        android:text="@string/planet_prompt"
    />
    <Spinner
        android:id="@+id/spinner"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:prompt="@string/planet_prompt"
    />
</LinearLayout>
```

Notez que l’attribut de `android:text` de l' [`TextView`](xref:Android.Widget.TextView)et l’attribut de `android:prompt` de l' [`Spinner`](xref:Android.Widget.Spinner)référencent la même ressource de chaîne. Ce texte se comporte comme un titre pour le widget. Lorsqu’il est appliqué à la [`Spinner`](xref:Android.Widget.Spinner), le texte du titre s’affiche dans la boîte de dialogue de sélection qui s’affiche lors de la sélection du widget.

Modifiez le fichier **Resources/values/Strings. xml** , puis modifiez le fichier pour qu’il ressemble à ceci :

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <string name="app_name">HelloSpinner</string>
  <string name="planet_prompt">Choose a planet</string>
  <string-array name="planets_array">
    <item>Mercury</item>
    <item>Venus</item>
    <item>Earth</item>
    <item>Mars</item>
    <item>Jupiter</item>
    <item>Saturn</item>
    <item>Uranus</item>
    <item>Neptune</item>
  </string-array>
</resources>
```

Le deuxième élément `<string>` définit la chaîne de titre référencée par le [`TextView`](xref:Android.Widget.TextView) et [`Spinner`](xref:Android.Widget.Spinner) dans la disposition ci-dessus.
L’élément `<string-array>` définit la liste des chaînes qui seront affichées comme liste dans le widget [`Spinner`](xref:Android.Widget.Spinner) .

À présent, ouvrez **MainActivity.cs** et ajoutez l’instruction `using` suivante :

```csharp
using System;
```

Ensuite, insérez le code suivant pour la méthode [`OnCreate()`](xref:Android.App.Activity.OnCreate*)) :

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "Main" layout resource
    SetContentView (Resource.Layout.Main);

    Spinner spinner = FindViewById<Spinner> (Resource.Id.spinner);

    spinner.ItemSelected += new EventHandler<AdapterView.ItemSelectedEventArgs> (spinner_ItemSelected);
    var adapter = ArrayAdapter.CreateFromResource (
            this, Resource.Array.planets_array, Android.Resource.Layout.SimpleSpinnerItem);

    adapter.SetDropDownViewResource (Android.Resource.Layout.SimpleSpinnerDropDownItem);
    spinner.Adapter = adapter;
}
```

Une fois que la disposition `Main.axml` est définie en tant qu’affichage du contenu, le widget [`Spinner`](xref:Android.Widget.Spinner) est capturé à partir de la disposition avec [`FindViewById<>(int)`](xref:Android.App.Activity.FindViewById*).
[`CreateFromResource()`](xref:Android.Widget.ArrayAdapter.CreateFromResource*)
la méthode crée ensuite un [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter), qui lie chaque élément du tableau de chaînes à l’apparence initiale du [`Spinner`](xref:Android.Widget.Spinner) (c’est-à-dire comment chaque élément apparaîtra dans le compteur lorsqu’il est sélectionné). L’ID de `Resource.Array.planets_array` fait référence à la `string-array` définie ci-dessus et l’ID de `Android.Resource.Layout.SimpleSpinnerItem` référence une disposition pour l’apparence du compteur standard, définie par la plateforme.
[`SetDropDownViewResource`](xref:Android.Widget.ArrayAdapter.SetDropDownViewResource*)
est appelé pour définir l’apparence de chaque élément lorsque le widget est ouvert. Enfin, l' [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter) est définie pour associer tous ses éléments à la [`Spinner`](xref:Android.Widget.Spinner) en définissant la propriété [`Adapter`](xref:Android.Widget.ArrayAdapter) .

À présent, fournissez une méthode de rappel qui notifie l’application lorsqu’un élément a été sélectionné à partir de la [`Spinner`](xref:Android.Widget.Spinner). Voici à quoi doit ressembler cette méthode :

```csharp
private void spinner_ItemSelected (object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format ("The planet is {0}", spinner.GetItemAtPosition (e.Position));
    Toast.MakeText (this, toast, ToastLength.Long).Show ();
}
```

Lorsqu’un élément est sélectionné, l’expéditeur est converti en [`Spinner`](xref:Android.Widget.Spinner) afin que les éléments soient accessibles. À l’aide de la propriété `Position` sur l' `ItemEventArgs`, vous pouvez rechercher le texte de l’objet sélectionné et l’utiliser pour afficher un [`Toast`](xref:Android.Widget.Toast).

Exécutez l’application. le résultat doit ressembler à ceci :

[Capture d’écran ![exemple de compteur avec mars sélectionné comme planète](spinner-images/02-basic-example-sml.png)](spinner-images/02-basic-example.png#lightbox)

## <a name="spinner-using-keyvalue-pairs"></a>Compteur utilisant des paires clé/valeur

Il est souvent nécessaire d’utiliser `Spinner` pour afficher les valeurs de clés associées à un type de données utilisé par votre application. Étant donné que `Spinner` ne fonctionne pas directement avec les paires clé/valeur, vous devez stocker la paire clé/valeur séparément, remplir le `Spinner` avec les valeurs de clé, puis utiliser la position de la clé sélectionnée dans le compteur pour rechercher la valeur de données associée. 

Dans les étapes suivantes, l’application **HelloSpinner** est modifiée pour afficher la température moyenne de la planète sélectionnée :

Ajoutez l’instruction `using` suivante à **MainActivity.cs**:

```csharp
using System.Collections.Generic;
```

Ajoutez la variable d’instance suivante à la classe `MainActivity`.
Cette liste contient les paires clé/valeur pour les planètes et leurs températures moyennes :

```csharp
private List<KeyValuePair<string, string>> planets;
```

Dans la méthode `OnCreate`, ajoutez le code suivant avant que `adapter` soit déclaré :

```csharp
planets = new List<KeyValuePair<string, string>>
{
    new KeyValuePair<string, string>("Mercury", "167 degrees C"),
    new KeyValuePair<string, string>("Venus", "464 degrees C"),
    new KeyValuePair<string, string>("Earth", "15 degrees C"),
    new KeyValuePair<string, string>("Mars", "-65 degrees C"),
    new KeyValuePair<string, string>("Jupiter" , "-110 degrees C"),
    new KeyValuePair<string, string>("Saturn", "-140 degrees C"),
    new KeyValuePair<string, string>("Uranus", "-195 degrees C"),
    new KeyValuePair<string, string>("Neptune", "-200 degrees C")
};
```

Ce code crée un magasin simple pour les planètes et leurs températures moyennes associées. (Dans une application réelle, une base de données est généralement utilisée pour stocker des clés et leurs données associées.)

Juste après le code ci-dessus, ajoutez les lignes suivantes pour extraire les clés et les placer dans une liste (dans l’ordre) :

```csharp
List<string> planetNames = new List<string>();
foreach (var item in planets)
    planetNames.Add (item.Key);
```

Transmettez cette liste au constructeur `ArrayAdapter` (au lieu de la ressource `planets_array`) :

```csharp
var adapter = new ArrayAdapter<string>(this,
    Android.Resource.Layout.SimpleSpinnerItem, planetNames);
```

Modifiez `spinner_ItemSelected` afin que la position sélectionnée soit utilisée pour rechercher la valeur (la température) associée à la planète sélectionnée :

```csharp
private void spinner_ItemSelected(object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format("The mean temperature for planet {0} is {1}",
        spinner.GetItemAtPosition(e.Position), planets[e.Position].Value);
    Toast.MakeText(this, toast, ToastLength.Long).Show();
}
```

Exécutez l’application. le Toast doit se présenter comme suit :

[![exemple de la sélection de la planète affichant la température](spinner-images/03-keyvalue-example-sml.png)](spinner-images/03-keyvalue-example.png#lightbox)

## <a name="resources"></a>Ressources

- [`Resource.Layout`](xref:Android.Resource.Layout)
- [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)
- [`Spinner`](xref:Android.Widget.Spinner)

*Certaines parties de cette page sont des modifications basées sur le travail créé et partagé par le projet open source Android et utilisées conformément aux termes décrits dans la*
[*licence d’attribution de Creative-2,5*](https://creativecommons.org/licenses/by/2.5/).
