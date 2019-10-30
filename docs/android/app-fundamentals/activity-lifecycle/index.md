---
title: Cycle de vie des activités
description: Les activités sont un bloc de construction fondamental des applications Android qui peuvent exister dans différents États. Le cycle de vie de l’activité commence par instanciation et se termine par la destruction, et comprend de nombreux États entre eux. Lorsqu’une activité change d’État, la méthode d’événement de cycle de vie appropriée est appelée, en avertissant l’activité du changement d’État imminent et en lui permettant d’exécuter du code pour s’adapter à cette modification. Cet article examine le cycle de vie des activités et explique la responsabilité d’une activité au cours de chacun de ces changements d’État pour faire partie d’une application fiable et avec un comportement correct.
ms.prod: xamarin
ms.assetid: 05B34788-F2D2-4347-B66B-40AFD7B1D167
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/28/2018
ms.openlocfilehash: 6e69d21bb734f13d220c042535842538306d16c8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016994"
---
# <a name="activity-lifecycle"></a>Cycle de vie des activités

_Les activités sont un bloc de construction fondamental des applications Android qui peuvent exister dans différents États. Le cycle de vie de l’activité commence par instanciation et se termine par la destruction, et comprend de nombreux États entre eux. Lorsqu’une activité change d’État, la méthode d’événement de cycle de vie appropriée est appelée, en avertissant l’activité du changement d’État imminent et en lui permettant d’exécuter du code pour s’adapter à cette modification. Cet article examine le cycle de vie des activités et explique la responsabilité d’une activité au cours de chacun de ces changements d’État pour faire partie d’une application fiable et avec un comportement correct._

## <a name="activity-lifecycle-overview"></a>Présentation du cycle de vie des activités

Les activités sont un concept de programmation inhabituel spécifique à Android. Dans le développement d’applications traditionnel, il y a généralement une méthode main statique, qui est exécutée pour lancer l’application. Avec Android, toutefois, les choses sont différentes ; Les applications Android peuvent être lancées par le biais d’une activité enregistrée au sein d’une application. Dans la pratique, la plupart des applications n’ont qu’une activité spécifique qui est spécifiée comme point d’entrée de l’application. Toutefois, si une application se bloque ou est arrêtée par le système d’exploitation, le système d’exploitation peut essayer de redémarrer l’application à la dernière activité ouverte ou n’importe où dans la pile d’activité précédente.
En outre, le système d’exploitation peut suspendre les activités lorsqu’elles ne sont pas actives et les récupérer si la mémoire est insuffisante. Une attention particulière doit être prise pour permettre à l’application de restaurer correctement son état dans le cas où une activité est redémarrée, en particulier si cette activité dépend de données d’activités précédentes.

Le cycle de vie de l’activité est implémenté sous la forme d’une collection de méthodes que le système d’exploitation appelle tout au long du cycle de vie d’une activité. Ces méthodes permettent aux développeurs d’implémenter les fonctionnalités nécessaires pour répondre aux exigences de gestion des ressources et de l’état de leurs applications.

Il est extrêmement important pour le développeur d’applications d’analyser les exigences de chaque activité afin de déterminer les méthodes exposées par le cycle de vie de l’activité qui doivent être implémentées. Si vous ne le faites pas, vous risquez de provoquer une instabilité de l’application, des pannes, une augmentation des ressources et éventuellement une instabilité sous-jacente du système d’exploitation.

Ce chapitre examine en détail le cycle de vie de l’activité, notamment :

- États de l’activité
- Méthodes de cycle de vie
- Conservation de l’état d’une application

Cette section comprend également une [procédure pas à pas](~/android/app-fundamentals/activity-lifecycle/saving-state.md) qui fournit des exemples pratiques sur l’enregistrement efficace de l’État pendant le cycle de vie de l’activité. À la fin de ce chapitre, vous devez comprendre le cycle de vie de l’activité et savoir comment la prendre en charge dans une application Android.

## <a name="activity-lifecycle"></a>Cycle de vie des activités

Le cycle de vie des activités Android comprend un ensemble de méthodes exposées dans la classe d’activité qui fournissent au développeur un Framework de gestion des ressources. Ce Framework permet aux développeurs de répondre aux besoins de gestion d’État uniques de chaque activité au sein d’une application et de gérer correctement la gestion des ressources.

### <a name="activity-states"></a>États de l’activité

Le système d’exploitation Android arbitre les activités en fonction de leur état. Cela permet à Android d’identifier les activités qui ne sont plus utilisées, ce qui permet au système d’exploitation de récupérer la mémoire et les ressources. Le diagramme suivant illustre les États qu’une activité peut traverser pendant sa durée de vie :

[diagramme des États de l’activité![](images/image1-sml.png)](images/image1.png#lightbox)

Ces États peuvent être divisés en 4 groupes principaux, comme suit :

1. Les activités *actives ou en cours d’exécution* &ndash; sont considérées comme actives ou en cours d’exécution si elles se trouvent au premier plan, également appelées haut de la pile d’activité. Cette activité est considérée comme la priorité la plus élevée dans Android. par conséquent, elle n’est supprimée que par le système d’exploitation dans des situations extrêmes, par exemple si l’activité tente d’utiliser plus de mémoire que ce qui est disponible sur l’appareil, car cela peut provoquer le blocage de l’interface utilisateur.

1. Mise en *pause* &ndash; lorsque l’appareil passe en mode veille ou quand une activité est toujours visible mais partiellement masquée par une activité nouvelle, non complète ou transparente, l’activité est considérée comme étant en pause. Les activités suspendues sont toujours actives, c’est-à-dire qu’elles conservent l’ensemble des informations sur les États et les membres et restent attachées au gestionnaire de fenêtres. Il s’agit de la deuxième activité de priorité la plus élevée dans Android. par conséquent, elle n’est supprimée par le système d’exploitation que si la suppression de cette activité répond aux besoins en ressources nécessaires pour garantir la stabilité et la réactivité de l’activité active/en cours d’exécution.

1. *Arrêté/en arrière-plan* &ndash; les activités qui sont complètement masquées par une autre activité sont considérées comme arrêtées ou en arrière-plan.
    Les activités arrêtées essaient toujours de conserver leurs informations d’État et de membre aussi longtemps que possible, mais les activités arrêtées sont considérées comme étant la priorité la plus faible des trois États et, par conséquent, le système d’exploitation va d’abord arrêter les activités de cet État pour satisfaire la ressource. exigences des activités avec une priorité plus élevée.

1. *Redémarré* &ndash; il est possible qu’une activité qui n’est pas interrompue dans le cycle de vie soit supprimée de la mémoire par Android. Si l’utilisateur revient à l’activité, il doit être redémarré, restauré à son état enregistré précédemment, puis affiché à l’utilisateur.

### <a name="activity-re-creation-in-response-to-configuration-changes"></a>Recréation d’activité en réponse à des modifications de configuration

Pour compliquer les choses, Android lève une clé supplémentaire dans la combinaison appelée modifications de configuration. Les modifications de configuration sont des cycles de destruction ou de recréation d’activité rapides qui se produisent lorsque la configuration d’une activité change, par exemple lorsque l’appareil est [pivoté](~/android/app-fundamentals/handling-rotation.md) (et que l’activité doit être reconstruite en mode paysage ou en mode portrait), lorsque le le clavier est affiché (et l’activité est présentée avec la possibilité de se redimensionner), ou lorsque l’appareil est placé dans une station d’accueil, entre autres.

Les modifications de configuration entraînent toujours les mêmes modifications d’état d’activité qui se produisent pendant l’arrêt et le redémarrage d’une activité. Toutefois, afin de s’assurer qu’une application semble réactive et fonctionne bien au cours des modifications de configuration, il est important qu’elles soient gérées aussi rapidement que possible. Pour cette raison, Android dispose d’une API spécifique qui peut être utilisée pour conserver l’État pendant les modifications de configuration.
Nous aborderons cela plus loin dans la section [gestion de l’État dans le cycle de vie](~/android/app-fundamentals/activity-lifecycle/index.md#Managing_State_Throughout_the_Lifecycle) .

### <a name="activity-lifecycle-methods"></a>Méthodes de cycle de vie des activités

La Android SDK et, par extension, l’infrastructure Xamarin. Android fournissent un modèle puissant pour gérer l’état des activités au sein d’une application. Lorsque l’état d’une activité change, l’activité est notifiée par le système d’exploitation, qui appelle des méthodes spécifiques sur cette activité. Le diagramme suivant illustre ces méthodes par rapport au cycle de vie de l’activité :

[Organigramme du cycle de vie de l’activité![](images/image2-sml.png)](images/image2.png#lightbox)

En tant que développeur, vous pouvez gérer les modifications d’État en substituant ces méthodes dans une activité. Toutefois, il est important de noter que toutes les méthodes de cycle de vie sont appelées sur le thread d’interface utilisateur et empêchent le système d’exploitation d’effectuer la partie suivante du travail d’interface utilisateur, comme le masquage de l’activité en cours, l’affichage d’une nouvelle activité, etc. Ainsi, le code de ces méthodes doit être le plus court possible pour que l’application fonctionne correctement. Toutes les tâches de longue durée doivent être exécutées sur un thread d’arrière-plan.

Examinons chacune de ces méthodes de cycle de vie et leur utilisation :

#### <a name="oncreate"></a>OnCreate

[OnCreate](xref:Android.App.Activity.OnCreate*) est la première méthode à appeler lors de la création d’une activité.
`OnCreate` est toujours substitué pour effectuer toutes les initialisations de démarrage qui peuvent être requises par une activité, par exemple :

- Création d’affichages
- Initialiser des variables
- Liaison de données statiques à des listes

`OnCreate` prend un paramètre [Bundle](xref:Android.OS.Bundle) , qui est un dictionnaire pour stocker et transmettre des informations d’État et des objets entre les activités si le bundle n’est pas null, cela indique que l’activité est en cours de redémarrage et qu’elle doit restaurer son état à partir du précédent. instancié. Le code suivant montre comment récupérer des valeurs à partir du bundle :

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

Une fois `OnCreate` terminé, Android appellera `OnStart`.

#### <a name="onstart"></a>OnStart

La méthode [OnStart](xref:Android.App.Activity.OnStart) est toujours appelée par le système une fois `OnCreate` terminée. Les activités peuvent remplacer cette méthode si elles doivent effectuer des tâches spécifiques juste avant qu’une activité devienne visible, par exemple l’actualisation des valeurs actuelles des vues au sein de l’activité. Android appellera `OnResume` immédiatement après cette méthode.

#### <a name="onresume"></a>OnResume

Le système appelle [OnResume](xref:Android.App.Activity.OnResume) quand l’activité est prête à démarrer l’interaction avec l’utilisateur.
Les activités doivent remplacer cette méthode pour effectuer des tâches telles que :

- Accélération des fréquences d’images (tâche courante dans le développement de jeux)
- Démarrage d’animations
- Écoute des mises à jour GPS
- Afficher les alertes ou les boîtes de dialogue pertinentes
- Associer des gestionnaires d’événements externes

À titre d’exemple, l’extrait de code suivant montre comment initialiser l’appareil photo :

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

`OnResume` est important car toute opération effectuée dans `OnPause` doit être annulée dans `OnResume`, puisqu’il s’agit de la seule méthode de cycle de vie dont l’exécution est garantie après `OnPause` lors de la restauration de l’activité.

#### <a name="onpause"></a>OnPause

[OnPause](xref:Android.App.Activity.OnPause) est appelé quand le système est sur le paragraphe de placer l’activité dans l’arrière-plan ou lorsque l’activité devient partiellement masquée. Les activités doivent remplacer cette méthode s’ils ont besoin d’effectuer les opérations suivantes :

- Valider les modifications non enregistrées dans les données persistantes

- Détruire ou nettoyer d’autres objets consommant des ressources

- Fréquences d’images de rampe et de suspension des animations

- Annule l’enregistrement des gestionnaires d’événements externes ou des gestionnaires de notifications (c’est-à-dire ceux qui sont liés à un service). Cela doit être fait pour empêcher les fuites de mémoire d’activité.

- De même, si l’activité a affiché des boîtes de dialogue ou des alertes, elles doivent être nettoyées avec la méthode `.Dismiss()`.

Par exemple, l’extrait de code suivant libère l’appareil photo, car l’activité ne peut pas l’utiliser pendant la suspension :

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

Il existe deux méthodes de cycle de vie possibles qui seront appelées après `OnPause`:

1. `OnResume` est appelée si l’activité doit être retournée au premier plan.
1. `OnStop` est appelée si l’activité est placée en arrière-plan.

#### <a name="onstop"></a>OnStop

[OnStop](xref:Android.App.Activity.OnStop) est appelé lorsque l’activité n’est plus visible par l’utilisateur. Cela se produit lorsque l’un des événements suivants se produit :

- Une nouvelle activité est en cours de démarrage et couvre cette activité.
- Une activité existante est placée au premier plan.
- L’activité est en cours de destruction.

`OnStop` ne peuvent pas toujours être appelés dans des situations de mémoire insuffisante, par exemple quand Android est privé de ressources et ne peut pas être correctement en arrière-plan de l’activité. Pour cette raison, il est préférable de ne pas s’appuyer sur la `OnStop` appelée lors de la préparation d’une activité pour la destruction. Les méthodes de cycle de vie suivantes qui peuvent être appelées après celle-ci sont `OnDestroy` si l’activité est en cours, ou `OnRestart` si l’activité est à nouveau en interaction avec l’utilisateur.

#### <a name="ondestroy"></a>OnDestroy

[OnDestroy](xref:Android.App.Activity.OnDestroy) est la dernière méthode appelée sur une instance d’activité avant qu’elle ne soit détruite et complètement supprimée de la mémoire. Dans les situations extrêmes, Android peut arrêter le processus d’application qui héberge l’activité, ce qui entraînera l’invocation de `OnDestroy`. La plupart des activités n’implémentent pas cette méthode, car la plupart des opérations de nettoyage et d’arrêt ont été effectuées dans les méthodes `OnPause` et `OnStop`. La méthode `OnDestroy` est généralement remplacée pour nettoyer les ressources longues qui peuvent provoquer des fuites de ressources. Par exemple, les threads d’arrière-plan qui ont été démarrés dans `OnCreate`.

Aucune méthode de cycle de vie n’est appelée une fois que l’activité a été détruite.

#### <a name="onrestart"></a>OnRestart

[OnRestart](xref:Android.App.Activity.OnRestart) est appelé après l’arrêt de votre activité, avant son redémarrage. C’est le cas, par exemple, lorsque l’utilisateur appuie sur le bouton d’hébergement sur une activité de l’application. Lorsque cela se produit `OnPause` puis `OnStop` méthodes sont appelées, et l’activité est déplacée en arrière-plan, mais n’est pas détruite. Si l’utilisateur a ensuite restauré l’application à l’aide du gestionnaire des tâches ou d’une application similaire, Android appellera la méthode `OnRestart` de l’activité.

Il n’existe pas d’indications générales sur le type de logique qui doit être implémenté dans `OnRestart`. Cela est dû au fait que `OnStart` est toujours appelé, que l’activité soit en cours de création ou en cours de redémarrage, de sorte que toutes les ressources requises par l’activité doivent être initialisées dans `OnStart`, plutôt que `OnRestart`.

La méthode de cycle de vie suivante appelée après `OnRestart` sera `OnStart`.

### <a name="back-vs-home"></a>Retour et page d’hébergement

De nombreux appareils Android possèdent deux boutons distincts : un bouton « précédent » et un bouton « démarrage ». Vous pouvez voir un exemple dans la capture d’écran suivante d’Android 4.0.3 :

[![les boutons précédent et principal](images/image4-sml.png)](images/image4.png#lightbox)

Il existe une différence subtile entre les deux boutons, même s’ils semblent avoir le même effet de placer une application en arrière-plan. Quand un utilisateur clique sur le bouton précédent, il indique à Android qu’il est terminé avec l’activité. Android détruira l’activité. En revanche, quand l’utilisateur clique sur le bouton de démarrage, l’activité est simplement placée en arrière-plan &ndash; Android n’interrompt pas l’activité.

<a name="Managing_State_Throughout_the_Lifecycle" />

## <a name="managing-state-throughout-the-lifecycle"></a>Gestion de l’État tout au long du cycle de vie

Lorsqu’une activité est arrêtée ou détruite, le système offre la possibilité d’enregistrer l’état de l’activité pour une réalimentation ultérieure.
Cet état enregistré est appelé état de l’instance. Android offre trois options pour le stockage de l’état de l’instance pendant le cycle de vie de l’activité :

1. Le stockage de valeurs primitives dans un `Dictionary` connu sous le nom de [Bundle](xref:Android.OS.Bundle) qui sera utilisé par Android pour enregistrer l’État.

1. Création d’une classe personnalisée qui contiendra des valeurs complexes telles que des bitmaps. Android utilisera cette classe personnalisée pour enregistrer l’État.

1. Contournement du cycle de vie des modifications de configuration et en supposant une responsabilité complète de la gestion de l’état de l’activité.

Ce guide couvre les deux premières options.

### <a name="bundle-state"></a>État du bundle

L’option principale d’enregistrement de l’état de l’instance consiste à utiliser un objet de dictionnaire de clé/valeur appelé [Bundle](xref:Android.OS.Bundle).
Rappelez-vous que lors de la création d’une activité, il est possible d’utiliser cette offre groupée pour restaurer l’état de l’instance lors de la création d’une activité de la méthode `OnCreate`. Il n’est pas recommandé d’utiliser un bundle pour des données plus complexes qui ne peuvent pas être sérialisées rapidement ou facilement en paires clé/valeur (telles que des bitmaps); au lieu de cela, elle doit être utilisée pour des valeurs simples comme les chaînes.

Une activité fournit des méthodes pour faciliter l’enregistrement et la récupération de l’état de l’instance dans le bundle :

- [OnSaveInstanceState](xref:Android.App.Activity.OnSaveInstanceState*) &ndash; ce code est appelé par Android lorsque l’activité est détruite. Les activités peuvent implémenter cette méthode si elles doivent rendre persistantes les éléments d’état de clé/valeur.

- [OnRestoreInstanceState](xref:Android.App.Activity.OnRestoreInstanceState*) &ndash; cette méthode est appelée une fois la méthode `OnCreate` terminée et offre une autre possibilité pour une activité de restaurer son état une fois l’initialisation terminée.

Le diagramme suivant illustre l’utilisation de ces méthodes :

[Organigramme des États du bundle![](images/image3-sml.png)](images/image3.png#lightbox)

#### <a name="onsaveinstancestate"></a>OnSaveInstanceState

[OnSaveInstanceState](xref:Android.App.Activity.OnSaveInstanceState*) sera appelé lorsque l’activité sera arrêtée. Elle recevra un paramètre Bundle dans lequel l’activité peut stocker son état. Quand un appareil subit une modification de configuration, une activité peut utiliser l’objet `Bundle` passé pour conserver l’état de l’activité en remplaçant `OnSaveInstanceState`. Considérons par exemple le code suivant :

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

Le code ci-dessus incrémente un entier nommé `c` lorsqu’un clic est effectué sur un bouton nommé `incrementCounter`, ce qui affiche le résultat dans un `TextView` `output`nommé. En cas de changement de configuration (par exemple, lors de la rotation de l’appareil), le code ci-dessus perd la valeur de `c`, car le `bundle` est `null`, comme illustré dans la figure ci-dessous :

[l’affichage de![n’affiche pas la valeur précédente](images/07-sml.png)](images/07.png#lightbox)

Pour conserver la valeur de `c` dans cet exemple, l’activité peut remplacer `OnSaveInstanceState`, en enregistrant la valeur dans le bundle comme indiqué ci-dessous :

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
  outState.PutInt ("counter", c);
  base.OnSaveInstanceState (outState);
}
```

Désormais, lorsque l’appareil passe à une nouvelle orientation, l’entier est enregistré dans le bundle et est récupéré avec la ligne suivante :

```csharp
c = bundle.GetInt ("counter", -1);
```

> [!NOTE]
> Il est important de toujours appeler l’implémentation de base de `OnSaveInstanceState` afin que l’état de la hiérarchie d’affichage puisse également être enregistré.

##### <a name="view-state"></a>État d’affichage

Le remplacement de `OnSaveInstanceState` est un mécanisme approprié pour l’enregistrement des données temporaires dans une activité sur les modifications d’orientation, telles que le compteur dans l’exemple ci-dessus. Toutefois, l’implémentation par défaut de `OnSaveInstanceState` se charge d’enregistrer les données temporaires dans l’interface utilisateur pour chaque vue, à condition que chaque vue ait un ID affecté. Par exemple, imaginons qu’une application possède un élément `EditText` défini dans XML comme suit :

```xml
<EditText android:id="@+id/myText"
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"/>
```

Étant donné que le contrôle de `EditText` a un `id` affecté, lorsque l’utilisateur entre des données et fait pivoter l’appareil, les données sont toujours affichées, comme indiqué ci-dessous :

[![données sont conservées en mode paysage](images/08-sml.png)](images/08.png#lightbox)

#### <a name="onrestoreinstancestate"></a>OnRestoreInstanceState

[OnRestoreInstanceState](xref:Android.App.Activity.OnRestoreInstanceState*) sera appelé après `OnStart`. Elle offre à l’activité la possibilité de restaurer tout état précédemment enregistré dans un bundle au cours de la `OnSaveInstanceState`précédente. Toutefois, il s’agit du même regroupement fourni pour `OnCreate`.

Le code suivant illustre la façon dont l’État peut être restauré dans `OnRestoreInstanceState`:

```csharp
protected override void OnRestoreInstanceState(Bundle savedState)
{
    base.OnRestoreSaveInstanceState(savedState);
    var myString = savedState.GetString("myString");
    var myBool = savedState.GetBoolean("myBool");
}
```

Cette méthode permet d’offrir une certaine flexibilité lorsque l’État doit être restauré. Parfois, il est plus approprié d’attendre que toutes les initialisations soient effectuées avant de restaurer l’état de l’instance. En outre, une sous-classe d’une activité existante peut uniquement restaurer certaines valeurs à partir de l’état de l’instance. Dans de nombreux cas, il n’est pas nécessaire de remplacer `OnRestoreInstanceState`, car la plupart des activités peuvent restaurer l’État à l’aide du bundle fourni à `OnCreate`.

Pour obtenir un exemple d’enregistrement d’État à l’aide d’un `Bundle`, consultez la [procédure pas à pas : enregistrement de l’état de l’activité](saving-state.md).

#### <a name="bundle-limitations"></a>Limitations de Bundle

Bien que `OnSaveInstanceState` facilite l’enregistrement de données temporaires, il présente certaines limitations :

- Elle n’est pas appelée dans tous les cas. Par exemple, si vous appuyez sur la touche **début** ou **précédent** pour quitter une activité, aucune `OnSaveInstanceState` n’est appelée.

- Le bundle passé dans `OnSaveInstanceState` n’est pas conçu pour les objets volumineux, tels que les images. Dans le cas d’objets volumineux, l’enregistrement de l’objet à partir de [OnRetainNonConfigurationInstance](xref:Android.App.Activity.OnRetainNonConfigurationInstance) est préférable, comme indiqué ci-dessous.

- Les données enregistrées à l’aide du bundle sont sérialisées, ce qui peut entraîner des retards.

L’état de regroupement est utile pour les données simples qui n’utilisent pas beaucoup de mémoire, alors que les *données d’instance non-configuration* sont utiles pour les données plus complexes, ou les données qui sont coûteuses à récupérer, par exemple à partir d’un appel de service Web ou d’une requête de base de données complexe. Les données d’instance non-configuration sont enregistrées dans un objet en fonction des besoins. La section suivante présente `OnRetainNonConfigurationInstance` pour conserver des types de données plus complexes grâce à des modifications de configuration.

### <a name="persisting-complex-data"></a>Persistance des données complexes

En plus de rendre les données persistantes dans le bundle, Android prend également en charge l’enregistrement des données en remplaçant [OnRetainNonConfigurationInstance](xref:Android.App.Activity.OnRetainNonConfigurationInstance) et en retournant une instance d’un `Java.Lang.Object` qui contient les données à conserver. L’utilisation de `OnRetainNonConfigurationInstance` pour enregistrer l’état présente deux avantages principaux :

- L’objet retourné par `OnRetainNonConfigurationInstance` s’exécute correctement avec des types de données plus grands et plus complexes, car la mémoire conserve cet objet.

- La méthode `OnRetainNonConfigurationInstance` est appelée à la demande et uniquement lorsque cela est nécessaire. Cela est plus économique que l’utilisation d’un cache manuel.

L’utilisation de `OnRetainNonConfigurationInstance` convient aux scénarios dans lesquels il est coûteux de récupérer les données plusieurs fois, par exemple dans les appels de service Web. Par exemple, considérez le code suivant qui recherche Twitter :

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

Ce code récupère les résultats du Web au format JSON, les analyse, puis présente les résultats dans une liste, comme illustré dans la capture d’écran suivante :

[![les résultats affichés à l’écran](images/06-sml.png)](images/06.png#lightbox)

Quand une modification de configuration se produit (par exemple, lors de la rotation d’un appareil), le code répète le processus. Pour réutiliser les résultats récupérés à l’origine et ne pas avoir besoin d’appels réseau redondants, nous pouvons utiliser `OnRetainNonconfigurationInstance` pour enregistrer les résultats, comme indiqué ci-dessous :

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

Désormais, lorsque l’appareil pivote, les résultats d’origine sont récupérés à partir de la propriété `LastNonConfiguartionInstance`. Dans cet exemple, les résultats se composent d’un `string[]` contenant des tweets. Étant donné que `OnRetainNonConfigurationInstance` exige qu’une `Java.Lang.Object` soit retournée, la `string[]` est encapsulée dans une classe qui sous-classe `Java.Lang.Object`, comme indiqué ci-dessous :

```csharp
class TweetListWrapper : Java.Lang.Object
{
  public string[] Tweets { get; set; }
}
```

Par exemple, la tentative d’utilisation d’une `TextView` comme objet retourné par `OnRetainNonConfigurationInstance` entraînera une fuite de l’activité, comme l’illustre le code ci-dessous :

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

Dans cette section, nous avons appris à préserver les données d’État simples avec le `Bundle`et à conserver des types de données plus complexes avec `OnRetainNonConfigurationInstance`.

## <a name="summary"></a>Récapitulatif

Le cycle de vie des activités Android fournit une infrastructure puissante pour la gestion de l’état des activités dans une application, mais il peut être difficile de comprendre et d’implémenter. Ce chapitre présente les différents États qu’une activité peut franchir pendant sa durée de vie, ainsi que les méthodes de cycle de vie associées à ces États. Ensuite, vous trouverez des conseils sur le type de logique qui doit être exécuté dans chacune de ces méthodes.

## <a name="related-links"></a>Liens associés

- [Rotation de la gestion](~/android/app-fundamentals/handling-rotation.md)
- [Activité Android](xref:Android.App.Activity)
