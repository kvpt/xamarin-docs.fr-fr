---
title: Garbage Collection
ms.prod: xamarin
ms.assetid: 298139E2-194F-4A58-BC2D-1D22231066C4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: 09466cc9eed4899ef0aa1198ff0aee5cd420e110
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674635"
---
# <a name="garbage-collection"></a>Garbage Collection

Xamarin.Android utilise Mono [RÉCUPÉRATEUR de mémoire générationnel Simple](https://www.mono-project.com/docs/advanced/garbage-collector/sgen/). Il s’agit d’un RÉCUPÉRATEUR de mémoire mark-and-sweep avec deux générations et un *espace d’objets volumineux*, avec deux types de collections : 

-   Collections mineures (segment de mémoire Gen0 de collecte) 
-   Collections majeures (tas espacement collecte Gen1 et des objets volumineux). 

> [!NOTE]
> En l’absence d’une collection explicite via [GC. Collect()](xref:System.GC.Collect) sont des collections *à la demande*, en fonction des allocations de tas. *Cela n’est pas un système de comptage de références*; objets *ne seront pas collectées dès qu’il n’existe aucune référence en suspens*, ou quand une étendue s’est arrêté. Le garbage collector s’exécute lorsque le tas secondaire a suffisamment de mémoire pour les nouvelles allocations. S’il n’y a pas d’allocations, il ne fonctionnera pas.


Collections mineures sont peu onéreuse et fréquentes et sont utilisées pour collecter les objets morts et récemment alloués. Collections mineures sont effectuées après chaque quelques Mo des objets alloués. Collections secondaires peuvent être effectuées manuellement en appelant [GC. Collecter (0)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) 

Collections principales sont coûteuses et moins fréquentes et sont utilisées pour récupérer tous les objets morts. Collections principales sont effectuées une fois que la mémoire est épuisée pour la taille de segment de mémoire actuelle (avant le redimensionnement du tas). Collections majeures peuvent être effectuées manuellement en appelant [GC. Collecter ()](xref:System.GC.Collect) ou en appelant [GC. Collecter (int)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) avec l’argument [GC. MaxGeneration](xref:System.GC.MaxGeneration). 



## <a name="cross-vm-object-collections"></a>Collections d’objets entre les machines virtuelles

Il existe trois catégories de types d’objets.

-   **Objets managés**: types de font *pas* héritent [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) , par exemple [System.String](xref:System.String). 
    Ils sont collectés normalement par le garbage collector. 

-   **Les objets Java**: Types Java qui sont présents dans la machine virtuelle du runtime Android, mais non exposées à la machine virtuelle Mono. Ceux-ci sont ennuyeux et ne seront pas traitées davantage. Ils sont collectés normalement par le runtime Android machine virtuelle. 

-   **Objets de l’homologue**: les types qui implémentent [IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) , par exemple, tous les [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) et [Java.Lang.Throwable](https://developer.xamarin.com/api/type/Java.Lang.Throwable/) sous-classes. Les instances de ces types ont deux « halfs » un *homologue managé* et un *homologue natif*. L’homologue géré est une instance de la C# classe. L’homologue natif est une instance d’une classe Java dans le runtime Android machine virtuelle et le C# [IJavaObject.Handle](https://developer.xamarin.com/api/property/Android.Runtime.IJavaObject.Handle/) propriété contient une référence globale de JNI à l’homologue natif. 


Il existe deux types de pairs natifs :

-   **Framework homologues** : Les types Java « Normal » qui savent rien de Xamarin.Android, par exemple, [android.content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/).

-   **Utilisateur homologues** : [Les Wrappers RCW Android](~/android/platform/java-integration/working-with-jni.md) qui sont généré au moment de la génération pour chaque sous-classe Java.Lang.Object présente dans l’application.


Qu’il sont a deux machines virtuelles au sein d’un processus de Xamarin.Android, il existe deux types de garbage collection :

-   Collections de runtime Android 
-   Collections de mono 

Collections de runtime Android fonctionnent normalement, mais avec un inconvénient : une référence globale de JNI est traitée comme une racine GC. Par conséquent, s’il existe un JNI global référencer un runtime Android détention d’objet de machine virtuelle, l’objet *ne peut pas* collectés, même s’il est éligible pour la collection.

Collections mono sont où se produit la partie amusante. Les objets managés sont collectés normalement. Objets homologues sont collectés en effectuant le processus suivant :

1.  Tous les objets homologues éligibles pour la collection Mono ont leur référence globale de JNI remplacée par une référence globale faible JNI. 

2.  Un runtime Android que GC de machine virtuelle est appelée. N’importe quelle instance de l’homologue natif peut-être être collectée. 

3.  Les global les références faibles JNI créées dans (1) sont activés. Si la référence faible ont été collectée, l’objet homologue est collectée. Si la référence faible a *pas* été collectés, puis la référence faible est remplacée par une référence globale de JNI et l’objet homologue n’est pas collectée. Remarque : sur les API 14 +, cela signifie que la valeur retournée par `IJavaObject.Handle` peut changer après un catalogue global. 

Le résultat final est qu’une instance d’un objet homologue se trouvera tant qu’il est référencé à l’aide du code managé (par exemple, stockés dans un `static` variable) ou référencé par le code Java. En outre, la durée de vie de pairs natifs sera étendue au-delà de ce qu’ils avaient sinon en direct, l’homologue natif ne sera pas pouvant être collecté jusqu'à ce que l’homologue natif et l’homologue managé sont pouvant être collectés.


## <a name="object-cycles"></a>Cycles de l’objet

Objets de pair sont logiquement présentes dans le runtime Android et Mono de la machine virtuelle. Par exemple, un [Android.App.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/) instance managée homologue aura un correspondant [android.app.Activity](https://developer.android.com/reference/android/app/Activity.html) instance du framework homologue Java. Tous les objets qui héritent de [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) peut s’attendre à avoir des représentations dans les deux machines virtuelles. 

Tous les objets dont la représentation dans les deux machines virtuelles seront ont des durées de vie qui sont étendues par rapport aux objets qui sont présents uniquement dans une seule machine virtuelle (comme un [ `System.Collections.Generic.List<int>` ](xref:System.Collections.Generic.List%601)). Appel [GC. Collecter](xref:System.GC.Collect) ne collectons pas nécessairement ces objets, comme le GC Xamarin.Android doit s’assurer que l’objet n’est pas référencé par une machine virtuelle avant sa collecte. 

Pour raccourcir la durée de vie d’objet, [Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/) doit être appelé. Cela manuellement » interrompt « la connexion sur l’objet entre les deux machines virtuelles en libérant la référence globale, autorisant ainsi les objets devant être collectées plus rapidement. 


## <a name="automatic-collections"></a>Collections automatique

À partir de [version 4.1.0](https://developer.xamarin.com/releases/android/mono_for_android_4/mono_for_android_4.1.0), Xamarin.Android effectue automatiquement un GC complet lorsqu’un seuil gref est atteint. Ce seuil est de 90 % de la grefs maximale connus pour la plateforme : grefs 1800 sur l’émulateur (2000 max) et 46800 grefs sur du matériel (52000 maximale). *Remarque :* Xamarin.Android ne compte que le grefs créé par [Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)et ne saura pas sur n’importe quel autre grefs créé dans le processus. Il s’agit d’une méthode heuristique *uniquement*. 

Lorsqu’une collection automatique est effectuée, un message semblable au suivant s’affichera dans le journal de débogage :

```shell
I/monodroid-gc(PID): 46800 outstanding GREFs. Performing a full GC!
```

L’occurrence de ce n’est pas déterministe et peut se produire à des moments inopportuns (par exemple, au milieu d’un rendu de graphiques). Si vous voyez ce message, vous pouvez souhaiter effectuer une collection explicite dans un autre emplacement, ou voulez-vous essayez [réduire la durée de vie des objets homologues](#Helping_the_GC). 

## <a name="gc-bridge-options"></a>Options de pont de GC

Xamarin.Android offre une gestion de mémoire transparent avec Android et le runtime Android. Il est implémenté en tant qu’extension au garbage collector Mono appelé le *GC pont*. 

Le pont GC fonctionne pendant un nettoyage Mono et les figures out quels homologue objets doit leur « activité » vérifiée avec le tas du runtime Android. Le pont GC opère cette détermination en procédant comme suit (dans l’ordre) :

1.  Provoquer le graphe de référence mono d’objets homologues inaccessible dans les objets Java qu’ils représentent. 

2.  Effectuer un GC de Java.

3.  Vérifiez quels objets sont morts vraiment. 

Ce processus complexe est ce qui permet aux sous-classes de `Java.Lang.Object` à référence librement existe-t-il des objets ; il supprime toutes les restrictions sur le Java objets peuvent être liés à C#. En raison de cette complexité, le processus de pont peut être très coûteux et peut provoquer des pauses notables dans une application. Si l’application rencontre des pauses significatifs, il est conseillé de rechercher un des trois implémentations de pont de GC suivantes : 

-   **Tarjan** -une entièrement nouvelle conception du pont GC basée sur [algorithme de Robert Tarjan et vers l’arrière référence la propagation](https://en.wikipedia.org/wiki/Tarjan's_strongly_connected_components_algorithm).
    Il offre les meilleures performances sous nos charges de travail simulées, mais il a également le partage de code expérimentale plus volumineux. 

-   **Nouvelle** -révision majeure du code d’origine, résolution des deux instances de comportement quadratique mais en conservant l’algorithme principal (selon [algorithme de Kosaraju](https://en.wikipedia.org/wiki/Kosaraju's_algorithm) pour recherche fortement des composants connectés). 

-   **Ancien** -l’implémentation d’origine (considéré comme le plus stable des trois). Il s’agit d’une application doit utiliser si le pont le `GC_BRIDGE` pauses sont acceptables. 


La seule façon de déterminer qui fonctionne GC pont mieux est en expérimentant dans une application et en analysant la sortie. Il existe deux façons de collecter les données pour l’analyse comparative : 

-   **Activer la journalisation** -activer la journalisation (comme décrit dans la [Configuration](~/android/internals/garbage-collection.md) section) pour chaque option de pont de catalogue global, puis capturer et comparer les sorties de journal à partir de chaque paramètre. Inspecter le `GC` messages pour chaque option, en particulier la `GC_BRIDGE` messages. Met en pause jusqu'à 150ms pour les applications non interactives sont inacceptables, mais les pauses au-dessus de 60 MS pour les applications très interactives (par exemple, des jeux) sont un problème. 

-   **Activer la gestion de pont** -comptabilité de pont affiche le coût moyen des objets pointés par chaque objet impliqué dans le processus de pont. Ces informations de tri par taille sera fournir des indications quant à ce qui maintient la plus grande quantité d’objets supplémentaires. 


Pour spécifier quel `GC_BRIDGE` option une application doit utiliser, passez `bridge-implementation=old`, `bridge-implementation=new` ou `bridge-implementation=tarjan` à la `MONO_GC_PARAMS` variable d’environnement, par exemple : 

```shell
MONO_GC_PARAMS=bridge-implementation=tarjan
```

Le paramètre par défaut est **Tarjan**. Si vous trouvez une régression, vous pouvez s’avérer nécessaire de définir cette option **ancien**. En outre, vous pouvez choisir d’utiliser le plus stable **ancien** option si **Tarjan** ne génère pas d’amélioration des performances. 

<a name="Helping_the_GC" />

## <a name="helping-the-gc"></a>Aider le garbage collector

Il existe plusieurs façons de vous aider au GC pour réduire les durées de collecte et utilisation de mémoire.



### <a name="disposing-of-peer-instances"></a>Suppression d’instances de l’homologue

Le garbage collector comporte une vue complète des processus et peut ne pas exécuter lorsque la mémoire est faible, car le garbage collector ne sait pas que la mémoire est faible. 

Par exemple, une instance d’un [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) type ou type dérivé est au moins de 20 octets (susceptibles de changer sans préavis, etc., etc..). 
[Des Callable Wrappers managés](~/android/internals/architecture.md) n’ajoutez pas de membres d’instance supplémentaire, par conséquent, lorsque vous avez un [Android.Graphics.Bitmap](https://developer.xamarin.com/api/type/Android.Graphics.Bitmap/) instance qui fait référence à un objet blob de 10 Mo de mémoire, GC de Xamarin.Android ne saura pas que &ndash; le GC s’affiche un objet de 20 octets et ne peut pas déterminer qui est lié à Android objets alloués par runtime qui consiste à conserver les 10 Mo de mémoire actif. 

Il est souvent nécessaire aider le garbage collector. Malheureusement, *GC. AddMemoryPressure()* et *GC. RemoveMemoryPressure()* ne sont pas pris en charge, par conséquent, si vous *savoir* que vous avez libéré simplement un graphique des objets volumineux alloués par Java vous deviez appeler manuellement [GC. Collect()](xref:System.GC.Collect) à l’invite un catalogue global pour libérer le côté Java mémoire, ou vous pouvez supprimer explicitement les *Java.Lang.Object* sous-classes, interrompre le mappage entre le wrapper managé et l’instance de Java. Par exemple, consultez [bogue 1084](http://bugzilla.xamarin.com/show_bug.cgi?id=1084#c6). 


> [!NOTE]
> Vous devez être *extrêmement* prudent lors de la suppression de `Java.Lang.Object` instances de la sous-classe.

Pour minimiser les risques de corruption de la mémoire, respectez les consignes suivantes lors de l’appel `Dispose()`.


#### <a name="sharing-between-multiple-threads"></a>Partage entre plusieurs Threads

Si le *Java ou gérés* instance peut-être être partagée entre plusieurs threads, *ne doit pas être `Dispose()`d*, **jamais**. Par exemple, [`Typeface.Create()`](https://developer.xamarin.com/api/member/Android.Graphics.Typeface.Create/(System.String%2cAndroid.Graphics.TypefaceStyle)) 
peut retourner un *instance mise en cache*. Si plusieurs threads fournissent les mêmes arguments, ils obtiendront la *même* instance. Par conséquent, `Dispose()`binaire de la `Typeface` instance à partir d’un seul thread peut invalider des autres threads, ce qui peuvent entraîner `ArgumentException`s à partir de `JNIEnv.CallVoidMethod()` (entre autres), car l’instance a été supprimée à partir d’un autre thread. 


#### <a name="disposing-bound-java-types"></a>Types Java lié en cours de suppression

Si l’instance est d’un type Java lié, l’instance peut être supprimé de *tant que* l’instance n’est pas réutilisé à partir de code managé *et* l’instance Java ne peut pas être partagé entre threads (voir la section précédente `Typeface.Create()` discussion). (Cette décision peut être difficile.) La prochaine fois que l’instance Java entre dans code managé, un *nouveau* wrapper sera créé pour celui-ci. 

C’est souvent utile lorsqu’il s’agit de Drawables et d’autres instances nécessitant beaucoup de ressources :

```csharp
using (var d = Drawable.CreateFromPath ("path/to/filename"))
    imageView.SetImageDrawable (d);
```

La méthode ci-dessus est sécurisée, car l’homologue qui [Drawable.CreateFromPath()](https://developer.xamarin.com/api/member/Android.Graphics.Drawables.Drawable.CreateFromPath/) retourne fait référence à un homologue de Framework, *pas* un homologue de l’utilisateur. Le `Dispose()` appeler à la fin de la `using` bloc rompt la relation entre managé [Drawable](https://developer.xamarin.com/api/type/Android.Graphics.Drawables.Drawable/) et framework [Drawable](https://developer.android.com/reference/android/graphics/drawable/Drawable.html) instances, ce qui permet de l’instance de Java être collectées dès que le runtime Android a besoin. Cela serait *pas* être sûr si l’instance d’homologue auquel un homologue de l’utilisateur ; nous utilisons ici des informations de « externes » pour *savoir* qui le `Drawable` ne peut pas faire référence à un homologue de l’utilisateur et donc le `Dispose()` appeler est plus sûr. 


#### <a name="disposing-other-types"></a>Suppression d’autres Types 

Si l’instance fait référence à un type qui n’est pas une liaison d’un type Java (par exemple un personnalisé `Activity`), **ne sont pas** appeler `Dispose()` , sauf si vous *savoir* qu’aucun code Java n’appelle les méthodes substituées sur qui instance. Pour ce faire, manquement [ `NotSupportedException`s](~/android/internals/architecture.md#Premature_Dispose_Calls). 

Par exemple, si vous avez personnalisé sur le port d’écoute :

```csharp
partial class MyClickListener : Java.Lang.Object, View.IOnClickListener {
    // ...
}
```

Vous *ne doivent pas* dispose de cette instance, comme Java essaiera d’appeler des méthodes sur celle-ci à l’avenir :

```csharp
// BAD CODE; DO NOT USE
Button b = FindViewById<Button> (Resource.Id.myButton);
using (var listener = new MyClickListener ())
    b.SetOnClickListener (listener);
```


#### <a name="using-explicit-checks-to-avoid-exceptions"></a>À l’aide de vérifications explicites pour éviter les Exceptions

Si vous avez implémenté un [Java.Lang.Object.Dispose](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose(System.Boolean)/) surcharge de méthode, évitez de toucher les objets qui impliquent de JNI. Cette opération peut entraîner un *dispose de double* situation qui rend possible pour que votre code (fatal) essayez d’accéder à un objet Java sous-jacent qui a déjà été garbage collector. Cette action génère une exception semblable à ce qui suit : 

```shell
System.ArgumentException: 'jobject' must not be IntPtr.Zero.
Parameter name: jobject
at Android.Runtime.JNIEnv.CallVoidMethod
```

Fréquence à laquelle cette situation se produit lorsque la premier dispose d’un objet entraîne un membre devienne null, et ensuite une tentative d’accès suivantes sur ce membre null provoque une exception levée. Plus précisément, l’objet `Handle` (qui lie une instance gérée à son instance Java sous-jacent) est invalidé sur la premier dispose, mais le code managé tente toujours d’accéder à cette instance de Java sous-jacent même si elle n’est plus disponible (voir [ Géré les Wrappers RCW](~/android/internals/architecture.md#Managed_Callable_Wrappers) pour plus d’informations sur le mappage entre les instances de Java et des instances gérées). 

Un bon moyen d’éviter cette exception consiste à vérifier explicitement dans votre `Dispose` méthode le mappage entre l’instance gérée et l’instance Java sous-jacente est encore valide ; c'est-à-dire, vérifiez si l’objet `Handle` a la valeur null (`IntPtr.Zero`) avant d’accéder à ses membres. Par exemple, ce qui suit `Dispose` méthode accède un `childViews` objet : 

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

Si une suppression initiale passez causes `childViews` pour avoir un non valide `Handle`, le `for` accès à la boucle lève une `ArgumentException`. En ajoutant un texte explicite `Handle` null vérification avant la première `childViews` accéder, ce qui suit `Dispose` méthode empêche cette exception se produise : 

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


### <a name="reduce-referenced-instances"></a>Réduire les Instances référencés

Chaque fois qu’une instance d’un `Java.Lang.Object` type ou sous-classe est analysée pendant le garbage collector, l’intégralité de *graphique d’objet* que l’instance fait référence à doit également être analysé. Le graphique d’objets est l’ensemble des instances d’objet qui fait référence l’instance « racine », *plus* tous les éléments référencés par le l’instance racine fait référence à, de manière récursive. 

Considérez la classe suivante :

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

Lorsque `BadActivity` est construit, le graphique d’objet contient des 10004 instances (1 x `BadActivity`, 1 x `strings`, 1 x `string[]` détenues par `strings`, 10000 x instances de chaîne), *tous les* de qui devra être analysés chaque fois que le `BadActivity` instance est analysée. 

Cela peut avoir un impact négatif sur vos heures de collection, ce qui augmente les temps de pause de GC. 

Vous pouvez aider le GC par *réduisant* la taille de graphiques d’objets qui sont inclus par les instances utilisateur homologue. Dans l’exemple ci-dessus, cela est possible en déplaçant `BadActivity.strings` dans une classe distincte qui n’hérite pas de Java.Lang.Object : 

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


## <a name="minor-collections"></a>Collections mineures

Collections secondaires peuvent être effectuées manuellement en appelant [GC. Collect(0)](xref:System.GC.Collect). Collections mineures sont peu coûteuses (en comparaison des collections principales), mais ont un coût fixe, donc vous ne souhaitez pas les déclenchent trop souvent et doit avoir un temps de pause de quelques millisecondes. 

Si votre application comporte un « cycle d’utilisation » dans lequel la même chose est effectuée maintes et maintes fois, il peut être recommandé d’effectuer manuellement une collection secondaire une fois que le cycle d’utilisation s’est terminée. Cycles de travail d’exemple sont les suivantes : 

-  Cycle de rendu d’une seule image de jeu.
-  L’intégralité de l’interaction avec une boîte de dialogue d’application donnée (ouverture, remplissage, fermeture) 
-  Un groupe de demandes de réseau à la synchronisation/actualisation des données d’application.



## <a name="major-collections"></a>Collections principales

Collections majeures peuvent être effectuées manuellement en appelant [GC. Collect()](xref:System.GC.Collect) ou `GC.Collect(GC.MaxGeneration)`. 

Ils doivent être rarement exécutées et peut-être un temps de pause d’une seconde sur un appareil Android-style lors de la collecte d’un segment de mémoire de 512 Mo. 

Collections majeures doivent uniquement être appelées manuellement, si jamais : 

-   À la fin du droit de long cycles et lorsque la valeur de type long suspendez ne présentent un problème à l’utilisateur. 

-   Au sein d’un substituée [Android.App.Activity.OnLowMemory()](https://developer.xamarin.com/api/member/Android.App.Activity.OnLowMemory/) (méthode). 



## <a name="diagnostics"></a>Diagnostics

Pour effectuer le suivi des références globales sont créés et détruits, vous pouvez définir le [debug.mono.log](~/android/troubleshooting/index.md) propriété système pour contenir [ *gref* ](~/android/troubleshooting/index.md) et/ou [ *gc*](~/android/troubleshooting/index.md). 



## <a name="configuration"></a>Configuration

Le garbage collector de Xamarin.Android peut être configuré en définissant le `MONO_GC_PARAMS` variable d’environnement. Variables d’environnement peuvent être définies avec une action de génération [AndroidEnvironment](~/android/deploy-test/environment.md).

Le `MONO_GC_PARAMS` variable d’environnement est une liste séparée par des virgules des paramètres suivants : 

-   `nursery-size` = *taille* : Définit la taille de la « nursery ». La taille est spécifiée en octets et doit être une puissance de deux. Les suffixes `k` , `m` et `g` peuvent être utilisées pour spécifier les kilo, méga et gigaoctets, respectivement. La « nursery » est la première génération (de deux). Une plus grande nursery accélérera généralement le programme mais évidemment utilise davantage de mémoire. La « nursery » par défaut de taille 512 Ko. 

-   `soft-heap-limit` = *taille* : La consommation de mémoire managée maximale cible pour l’application. Lors de l’utilisation de la mémoire est inférieure à la valeur spécifiée, le GC est optimisé pour la durée d’exécution (collections moins). 
    Au-delà de cette limite, le GC est optimisé pour l’utilisation de la mémoire (collections plus). 

-   `evacuation-threshold` = *seuil* : Définit le seuil d’évacuation en pourcentage. La valeur doit être un entier compris entre 0 et 100. La valeur par défaut est 66. Si la phase de balayage de la collection détecte que le niveau d’occupation d’un type de bloc de tas spécifique est inférieure à ce pourcentage, il le fera une collection de copie pour ce type de bloc dans la collection majeure suivante, restaurant ainsi l’occupation à presque 100 %. La valeur 0 désactive d’évacuation. 

-   `bridge-implementation` = *implémentation de combler* : Cela définira l’option GC pont pour aider à résoudre les problèmes de performances de GC. Il existe trois valeurs possibles : *ancien* , *nouveau* , *tarjan*.

-   `bridge-require-precise-merge`: Le Tarjan pont contient une optimisation, ce qui peut, dans de rares occasions, un objet à être collectées un GC dès qu’il est tout d’abord garbage. Y compris de cette option désactive cette optimisation, rendre les catalogues globaux plus prévisible, mais potentiellement plus lent.

Par exemple, pour configurer le catalogue global avec une limite de taille de segment de mémoire de 128 Mo, ajouter un nouveau fichier à votre projet avec un **action de génération** de `AndroidEnvironment` avec le contenu : 

```shell
MONO_GC_PARAMS=soft-heap-limit=128m
```
