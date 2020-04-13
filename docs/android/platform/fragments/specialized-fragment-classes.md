---
title: Classes de fragment spécialisé
ms.prod: xamarin
ms.assetid: 7A0AEB2C-EE77-63BF-652A-DA049B691C64
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/08/2018
ms.openlocfilehash: b004fbf121374a2bb3bf5d85f45d8cae293573bf
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027312"
---
# <a name="specialized-fragment-classes"></a>Classes de fragment spécialisé

L’API Fragments fournit d’autres sous-classes qui encapsulent certaines des fonctionnalités les plus courantes trouvées dans les applications. Ces sous-classes sont les :

- **ListeFragment** &ndash; Ce fragment est utilisé pour afficher une liste d’éléments liés à une source de données comme un tableau ou un curseur.

- **DialogFragment** &ndash; Ce fragment est utilisé comme emballage autour d’un dialogue. Le Fragment affichera le dialogue au-dessus de son activité.

- **PréférenceFragment** &ndash; Ce fragment est utilisé pour afficher les objets Préférence comme listes.

## <a name="the-listfragment"></a>La ListeFragment

Le `ListFragment` est très similaire dans le `ListActivity`concept et la fonctionnalité à la ; c’est un emballage `ListView` qui héberge un fragment. L’image ci-dessous montre une `ListFragment` course sur une tablette et un téléphone:

[![Captures d’écran de ListFragment sur une tablette et sur un téléphone](specialized-fragment-classes-images/intro-screenshot-sml.png)](specialized-fragment-classes-images/intro-screenshot.png#lightbox)

### <a name="binding-data-with-the-listadapter"></a>Données contraignantes avec the ListAdapter

La `ListFragment` classe fournit déjà une mise en page `OnCreateView` par défaut, il `ListFragment`n’est donc pas nécessaire de remplacer pour afficher le contenu de la . Le `ListView` est lié aux `ListAdapter` données en utilisant une implémentation. L’exemple suivant montre comment cela pourrait être fait en utilisant un simple éventail de chaînes:

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

Lors de `ListAdapter`la mise en `ListFragment.ListAdapter` place de la `ListView.ListAdapter` , il est important d’utiliser la propriété, et non la propriété. L’utilisation `ListView.ListAdapter` entraînera l’abandon d’importants codes d’initialisation.

### <a name="responding-to-user-selection"></a>Répondre à la sélection des utilisateurs

Pour répondre aux sélections d’utilisateurs, `OnListItemClick` une application doit passer outre à la méthode. L’exemple suivant montre une telle possibilité :

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

Dans le code ci-dessus, lorsque l’utilisateur sélectionne un élément dans le `ListFragment`, un nouveau fragment est affiché dans l’activité d’hébergement, montrant plus de détails sur l’élément qui a été sélectionné.

## <a name="dialogfragment"></a>DialogFragment

Le *DialogueFragment* est un fragment qui est utilisé pour afficher un objet de dialogue à l’intérieur d’un fragment qui flottera sur la fenêtre de l’activité. Il est destiné à remplacer les API de dialogue géré (à partir d’Android 3.0). La capture d’écran `DialogFragment`suivante montre un exemple d’un :

[![Capture d’écran de DialogFragment affichant Add New Vehicle EditBox](specialized-fragment-classes-images/dialog-fragment-example.png)](specialized-fragment-classes-images/dialog-fragment-example.png#lightbox)

A `DialogFragment` veille à ce que l’état entre le Fragment et le dialogue reste cohérent. Toutes les interactions et le contrôle de `DialogFragment` l’objet de dialogue doivent se produire par l’API, et ne pas être fait avec des appels directs sur l’objet de dialogue. L’API `DialogFragment` fournit à `Show()` chaque instance une méthode qui est utilisée pour afficher un fragment. Il y a deux façons de se débarrasser d’un fragment :

- Faites `DialogFragment.Dismiss()` appel `DialogFragment` à l’instance. 

- Afficher `DialogFragment`un autre .

Pour créer `DialogFragment`un , une `Android.App.DialogFragment,` classe hérite de puis remplace l’une des deux méthodes suivantes:

- **OnCreateView** &ndash; Cela crée et renvoie une vue.

- **OnCreateDialog** &ndash; Cela crée un dialogue personnalisé. Il est généralement utilisé pour montrer un *AlertDialog*. Lorsque vous l’emportez sur cette méthode, il n’est pas nécessaire de l’emporter `OnCreateView` .

### <a name="a-simple-dialogfragment"></a>Un simple dialogueFragment

La capture d’écran suivante montre un simple `DialogFragment` qui a un `TextView` et deux `Button`s:

[![Exemple DialogFragment avec un TextView et deux boutons](specialized-fragment-classes-images/dialog-fragment-example-2.png)](specialized-fragment-classes-images/dialog-fragment-example-2.png#lightbox)

Le `TextView` sera afficher le nombre de fois que l’utilisateur a cliqué sur un bouton dans le `DialogFragment`, tout en cliquant sur l’autre bouton fermera le fragment. Le code `DialogFragment` pour est:

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

Comme tous les `DialogFragment` Fragments, a est `FragmentTransaction`affiché dans le contexte d’un .

La `Show()` méthode `DialogFragment` sur `FragmentTransaction` un `string` prend un et un comme une entrée. Le dialogue sera ajouté à l’activité et aux `FragmentTransaction` engagés.

Le code suivant démontre une façon possible `Show()` d’utiliser `DialogFragment`la méthode pour afficher un :

```csharp
public void ShowDialog()
{
    var transaction = FragmentManager.BeginTransaction();
    var dialogFragment = new MyDialogFragment();
    dialogFragment.Show(transaction, "dialog_fragment");
}
```

### <a name="dismissing-a-fragment"></a>Rejet d’un fragment

Faire `Dismiss()` appel à `DialogFragment` un cas de causes d’un fragment à supprimer de l’activité et engage cette transaction.
Les méthodes standard du cycle de vie fragment qui sont impliquées dans la destruction d’un fragment seront appelées.

### <a name="alert-dialog"></a>Dialogue d’alerte

Au lieu de `OnCreateView`l’emporter, `DialogFragment` `OnCreateDialog`un peut plutôt l’emporter . Cela permet à une application de créer un [AlertDialog](xref:Android.App.AlertDialog) qui est géré par un fragment. Le code suivant est un `AlertDialog.Builder` exemple `Dialog`qui utilise le pour créer un :

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

## <a name="preferencefragment"></a>PréférenceFragment

Pour aider à gérer les préférences, l’API Fragments fournit la `PreferenceFragment` sous-classe. L’est `PreferenceFragment` similaire à la [PréférenceActivité,](xref:Android.Preferences.PreferenceActivity) &ndash; il affichera une hiérarchie de préférences à l’utilisateur dans un fragment. Au fur et à mesure que l’utilisateur interagit avec les préférences, elles seront automatiquement enregistrées à [SharedPreferences](https://developer.android.com/reference/android/content/SharedPreferences.html).
Dans les applications Android 3.0 `PreferenceFragment` ou plus, utilisez les préférences pour traiter les préférences dans les applications. L’image suivante montre `PreferenceFragment`un exemple d’un :

[![Exemple PréférencesFragment avec les préférences d’inline, de dialogue et de lancement](specialized-fragment-classes-images/preferences-dialog.png)](specialized-fragment-classes-images/preferences-dialog.png#lightbox)

### <a name="create-a-preference-fragment-from-a-resource"></a>Créer un fragment de préférence à partir d’une ressource

Le fragment de préférence peut être gonflé à partir d’un fichier de ressources XML en utilisant la méthode [PreferenceFragment.AddPreferencesFromResource.](xref:Android.Preferences.PreferenceFragment.AddPreferencesFromResource*) Un endroit logique pour appeler cette méthode dans le `OnCreate` cycle de vie du fragment serait dans la méthode.

La `PreferenceFragment` photo ci-dessus a été créée en chargeant une ressource de XML. Le fichier des ressources est :

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

Le code pour la préférence Fragment est le suivant:

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

### <a name="querying-activities-to-create-a-preference-fragment"></a>Interroger les activités pour créer un fragment de préférence

Une autre technique `PreferenceFragment` pour créer un implique de poser des activités. Chaque activité peut utiliser l’attribut [MÉTAADATA\_KEY\_PREFERENCE](xref:Android.Preferences.PreferenceManager.MetadataKeyPreferences) qui indiquera un fichier de ressources XML. Dans Xamarin.Android, cela se fait en `MetaDataAttribute`ornant une activité avec le , puis en spécifiant le fichier de ressources à utiliser. La `PreferenceFragment` classe fournit la méthode [AddPreferenceFromIntent](xref:Android.Preferences.PreferenceFragment.AddPreferencesFromIntent*) qui peut être utilisé pour interroger une activité pour trouver cette ressource XML et gonfler une hiérarchie de préférence pour elle.

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

Android va regarder `MyActivityWithPreference`la classe . La classe doit être `MetaDataAttribute,` ornée de ce que l’on voit dans l’extrait de code suivant :

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

Le `MetaDataAttribute` déclare un fichier de `PreferenceFragment` ressources XML que le sera utilisé pour gonfler la hiérarchie de préférence. Si `MetatDataAttribute` le n’est pas fourni, alors une exception sera lancée au moment de la course. Lorsque ce code `PreferenceFragment` s’exécute, le apparaît comme dans la capture d’écran suivante:

[![Capture d’écran de l’application d’exemple avec PreferenceFragment affichée](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png)](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png#lightbox)
