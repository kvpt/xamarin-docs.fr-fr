---
title: Procédure pas à pas sur les fragments - Partie 2
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: 444A894D-5197-4726-934F-79BA80A71CB0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/26/2018
ms.openlocfilehash: c0d4b0d1a31be43d16fb69eba18c07815631f496
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453933"
---
# <a name="fragments-walkthrough-ndash-landscape"></a>Présentation de la procédure pas à pas de fragments &ndash;

La [ &ndash; partie 1 de la procédure pas à pas fragments](./walkthrough.md) montre comment créer et utiliser des fragments dans une application Android qui cible les plus petits écrans sur un téléphone. L’étape suivante de cette procédure pas à pas consiste à modifier l’application pour tirer parti de l’espace horizontal supplémentaire sur la tablette. &ndash;  une activité sera toujours la liste des lectures (le `TitlesFragment` ) et  `PlayQuoteFragment` sera ajoutée dynamiquement à l’activité en réponse à une sélection effectuée par l’utilisateur :

[![Application en cours d’exécution sur tablette](./walkthrough-landscape-images/01-tablet-screenshot-sml.png)](./walkthrough-landscape-images/01-tablet-screenshot.png#lightbox)

Les téléphones qui s’exécutent en mode paysage bénéficient également de cette amélioration :

[![Application en cours d’exécution sur un téléphone Android en mode paysage](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

## <a name="updating-the-app-to-handle-landscape-orientation"></a>Mise à jour de l’application pour gérer l’orientation paysage

Les modifications suivantes s’appuient sur le travail effectué dans la [procédure pas à pas sur les fragments-téléphone](./walkthrough.md)

1. Créez une autre disposition pour afficher à la fois le `TitlesFragment` et le `PlayQuoteFragment` .
1. Mettez à jour `TitlesFragment` pour détecter si l’appareil affiche les deux fragments simultanément et modifier le comportement en conséquence.
1. Mettez à jour `PlayQuoteActivity` pour fermer lorsque l’appareil est en mode paysage.

## <a name="1-create-an-alternate-layout"></a>1. créer une autre disposition

Lorsque l’activité principale est créée sur un appareil Android, Android détermine la disposition à charger en fonction de l’orientation de l’appareil. Par défaut, Android fournit le fichier de disposition **Resources/layout/activity_main. AXML** . Pour les appareils qui se chargent en mode paysage Android fournissent le fichier de disposition **Resources/layout-Land/activity_main. AXML** . Le guide sur les [ressources Android](../../../app-fundamentals/resources-in-android/index.md) contient plus de détails sur la façon dont Android décide des fichiers de ressources à charger pour une application.

Créez une autre disposition qui cible l’orientation **paysage** en suivant les étapes décrites dans le guide [autres dispositions](../../../user-interface/android-designer/alternative-layout-views.md) . Cela doit ajouter un nouveau fichier de ressources de disposition au projet, **Resources/layout/activity_main. AXML**:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Autre disposition dans Explorateur de solutions](./walkthrough-landscape-images/02-alternate-layout.w157-sml.png)](./walkthrough-landscape-images/02-alternate-layout.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![Autre disposition dans Panneau Solutions](./walkthrough-landscape-images/02-alternate-layout.m743-sml.png)](./walkthrough-landscape-images/02-alternate-layout.m743.png#lightbox)

-----

Après avoir créé l’autre disposition, modifiez la source du fichier Resources **/Layout-Land/activity_main. AXML** afin qu’il corresponde à ce code XML :

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/two_fragments_layout"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <fragment android:name="FragmentSample.TitlesFragment"
        android:id="@+id/titles"
        android:layout_weight="1"
        android:layout_width="0px"
        android:layout_height="match_parent" />

    <FrameLayout android:id="@+id/playquote_container"
            android:layout_weight="1"
            android:layout_width="0px"
            android:layout_height="match_parent"
             />
</LinearLayout>
```

La vue racine de l’activité reçoit l’ID de ressource `two_fragments_layout` et a deux sous-vues, a `fragment` et a `FrameLayout` . Tandis que le `fragment` est chargé de manière statique, le `FrameLayout` agit comme un « espace réservé » qui sera remplacé au moment de l’exécution par le `PlayQuoteFragment` . Chaque fois qu’une nouvelle lecture est sélectionnée dans le `TitlesFragment` , la est `playquote_container` mise à jour avec une nouvelle instance du `PlayQuoteFragment` .

Chaque sous-vue occupera la pleine hauteur de son parent. La largeur de chaque sous-affichage est contrôlée par `android:layout_weight` les `android:layout_width` attributs et. Dans cet exemple, chaque sous-vue occupe 50% de la largeur fournie par le parent. Pour plus d’informations sur l’épaisseur de la _disposition_, consultez [le document de Google sur le élément LinearLayout](https://developer.android.com/guide/topics/ui/layout/linear.html) .

## <a name="2-changes-to-titlesfragment"></a>2. modifications apportées à TitlesFragment

Une fois l’autre disposition créée, vous devez la mettre à jour `TitlesFragment` . Lorsque l’application affiche les deux fragments sur une activité, vous `TitlesFragment` devez charger le `PlayQuoteFragment` dans l’activité parente. Sinon, `TitlesFragment` doit lancer le `PlayQuoteActivity` qui héberge le `PlayQuoteFragment` . Un indicateur booléen vous aide à `TitlesFragment` déterminer le comportement à utiliser. Cet indicateur sera initialisé dans la `OnActivityCreated` méthode.

Tout d’abord, ajoutez une variable d’instance en haut de la `TitlesFragment` classe :

```csharp
bool showingTwoFragments;
```

Ensuite, ajoutez l’extrait de code suivant à  `OnActivityCreated` pour initialiser la variable : 

```csharp
var quoteContainer = Activity.FindViewById(Resource.Id.playquote_container);
showingTwoFragments = quoteContainer != null &&
                      quoteContainer.Visibility == ViewStates.Visible;
if (showingTwoFragments)
{
    ListView.ChoiceMode = ChoiceMode.Single;
    ShowPlayQuote(selectedPlayId);
}
```

Si l’appareil s’exécute en mode paysage, alors le `FrameLayout` avec l’ID de ressource sera `playquote_container` visible à l’écran, donc `showingTwoFragments` sera initialisé à `true` . Si l’appareil s’exécute en mode portrait, il n' `playquote_container` apparaîtra pas à l’écran `showingTwoFragments` `false` .

La `ShowPlayQuote` méthode devra modifier l’affichage d’un guillemet &ndash; dans un fragment ou lancer une nouvelle activité.  Mettez à jour la `ShowPlayQuote` méthode pour charger un fragment lors de l’émission de deux fragments. sinon, elle doit lancer une activité :

```csharp
void ShowPlayQuote(int playId)
{
    selectedPlayId = playId;
    if (showingTwoFragments)
    {
        ListView.SetItemChecked(selectedPlayId, true);

        var playQuoteFragment = FragmentManager.FindFragmentById(Resource.Id.playquote_container) as PlayQuoteFragment;

        if (playQuoteFragment == null || playQuoteFragment.PlayId != playId)
        {
            var container = Activity.FindViewById(Resource.Id.playquote_container);
            var quoteFrag = PlayQuoteFragment.NewInstance(selectedPlayId);

            FragmentTransaction ft = FragmentManager.BeginTransaction();
            ft.Replace(Resource.Id.playquote_container, quoteFrag);
            ft.Commit();
        }
    }
    else
    {
        var intent = new Intent(Activity, typeof(PlayQuoteActivity));
        intent.PutExtra("current_play_id", playId);
        StartActivity(intent);
    }
}
```

Si l’utilisateur a sélectionné une lecture différente de celle qui est actuellement affichée dans `PlayQuoteFragment` , un nouveau `PlayQuoteFragment` est créé et remplace le contenu du `playquote_container` dans le contexte d’un objet `FragmentTransaction` .

### <a name="complete-code-for-titlesfragment"></a>Code complet pour TitlesFragment

Après avoir effectué toutes les modifications précédentes de `TitlesFragment` , la classe complète doit correspondre à ce code :

```csharp
public class TitlesFragment : ListFragment
{
    int selectedPlayId;
    bool showingTwoFragments;

    public override void OnActivityCreated(Bundle savedInstanceState)
    {
        base.OnActivityCreated(savedInstanceState);
        ListAdapter = new ArrayAdapter<string>(Activity, Android.Resource.Layout.SimpleListItemActivated1, Shakespeare.Titles);

        if (savedInstanceState != null)
        {
            selectedPlayId = savedInstanceState.GetInt("current_play_id", 0);
        }

        var quoteContainer = Activity.FindViewById(Resource.Id.playquote_container);
        showingTwoFragments = quoteContainer != null &&
                                quoteContainer.Visibility == ViewStates.Visible;
        if (showingTwoFragments)
        {
            ListView.ChoiceMode = ChoiceMode.Single;
            ShowPlayQuote(selectedPlayId);
        }
    }

    public override void OnSaveInstanceState(Bundle outState)
    {
        base.OnSaveInstanceState(outState);
        outState.PutInt("current_play_id", selectedPlayId);
    }

    public override void OnListItemClick(ListView l, View v, int position, long id)
    {
        ShowPlayQuote(position);
    }

    void ShowPlayQuote(int playId)
    {
        selectedPlayId = playId;
        if (showingTwoFragments)
        {
            ListView.SetItemChecked(selectedPlayId, true);

            var playQuoteFragment = FragmentManager.FindFragmentById(Resource.Id.playquote_container) as PlayQuoteFragment;

            if (playQuoteFragment == null || playQuoteFragment.PlayId != playId)
            {
                var container = Activity.FindViewById(Resource.Id.playquote_container);
                var quoteFrag = PlayQuoteFragment.NewInstance(selectedPlayId);

                FragmentTransaction ft = FragmentManager.BeginTransaction();
                ft.Replace(Resource.Id.playquote_container, quoteFrag);
                ft.AddToBackStack(null);
                ft.SetTransition(FragmentTransit.FragmentFade);
                ft.Commit();
            }
        }
        else
        {
            var intent = new Intent(Activity, typeof(PlayQuoteActivity));
            intent.PutExtra("current_play_id", playId);
            StartActivity(intent);
        }
    }
}
```

## <a name="3-changes-to-playquoteactivity"></a>3. modifications apportées à PlayQuoteActivity

Il existe un détail final à prendre en charge : `PlayQuoteActivity` n’est pas nécessaire lorsque l’appareil est en mode paysage. Si l’appareil est en mode paysage, le ne `PlayQuoteActivity` doit pas être visible. Mettez à jour la `OnCreate` méthode de `PlayQuoteActivity` afin qu’elle se ferme lui-même. Ce code est la dernière version de `PlayQuoteActivity.OnCreate` :

```csharp
protected override void OnCreate(Bundle savedInstanceState)
{
    base.OnCreate(savedInstanceState);

    if (Resources.Configuration.Orientation == Android.Content.Res.Orientation.Landscape)
    {
        Finish();
    }

    var playId = Intent.Extras.GetInt("current_play_id", 0);
    var playQuoteFrag = PlayQuoteFragment.NewInstance(playId);
    FragmentManager.BeginTransaction()
                    .Add(Android.Resource.Id.Content, playQuoteFrag)
                    .Commit();
}
```

Cette modification ajoute une vérification de l’orientation de l’appareil. S’il est en mode paysage, `PlayQuoteActivity` se ferme lui-même.

## <a name="4-run-the-application"></a>4. exécuter l’application

Une fois ces modifications terminées, exécutez l’application, faites pivoter l’appareil en mode paysage (si nécessaire), puis sélectionnez une lecture. Le guillemet doit s’afficher sur le même écran que la liste des lectures :

[![Application en cours d’exécution sur un téléphone Android en mode paysage](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

[![Application s’exécutant sur une tablette Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)