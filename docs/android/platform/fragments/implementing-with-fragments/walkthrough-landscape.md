---
title: Procédure pas à pas sur les fragments - Partie 2
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: 444A894D-5197-4726-934F-79BA80A71CB0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/26/2018
ms.openlocfilehash: 4d9ef88f39914f8fa5e578577ee9f6977c2bc88e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020270"
---
# <a name="fragments-walkthrough-ndash-landscape"></a>Fragments paysage pas &ndash; à pas

Le [Fragments Walkthrough &ndash; Partie 1](./walkthrough.md) a démontré comment créer et utiliser des fragments dans une application Android qui cible les écrans plus petits sur un téléphone. La prochaine étape dans cette procédure pas à pas est de modifier &ndash; l’application pour profiter de l’espace horizontal `TitlesFragment`supplémentaire `PlayQuoteFragment` sur la tablette il y aura une activité qui sera toujours la liste des jeux (le ) et sera dynamiquement ajouté à l’activité en réponse à une sélection faite par l’utilisateur:

[![Application fonctionnant sur tablette](./walkthrough-landscape-images/01-tablet-screenshot-sml.png)](./walkthrough-landscape-images/01-tablet-screenshot.png#lightbox)

Les téléphones en mode paysage bénéficieront également de cette amélioration :

[![Application fonctionnant sur un téléphone Android en mode paysage](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

## <a name="updating-the-app-to-handle-landscape-orientation"></a>Mise à jour de l’application pour gérer l’orientation paysagère

Les modifications suivantes s’appuieront sur le travail qui a été fait dans le [Procédure pas à pas Fragments - Téléphone](./walkthrough.md)

1. Créez une disposition alternative `TitlesFragment` pour `PlayQuoteFragment`afficher à la fois le et .
1. Mise `TitlesFragment` à jour pour détecter si l’appareil affiche les deux fragments simultanément et changer le comportement en conséquence.
1. Mise `PlayQuoteActivity` à jour pour fermer lorsque l’appareil est en mode paysage.

## <a name="1-create-an-alternate-layout"></a>1. Créer une mise en page alternative

Lorsque Main Activity est créée sur un appareil Android, Android décidera de la mise en page à charger en fonction de l’orientation de l’appareil. Par défaut, Android fournira le fichier **de mise en page Ressources/layout/activity_main.axml.** Pour les appareils qui chargent en mode paysage Android fournira le fichier de mise en **page Ressources/layout-land/activity_main.axml.** Le guide sur [Android Resources](/xamarin/android/app-fundamentals/resources-in-android) contient plus de détails sur la façon dont Android décide quels fichiers de ressources à charger pour une application.

Créez une disposition alternative qui cible **l’orientation du paysage** en suivant les étapes décrites dans le guide [Alternate Layouts.](/xamarin/android/user-interface/android-designer/alternative-layout-views) Cela devrait ajouter un nouveau fichier de ressources de mise en page au projet, **Ressources/mise en page/activity_main.axml**:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Mise en page alternative dans Solution Explorer](./walkthrough-landscape-images/02-alternate-layout.w157-sml.png)](./walkthrough-landscape-images/02-alternate-layout.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![Mise en page alternative dans Solution Pad](./walkthrough-landscape-images/02-alternate-layout.m743-sml.png)](./walkthrough-landscape-images/02-alternate-layout.m743.png#lightbox)

-----

Après avoir créé la mise en page alternative, modifier la source du fichier **Ressources/ layout-land/activity_main.axml** afin qu’il corresponde à ce XML:

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

La vue d’enracinement de `two_fragments_layout` l’activité est donnée `fragment` l’ID de ressource et a deux sous-vues, a et a `FrameLayout`. Bien `fragment` que le est `FrameLayout` chargé statiquement, les agit comme un «placeholder» qui sera remplacé à l’heure de exécution par le `PlayQuoteFragment`. Chaque fois qu’une nouvelle `TitlesFragment`pièce `playquote_container` est sélectionnée dans le `PlayQuoteFragment`, le sera mis à jour avec une nouvelle instance de la .

Chacune des sous-vues occupera toute la hauteur de ses parents. La largeur de chaque sous-vision `android:layout_weight` `android:layout_width` est contrôlée par les attributs et les attributs. Dans cet exemple, chaque sous-vision occupera 50 % de la largeur fourni par le parent. Voir [le document de Google sur le LinearLayout](https://developer.android.com/guide/topics/ui/layout/linear.html) pour plus de détails sur le poids de mise en _page_.

## <a name="2-changes-to-titlesfragment"></a>2. Modifications apportées à TitlesFragment

Une fois que la disposition alternative a `TitlesFragment`été créée, il est nécessaire de mettre à jour . Lorsque l’application affiche les deux fragments `TitlesFragment` sur `PlayQuoteFragment` une activité, il faut ensuite charger l’activité dans l’activité parente. Sinon, `TitlesFragment` devrait `PlayQuoteActivity` lancer l’hôte qui le `PlayQuoteFragment`. Un drapeau boolean `TitlesFragment` aidera à déterminer quel comportement il doit utiliser. Ce drapeau sera paradé `OnActivityCreated` dans la méthode.

Tout d’abord, ajoutez une `TitlesFragment` variable d’instance en haut de la classe :

```csharp
bool showingTwoFragments;
```

Ensuite, ajoutez l’extrait de `OnActivityCreated` code suivant pour initialiser la variable : 

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

Si l’appareil est en cours `FrameLayout` d’exécution `playquote_container` en mode paysage, `showingTwoFragments` puis l’ID `true`de ressource avec sera visible sur l’écran, sera donc parasécé à . Si l’appareil est en `playquote_container` cours d’exécution en `showingTwoFragments` mode `false`portrait, alors ne sera pas sur l’écran, sera donc .

La `ShowPlayQuote` méthode devra changer la façon &ndash; dont elle affiche une citation soit dans un fragment ou lancer une nouvelle activité.  Mettre `ShowPlayQuote` à jour la méthode pour charger un fragment lors de la présentation de deux fragments, sinon il devrait lancer une activité:

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

Si l’utilisateur a sélectionné une pièce différente de celle `PlayQuoteFragment`qui est `PlayQuoteFragment` actuellement affichée dans , `playquote_container` puis un nouveau `FragmentTransaction`est créé et remplacera le contenu de la dans le contexte d’un .

### <a name="complete-code-for-titlesfragment"></a>Code complet pour TitlesFragment

Après avoir terminé toutes `TitlesFragment`les modifications précédentes à , la classe complète doit correspondre à ce code:

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

## <a name="3-changes-to-playquoteactivity"></a>3. Modifications apportées à PlayQuoteActivity

Il y a un dernier `PlayQuoteActivity` détail à prendre en charge : n’est pas nécessaire lorsque l’appareil est en mode paysage. Si l’appareil est `PlayQuoteActivity` en mode paysage, il ne doit pas être visible. Mettre `OnCreate` à `PlayQuoteActivity` jour la méthode de sorte qu’il se fermera. Ce code est la `PlayQuoteActivity.OnCreate`version finale de :

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

Cette modification ajoute un contrôle pour l’orientation de l’appareil. S’il est en `PlayQuoteActivity` mode paysage, alors se fermera.

## <a name="4-run-the-application"></a>4. Exécuter l’application

Une fois ces modifications terminées, exécutez l’application, faites pivoter l’appareil en mode paysage (si nécessaire), puis sélectionnez une pièce. La citation doit être affichée sur le même écran que la liste des pièces de théâtre :

[![Application fonctionnant sur un téléphone Android en mode paysage](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

[![Application fonctionnant sur une tablette Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)
