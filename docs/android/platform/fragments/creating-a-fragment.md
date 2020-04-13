---
title: Création d’un fragment
ms.prod: xamarin
ms.assetid: F2997242-BC29-1440-7F1A-CFC447CD73FA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 0e8d3748c7ddd337cf2f27f5b272b208e79d503a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027505"
---
# <a name="creating-a-fragment"></a>Création d’un fragment

Pour créer un fragment, une `Android.App.Fragment` classe doit `OnCreateView` hériter de la méthode, puis l’emporter. `OnCreateView`sera appelé par l’activité d’hébergement quand il est temps de `View`mettre le fragment sur l’écran, et retournera un . Un `OnCreateView` typique créera ceci `View` en gonflant un fichier de mise en page et puis en l’attachant à un récipient parent. Les caractéristiques du conteneur sont importantes car Android appliquera les paramètres de mise en page du parent à l’interface utilisateur du Fragment. L'exemple suivant illustre ce mécanisme :

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    return inflater.Inflate(Resource.Layout.Example_Fragment, container, false);
}
```

Le code ci-dessus `Resource.Layout.Example_Fragment`gonflera la vue et `ViewGroup` l’ajoutera comme une vue d’enfant sur le récipient.

> [!NOTE]
> Les sous-classes fragmentes doivent avoir un constructeur public par défaut sans argument.

## <a name="adding-a-fragment-to-an-activity"></a>Ajout d’un fragment à une activité

Il y a deux façons d’héberger un fragment à l’intérieur d’une activité :

- **Declarativement** &ndash; Fragments peuvent être utilisés `.axml` de façon déclarative dans les fichiers de mise en page en utilisant l’étiquette. `<Fragment>`

- Les fragments **programmatiques** &ndash; peuvent également être instantanés de façon dynamique en utilisant l’API de la `FragmentManager` classe.

L’utilisation programmatique par l’intermédiaire de la `FragmentManager` classe sera discutée plus tard dans ce guide.

### <a name="using-a-fragment-declaratively"></a>Utilisation d’un fragment déclarative

L’ajout d’un fragment `<fragment>` à l’intérieur de la `class` mise `android:name` en page nécessite l’utilisation de l’étiquette, puis l’identification du fragment en fournissant soit l’attribut ou l’attribut. L’extrait suivant montre comment utiliser `class` l’attribut `fragment`pour déclarer un :

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment class="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Cet extrait suivant montre comment déclarer `fragment` un `android:name` en utilisant l’attribut pour identifier la classe Fragment :

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment android:name="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Lorsque l’activité est créée, Android instantané chaque fragment spécifié dans le fichier `OnCreateView` de mise `Fragment` en page et insérer la vue qui est créée à partir de la place de l’élément.
Les fragments qui sont ajoutés de façon déclarative à une activité sont statiques et resteront sur l’activité jusqu’à ce qu’elle soit détruite; il n’est pas possible de remplacer ou d’enlever dynamiquement un tel fragment pendant la durée de vie de l’activité à laquelle il est attaché.

Chaque fragment doit se voir attribuer un identifiant unique :

- **android:id** &ndash; Comme avec d’autres éléments d’interface utilisateur dans un fichier de mise en page, il s’agit d’un ID unique.

- **android:tag** &ndash; Cet attribut est une chaîne unique.

Si aucune des deux méthodes précédentes n’est utilisée, alors le fragment assumera l’ID de la vue du conteneur. Dans l’exemple `android:id` suivant `android:tag` où ni ni `fragment_container` est fourni, Android attribuera l’ID au Fragment :

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

### <a name="package-name-case"></a>Cas de nom de paquet

Android ne permet pas les caractères majuscules dans les noms de paquets; il jettera une exception en essayant de gonfler la vue si un nom de paquet contient un caractère majuscule. Cependant, Xamarin.Android est plus indulgent, et tolérera les caractères uppercase dans l’espace de nom.

Par exemple, les deux extraits suivants fonctionneront avec Xamarin.Android. Cependant, le deuxième extrait provoquera `android.view.InflateException` un être jeté par une application Android basée sur Java pure.

```xml
<fragment class="com.example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

OR

```xml
<fragment class="Com.Example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

## <a name="fragment-lifecycle"></a>Cycle de vie fragmenté

Les fragments ont leur propre cycle de vie qui est quelque peu indépendant, mais toujours affecté par, le [cycle de vie de l’activité d’hébergement](~/android/app-fundamentals/activity-lifecycle/index.md).
Par exemple, lorsqu’une activité s’arrête, tous ses fragments associés sont mis en pause. Le diagramme suivant décrit le cycle de vie du Fragment.

[![Diagramme de flux illustrant le cycle de vie de Fragment](creating-a-fragment-images/fragment-lifecycle.png)](creating-a-fragment-images/fragment-lifecycle.png#lightbox)

### <a name="fragment-creation-lifecycle-methods"></a>Méthodes de cycle de vie de création de fragments

La liste ci-dessous montre le flux des différents rappels dans le cycle de vie d’un fragment tel qu’il est créé :

- **`OnInflate()`**&ndash; Appelé lorsque le Fragment est créé dans le cadre d’une mise en page de vue. Cela peut être appelé immédiatement après le Fragment est créé déclarativement à partir d’un fichier de mise en page XML. Le Fragment n’est pas encore associé à son activité, mais **l’activité**, **bundle**, et **AttributeSet** de la hiérarchie de vue sont passés en tant que paramètres. Cette méthode est mieux utilisée pour analyser **l’AttributSet** et pour enregistrer les attributs qui pourraient être utilisés plus tard par le Fragment.

- **`OnAttach()`**&ndash; Appelé après le fragment est associé à l’activité. C’est la première méthode à exécuter lorsque le fragment est prêt à être utilisé. En général, Fragments ne doit pas implémenter un constructeur ou remplacer le constructeur par défaut. Tous les composants requis pour le fragment doivent être parasmés dans cette méthode.

- **`OnCreate()`**&ndash; Appelé par l’activité pour créer le fragment. Lorsque cette méthode est appelée, la hiérarchie de vue de l’activité d’hébergement peut ne pas être complètement instantanée, de sorte que le fragment ne doit pas s’appuyer sur une partie de la hiérarchie de vue de l’activité jusqu’à plus tard dans le cycle de vie du Fragment. Par exemple, n’utilisez pas cette méthode pour effectuer des modifications ou des ajustements à l’interface utilisateur de l’application. C’est la première fois que le Fragment peut commencer à recueillir les données dont il a besoin. Le Fragment est en cours d’exécution dans le thread d’interface utilisateur à ce stade, afin d’éviter tout traitement prolongé, ou effectuer ce traitement sur un thread de fond. Cette méthode peut être ignorée si **SetRetainInstance (vrai)** est appelé.
    Cette alternative sera décrite plus en détail ci-dessous.

- **`OnCreateView()`**&ndash; Crée la vue pour le Fragment.
    Cette méthode est appelée une fois la méthode **OnCreate()** de l’activité terminée. À ce stade, il est sûr d’interagir avec la hiérarchie de vue de l’activité. Cette méthode devrait retourner la vue qui sera utilisée par le Fragment.

- **`OnActivityCreated()`**&ndash; Appelé après **Activity.OnCreate** a été complété par l’activité d’hébergement.
    Les modifications finales apportées à l’interface utilisateur doivent être effectuées à ce moment-là.

- **`OnStart()`**&ndash; Appelé après la reprise de l’activité de confinement. Cela rend le fragment visible à l’utilisateur. Dans de nombreux cas, le Fragment contiendra du code qui serait autrement dans la méthode **OnStart()** d’une activité.

- **`OnResume()`**&ndash; C’est la dernière méthode appelée avant que l’utilisateur puisse interagir avec le Fragment. Un exemple du type de code qui devrait être exécuté dans cette méthode serait d’activer les fonctionnalités d’un appareil avec lequel l’utilisateur peut interagir, comme la caméra que les services de localisation. Des services comme ceux-ci peuvent causer un vidage excessif de la batterie, cependant, et une application devrait minimiser leur utilisation pour préserver la durée de vie de la batterie.

### <a name="fragment-destruction-lifecycle-methods"></a>Méthodes de cycle de vie de destruction de fragments

La liste suivante explique que les méthodes de cycle de vie qui sont appelées comme un fragment est en cours de destruction:

- **`OnPause()`**&ndash; L’utilisateur n’est plus en mesure d’interagir avec le Fragment. Cette situation existe parce qu’une autre opération Fragment modifie ce fragment, ou l’activité d’hébergement est interrompue. Il est possible que l’activité hébergeant ce fragment soit encore visible, c’est-à-dire que l’activité mise au point est partiellement transparente ou n’occupe pas le plein écran. Lorsque cette méthode devient active, c’est la première indication que l’utilisateur quitte le Fragment. Le Fragment doit enregistrer tout changement.

- **`OnStop()`**&ndash; Le Fragment n’est plus visible. L’activité hôte peut être arrêtée, ou une opération Fragment la modifie dans l’activité. Ce rappel sert le même but que **Activity.OnStop**.

- **`OnDestroyView()`**&ndash; Cette méthode est appelée à nettoyer les ressources associées à la vue. C’est ce qu’on appelle lorsque la vue associée au Fragment a été détruite.

- **`OnDestroy()`**&ndash; Cette méthode est appelée lorsque le Fragment n’est plus utilisé. Il est toujours associé à l’activité, mais le fragment n’est plus fonctionnel. Cette méthode doit libérer toutes les ressources qui sont utilisées par le Fragment, comme un [**SurfaceView**](xref:Android.Views.SurfaceView) qui pourrait être utilisé pour une caméra. Cette méthode peut être ignorée si **SetRetainInstance (vrai)** est appelé. Cette alternative sera décrite plus en détail ci-dessous.

- **`OnDetach()`**&ndash; Cette méthode s’appelle juste avant que le Fragment ne soit plus associé à l’activité. La hiérarchie de vue du Fragment n’existe plus, et toutes les ressources utilisées par le Fragment doivent être libérées à ce stade.

### <a name="using-setretaininstance"></a>Utilisation de SetRetainInstance

Il est possible pour un fragment de spécifier qu’il ne doit pas être complètement détruit si l’activité est recréée. La `Fragment` classe fournit `SetRetainInstance` la méthode à cet effet. Si `true` elle est transmise à cette méthode, lorsque l’activité est redémarrée, la même instance du fragment sera utilisée. Si cela se produit, alors toutes les `OnCreate` méthodes `OnDestroy` de rappel seront invoquées, sauf les rappels et le cycle de vie. Ce processus est illustré dans le diagramme du cycle de vie indiqué ci-dessus (par les lignes pointillées vertes).

## <a name="fragment-state-management"></a>Gestion de l’État fragment

Les fragments peuvent sauver et restaurer leur état pendant `Bundle`le cycle de vie de Fragment en utilisant un exemple de . Le Bundle permet à un fragment d’enregistrer des données en tant que paires de clés/valeurs et est utile pour des données simples qui ne nécessitent pas beaucoup de mémoire. Un fragment peut sauver son `OnSaveInstanceState`état avec un appel à :

```csharp
public override void OnSaveInstanceState(Bundle outState)
{
    base.OnSaveInstanceState(outState);
    outState.PutInt("current_choice", _currentCheckPosition);
}
```

Lorsqu’une nouvelle instance d’un fragment est `Bundle` créée, l’état enregistré `OnCreate` `OnCreateView`dans `OnActivityCreated` le deviendra disponible pour la nouvelle instance par le , , et les méthodes de la nouvelle instance.
L’échantillon suivant montre comment `current_choice` récupérer `Bundle`la valeur de la :

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

Le dépassement `OnSaveInstanceState` est un mécanisme approprié pour enregistrer les données transitoires `current_choice` dans un fragment entre les changements d’orientation, comme la valeur dans l’exemple ci-dessus. Toutefois, la mise `OnSaveInstanceState` en œuvre par défaut prend soin d’enregistrer des données transitoires dans l’interface utilisateur pour chaque vue qui a une pièce d’identité assignée. Par exemple, regardez une application `EditText` qui a un élément défini dans XML comme suit:

```xml
<EditText android:id="@+id/myText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"/>
```

Étant `EditText` donné que `id` le contrôle a un assigné, le `OnSaveInstanceState` fragment enregistre automatiquement les données dans le widget lorsqu’il est appelé.

### <a name="bundle-limitations"></a>Limitations de faisceau

Bien `OnSaveInstanceState` que l’utilisation il soit facile d’enregistrer des données transitoires, l’utilisation de cette méthode a quelques limites:

- Si le fragment n’est pas ajouté à la pile arrière, alors son état ne sera pas restauré lorsque l’utilisateur appuie sur le bouton **Back.**

- Lorsque le bundle est utilisé pour enregistrer des données, ces données sont sérialisées. Cela peut entraîner des retards de traitement.

## <a name="contributing-to-the-menu"></a>Contribuer au menu

Les fragments peuvent contribuer à la carte de leur activité d’hébergement.
Une activité gère d’abord les éléments du menu. Si l’activité n’a pas de gestionnaire, l’événement sera transmis au Fragment, qui s’en occupera ensuite.

Pour ajouter des éléments au menu de l’activité, un fragment doit faire deux choses.
Tout d’abord, le `OnCreateOptionsMenu` Fragment doit implémenter la méthode et placer ses éléments dans le menu, comme indiqué dans le code suivant :

```csharp
public override void OnCreateOptionsMenu(IMenu menu, MenuInflater menuInflater)
{
    menuInflater.Inflate(Resource.Menu.menu_fragment_vehicle_list, menu);
    base.OnCreateOptionsMenu(menu, menuInflater);
}
```

Le menu de l’extrait de code précédent est gonflé à partir `menu_fragment_vehicle_list.xml`du XML suivant, situé dans le fichier :

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/add_vehicle"
        android:icon="@drawable/ic_menu_add_data"
        android:title="@string/add_vehicle" />
</menu>
```

Ensuite, le Fragment `SetHasOptionsMenu(true)`doit appeler . L’appel à cette méthode annonce à Android que le Fragment a des éléments de menu pour contribuer au menu d’option. À moins que l’appel à cette méthode ne soit effectué, les éléments du menu du Fragment ne seront pas ajoutés au menu d’option de l’activité. Ceci est généralement fait dans `OnCreate()`la méthode du cycle de vie , comme indiqué dans l’extrait de code suivant:

```csharp
public override void OnCreate(Bundle savedState)
{
    base.OnCreate(savedState);
    SetHasOptionsMenu(true);
}
```

L’écran suivant montre à quoi ressemblerait ce menu :

[![Exemple de capture d’écran de l’application My Trips affichant les éléments du menu](creating-a-fragment-images/fragment-menu-example.png)](creating-a-fragment-images/fragment-menu-example.png#lightbox)
