---
title: Principes de conception de l’API de Xamarin.Android
ms.prod: xamarin
ms.assetid: 3E52D815-D95D-5510-0D8F-77DAC7E62EDE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: c10935f4623fd4455ec5cf8a80c6473c0f69d9b9
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674689"
---
# <a name="xamarinandroid-api-design-principles"></a>Principes de conception de l’API de Xamarin.Android


## <a name="overview"></a>Vue d'ensemble

Outre la base des bibliothèques de classes de Base qui font partie de Mono, Xamarin.Android est livré avec des liaisons pour diverses API Android permettre aux développeurs de créer des applications Android natives avec Mono.

Au cœur de Xamarin.Android est un moteur d’interopérabilité ce monde ponts c# avec le monde de Java et fournit aux développeurs un accès aux API Java à partir de c# ou d’autres langages .NET.


## <a name="design-principles"></a>Principes de conception

Voici quelques-unes de nos principes de conception pour la liaison de Xamarin.Android

-  Est conforme à la [instructions de conception .NET Framework](https://docs.microsoft.com/dotnet/standard/design-guidelines/).

-  Permettre aux développeurs de classes Java de sous-classe.

-  Sous-classe doit fonctionner avec les constructions c# standards.

-  Dériver à partir d’une classe existante.

-  Appelez le constructeur de base pour la chaîne.

-  Méthodes de substitution doit être effectuée avec C#de remplacer le système.

-  Simplifiez les tâches courantes Java et les tâches Java durs rendent possibles.

-  Exposer les propriétés de JavaBean en tant que propriétés c#.

-  Exposer une API fortement typée :

    - Augmenter la sécurité de type.

    - Réduire les erreurs d’exécution.

    - Obtenir IDE intellisense sur les types de retour.

    - Permet la documentation de fenêtre contextuelle IDE.

-  Encourager l’exploration dans l’IDE de l’API :

    - Utiliser des Alternatives de Framework à l’exposition de réduire les Classlib Java.

    - Exposer les délégués c# (les expressions lambda, méthodes anonymes et System.Delegate) au lieu d’interfaces de méthode unique lorsque approprié et applicable.

    - Fournir un mécanisme pour appeler les bibliothèques Java arbitraires ( [Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)).


## <a name="assemblies"></a>Assemblys

Xamarin.Android inclut un nombre d’assemblys qui constituent le *MonoMobile profil*. Le [assemblys](~/cross-platform/internals/available-assemblies.md) page comporte des informations supplémentaires.

Les liaisons à la plateforme Android sont contenus dans le `Mono.Android.dll` assembly. Cet assembly contient la liaison entière pour les API Android consommation et communiquer avec le runtime Android machine virtuelle.


## <a name="binding-design"></a>Conception de liaison


### <a name="collections"></a>Collections

L’API Android utilisent les collections java.util largement pour fournir des listes, des jeux et des cartes. Nous exposons ces éléments à l’aide de la [System.Collections.Generic](xref:System.Collections.Generic) interfaces dans notre liaison. Les mappages fondamentaux sont :

-   [java.util.Set<E> ](https://developer.android.com/reference/java/util/Set.html) est mappé au type de système [ICollection<T>](xref:System.Collections.Generic.ICollection`1), classe d’assistance [Android.Runtime.JavaSet<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaSet%601/).

-   [java.util.List<E> ](https://developer.android.com/reference/java/util/List.html) est mappé au type de système [IList<T>](xref:System.Collections.Generic.IList`1), classe d’assistance [Android.Runtime.JavaList<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaList%601/).

-   [java.util.Map < K, V >](https://developer.android.com/reference/java/util/Map.html) est mappé au type de système [IDictionary < TKey, TValue >](xref:System.Collections.Generic.IDictionary`2), classe d’assistance [Android.Runtime.JavaDictionary < K, V >](https://developer.xamarin.com/api/type/Android.Runtime.JavaDictionary%602/).

-   [java.util.Collection<E> ](https://developer.android.com/reference/java/util/Collection.html) est mappé au type de système [ICollection<T>](xref:System.Collections.Generic.ICollection`1), classe d’assistance [Android.Runtime.JavaCollection<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaCollection%601/).

Nous avons fourni des classes d’assistance pour faciliter plus rapidement copyless marshaling de ces types. Lorsque cela est possible, nous vous recommandons d’utiliser ces fourni des collections au lieu de l’implémentation de l’infrastructure fournie, comme [ `List<T>` ](xref:System.Collections.Generic.List`1) ou [ `Dictionary<TKey, TValue>` ](xref:System.Collections.Generic.Dictionary`2). Le [Android.Runtime](https://developer.xamarin.com/api/namespace/Android.Runtime/) implémentations utilisent une collection Java native en interne et ne requièrent donc pas copier vers et depuis une collection native lors du passage à un membre de l’API Android.

Vous pouvez passer n’importe quelle implémentation d’interface à une méthode Android d’acceptation de cette interface, par exemple, passer un `List<int>` à la [ArrayAdapter&lt;int&gt;(contexte, int, IList&lt;int&gt;)](https://developer.xamarin.com/api/constructor/Android.Widget.ArrayAdapter%3CT%3E.ArrayAdapter%3CT%3E/p/Android.Content.Context/System.Int32/System.Collections.Generic.IList%7BT%7D/)constructeur. *Toutefois*, pour toutes les implémentations *sauf* pour les implémentations Android.Runtime, cela implique *copie* la liste à partir de la machine virtuelle Mono dans la machine virtuelle du runtime Android. Si la liste est ultérieure modifié dans le runtime Android (par exemple, en appelant le [ArrayAdapter&lt;T&gt;. Add(T)](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter%3CT%3E.Add/p/T/) méthode), ces modifications *ne sera pas* être visible dans le code managé. Si un `JavaList<int>` ont été utilisés, ces modifications sont visibles.

Reformulée, qui sont des implémentations d’interface collections *pas* ci-dessus répertoriés **classe d’assistance**es marshaler uniquement [In] :

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

Méthodes Java sont transformés en propriétés, le cas échéant :

-  La paire de méthodes Java `T getFoo()` et `void setFoo(T)` sont transformés en le `Foo` propriété. Exemple : [Activity.Intent](https://developer.xamarin.com/api/property/Android.App.Activity.Intent/).

-  La méthode Java `getFoo()` est transformé en la propriété Foo en lecture seule. Exemple : [Context.PackageName](https://developer.xamarin.com/api/property/Android.Content.Context.PackageName/).

-  Propriétés à définir uniquement ne sont pas générées.

-  Les propriétés sont *pas* générée si le type de propriété serait un tableau.



### <a name="events-and-listeners"></a>Événements et des écouteurs

Les API Android sont basés sur Java et ses composants suivent le modèle de Java pour raccorder des écouteurs d’événements. Ce modèle a tendance à être fastidieuse, car elle nécessite l’utilisateur de créer une classe anonyme et déclarer les méthodes à substituer, par exemple, voici comment choses reviennent dans Android avec Java :

```csharp
final android.widget.Button button = new android.widget.Button(context);

button.setText(this.count + " clicks!");
button.setOnClickListener (new View.OnClickListener() {
    public void onClick (View v) {
        button.setText(++this.count + " clicks!");
    }
});
```

Le code équivalent en c# à l’aide d’événements serait :

```csharp
var button = new Android.Widget.Button (context) {
    Text = string.Format ("{0} clicks!", this.count),
};
button.Click += (sender, e) => {
    button.Text = string.Format ("{0} clicks!", ++this.count);
};
```

Notez que deux des mécanismes ci-dessus sont disponibles avec Xamarin.Android. Vous pouvez implémenter une interface d’écouteur et attachez-le avec View.SetOnClickListener, ou vous pouvez attacher un délégué créé par le biais des paradigmes c# habituels à l’événement Click.

Lorsque la méthode de rappel d’écouteur a un retour void, nous créons des éléments d’API basées sur un [EventHandler&lt;TEventArgs&gt; ](xref:System.EventHandler`1) déléguer. Nous générons un événement, comme l’exemple ci-dessus pour ces types d’écouteur. Toutefois, si le rappel de l’écouteur retourne un non void et non- **booléenne** valeur, les événements et EventHandlers ne sont pas utilisés. Au lieu de cela, nous générer un délégué spécifique pour la signature du rappel et ajouter des propriétés au lieu des événements. La raison est gérer ordre d’appel de délégué et retourner la gestion. Cette approche reflète ce qui se fait avec l’API Xamarin.iOS.

Propriétés ou événements c# uniquement générées automatiquement si la méthode d’inscription aux événements Android :

1. A un `set` de préfixe, par exemple, [ *définir*OnClickListener](https://developer.xamarin.com/api/member/Android.Views.View.SetOnClickListener/).

1. A un `void` type de retour.

1. Accepte qu’un seul paramètre, le type de paramètre est une interface, l’interface possède une seule méthode, et se termine par le nom de l’interface `Listener` , par exemple [View.OnClick *écouteur*](https://developer.xamarin.com/api/type/Android.Views.View+IOnClickListener/).


En outre, si la méthode d’interface de l’écouteur a un type de retour **booléenne** au lieu de **void**, puis généré *EventArgs* sous-classe contiendra un *Handled* propriété. La valeur de la *Handled* propriété est utilisée comme valeur de retour pour la *écouteur* méthode et qu’elle utilise par défaut `true`.

Par exemple, l’Android [View.setOnKeyListener()](https://developer.xamarin.com/api/member/Android.Views.View.SetOnKeyListener/p/Android.Views.View+IOnKeyListener/) méthode accepte le [View.OnKeyListener](https://developer.xamarin.com/api/type/Android.Views.View+IOnKeyListener) interface et le [View.OnKeyListener.onKey (vue, int, KeyEvent)](https://developer.xamarin.com/api/member/Android.Views.View+IOnKeyListener.OnKey/p/Android.Views.View/Android.Views.Keycode/Android.Views.KeyEvent/) méthode a un type de retour boolean. Xamarin.Android génère un correspondant [View.KeyPress](https://developer.xamarin.com/api/event/Android.Views.View.KeyPress/) événement, qui est un [EventHandler&lt;View.KeyEventArgs&gt;](https://developer.xamarin.com/api/type/Android.Views.View+KeyEventArgs/).
Le *KeyEventArgs* classe a à son tour un [View.KeyEventArgs.Handled](https://developer.xamarin.com/api/property/Android.Views.View+KeyEventArgs.Handled/) propriété, qui est utilisée comme valeur de retour pour la *View.OnKeyListener.onKey()* (méthode).

Nous avons l’intention d’ajouter des surcharges pour les autres méthodes et les constructeurs pour exposer la connexion basée sur le délégué. En outre, écouteurs avec plusieurs rappels nécessitent certains inspection supplémentaire pour déterminer si l’implémentation des rappels individuels est raisonnable, donc nous convertissons ces comme ils sont identifiés. S’il n’existe aucun événement correspondant, écouteurs doivent être utilisés en c#, mais placez celles que vous pensez que peut avoir l’utilisation de délégué à notre attention. Nous avons également effectué certaines conversions des interfaces sans le suffixe « Écouteur » lorsqu’il était évident qu’ils tireront parti d’un autre délégué.

Toutes les interfaces d’écouteurs implémentent le [`Android.Runtime.IJavaObject`](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/)
interface, en raison des détails d’implémentation de la liaison, les classes de l’écouteur doivent implémenter cette interface. Cela est possible en implémentant l’interface de récepteur sur une sous-classe de [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) ou tout autre objet Java, telle qu’une activité Android.


### <a name="runnables"></a>Runnables

Java utilise le [java.lang.Runnable](https://developer.xamarin.com/api/type/Java.Lang.Runnable/) interface pour fournir un mécanisme de délégation. Le [java.lang.Thread](https://developer.xamarin.com/api/type/Java.Lang.Thread/) classe est un consommateur notable de cette interface. Android a utilisé l’interface dans l’API.
[Activity.runOnUiThread()](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/p/Java.Lang.IRunnable/) et [View.post()](https://developer.xamarin.com/api/member/Android.Views.View.Post/p/Java.Lang.IRunnable) sont des exemples notables.

Le `Runnable` interface contient une méthode void unique, [run()](https://developer.xamarin.com/api/member/Java.Lang.Runnable.Run%28%29/). Par conséquent prêter à la liaison dans c# comme un [System.Action](xref:System.Action) déléguer. Nous avons fourni dans la liaison des surcharges qui acceptent un `Action` paramètre pour tous les membres de l’API qui consomment un `Runnable` dans l’API native, par exemple, [Activity.RunOnUiThread()](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/(System.Action)) et [View.Post()](https://developer.xamarin.com/api/member/Android.Views.View.Post/(System.Action)).

Nous avons laissé le [IRunnable](https://developer.xamarin.com/api/type/Java.Lang.IRunnable/) surcharges en place au lieu de les remplacer dans la mesure où plusieurs types implémentent l’interface et peuvent donc être passés en tant que runnables directement.


### <a name="inner-classes"></a>Classes internes

Java a deux types différents de [classes imbriquées](http://download.oracle.com/javase/tutorial/java/javaOO/nested.html): statique imbriqués des classes et les classes non statiques.

Les classes imbriquées statiques de Java sont identiques aux types imbriqués en c#.

Non statiques classes imbriquées, également appelés *classes internes*, sont très différents. Ils contiennent une référence implicite à une instance de leur type englobant et ne peut pas contenir de membres statiques (parmi d’autres différences sort du cadre de cette vue d’ensemble).

Lorsqu’il s’agit de liaison et l’utilisation de c#, classes imbriquées statiques sont traités comme des types imbriqués normales. Classes internes, ont pendant ce temps, les deux différences importantes :

1. La référence implicite au type conteneur doit être fournie explicitement comme paramètre de constructeur.

1. Lorsque vous héritez d’une classe interne, la classe interne *doit* être imbriqué dans un type qui hérite du type contenant la classe de base interne, et le type dérivé doit fournir un constructeur de même type que le c# contenant le type.


Par exemple, considérez le [Android.Service.Wallpaper.WallpaperService.Engine](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService+Engine/) classe interne. Puisqu’il s’agit d’une classe interne, le [WallpaperService.Engine() constructeur](https://developer.xamarin.com/api/constructor/Android.Service.Wallpaper.WallpaperService+Engine.Engine/p/Android.Service.Wallpaper.WallpaperService/) prend une référence à un [WallpaperService](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService/) instance (de comparer le Java [WallpaperService.Engine () constructeur),](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService+Engine/) qui n’accepte aucun paramètre).

Une dérivation de l’exemple d’une classe interne est CubeWallpaper.CubeEngine :

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

Notez comment `CubeWallpaper.CubeEngine` est imbriqué dans `CubeWallpaper`, `CubeWallpaper` hérite de la classe de conteneur `WallpaperService.Engine`, et `CubeWallpaper.CubeEngine` a un constructeur qui prend le type déclarant-- `CubeWallpaper` dans ce cas--all comme spécifié ci-dessus.


### <a name="interfaces"></a>Interfaces

Interfaces Java peuvent contenir trois jeux de membres, dont deux entraîner des problèmes à partir de c# :

1. Méthodes

1. Types

1. Champs


Interfaces Java sont traduites en deux types :

1. Une interface (facultative) contenant les déclarations de méthode. Cette interface a le même nom que l’interface Java, *sauf* qu’il comporte également un « *je* ' préfixe.

1. Une classe statique (facultative) qui contient tous les champs déclarés dans l’interface Java.

Les types imbriqués sont « déplacées » pour être frères de l’interface englobante au lieu des types imbriqués, avec le nom d’interface englobante comme préfixe.

Par exemple, considérez le [android.os.Parcelable](https://developer.xamarin.com/api/type/Android.OS.Parcelable/) interface.
Le *Parcelable* interface contient les méthodes, les types imbriqués et les constantes. Le *Parcelable* méthodes d’interface sont placés dans le [Android.OS.IParcelable](https://developer.xamarin.com/api/type/Android.OS.IParcelable/) interface.
Le *Parcelable* constantes de l’interface sont placés dans le [Android.OS.ParcelableConsts](https://developer.xamarin.com/api/type/Android.OS.ParcelableConsts/) type. Imbriqué [android.os.Parcelable.ClassLoaderCreator <t> </t> ](https://developer.android.com/reference/android/os/Parcelable.ClassLoaderCreator.html) et [android.os.Parcelable.Creator <t> </t> ](https://developer.android.com/reference/android/os/Parcelable.Creator.html) types ne sont actuellement pas lié en raison des limitations dans notre prise en charge les génériques ; Si elles ont été prises en charge, ils doivent être présents en tant que le *Android.OS.IParcelableClassLoaderCreator* et *Android.OS.IParcelableCreator* interfaces. Par exemple, l’imbriquée [android.os.IBinder.DeathRecipient](https://developer.android.com/reference/android/os/IBinder.DeathRecipient.html) interface est liée en tant que le [Android.OS.IBinderDeathRecipient](https://developer.xamarin.com/api/type/Android.OS.IBinderDeathRecipient/) interface.


> [!NOTE]
> À compter de Xamarin.Android 1.9, constantes d’interface de Java sont <em>dupliqué</em> de code dans le but de simplifier le portage de Java. Cela contribue à améliorer le portage de code Java qui s’appuie sur [fournisseur android](https://developer.android.com/reference/android/provider/package-summary.html) constantes de l’interface.

Outre les types ci-dessus, il existe quatre autres modifications :

1. Un type portant le même nom que l’interface Java est généré pour contenir des constantes.

1. Types contenant également des constantes de l’interface contiennent toutes les constantes qui proviennent d’interfaces implémentées de Java.

1. Toutes les classes qui implémentent une interface Java contenant des constantes Obtient un nouveau type InterfaceConsts imbriqué qui contient des constantes à partir de toutes les interfaces implémentées.

1. Le *Consts* type est désormais obsolète.


Pour le *android.os.Parcelable* interface, cela signifie qu’il y aura désormais un [ *Android.OS.Parcelable* ](https://developer.xamarin.com/api/type/Android.OS.Parcelable/) type pour contenir les constantes. Par exemple, le [Parcelable.CONTENTS_FILE_DESCRIPTOR](https://developer.android.com/reference/android/os/Parcelable.html#CONTENTS_FILE_DESCRIPTOR) constante va être liée en tant que le [ *Parcelable.ContentsFileDescriptor* ](https://developer.xamarin.com/api/field/Android.OS.Parcelable.ContentsFileDescriptor/) constante, au lieu de comme le  *ParcelableConsts.ContentsFileDescriptor* constante.

Pour les interfaces contenant des constantes qui implémentent des autres interfaces contenant encore plus de constantes, l’union de toutes les constantes est désormais générée. Par exemple, le [android.provider.MediaStore.Video.VideoColumns](https://developer.android.com/reference/android/provider/MediaStore.Video.VideoColumns.html) interface implémente la [android.provider.MediaStore.MediaColumns](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+MediaColumns/) interface. Toutefois, avant 1.9, les [Android.Provider.MediaStore.Video.VideoColumnsConsts](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+Video+VideoColumnsConsts/) type n’a aucun moyen d’accéder à l’une des constantes déclarées sur [Android.Provider.MediaStore.MediaColumnsConsts](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+MediaColumnsConsts/).
Par conséquent, l’expression Java *MediaStore.Video.VideoColumns.TITLE* doit être lié à l’expression c# *MediaStore.Video.MediaColumnsConsts.Title* qui est difficile à découvrir sans effectuer de lecture grand nombre de documentation Java. Dans 1.9, l’équivalent C# expression sera [ *MediaStore.Video.VideoColumns.Title*](https://developer.xamarin.com/api/field/Android.Provider.MediaStore+Video+VideoColumns.Title/).

En outre, envisagez la [android.os.Bundle](https://developer.xamarin.com/api/type/Android.OS.Bundle/) type, qui implémente le Java *Parcelable* interface. Dans la mesure où elle implémente l’interface, toutes les constantes sur cette interface sont accessibles « via » le type de regroupement, par exemple, *Bundle.CONTENTS_FILE_DESCRIPTOR* est une expression Java parfaitement valide.
Précédemment, porter cette expression à C# vous devez examiner toutes les interfaces qui sont implémentées pour voir à partir de quel type le *CONTENTS_FILE_DESCRIPTOR* provient. À compter de Xamarin.Android 1.9, classes implémentant les interfaces Java qui contiennent des constantes aura imbriquée *InterfaceConsts* type, qui contiendra toutes les constantes de l’interface héritée. Cela permettra de traduire *Bundle.CONTENTS_FILE_DESCRIPTOR* à [ *Bundle.InterfaceConsts.ContentsFileDescriptor*](https://developer.xamarin.com/api/field/Android.OS.Bundle+InterfaceConsts.ContentsFileDescriptor/).

Enfin, les types avec une *Consts* tels que *Android.OS.ParcelableConsts* sont maintenant obsolètes, autre que le InterfaceConsts récemment introduits les types imbriqués. Ils sont supprimés dans Xamarin.Android 3.0.


## <a name="resources"></a>Ressources

Images, les descriptions de disposition, les BLOB et les dictionnaires de chaîne peuvent être inclus dans votre application en tant que [fichiers de ressources](https://developer.android.com/guide/topics/resources/providing-resources.html).
Diverses API Android sont conçus pour [opèrent sur les ID de ressource](https://developer.android.com/guide/topics/resources/accessing-resources.html) au lieu de traiter les images, binaire ou chaînes d’objets BLOB directement.

Par exemple, une application exemple Android qui contient une disposition de l’interface utilisateur ( `main.axml`), une chaîne de la table internationalisation ( `strings.xml`) et certaines icônes ( `drawable-*/icon.png`) conserve ses ressources dans le répertoire « Ressources » de l’application :

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

Natif Android API n’opèrent pas directement avec les noms de fichiers, mais plutôt fonctionner sur les ID de ressource. Lorsque vous compilez une application Android qui utilise des ressources, le système de génération sera empaqueter les ressources pour la distribution et générer une classe appelée `Resource` qui contient les jetons pour chacune des ressources incluses. Par exemple, pour la mise en page de ressources ci-dessus, il s’agit la classe R exposerait :

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

Vous devez utiliser `Resource.Drawable.icon` pour référencer le `drawable/icon.png` fichier, ou `Resource.Layout.main` pour référencer le `layout/main.xml` fichier, ou `Resource.String.first_string` pour référencer la première chaîne dans le fichier de dictionnaire `values/strings.xml`.


## <a name="constants-and-enumerations"></a>Constantes et énumérations

Natif API Android ont de nombreuses méthodes qui utilisent ou renvoient une valeur int qui doit être mappé à un champ constant pour déterminer ce que signifie l’int. Pour utiliser ces méthodes, l’utilisateur est nécessaire de consulter la documentation pour voir les constantes sont des valeurs appropriées, ce qui est loin d’être idéal.

Par exemple, considérez [Activity.requestWindowFeature (int featureID)](https://developer.android.com/reference/android/app/Activity.html#requestWindowFeature(int)).

Dans ce cas, nous nous efforçons de regrouper des constantes connexes dans une énumération .NET et remapper la méthode prenne l’énumération à la place.
Ce faisant, nous sommes en mesure d’offrir la sélection IntelliSense des valeurs potentielles.

L’exemple ci-dessus devient : [Activity.RequestWindowFeature (WindowFeatures featureId)](https://developer.xamarin.com/api/member/Android.App.Activity.RequestWindowFeature/p/Android.Views.WindowFeatures/).

Notez qu’il s’agit d’un processus très manuel pour déterminer les constantes vont ensemble, et quelles API utilisent ces constantes. Envoyez des bogues pour les constantes utilisées dans l’API qui serait mieux exprimée en tant qu’énumération.
