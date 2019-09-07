---
title: Garbage Collection
ms.prod: xamarin
ms.assetid: 298139E2-194F-4A58-BC2D-1D22231066C4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: 40fb8f81a82aab9e7d9d3ea3bf4084c14cb6d4ff
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757932"
---
# <a name="garbage-collection"></a>Garbage Collection

Xamarin. Android utilise le [garbage collector générationnel simple](https://www.mono-project.com/docs/advanced/garbage-collector/sgen/)de mono. Il s’agit d’un garbage collector de marque et de balayage avec deux générations et un *espace d’objet volumineux*, avec deux genres de collections : 

- Collections mineures (collecte le tas Gen0) 
- Collections principales (collecte les tas de Gen1 et d’espace d’objet volumineux). 

> [!NOTE]
> En l’absence d’une collection explicite via [gc. ](xref:System.GC.Collect)Les regroupements () sont *à la demande*, en fonction des allocations de tas. *Il ne s’agit pas d’un système de décompte de références*. *les objets ne sont pas collectés dès qu’il n’y a aucune référence en suspens*, ou lorsqu’une étendue s’est arrêtée. Le GC s’exécute lorsque le tas secondaire ne dispose pas de suffisamment de mémoire pour les nouvelles allocations. S’il n’y a pas d’allocation, il ne s’exécute pas.

Les collections mineures sont peu coûteuses et fréquentes, et sont utilisées pour collecter les objets récemment alloués et morts. Les collections mineures sont effectuées après chaque Mo d’objets alloués. Les collections mineures peuvent être exécutées manuellement en appelant [gc. Collecter (0)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) 

Les collections majeures sont coûteuses et moins fréquentes, et sont utilisées pour récupérer tous les objets morts. Les collections principales sont effectuées une fois que la mémoire est épuisée pour la taille actuelle du tas (avant le redimensionnement du tas). Les collections majeures peuvent être effectuées manuellement en appelant [gc. Collect ()](xref:System.GC.Collect) ou en appelant [gc. Collect (int)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) avec l’argument [gc. MaxGeneration](xref:System.GC.MaxGeneration). 

## <a name="cross-vm-object-collections"></a>Collections d’objets entre machines virtuelles

Il existe trois catégories de types d’objets.

- **Objets managés**: types qui n’héritent *pas* de [java. lang. Object](xref:Java.Lang.Object) , par exemple [System. String](xref:System.String). 
    Celles-ci sont collectées normalement par le GC. 

- **Objets Java**: Types Java présents dans la machine virtuelle du runtime Android, mais non exposés à la machine virtuelle mono. Ces informations sont ennuyeuses et ne sont pas abordées plus en détail. Celles-ci sont collectées normalement par la machine virtuelle du runtime Android. 

- **Objets homologues**: types qui implémentent [IJavaObject](xref:Android.Runtime.IJavaObject) , par exemple, toutes les sous-classes [java. lang. Object](xref:Java.Lang.Object) et [java. lang. Throwable](xref:Java.Lang.Throwable) . Les instances de ces types ont deux « moitiés » un *pair géré* et un *pair natif*. L’homologue managé est une instance de C# la classe. L’homologue natif est une instance d’une classe Java au sein de la machine virtuelle du C# Runtime Android, et la propriété [IJavaObject. handle](xref:Android.Runtime.IJavaObject.Handle) contient une référence globale JNI à l’homologue natif. 

Il existe deux types d’homologues natifs :

- **Homologues de Framework** : Types Java « normaux » qui ne savent rien de Xamarin. Android, par exemple   [Android. Content. Context](xref:Android.Content.Context).

- **Pairs** de l’utilisateur :   Les [wrappers pouvant être appelés Android](~/android/platform/java-integration/working-with-jni.md) qui sont générés au moment de la génération pour chaque sous-classe Java. lang. Object présente dans l’application.

Comme il existe deux machines virtuelles au sein d’un processus Xamarin. Android, il existe deux types de garbage collection :

- Collections Runtime Android 
- Collections mono 

Les collections du runtime Android fonctionnent normalement, mais avec un inconvénient : une référence globale JNI est traitée comme une racine GC. Par conséquent, s’il existe une référence globale JNI contenant un objet de machine virtuelle du runtime Android, l’objet *ne peut pas* être collecté, même s’il est éligible pour la collecte.

Les regroupements mono sont là où le plaisir se produit. Les objets managés sont collectés normalement. Les objets homologues sont collectés en effectuant le processus suivant :

1. La référence globale JNI de tous les objets homologues éligibles à la collection mono est remplacée par une référence globale JNI faible. 

2. Un GC de machine virtuelle du runtime Android est appelé. Toute instance de pair Native peut être collectée. 

3. Les références globales universelles JNI créées dans (1) sont vérifiées. Si la référence faible a été collectée, l’objet homologue est collecté. Si la référence faible n’a *pas* été collectée, la référence faible est remplacée par une référence globale JNI et l’objet homologue n’est pas collecté. Remarque : sur l’API 14 +, cela signifie que la valeur retournée par `IJavaObject.Handle` peut changer après un gc. 

Le résultat final de tout cela est qu’une instance d’un objet homologue est active tant qu’elle est référencée par du code managé (par exemple, stockée `static` dans une variable) ou référencée par du code Java. En outre, la durée de vie des homologues natifs sera étendue au-delà de ce qu’ils seraient autrement en cours, car l’homologue natif ne sera pas collecté tant que l’homologue natif et l’homologue géré ne seront pas collectés.

## <a name="object-cycles"></a>Cycles d’objets

Les objets homologues sont logiquement présents dans le runtime Android et les machines virtuelles mono. Par exemple, une instance d’homologue gérée [Android. app. Activity](xref:Android.App.Activity) aura une instance java [Android. app. Activity](https://developer.android.com/reference/android/app/Activity.html) Framework homologue correspondante. Tous les objets qui héritent de [java. lang. Object](xref:Java.Lang.Object) peuvent être supposés avoir des représentations dans les deux machines virtuelles. 

Tous les objets qui ont une représentation dans les deux machines virtuelles auront des durées de vie qui sont étendues par rapport aux objets qui sont présents [`System.Collections.Generic.List<int>`](xref:System.Collections.Generic.List%601)uniquement dans une seule machine virtuelle (par exemple,). Appel de [gc. Collect](xref:System.GC.Collect) ne collecte pas nécessairement ces objets, car le GC Xamarin. Android doit s’assurer que l’objet n’est pas référencé par l’une ou l’autre machine virtuelle avant de le collecter. 

Pour raccourcir la durée de vie des objets, [java. lang. Object. Dispose ()](xref:Java.Lang.Object.Dispose) doit être appelé. Cela permet de « rompre » manuellement la connexion sur l’objet entre les deux machines virtuelles en libérant la référence globale, ce qui permet de collecter plus rapidement les objets. 

## <a name="automatic-collections"></a>Regroupements automatiques

À partir de la [version 4.1.0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/mono_for_android_4/mono_for_android_4.1.0/index.md), Xamarin. Android effectue automatiquement un GC complet lorsqu’un seuil Gref est franchi. Ce seuil est de 90% du nombre maximal connu de grefs pour la plateforme : 1800 grefs sur l’émulateur (2000 max) et 46800 grefs sur le matériel (52000 maximum). *Remarque :* Xamarin. Android compte uniquement les grefs créés par [Android. Runtime. JNIEnv](xref:Android.Runtime.JNIEnv), et ne connaît pas les autres grefs créés dans le processus. Il s’agit d’une méthode heuristique *uniquement*. 

Lorsqu’une collection automatique est exécutée, un message semblable au suivant s’affiche dans le journal de débogage :

```shell
I/monodroid-gc(PID): 46800 outstanding GREFs. Performing a full GC!
```

L’occurrence de ce n’est pas déterministe et peut se produire à des moments inopportuns (par exemple, au milieu du rendu graphique). Si vous voyez ce message, vous souhaiterez peut-être effectuer une collecte explicite ailleurs ou essayer de [réduire la durée de vie des objets homologues](#Helping_the_GC). 

## <a name="gc-bridge-options"></a>Options de pont GC

Xamarin. Android offre une gestion transparente de la mémoire avec Android et le runtime Android. Elle est implémentée en tant qu’extension du garbage collector mono, appelée *pont gc*. 

Le pont GC fonctionne pendant une garbage collection mono et détermine les objets homologues qui ont besoin de leur « activité » vérifiée avec le tas du runtime Android. Le pont GC effectue cette détermination en procédant comme suit (dans l’ordre) :

1. Induitz le graphique de référence mono des objets homologues inaccessibles dans les objets Java qu’ils représentent. 

2. Effectuez un GC Java.

3. Vérifiez quels objets sont vraiment morts. 

Ce processus compliqué permet aux sous-classes de de `Java.Lang.Object` faire référence librement à tous les objets C#. il supprime toutes les restrictions sur lesquelles les objets Java peuvent être liés. En raison de cette complexité, le processus de pont peut être très onéreux et peut provoquer des pauses perceptibles dans une application. Si l’application rencontre des pauses significatives, il est utile d’examiner l’une des trois implémentations de pont GC suivantes : 

- **Tarjan** : conception entièrement nouvelle du pont gc basée sur l' [algorithme de Robert Tarjan et propagation de référence vers l’arrière](https://en.wikipedia.org/wiki/Tarjan's_strongly_connected_components_algorithm).
    Il offre des performances optimales dans le cadre de nos charges de travail simulées, mais il possède également la plus grande part de code expérimental. 

- **Nouvelle** : révision majeure du code d’origine, correction de deux instances de comportement quadratique, mais maintien de l’algorithme principal (basé sur l' [algorithme de Kosaraju](https://en.wikipedia.org/wiki/Kosaraju's_algorithm) pour la recherche de composants fortement connectés). 

- **Old** : implémentation d’origine (considérée comme la plus stable des trois). Il s’agit de la passerelle qu’une application doit utiliser `GC_BRIDGE` si les pauses sont acceptables. 

La seule façon de déterminer quel pont GC fonctionne le mieux est d’expérimenter une application et d’analyser la sortie. Il existe deux façons de collecter les données à des fins d’évaluation : 

- **Activez** la journalisation-activer la journalisation (comme décrit dans la section [configuration](~/android/internals/garbage-collection.md) ) pour chaque option de pont GC, puis capturez et comparez les sorties de journal de chaque paramètre. Inspectez `GC` les messages pour chaque option, en particulier les `GC_BRIDGE` messages. Les interruptions jusqu’à 150 m pour les applications non interactives sont tolérables, mais les pauses au-dessus de 60 ms pour les applications très interactives (comme les jeux) posent problème. 

- **Activer la gestion** des ponts : la gestion des ponts affiche le coût moyen des objets pointés par chaque objet impliqué dans le processus de pont. Le tri de ces informations par taille fournira des indications sur ce qui détient le plus grand nombre d’objets supplémentaires. 

Pour spécifier l' `GC_BRIDGE` option qu’une application doit US, `bridge-implementation=old` `bridge-implementation=new` passer ou `bridge-implementation=tarjan` la `MONO_GC_PARAMS` variable d’environnement, par exemple : 

```shell
MONO_GC_PARAMS=bridge-implementation=tarjan
```

Le paramètre par défaut est **Tarjan**. Si vous trouvez une régression, il peut s’avérer nécessaire d’affecter la valeur **ancien**à cette option. Vous pouvez également choisir d’utiliser l' **ancienne** option la plus stable si **Tarjan** ne produit pas d’amélioration des performances. 

<a name="Helping_the_GC" />

## <a name="helping-the-gc"></a>Aider le GC

Il existe plusieurs façons d’aider le GC à réduire l’utilisation de la mémoire et les temps de collecte.

### <a name="disposing-of-peer-instances"></a>Suppression d’instances homologues

Le GC a une vue incomplète du processus et peut ne pas s’exécuter lorsque la mémoire est faible, car le GC ne sait pas que la mémoire est faible. 

Par exemple, une instance d’un type [java. lang. Object](xref:Java.Lang.Object) ou d’un type dérivé a une taille d’au moins 20 octets (susceptible d’être modifiée sans préavis, etc.). 
Les [wrappers pouvant être appelés](~/android/internals/architecture.md) n’ajoutent pas de membres d’instance supplémentaires. par conséquent, lorsque vous avez une instance [Android. Graphics. Bitmap](xref:Android.Graphics.Bitmap) qui fait référence à un blob de 10 Mo de &ndash; mémoire, le GC Xamarin. Android ne sait pas que le GC verra un objet de 20 octets et ne pourra pas déterminer qu’il est lié aux objets alloués par le runtime Android qui conservent 10 Mo de mémoire. 

Il est souvent nécessaire d’aider le garbage collector. Malheureusement, *gc. AddMemoryPressure ()* et *gc. RemoveMemoryPressure ()* n’étant pas pris en charge, si vous *savez* que vous venez de libérer un grand graphique d’objets alloués par Java, vous devrez peut-être appeler manuellement [gc. Collect ()](xref:System.GC.Collect) pour inviter un GC à libérer la mémoire côté Java, ou vous pouvez supprimer explicitement des sous-classes *java. lang. Object* , en rompant le mappage entre le wrapper managé et l’instance java. Par exemple, consultez le [bogue 1084](http://bugzilla.xamarin.com/show_bug.cgi?id=1084#c6). 

> [!NOTE]
> Vous devez être *extrêmement* vigilant lorsque vous supprimez `Java.Lang.Object` des instances de sous-classes.

Pour réduire le risque d’altération de la mémoire, respectez les consignes `Dispose()`suivantes lors de l’appel de.

#### <a name="sharing-between-multiple-threads"></a>Partage entre plusieurs threads

Si l’instance *Java ou Managed* instance peut être partagée entre plusieurs threads, *elle ne doit `Dispose()`jamais être d*. Par exemple,[`Typeface.Create()`](xref:Android.Graphics.Typeface.Create*) 
peut retourner une *instance mise en cache*. Si plusieurs threads fournissent les mêmes arguments, ils obtiendront la *même* instance. Par conséquent `Dispose()`, la ING `Typeface` de l’instance d’un thread peut invalider d’autres threads `ArgumentException`, ce qui `JNIEnv.CallVoidMethod()` peut entraîner des s à partir de (entre autres), car l’instance a été supprimée d’un autre thread. 

#### <a name="disposing-bound-java-types"></a>Suppression de types Java liés

Si l’instance est de type Java lié, l’instance peut être supprimée *tant que* l’instance n’est pas réutilisée à partir du code managé *et* que l’instance Java ne peut pas être partagée entre les `Typeface.Create()` threads (voir la discussion précédente). (Il peut être difficile d’effectuer cette détermination.) La prochaine fois que l’instance java entre du code managé, un *nouveau* Wrapper sera créé pour celle-ci. 

Cela s’avère souvent utile lorsqu’il s’agit d’Drawables et d’autres instances à ressources lourdes :

```csharp
using (var d = Drawable.CreateFromPath ("path/to/filename"))
    imageView.SetImageDrawable (d);
```

L’opération ci-dessus est sécurisée, car l’homologue que [Drawing. CreateFromPath ()](xref:Android.Graphics.Drawables.Drawable.CreateFromPath*) retourne fait référence à un homologue d’infrastructure, et *non* à un homologue d’utilisateur. L' `Dispose()` appel à la fin `using` du bloc rompt la relation entre les instances de dessin pouvant être dessinées [et le](xref:Android.Graphics.Drawables.Drawable) [Framework,](https://developer.android.com/reference/android/graphics/drawable/Drawable.html) ce qui permet de collecter l’instance java dès que le runtime Android en a besoin. Cela *ne serait pas* sûr si l’instance de pair faisait appel à un homologue d’utilisateur ; ici, nous utilisons des informations « externes » pour *savoir* que `Drawable` ne peut pas faire référence à un homologue d' `Dispose()` utilisateur et, par conséquent, l’appel est sécurisé. 

#### <a name="disposing-other-types"></a>Suppression d’autres types 

Si l’instance fait référence à un type qui n’est pas une liaison d’un type Java (tel `Activity`qu’un personnalisé), `Dispose()` **n’appelez pas** , sauf si vous *savez* qu’aucun code Java n’appellera des méthodes substituées sur cette instance. Si ce n’est pas le [ `NotSupportedException`cas, le résultat est.](~/android/internals/architecture.md#Premature_Dispose_Calls) 

Par exemple, si vous avez un écouteur de clic personnalisé :

```csharp
partial class MyClickListener : Java.Lang.Object, View.IOnClickListener {
    // ...
}
```

Vous ne *devez pas* supprimer cette instance, car Java va tenter d’appeler des méthodes sur celle-ci à l’avenir :

```csharp
// BAD CODE; DO NOT USE
Button b = FindViewById<Button> (Resource.Id.myButton);
using (var listener = new MyClickListener ())
    b.SetOnClickListener (listener);
```

#### <a name="using-explicit-checks-to-avoid-exceptions"></a>Utilisation de vérifications explicites pour éviter les exceptions

Si vous avez implémenté une méthode [java. lang. Object. dispose](xref:Java.Lang.Object.Dispose*) Overload, évitez de toucher les objets qui impliquent JNI. Cela peut créer une situation de *double dispose* qui permet à votre code d’essayer (irrémédiablement) d’accéder à un objet Java sous-jacent qui a déjà été récupéré par le garbage collector. Cela génère une exception semblable à la suivante : 

```shell
System.ArgumentException: 'jobject' must not be IntPtr.Zero.
Parameter name: jobject
at Android.Runtime.JNIEnv.CallVoidMethod
```

Cette situation se produit souvent lorsque la première suppression d’un objet entraîne la création d’une valeur NULL par un membre, puis une nouvelle tentative d’accès sur ce membre null entraîne la levée d’une exception. Plus précisément, l’objet `Handle` (qui lie une instance gérée à son instance Java sous-jacente) est invalidé lors de la première suppression, mais le code managé tente toujours d’accéder à cette instance Java sous-jacente, même si elle n’est plus disponible (voir [ Wrappers pouvant être appelés](~/android/internals/architecture.md#Managed_Callable_Wrappers) pour plus d’informations sur le mappage entre les instances Java et les instances managées. 

Pour éviter cette exception, il est judicieux de vérifier explicitement dans votre `Dispose` méthode que le mappage entre l’instance gérée et l’instance Java sous-jacente est toujours valide ; autrement dit, vérifier si la valeur `Handle` de l’objet`IntPtr.Zero`est null () avant d’accéder à ses membres. Par exemple, la méthode `Dispose` suivante accède à un `childViews` objet : 

```csharp
class MyClass : Java.Lang.Object, ISomeInterface 
{
    protected override void Dispose (bool disposing)
    {
        base.Dispose (disposing);
        for (int i = 0; i < this.childViews.Count; ++i)
        {
            // ...
        }
    }
}
```

Si une étape de suppression initiale `childViews` provoque un non valide `Handle`, l' `for` accès à la boucle lèvera `ArgumentException`une. Si vous ajoutez une `Handle` vérification de valeur null explicite `childViews` avant le premier accès `Dispose` , la méthode suivante empêche l’exception de se produire : 

```csharp
class MyClass : Java.Lang.Object, ISomeInterface 
{
    protected override void Dispose (bool disposing)
    {
        base.Dispose (disposing);

        // Check for a null handle:
        if (this.childViews.Handle == IntPtr.Zero)
            return;

        for (int i = 0; i < this.childViews.Count; ++i)
        {
            // ...
        }
    }
}
```

### <a name="reduce-referenced-instances"></a>Réduire les instances référencées

Chaque fois qu’une instance `Java.Lang.Object` d’un type ou d’une sous-classe est analysée pendant le GC, le *graphique d’objets* entier auquel l’instance fait référence doit également être analysé. Le graphique d’objet est le jeu d’instances d’objet auquel fait référence l’instance racine, *ainsi* que tout ce qui est référencé par ce que fait l’instance racine, de manière récursive. 

Considérons la classe suivante :

```csharp
class BadActivity : Activity {

    private List<string> strings;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        strings.Value = new List<string> (
                Enumerable.Range (0, 10000)
                .Select(v => new string ('x', v % 1000)));
    }
}
```

Lorsque `BadActivity` est construit, le graphique d’objets contient 10004 instances (1x `BadActivity`, 1x `strings`, 1x `string[]` détenues par `strings`, instances de chaîne 10000x), qui doivent *toutes* être analysées chaque fois que `BadActivity` l’instance est analysée. 

Cela peut avoir un impact négatif sur vos heures de collecte, entraînant une augmentation des temps de pause GC. 

Vous pouvez aider le catalogue global en *réduisant* la taille des graphiques d’objets qui sont enracinés par des instances d’homologue utilisateur. Dans l’exemple ci-dessus, vous pouvez effectuer cette `BadActivity.strings` opération en déplaçant dans une classe distincte qui n’hérite pas de Java. lang. Object : 

```csharp
class HiddenReference<T> {

    static Dictionary<int, T> table = new Dictionary<int, T> ();
    static int idgen = 0;

    int id;

    public HiddenReference ()
    {
        lock (table) {
            id = idgen ++;
        }
    }

    ~HiddenReference ()
    {
        lock (table) {
            table.Remove (id);
        }
    }

    public T Value {
        get { lock (table) { return table [id]; } }
        set { lock (table) { table [id] = value; } }
    }
}

class BetterActivity : Activity {

    HiddenReference<List<string>> strings = new HiddenReference<List<string>>();

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        strings.Value = new List<string> (
                Enumerable.Range (0, 10000)
                .Select(v => new string ('x', v % 1000)));
    }
}
```

## <a name="minor-collections"></a>Collections secondaires

Les collections mineures peuvent être exécutées manuellement en appelant [gc. Collecter (0)](xref:System.GC.Collect). Les collections mineures sont peu coûteuses (par rapport aux collections majeures), mais elles ont un coût fixe significatif, donc vous ne souhaitez pas les déclencher trop souvent et vous devez avoir une durée de pause de quelques millisecondes. 

Si votre application a un « cycle de travail » dans lequel la même chose est effectuée de façon répétée, il peut être recommandé d’effectuer manuellement un regroupement mineur une fois que le cycle d’utilisation est terminé. Voici quelques exemples de cycles de responsabilité : 

- Le cycle de rendu d’une trame de jeu unique.
- Interaction complète avec une boîte de dialogue d’application donnée (ouverture, remplissage, fermeture) 
- Groupe de demandes réseau pour actualiser/synchroniser des données d’application.

## <a name="major-collections"></a>Collections principales

Les collections majeures peuvent être effectuées manuellement en appelant [gc. Collect ()](xref:System.GC.Collect) ou `GC.Collect(GC.MaxGeneration)`. 

Elles doivent être exécutées rarement et peuvent avoir une durée de pause d’une seconde sur un appareil de style Android lors de la collecte d’un segment de mémoire de 512 Mo. 

Les collections principales doivent être appelées manuellement, si jamais : 

- À la fin des cycles de droits longs et lorsqu’une pause longue ne présente pas de problème à l’utilisateur. 

- Dans une méthode [Android. app. Activity. OnLowMemory ()](xref:Android.App.Activity.OnLowMemory) remplacée. 

## <a name="diagnostics"></a>Diagnostics

Pour suivre le moment où les références globales sont créées et détruites, vous pouvez définir la propriété système [Debug. mono. log](~/android/troubleshooting/index.md) de sorte qu’elle contienne [*Gref*](~/android/troubleshooting/index.md) et/ou [*GC*](~/android/troubleshooting/index.md). 

## <a name="configuration"></a>Configuration

Le garbage collector Xamarin. Android peut être configuré en définissant `MONO_GC_PARAMS` la variable d’environnement. Les variables d’environnement peuvent être définies avec une action de génération [AndroidEnvironment](~/android/deploy-test/environment.md).

La `MONO_GC_PARAMS` variable d’environnement est une liste séparée par des virgules des paramètres suivants : 

- `nursery-size` = *taille* : Définit la taille de la pépinière. La taille est spécifiée en octets et doit être une puissance de deux. Les suffixes `k` `m` et`g` peuvent être utilisés pour spécifier les kilo-octets, Mega-et-gigaoctets, respectivement. La pépinière est la première génération (de deux). Une plus grande pépinière accélérera généralement le programme, mais utilisera évidemment plus de mémoire. Taille de la pépinière par défaut de 512 Ko. 

- `soft-heap-limit` = *taille* : Consommation maximale de mémoire managée cible pour l’application. Lorsque l’utilisation de la mémoire est inférieure à la valeur spécifiée, le GC est optimisé pour le temps d’exécution (moins de collections). 
    Au-delà de cette limite, le GC est optimisé pour l’utilisation de la mémoire (autres collections). 

- `evacuation-threshold` = *seuil* : Définit le seuil d’évacuation en pourcentage. La valeur doit être un entier compris entre 0 et 100. La valeur par défaut est 66. Si la phase de balayage de la collection constate que l’occupation d’un type de bloc de segment de mémoire spécifique est inférieure à ce pourcentage, il effectue une collection de copie pour ce type de bloc dans la prochaine collection principale, ce qui restaure l’occupation à une valeur proche de 100%. La valeur 0 désactive l’évacuation. 

- `bridge-implementation` = *implémentation du pont* : Cela permet de définir l’option de pont GC pour résoudre les problèmes de performances du catalogue global. Il existe trois valeurs possibles : *Old* , *New* , *Tarjan*.

- `bridge-require-precise-merge`: Le pont Tarjan contient une optimisation qui peut, dans de rares occasions, entraîner la collecte d’un objet GC après la première tentative de garbage collection. L’inclusion de cette option désactive cette optimisation, ce qui rend les GC plus prévisibles, mais potentiellement plus lents.

Par exemple, pour configurer le GC pour qu’il ait une limite de taille de segment de mémoire de 128 Mo, ajoutez un nouveau fichier à `AndroidEnvironment` votre projet avec une **action de génération** avec le contenu : 

```shell
MONO_GC_PARAMS=soft-heap-limit=128m
```
