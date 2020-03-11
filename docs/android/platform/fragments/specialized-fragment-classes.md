---
title: Classes de fragment spécialisé
ms.prod: xamarin
ms.assetid: 7A0AEB2C-EE77-63BF-652A-DA049B691C64
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/08/2018
ms.openlocfilehash: b004fbf121374a2bb3bf5d85f45d8cae293573bf
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027312"
---
# <a name="specialized-fragment-classes"></a>Classes de fragment spécialisé

L’API fragments fournit d’autres sous-classes qui encapsulent certaines des fonctionnalités les plus courantes présentes dans les applications. Ces sous-classes sont les suivantes :

- **ListFragment** &ndash; ce fragment est utilisé pour afficher une liste d’éléments liés à une source de source telle qu’un tableau ou un curseur.

- **DialogFragment** &ndash; ce fragment est utilisé comme un wrapper autour d’une boîte de dialogue. Le fragment affiche la boîte de dialogue au-dessus de son activité.

- **PreferenceFragment** &ndash; ce fragment est utilisé pour afficher les objets de préférence en tant que listes.

## <a name="the-listfragment"></a>ListFragment

La `ListFragment` est très similaire au concept et aux fonctionnalités du `ListActivity`; Il s’agit d’un wrapper qui héberge un `ListView` dans un fragment. L’image ci-dessous montre une `ListFragment` s’exécutant sur une tablette et un téléphone :

[![des captures d’écran de ListFragment sur une tablette et sur un téléphone](specialized-fragment-classes-images/intro-screenshot-sml.png)](specialized-fragment-classes-images/intro-screenshot.png#lightbox)

### <a name="binding-data-with-the-listadapter"></a>Liaison de données avec ListAdapter

La classe `ListFragment` fournit déjà une disposition par défaut. il n’est donc pas nécessaire de remplacer `OnCreateView` pour afficher le contenu du `ListFragment`. Le `ListView` est lié aux données à l’aide d’une implémentation de `ListAdapter`. L’exemple suivant montre comment effectuer cette opération à l’aide d’un simple tableau de chaînes :

```csharp
public override void OnActivityCreated(Bundle savedInstanceState)
{
    base.OnActivityCreated(savedInstanceState);
    string[] values = new[] { "Android", "iPhone", "WindowsMobile",
                "Blackberry", "WebOS", "Ubuntu", "Windows7", "Max OS X",
                "Linux", "OS/2" };
    this.ListAdapter = new ArrayAdapter<string>(Activity, Android.Resource.Layout.SimpleExpandableListItem1, values);
}
```

Lors de la définition de la `ListAdapter`, il est important d’utiliser la propriété `ListFragment.ListAdapter`, et non la propriété `ListView.ListAdapter`. L’utilisation de `ListView.ListAdapter` entraîne l’ignorance du code d’initialisation important.

### <a name="responding-to-user-selection"></a>Réponse à la sélection de l’utilisateur

Pour répondre aux sélections des utilisateurs, une application doit remplacer la méthode `OnListItemClick`. L’exemple suivant montre une des possibilités suivantes :

```csharp
public override void OnListItemClick(ListView l, View v, int index, long id)
{
    // We can display everything in place with fragments.
    // Have the list highlight this item and show the data.
    ListView.SetItemChecked(index, true);

    // Check what fragment is shown, replace if needed.
    var details = FragmentManager.FindFragmentById<DetailsFragment>(Resource.Id.details);
    if (details == null || details.ShownIndex != index)
    {
        // Make new fragment to show this selection.
        details = DetailsFragment.NewInstance(index);

        // Execute a transaction, replacing any existing
        // fragment with this one inside the frame.
        var ft = FragmentManager.BeginTransaction();
        ft.Replace(Resource.Id.details, details);
        ft.SetTransition(FragmentTransit.FragmentFade);
        ft.Commit();
    }
}
```

Dans le code ci-dessus, quand l’utilisateur sélectionne un élément dans la `ListFragment`, un nouveau fragment est affiché dans l’activité d’hébergement, affichant plus de détails sur l’élément sélectionné.

## <a name="dialogfragment"></a>DialogFragment

Le *DialogFragment* est un fragment utilisé pour afficher un objet de boîte de dialogue à l’intérieur d’un fragment qui flottera au-dessus de la fenêtre de l’activité. Il est destiné à remplacer les API de dialogue géré (à partir d’Android 3,0). La capture d’écran suivante montre un exemple de `DialogFragment`:

[Capture d’écran ![de l’affichage de la DialogFragment ajouter une nouvelle édition de véhicule](specialized-fragment-classes-images/dialog-fragment-example.png)](specialized-fragment-classes-images/dialog-fragment-example.png#lightbox)

Une `DialogFragment` garantit que l’état entre le fragment et la boîte de dialogue reste cohérent. Toutes les interactions et le contrôle de l’objet de boîte de dialogue doivent se produire via l’API `DialogFragment` et ne sont pas effectués avec des appels directs sur l’objet Dialog. L’API `DialogFragment` fournit à chaque instance une méthode `Show()` utilisée pour afficher un fragment. Il existe deux façons de se débarrasser d’un fragment :

- Appelez `DialogFragment.Dismiss()` sur l’instance `DialogFragment`. 

- Affichez un autre `DialogFragment`.

Pour créer un `DialogFragment`, une classe hérite de `Android.App.DialogFragment,` puis remplace l’une des deux méthodes suivantes :

- **OnCreateView** &ndash; cela crée et retourne une vue.

- **OnCreateDialog** &ndash; cela crée une boîte de dialogue personnalisée. Il est généralement utilisé pour afficher un *AlertDialog*. Lors de la substitution de cette méthode, il n’est pas nécessaire de substituer `OnCreateView`.

### <a name="a-simple-dialogfragment"></a>DialogFragment simple

La capture d’écran suivante montre un `DialogFragment` simple qui a un `TextView` et deux `Button`s :

[![exemple DialogFragment avec TextView et deux boutons](specialized-fragment-classes-images/dialog-fragment-example-2.png)](specialized-fragment-classes-images/dialog-fragment-example-2.png#lightbox)

Le `TextView` affiche le nombre de fois que l’utilisateur a cliqué sur un bouton dans la `DialogFragment`, alors que le fait de cliquer sur le bouton autre ferme le fragment. Le code pour `DialogFragment` est le suivant :

```csharp
public class MyDialogFragment : DialogFragment
{
    private int _clickCount;
    public override void OnCreate(Bundle savedInstanceState)
    {
        _clickCount = 0;
    }

    public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState)
        
        var view = inflater.Inflate(Resource.Layout.dialog_fragment_layout, container, false);
        var textView = view.FindViewById<TextView>(Resource.Id.dialog_text_view);
            
        view.FindViewById<Button>(Resource.Id.dialog_button).Click += delegate
            {
                textView.Text = "You clicked the button " + _clickCount++ + " times.";
            };

        // Set up a handler to dismiss this DialogFragment when this button is clicked.
        view.FindViewById<Button>(Resource.Id.dismiss_dialog_button).Click += (sender, args) => Dismiss();
        return view;
        }
    }
}
```

### <a name="displaying-a-fragment"></a>Affichage d’un fragment

Comme tous les fragments, un `DialogFragment` s’affiche dans le contexte d’une `FragmentTransaction`.

La méthode `Show()` sur un `DialogFragment` prend un `FragmentTransaction` et un `string` comme entrée. La boîte de dialogue sera ajoutée à l’activité et la `FragmentTransaction` validée.

Le code suivant illustre un moyen possible pour une activité d’utiliser la méthode `Show()` pour afficher une `DialogFragment`:

```csharp
public void ShowDialog()
{
    var transaction = FragmentManager.BeginTransaction();
    var dialogFragment = new MyDialogFragment();
    dialogFragment.Show(transaction, "dialog_fragment");
}
```

### <a name="dismissing-a-fragment"></a>Ignorer un fragment

L’appel de `Dismiss()` sur une instance d’un `DialogFragment` entraîne la suppression d’un fragment de l’activité et la validation de cette transaction.
Les méthodes de cycle de vie de fragments standard impliquées dans la destruction d’un fragment sont appelées.

### <a name="alert-dialog"></a>Boîte de dialogue d’alerte

Au lieu de substituer `OnCreateView`, un `DialogFragment` peut remplacer `OnCreateDialog`. Cela permet à une application de créer un [AlertDialog](xref:Android.App.AlertDialog) géré par un fragment. Le code suivant est un exemple qui utilise la `AlertDialog.Builder` pour créer un `Dialog`:

```csharp
public class AlertDialogFragment : DialogFragment
{
    public override Dialog OnCreateDialog(Bundle savedInstanceState)
    {
        EventHandler<DialogClickEventArgs> okhandler;
        var builder = new AlertDialog.Builder(Activity)
            .SetMessage("This is my dialog.")
            .SetPositiveButton("Ok", (sender, args) =>
                                         {
                                             // Do something when this button is clicked.
                                         })
            .SetTitle("Custom Dialog");
        return builder.Create();
    }
}
```

## <a name="preferencefragment"></a>PreferenceFragment

Pour faciliter la gestion des préférences, l’API fragments fournit la sous-classe `PreferenceFragment`. Le `PreferenceFragment` est similaire à l' [PreferenceActivity](xref:Android.Preferences.PreferenceActivity) &ndash; il affiche une hiérarchie de préférences pour l’utilisateur dans un fragment. Lorsque l’utilisateur interagit avec les préférences, il est automatiquement enregistré dans [SharedPreferences](https://developer.android.com/reference/android/content/SharedPreferences.html).
Dans les applications Android 3,0 ou versions ultérieures, utilisez la `PreferenceFragment` pour gérer les préférences des applications. L’illustration suivante montre un exemple de `PreferenceFragment`:

[![exemple PreferencesFragment avec les préférences en ligne, boîte de dialogue et lancement](specialized-fragment-classes-images/preferences-dialog.png)](specialized-fragment-classes-images/preferences-dialog.png#lightbox)

### <a name="create-a-preference-fragment-from-a-resource"></a>Créer un fragment de préférence à partir d’une ressource

Le fragment de préférence peut être augmenté d’un fichier de ressources XML à l’aide de la méthode [PreferenceFragment. AddPreferencesFromResource](xref:Android.Preferences.PreferenceFragment.AddPreferencesFromResource*) . L’emplacement logique pour appeler cette méthode dans le cycle de vie du fragment se trouve dans la méthode `OnCreate`.

La `PreferenceFragment` illustrée ci-dessus a été créée en chargeant une ressource à partir de XML. Le fichier de ressources est :

```xml
<?xml version="1.0" encoding="utf-8"?>

<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">

  <PreferenceCategory android:title="Inline Preferences">
    <CheckBoxPreference android:key="checkbox_preference"
                        android:title="Checkbox Preference Title"
                        android:summary="Checkbox Preference Summary" />

  </PreferenceCategory>

  <PreferenceCategory android:title="Dialog Based Preferences">

    <EditTextPreference android:key="edittext_preference"
                        android:title="EditText Preference Title"
                        android:summary="EditText Preference Summary"
                        android:dialogTitle="Edit Text Preferrence Dialog Title" />

  </PreferenceCategory>

  <PreferenceCategory android:title="Launch Preferences">

    <!-- This PreferenceScreen tag serves as a screen break (similar to page break
             in word processing). Like for other preference types, we assign a key
             here so it is able to save and restore its instance state. -->
    <PreferenceScreen android:key="screen_preference"
                      android:title="Title Screen Preferences"
                      android:summary="Summary Screen Preferences">

      <!-- You can place more preferences here that will be shown on the next screen. -->

      <CheckBoxPreference android:key="next_screen_checkbox_preference"
                          android:title="Next Screen Toggle Preference Title"
                          android:summary="Next Screen Toggle Preference Summary" />

    </PreferenceScreen>

    <PreferenceScreen android:title="Intent Preference Title"
                      android:summary="Intent Preference Summary">

      <intent android:action="android.intent.action.VIEW"
              android:data="http://www.android.com" />

    </PreferenceScreen>

  </PreferenceCategory>

</PreferenceScreen>
```

Le code du fragment de préférence est le suivant :

```csharp
public class PrefFragment : PreferenceFragment
{
    public override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        AddPreferencesFromResource(Resource.Xml.preferences);
    }
}
```

### <a name="querying-activities-to-create-a-preference-fragment"></a>Interrogation des activités pour créer un fragment de préférence

Une autre technique pour créer un `PreferenceFragment` consiste à interroger des activités. Chaque activité peut utiliser la [clé de\_de métadonnées\_](xref:Android.Preferences.PreferenceManager.MetadataKeyPreferences) attribut de préférence qui pointe vers un fichier de ressources XML. Dans Xamarin. Android, cette opération s’effectue en orneant une activité avec le `MetaDataAttribute`, puis en spécifiant le fichier de ressources à utiliser. La classe `PreferenceFragment` fournit la méthode [AddPreferenceFromIntent](xref:Android.Preferences.PreferenceFragment.AddPreferencesFromIntent*) qui peut être utilisée pour interroger une activité afin de trouver cette ressource XML et gonfler une hiérarchie de préférence pour celle-ci.

Un exemple de ce processus est fourni dans l’extrait de code suivant, qui utilise `AddPreferencesFromIntent` pour créer un `PreferenceFragment`:

```csharp
public class MyPreferenceFragment : PreferenceFragment
{
    public override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        var intent = new Intent(this.Activity, typeof (MyActivityWithPreferences));
        AddPreferencesFromIntent(intent);
    }
}
```

Android regardera la classe `MyActivityWithPreference`. La classe doit être ornée avec le `MetaDataAttribute,` comme indiqué dans l’extrait de code suivant :

```csharp
[Activity(Label = "My Activity with Preferences")]
[MetaData(PreferenceManager.MetadataKeyPreferences, Resource = "@xml/preference_from_intent")]
public class MyActivityWithPreferences : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        // This is deliberately blank
    }
}
```

Le `MetaDataAttribute` déclare un fichier de ressources XML que le `PreferenceFragment` utilisera pour augmenter la hiérarchie des préférences. Si le `MetatDataAttribute` n’est pas fourni, une exception est levée au moment de l’exécution. Lorsque ce code s’exécute, le `PreferenceFragment` s’affiche comme dans la capture d’écran suivante :

[Capture d’écran ![de l’exemple d’application avec PreferenceFragment affiché](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png)](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png#lightbox)
