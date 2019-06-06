---
title: Cycle de vie des activités
description: Les activités sont un bloc de construction fondamental des applications Android, et ils peuvent exister dans un nombre d’états différents. Le cycle de vie d’activité commence par instanciation se termine par la destruction et inclut de nombreux états entre les deux. Lorsqu’une activité change d’état, la méthode d’événement du cycle de vie appropriée est appelée pour informer de l’activité de la modification d’état imminente et ce qui lui permet d’exécuter du code pour s’adapter à cette modification. Cet article examine le cycle de vie des activités et explique la responsabilité qu’une activité a lors de chacune de ces modifications d’état à faire partie d’une application fiable se comportant bien.
ms.prod: xamarin
ms.assetid: 05B34788-F2D2-4347-B66B-40AFD7B1D167
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: 48ff30397b2592dd2c4dbd445987392d78ced6f3
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66740776"
---
# <a name="activity-lifecycle"></a>Cycle de vie des activités

_Les activités sont un bloc de construction fondamental des applications Android, et ils peuvent exister dans un nombre d’états différents. Le cycle de vie d’activité commence par instanciation se termine par la destruction et inclut de nombreux états entre les deux. Lorsqu’une activité change d’état, la méthode d’événement du cycle de vie appropriée est appelée pour informer de l’activité de la modification d’état imminente et ce qui lui permet d’exécuter du code pour s’adapter à cette modification. Cet article examine le cycle de vie des activités et explique la responsabilité qu’une activité a lors de chacune de ces modifications d’état à faire partie d’une application fiable se comportant bien._

## <a name="activity-lifecycle-overview"></a>Vue d’ensemble du cycle de vie des activités

Les activités sont un concept de programmation inhabituel spécifique à Android. Dans le développement d’applications traditionnelles, il existe généralement une méthode main statique, qui est exécutée pour lancer l’application. Avec Android, toutefois, choses sont différentes ; Applications Android peuvent être lancées par le biais de toute activité enregistrée dans une application. Dans la pratique, la plupart des applications n’ont une activité spécifique qui est spécifiée comme point d’entrée de l’application. Toutefois, si une application se bloque, ou se termine par le système d’exploitation, le système d’exploitation peut tenter de redémarrer l’application à la dernière activité ouverte ou n’importe où dans la pile de l’activité précédente.
En outre, le système d’exploitation peut suspendre les activités lorsqu’ils ne sont pas actives et les récupérer si elle manque de mémoire. Une attention particulière doit être effectuée pour autoriser l’application restaurer correctement son état dans le cas où une activité est redémarrée, surtout si qui activité dépend des données à partir d’activités précédentes.

Le cycle de vie d’activité est implémentée en tant que collection d’appels de méthodes, le système d’exploitation tout au long du cycle de vie d’une activité. Ces méthodes permettent aux développeurs d’implémenter les fonctionnalités qui sont nécessaires pour satisfaire les exigences de gestion de ressources et d’état de leurs applications.

Il est extrêmement important pour le développeur d’applications analyser les exigences de chaque activité pour déterminer les méthodes exposées par le cycle de vie d’activité doivent être implémentées. Dans le cas peut entraîner une instabilité de l’application, des défaillances, encombrement de ressource et peut-être même sous-jacent une instabilité du système d’exploitation.

Ce chapitre examine le cycle de vie d’activité en détail, y compris :

-  États d’activité
-  Méthodes de cycle de vie
-  En conservant l’état d’une Application


Cette section inclut également un [procédure pas à pas](~/android/app-fundamentals/activity-lifecycle/saving-state.md) qui fournissent des exemples pratiques sur la façon d’enregistrer efficacement l’état pendant le cycle de vie d’activité. À la fin de ce chapitre, vous devez avoir une compréhension de l’activité du cycle de vie et sa prise en charge dans une application Android.

## <a name="activity-lifecycle"></a>Cycle de vie des activités

Le cycle de vie d’une activité Android comprend un ensemble de méthodes exposées au sein de la classe d’activité qui fournissent au développeur avec une infrastructure de gestion de ressources. Cette infrastructure permet aux développeurs de satisfaire les besoins de gestion d’état unique de chaque activité au sein d’une application et de gérer correctement la gestion des ressources.

### <a name="activity-states"></a>États d’activité

Le système d’exploitation Android arbitre les activités en fonction de leur état. Cela permet à Android d’identifier les activités qui ne sont plus en cours d’utilisation, ce qui permet de récupérer des ressources mémoire et le système d’exploitation. Le diagramme suivant illustre les États de qu'une activité peut passer par pendant sa durée de vie :

[![Diagramme d’activités États](images/image1-sml.png)](images/image1.png#lightbox)

Ces États peuvent être classées en 4 groupes principaux comme suit :

1.  *Active ou en cours d’exécution* &ndash; activités sont considérées comme active ou en cours d’exécution si elles se trouvent dans le premier plan, également appelées le haut de la pile de l’activité. Cela est considéré comme l’activité de priorité la plus élevée dans Android et par conséquent est uniquement supprimé par le système d’exploitation dans des situations extrêmes, tels que si l’activité tente d’utiliser plus de mémoire que sont disponible sur l’appareil comme cela peut entraîner l’interface utilisateur de ne plus répondre.

1.  *Suspendu* &ndash; lorsque l’appareil se met en veille ou une activité est toujours visible mais partiellement masqué par une activité nouvelle, non en taille réelle ou transparente, l’activité est considérée comme suspendu. Les activités en pause sont encore actifs, autrement dit, ils conserver toutes les informations d’état et de membre et restent attachées au Gestionnaire de fenêtre. Cela est considéré comme la deuxième activité de priorité la plus élevée dans Android et, par conséquent, sera uniquement être arrêté par le système d’exploitation si l’arrêt de cette activité satisfait les besoins en ressources requises pour maintenir l’activité actif/en cours d’exécution stable et réactif.

1.  *Arrêté/Backgrounded* &ndash; activités qui sont entièrement masquées par une autre activité sont considérés comme arrêté ou en arrière-plan.
    Activités arrêtées toujours essayer de conserver leurs informations d’état et de membre pour tant que les activités possibles, mais il est arrêtées sont considérés comme étant la priorité la plus faible des trois états et, par conséquent, le système d’exploitation va arrêter les activités dans cet état pour répondre à la ressource exigences des activités de priorité plus élevées.

1.  *Redémarré* &ndash; il est possible pour une activité qui est comprise entre suspendu arrêté dans le cycle de vie à supprimer de la mémoire par Android. Si l’utilisateur revient à l’activité, il doit être redémarré, restauré à son état précédemment enregistré, puis affiché à l’utilisateur.


### <a name="activity-re-creation-in-response-to-configuration-changes"></a>Recréation des activités en réponse aux modifications de Configuration

Pour rendre les choses plus compliquées, Android lève une clé plus dans la combinaison de modifications de Configuration. Modifications de configuration sont activité rapide destruction/ré-creation cycles qui se produisent lors de la configuration d’une activité change, par exemple quand l’appareil est [pivoté](~/android/app-fundamentals/handling-rotation.md) (et l’activité doit obtenir régénéré dans paysage ou portrait mode), lorsque le clavier est affiché (et que l’activité est présentée avec possibilité de redimensionner lui-même), ou lorsque l’appareil est placé dans une station d’accueil, entre autres.

Modifications de configuration entraînent toujours les mêmes modifications d’état de l’activité qui se produirait pendant l’arrêt et redémarrage d’une activité. Toutefois, pour vous assurer qu’une application semble réactive et effectue correctement lors des modifications de configuration, il est important qu’elles sont gérées aussi rapidement que possible. Pour cette raison, Android a une API spécifique qui peut être utilisée pour conserver l’état pendant les modifications de configuration.
Nous aborderons cela plus loin dans le [gestion état tout au long le cycle de vie](~/android/app-fundamentals/activity-lifecycle/index.md#Managing_State_Throughout_the_Lifecycle) section.

### <a name="activity-lifecycle-methods"></a>Méthodes de cycle de vie d’activité

Le Kit de développement logiciel Android et, par extension, le framework Xamarin.Android fournissent un modèle puissant pour la gestion de l’état des activités au sein d’une application. Lorsque l’état d’une activité est modifié, l’activité est avertie par le système d’exploitation, qui appelle des méthodes spécifiques sur cette activité. Le diagramme suivant illustre ces méthodes en relation avec le cycle de vie d’activité :

[![Organigramme de cycle de vie d’activité](images/image2-sml.png)](images/image2.png#lightbox)

En tant que développeur, vous pouvez gérer les changements d’état en remplaçant ces méthodes dans une activité. Il est important de noter, cependant, que toutes les méthodes de cycle de vie sont appelées sur le thread d’interface utilisateur et bloquent le système d’exploitation à partir de l’exécution de la pièce suivante du travail de l’interface utilisateur, telles que masquer l’activité en cours, en affichant une nouvelle activité, un etc. Par conséquent, le code dans ces méthodes doit être aussi courte que possible rendre une application bien effectuer l’impression. Toutes les tâches longues doivent être exécutées sur un thread d’arrière-plan.

Examinons chacun de ces méthodes de cycle de vie et leur utilisation :

#### <a name="oncreate"></a>OnCreate

[OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) est la première méthode appelée lorsqu’une activité est créée.
`OnCreate` est toujours remplacée pour effectuer des initialisations de démarrage qui peuvent être requis par une activité tel que :

-  Création de vues
-  L’initialisation de variables
-  Liaison de données statiques à des listes


`OnCreate` prend un [Bundle](https://developer.xamarin.com/api/type/Android.OS.Bundle/) paramètre, qui est un dictionnaire pour stocker et en passant les informations d’état et des objets entre les activités si le groupe n’est pas null, cela indique que le redémarrage de l’activité et il doit restaurer son état à partir de la instance précédente. Le code suivant illustre comment récupérer des valeurs à partir de l’offre groupée :

```csharp
protected override void OnCreate(Bundle bundle)
{
   base.OnCreate(bundle);

   string intentString;
   bool intentBool;

   if (bundle != null)
   {
      intentString = bundle.GetString("myString");
      intentBool = bundle.GetBoolean("myBool");
   }

   // Set our view from the "main" layout resource
   SetContentView(Resource.Layout.Main);
}
```

Une fois `OnCreate` a terminé, Android appellera `OnStart`.

#### <a name="onstart"></a>OnStart

[OnStart](https://developer.xamarin.com/api/member/Android.App.Activity.OnStart/) est toujours appelé par le système après `OnCreate` est terminé. Les activités peuvent substituer cette méthode s’ils doivent effectuer aucun droit de tâches spécifiques avant une activité devient visible, comme actualiser les valeurs actuelles des vues au sein de l’activité. Android appelle `OnResume` immédiatement après cette méthode.

#### <a name="onresume"></a>OnResume

Les appels système [OnResume](https://developer.xamarin.com/api/member/Android.App.Activity.OnResume/) lorsque l’activité est prête à commencer à interagir avec l’utilisateur.
Activités doivent substituer cette méthode pour effectuer des tâches telles que :

-  Renforcement des fréquences d’images (une tâche courante dans le développement de jeux)
-  Animations de départ
-  À l’écoute des mises à jour GPS
-  Afficher toutes les alertes pertinentes ou boîtes de dialogue
-  Des gestionnaires d’événements externes


Par exemple, l’extrait de code suivant illustre l’initialisation de l’appareil photo :

```csharp
public void OnResume()
{
    base.OnResume(); // Always call the superclass first.

    if (_camera==null)
    {
        // Do camera initializations here
    }
}
```

`OnResume` est important, car toute opération qui est effectuée dans `OnPause` doit être annulée dans `OnResume`, puisqu’il s’agit de la seule méthode de cycle de vie est toujours exécuté après `OnPause` lorsque vous importez l’activité à la vie.

#### <a name="onpause"></a>OnPause

[OnPause](https://developer.xamarin.com/api/member/Android.App.Activity.OnPause/) est appelée lorsque le système est sur le point de placer l’activité en arrière-plan ou lorsque l’activité est partiellement masquée. Activités doivent substituer cette méthode s’ils doivent :

-   Valider les changements apportés aux données persistantes

-   Détruire ou nettoyez les autres objets consomme de ressources

-   Rampe de fréquences d’images et animations mise en pause

-   Annuler l’inscription des gestionnaires d’événements externes ou des gestionnaires de notification (c'est-à-dire celles qui sont liées à un service). Ceci doit être fait pour empêcher les fuites de mémoire d’activité.

-   De même, si l’activité a affiché les boîtes de dialogue ou les alertes, ils doivent être nettoyés avec le `.Dismiss()` (méthode).

Par exemple, l’extrait de code suivant libère l’appareil photo, comme l’activité ne peut pas rendre l’utiliser pendant la suspension :

```csharp
public void OnPause()
{
    base.OnPause(); // Always call the superclass first

    // Release the camera as other activities might need it
    if (_camera != null)
    {
        _camera.Release();
        _camera = null;
    }
}
```

Il existe deux méthodes de cycle de vie possible qui seront appelées après `OnPause`:

1.  `OnResume` est appelée si l’activité doit être retournée au premier plan.
1.  `OnStop` est appelée si l’activité est placée en arrière-plan.


#### <a name="onstop"></a>OnStop

[OnStop](https://developer.xamarin.com/api/member/Android.App.Activity.OnStop/) est appelée lorsque l’activité n’est plus visible par l’utilisateur. Cela se produit lorsqu’une des actions suivantes se produit :

-  Une nouvelle activité en cours de démarrage et couvre cette activité.
-  Une activité existante est mise au premier plan.
-  L’activité est en cours de destruction.


`OnStop` peut-être pas toujours être appelé dans les situations de mémoire insuffisante, telles que lorsque Android est suffisante pour les ressources et l’activité correctement ne peut pas en arrière-plan. Pour cette raison, il est préférable de ne pas s’appuient sur `OnStop` appelée lors de la préparation d’une activité de destruction. Les méthodes de cycle de vie suivants qui peuvent être appelées une fois que celui-ci sera `OnDestroy` si l’activité est obsolète, ou `OnRestart` si l’activité revient à interagir avec l’utilisateur.

#### <a name="ondestroy"></a>onDestroy

[OnDestroy](https://developer.xamarin.com/api/member/Android.App.Activity.OnDestroy/) est la dernière méthode est appelée sur une instance d’activité avant qu’il a détruit et complètement supprimée de la mémoire. Dans des situations extrêmes Android risque d’arrêter le processus d’application qui héberge l’activité, ce qui entraîne `OnDestroy` ne pas invoquée. La plupart des activités n’implémentez pas cette méthode, car la plupart nettoyer et d’arrêt a été effectuée dans le `OnPause` et `OnStop` méthodes. Le `OnDestroy` méthode est généralement substituée à nettoyer de longue durée en cours d’exécution ressources qui peuvent avoir une fuite ressources. Un exemple de ceci est peut-être les threads d’arrière-plan qui ont été démarrées dans `OnCreate`.

Il n’y a aucune méthode de cycle de vie appelé une fois que l’activité a été détruite.

#### <a name="onrestart"></a>OnRestart

[OnRestart](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestart/) est appelée une fois que votre activité a été arrêtée avant d’être relancé. Un bon exemple de ce serait quand l’utilisateur appuie sur le bouton Accueil pendant que sur une activité dans l’application. Lorsque cela se produit `OnPause` , puis `OnStop` méthodes sont appelées, et l’activité est déplacée vers l’arrière-plan, mais n’est pas détruite. Si l’utilisateur était pour restaurer l’application à l’aide du Gestionnaire des tâches ou une application similaire, Android appellera le `OnRestart` méthode de l’activité.

Il n’y a aucune des instructions générales pour le type de logique doit être implémenté dans `OnRestart`. Il s’agit, car `OnStart` est toujours appelée indépendamment de si l’activité est en cours de création ou en cours de redémarrage, donc toutes les ressources requises par l’activité doivent être initialisés dans `OnStart`, plutôt que `OnRestart`.

La méthode de cycle de vie suivante appelée après `OnRestart` sera `OnStart`.

### <a name="back-vs-home"></a>Visual Studio en retour. Accueil

Nombre d’appareils Android ont deux boutons distincts : un bouton « Précédent » et un bouton « Home ». Un exemple de ce peut être consultée dans la capture d’écran suivante d’Android 4.0.3 :

[![Boutons Précédent et accueil](images/image4-sml.png)](images/image4.png#lightbox)

Il existe une différence subtile entre les deux boutons, que même s’ils semblent avoir le même effet de mettre une application en arrière-plan. Lorsqu’un utilisateur clique sur le bouton précédent, ils disent Android que qu’ils ont terminé l’activité. Android détruira l’activité. En revanche, lorsque l’utilisateur clique sur le bouton Accueil l’activité est simplement placée dans l’arrière-plan &ndash; Android ne va pas arrêter l’activité.

<a name="Managing_State_Throughout_the_Lifecycle" />

## <a name="managing-state-throughout-the-lifecycle"></a>Gestion de l’état tout au long du cycle de vie

Lorsqu’une activité est arrêtée ou détruite le système fournit une opportunité d’enregistrer l’état de l’activité pour la réactivation ultérieure.
Cet état enregistré est appelé état de l’instance. Android fournit trois options pour stocker l’état de l’instance pendant le cycle de vie d’activité :

1. Stockage de valeurs primitives dans un `Dictionary` appelé un [Bundle](https://developer.xamarin.com/api/type/Android.OS.Bundle/) que Android utilisera pour enregistrer l’état.

1. Création d’une classe personnalisée qui contiendra les valeurs complexes telles que les bitmaps. Android utilisera cette classe personnalisée pour enregistrer l’état.

1. Contourner le cycle de vie de modification de configuration et en supposant que la responsabilité complète pour gérer l’état dans l’activité.


Ce guide couvre les deux premières options.



### <a name="bundle-state"></a>État de l’offre groupée

L’option pour enregistrer l’état de l’instance principale consiste à utiliser un objet de dictionnaire de clé/valeur appelé un [Bundle](https://developer.xamarin.com/api/type/Android.OS.Bundle/).
N’oubliez pas que lors de la création d’une activité qui le `OnCreate` est transmis à la méthode un regroupement en tant que paramètre, cette offre groupée peut être utilisée pour restaurer l’état de l’instance. Il est déconseillé d’utiliser un regroupement pour des données plus complexes qui ne sont pas rapidement ou facilement sérialisés en clé/valeur paires (telles que les bitmaps) ; au lieu de cela, elle doit être utilisée pour les valeurs simples telles que des chaînes.

Une activité fournit des méthodes pour faciliter l’enregistrement et la récupération de l’état d’instance dans le regroupement :

-   [OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) &ndash; cela est appelé par Android lorsque l’activité est en cours de destruction. Activités peuvent implémenter cette méthode s’ils doivent conserver les éléments d’état de clé/valeur.

-   [OnRestoreInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestoreInstanceState/p/Android.OS.Bundle/) &ndash; elle est appelée après la `OnCreate` méthode est terminé et fournit une autre possibilité pour une activité restaurer son état après l’initialisation est terminée.

Le diagramme suivant illustre la façon dont ces méthodes sont utilisées :

[![Organigramme d’états de bundle](images/image3-sml.png)](images/image3.png#lightbox)

#### <a name="onsaveinstancestate"></a>OnSaveInstanceState

[OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) est appelée lorsque l’activité est en cours d’arrêt. Elle reçoit un paramètre de groupe que l’activité peut stocker son état dans. Quand un appareil une modification de configuration, une activité peut utiliser le `Bundle` objet qui est transmise à conserver l’état de l’activité en substituant `OnSaveInstanceState`. Considérons par exemple le code suivant :

```csharp
int c;

protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);

  this.SetContentView (Resource.Layout.SimpleStateView);

  var output = this.FindViewById<TextView> (Resource.Id.outputText);

  if (bundle != null) {
    c = bundle.GetInt ("counter", -1);
  } else {
    c = -1;
  }

  output.Text = c.ToString ();

  var incrementCounter = this.FindViewById<Button> (Resource.Id.incrementCounter);

  incrementCounter.Click += (s,e) => {
    output.Text = (++c).ToString();
  };
}
```

Le code ci-dessus incrémente un entier nommé `c` lorsqu’un bouton nommé `incrementCounter` est activé, affiche le résultat dans un `TextView` nommé `output`. Quand une modification de configuration se produit - par exemple, lorsque l’appareil est pivoté : le code ci-dessus perdrait la valeur de `c` , car le `bundle` serait `null`, comme illustré dans la figure ci-dessous :

[![Affichage ne montre pas la valeur précédente](images/07-sml.png)](images/07.png#lightbox)

Pour conserver la valeur de `c` dans cet exemple, l’activité peut remplacer `OnSaveInstanceState`, l’enregistrement de la valeur de l’offre groupée comme indiqué ci-dessous :

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
  outState.PutInt ("counter", c);
  base.OnSaveInstanceState (outState);
}
```

Maintenant lorsque l’appareil est pivotée vers une nouvelle orientation, l’entier est enregistré dans le regroupement et est récupéré avec la ligne :

```csharp
c = bundle.GetInt ("counter", -1);
```

> [!NOTE]
> Il est important de toujours appel de l’implémentation de base de `OnSaveInstanceState` afin que l’état de la hiérarchie d’affichage peut également être enregistré.



##### <a name="view-state"></a>État d’affichage

Substitution de `OnSaveInstanceState` est un mécanisme approprié pour enregistrer les données temporaires dans une activité sur les changements d’orientation, telles que le compteur dans l’exemple ci-dessus. Toutefois, l’implémentation par défaut de `OnSaveInstanceState` se chargera d’enregistrer les données temporaires dans l’interface utilisateur pour chaque vue, tant que chaque vue possède un ID assigné. Par exemple, une application a un `EditText` élément défini dans XML comme suit :

```xml
<EditText android:id="@+id/myText"
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"/>
```

Dans la mesure où le `EditText` contrôle a une `id` affectée lorsque l’utilisateur saisit des données et fait pivoter l’appareil, les données sont toujours affichées, comme indiqué ci-dessous :

[![Données sont conservées en mode paysage](images/08-sml.png)](images/08.png#lightbox)

#### <a name="onrestoreinstancestate"></a>OnRestoreInstanceState

[OnRestoreInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestoreInstanceState/p/Android.OS.Bundle/) sera appelée après `OnStart`. Il fournit une activité de la possibilité de restaurer l’état précédemment enregistré pour un regroupement lors de la précédente des `OnSaveInstanceState`. Voici le même regroupement qui est fourni pour `OnCreate`, toutefois.

Le code suivant illustre la façon dont l’état peut être restauré dans `OnRestoreInstanceState`:

```csharp
protected override void OnRestoreInstanceState(Bundle savedState)
{
    base.OnRestoreSaveInstanceState(savedState);
    var myString = savedState.GetString("myString");
    var myBool = savedState.GetBoolean("myBool");
}
```

Cette méthode existe pour fournir une certaine flexibilité autour lors de l’état doit être restauré. Il est parfois plus approprié d’attendre jusqu'à ce que toutes les initialisations sont terminées avant de restaurer l’état de l’instance. En outre, une sous-classe d’une activité existante peut uniquement peut-être restaurer certaines valeurs de l’état de l’instance. Dans de nombreux cas, il n’est pas nécessaire de remplacer `OnRestoreInstanceState`, étant donné que la plupart des activités peuvent restaurer l’état à l’aide de l’offre groupée fournie à `OnCreate`.

Pour obtenir un exemple de l’enregistrement d’état à l’aide un `Bundle`, reportez-vous à la [procédure pas à pas - état de l’enregistrement de l’activité](saving-state.md).


#### <a name="bundle-limitations"></a>Limitations de l’offre groupée

Bien que `OnSaveInstanceState` rend facile d’enregistrer les données temporaires, il présente certaines limitations :

-   Il n’est pas appelée dans tous les cas. Par exemple, en appuyant sur **accueil** ou **retour** pour quitter une activité ne provoque pas de `OnSaveInstanceState` qui est appelée.

-   L’offre groupée passé dans `OnSaveInstanceState` n’est pas conçu pour les objets volumineux, tels que des images. Dans le cas des objets volumineux, l’enregistrement de l’objet à partir de [OnRetainNonConfigurationInstance](https://developer.xamarin.com/api/member/Android.App.Activity.OnRetainNonConfigurationInstance/) est préférable, comme indiqué ci-dessous.

-   Données enregistrées à l’aide de l’offre groupée sont sérialisées, ce qui peut entraîner des retards.

État de l’offre groupée est utile pour les données simples qui n’utilisent pas beaucoup de mémoire, tandis que *les données de configuration non instance* est utile pour des données plus complexes, ou données qui sont coûteuses à récupérer, par exemple d’un appel de service web ou un complexe requête de base de données. Données d’instance non-configuration sont enregistrées dans un objet en fonction des besoins. La section suivante présente `OnRetainNonConfigurationInstance` comme un moyen de conserver les types de données plus complexes à travers les modifications de configuration.


### <a name="persisting-complex-data"></a>Conservation des données complexes

En plus de conservation des données dans le regroupement, Android prend également en charge l’enregistrement des données en substituant [OnRetainNonConfigurationInstance](https://developer.xamarin.com/api/member/Android.App.Activity.OnRetainNonConfigurationInstance/) et retourner une instance d’un `Java.Lang.Object` qui contient les données à conserver. Il existe deux grands avantages de l’utilisation de `OnRetainNonConfigurationInstance` pour enregistrer l’état :

-   L’objet retourné par `OnRetainNonConfigurationInstance` fonctionne parfaitement avec les types de données plus volumineuses et complexes, car la mémoire conserve cet objet.

-   Le `OnRetainNonConfigurationInstance` méthode est appelée à la demande et uniquement lorsque nécessaire. Cette solution est plus économique que l’utilisation d’un cache manuels.

À l’aide de `OnRetainNonConfigurationInstance` est adapté aux scénarios où il est coûteux récupérer les données plusieurs fois, comme dans les appels de service web. Par exemple, considérez le code suivant qui recherche Twitter :

```csharp
public class NonConfigInstanceActivity : ListActivity
{
  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);
    SearchTwitter ("xamarin");
  }

  public void SearchTwitter (string text)
  {
    string searchUrl = String.Format("http://search.twitter.com/search.json?" + "q={0}&rpp=10&include_entities=false&" + "result_type=mixed", text);

    var httpReq = (HttpWebRequest)HttpWebRequest.Create (new Uri (searchUrl));
    httpReq.BeginGetResponse (new AsyncCallback (ResponseCallback), httpReq);
  }

  void ResponseCallback (IAsyncResult ar)
  {
    var httpReq = (HttpWebRequest)ar.AsyncState;

    using (var httpRes = (HttpWebResponse)httpReq.EndGetResponse (ar)) {
      ParseResults (httpRes);
    }
  }

  void ParseResults (HttpWebResponse httpRes)
  {
    var s = httpRes.GetResponseStream ();
    var j = (JsonObject)JsonObject.Load (s);

    var results = (from result in (JsonArray)j ["results"] let jResult = result as JsonObject select jResult ["text"].ToString ()).ToArray ();

    RunOnUiThread (() => {
      PopulateTweetList (results);
    });
  }

  void PopulateTweetList (string[] results)
  {
    ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.ItemView, results);
  }
}
```

Ce code récupère les résultats à partir du web au format JSON, les analyse et présente ensuite les résultats dans une liste, comme indiqué dans la capture d’écran suivante :

[![Résultats affichés sur l’écran](images/06-sml.png)](images/06.png#lightbox)

Lorsqu’une modification de configuration se produit - par exemple, quand un appareil est pivoté - le code répète le processus. Pour réutiliser les résultats récupérés à l’origine et ne provoquent pas les appels réseau inutiles, redondantes, nous pouvons utiliser `OnRetainNonconfigurationInstance` pour enregistrer les résultats, comme indiqué ci-dessous :

```csharp
public class NonConfigInstanceActivity : ListActivity
{
  TweetListWrapper _savedInstance;

  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);

    var tweetsWrapper = LastNonConfigurationInstance as TweetListWrapper;

    if (tweetsWrapper != null) {
      PopulateTweetList (tweetsWrapper.Tweets);
    } else {
      SearchTwitter ("xamarin");
    }

    public override Java.Lang.Object OnRetainNonConfigurationInstance ()
    {
      base.OnRetainNonConfigurationInstance ();
      return _savedInstance;
    }

    ...

    void PopulateTweetList (string[] results)
    {
      ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.ItemView, results);
      _savedInstance = new TweetListWrapper{Tweets=results};
    }
}
```

Maintenant lorsque vous faites pivoter l’appareil, les résultats d’origine sont récupérés à partir de la `LastNonConfiguartionInstance` propriété. Dans cet exemple, les résultats se composent d’un `string[]` contenant des tweets. Dans la mesure où `OnRetainNonConfigurationInstance` qui nécessite un `Java.Lang.Object` retourné, le `string[]` est encapsulée dans une classe qui sous-classe `Java.Lang.Object`, comme illustré ci-dessous :

```csharp
class TweetListWrapper : Java.Lang.Object
{
  public string[] Tweets { get; set; }
}
```

Par exemple, essayez d’utiliser un `TextView` en tant que l’objet retourné par `OnRetainNonConfigurationInstance` provoquera une fuite de l’activité, comme l’illustre le code ci-dessous :

```csharp
TextView _textView;

protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);

  var tv = LastNonConfigurationInstance as TextViewWrapper;

  if(tv != null) {
    _textView = tv;
    var parent = _textView.Parent as FrameLayout;
    parent.RemoveView(_textView);
  } else {
    _textView = new TextView (this);
    _textView.Text = "This will leak.";
  }

  SetContentView (_textView);
}

public override Java.Lang.Object OnRetainNonConfigurationInstance ()
{
  base.OnRetainNonConfigurationInstance ();
  return _textView;
}
```

Dans cette section, nous avons appris à préserver les données d’état simple avec le `Bundle`, et conserver les types de données plus complexes avec `OnRetainNonConfigurationInstance`.

## <a name="summary"></a>Récapitulatif

Le cycle de vie d’une activité Android fournit un framework puissant pour la gestion de l’état des activités au sein d’une application, mais il peut être difficile à comprendre et à implémenter. Ce chapitre introduit les différents États qu’une activité peut passer par pendant sa durée de vie, ainsi que les méthodes de cycle de vie qui sont associés à ces États. Ensuite, a été des conseils quant à quel type de logique doit être effectué dans chacune de ces méthodes.


## <a name="related-links"></a>Liens associés

- [Rotation de la gestion](~/android/app-fundamentals/handling-rotation.md)
- [Activité Android](https://developer.xamarin.com/api/type/Android.App.Activity/)
