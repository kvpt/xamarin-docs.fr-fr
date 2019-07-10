---
title: Utilisation de JNI
description: Xamarin.Android autorise l’écriture d’applications Android dans C# au lieu de Java. Plusieurs assemblys sont fournis avec Xamarin.Android qui fournissent des liaisons pour les bibliothèques Java, y compris Mono.Android.dll et Mono.Android.GoogleMaps.dll. Toutefois, liaisons ne sont pas fournies pour toutes les bibliothèques Java et les liaisons sont fournies ne peuvent pas lier chaque type Java et membre. Pour utiliser les types Java indépendant et les membres, l’Interface Native Java (JNI) peut être utilisé. Cet article illustre l’utilisation de JNI pour interagir avec les types Java et les membres à partir d’applications Xamarin.Android.
ms.prod: xamarin
ms.assetid: A417DEE9-7B7B-4E35-A79C-284739E3838E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: aa2e2ac96b37bc781f2e4a3778ea0aaf970649ec
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674606"
---
# <a name="working-with-jni"></a>Utilisation de JNI

_Xamarin.Android autorise l’écriture d’applications Android dans C# au lieu de Java. Plusieurs assemblys sont fournis avec Xamarin.Android qui fournissent des liaisons pour les bibliothèques Java, y compris Mono.Android.dll et Mono.Android.GoogleMaps.dll. Toutefois, liaisons ne sont pas fournies pour toutes les bibliothèques Java et les liaisons sont fournies ne peuvent pas lier chaque type Java et membre. Pour utiliser les types Java indépendant et les membres, l’Interface Native Java (JNI) peut être utilisé. Cet article illustre l’utilisation de JNI pour interagir avec les types Java et les membres à partir d’applications Xamarin.Android._


## <a name="overview"></a>Vue d'ensemble

Il n’est pas toujours nécessaire ou possible de créer un gérés Callable Wrapper (MCW) pour appeler le code Java. Dans de nombreux cas, « inline » JNI est parfaitement acceptable et utiles pour une utilisation unique de membres de Java indépendants. Il est souvent plus simple d’utiliser JNI pour appeler une méthode unique sur une classe Java que génère une liaison .jar entière.

Xamarin.Android fournit la `Mono.Android.dll` assembly, qui fournit une liaison pour Android `android.jar` bibliothèque. Types et membres non présent dans `Mono.Android.dll` et les types non présents dans `android.jar` peut être utilisé par les lier manuellement. Pour lier les types Java et les membres, vous utilisez le **Java Native Interface** (**JNI**) à rechercher des types, lire et écrire des champs et appeler des méthodes.

L’API JNI dans Xamarin.Android s’apparente conceptuellement à la `System.Reflection` API dans .NET : elle permet de rechercher des types et membres par nom, lire et écrire les valeurs de champ, appeler des méthodes et bien plus encore. Vous pouvez utiliser JNI et `Android.Runtime.RegisterAttribute` attribut personnalisé à déclarer des méthodes virtuelles qui peuvent être liées pour prendre en charge la substitution. Vous pouvez lier des interfaces afin qu’elles peuvent être implémentées dans C#.

Ce document explique :

-  Comment JNI fait référence aux types.
-  Comment rechercher, lire et écrire des champs.
-  Comment rechercher et appeler des méthodes.
-  Guide pratique pour exposer des méthodes virtuelles pour permettre le remplacement du code managé.
-  Guide pratique pour exposer des interfaces.



## <a name="requirements"></a>Configuration requise

JNI, telle qu’exposée via la [espace de noms Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/), est disponible dans chaque version de Xamarin.Android.
Pour lier les interfaces et les types Java, vous devez utiliser Xamarin.Android 4.0 ou version ultérieure.


## <a name="managed-callable-wrappers"></a>Wrappers RCW gérés

Un **gérés Callable Wrapper** (**MCW**) est un *liaison* pour une interface qui toutes les machines JNI conclut donc que le client ou une classe Java C# code n’a pas besoin soucier de la complexité sous-jacente de JNI. La plupart des `Mono.Android.dll` se compose des wrappers RCW gérés.

Les wrappers RCW gérés ont deux utilités :

1.  Encapsuler l’utilisation JNI afin que le code client n’a pas besoin de savoir à propos de la complexité sous-jacente.
1.  Permettent de sous-composant classe types Java et d’implémenter des interfaces Java.

L’objectif premier est purement pour plus de commodité et l’encapsulation de complexité afin que les consommateurs ont un ensemble de classes à utiliser simple et géré. Cela nécessite l’utilisation des divers [JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/) membres comme décrit plus loin dans cet article. N’oubliez pas que des callable wrappers managés ne sont pas strictement nécessaires &ndash; « inline » JNI utiliser est parfaitement acceptable et est utile pour une utilisation unique de membres de Java indépendants. Implémentation de sous-classe et interface requiert l’utilisation de wrappers RCW gérés.



## <a name="android-callable-wrappers"></a>Wrappers pouvant être appelés par Android

Les wrappers RCW Android (ACW) sont requis chaque fois que le runtime Android (ART) doit appeler du code managé ; ces wrappers sont requis, car il n’existe aucun moyen pour inscrire des classes avec l’ART lors de l’exécution.
(Plus précisément, le [DefineClass](http://docs.oracle.com/javase/6/docs/technotes/guides/jni/spec/functions.html#wp15986) (fonction) JNI n’est pas pris en charge par le runtime Android. Les wrappers RCW Android donc composent l’absence de prise en charge de l’inscription de type runtime.)

Chaque fois que le code Android doit exécuter une machine virtuelle ou la méthode qui est substituée ou implémentée dans du code managé d’interface, Xamarin.Android doit fournir un proxy Java pour que cette méthode est envoyée vers le type managé approprié. Ces types de proxy Java sont du code Java qui ont de la classe de base « même » et de la liste des interfaces Java en tant que type managé, implémenter les constructeurs mêmes et la déclaration de n’importe quelle classe de base substituée et méthodes d’interface.

Les wrappers RCW Android sont générés par le **monodroid.exe** du programme pendant le [du processus de génération](~/android/deploy-test/building-apps/build-process.md)et sont générées pour tous les types qui héritent de (directement ou indirectement) [ Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/).



### <a name="implementing-interfaces"></a>Implémentation des interfaces

Parfois, lorsque vous devez implémenter une interface Android, (telles que [Android.Content.IComponentCallbacks](https://developer.xamarin.com/api/type/Android.Content.IComponentCallbacks/)).

Toutes les classes Android et interfaces étendent la [Android.Runtime.IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) interface ; par conséquent, tous les types d’Android doivent implémenter `IJavaObject`.
Xamarin.Android tire parti de ce fait &ndash; il utilise `IJavaObject` pour fournir Android avec un proxy Java (un wrapper CCW Android) pour le type managé donné. Étant donné que **monodroid.exe** recherche uniquement `Java.Lang.Object` sous-classes (qui doit implémenter `IJavaObject`), sous-classement `Java.Lang.Object` nous offre un moyen d’implémenter des interfaces en code managé. Par exemple :

```csharp
class MyComponentCallbacks : Java.Lang.Object, Android.Content.IComponentCallbacks {
    public void OnConfigurationChanged (Android.Content.Res.Configuration newConfig) {
        // implementation goes here...
    }
    public void OnLowMemory () {
        // implementation goes here...
    }
}
```


### <a name="implementation-details"></a>Détails de l’implémentation

*Le reste de cet article fournit des détails d’implémentation susceptibles d’être modifiées sans préavis* (et est présentée ici uniquement parce que les développeurs peuvent être curieux de savoir ce qui se passe sous le capot).

Par exemple, étant donné ce qui suit C# source :

```csharp
using System;
using Android.App;
using Android.OS;

namespace Mono.Samples.HelloWorld
{
    public class HelloAndroid : Activity
    {
        protected override void OnCreate (Bundle savedInstanceState)
        {
            base.OnCreate (savedInstanceState);
            SetContentView (R.layout.main);
        }
    }
}
```

Le **mandroid.exe** programme générera le Wrapper Android suivants :

```java
package mono.samples.helloWorld;

public class HelloAndroid extends android.app.Activity {
    static final String __md_methods;
    static {
        __md_methods =
            "n_onCreate:(Landroid/os/Bundle;)V:GetOnCreate_Landroid_os_Bundle_Handler\n" +
            "";
        mono.android.Runtime.register (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null",
                HelloAndroid.class,
                __md_methods);
    }

    public HelloAndroid ()
    {
        super ();
        if (getClass () == HelloAndroid.class)
            mono.android.TypeManager.Activate (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null",
                "", this, new java.lang.Object[] { });
    }

    @Override
    public void onCreate (android.os.Bundle p0)
    {
        n_onCreate (p0);
    }

    private native void n_onCreate (android.os.Bundle p0);
}
```

Notez que la classe de base est préservée, et les déclarations de méthode native sont fournies pour chaque méthode est substituée dans du code géré.



### <a name="exportattribute-and-exportfieldattribute"></a>ExportAttribute et ExportFieldAttribute

En règle générale, Xamarin.Android génère automatiquement le code Java qui comprend le ACW ; Cette génération est basée sur les noms de classe et méthode quand une classe dérive d’une classe Java et substitue les méthodes Java existants. Toutefois, dans certains scénarios, la génération de code n’est pas suffisante, comme indiqué ci-dessous :

-   Android prend en charge les noms d’actions dans les attributs XML de disposition, par exemple le [android : onClick](https://developer.xamarin.com/api/member/Android.Views.View+IOnClickListener.OnClick/p/Android.Views.View/) attribut XML. Lorsqu’il est spécifié, l’instance de vue gonflé tente de rechercher la méthode Java.

-   Le [java.io.Serializable](https://developer.android.com/reference/java/io/Serializable.html) interface requiert `readObject` et `writeObject` méthodes. Dans la mesure où ils ne sont pas membres de cette interface, notre implémentation managée correspondante n’expose pas ces méthodes pour le code Java.

-   Le [android.os.Parcelable](https://developer.xamarin.com/api/type/Android.Os.Parcelable/) interface attend qu’une classe d’implémentation doit avoir un champ statique `CREATOR` de type `Parcelable.Creator`. Le code Java généré requiert un champ explicit. Avec notre scénario standard, il n’existe aucun moyen pour le champ de sortie dans le code Java à partir du code managé.


Étant donné que la génération de code ne fournit pas une solution pour générer des méthodes Java arbitraires avec des noms arbitraires, en commençant par Xamarin.Android 4.2, les [ExportAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute/) et [ExportFieldAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportFieldAttribute/) ont été introduit pour offrir une solution pour les scénarios ci-dessus. Les deux attributs se trouvent dans le `Java.Interop` espace de noms :

-   `ExportAttribute` &ndash; Spécifie un nom de méthode et ses types d’exception attendue (pour donner explicite « jette » dans Java). Lorsqu’il est utilisé sur une méthode, la méthode sera « exporter » une méthode Java qui génère un code d’expédition à l’appel de JNI correspondant à la méthode managée. Cela peut être utilisé avec `android:onClick` et `java.io.Serializable`.

-   `ExportFieldAttribute` &ndash; Spécifie un nom de champ. Il se trouve sur une méthode qui fonctionne comme un initialiseur de champ. Cela peut être utilisé avec `android.os.Parcelable`.

Le [ExportAttribute](https://developer.xamarin.com/samples/monodroid/ExportAttribute/) exemple de projet illustre l’utilisation de ces attributs.


#### <a name="troubleshooting-exportattribute-and-exportfieldattribute"></a>Résolution des problèmes de ExportAttribute et ExportFieldAttribute

-   Échec d’empaquetage due à l’absence **Mono.Android.Export.dll** &ndash; si vous avez utilisé `ExportAttribute` ou `ExportFieldAttribute` sur certaines méthodes dans votre code ou les bibliothèques dépendantes, vous devez ajouter  **Mono.Android.Export.dll**. Cet assembly est isolé pour prendre en charge le code de rappel à partir de Java. Il se distingue **Mono.Android.dll** quand il ajoute la taille supplémentaire à l’application.

-   Dans la version Release, `MissingMethodException` se produit pour les méthodes d’exportation &ndash; dans la version Release, `MissingMethodException` se produit pour les méthodes d’exportation. (Ce problème est résolu dans la dernière version de Xamarin.Android).



### <a name="exportparameterattribute"></a>ExportParameterAttribute

`ExportAttribute` et `ExportFieldAttribute` fournissent des fonctionnalités que Java Runtime code peut utiliser. Ce code d’exécution accède à du code managé via les méthodes générées de JNI dues à ces attributs. Par conséquent, il n’existe aucune méthode Java existant qui lie de la méthode managée ; Par conséquent, la méthode Java est générée à partir d’une signature de méthode managée.

Toutefois, ce cas n’est pas entièrement déterminant. Cela vaut plus particulièrement, dans certains mappages avancées entre les types managés et des types Java, tels que :

-  InputStream
-  OutputStream
-  XmlPullParser
-  XmlResourceParser

Lorsque les types telles que celles-ci sont nécessaires pour les méthodes exportées, le `ExportParameterAttribute` doit être utilisé pour donner au paramètre correspondant explicitement ou d’un type de valeur de retour.



### <a name="annotation-attribute"></a>Attribut d’annotation

Dans Xamarin.Android, 4.2, nous avons converti `IAnnotation` types d’implémentation dans les attributs (System.Attribute) et la prise en charge pour la génération d’annotation dans les wrappers de Java.

Cela signifie que les modifications directionnelles suivantes :

-   Le Générateur de liaison génère `Java.Lang.DeprecatedAttribute` de `java.Lang.Deprecated` (s’il doit être `[Obsolete]` dans du code managé).

-   Cela ne signifie pas que déjà `Java.Lang.Deprecated` classe disparaîtront. Ces objets basée sur Java peut toujours servir en tant qu’objets de Java habituelles (si une telle utilisation existe). Il y aura `Deprecated` et `DeprecatedAttribute` classes.

-   Le `Java.Lang.DeprecatedAttribute` classe est marquée comme `[Annotation]` . Lorsqu’il existe un attribut personnalisé qui est hérité de cette `[Annotation]` attribut, tâche msbuild génère une annotation de Java pour cet attribut personnalisé (@Deprecated) dans le Wrapper RCW Android (ACW).

-   Annotations a pu être créées sur les classes, méthodes et exportés des champs (qui est une méthode dans le code managé).

Si la classe de conteneur (la classe annotée lui-même ou la classe qui contient les membres annotés) n’est pas inscrit, la source de classe Java entière n'est pas générée, y compris les annotations. Pour les méthodes, vous pouvez spécifier le `ExportAttribute` pour obtenir la méthode explicitement généré et annotées. En outre, il n’est pas une fonctionnalité à une définition de classe Java annotation « générer ». En d’autres termes, si vous définissez un attribut personnalisé géré pour une annotation de certains, vous devrez ajouter une autre bibliothèque .jar qui contient la classe d’annotation Java correspondante. Ajout d’un fichier source Java qui définit le type d’annotation n’est pas suffisant. Le compilateur Java ne fonctionne pas dans la même façon que **apt**.

En outre, les limitations suivantes s’appliquent :

-   Ce processus de conversion ne tient pas compte `@Target` annotation sur le type d’annotation jusqu'à présent.

-   Attributs sur une propriété ne fonctionne pas. Utilisez à la place des attributs pour l’accesseur Get de propriété ou accesseur Set.



## <a name="class-binding"></a>Liaison de classe

Une classe de liaison signifie écrire un wrapper géré pouvant être appelé pour simplifier l’invocation du type Java sous-jacent.

Méthodes virtuelles et abstraites pour permettre la substitution de la liaison C# nécessite Xamarin.Android 4.0. Toutefois, n’importe quelle version de Xamarin.Android peut lier les méthodes non virtuelles, des méthodes statiques ou des méthodes virtuelles sans prendre en charge des substitutions.

En règle générale, une liaison contient les éléments suivants :

-  Un [JNI handle vers le type Java lié](#_Looking_up_Java_Types).

-  [JNI champ ID et des propriétés pour chaque champ lié](#_Instance_Fields).

-  [ID de méthode JNI et méthodes pour chaque lié méthode](#_Instance_Methods).

-  Si la sous-classe est requis, le type doit avoir un [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) un attribut personnalisé sur la déclaration de type avec [RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) défini sur `true`.



### <a name="declaring-type-handle"></a>Handle du Type déclarant

Les méthodes de recherche de champ et la méthode nécessitent une référence d’objet faisant référence à son type déclarant. Par convention, il est maintenu dans un `class_ref` champ :

```csharp
static IntPtr class_ref = JNIEnv.FindClass(CLASS);
```

Consultez le [références de Type JNI](#_JNI_Type_References) section pour plus d’informations sur la `CLASS` jeton.


### <a name="binding-fields"></a>Champs de liaison

Champs de Java sont exposés en tant que C# propriétés, par exemple le champ Java [java.lang.System.in](https://developer.android.com/reference/java/lang/System.html#in) est lié en tant que le C# propriété [Java.Lang.JavaSystem.In](https://developer.xamarin.com/api/property/Java.Lang.JavaSystem.In/).
En outre, étant donné que JNI fait la distinction entre les champs statiques et les champs d’instance, différentes méthodes servir lors de l’implémentation des propriétés.

La liaison de champ implique trois ensembles de méthodes :

1.  Le *obtenir l’id de champ* (méthode). Le *obtenir l’id de champ* méthode est chargée de retourner un champ gérer cela le *obtenir la valeur du champ* et *définir la valeur du champ* méthodes utiliseront. Obtention de l’id de champ nécessite de connaître la déclaration de type, le nom du champ et le [signature de type JNI](#JNI_Type_Signatures) du champ.

1.  Le *obtenir la valeur du champ* méthodes. Ces méthodes requièrent le handle de champ et sont responsables de lire la valeur du champ à partir de Java.
    La méthode à utiliser varie selon le type de champ.

1.  Le *définir la valeur du champ* méthodes. Ces méthodes nécessitent le handle de champ et sont responsables de l’écriture de la valeur du champ dans Java. La méthode à utiliser varie selon le type de champ.


 [Les champs statiques](#_Static_Fields) utiliser le [JNIEnv.GetStaticFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/), `JNIEnv.GetStatic*Field`, et [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/) méthodes.

 [Champs d’instance](#_Instance_Fields) utiliser le [JNIEnv.GetFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFieldID/), `JNIEnv.Get*Field`, et [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/) méthodes.

Par exemple, la propriété statique `JavaSystem.In` peut être implémentée en tant que :

```csharp
static IntPtr in_jfieldID;
public static System.IO.Stream In
{
    get {
        if (in_jfieldId == IntPtr.Zero)
            in_jfieldId = JNIEnv.GetStaticFieldID (class_ref, "in", "Ljava/io/InputStream;");
        IntPtr __ret = JNIEnv.GetStaticObjectField (class_ref, in_jfieldId);
        return InputStreamInvoker.FromJniHandle (__ret, JniHandleOwnership.TransferLocalRef);
    }
}
```

Remarque : Nous utilisons [InputStreamInvoker.FromJniHandle](https://developer.xamarin.com/api/member/Android.Runtime.InputStreamInvoker.FromJniHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) pour convertir la référence JNI dans un `System.IO.Stream` instance et nous utilisons `JniHandleOwnership.TransferLocalRef` car [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/) retourne un référence locale.

Un grand nombre de la [Android.Runtime](https://developer.xamarin.com/api/namespace/Android.Runtime/) types ont `FromJniHandle` référence sur les méthodes qui convertira un JNI dans le type souhaité.



### <a name="method-binding"></a>Liaison de méthode

Méthodes Java sont exposées en tant que C# méthodes et en tant que C# propriétés. Par exemple, la méthode Java [java.lang.Runtime.runFinalizersOnExit](https://developer.android.com/reference/java/lang/Runtime.html#runFinalizersOnExit(boolean)) méthode est liée en tant que le [Java.Lang.Runtime.RunFinalizersOnExit](https://developer.xamarin.com/api/member/Java.Lang.Runtime.RunFinalizersOnExit/) (méthode) et le [java.lang.Object.getClass ](https://developer.android.com/reference/java/lang/Object.html#getClass) méthode est liée en tant que le [Java.Lang.Object.Class](https://developer.xamarin.com/api/property/Java.Lang.Object.Class/) propriété.

Appel de méthode est un processus en deux étapes :

1.  Le *obtenir l’id de la méthode* pour la méthode à appeler. Le *obtenir l’id de la méthode* méthode est chargée de retourner un handle de méthode qui utilisent les méthodes d’appel de méthode. Obtention de l’id de la méthode nécessite de connaître le la déclaration de type, le nom de la méthode et le [signature de type JNI](#JNI_Type_Signatures) de la méthode.

1.  Appelez la méthode.

Comme avec les champs, les méthodes à utiliser pour obtenir l’id de méthode et appeler la méthode diffèrent entre les méthodes statiques et des méthodes d’instance.

[Méthodes statiques](#_Static_Methods_1) utiliser [JNIEnv.GetStaticMethodID()](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/) pour rechercher l’id de méthode, utilisez la `JNIEnv.CallStatic*Method` famille de méthodes pour l’appel.

[Méthodes d’instance](#_Instance_Methods) utiliser [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/) pour rechercher l’id de méthode, utilisez la `JNIEnv.Call*Method` et `JNIEnv.CallNonvirtual*Method` familles des méthodes pour l’appel.

Liaison de méthode est potentiellement plus qu’appel de méthode. Liaison de méthode inclut ce qui permet une méthode de substitution (pour les méthodes abstraites et non finale) également ou implémenté (pour les méthodes d’interface). Le [prenant en charge un héritage, Interfaces](#_Supporting_Inheritance,_Interfaces_1) section couvre les complexités de prendre en charge les méthodes virtuelles et les méthodes d’interface.

<a name="_Static_Methods_1" />

#### <a name="static-methods"></a>Méthodes statiques

Liaison d’une méthode statique implique l’utilisation de `JNIEnv.GetStaticMethodID` pour obtenir un handle de méthode, puis respectant `JNIEnv.CallStatic*Method` (méthode), selon le type de retour de la méthode. Voici un exemple d’une liaison pour le [Runtime.getRuntime](https://developer.android.com/reference/java/lang/Runtime.html#getRuntime()) méthode :

```csharp
static IntPtr id_getRuntime;

[Register ("getRuntime", "()Ljava/lang/Runtime;", "")]
public static Java.Lang.Runtime GetRuntime ()
{
    if (id_getRuntime == IntPtr.Zero)
        id_getRuntime = JNIEnv.GetStaticMethodID (class_ref,
                "getRuntime", "()Ljava/lang/Runtime;");

    return Java.Lang.Object.GetObject<Java.Lang.Runtime> (
            JNIEnv.CallStaticObjectMethod  (class_ref, id_getRuntime),
            JniHandleOwnership.TransferLocalRef);
}
```

Notez que nous stockons le handle de méthode dans un champ statique, `id_getRuntime`. Il s’agit d’une optimisation des performances, afin que le handle de méthode n’a pas besoin être recherchés à chaque appel. Il n’est pas nécessaire pour mettre en cache le handle de méthode de cette façon. Une fois que le handle de méthode est obtenu, [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) est utilisé pour appeler la méthode. `JNIEnv.CallStaticObjectMethod` Retourne un `IntPtr` qui contient le handle de l’instance retournée de Java.
[Java.Lang.Object.GetObject&lt;T&gt;(IntPtr, JniHandleOwnership)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) est utilisée pour convertir le handle de Java dans une instance d’objet fortement typé.



#### <a name="non-virtual-instance-method-binding"></a>Liaison de méthode d’Instance non virtuelle

Liaison un `final` méthode d’instance ou une méthode d’instance qui ne requiert pas le remplacement, implique l’utilisation de `JNIEnv.GetMethodID` pour obtenir un handle de méthode, puis respectant `JNIEnv.Call*Method` (méthode), selon le type de retour de la méthode. Voici un exemple d’une liaison pour le `Object.Class` propriété :

```csharp
static IntPtr id_getClass;
public Java.Lang.Class Class {
    get {
        if (id_getClass == IntPtr.Zero)
            id_getClass = JNIEnv.GetMethodID (class_ref, "getClass", "()Ljava/lang/Class;");
        return Java.Lang.Object.GetObject<Java.Lang.Class> (
                JNIEnv.CallObjectMethod (Handle, id_getClass),
                JniHandleOwnership.TransferLocalRef);
    }
}
```

Notez que nous stockons le handle de méthode dans un champ statique, `id_getClass`.
Il s’agit d’une optimisation des performances, afin que le handle de méthode n’a pas besoin être recherchés à chaque appel. Il n’est pas nécessaire pour mettre en cache le handle de méthode de cette façon. Une fois que le handle de méthode est obtenu, [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) est utilisé pour appeler la méthode. `JNIEnv.CallStaticObjectMethod` Retourne un `IntPtr` qui contient le handle de l’instance retournée de Java.
[Java.Lang.Object.GetObject&lt;T&gt;(IntPtr, JniHandleOwnership)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) est utilisée pour convertir le handle de Java dans une instance d’objet fortement typé.


### <a name="binding-constructors"></a>Constructeurs de liaison

Les constructeurs sont des méthodes Java portant le nom `"<init>"`. Comme avec les méthodes d’instance Java, `JNIEnv.GetMethodID` est utilisé pour rechercher le handle de constructeur. Contrairement aux méthodes de Java, le [JNIEnv.NewObject](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewObject/) méthodes sont utilisées pour appeler le handle de méthode de constructeur. La valeur de retour de `JNIEnv.NewObject` est une référence locale de JNI :


```csharp
int value = 42;
IntPtr class_ref    = JNIEnv.FindClass ("java/lang/Integer");
IntPtr id_ctor_I    = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
IntPtr lrefInstance = JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value));
// Dispose of lrefInstance, class_ref…
```

Normalement une liaison de la classe sera sous-classe [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/).
Lors du sous-classement `Java.Lang.Object`, un supplémentaires sémantique intervient : un `Java.Lang.Object` instance conserve une référence globale à une instance Java via le `Java.Lang.Object.Handle` propriété.

1.  Le `Java.Lang.Object` constructeur par défaut alloue une instance de Java.

1.  Si le type a un `RegisterAttribute` , et `RegisterAttribute.DoNotGenerateAcw` est `true` , puis une instance de la `RegisterAttribute.Name` type est créé via son constructeur par défaut.

1.  Sinon, le [Android Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) correspondant à `this.GetType` est instancié via son constructeur par défaut. Les Wrappers RCW Android sont générés lors de la création de package pour chaque `Java.Lang.Object` sous-classe pour lequel `RegisterAttribute.DoNotGenerateAcw` n’a pas la valeur `true`.

Pour les types qui sont ne classe pas les liaisons, cela est attendu sémantique : instancier un `Mono.Samples.HelloWorld.HelloAndroid` C# instance doit construire un Java `mono.samples.helloworld.HelloAndroid` instance qui est un Wrapper RCW Android généré.

Pour les liaisons de classe, cela peut être le comportement correct si le type Java contient un constructeur par défaut ou aucun autre constructeur ne doit être appelé. Sinon, un constructeur doit être fourni qui effectue les actions suivantes :

1.  Appel de la [Java.Lang.Object (IntPtr, JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) au lieu de la valeur par défaut `Java.Lang.Object` constructeur. Cela est nécessaire pour éviter de créer une nouvelle instance de Java.

1.  Vérifiez la valeur de [Java.Lang.Object.Handle](https://developer.xamarin.com/api/property/Java.Lang.Object.Handle/) avant de créer toutes les instances de Java. Le `Object.Handle` propriété aura une valeur autre que `IntPtr.Zero` si un Wrapper RCW Android a été construit dans le code Java et la liaison de la classe est en cours de construction pour contenir l’instance de Wrapper RCW Android créée. Par exemple, quand Android crée un `mono.samples.helloworld.HelloAndroid` instance, le Wrapper RCW Android sera créé premier et le Java `HelloAndroid` constructeur crée une instance correspondante `Mono.Samples.HelloWorld.HelloAndroid` type, avec le `Object.Handle` propriété en cours défini sur l’instance Java avant l’exécution du constructeur.

1.  Si le type de runtime actuel n’est pas le même que la déclaration de type, une instance du Wrapper RCW Android correspondante doit être créé puis utiliser [Object.SetHandle](https://developer.xamarin.com/api/member/Java.Lang.Object.SetHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) pour stocker le handle retourné par [ JNIEnv.CreateInstance](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CreateInstance/).

1.  Si le type de runtime actuel est le même que le type déclarant, puis appeler le constructeur de Java et utilisez [Object.SetHandle](https://developer.xamarin.com/api/member/Java.Lang.Object.SetHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership)) pour stocker le handle retourné par `JNIEnv.NewInstance` .


Par exemple, considérez le [java.lang.Integer(int)](https://developer.android.com/reference/java/lang/Integer.html#Integer(int)) constructeur. Cela est lié en tant que :

```csharp
// Cache the constructor's method handle for later use
static IntPtr id_ctor_I;

// Need [Register] for subclassing
// RegisterAttribute.Name is always ".ctor"
// RegisterAttribute.Signature is tye JNI type signature of constructor
// RegisterAttribute.Connector is ignored; use ""
[Register (".ctor", "(I)V", "")]
public Integer (int value)
    // 1. Prevent Object default constructor execution
    : base (IntPtr.Zero, JniHandleOwnership.DoNotTransfer)
{
    // 2. Don't allocate Java instance if already allocated
    if (Handle != IntPtr.Zero)
        return;

    // 3. Derived type? Create Android Callable Wrapper
    if (GetType () != typeof (Integer)) {
        SetHandle (
                Android.Runtime.JNIEnv.CreateInstance (GetType (), "(I)V", new JValue (value)),
                JniHandleOwnership.TransferLocalRef);
        return;
    }

    // 4. Declaring type: lookup &amp; cache method id...
    if (id_ctor_I == IntPtr.Zero)
        id_ctor_I = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
    // ...then create the Java instance and store
    SetHandle (
            JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value)),
            JniHandleOwnership.TransferLocalRef);
}
```

Le [JNIEnv.CreateInstance](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CreateInstance/) méthodes sont des programmes d’assistance pour effectuer un `JNIEnv.FindClass`, `JNIEnv.GetMethodID`, `JNIEnv.NewObject`, et `JNIEnv.DeleteGlobalReference` sur la valeur retournée par `JNIEnv.FindClass`. Consultez la section suivante pour plus de détails.

<a name="_Supporting_Inheritance,_Interfaces_1" />

### <a name="supporting-inheritance-interfaces"></a>Prise en charge l’héritage, Interfaces

Sous-classement d’un type Java ou en implémentant une interface Java requiert la génération de [Android Callable Wrappers](~/android/platform/java-integration/android-callable-wrappers.md) (ACWs) qui sont générés pour chaque `Java.Lang.Object` sous-classe pendant le processus d’empaquetage. Génération de ACW est contrôlée par le biais du [Android.Runtime.RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/) attribut personnalisé.

Pour C# types, le `[Register]` constructeur d’attribut personnalisé requiert un argument : le [JNI simplifié la référence de type](#_Simplified_Type_References_1) pour le correspondante Java tapez. Cela permet de fournir des noms différents entre Java et C#.

Antérieures à 4.0 Xamarin.Android, le `[Register]` attribut personnalisé n’est pas disponible pour « alias » les types Java existants. Il s’agit, car le processus de génération ACW générerait ACWs pour chaque `Java.Lang.Object` sous-classe rencontrée.

Xamarin.Android 4.0 a introduit le [RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/) propriété. Cette propriété indique le processus de génération ACW *ignorer* le type annoté, ce qui permet la déclaration de nouveaux Wrappers RCW gérés ne provoque pas de ACWs en cours de génération au moment de la création de package. Ainsi, les types Java existants de liaison. Par exemple, prenons la classe Java simple suivante, `Adder`, qui contient une méthode, `add`, qui ajoute à des entiers et retourne le résultat :

```java
package mono.android.test;
public class Adder {
    public int add (int a, int b) {
        return a + b;
    }
}
```

Le `Adder` type peut être lié en tant que :

```csharp
[Register ("mono/android/test/Adder", DoNotGenerateAcw=true)]
public partial class Adder : Java.Lang.Object {
    static IntPtr class_ref = JNIEnv.FindClass ( "mono/android/test/Adder");

    public Adder ()
    {
    }

    public Adder (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }
}
partial class ManagedAdder : Adder {
}
```

Ici, le `Adder` C# type *alias* le `Adder` type Java. Le `[Register]` attribut est utilisé pour spécifier le nom JNI de la `mono.android.test.Adder` type Java et le `DoNotGenerateAcw` propriété est utilisée pour empêcher la génération de ACW. Cela entraîne la génération d’un ACW pour le `ManagedAdder` de type, qui correctement sous-classes le `mono.android.test.Adder` type. Si le `RegisterAttribute.DoNotGenerateAcw` propriété n’avait pas été utilisée, puis le processus de génération Xamarin.Android aurait généré un nouveau `mono.android.test.Adder` type Java. Cela entraînerait des erreurs de compilation, comme le `mono.android.test.Adder` type est présent à deux reprises, en deux fichiers distincts.



### <a name="binding-virtual-methods"></a>Méthodes virtuelles de liaison

`ManagedAdder` sous-classes Java `Adder` type, mais il n’est pas particulièrement intéressant : le C# `Adder` type ne définit pas les méthodes virtuelles, par conséquent, `ManagedAdder` ne peut pas remplacer quoi que ce soit.

Liaison `virtual` méthodes pour permettre la substitution par les sous-classes nécessite plusieurs choses à faire ce qui se répartissent en deux catégories suivantes :

1.  **Liaison de méthode**

1.  **Inscription de la méthode**


#### <a name="method-binding"></a>Liaison de méthode

Une liaison de méthode nécessite l’ajout de deux membres de prise en charge pour le C# `Adder` définition : `ThresholdType`, et `ThresholdClass`.

##### <a name="thresholdtype"></a>ThresholdType

Le `ThresholdType` propriété retourne le type actuel de la liaison :

```csharp
partial class Adder {
    protected override System.Type ThresholdType {
        get {
            return typeof (Adder);
        }
    }
}
```

`ThresholdType` est utilisé dans la méthode de liaison pour déterminer quand il doit exécuter virtuelle et l’expédition de méthode non virtuelle. Elle doit toujours retourner un `System.Type` instance qui correspond à la déclaration C# type.

##### <a name="thresholdclass"></a>ThresholdClass

Le `ThresholdClass` propriété retourne la référence de classe JNI pour le type de limite :

```csharp
partial class Adder {
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```

`ThresholdClass` est utilisé dans la méthode de liaison lors de l’appel des méthodes non virtuelles.

#### <a name="binding-implementation"></a>Implémentation de la liaison

L’implémentation de liaison de méthode est responsable de l’appel d’exécution de la méthode de Java. Il contient également un `[Register]` déclaration d’attribut personnalisé qui fait partie de l’inscription de la méthode et sera abordée dans la section de l’inscription de la méthode :

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
    public virtual int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        if (GetType () == ThresholdType)
            return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
        return JNIEnv.CallNonvirtualIntMethod (Handle, ThresholdClass, id_add, new JValue (a), new JValue (b));
    }
}
```

Le `id_add` champ contient l’ID de méthode pour la méthode Java à appeler. Le `id_add` valeur est obtenue à partir de `JNIEnv.GetMethodID`, ce qui nécessite la classe de déclaration (`class_ref`), le nom de la méthode Java (`"add"`) et de la méthode, la signature JNI (`"(II)I"`).

Une fois que l’ID de méthode est obtenu, `GetType` est comparé à `ThresholdType` pour déterminer si dispatch virtuel ou non virtuelle est requise. Dispatch virtuel est requis quand `GetType` correspond à `ThresholdType`, en tant que `Handle` peuvent faire référence à une sous-classe alloué Java qui substitue la méthode.

Lorsque `GetType` ne correspond pas à `ThresholdType`, `Adder` a été sous-classé (par exemple, `ManagedAdder`) et le `Adder.Add` implémentation sera appelée uniquement si la sous-classe appelé `base.Add`. C’est le cas de répartition non virtuelle, là où `ThresholdClass` arrive. `ThresholdClass` Spécifie quelle classe Java fournit l’implémentation de la méthode à appeler.



#### <a name="method-registration"></a>Inscription de la méthode

Supposons que nous avons une mise à jour `ManagedAdder` définition qui remplace le `Adder.Add` méthode :

```csharp
partial class ManagedAdder : Adder {
    public override int Add (int a, int b) {
        return (a*2) + (b*2);
    }
}
```

N’oubliez pas que `Adder.Add` avait un `[Register]` attribut personnalisé :

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
```

Le `[Register]` constructeur d’attribut personnalisé accepte trois valeurs :

1.  Le nom de la méthode Java, `"add"` dans ce cas.

1.  La Signature de Type JNI de la méthode, `"(II)I"` dans ce cas.

1.  Le *méthode de connecteur* , `GetAddHandler` dans ce cas.
    Nous verrons par la suite des méthodes de connecteur.


Les deux premiers paramètres autoriser le processus de génération ACW générer une déclaration de méthode pour substituer la méthode. Le ACW qui en résulte contient le code suivant :

```csharp
public class ManagedAdder extends mono.android.test.Adder {
    static final String __md_methods;
    static {
        __md_methods = "n_add:(II)I:GetAddHandler\n" +
            "";
        mono.android.Runtime.register (...);
    }
    @Override
    public int add (int p0, int p1) {
        return n_add (p0, p1);
    }
    private native int n_add (int p0, int p1);
    // ...
}
```

Notez qu’un `@Override` méthode est déclarée, qui délègue à un `n_`-méthode portant le même nom de préfixe. Vérifiez que lorsque le code Java appelle `ManagedAdder.add`, `ManagedAdder.n_add` sera appelée, ce qui permettra la substitution de C# `ManagedAdder.Add` méthode à exécuter.

Par conséquent, la question la plus importante : comment est `ManagedAdder.n_add` raccordé à `ManagedAdder.Add`?

Java `native` méthodes sont inscrits avec le runtime Java (le runtime Android) via le [JNI RegisterNatives fonction](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp17734).
`RegisterNatives` prend un tableau de structures contenant le nom de la méthode Java, la Signature de Type JNI et un pointeur de fonction à appeler ce qui suit [JNI convention d’appel](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/design.html#wp715).
Le pointeur de fonction doit être une fonction qui accepte deux arguments de pointeur suivis par les paramètres de méthode. Java `ManagedAdder.n_add` méthode doit être implémentée via une fonction qui a le prototype C suivant :

```csharp
int FunctionName(JNIEnv *env, jobject this, int a, int b)
```

Xamarin.Android n’expose pas un `RegisterNatives` (méthode). Au lieu de cela, le ACW et le MCW fournissent les informations nécessaires pour appeler `RegisterNatives`: le ACW contient le nom de méthode et la signature de type JNI, la seule chose qui manque est un pointeur de fonction pour raccorder.

C’est là le *méthode de connecteur* arrive. La troisième `[Register]` paramètre de l’attribut personnalisé est le nom d’une méthode définie dans le type inscrit ou une classe de base du type inscrit qui n’accepte aucun paramètre et retourne un `System.Delegate`. Retourné `System.Delegate` à son tour fait référence à une méthode qui a la signature de fonction JNI correcte. Enfin, le délégué retourné par la méthode de connecteur *doit* à une racine afin que le garbage collector ne collecte pas, comme le délégué est fourni à Java.

```csharp
#pragma warning disable 0169
static Delegate cb_add;
// This method must match the third parameter of the [Register]
// custom attribute, must be static, must return System.Delegate,
// and must accept no parameters.
static Delegate GetAddHandler ()
{
    if (cb_add == null)
        cb_add = JNINativeWrapper.CreateDelegate ((Func<IntPtr, IntPtr, int, int, int>) n_Add);
    return cb_add;
}
// This method is registered with JNI.
static int n_Add (IntPtr jnienv, IntPtr lrefThis, int a, int b)
{
    Adder __this = Java.Lang.Object.GetObject<Adder>(lrefThis, JniHandleOwnership.DoNotTransfer);
    return __this.Add (a, b);
}
#pragma warning restore 0169
```

Le `GetAddHandler` méthode crée un `Func<IntPtr, IntPtr, int, int,
int>` délégué qui fait référence à la `n_Add` (méthode), puis appelle [JNINativeWrapper.CreateDelegate](https://developer.xamarin.com/api/member/Android.Runtime.JNINativeWrapper.CreateDelegate/).
`JNINativeWrapper.CreateDelegate` encapsule la méthode fournie dans un bloc try/catch, afin que les exceptions non gérées sont gérées et entraînent le déclenchement du [AndroidEvent.UnhandledExceptionRaiser](https://developer.xamarin.com/api/event/Android.Runtime.AndroidEnvironment.UnhandledExceptionRaiser/) événement. Le délégué résultant est stocké dans la méthode statique `cb_add` variable afin que le garbage collector ne libère pas le délégué.

Enfin, le `n_Add` méthode est chargée pour le marshaling des paramètres JNI pour les types managés correspondants, puis appelez de délégation de la méthode.

Remarque : Toujours utiliser `JniHandleOwnership.DoNotTransfer` lors de l’obtention d’un MCW sur une instance de Java. En les traitant comme une référence locale (et par conséquent, l’appel `JNIEnv.DeleteLocalRef`) s’arrête géré -&gt; Java -&gt; des transitions de pile.



### <a name="complete-adder-binding"></a>Adder liaison complète

L’ensemble gérés de liaison pour le `mono.android.tests.Adder` est de type :

```csharp
[Register ("mono/android/test/Adder", DoNotGenerateAcw=true)]
public class Adder : Java.Lang.Object {

    static IntPtr class_ref = JNIEnv.FindClass ("mono/android/test/Adder");

    public Adder ()
    {
    }

    public Adder (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }

    protected override Type ThresholdType {
        get {return typeof (Adder);}
    }

    protected override IntPtr ThresholdClass {
        get {return class_ref;}
    }

#region Add
    static IntPtr id_add;

    [Register ("add", "(II)I", "GetAddHandler")]
    public virtual int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        if (GetType () == ThresholdType)
            return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
        return JNIEnv.CallNonvirtualIntMethod (Handle, ThresholdClass, id_add, new JValue (a), new JValue (b));
    }

#pragma warning disable 0169
    static Delegate cb_add;
    static Delegate GetAddHandler ()
    {
        if (cb_add == null)
            cb_add = JNINativeWrapper.CreateDelegate ((Func<IntPtr, IntPtr, int, int, int>) n_Add);
        return cb_add;
    }

    static int n_Add (IntPtr jnienv, IntPtr lrefThis, int a, int b)
    {
        Adder __this = Java.Lang.Object.GetObject<Adder>(lrefThis, JniHandleOwnership.DoNotTransfer);
        return __this.Add (a, b);
    }
#pragma warning restore 0169
#endregion
}
```



### <a name="restrictions"></a>Restrictions

Lors de l’écriture d’un type qui correspond aux critères suivants :

1.  Sous-classes `Java.Lang.Object`

1.  A un `[Register]` attribut personnalisé

1.  `RegisterAttribute.DoNotGenerateAcw` a la valeur `true`.


Puis pour le type de l’interaction GC *ne doit pas* ont tous les champs qui peuvent faire référence à un `Java.Lang.Object` ou `Java.Lang.Object` sous-classe lors de l’exécution. Par exemple, les champs de type `System.Object` et n’importe quel type d’interface ne sont pas autorisées. Les types qui ne peut pas faire référence à `Java.Lang.Object` instances sont autorisées, telles que `System.String` et `List<int>`. Cette restriction consiste à empêcher la collecte d’exécution prématuré d’un objet par le garbage collector.

Si le type doit contenir un champ d’instance qui peut faire référence à un `Java.Lang.Object` de l’instance, puis le type de champ doit être `System.WeakReference` ou `GCHandle`.



## <a name="binding-abstract-methods"></a>Liaison de méthodes abstraites

Liaison `abstract` méthodes est en grande partie identique à la liaison de méthodes virtuelles. Il existe seulement deux différences :

1.  La méthode abstraite est abstraite. Il conserve toujours le `[Register]` attribut et l’inscription de la méthode associée, la méthode de liaison est simplement déplacée vers le `Invoker` type.

1.  Non - `abstract` `Invoker` type est créé qui sous-classe du type abstrait. Le `Invoker` type doit substituer abstraite de toutes les méthodes déclarées dans la classe de base, et l’implémentation substituée est l’implémentation de méthode de liaison, bien que le cas de dispatch non virtuel peut être ignoré.


Par exemple, supposons que la méthode ci-dessus `mono.android.test.Adder.add` méthode était `abstract`. Le C# liaison pourrait être modifié afin que `Adder.Add` ont été abstract et un nouveau `AdderInvoker` type serait défini qui implémenté `Adder.Add`:

```csharp
partial class Adder {
    [Register ("add", "(II)I", "GetAddHandler")]
    public abstract int Add (int a, int b);

    // The Method Registration machinery is identical to the
    // virtual method case...
}

partial class AdderInvoker : Adder {
    public AdderInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }

    static IntPtr id_add;
    public override int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
    }
}
```

Le `Invoker` type est nécessaire uniquement lors de l’obtention des références JNI aux instances créées en Java.


## <a name="binding-interfaces"></a>Interfaces de liaison

Interfaces de liaison sont conceptuellement semblable à la liaison des classes contenant des méthodes virtuelles, mais la plupart des aspects spécifiques diffèrent dans des moyens subtiles (et pas si subtiles). Considérez les éléments suivants [déclaration d’interface Java](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/Adder.java#L14):

```csharp
public interface Progress {
    void onAdd(int[] values, int currentIndex, int currentSum);
}
```

Liaisons d’interface ont deux parties : le C# définition d’interface et une définition de demandeur pour l’interface.



### <a name="interface-definition"></a>Définition d’interface

Le C# définition d’interface doit remplir les conditions suivantes :

-   La définition d’interface doit avoir un `[Register]` attribut personnalisé.

-   La définition d’interface doit étendre la `IJavaObject interface`.
    Cela empêchera ACWs héritant de l’interface Java.

-   Chaque méthode d’interface doit contenir un `[Register]` attribut spécifiant le nom de méthode Java correspondant, la signature JNI et la méthode de connecteur.

-   La méthode de connecteur doit également spécifier le type de la méthode de connecteur peut se trouver sur.

Lors de la liaison `abstract` et `virtual` méthodes, la méthode de connecteur seraient rechercher dans la hiérarchie d’héritage du type en cours d’inscription. Les interfaces ne peuvent avoir aucune méthode contenant des organismes de normalisation, afin que cela ne fonctionne pas, par conséquent, l’exigence que spécifier un type qui indique où se trouve la méthode de connecteur. Le type est spécifié dans la chaîne de méthode de connecteur, après un signe deux-points `':'`, et doit être le nom de type qualifié d’assembly du type contenant le demandeur.

Déclarations de méthode d’interface sont une traduction de la méthode Java correspondant à l’aide *compatible* types. Pour les types de builtin Java, les types compatibles sont correspondant C# types, par exemple Java `int` est C# `int`. Pour les types référence, le type compatible est un type qui peut fournir un handle JNI du type Java approprié.

Les membres d’interface ne seront pas appelés directement par Java &ndash; appel sera soit relayé par le type de demandeur &ndash; donc une certaine quantité de flexibilité est autorisée.

L’interface de progression de Java peut être [déclaré dans C# comme](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L83):

```csharp
[Register ("mono/android/test/Adder$Progress", DoNotGenerateAcw=true)]
public interface IAdderProgress : IJavaObject {
    [Register ("onAdd", "([III)V",
            "GetOnAddHandler:Mono.Samples.SanityTests.IAdderProgressInvoker, SanityTests, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null")]
    void OnAdd (JavaArray<int> values, int currentIndex, int currentSum);
}
```

Notez que dans l’exemple ci-dessus que nous mappons le Java `int[]` paramètre à un [JavaArray&lt;int&gt;](https://developer.xamarin.com/api/type/Android.Runtime.JavaArray%601/).
Cela n’est pas nécessaire : nous avons l’avez liée à un C# `int[]`, ou un `IList<int>`, ou autre chose entièrement. Le type est choisi, le `Invoker` doit être en mesure de traduire en un Java `int[]` type pour l’appel.


### <a name="invoker-definition"></a>Définition du demandeur

Le `Invoker` définition de type doit hériter `Java.Lang.Object`, implémentez l’interface appropriée et de fournir toutes les méthodes de connexion référencées dans la définition d’interface. Il existe une solution qui suggère plusieurs qui diffère d’une liaison de la classe : le `class_ref` ID de champ et de méthode doivent être membres d’instance, les membres non statiques.

La raison de préférer les membres d’instance a trait aux `JNIEnv.GetMethodID` comportement dans le runtime Android. (Il peut s’agir de comportement de Java ainsi ; il n’a pas été testé). `JNIEnv.GetMethodID` retourne la valeur null lorsque vous recherchez une méthode qui provient d’une interface implémentée et pas l’interface déclarée. Prendre en compte la [java.util.SortedMap&lt;K, V&gt; ](https://developer.android.com/reference/java/util/SortedMap.html) interface Java, qui implémente le [java.util.Map&lt;K, V&gt; ](https://developer.android.com/reference/java/util/Map.html) interface. Carte fournit un [effacer](https://developer.android.com/reference/java/util/Map.html#clear()) (méthode), par conséquent apparemment raisonnable `Invoker` définition pour SortedMap serait :

```csharp
// Fails at runtime. DO NOT FOLLOW
partial class ISortedMapInvoker : Java.Lang.Object, ISortedMap {
    static IntPtr class_ref = JNIEnv.FindClass ("java/util/SortedMap");
    static IntPtr id_clear;
    public void Clear()
    {
        if (id_clear == IntPtr.Zero)
            id_clear = JNIEnv.GetMethodID(class_ref, "clear", "()V");
        JNIEnv.CallVoidMethod(Handle, id_clear);
    }
     // ...
}
```

La méthode ci-dessus échoue, car `JNIEnv.GetMethodID` retournera `null` lorsque vous recherchez le `Map.clear` méthode via la `SortedMap` instance de classe.

Il existe deux solutions à cela : effectuer le suivi d’une interface qui provient de chaque méthode et ont un `class_ref` pour chaque interface, ou conservez le tout en tant que membres d’instance et effectuer la recherche de méthode sur le type de classe la plus dérivée, pas le type d’interface. Ce dernier s’effectue **Mono.Android.dll**.

La définition du demandeur a six sections : le constructeur, le `Dispose` (méthode), le `ThresholdType` et `ThresholdClass` membres, le `GetObject` (méthode), implémentation de méthode d’interface et l’implémentation de méthode du connecteur.



#### <a name="constructor"></a>Constructeur

Le constructeur doit rechercher la classe d’exécution de l’instance invoquée et stocker la classe d’exécution dans l’instance `class_ref` champ :

```csharp
partial class IAdderProgressInvoker {
    IntPtr class_ref;
    public IAdderProgressInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass (Handle);
        class_ref   = JNIEnv.NewGlobalRef (lref);
        JNIEnv.DeleteLocalRef (lref);
    }
}
```

Remarque : Le `Handle` propriété doit être utilisée dans le corps du constructeur et non le `handle` paramètre, comme sur Android v4.0 le `handle` paramètre peut être non valide, une fois que le constructeur de base termine son exécution.


#### <a name="dispose-method"></a>Dispose, méthode

Le `Dispose` méthode doit libérer la référence globale allouée dans le constructeur :

```csharp
partial class IAdderProgressInvoker {
    protected override void Dispose (bool disposing)
    {
        if (this.class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef (this.class_ref);
        this.class_ref = IntPtr.Zero;
        base.Dispose (disposing);
    }
}
```


#### <a name="thresholdtype-and-thresholdclass"></a>ThresholdType et ThresholdClass

Le `ThresholdType` et `ThresholdClass` membres sont identiques à ce qui se trouve dans une liaison de la classe :

```csharp
partial class IAdderProgressInvoker {
    protected override Type ThresholdType {
        get {
            return typeof (IAdderProgressInvoker);
        }
    }
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```


#### <a name="getobject-method"></a>GetObject, méthode

Statique `GetObject` méthode est requise pour prendre en charge [Extensions.JavaCast&lt;T&gt;()](https://developer.xamarin.com/api/member/Android.Runtime.Extensions.JavaCast%7BTResult%7D/p/Android.Runtime.IJavaObject/):

```csharp
partial class IAdderProgressInvoker {
    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }
}
```


#### <a name="interface-methods"></a>Méthodes d’interface

Chaque méthode de l’interface doit avoir une implémentation, qui appelle la méthode Java correspondante via JNI :

```csharp
partial class IAdderProgressInvoker {
    IntPtr id_onAdd;
    public void OnAdd (JavaArray<int> values, int currentIndex, int currentSum)
    {
        if (id_onAdd == IntPtr.Zero)
            id_onAdd = JNIEnv.GetMethodID (class_ref, "onAdd", "([III)V");
        JNIEnv.CallVoidMethod (Handle, id_onAdd, new JValue (JNIEnv.ToJniHandle (values)), new JValue (currentIndex), new JValue (currentSum));
    }
}
```



#### <a name="connector-methods"></a>Méthodes de connecteur

Les méthodes de connecteur et l’infrastructure de prise en charge sont chargés de marshaler les paramètres JNI appropriés C# types. Java `int[]` paramètre sera transmis en tant qu’un JNI `jintArray`, qui est un `IntPtr` dans C#. Le `IntPtr` doivent être marshalées vers un `JavaArray<int>` pour prendre en charge l’appel de la C# interface :

```csharp
partial class IAdderProgressInvoker {
    static Delegate cb_onAdd;
    static Delegate GetOnAddHandler ()
    {
        if (cb_onAdd == null)
            cb_onAdd = JNINativeWrapper.CreateDelegate ((Action<IntPtr, IntPtr, IntPtr, int, int>) n_OnAdd);
        return cb_onAdd;
    }

    static void n_OnAdd (IntPtr jnienv, IntPtr lrefThis, IntPtr values, int currentIndex, int currentSum)
    {
        IAdderProgress __this = Java.Lang.Object.GetObject<IAdderProgress>(lrefThis, JniHandleOwnership.DoNotTransfer);
        using (var _values = new JavaArray<int>(values, JniHandleOwnership.DoNotTransfer)) {
            __this.OnAdd (_values, currentIndex, currentSum);
        }
    }
}
```

Si `int[]` serait vaut mieux utiliser `JavaList<int>`, puis [JNIEnv.GetArray()](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetArray/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership%2cSystem.Type)) peut être utilisé à la place :

```csharp
int[] _values = (int[]) JNIEnv.GetArray(values, JniHandleOwnership.DoNotTransfer, typeof (int));
```

Notez, cependant, que `JNIEnv.GetArray` copie l’intégralité du tableau entre les machines virtuelles, afin de grands tableaux cela pourrait entraîner un grand nombre de sollicitation de GC.


### <a name="complete-invoker-definition"></a>Terminer la définition du demandeur

Le [terminer IAdderProgressInvoker définition](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L88):

```csharp
class IAdderProgressInvoker : Java.Lang.Object, IAdderProgress {

    IntPtr class_ref;

    public IAdderProgressInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass (Handle);
        class_ref = JNIEnv.NewGlobalRef (lref);
        JNIEnv.DeleteLocalRef (lref);
    }

    protected override void Dispose (bool disposing)
    {
        if (this.class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef (this.class_ref);
        this.class_ref = IntPtr.Zero;
        base.Dispose (disposing);
    }

    protected override Type ThresholdType {
        get {return typeof (IAdderProgressInvoker);}
    }

    protected override IntPtr ThresholdClass {
        get {return class_ref;}
    }

    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }

#region OnAdd
    IntPtr id_onAdd;
    public void OnAdd (JavaArray<int> values, int currentIndex, int currentSum)
    {
        if (id_onAdd == IntPtr.Zero)
            id_onAdd = JNIEnv.GetMethodID (class_ref, "onAdd",
                    "([III)V");
        JNIEnv.CallVoidMethod (Handle, id_onAdd,
                new JValue (JNIEnv.ToJniHandle (values)),
                new JValue (currentIndex),
new JValue (currentSum));
    }

#pragma warning disable 0169
    static Delegate cb_onAdd;
    static Delegate GetOnAddHandler ()
    {
        if (cb_onAdd == null)
            cb_onAdd = JNINativeWrapper.CreateDelegate ((Action<IntPtr, IntPtr, IntPtr, int, int>) n_OnAdd);
        return cb_onAdd;
    }

    static void n_OnAdd (IntPtr jnienv, IntPtr lrefThis, IntPtr values, int currentIndex, int currentSum)
    {
        IAdderProgress __this = Java.Lang.Object.GetObject<IAdderProgress>(lrefThis, JniHandleOwnership.DoNotTransfer);
        using (var _values = new JavaArray<int>(values, JniHandleOwnership.DoNotTransfer)) {
            __this.OnAdd (_values, currentIndex, currentSum);
        }
    }
#pragma warning restore 0169
#endregion
}
```



## <a name="jni-object-references"></a>Références d’objet JNI

De nombreuses méthodes JNIEnv renvoient *JNI* *références à des objets*, qui sont similaires à `GCHandle`s. JNI fournit trois types différents de références d’objet : références locales, les références globales et les références globales faibles. Les trois sont représentés en tant que `System.IntPtr`, *mais* (conformément à la section Types de fonction JNI) pas toutes `IntPtr`retournés à partir `JNIEnv` méthodes sont des références. Par exemple, [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/) retourne un `IntPtr`, mais il ne retourne pas une référence d’objet, elle renvoie un `jmethodID`. Consultez le [documentation sur la fonction JNI](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html) pour plus d’informations.

Références locales sont créés par *la plupart des* -création d’une référence de méthodes.
Android autorise uniquement un nombre limité de références locales d’exister à un moment donné, généralement à 512. Références locales peuvent être supprimés [JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/).
Contrairement à JNI, pas toutes référencent des méthodes JNIEnv les références d’objet de retour retournent des références locales ; [JNIEnv.FindClass](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/) retourne un *global* référence. Il est fortement recommandé de supprimer des références locales aussi rapidement que possible, éventuellement en construisant un [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) autour de l’objet et en spécifiant `JniHandleOwnership.TransferLocalRef` à la [Java.Lang.Object (IntPtr gérer, de transfert de JniHandleOwnership)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) constructeur.

Les références globales sont créées par [JNIEnv.NewGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewGlobalRef/) et [JNIEnv.FindClass](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/).
Ils peuvent être détruits avec [JNIEnv.DeleteGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteGlobalRef/).
Émulateurs ont une limite de 2 000 références globales en suspens, tandis que les périphériques matériels ont une limite d’environ 52 000 références globales.

Les références globales faibles sont uniquement disponibles sur Android version 2.2 (Froyo) et versions ultérieures. Les références faibles globales peuvent être supprimées avec [JNIEnv.DeleteWeakGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteWeakGlobalRef/(System.IntPtr)).


### <a name="dealing-with-jni-local-references"></a>Utilisation de références Local JNI

Le [JNIEnv.GetObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetObjectField/), [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/), [JNIEnv.CallObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallObjectMethod/), [JNIEnv.CallNonvirtualObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualObjectMethod/)et [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) méthodes retournent un `IntPtr` qui contient une référence locale de JNI à un objet Java, ou `IntPtr.Zero` si Java retourné `null`. En raison du nombre limité de références locales qui peuvent être en cours à une fois (512 entrées), il est souhaitable de s’assurer que les références sont supprimés en temps voulu. Il existe trois façons références locales peuvent être traitées : explicitement les supprimer, création d’un `Java.Lang.Object` instance pour contenir les et à l’aide `Java.Lang.Object.GetObject<T>()` pour créer un wrapper managé callable autour d’elles.



### <a name="explicitly-deleting-local-references"></a>Supprimer explicitement des références locales

[JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/) est utilisée pour supprimer des références locales. Une fois que la référence locale a été supprimée, il ne peut pas être plus utilisé, afin d’être vigilant pour vous assurer que `JNIEnv.DeleteLocalRef` est la dernière opération effectuée avec la référence locale.

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
try {
    // Do something with `lref`
}
finally {
    JNIEnv.DeleteLocalRef (lref);
}
```



### <a name="wrapping-with-javalangobject"></a>Retour à la ligne avec Java.Lang.Object

`Java.Lang.Object` fournit un [Java.Lang.Object (handle IntPtr, JniHandleOwnership transfert)](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) constructeur qui peut être utilisée pour encapsuler une référence JNI existante. Le [JniHandleOwnership](https://developer.xamarin.com/api/type/Android.Runtime.JniHandleOwnership/) paramètre détermine comment la `IntPtr` paramètre doit être traité :

-   [JniHandleOwnership.DoNotTransfer](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.DoNotTransfer/) &ndash; créé `Java.Lang.Object` instance créera une nouvelle référence globale à partir de la `handle` paramètre, et `handle` est inchangé.
    L’appelant est chargé de libérer `handle` , si nécessaire.

-   [JniHandleOwnership.TransferLocalRef](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.TransferLocalRef/) &ndash; créé `Java.Lang.Object` instance créera une nouvelle référence globale à partir de la `handle` paramètre, et `handle` est supprimé avec [JNIEnv.DeleteLocalRef ](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/) . L’appelant ne doit pas libérer `handle` et ne doivent pas utiliser `handle` une fois que le constructeur termine son exécution.

-   [JniHandleOwnership.TransferGlobalRef](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.TransferLocalRef/) &ndash; créé `Java.Lang.Object` instance prendra possession du `handle` paramètre. L’appelant ne doit pas libérer `handle` .


Étant donné que les méthodes d’appel de méthode JNI retournent refs locales, `JniHandleOwnership.TransferLocalRef` doit normalement être utilisé :

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef);
```

La référence globale créée n’est pas libérée jusqu'à ce que le `Java.Lang.Object` instance est nettoyé. Si vous êtes en mesure, en cours de suppression de l’instance libère la référence globale, en accélérant la garbage collection :

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
using (var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef)) {
    // use value ...
}
```



### <a name="using-javalangobjectgetobjectlttgt"></a>Using Java.Lang.Object.GetObject&lt;T&gt;()

`Java.Lang.Object` fournit un [Java.Lang.Object.GetObject&lt;T&gt;(handle IntPtr, JniHandleOwnership transfert)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) méthode qui peut être utilisée pour créer un wrapper CCW managé du type spécifié.

Le type `T` doit remplir les conditions suivantes :

1.  `T` doit être un type référence.

1.  `T` doit implémenter le `IJavaObject` interface.

1.  Si `T` n’est pas une classe ou interface abstraite, puis `T` doivent fournir un constructeur avec les types de paramètre `(IntPtr,
    JniHandleOwnership)` .

1.  Si `T` est une classe abstraite ou une interface, il *doit* être un *demandeur* disponibles pour `T` . Un demandeur est un type non abstraite qui hérite `T` ou implémente `T` , et a le même nom que `T` avec un suffixe de demandeur. Par exemple, si T est l’interface `Java.Lang.IRunnable` , puis le type `Java.Lang.IRunnableInvoker` doit exister et doit contenir le texte requis `(IntPtr,
    JniHandleOwnership)` constructeur.


Étant donné que les méthodes d’appel de méthode JNI retournent refs locales, `JniHandleOwnership.TransferLocalRef` doit normalement être utilisé :

```csharp
IntPtr lrefString = JNIEnv.CallObjectMethod(instance, methodID);
Java.Lang.String value = Java.Lang.Object.GetObject<Java.Lang.String>( lrefString, JniHandleOwnership.TransferLocalRef);
```

<a name="_Looking_up_Java_Types" />

## <a name="looking-up-java-types"></a>Recherche de Types Java

Pour rechercher un champ ou une méthode dans JNI, le type déclarant pour le champ ou la méthode doit être consulté en premier. Le [Android.Runtime.JNIEnv.FindClass(string)](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/(System.String)) méthode est utilisée pour rechercher des types Java. Le paramètre de chaîne est la *simplifié de référence de type* ou *référence de type complet* pour le type Java. Consultez le [section Références de Type JNI](#_JNI_Type_References) pour plus d’informations sur les références de type simplifié et complet.

Remarque : Contrairement à tous les autres `JNIEnv` méthode qui retourne des instances d’objet, `FindClass` retourne une référence globale, pas une référence locale.

<a name="_Instance_Fields" />

## <a name="instance-fields"></a>Champs d’instance

Les champs sont manipulées via *ID de champ*. ID de champ sont obtenues via [JNIEnv.GetFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFieldID/), ce qui nécessite la classe que le champ est défini dans le nom du champ et le [Signature de Type JNI](#JNI_Type_Signatures) du champ.

ID de champ n’êtes pas obligé d’être libéré et sont valides tant que le type Java correspondant est chargé. (Android ne prend pas en charge le déchargement de classe.)

Il existe deux ensembles de méthodes de manipulation des champs d’instance : une pour lire les champs d’instance et l’autre pour l’écriture des champs d’instance. Tous les ensembles de méthodes nécessitent un ID de champ lire ou écrire la valeur du champ.


### <a name="reading-instance-field-values"></a>Valeurs de champ d’Instance de lecture

L’ensemble de méthodes pour lire les valeurs de champ d’instance suit le modèle d’affectation de noms :

```csharp
* JNIEnv.Get*Field(IntPtr instance, IntPtr fieldID);
```
où `*` est le type du champ :

-   [JNIEnv.GetObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetObjectField/) &ndash; lire la valeur de n’importe quel champ d’instance qui n’est pas un type intégré, tel que `java.lang.Object` , tableaux et les types d’interface. La valeur retournée est une référence locale de JNI.

-   [JNIEnv.GetBooleanField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetBooleanField/) &ndash; lire la valeur de `bool` champs d’instance.

-   [JNIEnv.GetByteField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetByteField/) &ndash; lire la valeur de `sbyte` champs d’instance.

-   [JNIEnv.GetCharField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetCharField/) &ndash; lire la valeur de `char` champs d’instance.

-   [JNIEnv.GetShortField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetShortField/) &ndash; lire la valeur de `short` champs d’instance.

-   [JNIEnv.GetIntField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetIntField/) &ndash; lire la valeur de `int` champs d’instance.

-   [JNIEnv.GetLongField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetLongField/) &ndash; lire la valeur de `long` champs d’instance.

-   [JNIEnv.GetFloatField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFloatField/) &ndash; lire la valeur de `float` champs d’instance.

-   [JNIEnv.GetDoubleField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetDoubleField/) &ndash; lire la valeur de `double` champs d’instance.





### <a name="writing-instance-field-values"></a>Écriture des valeurs de champ Instance

L’ensemble de méthodes pour écrire des valeurs de champ d’instance suit le modèle d’affectation de noms :

```csharp
JNIEnv.SetField(IntPtr instance, IntPtr fieldID, Type value);
```

où *Type* est le type du champ :

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.IntPtr)) &ndash; écrire la valeur d’un champ qui n’est pas un type intégré, tel que `java.lang.Object` , tableaux et les types d’interface. Le `IntPtr` valeur peut être une référence locale de JNI, la référence globale de JNI, la référence faible globale de JNI, ou `IntPtr.Zero` (pour `null` ).

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Boolean)) &ndash; écrire la valeur de `bool` champs d’instance.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.SByte)) &ndash; écrire la valeur de `sbyte` champs d’instance.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Char)) &ndash; écrire la valeur de `char` champs d’instance.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int16)) &ndash; écrire la valeur de `short` champs d’instance.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int32)) &ndash; écrire la valeur de `int` champs d’instance.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int64)) &ndash; écrire la valeur de `long` champs d’instance.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Single)) &ndash; écrire la valeur de `float` champs d’instance.

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Double)) &ndash; écrire la valeur de `double` champs d’instance.


<a name="_Static_Fields" />

## <a name="static-fields"></a>Champs statiques

Les champs statiques sont manipulées via *ID de champ*. ID de champ sont obtenues via [JNIEnv.GetStaticFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticFieldID/), ce qui nécessite la classe que le champ est défini dans le nom du champ et le [Signature de Type JNI](#JNI_Type_Signatures) du champ.

ID de champ n’êtes pas obligé d’être libéré et sont valides tant que le type Java correspondant est chargé. (Android ne prend pas en charge le déchargement de classe.)

Il existe deux ensembles de méthodes pour manipuler des champs statiques : une pour lire les champs d’instance et l’autre pour l’écriture des champs d’instance. Tous les ensembles de méthodes nécessitent un ID de champ lire ou écrire la valeur du champ.


### <a name="reading-static-field-values"></a>Lire les valeurs de champ statique

L’ensemble des méthodes pour lire les valeurs de champ static suit le modèle d’affectation de noms :

```csharp
* JNIEnv.GetStatic*Field(IntPtr class, IntPtr fieldID);
```

où `*` est le type du champ :

-   [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/) &ndash; lire la valeur d’un champ statique qui n’est pas un type intégré, tel que `java.lang.Object` , tableaux et les types d’interface. La valeur retournée est une référence locale de JNI.

-   [JNIEnv.GetStaticBooleanField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticBooleanField/) &ndash; lire la valeur de `bool` champs statiques.

-   [JNIEnv.GetStaticByteField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticByteField/) &ndash; lire la valeur de `sbyte` champs statiques.

-   [JNIEnv.GetStaticCharField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticCharField/) &ndash; lire la valeur de `char` champs statiques.

-   [JNIEnv.GetStaticShortField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticShortField/) &ndash; lire la valeur de `short` champs statiques.

-   [JNIEnv.GetStaticLongField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticLongField/) &ndash; lire la valeur de `long` champs statiques.

-   [JNIEnv.GetStaticFloatField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticFloatField/) &ndash; lire la valeur de `float` champs statiques.

-   [JNIEnv.GetStaticDoubleField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticDoubleField/) &ndash; lire la valeur de `double` champs statiques.



### <a name="writing-static-field-values"></a>Écriture des valeurs de champ statique

L’ensemble des méthodes pour écrire des valeurs de champ static suit le modèle d’affectation de noms :

```csharp
JNIEnv.SetStaticField(IntPtr class, IntPtr fieldID, Type value);
```

où *Type* est le type du champ :

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.IntPtr)) &ndash; écrire la valeur d’un champ statique qui n’est pas un type intégré, tel que `java.lang.Object` , tableaux et les types d’interface. Le `IntPtr` valeur peut être une référence locale de JNI, la référence globale de JNI, la référence faible globale de JNI, ou `IntPtr.Zero` (pour `null` ).

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Boolean)) &ndash; écrire la valeur de `bool` champs statiques.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.SByte)) &ndash; écrire la valeur de `sbyte` champs statiques.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Char)) &ndash; écrire la valeur de `char` champs statiques.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int16)) &ndash; écrire la valeur de `short` champs statiques.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int32)) &ndash; écrire la valeur de `int` champs statiques.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int64)) &ndash; écrire la valeur de `long` champs statiques.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Single)) &ndash; écrire la valeur de `float` champs statiques.

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Double)) &ndash; écrire la valeur de `double` champs statiques.


<a name="_Instance_Methods" />

## <a name="instance-methods"></a>Méthodes d’instance

Méthodes d’instance sont appelés par l’intermédiaire *méthode ID*. ID de méthode sont obtenus par le biais de [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/), ce qui nécessite que le type que la méthode est définie dans le nom de la méthode et le [Signature de Type JNI](#JNI_Type_Signatures) de la méthode.

ID de la méthode n’êtes pas obligé d’être libéré et sont valides tant que le type Java correspondant est chargé. (Android ne prend pas en charge le déchargement de classe.)

Il existe deux ensembles de méthodes pour appeler les méthodes : une pour l’appel de méthodes pratiquement et une pour appeler des méthodes non virtuelle. Les deux ensembles de méthodes nécessitent un ID de la méthode à appeler la méthode et appel non virtuel requiert également que vous spécifiez quelle implémentation de classe doit être appelée.

Méthodes d’interface peuvent uniquement être recherchés dans le type de déclaration ; les méthodes qui proviennent d’interfaces étendues/héritées ne peuvent pas être recherchées. Consultez les Interfaces de liaison ultérieure / implémentation du demandeur de section pour plus d’informations.

N’importe quelle méthode déclarée dans la classe ou de toute classe de base ou l’interface implémentée peut être recherché.


### <a name="virtual-method-invocation"></a>Appel de méthode virtuelle

L’ensemble de méthodes pour appeler des méthodes suit quasiment le modèle d’affectation de noms :

```csharp
* JNIEnv.Call*Method( IntPtr instance, IntPtr methodID, params JValue[] args );
```

où `*` est le type de retour de la méthode.

-   [JNIEnv.CallObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallObjectMethod/) &ndash; appeler une méthode qui retourne un type non intégré, tel que `java.lang.Object` , tableaux et les interfaces. La valeur retournée est une référence locale de JNI.

-   [JNIEnv.CallBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallBooleanMethod/) &ndash; appeler une méthode qui retourne un `bool` valeur.

-   [JNIEnv.CallByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallByteMethod/) &ndash; appeler une méthode qui retourne un `sbyte` valeur.

-   [JNIEnv.CallCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallCharMethod/) &ndash; appeler une méthode qui retourne un `char` valeur.

-   [JNIEnv.CallShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallShortMethod/) &ndash; appeler une méthode qui retourne un `short` valeur.

-   [JNIEnv.CallLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallLongMethod/) &ndash; appeler une méthode qui retourne un `long` valeur.

-   [JNIEnv.CallFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallFloatMethod/) &ndash; appeler une méthode qui retourne un `float` valeur.

-   [JNIEnv.CallDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallDoubleMethod/) &ndash; appeler une méthode qui retourne un `double` valeur.



### <a name="non-virtual-method-invocation"></a>Appel de méthode non virtuelle

L’ensemble de méthodes pour appeler des méthodes suit non pratiquement le modèle d’affectation de noms :

```csharp
* JNIEnv.CallNonvirtual*Method( IntPtr instance, IntPtr class, IntPtr methodID, params JValue[] args );
```

où `*` est le type de retour de la méthode. Appel de méthode non virtuelle est généralement utilisé pour appeler la méthode de base d’une méthode virtuelle.

-   [JNIEnv.CallNonvirtualObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualObjectMethod/) &ndash; Non pratiquement appeler une méthode qui retourne un type non intégré, tel que `java.lang.Object` , tableaux et les interfaces. La valeur retournée est une référence locale de JNI.

-   [JNIEnv.CallNonvirtualBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualBooleanMethod/) &ndash; Non pratiquement appeler une méthode qui retourne un `bool` valeur.

-   [JNIEnv.CallNonvirtualByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualByteMethod/) &ndash; Non pratiquement appeler une méthode qui retourne un `sbyte` valeur.

-   [JNIEnv.CallNonvirtualCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualCharMethod/) &ndash; Non pratiquement appeler une méthode qui retourne un `char` valeur.

-   [JNIEnv.CallNonvirtualShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualShortMethod/) &ndash; Non pratiquement appeler une méthode qui retourne un `short` valeur.

-   [JNIEnv.CallNonvirtualLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualLongMethod/) &ndash; Non pratiquement appeler une méthode qui retourne un `long` valeur.

-   [JNIEnv.CallNonvirtualFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualFloatMethod/) &ndash; Non pratiquement appeler une méthode qui retourne un `float` valeur.

-   [JNIEnv.CallNonvirtualDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualDoubleMethod/) &ndash; Non pratiquement appeler une méthode qui retourne un `double` valeur.


<a name="_Static_Methods" />

## <a name="static-methods"></a>Méthodes statiques

Méthodes statiques sont appelés par l’intermédiaire *méthode ID*. ID de méthode sont obtenus par le biais de [JNIEnv.GetStaticMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/), ce qui nécessite que le type que la méthode est définie dans le nom de la méthode et le [Signature de Type JNI](#JNI_Type_Signatures) de la méthode.

ID de la méthode n’êtes pas obligé d’être libéré et sont valides tant que le type Java correspondant est chargé. (Android ne prend pas en charge le déchargement de classe.)



### <a name="static-method-invocation"></a>Appel de méthode statique

L’ensemble de méthodes pour appeler des méthodes suit quasiment le modèle d’affectation de noms :

```csharp
* JNIEnv.CallStatic*Method( IntPtr class, IntPtr methodID, params JValue[] args );
```

où `*` est le type de retour de la méthode.

-   [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) &ndash; appeler une méthode statique qui retourne un type non intégré, tel que `java.lang.Object` , tableaux et les interfaces. La valeur retournée est une référence locale de JNI.

-   [JNIEnv.CallStaticBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticBooleanMethod/) &ndash; appeler une méthode statique qui retourne un `bool` valeur.

-   [JNIEnv.CallStaticByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticByteMethod/) &ndash; appeler une méthode statique qui retourne un `sbyte` valeur.

-   [JNIEnv.CallStaticCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticCharMethod/) &ndash; appeler une méthode statique qui retourne un `char` valeur.

-   [JNIEnv.CallStaticShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticShortMethod/) &ndash; appeler une méthode statique qui retourne un `short` valeur.

-   [JNIEnv.CallStaticLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallLongMethod/) &ndash; appeler une méthode statique qui retourne un `long` valeur.

-   [JNIEnv.CallStaticFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticFloatMethod/) &ndash; appeler une méthode statique qui retourne un `float` valeur.

-   [JNIEnv.CallStaticDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticDoubleMethod/) &ndash; appeler une méthode statique qui retourne un `double` valeur.


<a name="JNI_Type_Signatures" />

## <a name="jni-type-signatures"></a>Signatures de Type JNI

[Les Signatures de Type JNI](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/types.html#wp16432) sont [références de Type JNI](#_JNI_Type_References) (références de type cependant pas simplifié), sauf pour les méthodes. Avec les méthodes, la Signature de Type JNI est une parenthèse ouvrante `'('`, suivi par les références de type pour l’ensemble du paramètre types concaténées (avec aucune séparation des virgules ou tout autre élément), suivie d’une parenthèse fermante `')'`, suivi de la référence de type JNI du type de retour de méthode.

Par exemple, étant donné la méthode Java :

```java
long f(int n, String s, int[] array);
```

La signature de type JNI serait :

```csharp
(ILjava/lang/String;[I)J
```

En général, il est *fortement* recommandé d’utiliser le `javap` commande permet de déterminer les signatures JNI. Par exemple, la Signature de Type JNI de la [java.lang.Thread.State.valueOf(String)](https://developer.android.com/reference/java/lang/Thread.State.html#valueOf(java.lang.String)) méthode est « (Ljava/lang/String) ; Ljava/lang/Thread$ état ; », tandis que le JNI tapez la Signature de la [ java.lang.Thread.State.values](https://developer.android.com/reference/java/lang/Thread.State.html#values) méthode est « () [Ljava/lang/Thread$ état ; ». Surveiller les points-virgules de fin ; ces *sont* dans le cadre de la signature de type JNI.

<a name="_JNI_Type_References" />

## <a name="jni-type-references"></a>Références de Type JNI

Références de type JNI sont différents des références à des types Java. Vous ne pouvez pas utiliser des noms de types qualifiés complets Java comme `java.lang.String` JNI, vous devez plutôt utiliser les variantes de JNI `"java/lang/String"` ou `"Ljava/lang/String;"`, selon le contexte ; voir ci-dessous pour plus d’informations.
Il existe quatre types de références de type JNI :

-  **built-in**
-  **simplified**
-  **type**
-  **array**


### <a name="built-in-type-references"></a>Références de Type intégré

Les références de type intégré sont un caractère unique utilisé pour référencer des types valeur intégrés. Le mappage est comme suit :

-  `"B"` pour `sbyte` .
-  `"S"` pour `short` .
-  `"I"` pour `int` .
-  `"J"` pour `long` .
-  `"F"` pour `float` .
-  `"D"` pour `double` .
-  `"C"` pour `char` .
-  `"Z"` pour `bool` .
-  `"V"` pour `void` types de retour de méthode.


<a name="_Simplified_Type_References_1" />

### <a name="simplified-type-references"></a>Références de Type simplifiée

Références de type simplifié est utilisable uniquement dans [JNIEnv.FindClass(string)](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/(System.String)).
Il existe deux façons de dériver une référence de type simplifiée :

1.  À partir d’un nom qualifié complet de Java, remplacez chaque `'.'` dans le nom du package et avant le nom de type `'/'` et chaque `'.'` au sein d’un nom de type `'$'` .

1.  Lire la sortie de `'unzip -l android.jar | grep JavaName'` .


Une des deux entraîne le type Java [java.lang.Thread.State](https://developer.android.com/reference/java/lang/Thread.State.html) qui est mappé à la référence de type simplifiée `java/lang/Thread$State`.


### <a name="type-references"></a>Références de type

Une référence de type est une référence de type intégré ou une référence de type simplifiée avec un `'L'` préfixe et un `';'` suffixe. Pour le type Java [java.lang.String](https://developer.android.com/reference/java/lang/String.html), la référence de type simplifié est `"java/lang/String"`, tandis que la référence de type est `"Ljava/lang/String;"`.

Références de type sont utilisés avec des références de type tableau et avec des Signatures de JNI.

Une méthode supplémentaire pour obtenir une référence de type est en lisant la sortie de `'javap -s -classpath android.jar fully.qualified.Java.Name'`.
En fonction du type impliqué, vous pouvez utiliser une déclaration de constructeur ou le type pour déterminer le nom JNI de retour de méthode. Par exemple :

```shell
$ javap -classpath android.jar -s java.lang.Thread.State
Compiled from "Thread.java"
```

```java
public final class java.lang.Thread$State extends java.lang.Enum{
public static final java.lang.Thread$State NEW;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State RUNNABLE;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State BLOCKED;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State WAITING;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State TIMED_WAITING;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State TERMINATED;
  Signature: Ljava/lang/Thread$State;
public static java.lang.Thread$State[] values();
  Signature: ()[Ljava/lang/Thread$State;
public static java.lang.Thread$State valueOf(java.lang.String);
  Signature: (Ljava/lang/String;)Ljava/lang/Thread$State;
static {};
  Signature: ()V
}
```

`Thread.State` étant un type enum de Java, nous pouvons utiliser la Signature de la `valueOf` méthode afin de déterminer que la référence de type est Ljava/lang/Thread$ état ;.



### <a name="array-type-references"></a>Références de Type tableau

Références à des types tableau sont `'['` ajouté en préfixe une référence de type JNI.
Impossible d’utiliser des références de type simplifiée lors de la spécification des tableaux.

Par exemple, `int[]` est `"[I"`, `int[][]` est `"[[I"`, et `java.lang.Object[]` est `"[Ljava/lang/Object;"`.



## <a name="java-generics-and-type-erasure"></a>Java génériques et l’effacement de Type

*La plupart des* du temps, comme indiqué par le biais JNI, les génériques Java *n’existent pas*.
Il existe certaines « plis », mais ces soucis sont dans la façon dont Java interagit avec les génériques, pas avec comment JNI recherche et appelle les membres génériques.

Il n’existe aucune différence entre un type générique ou membre et un type non générique ou un membre lors de l’interaction via JNI. Par exemple, le type générique [java.lang.Class&lt;T&gt; ](https://developer.android.com/reference/java/lang/Class.html) est également le type générique « brut » `java.lang.Class`, qui ont toutes deux la même référence de type simplifiée, `"java/lang/Class"`.


## <a name="java-native-interface-support"></a>Prise en charge de l’Interface Native Java

[Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/) est wrapper managé pour l’Interface Native Jave (JNI). JNI fonctions sont déclarées dans le [Java Native Interface spécification](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html), bien que les méthodes ont été modifiés pour supprimer l’explicite `JNIEnv*` paramètre et `IntPtr` est utilisé au lieu de `jobject`, `jclass`, `jmethodID`, etc. Par exemple, considérez le [JNI NewObject fonction](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp4517):

```csharp
jobject NewObjectA(JNIEnv *env, jclass clazz, jmethodID methodID, jvalue *args);
```

Ces informations sont exposées en tant que le [JNIEnv.NewObject](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewObject/p/System.IntPtr/System.IntPtr/Android.Runtime.JValue[]/) méthode :

```csharp
public static IntPtr NewObject(IntPtr clazz, IntPtr jmethod, params JValue[] parms);
```

Conversion entre les deux appels est assez simple. En C, vous devez :

```c
jobject CreateMapActivity(JNIEnv *env)
{
    jclass    Map_Class   = (*env)->FindClass(env, "mono/samples/googlemaps/MyMapActivity");
    jmethodID Map_defCtor = (*env)->GetMethodID (env, Map_Class, "<init>", "()V");
    jobject   instance    = (*env)->NewObject (env, Map_Class, Map_defCtor);

    return instance;
}
```

Le C# équivalent serait :

```csharp
IntPtr CreateMapActivity()
{
    IntPtr Map_Class   = JNIEnv.FindClass ("mono/samples/googlemaps/MyMapActivity");
    IntPtr Map_defCtor = JNIEnv.GetMethodID (Map_Class, "<init>", "()V");
    IntPtr instance    = JNIEnv.NewObject (Map_Class, Map_defCtor);

    return instance;
}
```

Une fois que vous avez une instance d’objet Java contenue dans un IntPtr, vous souhaiterez probablement faire quelque chose. Vous pouvez utiliser les méthodes JNIEnv comme [ <span class="external">JNIEnv.CallVoidMethod()</span> ](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallVoidMethod/p/System.IntPtr/System.IntPtr/Android.Runtime.JValue[]/) pour ce faire, mais s’il existe déjà une analogie C# wrapper, vous voudrez créer un wrapper sur la référence JNI. Vous pouvez le faire via le [Extensions.JavaCast <t>()</t> ](https://developer.xamarin.com/api/member/Android.Runtime.Extensions.JavaCast%7BTResult%7D/p/Android.Runtime.IJavaObject/) méthode d’extension :

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = new Java.Lang.Object(lrefActivity, JniHandleOwnership.TransferLocalRef)
    .JavaCast<Activity>();
```

Vous pouvez également utiliser le [Java.Lang.Object.GetObject <t>()</t> ](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) méthode :

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = Java.Lang.Object.GetObject<Activity>(lrefActivity, JniHandleOwnership.TransferLocalRef);
```

En outre, toutes les fonctions JNI ont été modifiées en supprimant le `JNIEnv*` paramètre présent dans chaque fonction JNI.


## <a name="summary"></a>Récapitulatif

Traiter directement avec JNI est une expérience terrible qui doit être évitée à tout prix. Malheureusement, il n’est pas toujours évitable ; J’espère que ce guide fournit une assistance lorsque vous atteignez les cas de Java indépendants avec Mono pour Android.


## <a name="related-links"></a>Liens associés

- [SanityTests (sample)](https://developer.xamarin.com/samples/SanityTests/)
- [Spécification de l’Interface Native Java](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/jniTOC.html)
- [Fonctions de l’Interface Native Java](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)
