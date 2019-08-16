---
title: Classes de fragment spécialisé
ms.prod: xamarin
ms.assetid: 7A0AEB2C-EE77-63BF-652A-DA049B691C64
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/08/2018
ms.openlocfilehash: e49f12dd656d5e07feccd34e231a00124d81048a
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524274"
---
# <a name="specialized-fragment-classes"></a>Classes de fragment spécialisé

L’API fragments fournit d’autres sous-classes qui encapsulent certaines des fonctionnalités les plus courantes présentes dans les applications. Ces sous-classes sont les suivantes:

- **ListFragment** &ndash; Ce fragment est utilisé pour afficher une liste d’éléments liés à une source de source telle qu’un tableau ou un curseur.

- **DialogFragment** &ndash; Ce fragment est utilisé comme un wrapper autour d’une boîte de dialogue. Le fragment affiche la boîte de dialogue au-dessus de son activité.

- **PreferenceFragment** &ndash; Ce fragment est utilisé pour afficher les objets de préférence en tant que listes.



## <a name="the-listfragment"></a>ListFragment

Le `ListFragment` est très similaire au concept et aux fonctionnalités du `ListActivity`; il s’agit d’un wrapper qui `ListView` héberge un dans un fragment. L’image ci-dessous `ListFragment` montre un s’exécutant sur une tablette et un téléphone:

[![Captures d’écran de ListFragment sur une tablette et sur un téléphone](specialized-fragment-classes-images/intro-screenshot-sml.png)](specialized-fragment-classes-images/intro-screenshot.png#lightbox)


### <a name="binding-data-with-the-listadapter"></a>Liaison de données avec ListAdapter

La `ListFragment` classe fournit déjà une disposition par défaut `ListFragment`. il n’est donc pas nécessaire de `OnCreateView` la substituer pour afficher le contenu du. Le `ListView` est lié aux données à l’aide `ListAdapter` d’une implémentation. L’exemple suivant montre comment effectuer cette opération à l’aide d’un simple tableau de chaînes:

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

Lors de la `ListAdapter`définition de l’élément, il est `ListFragment.ListAdapter` important d’utiliser la propriété `ListView.ListAdapter` , et non la propriété. L' `ListView.ListAdapter` utilisation de entraînera l’ignorance du code d’initialisation important.



### <a name="responding-to-user-selection"></a>Réponse à la sélection de l’utilisateur

Pour répondre aux sélections de l’utilisateur, une application doit `OnListItemClick` substituer la méthode. L’exemple suivant montre une des possibilités suivantes:

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

Dans le code ci-dessus, quand l’utilisateur sélectionne un élément `ListFragment`dans le, un nouveau fragment est affiché dans l’activité d’hébergement, affichant plus de détails sur l’élément sélectionné.



## <a name="dialogfragment"></a>DialogFragment

Le *DialogFragment* est un fragment utilisé pour afficher un objet de boîte de dialogue à l’intérieur d’un fragment qui flottera au-dessus de la fenêtre de l’activité. Il est destiné à remplacer les API de dialogue géré (à partir d’Android 3,0). La capture d’écran suivante montre un exemple `DialogFragment`de:

[![Capture d’écran de l’affichage de la DialogFragment ajouter une nouvelle édition de véhicule](specialized-fragment-classes-images/dialog-fragment-example.png)](specialized-fragment-classes-images/dialog-fragment-example.png#lightbox)

Un `DialogFragment` garantit que l’état entre le fragment et la boîte de dialogue reste cohérent. Toutes les interactions et le contrôle de l’objet de boîte de `DialogFragment` dialogue doivent se produire via l’API et ne sont pas effectués avec des appels directs sur l’objet Dialog. L' `DialogFragment` API fournit à chaque instance une `Show()` méthode utilisée pour afficher un fragment. Il existe deux façons de se débarrasser d’un fragment:

- Appelez `DialogFragment.Dismiss()` sur l' `DialogFragment` instance. 

- Affichez `DialogFragment`un autre.

Pour créer un `DialogFragment`, une classe hérite de `Android.App.DialogFragment,` , puis remplace l’une des deux méthodes suivantes:

- **OnCreateView** &ndash; Cela crée et retourne une vue.

- **OnCreateDialog** &ndash; Cela crée une boîte de dialogue personnalisée. Il est généralement utilisé pour afficher un *AlertDialog*. Lors de la substitution de cette méthode, il n’est pas nécessaire `OnCreateView` de substituer.



### <a name="a-simple-dialogfragment"></a>DialogFragment simple

La capture d’écran suivante montre `DialogFragment` un simple qui `TextView` a un `Button`et deux s:

[![Exemple de DialogFragment avec un TextView et deux boutons](specialized-fragment-classes-images/dialog-fragment-example-2.png)](specialized-fragment-classes-images/dialog-fragment-example-2.png#lightbox)

Le `TextView` affiche le nombre de fois que l’utilisateur a cliqué sur un bouton dans le `DialogFragment`, alors que le fait de cliquer sur le bouton autre ferme le fragment. Le code pour `DialogFragment` est:

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

Comme tous les fragments, `DialogFragment` un est affiché dans le contexte d' `FragmentTransaction`un.

La `Show()` méthode sur un `DialogFragment` prend un `FragmentTransaction` et un `string` comme entrée. La boîte de dialogue sera ajoutée à l’activité et `FragmentTransaction` validée.

Le code suivant illustre un moyen possible pour une activité d’utiliser `Show()` la méthode pour afficher `DialogFragment`un:

```csharp
public void ShowDialog()
{
    var transaction = FragmentManager.BeginTransaction();
    var dialogFragment = new MyDialogFragment();
    dialogFragment.Show(transaction, "dialog_fragment");
}
```


### <a name="dismissing-a-fragment"></a>Ignorer un fragment

L' `Dismiss()` appel de sur une instance `DialogFragment` de entraîne la suppression d’un fragment de l’activité et la validation de cette transaction.
Les méthodes de cycle de vie de fragments standard impliquées dans la destruction d’un fragment sont appelées.


### <a name="alert-dialog"></a>Boîte de dialogue d’alerte

Au lieu de `OnCreateView`remplacer, un `DialogFragment` peut remplacer. `OnCreateDialog` Cela permet à une application de créer un [AlertDialog](xref:Android.App.AlertDialog) géré par un fragment. Le code suivant est un exemple qui utilise `AlertDialog.Builder` pour créer un: `Dialog`

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

Pour faciliter la gestion des préférences, l’API fragments `PreferenceFragment` fournit la sous-classe. Le `PreferenceFragment` est similaire au [PreferenceActivity](xref:Android.Preferences.PreferenceActivity) &ndash; . il présente une hiérarchie de préférences pour l’utilisateur dans un fragment. Lorsque l’utilisateur interagit avec les préférences, il est automatiquement enregistré dans [SharedPreferences](https://developer.android.com/reference/android/content/SharedPreferences.html).
Dans les applications Android 3,0 ou versions ultérieures `PreferenceFragment` , utilisez le pour gérer les préférences des applications. L’illustration suivante montre un exemple de `PreferenceFragment`:

[![Exemple de PreferencesFragment avec des préférences Inline, de dialogue et de lancement](specialized-fragment-classes-images/preferences-dialog.png)](specialized-fragment-classes-images/preferences-dialog.png#lightbox)


### <a name="create-a-preference-fragment-from-a-resource"></a>Créer un fragment de préférence à partir d’une ressource

Le fragment de préférence peut être augmenté d’un fichier de ressources XML à l’aide de la méthode [PreferenceFragment. AddPreferencesFromResource](xref:Android.Preferences.PreferenceFragment.AddPreferencesFromResource*) . Un emplacement logique pour appeler cette méthode dans le cycle de vie du fragment est dans la `OnCreate` méthode.

L' `PreferenceFragment` image ci-dessus a été créée en chargeant une ressource à partir de XML. Le fichier de ressources est:

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

Le code du fragment de préférence est le suivant:

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

Une autre technique pour créer `PreferenceFragment` un implique l’interrogation des activités. Chaque activité peut utiliser l’attribut de [préférence de clé\_\_](xref:Android.Preferences.PreferenceManager.MetadataKeyPreferences) de métadonnées qui pointe vers un fichier de ressources XML. Dans Xamarin. Android, cette opération s’effectue en orneant une activité avec `MetaDataAttribute`le, puis en spécifiant le fichier de ressources à utiliser. La `PreferenceFragment` classe fournit la méthode [AddPreferenceFromIntent](xref:Android.Preferences.PreferenceFragment.AddPreferencesFromIntent*) qui peut être utilisée pour interroger une activité afin de trouver cette ressource XML et gonfler une hiérarchie de préférence pour celle-ci.

Un exemple de ce processus est fourni dans l’extrait de code suivant, qui `AddPreferencesFromIntent` utilise pour créer `PreferenceFragment`un:

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

Android regardera la classe `MyActivityWithPreference`. La classe doit être ornée `MetaDataAttribute,` de, comme illustré dans l’extrait de code suivant:

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

Déclare un fichier de ressources XML que le `PreferenceFragment` utilisera pour augmenter la hiérarchie des préférences. `MetaDataAttribute` Si le `MetatDataAttribute` n’est pas fourni, une exception est levée au moment de l’exécution. Lorsque ce code s’exécute, `PreferenceFragment` le s’affiche comme dans la capture d’écran suivante:

[![Capture d’écran de l’exemple d’application avec PreferenceFragment affiché](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png)](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png#lightbox)
