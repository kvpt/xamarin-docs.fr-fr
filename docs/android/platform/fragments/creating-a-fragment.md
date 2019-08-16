---
title: Création d’un fragment
ms.prod: xamarin
ms.assetid: F2997242-BC29-1440-7F1A-CFC447CD73FA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: 1948c700827f1cc235de5857cde9a2a149af8412
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524363"
---
# <a name="creating-a-fragment"></a>Création d’un fragment

Pour créer un fragment, une classe doit hériter `Android.App.Fragment` de la méthode, puis `OnCreateView` se substituer à celle-ci. `OnCreateView`est appelé par l’activité d’hébergement lorsqu’il est temps de placer le fragment à l’écran et retourne un `View`. En général `OnCreateView` , cela `View` crée un fichier de disposition, puis l’attache à un conteneur parent. Les caractéristiques du conteneur sont importantes car Android applique les paramètres de disposition du parent à l’interface utilisateur du fragment. L'exemple suivant illustre ce comportement :

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    return inflater.Inflate(Resource.Layout.Example_Fragment, container, false);
}
```

Le code ci-dessus gonfle `Resource.Layout.Example_Fragment`la vue et l’ajoute en tant que vue enfant `ViewGroup` au conteneur.


> [!NOTE]
> Les sous-classes de fragments doivent avoir un constructeur sans argument public par défaut.

## <a name="adding-a-fragment-to-an-activity"></a>Ajout d’un fragment à une activité

Il existe deux façons d’héberger un fragment dans une activité:

- Par **déclaration** Les fragments peuvent être utilisés de `.axml` façon déclarative dans les fichiers `<Fragment>` de disposition à l’aide de la balise. &ndash;

- **Par programmation** Les fragments peuvent également être instanciés de manière dynamique à `FragmentManager` l’aide de l’API de la classe. &ndash;

L’utilisation par programmation `FragmentManager` via la classe sera abordée plus loin dans ce guide.

### <a name="using-a-fragment-declaratively"></a>Utilisation d’un fragment de façon déclarative

L’ajout d’un fragment à l’intérieur de `<fragment>` la disposition requiert l’utilisation de la balise, `class` puis l’identification `android:name` du fragment en fournissant l’attribut ou l’attribut. L’extrait de code suivant montre comment utiliser `class` l’attribut pour déclarer `fragment`un:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment class="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Cet extrait de code suivant montre comment déclarer `fragment` un en utilisant `android:name` l’attribut pour identifier la classe de fragment:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment android:name="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Lorsque l’activité est créée, Android instancie chaque fragment spécifié dans le fichier de disposition et insère la vue créée à partir `OnCreateView` de à la place de l' `Fragment` élément.
Les fragments qui sont ajoutés de façon déclarative à une activité sont statiques et restent sur l’activité jusqu’à ce qu’il soit détruit; Il n’est pas possible de remplacer ou de supprimer dynamiquement ce fragment pendant la durée de vie de l’activité à laquelle il est attaché.

Un identificateur unique doit être assigné à chaque fragment:

- **Android: ID** &ndash; Comme avec d’autres éléments d’interface utilisateur dans un fichier de disposition, il s’agit d’un ID unique.

- **Android: balise** &ndash; Cet attribut est une chaîne unique.

Si aucune des deux méthodes précédentes n’est utilisée, le fragment supposera l’ID de la vue de conteneur. Dans l’exemple suivant où `android:id` ni ni `android:tag` n’est fourni, Android attribue l’ID `fragment_container` au fragment:

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                android:id="+@id/fragment_container"
                android:orientation="horizontal"
                android:layout_width="match_parent"
                android:layout_height="match_parent">

        <fragment class="com.example.android.apis.app.TitlesFragment"
                android:layout_width="match_parent"
                android:layout_height="match_parent" />
</LinearLayout>
```

### <a name="package-name-case"></a>Casse du nom de package

Android n’autorise pas les caractères majuscules dans les noms de packages; une exception est levée lors de la tentative d’augmentation de la vue si un nom de package contient un caractère majuscule. Toutefois, Xamarin. Android est plus indulgent avec et tolère les caractères majuscules dans l’espace de noms.

Par exemple, les deux extraits de code suivants fonctionnent avec Xamarin. Android. Toutefois, le second extrait de code provoquera la levée d’une `android.view.InflateException` application Android basée sur Java.

```xml
<fragment class="com.example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

Ou

```xml
<fragment class="Com.Example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```


## <a name="fragment-lifecycle"></a>Cycle de vie des fragments

Les fragments ont leur propre cycle de vie qui est quelque peu indépendant de, mais toujours affecté par, le [cycle de vie de l’activité d’hébergement](~/android/app-fundamentals/activity-lifecycle/index.md).
Par exemple, lorsqu’une activité est suspendue, tous ses fragments associés sont suspendus. Le diagramme suivant présente le cycle de vie du fragment.

[![Diagramme de Flow illustrant le cycle de vie des fragments](creating-a-fragment-images/fragment-lifecycle.png)](creating-a-fragment-images/fragment-lifecycle.png#lightbox)


### <a name="fragment-creation-lifecycle-methods"></a>Méthodes de cycle de vie de création de fragments

La liste ci-dessous montre le déroulement des différents rappels dans le cycle de vie d’un fragment au fur et à mesure de leur création:

- **`OnInflate()`** &ndash; Appelé lorsque le fragment est créé dans le cadre d’une mise en page de vue. Cela peut être appelé immédiatement après que le fragment soit créé de manière déclarative à partir d’un fichier de disposition XML. Le fragment n’est pas encore associé à son activité, mais l' **activité**, le **Bundle**et le **AttributeSet** de la hiérarchie de vue sont passés comme paramètres. Cette méthode est la meilleure pour l’analyse de **AttributeSet** et pour l’enregistrement des attributs qui peuvent être utilisés ultérieurement par le fragment.

- **`OnAttach()`** &ndash; Appelée une fois que le fragment est associé à l’activité. Il s’agit de la première méthode à exécuter lorsque le fragment est prêt à être utilisé. En général, les fragments ne doivent pas implémenter de constructeur ou substituer le constructeur par défaut. Tous les composants requis pour le fragment doivent être initialisés dans cette méthode.

- **`OnCreate()`** &ndash; Appelé par l’activité pour créer le fragment. Lorsque cette méthode est appelée, la hiérarchie d’affichage de l’activité d’hébergement ne peut pas être complètement instanciée. par conséquent, le fragment ne doit pas reposer sur les parties de la hiérarchie d’affichage de l’activité jusqu’à la fin du cycle de vie du fragment. Par exemple, n’utilisez pas cette méthode pour effectuer des ajustements ou des ajustements de l’interface utilisateur de l’application. Il s’agit de la première heure à laquelle le fragment peut commencer à collecter les données dont il a besoin. Le fragment s’exécutant dans le thread d’interface utilisateur à ce stade, évitez tout traitement long ou effectuez ce traitement sur un thread d’arrière-plan. Cette méthode peut être ignorée si **SetRetainInstance (true)** est appelé.
    Cette alternative sera décrite plus en détail ci-dessous.

- **`OnCreateView()`** &ndash; Crée la vue pour le fragment.
    Cette méthode est appelée une fois la méthode **OnCreate ()** de l’activité terminée. À ce stade, il est possible d’interagir en toute sécurité avec la hiérarchie d’affichage de l’activité. Cette méthode doit retourner la vue qui sera utilisée par le fragment.

- **`OnActivityCreated()`** Appelé après **Activity. OnCreate** a été effectué par l’activité d’hébergement. &ndash;
    Les dernières modifications apportées à l’interface utilisateur doivent être effectuées pour l’instant.

- **`OnStart()`** &ndash; Appelé après la reprise de l’activité contenante. Cela rend le fragment visible pour l’utilisateur. Dans de nombreux cas, le fragment contient du code qui serait autrement dans la méthode **OnStart ()** d’une activité.

- **`OnResume()`** &ndash; Il s’agit de la dernière méthode appelée avant que l’utilisateur puisse interagir avec le fragment. Un exemple du type de code qui doit être exécuté dans cette méthode est l’activation des fonctionnalités d’un appareil avec lequel l’utilisateur peut interagir, par exemple l’appareil photo que l’emplacement héberge les services. Les services tels que ceux-ci peuvent cependant entraîner un drainage excessif de la batterie, et une application doit réduire leur utilisation pour préserver la durée de vie de la batterie.


### <a name="fragment-destruction-lifecycle-methods"></a>Méthodes de cycle de vie de destruction de fragment

La liste suivante explique les méthodes de cycle de vie qui sont appelées comme un fragment est en cours de destruction:

- **`OnPause()`** &ndash; L’utilisateur n’est plus en mesure d’interagir avec le fragment. Cette situation est due au fait qu’une autre opération de fragment modifie ce fragment ou lorsque l’activité d’hébergement est suspendue. Il est possible que l’activité qui héberge ce fragment soit toujours visible, autrement dit, que l’activité active est partiellement transparente ou n’occupe pas le plein écran. Quand cette méthode devient active, il s’agit de la première indication que l’utilisateur quitte le fragment. Le fragment doit enregistrer toutes les modifications.

- **`OnStop()`** &ndash; Le fragment n’est plus visible. L’activité de l’hôte peut être arrêtée ou une opération de fragment est modifiée dans l’activité. Ce rappel a le même objectif que **Activity. OnStop**.

- **`OnDestroyView()`** &ndash; Cette méthode est appelée pour nettoyer les ressources associées à la vue. Cette méthode est appelée lorsque la vue associée au fragment a été détruite.

- **`OnDestroy()`** &ndash; Cette méthode est appelée lorsque le fragment n’est plus utilisé. Elle est toujours associée à l’activité, mais le fragment n’est plus fonctionnel. Cette méthode doit libérer toutes les ressources utilisées par le fragment, telles qu’un [**SurfaceView**](xref:Android.Views.SurfaceView) qui peut être utilisé pour un appareil photo. Cette méthode peut être ignorée si **SetRetainInstance (true)** est appelé. Cette alternative sera décrite plus en détail ci-dessous.

- **`OnDetach()`** &ndash; Cette méthode est appelée juste avant que le fragment ne soit plus associé à l’activité. La hiérarchie d’affichage du fragment n’existe plus et toutes les ressources utilisées par le fragment doivent être libérées à ce stade.


### <a name="using-setretaininstance"></a>Utilisation de SetRetainInstance

Il est possible pour un fragment de spécifier qu’il ne doit pas être complètement détruit si l’activité est recréée. La `Fragment` classe fournit la méthode `SetRetainInstance` à cet effet. Si `true` est passé à cette méthode, lorsque l’activité est redémarrée, la même instance du fragment sera utilisée. Si cela se produit, toutes les méthodes de rappel sont appelées, `OnCreate` à `OnDestroy` l’exception des rappels de cycle de vie et. Ce processus est illustré dans le diagramme de cycle de vie illustré ci-dessus (par les lignes en pointillés vertes).


## <a name="fragment-state-management"></a>Gestion de l’état des fragments

Les fragments peuvent enregistrer et restaurer leur état pendant le cycle de vie du fragment à l' `Bundle`aide d’une instance de. Le bundle permet à un fragment d’enregistrer des données en tant que paires clé/valeur et est utile pour les données simples qui ne nécessitent pas très de mémoire. Un fragment peut enregistrer son état avec un appel à `OnSaveInstanceState`:

```csharp
public override void OnSaveInstanceState(Bundle outState)
{
    base.OnSaveInstanceState(outState);
    outState.PutInt("current_choice", _currentCheckPosition);
}
```

Quand une nouvelle instance d’un fragment est créée, l’état enregistré dans le `Bundle` devient disponible pour la nouvelle instance via les `OnCreate`méthodes, `OnCreateView`et `OnActivityCreated` de la nouvelle instance.
L’exemple suivant montre comment récupérer la valeur `current_choice` `Bundle`à partir de:

```csharp
public override void OnActivityCreated(Bundle savedInstanceState)
{
    base.OnActivityCreated(savedInstanceState);
    if (savedInstanceState != null)
    {
        _currentCheckPosition = savedInstanceState.GetInt("current_choice", 0);
    }
}
```

La substitution `OnSaveInstanceState` est un mécanisme approprié pour enregistrer des données temporaires dans un fragment à travers des modifications d’orientation `current_choice` , telles que la valeur de l’exemple ci-dessus. Toutefois, l’implémentation par défaut `OnSaveInstanceState` de s’occupe de l’enregistrement des données temporaires dans l’interface utilisateur pour chaque vue à laquelle un ID est affecté. Par exemple, examinez une application qui possède un `EditText` élément défini dans XML comme suit:

```xml
<EditText android:id="@+id/myText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"/>
```

Étant donné `EditText` que le contrôle `id` a un assigné, le fragment enregistre automatiquement les données dans le `OnSaveInstanceState` widget quand est appelé.


### <a name="bundle-limitations"></a>Limitations de Bundle

Bien que `OnSaveInstanceState` l’utilisation de facilite l’enregistrement de données temporaires, l’utilisation de cette méthode présente certaines limitations:

- Si le fragment n’est pas ajouté à la pile de retour, son état n’est pas restauré lorsque l’utilisateur appuie sur le bouton **précédent** .

- Lorsque le bundle est utilisé pour enregistrer des données, ces données sont sérialisées. Cela peut entraîner des retards de traitement.


## <a name="contributing-to-the-menu"></a>Contribution au menu

Les fragments peuvent contribuer aux éléments dans le menu de leur activité d’hébergement.
Une activité gère d’abord les éléments de menu. Si l’activité n’a pas de gestionnaire, l’événement est passé au fragment, qui le gérera ensuite.

Pour ajouter des éléments au menu de l’activité, un fragment doit faire deux choses.
Tout d’abord, le fragment doit implémenter la méthode `OnCreateOptionsMenu` et placer ses éléments dans le menu, comme indiqué dans le code suivant:

```csharp
public override void OnCreateOptionsMenu(IMenu menu, MenuInflater menuInflater)
{
    menuInflater.Inflate(Resource.Menu.menu_fragment_vehicle_list, menu);
    base.OnCreateOptionsMenu(menu, menuInflater);
}
```

Le menu dans l’extrait de code précédent est augmenté du code XML suivant, situé dans le fichier `menu_fragment_vehicle_list.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/add_vehicle"
        android:icon="@drawable/ic_menu_add_data"
        android:title="@string/add_vehicle" />
</menu>
```

Ensuite, le fragment doit appeler `SetHasOptionsMenu(true)`. L’appel à cette méthode annonce à Android que le fragment a des éléments de menu pour contribuer au menu d’options. À moins que l’appel à cette méthode ne soit effectué, les éléments de menu du fragment ne sont pas ajoutés au menu d’options de l’activité. Cela s’effectue généralement dans la méthode `OnCreate()`Lifecycle, comme indiqué dans l’extrait de code suivant:

```csharp
public override void OnCreate(Bundle savedState)
{
    base.OnCreate(savedState);
    SetHasOptionsMenu(true);
}
```

L’écran suivant montre l’apparence de ce menu:

[![Exemple de capture d’écran de l’application mes TRIPS affichant les éléments de menu](creating-a-fragment-images/fragment-menu-example.png)](creating-a-fragment-images/fragment-menu-example.png#lightbox)
