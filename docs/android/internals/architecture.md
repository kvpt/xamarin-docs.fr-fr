---
title: Architecture
ms.prod: xamarin
ms.assetid: 7DC22A08-808A-DC0C-B331-2794DD1F9229
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: fe0903eca5c907fc104728ca0ad7c676a45a5180
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027912"
---
# <a name="architecture"></a>Architecture

Les applications Xamarin. Android s’exécutent dans l’environnement d’exécution mono.
Cet environnement d’exécution s’exécute côte à côte avec la machine virtuelle Android Runtime (ART). Les deux environnements d’exécution s’exécutent au-dessus du noyau Linux et exposent différentes API au code utilisateur qui permet aux développeurs d’accéder au système sous-jacent. Le runtime mono est écrit en langage C.

Vous pouvez utiliser le [système](xref:System), [System.IO](xref:System.IO), [System.net](xref:System.Net) et les autres bibliothèques de classes .net pour accéder aux installations du système d’exploitation Linux sous-jacent.

Sur Android, la plupart des fonctionnalités système telles que l’audio, les graphiques, OpenGL et la téléphonie ne sont pas disponibles directement pour les applications natives, elles sont uniquement exposées via les API Java Android Runtime résidant dans l’un des espaces de noms [java](xref:Java.Lang). * ou [Android ](xref:Android). * espaces de noms. L’architecture ressemble à peu près à ce qui suit :

[![diagramme de mono et ART au-dessus du noyau et des liaisons .NET/Java + antérieures](architecture-images/architecture1.png)](architecture-images/architecture1.png#lightbox)

Les développeurs Xamarin. Android accèdent aux différentes fonctionnalités du système d’exploitation en appelant les API .NET qu’ils savent (pour un accès de bas niveau) ou en utilisant les classes exposées dans les espaces de noms Android qui fournissent un pont aux API Java exposées par le runtime Android.

Pour plus d’informations sur la façon dont les classes Android communiquent avec les classes d’exécution Android, consultez le document de [conception d’API](~/android/internals/api-design.md) .

## <a name="application-packages"></a>Packages d’application

Les packages d’application Android sont des conteneurs ZIP avec une extension de fichier *. apk* . Les packages d’application Xamarin. Android ont la même structure et la même disposition que les packages Android normaux, avec les ajouts suivants :

- Les assemblys d’application (contenant le langage intermédiaire) sont *stockés* non compressés dans le dossier d' *assemblys* . Lors du démarrage du processus dans les versions release, le *. apk* est *mmap ()* ed dans le processus et les assemblys sont chargés à partir de la mémoire. Cela permet un démarrage plus rapide des applications, car les assemblys n’ont pas besoin d’être extraits avant l’exécution.  
- *Remarque :* Les informations d’emplacement de l’assembly, telles que [assembly. Location](xref:System.Reflection.Assembly.Location) et [assembly. CodeBase](xref:System.Reflection.Assembly.CodeBase) , *ne peuvent pas être reposées sur* dans les versions release. Ils n’existent pas en tant qu’entrées distinctes du système de fichiers et n’ont pas d’emplacement utilisable.

- Les bibliothèques natives contenant le runtime mono sont présentes dans le *. apk* . Une application Xamarin. Android doit contenir des bibliothèques natives pour les architectures Android souhaitées/ciblées, par exemple, *ARMEABI* , *ARMEABI-V7A* , *x86* . Les applications Xamarin. Android ne peuvent pas s’exécuter sur une plateforme, sauf si elle contient les bibliothèques d’exécution appropriées.

Les applications Xamarin. Android contiennent également des *wrappers pouvant être appelés* par Android pour permettre à Android d’appeler du code managé.

## <a name="android-callable-wrappers"></a>Wrappers pouvant être appelés par Android

- Les **wrappers Android** pouvant être appelés sont un pont [JNI](https://en.wikipedia.org/wiki/Java_Native_Interface) utilisé chaque fois que le runtime Android doit appeler du code managé. Les wrappers pouvant être appelés par Android sont la manière dont les méthodes virtuelles peuvent être substituées et les interfaces Java peuvent être implémentées. Pour plus d’informations, consultez le document [vue d’ensemble de l’intégration Java](~/android/platform/java-integration/index.md) .

<a name="Managed_Callable_Wrappers" />

## <a name="managed-callable-wrappers"></a>Wrappers pouvant être appelés par Managed

Les wrappers pouvant être appelés sont un pont JNI qui est utilisé chaque fois que du code managé doit appeler du Code Android et prendre en charge la substitution de méthodes virtuelles et l’implémentation d’interfaces Java. L’ensemble des espaces de noms [Android](xref:Android). * et associés sont des wrappers pouvant être appelés managés générés via une [liaison. jar](~/android/platform/binding-java-library/index.md).
Les wrappers pouvant être appelés sont chargés de la conversion entre les types managés et Android et d’appeler les méthodes de plateforme Android sous-jacentes via JNI.

Chaque Wrapper pouvant être appelé managé contient une référence globale Java, accessible via la propriété [Android. Runtime. IJavaObject. handle](xref:Android.Runtime.IJavaObject.Handle) . Les références globales sont utilisées pour fournir le mappage entre les instances Java et les instances managées. Les références globales sont une ressource limitée : les émulateurs autorisent uniquement l’existence de 2000 références globales à la fois, tandis que la plupart du matériel autorise l’existence de plus de 52 000 références globales à la fois.

Pour suivre le moment où les références globales sont créées et détruites, vous pouvez définir la propriété système [Debug. mono. log](~/android/troubleshooting/index.md) de sorte qu’elle contienne [Gref](~/android/troubleshooting/index.md).

Les références globales peuvent être libérées explicitement en appelant [java. lang. Object. Dispose ()](xref:Java.Lang.Object.Dispose) sur le wrapper managé pouvant être appelé. Cela supprimera le mappage entre l’instance Java et l’instance gérée et autorisera la collecte de l’instance java. Si l’instance Java est à nouveau accessible à partir du code managé, un nouveau wrapper managé pouvant être appelé sera créé pour celle-ci.

Soyez vigilant lorsque vous supprimez des wrappers pouvant être appelés si l’instance peut être partagée par inadvertance entre les threads, car la suppression de l’instance aura un impact sur les références de tous les autres threads. Pour une sécurité maximale, seuls `Dispose()` d’instances qui ont été allouées via `new` *ou* à partir de méthodes que vous *connaissez* allouez systématiquement de nouvelles instances et non des instances mises en cache, ce qui peut entraîner un partage d’instance accidentel entre les threads.

## <a name="managed-callable-wrapper-subclasses"></a>Sous-classes de wrappers pouvant être appelées managées

Les sous-classes de wrappers pouvant être appelées sont là où toute la logique propre à l’application « intéressante » peut être active. Celles-ci incluent des sous-classes [Android. app. Activity](xref:Android.App.Activity) personnalisées (telles que le type [Activity1](https://github.com/xamarin/monodroid-samples/blob/master/HelloM4A/Activity1.cs#L13) dans le modèle de projet par défaut). (En particulier, il s’agit des sous-classes *java. lang. Object* qui ne contiennent *pas* d’attribut personnalisé [RegisterAttribute](xref:Android.Runtime.RegisterAttribute) ou [RegisterAttribute. DoNotGenerateAcw](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw) a la valeur *false*, qui est la valeur par défaut).

À l’instar des wrappers pouvant être appelés, les sous-classes de wrappers pouvant être appelés managées contiennent également une référence globale, accessible via la propriété [java. lang. Object. handle](xref:Java.Lang.Object.Handle) . Tout comme avec les wrappers pouvant être appelés, les références globales peuvent être libérées explicitement en appelant [java. lang. Object. Dispose ()](xref:Java.Lang.Object.Dispose).
Contrairement aux wrappers managés pouvant être appelés, il convient de *veiller* à la suppression de ces instances, car *dispose ()* -Ing de l’instance interrompt le mappage entre l’instance java (une instance d’un wrapper Android Callable) et le managé instancié.

### <a name="java-activation"></a>Activation Java

Lorsqu’un [Wrapper Android](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) est créé à partir de Java, le constructeur ACW entraîne l’appel du C# constructeur correspondant. Par exemple, le ACW pour *MainActivity* contient un constructeur par défaut qui appellera le constructeur par défaut de *MainActivity*. (Cette opération s’effectue par le biais de l’appel *TypeManager. Activate ()* dans les constructeurs ACW.)

Il existe une autre signature de constructeur de la conséquence : le constructeur *(IntPtr, JniHandleOwnership)* . Le constructeur *(IntPtr, JniHandleOwnership)* est appelé chaque fois qu’un objet Java est exposé au code managé et qu’un wrapper pouvant être appelé managé doit être construit pour gérer le descripteur JNI. Cette opération est généralement effectuée automatiquement.

Il existe deux scénarios dans lesquels le constructeur *(IntPtr, JniHandleOwnership)* doit être fourni manuellement sur une sous-classe de wrappers pouvant être appelés managés :

1. [Android. app. application](xref:Android.App.Application) est sous-classé. L' *application* est spéciale ; le constructeur *application* par défaut ne sera *jamais* appelé et le [constructeur (IntPtr, JniHandleOwnership) doit être fourni à la place](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/SanityTests/Hello.cs#L105).

2. Appel de méthode virtuelle à partir d’un constructeur de classe de base.

Notez que (2) est une abstraction avec fuite. En Java, comme dans C#, les appels aux méthodes virtuelles à partir d’un constructeur appellent toujours l’implémentation de méthode la plus dérivée. Par exemple, le [constructeur TextView (Context, AttributeSet, int)](xref:Android.Widget.TextView#ctor*) appelle la méthode virtuelle [TextView. getDefaultMovementMethod ()](https://developer.android.com/reference/android/widget/TextView.html#getDefaultMovementMethod()), qui est liée en tant que [propriété TextView. DefaultMovementMethod](xref:Android.Widget.TextView.DefaultMovementMethod).
Par conséquent, si un [LogTextBox](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs) de type a été (1) une sous- [classe TextView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L26), (2) [override TextView. DefaultMovementMethod](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L45)et (3) [active une instance de cette classe via XML,](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Resources/layout/log_text_box_1.xml#L29) la propriété *DefaultMovementMethod* substituée être appelé avant que le constructeur ACW ait eu l’occasion de s’exécuter, et il se produit C# avant que le constructeur ait eu l’occasion de s’exécuter.

Cela est pris en charge en instanciant une instance LogTextBox par le biais du constructeur [LogTextView (IntPtr, JniHandleOwnership)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L28) lorsque l’instance ACW LogTextBox entre en première place dans le code managé, puis en appelant le [LogTextBox (Context, IAttributeSet, int)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L41) *sur la même instance* lorsque le constructeur ACW s’exécute.

Ordre des événements :

1. La disposition XML est chargée dans un [ContentView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox1.cs#L41).

2. Android instancie le graphique d’objets de disposition et instancie une instance de *monodroid. apidemo. LogTextBox* , ACW pour *LogTextBox* .

3. Le constructeur *monodroid. apidemo. LogTextBox* exécute le constructeur [Android. widget. TextView](https://developer.android.com/reference/android/widget/TextView.html#TextView%28android.content.Context,%20android.util.AttributeSet%29) .

4. Le constructeur *TextView* appelle *monodroid. apidemo. LogTextBox. getDefaultMovementMethod ()* .

5. *monodroid. apidemo. LogTextBox. getDefaultMovementMethod ()* appelle *LogTextBox. n_getDefaultMovementMethod ()* , qui appelle *TextView. n_getDefaultMovementMethod ()* , qui appelle [Java. lang. Object. GetObject @no_ _t_4_ TextView&gt; (handle, JniHandleOwnership. DoNotTransfer)](xref:Java.Lang.Object.GetObject*) .

6. *Java. lang. Object. GetObject&lt;TextView&gt;()* vérifie s’il existe déjà une instance correspondante C# pour *descripteur* . Si c’est le cas, il est retourné. Dans ce scénario, ce n’est pas le cas, donc *Object. GetObject&lt;t&gt;()* doit en créer un.

7. *Object. GetObject&lt;t&gt;()* recherche le constructeur *LogTextBox (IntPtr, JniHandleOwneship)* , l’appelle, crée un mappage entre *handle* et l’instance créée et retourne l’instance créée.

8. *TextView. n_GetDefaultMovementMethod ()* appelle l’accesseur Get de la propriété *LogTextBox. DefaultMovementMethod* .

9. Le contrôle retourne au constructeur *Android. widget. TextView* , qui termine l’exécution.

10. Le constructeur *monodroid. apidemo. LogTextBox* s’exécute, en appelant *TypeManager. Activate ()* .

11. Le constructeur *LogTextBox (Context, IAttributeSet, int)* s’exécute *sur la même instance créée dans (7)* .

12. Si le constructeur (IntPtr, JniHandleOwnership) est introuvable, une exception System. MissingMethodException] (XREF : System. MissingMethodException) est levée.

<a name="Premature_Dispose_Calls" />

### <a name="premature-dispose-calls"></a>Appels dispose () prématurés

Il existe un mappage entre un handle JNI et l’instance C# correspondante. Java. lang. Object. Dispose () interrompt ce mappage. Si un handle JNI entre dans du code managé après la rupture du mappage, il ressemble à l’activation Java et le constructeur *(IntPtr, JniHandleOwnership)* est vérifié et appelé. Si le constructeur n’existe pas, une exception est levée.

Par exemple, à partir de la sous-classe d’encapsulage pouvant être appelée par Managed suivante :

```csharp
class ManagedValue : Java.Lang.Object {

    public string Value {get; private set;}

    public ManagedValue (string value)
    {
        Value = value;
    }

    public override string ToString ()
    {
        return string.Format ("[Managed: Value={0}]", Value);
    }
}
```

Si nous créons une instance, dispose () de celle-ci et que vous recréez le wrapper pouvant être appelé managé :

```csharp
var list = new JavaList<IJavaObject>();
list.Add (new ManagedValue ("value"));
list [0].Dispose ();
Console.WriteLine (list [0].ToString ());
```

Le programme va s’exécuter :

```shell
E/mono    ( 2906): Unhandled Exception: System.NotSupportedException: Unable to activate instance of type Scratch.PrematureDispose.ManagedValue from native handle 4051c8c8 --->
System.MissingMethodException: No constructor found for Scratch.PrematureDispose.ManagedValue::.ctor(System.IntPtr, Android.Runtime.JniHandleOwnership)
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateProxy (System.Type type, IntPtr handle, JniHandleOwnership transfer) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateInstance (IntPtr handle, JniHandleOwnership transfer, System.Type targetType) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   --- End of inner exception stack trace ---
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateInstance (IntPtr handle, JniHandleOwnership transfer, System.Type targetType) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Lang.Object.GetObject (IntPtr handle, JniHandleOwnership transfer, System.Type type) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Lang.Object._GetObject[IJavaObject] (IntPtr handle, JniHandleOwnership transfer) [0x00000
```

Si la sous-classe contient un constructeur *(IntPtr, JniHandleOwnership)* , une *nouvelle* instance du type sera créée. Par conséquent, l’instance semble « perdre » toutes les données d’instance, car il s’agit d’une nouvelle instance. (Notez que la valeur est null.)

```shell
I/mono-stdout( 2993): [Managed: Value=]
```

*Dispose* uniquement des sous-classes de wrappers pouvant être appelées managées lorsque vous savez que l’objet Java ne sera plus utilisé, ou que la sous-classe ne contient pas de données d’instance et qu’un constructeur *(IntPtr, JniHandleOwnership)* a été fourni.

## <a name="application-startup"></a>Démarrage de l'application

Lorsqu’une activité, un service, etc. est lancé, Android vérifie d’abord si un processus est déjà en cours d’exécution pour héberger l’activité/le service, etc. Si aucun processus de ce type n’existe, un nouveau processus est créé, [fichier AndroidManifest. xml](https://developer.android.com/guide/topics/manifest/manifest-intro.html) est lu et le type spécifié dans l’attribut [/manifest/application/@android:name](https://developer.android.com/guide/topics/manifest/application-element.html#nm) est chargé et instancié. Ensuite, tous les types spécifiés par les valeurs d’attribut [/manifest/application/provider/@android:name](https://developer.android.com/guide/topics/manifest/provider-element.html#nm) sont instanciés et la méthode [ContentProvider. attachInfo %28)](xref:Android.Content.ContentProvider.AttachInfo*) est appelée. Xamarin. Android s’attache à cela en ajoutant un *mono. MonoRuntimeProvider* *ContentProvider* à fichier AndroidManifest. xml pendant le processus de génération. *Mono. La méthode MonoRuntimeProvider. attachInfo ()* est chargée du chargement du runtime mono dans le processus.
Toute tentative d’utilisation de mono avant ce point échoue. ( *Remarque*: c’est la raison pour laquelle les types de sous-classes [Android. app. application](xref:Android.App.Application) doivent fournir un [constructeur (IntPtr, JniHandleOwnership)](https://github.com/xamarin/monodroid-samples/blob/a9e8ef23/SanityTests/Hello.cs#L103), car l’instance de l’application est créée avant que mono puisse être initialisé.)

Une fois l’initialisation du processus terminée, `AndroidManifest.xml` est consulté pour trouver le nom de la classe de l’activité/du service, etc. à lancer. Par exemple, l' [attribut/manifest/application/activity/@android:name](https://developer.android.com/guide/topics/manifest/activity-element.html#nm) est utilisé pour déterminer le nom d’une activité à charger. Pour les activités, ce type doit hériter d' [Android. app. Activity](xref:Android.App.Activity).
Le type spécifié est chargé via [Class. forName ()](https://developer.android.com/reference/java/lang/Class.html#forName(java.lang.String)) (ce qui nécessite que le type soit un type Java, par conséquent, les wrappers pouvant être appelés Android), puis instanciés. La création d’une instance de wrapper Android pouvant être appelé déclenchera la création d’une C# instance du type correspondant. Android appellera ensuite [Activity. OnCreate (Bundle)](https://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle)) , ce qui entraîne l’appel de l' [activité correspondante. OnCreate (Bundle)](xref:Android.App.Activity.OnCreate*) et vous êtes sur les courses.
