---
title: Dispositions avec onglets avec barre
description: Ce guide présente et explique comment utiliser les API barre pour créer une interface utilisateur avec onglets dans une application Xamarin. Android.
ms.prod: xamarin
ms.assetid: B7E60AAF-BDA5-4305-9000-675F0438734D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 4673b178512a886e5fdb154c57c8d659276bb392
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522327"
---
# <a name="tabbed-layouts-with-the-actionbar"></a>Dispositions avec onglets avec barre

_Ce guide présente et explique comment utiliser les API barre pour créer une interface utilisateur avec onglets dans une application Xamarin. Android._


## <a name="overview"></a>Présentation

La barre d’action est un modèle d’interface utilisateur Android qui est utilisé pour fournir une interface utilisateur cohérente pour les fonctionnalités clés telles que les onglets, l’identité de l’application, les menus et la recherche. Dans Android 3,0 (API de niveau 11), Google a introduit les API barre sur la plateforme Android. Les API barre introduisent des thèmes d’interface utilisateur pour fournir une apparence et une convivialité cohérentes et des classes qui autorisent les interfaces utilisateur avec onglet. Ce guide explique comment ajouter des onglets Barre d’action à une application Xamarin. Android. Il explique également comment utiliser la bibliothèque de prise en charge Android v7 pour rétroporter les onglets barre aux applications Xamarin. Android ciblant Android 2,1 pour Android 2,3. 

Notez que `Toolbar` est un composant de barre d’action plus récent et plus généralisé que vous devez utiliser `ActionBar` à`Toolbar` la place de ( `ActionBar`a été conçu pour remplacer). Pour plus d’informations, consultez [Toolbar](~/android/user-interface/controls/tool-bar/index.md). 



## <a name="requirements"></a>Configuration requise

Toute application Xamarin. Android qui cible le niveau d’API 11 (Android 3,0) ou une version ultérieure a accès aux API barre dans le cadre des API Android natives. 

Certaines API barre ont été reportées vers l’API de niveau 7 (Android 2,1) et sont disponibles via la [bibliothèque AppCompat v7](https://developer.android.com/tools/support-library/features.html#v7-appcompat), qui est mise à la disposition des applications Xamarin. Android via la [bibliothèque de prise en charge Xamarin Android-v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) package.



## <a name="introducing-tabs-in-the-actionbar"></a>Présentation des onglets dans le barre

La barre d’action tente d’afficher tous les onglets simultanément et de réduire la taille de tous les onglets en fonction de la largeur de l’étiquette d’onglet la plus large. Cela est illustré par la capture d’écran suivante: 

![Exemple de capture d’écran de barre avec tous les onglets de taille égale affichés](with-action-bar-images/image1.png)

Lorsque la barre ne peut pas afficher tous les onglets, elle configure les onglets dans une vue à défilement horizontal. L’utilisateur peut faire défiler vers la gauche ou vers la droite pour voir les autres onglets. Cette capture d’écran de Google Play présente un exemple: 

![Exemple de capture d’écran des onglets dans une vue à défilement horizontal](with-action-bar-images/image2.png)

Chaque onglet de la barre d’action doit être associé à un [*fragment*](~/android/platform/fragments/index.md). Lorsque l’utilisateur sélectionne un onglet, l’application affiche le fragment associé à l’onglet. Barre n’est pas responsable de l’affichage du fragment approprié à l’utilisateur. Au lieu de cela, le barre avertit une application des modifications d’État dans un onglet par le biais d’une classe qui implémente l’interface barre. ITabListener. Cette interface fournit trois méthodes de rappel que Android appellera quand l’état de l’onglet change: 

- **OnTabSelected** : cette méthode est appelée lorsque l’utilisateur sélectionne l’onglet. Il doit afficher le fragment.

- **OnTabReselected** : cette méthode est appelée lorsque l’onglet est déjà sélectionné, mais est de nouveau sélectionné par l’utilisateur. Ce rappel est généralement utilisé pour actualiser/mettre à jour le fragment affiché.

- **OnTabUnselected** : cette méthode est appelée lorsque l’utilisateur sélectionne un autre onglet. Ce rappel est utilisé pour enregistrer l’État dans le fragment affiché avant qu’il ne se ferme.

Xamarin. Android encapsule les `ActionBar.ITabListener` avec des événements sur `ActionBar.Tab` la classe. Les applications peuvent assigner des gestionnaires d’événements à un ou plusieurs de ces événements. Il existe trois événements (un pour chaque méthode dans `ActionBar.ITabListener`) qu’un onglet de barre d’action sera déclenché: 

- TabSelected
- TabReselected
- TabUnselected



### <a name="adding-tabs-to-the-actionbar"></a>Ajout d’onglets à barre

Le barre est natif pour Android 3,0 (niveau d’API 11) et supérieur et est disponible pour toute application Xamarin. Android qui cible cette API au minimum. 

Les étapes suivantes expliquent comment ajouter des onglets barre à une activité Android: 

1. Dans la `OnCreate` méthode d’une activité &ndash; &ndash; *avant l’initialisation des widgets d’interface utilisateur* , une application `NavigationMode` doit affecter `ActionBar` à `ActionBar.NavigationModeTabs` la valeur, comme indiqué dans cet extrait de code:

   ```csharp
   ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
   SetContentView(Resource.Layout.Main);
   ```

2. Créez un nouvel onglet à `ActionBar.NewTab()`l’aide de.

3. Assignez des gestionnaires d’événements ou `ActionBar.ITabListener` fournissez une implémentation personnalisée qui répondra aux événements déclenchés lorsque l’utilisateur interagit avec les onglets barre.

4. Ajoutez l’onglet créé à l’étape précédente à `ActionBar`.


Le code suivant est un exemple d’utilisation de ces étapes pour ajouter des onglets à une application qui utilise des gestionnaires d’événements pour répondre aux changements d’État: 

```csharp
protected override void OnCreate(Bundle bundle)
{
    ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
    SetContentView(Resource.Layout.Main);

    ActionBar.Tab tab = ActionBar.NewTab();
    tab.SetText(Resources.GetString(Resource.String.tab1_text));
    tab.SetIcon(Resource.Drawable.tab1_icon);
    tab.TabSelected += (sender, args) => {
                           // Do something when tab is selected
                       };
    ActionBar.AddTab(tab);

    tab = ActionBar.NewTab();
    tab.SetText(Resources.GetString(Resource.String.tab2_text));
    tab.SetIcon(Resource.Drawable.tab2_icon);
    tab.TabSelected += (sender, args) => {
                           // Do something when tab is selected
                       };
    ActionBar.AddTab(tab);
}
```


#### <a name="event-handlers-vs-actionbaritablistener"></a>Gestionnaires d’événements et barre. ITabListener

Les applications doivent utiliser des gestionnaires d' `ActionBar.ITabListener` événements et pour différents scénarios. Les gestionnaires d’événements offrent un certain niveau de commodité syntaxique. ils vous évitent d’avoir à créer une classe et `ActionBar.ITabListener`à implémenter. Cette facilité est &ndash; Xamarin. Android effectue cette transformation pour vous, en créant une classe et en implémentant `ActionBar.ITabListener` pour vous. C’est parfait lorsqu’une application a un nombre limité d’onglets. 

Lorsque vous traitez un grand nombre d’onglets ou que vous partagez des fonctionnalités communes entre les onglets barre, il peut être plus efficace en termes de mémoire et de `ActionBar.ITabListener`performances pour créer une classe personnalisée qui implémente et partage une seule instance de la classe. Cela permet de réduire le nombre d’GREF qu’une application Xamarin. Android utilise. 



### <a name="backwards-compatibility-for-older-devices"></a>Compatibilité descendante pour les appareils plus anciens

La [bibliothèque de prise en charge Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) ports Back AppCompat barre onglets à Android 2,1 (niveau d’API 7). Les onglets sont accessibles dans une application Xamarin. Android une fois que ce composant a été ajouté au projet.

Pour utiliser barre, une activité doit effectuer une sous `ActionBarActivity` -classe et utiliser le thème AppCompat comme indiqué dans l’extrait de code suivant:

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity: ActionBarActivity
```

Une activité peut obtenir une référence à son barre à partir `ActionBarActivity.SupportingActionBar` de la propriété. L’extrait de code suivant illustre un exemple de configuration de barre dans une activité:

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity : ActionBarActivity, ActionBar.ITabListener
{
    static readonly string Tag = "ActionBarTabsSupport";

    public void OnTabReselected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Optionally refresh/update the displayed tab.
        Log.Debug(Tag, "The tab {0} was re-selected.", tab.Text);
    }

    public void OnTabSelected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Display the fragment the user should see
        Log.Debug(Tag, "The tab {0} has been selected.", tab.Text);
    }

    public void OnTabUnselected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Save any state in the displayed fragment.
        Log.Debug(Tag, "The tab {0} as been unselected.", tab.Text);
    }

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SupportActionBar.NavigationMode = ActionBar.NavigationModeTabs;
        SetContentView(Resource.Layout.Main);
    }

    void AddTabToActionBar(int labelResourceId, int iconResourceId)
    {
        ActionBar.Tab tab = SupportActionBar.NewTab()
                                            .SetText(labelResourceId)
                                            .SetIcon(iconResourceId)
                                            .SetTabListener(this);
        SupportActionBar.AddTab(tab);
    }
}
```


## <a name="summary"></a>Récapitulatif

Dans ce guide, nous avons abordé la création d’une interface utilisateur avec onglets dans Xamarin. Android à l’aide de barre. Nous avons abordé l’ajout d’onglets au barre et la manière dont une activité peut interagir avec les `ActionBar.ITabListener` événements de tabulation via l’interface. Nous avons également vu comment le package de la bibliothèque de prise en charge Android v7 a utilisé les onglets barre dans les versions antérieures d’Android. 


## <a name="related-links"></a>Liens associés

- [ActionBarTabs (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-actionbartabs)
- [Barre d’outils](~/android/user-interface/controls/tool-bar/index.md)
- [Fragments](~/android/platform/fragments/index.md)
- [ActionBar](https://developer.android.com/guide/topics/ui/actionbar.html)
- [ActionBarActivity](https://developer.android.com/reference/android/support/v7/app/ActionBarActivity.html)
- [Modèle de Barre d’action](https://developer.android.com/design/patterns/actionbar.html)
- [Android v7 AppCompat](https://developer.android.com/tools/support-library/features.html#v7-appcompat)
- [Bibliothèque de prise en charge Xamarin. Android (package NuGet AppCompat)](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
