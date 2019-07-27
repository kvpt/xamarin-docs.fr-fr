---
title: Principes de conception de l’API Xamarin. Android
ms.prod: xamarin
ms.assetid: 3E52D815-D95D-5510-0D8F-77DAC7E62EDE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 2958e456aeb25ba39697ad82500d574907e963e4
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510762"
---
# <a name="xamarinandroid-api-design-principles"></a>Principes de conception de l’API Xamarin. Android

En plus des bibliothèques de classes de base principales qui font partie de mono, Xamarin. Android est fourni avec des liaisons pour différentes API Android afin de permettre aux développeurs de créer des applications Android natives avec mono.

Au cœur de Xamarin. Android, il existe un moteur d’interopérabilité qui C# fait le pont entre le monde et le monde Java et permet aux développeurs d' C# accéder aux API Java à partir de ou d’autres langages .net.


## <a name="design-principles"></a>Principes de conception

Voici quelques-uns de nos principes de conception pour la liaison Xamarin. Android

-  Conformez-vous aux [règles de conception de .NET Framework](https://docs.microsoft.com/dotnet/standard/design-guidelines/).

-  Permet aux développeurs de sous-classer des classes Java.

-  La sous-classe doit C# fonctionner avec les constructions standard.

-  Dérive d’une classe existante.

-  Appelez le constructeur de base pour chaîner.

-  Les méthodes de substitution doivent être effectuées C#avec le système de remplacement de.

-  Facilitez les tâches Java courantes et les tâches Java difficiles.

-  Exposez les C# propriétés JavaBean en tant que propriétés.

-  Exposer une API fortement typée:

    - Augmentez la sécurité du type.

    - Réduire les erreurs d’exécution.

    - Obtenir l’IDE IntelliSense sur les types de retour.

    - Autorise la documentation contextuelle de l’IDE.

-  Encourager l’exploration dans l’IDE des API:

    - Utilisez des solutions d’infrastructure pour réduire l’exposition CLASSLIB Java.

    - Exposez C# les délégués (lambdas, méthodes anonymes et System. Delegate) à la place des interfaces à méthode unique, le cas échéant.

    - Fournissez un mécanisme pour appeler des bibliothèques Java arbitraires ( [Android. Runtime. JNIEnv](xref:Android.Runtime.JNIEnv)).


## <a name="assemblies"></a>Assemblys

Xamarin. Android comprend un certain nombre d’assemblys qui constituent le *Profil monomobile*. La page [assemblys](~/cross-platform/internals/available-assemblies.md) contient plus d’informations.

Les liaisons à la plateforme Android sont contenues dans l' `Mono.Android.dll` assembly. Cet assembly contient l’ensemble de la liaison pour la consommation des API Android et la communication avec la machine virtuelle du runtime Android.


## <a name="binding-design"></a>Conception de liaison


### <a name="collections"></a>Collections

Les API Android utilisent largement les collections java. util pour fournir des listes, des ensembles et des mappages. Nous exposerons ces éléments à l’aide des interfaces [System. Collections. Generic](xref:System.Collections.Generic) dans notre liaison. Les mappages fondamentaux sont les suivants:

-   [java. util. Set<E> ](https://developer.android.com/reference/java/util/Set.html) est mappé au type de système [ICollection<T>](xref:System.Collections.Generic.ICollection`1), à la classe d’assistance [Android<T>. Runtime. JavaSet](xref:Android.Runtime.JavaSet`1).

-   [java. util. List<E> ](https://developer.android.com/reference/java/util/List.html) est mappé au type de système [IList<T>](xref:System.Collections.Generic.IList`1), à la classe d’assistance [Android<T>. Runtime. JavaList](xref:Android.Runtime.JavaList`1).

-   [java. util. Map < k, V >](https://developer.android.com/reference/java/util/Map.html) correspond au type de système [IDictionary < TKey, TValue >](xref:System.Collections.Generic.IDictionary`2), Class Helper [Android. Runtime. JavaDictionary < K, V >](xref:Android.Runtime.JavaDictionary`2).

-   [java. util. collection<E> ](https://developer.android.com/reference/java/util/Collection.html) est mappé au type de système [ICollection<T>](xref:System.Collections.Generic.ICollection`1), à la classe d’assistance [Android<T>. Runtime. JavaCollection](xref:Android.Runtime.JavaCollection`1).

Nous avons fourni des classes d’assistance pour faciliter le marshaling plus rapide de ces types. Dans la mesure du possible, nous vous recommandons d’utiliser ces collections fournies au lieu de [`List<T>`](xref:System.Collections.Generic.List`1) l' [`Dictionary<TKey, TValue>`](xref:System.Collections.Generic.Dictionary`2)implémentation fournie par l’infrastructure, comme ou. Les implémentations [Android. Runtime](xref:Android.Runtime) utilisent une collection Java Native en interne et ne nécessitent donc pas de copie vers et à partir d’une collection Native lors du passage à un membre de l’API Android.

Vous pouvez passer toute implémentation d’interface à une méthode Android acceptant cette interface, par exemple `List<int>` , passer un au constructeur [ArrayAdapter&lt;&gt;int (Context,&lt;int&gt;, IList int)](xref:Android.Widget.ArrayAdapter`1) . *Toutefois*, pour toutes les implémentations *à l’exception* des implémentations Android. Runtime, vous devez *copier* la liste de la machine virtuelle mono dans la machine virtuelle du runtime Android. Si la liste est modifiée par la suite dans le runtime Android (par exemple, en [appelant&lt;le&gt;ArrayAdapter T. Méthode Add (T)](xref:Android.Widget.ArrayAdapter`1.Add*) ), ces modifications ne seront *pas* visibles dans le code managé. Si un `JavaList<int>` a été utilisé, ces modifications sont visibles.

Rephrase, les implémentations d’interface de collections qui ne font *pas* partie de la **classe d’assistance**répertoriée ci-dessus ne marshalent que [in]:

```csharp
// This fails:
var badSource  = new List<int> { 1, 2, 3 };
var badAdapter = new ArrayAdapter<int>(context, textViewResourceId, badSource);
badAdapter.Add (4);
if (badSource.Count != 4) // true
    throw new InvalidOperationException ("this is thrown");

// this works:
var goodSource  = new JavaList<int> { 1, 2, 3 };
var goodAdapter = new ArrayAdapter<int> (context, textViewResourceId, goodSource);
goodAdapter.Add (4);
if (goodSource.Count != 4) // false
    throw new InvalidOperationException ("should not be reached.");
```


### <a name="properties"></a>Properties

Les méthodes Java sont transformées en propriétés, le cas échéant:

-  La paire `T getFoo()` de méthodes Java `void setFoo(T)` et sont transformées en `Foo` propriété. Exemple : [Activity. astent](xref:Android.App.Activity.Intent).

-  La méthode `getFoo()` Java est transformée en propriété foo en lecture seule. Exemple : [Context. PackageName](xref:Android.Content.Context.PackageName).

-  Les propriétés set-only ne sont pas générées.

-  Les propriétés ne sont *pas* générées si le type de propriété est un tableau.



### <a name="events-and-listeners"></a>Événements et écouteurs

Les API Android sont basées sur Java et ses composants suivent le modèle Java pour raccorder les écouteurs d’événements. Ce modèle a tendance à être fastidieux, car il oblige l’utilisateur à créer une classe anonyme et à déclarer les méthodes à substituer, par exemple, voici comment les choses seraient effectuées dans Android avec Java:

```csharp
final android.widget.Button button = new android.widget.Button(context);

button.setText(this.count + " clicks!");
button.setOnClickListener (new View.OnClickListener() {
    public void onClick (View v) {
        button.setText(++this.count + " clicks!");
    }
});
```

Le code équivalent dans C# utilisation des événements est le suivant:

```csharp
var button = new Android.Widget.Button (context) {
    Text = string.Format ("{0} clicks!", this.count),
};
button.Click += (sender, e) => {
    button.Text = string.Format ("{0} clicks!", ++this.count);
};
```

Notez que les deux mécanismes ci-dessus sont disponibles avec Xamarin. Android. Vous pouvez implémenter une interface d’écouteur et l’attacher avec View. SetOnClickListener, ou vous pouvez attacher un délégué créé via l’un C# des paradigmes habituels à l’événement Click.

Quand la méthode de rappel de l’écouteur a un retour void, nous créons des éléments d’API basés sur un délégué [EventHandler&lt;TEventArgs&gt; ](xref:System.EventHandler`1) . Nous générons un événement comme dans l’exemple ci-dessus pour ces types d’écouteur. Toutefois, si le rappel de l’écouteur retourne une valeur non void et non **booléenne** , les événements et les EventHandlers ne sont pas utilisés. Au lieu de cela, nous générons un délégué spécifique pour la signature du rappel et ajoutons des propriétés au lieu des événements. La raison est de traiter l’ordre des appels de délégué et la gestion des retours. Cette approche reflète les opérations effectuées avec l’API Xamarin. iOS.

C#les événements ou les propriétés sont générés automatiquement uniquement si la méthode d’inscription d’événements Android:

1. A un `set` préfixe, par exemple, [ *Set*OnClickListener](xref:Android.Views.View.SetOnClickListener*).

1. A un `void` type de retour.

1. Accepte un seul paramètre, le type de paramètre est une interface, l’interface n’a qu’une seule méthode et le nom de `Listener` l’interface se termine par, par exemple, [View. OnClick *Listener*](xref:Android.Views.View.IOnClickListener).


En outre, si la méthode d’interface d’écouteur a un type de retour **booléen** au lieu de **void**, la sous-classe *EventArgs* générée contient une propriété *gérée* . La valeur de la  propriété gérée est utilisée comme valeur de retour pour la méthode d' *écouteur* et `true`prend par défaut la valeur.

Par exemple, la méthode Android [View. setOnKeyListener ()](xref:Android.Views.View.SetOnKeyListener*) accepte l’interface [View. OnKeyListener](xref:Android.Views.View.IOnKeyListener) , tandis que la méthode [View. OnKeyListener. onKey (View, int, KeyEvent)](xref:Android.Views.View.IOnKeyListener.OnKey*) a un type de retour booléen. Xamarin. Android génère une [vue correspondante. KeyPress](xref:Android.Views.View.KeyPress) , qui est une [vue EventHandler&lt;. KeyEventArgs.&gt;](xref:Android.Views.View.KeyEventArgs)
La  classe KeyEventArgs à son tour a une propriété [View. KeyEventArgs. Handled](xref:Android.Views.View.KeyEventArgs.Handled) qui est utilisée comme valeur de retour pour la méthode *View. OnKeyListener. onKey ()* .

Nous envisageons d’ajouter des surcharges pour d’autres méthodes et des constructeurs pour exposer la connexion basée sur un délégué. En outre, les écouteurs avec plusieurs rappels nécessitent une inspection supplémentaire pour déterminer si l’implémentation de rappels individuels est raisonnable. nous allons donc les convertir à mesure qu’ils sont identifiés. S’il n’existe aucun événement correspondant, les écouteurs doivent être utilisés C#dans, mais n’hésitez pas à faire en sorte que vous puissiez faire déléguer l’utilisation à notre attention. Nous avons également effectué certaines conversions d’interfaces sans le suffixe «écouteur» lorsqu’il était clair qu’elles tireraient parti d’une alternative de délégué.

Toutes les interfaces d’écouteurs implémentent l’interface[`Android.Runtime.IJavaObject`](xref:Android.Runtime.IJavaObject)
l’interface, en raison des détails d’implémentation de la liaison, les classes d’écouteur doivent implémenter cette interface. Pour ce faire, vous pouvez implémenter l’interface d’écouteur sur une sous-classe de [java. lang. Object](xref:Java.Lang.Object) ou tout autre objet Java encapsulé, tel qu’une activité Android.


### <a name="runnables"></a>Runnables

Java utilise l’interface [java. lang. Runnable](xref:Java.Lang.Runnable) pour fournir un mécanisme de délégation. La classe [java. lang. thread](xref:Java.Lang.Thread) est un consommateur notable de cette interface. Android a également utilisé l’interface dans l’API.
[Activity. runOnUiThread ()](xref:Android.App.Activity.RunOnUiThread*) et [View.post ()](xref:Android.Views.View.Post*) sont des exemples notables.

L' `Runnable` interface contient une seule méthode void, [Run ()](xref:Java.Lang.Runnable.Run). Il se prête donc à la liaison en C# tant que délégué [System. action](xref:System.Action) . Nous avons fourni des surcharges dans la liaison qui acceptent `Action` un paramètre pour tous les membres d’API `Runnable` qui consomment un dans l’API native, par exemple [Activity. RunOnUiThread ()](xref:Android.App.Activity.RunOnUiThread*) et [View.post ()](xref:Android.Views.View.Post*).

Nous avons laissé les surcharges [IRunnable](xref:Java.Lang.IRunnable) en place au lieu de les remplacer, car plusieurs types implémentent l’interface et peuvent donc être passés en tant que Runnables directement.


### <a name="inner-classes"></a>Classes internes

Java a deux types différents de [classes imbriquées](http://download.oracle.com/javase/tutorial/java/javaOO/nested.html): les classes imbriquées statiques et les classes non statiques.

Les classes imbriquées statiques Java sont C# identiques aux types imbriqués.

Les classes imbriquées non statiques, également appelées *classes internes*, sont très différentes. Elles contiennent une référence implicite à une instance de leur type englobant et ne peuvent pas contenir de membres statiques (entre autres différences en dehors de l’étendue de cette vue d’ensemble).

Lorsqu’il s’agit d’une C# liaison et d’une utilisation, les classes imbriquées statiques sont traitées comme des types imbriqués normaux. Les classes internes comportent deux différences importantes:

1. La référence implicite au type conteneur doit être fournie explicitement en tant que paramètre de constructeur.

1. Lorsque vous héritez d’une classe interne, la classe interne *doit* être imbriquée dans un type qui hérite du type conteneur de la classe interne de base, et le type dérivé doit fournir un constructeur du même type que le C# type conteneur.

Par exemple, considérez la classe interne [Android. service. wallpaper. WallpaperService. Engine](xref:Android.Service.Wallpaper.WallpaperService.Engine) . Étant donné qu’il s’agit d’une classe interne, le [constructeur WallpaperService. Engine ()](xref:Android.Service.Wallpaper.WallpaperService.Engine#ctor) utilise une référence à une instance [WallpaperService](xref:Android.Service.Wallpaper.WallpaperService) (comparez et contrastez avec le constructeur Java WallpaperService. Engine (), qui ne prend aucun paramètre).

CubeWallpaper. CubeEngine est un exemple de dérivation d’une classe interne:

```csharp
class CubeWallpaper : WallpaperService {
        public override WallpaperService.Engine OnCreateEngine ()
        {
                return new CubeEngine (this);
        }

        class CubeEngine : WallpaperService.Engine {
                public CubeEngine (CubeWallpaper s)
                        : base (s)
                {
                }
        }
}
```

Notez comment `CubeWallpaper.CubeEngine` est imbriqué dans `CubeWallpaper`, `CubeWallpaper` hérite de la classe conteneur de `WallpaperService.Engine`et `CubeWallpaper.CubeEngine` a un constructeur qui prend le type déclarant, `CubeWallpaper` dans ce cas, le tout comme indiqué ci-dessus.

### <a name="interfaces"></a>Interfaces

Les interfaces Java peuvent contenir trois ensembles de membres, deux d’entre eux provoquant des problèmes C#:

1. Méthodes

1. Types

1. Champs

Les interfaces Java sont traduites en deux types:

1. Interface (facultative) contenant les déclarations de méthode. Cette interface porte le même nom que l’interface Java, *sauf* qu’elle possède également un préfixe « *I* ».

1. Classe statique (facultative) contenant tous les champs déclarés dans l’interface java.

Les types imbriqués sont «déplacés» pour être frères de l’interface englobante au lieu de types imbriqués, avec le nom d’interface englobant comme préfixe.

Par exemple, considérez l’interface [Android. OS. parcelable](xref:Android.OS.Parcelable) .
L' *interface* à regrouper contient des méthodes, des types imbriqués et des constantes. Les méthodes d' *interface qui* peuvent être reportées sont placées dans l’interface [Android. OS. IParcelable](xref:Android.OS.IParcelable) .
Les constantes *d’interface qui* peuvent être reportées sont placées dans le type [Android. OS. ParcelableConsts](xref:Android.OS.ParcelableConsts) . Les types de > [Android. OS. Parcel. ClassLoaderCreator&lt;t >](https://developer.android.com/reference/android/os/Parcelable.ClassLoaderCreator.html) et [Android. OS. parcelable. Creator&lt;t](https://developer.android.com/reference/android/os/Parcelable.Creator.html) ne sont actuellement pas liés en raison des limitations de notre prise en charge des génériques; s’ils étaient pris en charge, ils est présent en tant qu’interfaces *Android. OS. IParcelableClassLoaderCreator* et *Android. OS. IParcelableCreator* . Par exemple, l’interface [Android. OS. IBinder. DeathRecipient](https://developer.android.com/reference/android/os/IBinder.DeathRecipient.html) imbriquée est liée en tant qu’interface [Android. OS. IBinderDeathRecipient](xref:Android.OS.IBinderDeathRecipient) .

> [!NOTE]
> À partir de Xamarin. Android 1,9, les constantes de  l’interface Java sont dupliquées afin de simplifier le portage du code Java. Cela permet d’améliorer le portage du code Java qui s’appuie sur les constantes de l’interface du [fournisseur Android](https://developer.android.com/reference/android/provider/package-summary.html) .

Outre les types ci-dessus, il existe quatre autres modifications:

1. Un type portant le même nom que l’interface Java est généré pour contenir des constantes.

1. Les types contenant des constantes d’interface contiennent également toutes les constantes provenant d’interfaces Java implémentées.

1. Toutes les classes qui implémentent une interface Java contenant des constantes obtiennent un nouveau type InterfaceConsts imbriqué qui contient des constantes à partir de toutes les interfaces implémentées.

1. Le type *constructeurs* est désormais obsolète.


Pour l’interface *Android. OS. parcelable* , cela signifie qu’il y aura maintenant un type [*Android. OS. parcelable*](xref:Android.OS.Parcelable) pour contenir les constantes. Par exemple, la constante [parcelable. CONTENTS_FILE_DESCRIPTOR](https://developer.android.com/reference/android/os/Parcelable.html#CONTENTS_FILE_DESCRIPTOR) sera liée en tant que constante reparcelal [ *. ContentsFileDescriptor*](xref:Android.OS.Parcelable.ContentsFileDescriptor) , et non en tant que constante *ParcelableConsts. ContentsFileDescriptor* .

Pour les interfaces qui contiennent des constantes qui implémentent d’autres interfaces contenant encore plus de constantes, l’Union de toutes les constantes est maintenant générée. Par exemple, l’interface [Android. Provider. Mediastore. Video. VideoColumns](https://developer.android.com/reference/android/provider/MediaStore.Video.VideoColumns.html) implémente l’interface [Android. Provider. Mediastore. MediaColumns](xref:Android.Provider.MediaStore.MediaColumns) . Toutefois, avant le 1,9, le type [Android. Provider. Mediastore. Video. VideoColumnsConsts](xref:Android.Provider.MediaStore.Video.VideoColumnsConsts) n’a aucun moyen d’accéder aux constantes déclarées sur [Android. Provider. Mediastore. MediaColumnsConsts](xref:Android.Provider.MediaStore.MediaColumnsConsts).
En conséquence, l’expression Java *Mediastore. Video. VideoColumns. title* doit être liée à l' C# expression *Mediastore. Video. MediaColumnsConsts. title* , ce qui est difficile à découvrir sans lire beaucoup de documentation Java. Dans 1,9, l’expression C# équivalente sera [Mediastore. Video. VideoColumns. title](xref:Android.Provider.MediaStore.Video.VideoColumns.Title).

En outre, envisagez le type [Android. OS. Bundle](xref:Android.OS.Bundle) , qui implémente *l’interface Java* pouvant être regroupée. Étant donné qu’elle implémente l’interface, toutes les constantes de cette interface sont accessibles par le biais du type de Bundle, par exemple *bundle. CONTENTS_FILE_DESCRIPTOR* est une expression Java parfaitement valide.
Auparavant, pour porter cette expression vers C# , vous devez examiner toutes les interfaces qui sont implémentées pour voir à partir de quel type le *CONTENTS_FILE_DESCRIPTOR* provient. À compter de Xamarin. Android 1,9, les classes qui implémentent des interfaces Java qui contiennent des constantes auront un type *InterfaceConsts* imbriqué, qui contient toutes les constantes d’interface héritées. Cela permettra la traduction de *bundle. CONTENTS_FILE_DESCRIPTOR* en [*bundle. InterfaceConsts. ContentsFileDescriptor*](xref:Android.OS.Bundle.InterfaceConsts.ContentsFileDescriptor).

Enfin, les types avec un suffixe *constructeurs* , par exemple *Android. OS. ParcelableConsts* , sont désormais obsolètes, à l’exception des types imbriqués nouvellement introduits InterfaceConsts. Ils seront supprimés dans Xamarin. Android 3,0.


## <a name="resources"></a>Ressources

Les images, les descriptions de disposition, les blobs binaires et les dictionnaires de chaînes peuvent être inclus dans votre application en tant que [fichiers de ressources](https://developer.android.com/guide/topics/resources/providing-resources.html).
Différentes API Android sont conçues pour [fonctionner sur les ID de ressource](https://developer.android.com/guide/topics/resources/accessing-resources.html) au lieu de traiter directement des images, des chaînes ou des objets BLOB binaires.

Par exemple, un exemple d’application Android contenant une disposition d’interface utilisateur `main.axml`(), une chaîne de table d' `strings.xml`internationalisation () et `drawable-*/icon.png`certaines icônes () conserveraient ses ressources dans le répertoire «Resources» de l’application:

    Resources/
        drawable-hdpi/
            icon.png

        drawable-ldpi/
            icon.png

        drawable-mdpi/
            icon.png

        layout/
            main.axml

        values/
            strings.xml

Les API Android natives ne fonctionnent pas directement avec les noms de fichiers, mais fonctionnent plutôt sur les ID de ressource. Quand vous compilez une application Android qui utilise des ressources, le système de génération empaquette les ressources pour la `Resource` distribution et génère une classe appelée qui contient les jetons pour chaque ressource incluse. Par exemple, pour la disposition des ressources ci-dessus, c’est ce que la classe R expose:

```csharp
public class Resource {
    public class Drawable {
        public const int icon = 0x123;
    }

    public class Layout {
        public const int main = 0x456;
    }

    public class String {
        public const int first_string = 0xabc;
        public const int second_string = 0xbcd;
    }
}
```

Vous pouvez ensuite utiliser `Resource.Drawable.icon` pour référencer `drawable/icon.png` le `layout/main.xml` fichier, `Resource.Layout.main` ou pour référencer le fichier `Resource.String.first_string` , ou pour référencer la première chaîne dans `values/strings.xml`le fichier de dictionnaire.


## <a name="constants-and-enumerations"></a>Constantes et énumérations

Les API Android natives ont de nombreuses méthodes qui acceptent ou retournent un entier qui doit être mappé à un champ constant pour déterminer ce que signifie int. Pour utiliser ces méthodes, l’utilisateur doit consulter la documentation pour voir quelles constantes sont des valeurs appropriées, ce qui est moins qu’idéal.

Par exemple, considérez [Activity. requestWindowFeature (int FeatureId)](https://developer.android.com/reference/android/app/Activity.html#requestWindowFeature(int)).

Dans ces cas, nous nous efforçons de regrouper les constantes associées dans une énumération .NET et de remapper la méthode pour utiliser l’énumération à la place.
En procédant ainsi, nous pouvons proposer une sélection IntelliSense des valeurs potentielles.

L’exemple ci-dessus devient: [Activity. RequestWindowFeature (WindowFeatures FeatureId)](xref:Android.App.Activity.RequestWindowFeature*).

Notez qu’il s’agit d’un processus très manuel pour déterminer les constantes qui appartiennent ensemble, et les API qui consomment ces constantes. Veuillez signaler les bogues pour toutes les constantes utilisées dans l’API qui seraient mieux exprimées sous la forme d’une énumération.
