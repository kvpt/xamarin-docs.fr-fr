---
title: Utilisation de JNI et Xamarin. Android
description: Xamarin. Android permet d’écrire des applications Android avec C# au lieu de Java. Plusieurs assemblys sont fournis avec Xamarin. Android, qui fournissent des liaisons pour les bibliothèques Java, notamment mono. Android. dll et mono. Android. GoogleMaps. dll. Toutefois, les liaisons ne sont pas fournies pour chaque bibliothèque Java possible, et les liaisons fournies peuvent ne pas lier tous les membres et types Java. Pour utiliser des types et des membres de Java indépendants, vous pouvez utiliser le langage JNI (Java Native Interface). Cet article explique comment utiliser JNI pour interagir avec des types et des membres Java à partir d’applications Xamarin. Android.
ms.prod: xamarin
ms.assetid: A417DEE9-7B7B-4E35-A79C-284739E3838E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 00c9c2e9f39943960d35c30602935ed109639cf4
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84567724"
---
# <a name="working-with-jni-and-xamarinandroid"></a>Utilisation de JNI et Xamarin. Android

_Xamarin. Android permet d’écrire des applications Android avec C# au lieu de Java. Plusieurs assemblys sont fournis avec Xamarin. Android, qui fournissent des liaisons pour les bibliothèques Java, notamment mono. Android. dll et mono. Android. GoogleMaps. dll. Toutefois, les liaisons ne sont pas fournies pour chaque bibliothèque Java possible, et les liaisons fournies peuvent ne pas lier tous les membres et types Java. Pour utiliser des types et des membres de Java indépendants, vous pouvez utiliser le langage JNI (Java Native Interface). Cet article explique comment utiliser JNI pour interagir avec des types et des membres Java à partir d’applications Xamarin. Android._

## <a name="overview"></a>Vue d’ensemble

Il n’est pas toujours nécessaire ou possible de créer un wrapper pouvant être appelé managé (MCW) pour appeler du code Java. Dans de nombreux cas, la fonctionnalité JNI « Inline » est parfaitement acceptable et utile pour une utilisation unique des membres Java indépendants. Il est souvent plus simple d’utiliser JNI pour appeler une méthode unique sur une classe Java que de générer une liaison. jar entière.

Xamarin. Android fournit l' `Mono.Android.dll` assembly, qui fournit une liaison pour la `android.jar` bibliothèque Android. Les types et les membres absents de `Mono.Android.dll` et qui ne sont pas présents dans `android.jar` peuvent être utilisés par les liaisons manuelles. Pour lier des types et des membres Java, vous devez utiliser**JNI**( **Java Native Interface** ) pour rechercher des types, lire et écrire des champs et appeler des méthodes.

L’API JNI dans Xamarin. Android est conceptuellement très similaire à l' `System.Reflection` API dans .net : elle vous permet de rechercher des types et des membres par nom, de lire et d’écrire des valeurs de champ, d’appeler des méthodes et bien plus encore. Vous pouvez utiliser JNI et l' `Android.Runtime.RegisterAttribute` attribut personnalisé pour déclarer des méthodes virtuelles qui peuvent être liées à la prise en charge de la substitution. Vous pouvez lier des interfaces afin qu’elles puissent être implémentées en C#.

Ce document explique les éléments suivants :

- Comment JNI fait référence aux types.
- Comment rechercher, lire et écrire des champs.
- Comment rechercher et appeler des méthodes.
- Comment exposer des méthodes virtuelles pour permettre la substitution à partir du code managé.
- Comment exposer des interfaces.

## <a name="requirements"></a>Spécifications

JNI, tel qu’exposé par le biais de l' [espace de noms Android. Runtime. JNIEnv](xref:Android.Runtime.JNIEnv), est disponible dans toutes les versions de Xamarin. Android.
Pour lier des types et des interfaces Java, vous devez utiliser Xamarin. Android 4,0 ou une version ultérieure.

## <a name="managed-callable-wrappers"></a>Wrappers pouvant être appelés par Managed

Un **Wrapper Managed Callable** (**MCW**) est une *liaison* pour une classe ou une interface Java qui encapsule l’ensemble des machines JNI afin que le code C# du client n’ait pas à se soucier de la complexité sous-jacente de JNI. La plupart de `Mono.Android.dll` se composent de wrappers pouvant être appelés managés.

Les wrappers pouvant être appelés par Managed répondent à deux objectifs :

1. Encapsulez l’utilisation de JNI afin que le code client n’ait pas besoin de connaître la complexité sous-jacente.
1. Rendre possible les types Java sous-classe et implémenter des interfaces Java.

Le premier objectif est purement à la commodité et à l’encapsulation de la complexité, de sorte que les consommateurs disposent d’un ensemble simple et géré de classes à utiliser. Cela nécessite l’utilisation des différents membres [JNIEnv](xref:Android.Runtime.JNIEnv) , comme décrit plus loin dans cet article. Gardez à l’esprit que les wrappers pouvant être appelés ne sont pas strictement nécessaires &ndash; . l’utilisation d’JNI « Inline » est parfaitement acceptable et est utile pour une utilisation unique des membres Java indépendants. La sous-classe et l’implémentation de l’interface requièrent l’utilisation de wrappers pouvant être appelés managés.

## <a name="android-callable-wrappers"></a>Wrappers pouvant être appelés par Android

Les wrappers pouvant être appelés Android (ACW) sont requis chaque fois que le runtime Android (ART) doit appeler du code managé. Ces wrappers sont requis, car il n’existe aucun moyen d’inscrire des classes avec des illustrations au moment de l’exécution.
(En particulier, la fonction JNI [defineClass](https://docs.oracle.com/javase/6/docs/technotes/guides/jni/spec/functions.html#wp15986) n’est pas prise en charge par le runtime Android. Les wrappers pouvant être appelés par Android comportent le manque de prise en charge de l’inscription du type d’exécution.)

Chaque fois que le code Android doit exécuter une méthode virtuelle ou d’interface qui est substituée ou implémentée en code managé, Xamarin. Android doit fournir un proxy Java afin que cette méthode soit distribuée au type managé approprié. Ces types de proxy Java sont du code Java qui ont la même classe de base et la même liste d’interfaces Java que le type managé, implémentant les mêmes constructeurs et déclarant une classe de base et des méthodes d’interface substituées.

Les wrappers pouvant être appelés par Android sont générés par le programme **monodroid. exe** pendant le [processus de génération](~/android/deploy-test/building-apps/build-process.md)et sont générés pour tous les types qui héritent (directement ou indirectement) de [java. lang. Object](xref:Java.Lang.Object).

### <a name="implementing-interfaces"></a>Implémentation des interfaces

Il peut arriver que vous deviez implémenter une interface Android (par exemple, [Android. Content. IComponentCallbacks](xref:Android.Content.IComponentCallbacks)).

Toutes les interfaces et classes Android étendent l’interface [Android. Runtime. IJavaObject.](xref:Android.Runtime.IJavaObject) par conséquent, tous les types Android doivent implémenter `IJavaObject` .
Xamarin. Android tire parti de ce fait &ndash; qu’il utilise `IJavaObject` pour fournir Android avec un proxy Java (un wrapper Android pouvant être appelé) pour le type managé donné. Étant donné que **monodroid. exe** recherche uniquement les `Java.Lang.Object` sous-classes (qui doivent implémenter `IJavaObject` ), le sous `Java.Lang.Object` -processus fournit un moyen d’implémenter des interfaces dans du code managé. Par exemple :

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

### <a name="implementation-details"></a>Informations d’implémentation

*Le reste de cet article fournit des détails d’implémentation susceptibles de changer sans préavis* (et présenté ici uniquement parce que les développeurs peuvent être curieux de savoir ce qui se passe en coulisses).

Par exemple, en fonction de la source C# suivante :

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

Le programme **Android. exe** génère le wrapper Android pouvant être appelé suivant :

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

Notez que la classe de base est conservée, et les déclarations de méthode natives sont fournies pour chaque méthode substituée dans le code managé.

### <a name="exportattribute-and-exportfieldattribute"></a>ExportAttribute et ExportFieldAttribute

En règle générale, Xamarin. Android génère automatiquement le code Java qui comprend le ACW ; cette génération est basée sur les noms de la classe et de la méthode lorsqu’une classe dérive d’une classe Java et remplace les méthodes Java existantes. Toutefois, dans certains scénarios, la génération de code n’est pas appropriée, comme indiqué ci-dessous :

- Android prend en charge les noms d’action dans les attributs XML de mise en page, par exemple l’attribut XML [Android : OnClick](xref:Android.Views.View.IOnClickListener.OnClick*) . Lorsque cette valeur est spécifiée, l’instance de vue gonflée tente de rechercher la méthode Java.

- L’interface [java. IO. Serializable](https://developer.android.com/reference/java/io/Serializable.html) requiert les `readObject` `writeObject` méthodes et. Étant donné qu’ils ne sont pas membres de cette interface, notre implémentation managée correspondante n’expose pas ces méthodes au code Java.

- L’interface [Android. OS. parcelable](xref:Android.OS.Parcelable) s’attend à ce qu’une classe d’implémentation ait un champ statique `CREATOR` de type `Parcelable.Creator` . Le code Java généré requiert un champ explicite. Dans le cadre de notre scénario standard, il n’existe aucun moyen de générer un champ dans du code Java à partir de code managé.

Étant donné que la génération de code ne fournit pas de solution pour générer des méthodes Java arbitraires avec des noms arbitraires, en commençant par Xamarin. Android 4,2, [ExportAttribute](xref:Java.Interop.ExportAttribute) et [ExportFieldAttribute](xref:Java.Interop.ExportFieldAttribute) ont été introduits pour offrir une solution aux scénarios ci-dessus. Les deux attributs résident dans l' `Java.Interop` espace de noms :

- `ExportAttribute`&ndash;spécifie un nom de méthode et ses types d’exceptions attendus (pour fournir des « levées » explicites dans Java). Lorsqu’elle est utilisée sur une méthode, la méthode « exporte » une méthode Java qui génère un code de distribution vers l’appel JNI correspondant à la méthode managée. Cela peut être utilisé avec `android:onClick` et `java.io.Serializable` .

- `ExportFieldAttribute`&ndash;spécifie un nom de champ. Elle réside sur une méthode qui fonctionne comme un initialiseur de champ. Cela peut être utilisé avec `android.os.Parcelable` .

#### <a name="troubleshooting-exportattribute-and-exportfieldattribute"></a>Résolution des problèmes de ExportAttribute et ExportFieldAttribute

- L’empaquetage échoue en raison d’un **fichier mono. Android. Export. dll** manquant. &ndash; si vous avez utilisé `ExportAttribute` ou `ExportFieldAttribute` sur certaines méthodes de votre code ou de bibliothèques dépendantes, vous devez ajouter **mono. Android. Export. dll**. Cet assembly est isolé pour prendre en charge le code de rappel à partir de Java. Il est distinct de **mono. Android. dll** , car il ajoute de la taille supplémentaire à l’application.

- Dans la version Release, `MissingMethodException` se produit pour les méthodes d’exportation dans la version &ndash; Release, `MissingMethodException` se produit pour les méthodes d’exportation. (Ce problème est résolu dans la dernière version de Xamarin. Android.)

### <a name="exportparameterattribute"></a>ExportParameterAttribute

`ExportAttribute`et `ExportFieldAttribute` fournissent des fonctionnalités que le code d’exécution Java peut utiliser. Ce code d’exécution accède au code managé via les méthodes JNI générées pilotées par ces attributs. Par conséquent, il n’existe aucune méthode Java liée à la méthode managée. par conséquent, la méthode Java est générée à partir d’une signature de méthode managée.

Toutefois, ce cas n’est pas entièrement déterminant. En particulier, cela est vrai dans certains mappages avancés entre les types managés et les types Java tels que :

- InputStream
- OutputStream
- XmlPullParser
- XmlResourceParser

Lorsque des types tels que ceux-ci sont nécessaires pour les méthodes exportées, `ExportParameterAttribute` doit être utilisé pour donner explicitement un type au paramètre ou à la valeur de retour correspondant.

### <a name="annotation-attribute"></a>Attribut annotation

Dans Xamarin. Android 4,2, nous avons converti `IAnnotation` les types d’implémentation en attributs (System. Attribute) et ajouté la prise en charge de la génération d’annotations dans les wrappers Java.

Cela signifie les changements directionnels suivants :

- Le générateur de liaisons génère `Java.Lang.DeprecatedAttribute` à partir de `java.Lang.Deprecated` (alors qu’il doit être `[Obsolete]` en code managé).

- Cela ne signifie pas que la `Java.Lang.Deprecated` classe existante disparaîtra. Ces objets basés sur Java peuvent toujours être utilisés comme objets Java habituels (si cette utilisation existe). Il y aura `Deprecated` des `DeprecatedAttribute` classes et.

- La `Java.Lang.DeprecatedAttribute` classe est marquée comme `[Annotation]` . Lorsqu’un attribut personnalisé est hérité de cet `[Annotation]` attribut, la tâche MSBuild génère une annotation Java pour cet attribut personnalisé ( @Deprecated ) dans le wrapper ACW (Android Callable Wrapper).

- Les annotations peuvent être générées sur des classes, des méthodes et des champs exportés (qui est une méthode dans du code managé).

Si la classe conteneur (la classe annotée elle-même, ou la classe qui contient les membres annotés) n’est pas inscrite, la source de la classe Java entière n’est pas entièrement générée, y compris les annotations. Pour les méthodes, vous pouvez spécifier le `ExportAttribute` pour obtenir la méthode générée explicitement et annotée. En outre, il ne s’agit pas d’une fonctionnalité permettant de « générer » une définition de classe d’annotation Java. En d’autres termes, si vous définissez un attribut managé personnalisé pour une certaine annotation, vous devez ajouter une autre bibliothèque. jar qui contient la classe d’annotation Java correspondante. L’ajout d’un fichier source Java qui définit le type d’annotation n’est pas suffisant. Le compilateur Java ne fonctionne pas de la même façon que la méthode **apt**.

En outre, les limitations suivantes s’appliquent :

- Ce processus de conversion ne tient pas compte `@Target` de l’annotation sur le type d’annotation.

- Les attributs sur une propriété ne fonctionnent pas. Utilisez des attributs pour l’accesseur Get ou la méthode setter de propriété à la place.

## <a name="class-binding"></a>Liaison de classe

La liaison d’une classe signifie l’écriture d’un wrapper pouvant être appelé managé pour simplifier l’appel du type Java sous-jacent.

La liaison de méthodes virtuelles et abstraites pour permettre la substitution de C# nécessite Xamarin. Android 4,0. Toutefois, toute version de Xamarin. Android peut lier des méthodes non virtuelles, des méthodes statiques ou des méthodes virtuelles sans prendre en charge les remplacements.

Une liaison contient généralement les éléments suivants :

- [Handle JNI vers le type Java qui est lié](#_Looking_up_Java_Types).

- [ID de champ et propriétés JNI pour chaque champ lié](#_Instance_Fields).

- [ID et méthodes de méthode JNI pour chaque méthode liée](#_Instance_Methods).

- Si la sous-classe est requise, le type doit avoir un attribut personnalisé [RegisterAttribute](xref:Android.Runtime.RegisterAttribute) sur la déclaration de type avec [RegisterAttribute. DoNotGenerateAcw](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw) défini sur `true` .

### <a name="declaring-type-handle"></a>Déclaration de type DECLARATION

Les méthodes de recherche de champ et de méthode requièrent une référence d’objet qui fait référence à leur type déclarant. Par Convention, il est conservé dans un `class_ref` champ :

```csharp
static IntPtr class_ref = JNIEnv.FindClass(CLASS);
```

Pour plus d’informations sur le jeton, consultez la section [références de type JNI](#_JNI_Type_References) `CLASS` .

### <a name="binding-fields"></a>Champs de liaison

Les champs Java sont exposés en tant que propriétés C#, par exemple le champ Java [java.lang.System.in](https://developer.android.com/reference/java/lang/System.html#in) est lié en tant que la propriété c# [java.lang.JavaSystem.in](xref:Java.Lang.JavaSystem.In).
En outre, dans la mesure où JNI fait la distinction entre les champs statiques et les champs d’instance, différentes méthodes sont utilisées lors de l’implémentation des propriétés.

La liaison de champ implique trois ensembles de méthodes :

1. Méthode d' *ID de champ d’extraction* . La méthode *obtenir l’ID de champ* est chargée de retourner un handle de champ qui sera utilisé par les méthodes d' *extraction* de valeur de champ et de valeur de champ d' *ensemble* . Pour obtenir l’ID de champ, vous devez connaître le type de déclaration, le nom du champ et la [signature de type JNI](#JNI_Type_Signatures) du champ.

1. Méthodes d' *extraction de valeur de champ* . Ces méthodes requièrent le handle de champ et sont responsables de la lecture de la valeur du champ à partir de Java.
    La méthode à utiliser dépend du type du champ.

1. Méthodes *set de valeur de champ* . Ces méthodes requièrent le handle de champ et sont responsables de l’écriture de la valeur du champ dans Java. La méthode à utiliser dépend du type du champ.

Les [champs statiques](#_Static_Fields) utilisent les méthodes [JNIEnv. GetStaticFieldID](xref:Android.Runtime.JNIEnv.GetStaticMethodID*), `JNIEnv.GetStatic*Field` et [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) .

 Les [champs d’instance](#_Instance_Fields) utilisent les méthodes [JNIEnv. GetFieldID](xref:Android.Runtime.JNIEnv.GetFieldID*), `JNIEnv.Get*Field` et [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*) .

Par exemple, la propriété statique `JavaSystem.In` peut être implémentée comme suit :

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

Remarque : nous utilisons [InputStreamInvoker. FromJniHandle](xref:Android.Runtime.InputStreamInvoker.FromJniHandle*) pour convertir la référence JNI en `System.IO.Stream` instance, et nous utilisons `JniHandleOwnership.TransferLocalRef` car [JNIEnv. GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*) retourne une référence locale.

La plupart des types [Android. Runtime](xref:Android.Runtime) ont des `FromJniHandle` méthodes qui convertissent une référence JNI dans le type souhaité.

### <a name="method-binding"></a>Liaison de méthode

Les méthodes Java sont exposées en tant que méthodes C# et en tant que propriétés C#. Par exemple, la méthode Java Java [. lang. Runtime. runFinalizersOnExit](https://developer.android.com/reference/java/lang/Runtime.html#runFinalizersOnExit(boolean)) est liée en tant que méthode [java. lang. Runtime. runFinalizersOnExit](xref:Java.Lang.Runtime.RunFinalizersOnExit*) , et la méthode [java. lang. Object. GetClass](https://developer.android.com/reference/java/lang/Object.html#getClass) est liée en tant que propriété [java. lang. Object. Class](xref:Java.Lang.Object.Class) .

L’appel de méthode est un processus en deux étapes :

1. *ID de méthode d’extraction* pour la méthode à appeler. La méthode *obtenir l’ID de méthode* est chargée de retourner un handle de méthode qui sera utilisé par les méthodes d’appel de méthode. Pour obtenir l’ID de méthode, vous devez connaître le type de déclaration, le nom de la méthode et la [signature de type JNI](#JNI_Type_Signatures) de la méthode.

1. Appelez la méthode.

Tout comme avec les champs, les méthodes à utiliser pour récupérer l’ID de méthode et appeler la méthode diffèrent entre les méthodes statiques et les méthodes d’instance.

Les [méthodes statiques](#_Static_Methods_1) utilisent [JNIEnv. GetStaticMethodID ()](xref:Android.Runtime.JNIEnv.GetStaticMethodID*) pour Rechercher l’ID de méthode et utiliser la `JNIEnv.CallStatic*Method` famille de méthodes pour l’appel.

Les [méthodes d’instance](#_Instance_Methods) utilisent [JNIEnv. GetMethodID](xref:Android.Runtime.JNIEnv.GetMethodID*) pour Rechercher l’ID de méthode et utiliser les `JNIEnv.Call*Method` familles et `JNIEnv.CallNonvirtual*Method` des méthodes pour l’appel.

La liaison de méthode est potentiellement plus qu’un simple appel de méthode. La liaison de méthode comprend également l’autorisation d’une méthode à substituer (pour les méthodes abstraites et non finales) ou implémentée (pour les méthodes d’interface). La section [prise en charge de l’héritage, des interfaces](#_Supporting_Inheritance,_Interfaces_1) couvre les complexités de la prise en charge des méthodes virtuelles et des méthodes d’interface.

<a name="_Static_Methods_1"></a>

#### <a name="static-methods"></a>Méthodes statiques

La liaison d’une méthode statique implique l’utilisation `JNIEnv.GetStaticMethodID` de pour obtenir un handle de méthode, puis à l’aide de la `JNIEnv.CallStatic*Method` méthode appropriée, selon le type de retour de la méthode. Voici un exemple de liaison pour la méthode [Runtime. GetRuntime](https://developer.android.com/reference/java/lang/Runtime.html#getRuntime()) :

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

Notez que nous stockons le handle de méthode dans un champ statique, `id_getRuntime` . Il s’agit d’une optimisation des performances, afin que le handle de la méthode n’ait pas besoin d’être recherché à chaque appel. Il n’est pas nécessaire de mettre en cache le descripteur de méthode de cette façon. Une fois le handle de méthode obtenu, [JNIEnv. CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) est utilisé pour appeler la méthode. `JNIEnv.CallStaticObjectMethod`retourne un `IntPtr` qui contient le handle de l’instance java retournée.
[Java. lang. Object. GetObject &lt; T &gt; (IntPtr, JniHandleOwnership)](xref:Java.Lang.Object.GetObject*) est utilisé pour convertir le descripteur Java en instance d’objet fortement typée.

#### <a name="non-virtual-instance-method-binding"></a>Liaison de méthode d’instance non virtuelle

La liaison d’une `final` méthode d’instance ou d’une méthode d’instance qui ne requiert pas de substitution, implique l’utilisation `JNIEnv.GetMethodID` de pour obtenir un handle de méthode, puis à l’aide de la `JNIEnv.Call*Method` méthode appropriée, selon le type de retour de la méthode. Voici un exemple de liaison pour la `Object.Class` propriété :

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

Notez que nous stockons le handle de méthode dans un champ statique, `id_getClass` .
Il s’agit d’une optimisation des performances, afin que le handle de la méthode n’ait pas besoin d’être recherché à chaque appel. Il n’est pas nécessaire de mettre en cache le descripteur de méthode de cette façon. Une fois le handle de méthode obtenu, [JNIEnv. CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) est utilisé pour appeler la méthode. `JNIEnv.CallStaticObjectMethod`retourne un `IntPtr` qui contient le handle de l’instance java retournée.
[Java. lang. Object. GetObject &lt; T &gt; (IntPtr, JniHandleOwnership)](xref:Java.Lang.Object.GetObject*) est utilisé pour convertir le descripteur Java en instance d’objet fortement typée.

### <a name="binding-constructors"></a>Constructeurs de liaison

Les constructeurs sont des méthodes Java avec le nom `"<init>"` . Tout comme avec les méthodes d’instance Java, `JNIEnv.GetMethodID` est utilisé pour rechercher le handle du constructeur. Contrairement aux méthodes Java, les méthodes [JNIEnv. NewObject](xref:Android.Runtime.JNIEnv.NewObject*) sont utilisées pour appeler le handle de méthode de constructeur. La valeur de retour de `JNIEnv.NewObject` est une référence locale JNI :

```csharp
int value = 42;
IntPtr class_ref    = JNIEnv.FindClass ("java/lang/Integer");
IntPtr id_ctor_I    = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
IntPtr lrefInstance = JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value));
// Dispose of lrefInstance, class_ref…
```

Normalement, une liaison de classe sous-classe [java. lang. Object](xref:Java.Lang.Object).
Lors du sous-classement `Java.Lang.Object` , une sémantique supplémentaire entre en lecture : une `Java.Lang.Object` instance gère une référence globale à une instance Java via la `Java.Lang.Object.Handle` propriété.

1. Le `Java.Lang.Object` constructeur par défaut va allouer une instance java.

1. Si le type a un `RegisterAttribute` et que `RegisterAttribute.DoNotGenerateAcw` a la `true` valeur, une instance du `RegisterAttribute.Name` type est créée par le biais de son constructeur par défaut.

1. Sinon, le [Wrapper pouvant être appelé par Android](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) correspondant à `this.GetType` est instancié par le biais de son constructeur par défaut. Les wrappers pouvant être appelés Android sont générés au cours de la création du package pour chaque `Java.Lang.Object` sous-classe pour laquelle `RegisterAttribute.DoNotGenerateAcw` n’a pas la valeur `true` .

Pour les types qui ne sont pas des liaisons de classe, il s’agit de la sémantique attendue : l’instanciation d’une `Mono.Samples.HelloWorld.HelloAndroid` instance C# doit construire une `mono.samples.helloworld.HelloAndroid` instance Java qui est un wrapper Android pouvant être appelé.

Pour les liaisons de classe, il peut s’agir du comportement correct si le type Java contient un constructeur par défaut et/ou qu’aucun autre constructeur ne doit être appelé. Dans le cas contraire, un constructeur doit être fourni pour effectuer les actions suivantes :

1. Appel de [java. lang. Object (IntPtr, JniHandleOwnership)](xref:Java.Lang.Object#ctor*) au lieu du constructeur par défaut `Java.Lang.Object` . Cela est nécessaire pour éviter de créer une nouvelle instance java.

1. Vérifiez la valeur de [java. lang. Object. handle](xref:Java.Lang.Object.Handle) avant de créer des instances Java. La `Object.Handle` propriété a une valeur autre que `IntPtr.Zero` si un wrapper Android pouvant être appelé a été construit en code Java, et la liaison de classe est construite pour contenir l’instance de wrapper pouvant être appelé Android créée. Par exemple, quand Android crée une `mono.samples.helloworld.HelloAndroid` instance, le wrapper Android pouvant être appelé est créé en premier, et le `HelloAndroid` constructeur Java crée une instance du type correspondant `Mono.Samples.HelloWorld.HelloAndroid` , avec la `Object.Handle` propriété définie sur l’instance java avant l’exécution du constructeur.

1. Si le type d’exécution actuel n’est pas le même que le type déclarant, une instance du wrapper Android pouvant être appelé doit être créée et utilisez [Object. SetHandle](xref:Java.Lang.Object.SetHandle*) pour stocker le handle retourné par [JNIEnv. CreateInstance](xref:Android.Runtime.JNIEnv.CreateInstance*).

1. Si le type du runtime actuel est le même que le type déclarant, appelez le constructeur Java et utilisez [Object. SetHandle](xref:Java.Lang.Object.SetHandle*) pour stocker le handle retourné par `JNIEnv.NewInstance` .

Par exemple, considérez le constructeur [java. lang. Integer (int)](https://developer.android.com/reference/java/lang/Integer.html#Integer(int)) . Ceci est lié comme suit :

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

Les méthodes [JNIEnv. CreateInstance](xref:Android.Runtime.JNIEnv.CreateInstance*) sont des assistances pour exécuter `JNIEnv.FindClass` , `JNIEnv.GetMethodID` , `JNIEnv.NewObject` et `JNIEnv.DeleteGlobalReference` sur la valeur retournée par `JNIEnv.FindClass` . Voir la section suivante pour plus de détails.

<a name="_Supporting_Inheritance,_Interfaces_1"></a>

### <a name="supporting-inheritance-interfaces"></a>Prise en charge de l’héritage, interfaces

La sous-classement d’un type Java ou l’implémentation d’une interface Java requiert la génération de [wrappers Android](~/android/platform/java-integration/android-callable-wrappers.md) (ACWs) générés pour chaque `Java.Lang.Object` sous-classe pendant le processus d’empaquetage. La génération de ACW est contrôlée par le biais de l’attribut personnalisé [Android. Runtime. RegisterAttribute](xref:Android.Runtime.RegisterAttribute) .

Pour les types C#, le `[Register]` constructeur d’attribut personnalisé requiert un argument : la [référence de type simplifié JNI](#_Simplified_Type_References_1) pour le type Java correspondant. Cela permet de fournir des noms différents entre Java et C#.

Avant Xamarin. Android 4,0, l' `[Register]` attribut personnalisé n’était pas disponible pour les types Java existants « alias ». Cela est dû au fait que le processus de génération ACW génère des ACWs pour chaque `Java.Lang.Object` sous-classe rencontrée.

Xamarin. Android 4,0 a introduit la propriété [RegisterAttribute. DoNotGenerateAcw](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw) . Cette propriété indique au processus de génération ACW d' *Ignorer* le type annoté, en autorisant la déclaration de nouveaux wrappers pouvant être appelés, qui n’entraînent pas la génération de ACWs au moment de la création du package. Cela permet de lier des types Java existants. Par exemple, considérez la classe Java simple suivante, `Adder` , qui contient une méthode, `add` , qui ajoute aux entiers et retourne le résultat :

```java
package mono.android.test;
public class Adder {
    public int add (int a, int b) {
        return a + b;
    }
}
```

Le `Adder` type peut être lié comme suit :

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

Ici, le `Adder` type C# *alias* le `Adder` type Java. L' `[Register]` attribut est utilisé pour spécifier le nom JNI du `mono.android.test.Adder` type Java, et la `DoNotGenerateAcw` propriété est utilisée pour inhiber la génération de ACW. Cela entraînera la génération d’un ACW pour le `ManagedAdder` type, qui sous-classe correctement le `mono.android.test.Adder` type. Si la `RegisterAttribute.DoNotGenerateAcw` propriété n’a pas été utilisée, le processus de génération Xamarin. Android aurait généré un nouveau `mono.android.test.Adder` type Java. Cela entraînerait des erreurs de compilation, car le `mono.android.test.Adder` type serait présent deux fois, dans deux fichiers distincts.

### <a name="binding-virtual-methods"></a>Liaison de méthodes virtuelles

`ManagedAdder`sous-classe le `Adder` type Java, mais il n’est pas particulièrement intéressant : le `Adder` type C# ne définit pas de méthode virtuelle, donc vous `ManagedAdder` ne pouvez pas remplacer quoi que ce soit.

Les `virtual` méthodes de liaison pour permettre le remplacement par des sous-classes nécessitent plusieurs choses qui doivent être effectuées dans les deux catégories suivantes :

1. **Liaison de méthode**

1. **Inscription de méthode**

#### <a name="method-binding"></a>Liaison de méthode

Une liaison de méthode nécessite l’ajout de deux membres de support à la `Adder` définition C# : `ThresholdType` , et `ThresholdClass` .

##### <a name="thresholdtype"></a>ThresholdType

La `ThresholdType` propriété retourne le type actuel de la liaison :

```csharp
partial class Adder {
    protected override System.Type ThresholdType {
        get {
            return typeof (Adder);
        }
    }
}
```

`ThresholdType`est utilisé dans la liaison de méthode pour déterminer quand il doit effectuer une distribution de méthode virtuelle ou non virtuelle. Elle doit toujours retourner une `System.Type` instance qui correspond au type C# déclarant.

##### <a name="thresholdclass"></a>ThresholdClass

La `ThresholdClass` propriété retourne la référence de classe JNI pour le type lié :

```csharp
partial class Adder {
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```

`ThresholdClass`est utilisé dans la liaison de méthode lors de l’appel de méthodes non virtuelles.

#### <a name="binding-implementation"></a>Implémentation de liaison

L’implémentation de la liaison de méthode est responsable de l’appel de la méthode Java par le Runtime. Il contient également une `[Register]` déclaration d’attribut personnalisée qui fait partie de l’inscription de la méthode et qui sera abordée dans la section relative à l’inscription de la méthode :

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

Le `id_add` champ contient l’ID de méthode pour la méthode Java à appeler. La `id_add` valeur est obtenue à partir de `JNIEnv.GetMethodID` , ce qui nécessite la classe de déclaration ( `class_ref` ), le nom de la méthode Java ( `"add"` ) et la signature JNI de la méthode ( `"(II)I"` ).

Une fois l’ID de méthode obtenu, `GetType` est comparé à `ThresholdType` pour déterminer si une distribution virtuelle ou non virtuelle est requise. La distribution virtuelle est requise lorsque `GetType` correspond `ThresholdType` , car `Handle` peut faire référence à une sous-classe allouée par Java qui remplace la méthode.

Si `GetType` ne correspond pas `ThresholdType` , `Adder` a été sous-classé (par exemple `ManagedAdder` , par) et l' `Adder.Add` implémentation est appelée uniquement si la sous-classe est appelée `base.Add` . Il s’agit du cas de dispatch non virtuel, où est `ThresholdClass` fourni. `ThresholdClass`spécifie la classe Java qui fournira l’implémentation de la méthode à appeler.

#### <a name="method-registration"></a>Inscription de méthode

Supposons que nous ayons une définition mise à jour `ManagedAdder` qui remplace la `Adder.Add` méthode :

```csharp
partial class ManagedAdder : Adder {
    public override int Add (int a, int b) {
        return (a*2) + (b*2);
    }
}
```

Rappelez-vous que `Adder.Add` avait un `[Register]` attribut personnalisé :

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
```

Le `[Register]` constructeur d’attribut personnalisé accepte trois valeurs :

1. Nom de la méthode Java, `"add"` dans ce cas.

1. Signature de type JNI de la méthode, `"(II)I"` dans ce cas.

1. La *méthode de connecteur* , `GetAddHandler` dans ce cas.
    Les méthodes de connecteur seront abordées ultérieurement.

Les deux premiers paramètres permettent au processus de génération ACW de générer une déclaration de méthode pour substituer la méthode. Le ACW résultant contient une partie du code suivant :

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

Notez qu’une `@Override` méthode est déclarée, qui délègue à une `n_` méthode de préfixe du même nom. Cela permet de s’assurer que lors de l’appel du code Java `ManagedAdder.add` , `ManagedAdder.n_add` sera appelé, ce qui permettra l' `ManagedAdder.Add` exécution de la méthode C# de substitution.

Par conséquent, la question la plus importante : Comment est-il `ManagedAdder.n_add` raccordé `ManagedAdder.Add` ?

Les `native` méthodes Java sont inscrites auprès du runtime Java (Runtime Android) via la [fonction JNI RegisterNatives](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp17734).
`RegisterNatives`prend un tableau de structures contenant le nom de la méthode Java, la signature de type JNI et un pointeur de fonction à appeler qui suit la [Convention d’appel JNI](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/design.html#wp715).
Le pointeur de fonction doit être une fonction qui prend deux arguments de pointeur suivis par les paramètres de méthode. La `ManagedAdder.n_add` méthode Java doit être implémentée par le biais d’une fonction qui a le prototype C suivant :

```csharp
int FunctionName(JNIEnv *env, jobject this, int a, int b)
```

Xamarin. Android n’expose pas de `RegisterNatives` méthode. Au lieu de cela, les ACW et MCW fournissent ensemble les informations nécessaires pour appeler `RegisterNatives` : le ACW contient le nom de la méthode et la signature de type JNI, la seule chose qui manque est un pointeur de fonction pour se raccorder.

C’est là qu’intervient la *méthode du connecteur* . Le troisième `[Register]` paramètre d’attribut personnalisé est le nom d’une méthode définie dans le type inscrit ou une classe de base du type inscrit qui n’accepte aucun paramètre et retourne un `System.Delegate` . Le retourné à `System.Delegate` son tour fait référence à une méthode qui possède la signature de fonction JNI correcte. Enfin, le délégué retourné par la méthode de connecteur *doit* être associé à une racine afin que le garbage collector ne le collecte pas, car le délégué est fourni à Java.

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

La `GetAddHandler` méthode crée un `Func<IntPtr, IntPtr, int, int,
int>` délégué qui fait référence à la `n_Add` méthode, puis appelle [JNINativeWrapper. CreateDelegate](xref:Android.Runtime.JNINativeWrapper.CreateDelegate*).
`JNINativeWrapper.CreateDelegate`encapsule la méthode fournie dans un bloc try/catch, afin que toutes les exceptions non gérées soient gérées et entraîne le déclenchement de l’événement [AndroidEvent. UnhandledExceptionRaiser](xref:Android.Runtime.AndroidEnvironment.UnhandledExceptionRaiser) . Le délégué obtenu est stocké dans la `cb_add` variable statique afin que le GC ne libère pas le délégué.

Enfin, la `n_Add` méthode est chargée de marshaler les paramètres JNI vers les types managés correspondants, puis de déléguer l’appel de la méthode.

Remarque : toujours utiliser `JniHandleOwnership.DoNotTransfer` lors de l’obtention d’un MCW via une instance java. Les traiter en tant que référence locale (et donc appeler `JNIEnv.DeleteLocalRef` ) rompent les &gt; transitions de pile gérées par Java managées &gt; .

### <a name="complete-adder-binding"></a>Liaison Adder complète

La liaison managée complète pour le `mono.android.tests.Adder` type est :

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

Lors de l’écriture d’un type qui répond aux critères suivants :

1. Sous-classes`Java.Lang.Object`

1. A un `[Register]` attribut personnalisé

1. `RegisterAttribute.DoNotGenerateAcw` est `true`

Ensuite, pour l’interaction GC, le type ne *doit pas* avoir de champs qui peuvent faire référence à une `Java.Lang.Object` sous- `Java.Lang.Object` classe ou au moment de l’exécution. Par exemple, les champs de type `System.Object` et n’importe quel type d’interface ne sont pas autorisés. Les types qui ne peuvent pas faire référence à des `Java.Lang.Object` instances sont autorisés, tels que `System.String` et `List<int>` . Cette restriction consiste à empêcher la collection d’objets prématurée par le GC.

Si le type doit contenir un champ d’instance qui peut faire référence à une `Java.Lang.Object` instance, le type de champ doit être `System.WeakReference` ou `GCHandle` .

## <a name="binding-abstract-methods"></a>Liaison de méthodes abstraites

Les `abstract` méthodes de liaison sont en grande partie identiques à la liaison de méthodes virtuelles. Il n’existe que deux différences :

1. La méthode abstraite est abstraite. Il conserve toujours l' `[Register]` attribut et l’inscription de méthode associée, la liaison de méthode est simplement déplacée vers le `Invoker` type.

1. Un non- `abstract` `Invoker` type est créé, qui sous-classe le type abstrait. Le `Invoker` type doit remplacer toutes les méthodes abstraites déclarées dans la classe de base, et l’implémentation substituée est l’implémentation de liaison de méthode, bien que le cas de dispatch non virtuel puisse être ignoré.

Par exemple, supposons que la méthode ci-dessus `mono.android.test.Adder.add` était `abstract` . La liaison C# changerait afin `Adder.Add` d’être abstraite, et un nouveau `AdderInvoker` type serait défini, qui implémentait `Adder.Add` :

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

Le `Invoker` type est uniquement nécessaire lorsque vous obtenez des références JNI à des instances créées par Java.

## <a name="binding-interfaces"></a>Interfaces de liaison

Le concept des interfaces de liaison est similaire à celui des classes de liaison contenant des méthodes virtuelles, mais la plupart des spécificités diffèrent de manière subtile (et non discrète). Considérons la [déclaration d’interface Java](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/Adder.java#L14)suivante :

```csharp
public interface Progress {
    void onAdd(int[] values, int currentIndex, int currentSum);
}
```

Les liaisons d’interface comportent deux parties : la définition d’interface C# et une définition d’appelant pour l’interface.

### <a name="interface-definition"></a>Définition d'interface

La définition de l’interface C# doit respecter les exigences suivantes :

- La définition de l’interface doit avoir un `[Register]` attribut personnalisé.

- La définition de l’interface doit étendre `IJavaObject interface` .
    Dans le cas contraire, vous empêchez ACWs d’hériter de l’interface java.

- Chaque méthode d’interface doit contenir un `[Register]` attribut qui spécifie le nom de la méthode Java correspondante, la signature JNI et la méthode de connecteur.

- La méthode de connecteur doit également spécifier le type sur lequel la méthode de connecteur peut se trouver.

Lors de la liaison `abstract` et `virtual` des méthodes, la méthode de connecteur est recherchée dans la hiérarchie d’héritage du type en cours d’enregistrement. Comme les interfaces ne peuvent pas contenir de corps, cela ne fonctionne pas, c’est pourquoi un type doit être spécifié pour indiquer l’emplacement de la méthode de connecteur. Le type est spécifié dans la chaîne de méthode de connecteur, après un signe deux-points `':'` , et doit être le nom de type qualifié d’assembly du type contenant le demandeur.

Les déclarations de méthode d’interface sont une traduction de la méthode Java correspondante utilisant des types *compatibles* . Pour les types intégrés Java, les types compatibles sont les types C# correspondants, par exemple, Java `int` est c# `int` . Pour les types référence, le type compatible est un type qui peut fournir un handle JNI du type Java approprié.

Les membres d’interface ne sont pas appelés directement par le biais de l’appel Java, &ndash; &ndash; ce qui permet d’effectuer une certaine flexibilité.

L’interface de progression Java peut être [déclarée en C# en tant que](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L83):

```csharp
[Register ("mono/android/test/Adder$Progress", DoNotGenerateAcw=true)]
public interface IAdderProgress : IJavaObject {
    [Register ("onAdd", "([III)V",
            "GetOnAddHandler:Mono.Samples.SanityTests.IAdderProgressInvoker, SanityTests, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null")]
    void OnAdd (JavaArray<int> values, int currentIndex, int currentSum);
}
```

Notez dans la liste ci-dessus que nous allons mapper le `int[]` paramètre Java à un [JavaArray &lt; int &gt; ](xref:Android.Runtime.JavaArray`1).
Cela n’est pas nécessaire : nous aurions pu les lier à un code C#, à un, ou à une `int[]` `IList<int>` autre valeur. Quel que soit le type choisi, le `Invoker` doit être en mesure de le traduire en un `int[]` type Java pour l’appel.

### <a name="invoker-definition"></a>Définition de l’appelant

La `Invoker` définition de type doit hériter `Java.Lang.Object` , implémenter l’interface appropriée et fournir toutes les méthodes de connexion référencées dans la définition d’interface. Il existe une autre suggestion qui diffère d’une liaison de classe : les `class_ref` ID de champ et de méthode doivent être des membres d’instance, et non des membres statiques.

La raison pour laquelle les membres d’instance préfèrent être en fonction `JNIEnv.GetMethodID` du comportement dans le runtime Android. (Il peut également s’agir du comportement de Java ; il n’a pas été testé.) `JNIEnv.GetMethodID`retourne la valeur null lors de la recherche d’une méthode qui provient d’une interface implémentée et non de l’interface déclarée. Prenons l’exemple de l’interface Java [. util. SortedMap &lt; k, &gt; v](https://developer.android.com/reference/java/util/SortedMap.html) , qui implémente l’interface [java. util. map &lt; k, v &gt; ](https://developer.android.com/reference/java/util/Map.html) . Map fournit une méthode [claire](https://developer.android.com/reference/java/util/Map.html#clear()) . par conséquent, une définition apparemment raisonnable `Invoker` pour SortedMap serait la suivante :

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

L’exemple ci-dessus échoue, car `JNIEnv.GetMethodID` retourne `null` lors de la recherche de la `Map.clear` méthode par le biais de l' `SortedMap` instance de classe.

Il existe deux solutions à ce qui suit : effectuer le suivi de l’interface à partir de laquelle chaque méthode provient et avoir une `class_ref` interface for each, ou tout conserver en tant que membre d’instance et exécuter la recherche de méthode sur le type de classe le plus dérivé, et non sur le type d’interface. Ce dernier s’effectue dans **mono. Android. dll**.

La définition de l’appelant comprend six sections : le constructeur, la `Dispose` méthode, `ThresholdType` les `ThresholdClass` membres et, la `GetObject` méthode, l’implémentation de la méthode d’interface et l’implémentation de la méthode de connecteur.

#### <a name="constructor"></a>Constructeur

Le constructeur doit rechercher la classe Runtime de l’instance appelée et stocker la classe Runtime dans le champ d’instance `class_ref` :

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

Remarque : la `Handle` propriété doit être utilisée dans le corps du constructeur, et non dans le `handle` paramètre, comme sur Android v 4.0, le `handle` paramètre peut être non valide après la fin de l’exécution du constructeur de base.

#### <a name="dispose-method"></a>Dispose, méthode

La `Dispose` méthode doit libérer la référence globale allouée dans le constructeur :

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

Les `ThresholdType` `ThresholdClass` membres et sont identiques à ce qui se trouve dans une liaison de classe :

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

Une `GetObject` méthode statique est requise pour prendre en charge les [Extensions. JavaCast &lt; T &gt; ()](xref:Android.Runtime.Extensions.JavaCast*):

```csharp
partial class IAdderProgressInvoker {
    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }
}
```

#### <a name="interface-methods"></a>Méthodes d'interface

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

Les méthodes de connecteur et l’infrastructure de prise en charge sont responsables du marshaling des paramètres JNI en types C# appropriés. Le paramètre Java est `int[]` passé en tant que JNI `jintArray` , qui est un `IntPtr` dans C#. Le `IntPtr` doit être marshalé en un afin de `JavaArray<int>` prendre en charge l’appel de l’interface C# :

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

Si vous `int[]` préférez `JavaList<int>` , [JNIEnv. GetArray ()](xref:Android.Runtime.JNIEnv.GetArray*) peut être utilisé à la place :

```csharp
int[] _values = (int[]) JNIEnv.GetArray(values, JniHandleOwnership.DoNotTransfer, typeof (int));
```

Notez, toutefois, que `JNIEnv.GetArray` copie l’ensemble du tableau entre les machines virtuelles. par conséquent, pour les grands tableaux, cela peut entraîner beaucoup de sollicitation du gc.

### <a name="complete-invoker-definition"></a>Définition complète de l’appelant

[Définition complète du IAdderProgressInvoker](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L88):

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

## <a name="jni-object-references"></a>Références d’objets JNI

De nombreuses méthodes JNIEnv retournent des *références d’objets* *JNI* , qui sont similaires à `GCHandle` s. JNI fournit trois types différents de références d’objets : les références locales, les références globales et les références globales faibles. Les trois sont représentés en tant que `System.IntPtr` , *mais* (selon la section types de fonction JNI) tous les objets `IntPtr` retournés par les méthodes ne sont pas des `JNIEnv` références. Par exemple, [JNIEnv. GetMethodID](xref:Android.Runtime.JNIEnv.GetMethodID*) retourne `IntPtr` , mais ne retourne pas de référence d’objet, mais retourne un `jmethodID` . Pour plus d’informations, consultez la [documentation relative à la fonction JNI](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html) .

Les références locales sont créées par *la plupart des* méthodes de création de références.
Android n’autorise qu’un nombre limité de références locales à un moment donné, généralement 512. Les références locales peuvent être supprimées via [JNIEnv. DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*).
Contrairement à JNI, toutes les méthodes JNIEnv de référence qui retournent des références d’objet retournent des références locales ; [JNIEnv. FindClass](xref:Android.Runtime.JNIEnv.FindClass*) retourne une référence *globale* . Il est fortement recommandé de supprimer les références locales aussi rapidement que possible, en créant éventuellement un [objet Java. lang. Object](xref:Java.Lang.Object) autour de l’objet et en spécifiant `JniHandleOwnership.TransferLocalRef` le constructeur [java. lang. Object (handle IntPtr, JniHandleOwnership Transfer)](xref:Java.Lang.Object#ctor*) .

Les références globales sont créées par [JNIEnv. NewGlobalRef](xref:Android.Runtime.JNIEnv.NewGlobalRef*) et [JNIEnv. FindClass](xref:Android.Runtime.JNIEnv.FindClass*).
Ils peuvent être détruits avec [JNIEnv. DeleteGlobalRef](xref:Android.Runtime.JNIEnv.DeleteGlobalRef*).
Les émulateurs ont une limite de 2 000 références globales en suspens, tandis que les périphériques matériels ont une limite d’environ 52 000 références globales.

Les références globales faibles sont uniquement disponibles sur Android v 2.2 (Froyo) et versions ultérieures. Des références globales faibles peuvent être supprimées avec [JNIEnv. DeleteWeakGlobalRef](xref:Android.Runtime.JNIEnv.DeleteWeakGlobalRef*).

### <a name="dealing-with-jni-local-references"></a>Gestion des références locales JNI

Les méthodes [JNIEnv. GetObjectField](xref:Android.Runtime.JNIEnv.GetObjectField*), [JNIEnv. GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*), [JNIEnv. CallObjectMethod](xref:Android.Runtime.JNIEnv.CallObjectMethod*), [JNIEnv. CallNonvirtualObjectMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualObjectMethod*) et [JNIEnv. CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) retournent un `IntPtr` qui contient une référence locale JNI à un objet Java, ou `IntPtr.Zero` si Java est retourné `null` . En raison du nombre limité de références locales qui peuvent être en suspens (512 entrées), il est souhaitable de s’assurer que les références sont supprimées en temps voulu. Il existe trois façons de traiter les références locales : en les supprimant explicitement, en créant une `Java.Lang.Object` instance de stockage et en utilisant `Java.Lang.Object.GetObject<T>()` pour créer un wrapper managé pouvant être appelé autour de ces dernières.

### <a name="explicitly-deleting-local-references"></a>Suppression explicite de références locales

[JNIEnv. DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*) est utilisé pour supprimer les références locales. Une fois que la référence locale a été supprimée, elle ne peut plus être utilisée. vous devez donc veiller à ce qu’il s’agissait de `JNIEnv.DeleteLocalRef` la dernière opération effectuée avec la référence locale.

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
try {
    // Do something with `lref`
}
finally {
    JNIEnv.DeleteLocalRef (lref);
}
```

### <a name="wrapping-with-javalangobject"></a>Encapsulation avec Java. lang. Object

`Java.Lang.Object`fournit un constructeur [java. lang. Object (handle IntPtr, JniHandleOwnership Transfer)](xref:Java.Lang.Object#ctor*) qui peut être utilisé pour encapsuler une référence JNI de sortie. Le paramètre [JniHandleOwnership](xref:Android.Runtime.JniHandleOwnership) détermine la façon dont le `IntPtr` paramètre doit être traité :

- [JniHandleOwnership. DoNotTransfer](xref:Android.Runtime.JniHandleOwnership.DoNotTransfer) &ndash; l' `Java.Lang.Object` instance créée crée une nouvelle référence globale à partir du `handle` paramètre et `handle` reste inchangée.
    L’appelant est chargé de libérer `handle` , si nécessaire.

- [JniHandleOwnership. TransferLocalRef](xref:Android.Runtime.JniHandleOwnership.TransferLocalRef) &ndash; l' `Java.Lang.Object` instance créée créera une nouvelle référence globale à partir du `handle` paramètre et `handle` sera supprimée avec [JNIEnv. DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*) . L’appelant ne doit pas libérer `handle` et ne doit pas utiliser `handle` une fois l’exécution du constructeur terminée.

- [JniHandleOwnership. TransferGlobalRef](xref:Android.Runtime.JniHandleOwnership.TransferLocalRef) &ndash; l’instance créée prend le contrôle `Java.Lang.Object` de la propriété du `handle` paramètre. L’appelant ne doit pas libérer `handle` .

Étant donné que les méthodes d’appel de méthode JNI retournent des références locales, elles `JniHandleOwnership.TransferLocalRef` sont normalement utilisées :

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef);
```

La référence globale créée ne sera pas libérée tant que l' `Java.Lang.Object` instance n’est pas récupérée par le garbage collector. Si vous pouvez le faire, la suppression de l’instance libère la référence globale, ce qui accélère les garbage Collections :

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
using (var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef)) {
    // use value ...
}
```

### <a name="using-javalangobjectgetobjectlttgt"></a>Utilisation de Java. lang. Object. GetObject &lt; T &gt; ()

`Java.Lang.Object`fournit une méthode [java. lang. Object. GetObject &lt; T &gt; (handle IntPtr, JniHandleOwnership Transfer)](xref:Java.Lang.Object.GetObject*) qui peut être utilisée pour créer un wrapper managé pouvant être appelé du type spécifié.

Le type `T` doit respecter les exigences suivantes :

1. `T`doit être un type référence.

1. `T` doit implémenter l'interface `IJavaObject`.

1. Si `T` n’est pas une classe ou une interface abstraite, `T` doit fournir un constructeur avec les types de paramètres `(IntPtr,
    JniHandleOwnership)` .

1. Si `T` est une classe abstraite ou une interface, un *demandeur* *doit* être disponible pour `T` . Un demandeur est un type non abstrait qui hérite de `T` ou implémente `T` , et qui porte le même nom qu' `T` un suffixe de demandeur. Par exemple, si T est l’interface `Java.Lang.IRunnable` , le type `Java.Lang.IRunnableInvoker` doit exister et doit contenir le constructeur requis `(IntPtr,
    JniHandleOwnership)` .

Étant donné que les méthodes d’appel de méthode JNI retournent des références locales, elles `JniHandleOwnership.TransferLocalRef` sont normalement utilisées :

```csharp
IntPtr lrefString = JNIEnv.CallObjectMethod(instance, methodID);
Java.Lang.String value = Java.Lang.Object.GetObject<Java.Lang.String>( lrefString, JniHandleOwnership.TransferLocalRef);
```

<a name="_Looking_up_Java_Types"></a>

## <a name="looking-up-java-types"></a>Recherche de types Java

Pour rechercher un champ ou une méthode dans JNI, vous devez d’abord Rechercher le type déclarant pour le champ ou la méthode. La méthode [Android. Runtime. JNIEnv. FindClass (String)](xref:Android.Runtime.JNIEnv.FindClass*)) est utilisée pour rechercher des types Java. Le paramètre de chaîne est la *référence de type simplifiée* ou la *référence de type complète* pour le type Java. Consultez la [section Références de type JNI](#_JNI_Type_References) pour plus d’informations sur les références de type simplifiées et complètes.

Remarque : contrairement à toute autre `JNIEnv` méthode qui retourne des instances `FindClass` d’objet, retourne une référence globale, et non une référence locale.

<a name="_Instance_Fields"></a>

## <a name="instance-fields"></a>Champs d’instance

Les champs sont manipulés à l’aide d' *ID de champ*. Les ID de champ sont obtenus via [JNIEnv. GetFieldID](xref:Android.Runtime.JNIEnv.GetFieldID*), qui requiert la classe dans laquelle le champ est défini, le nom du champ et la [signature de type JNI](#JNI_Type_Signatures) du champ.

Les ID de champ n’ont pas besoin d’être libérés et sont valides tant que le type Java correspondant est chargé. (Android ne prend pas en charge actuellement le déchargement de classe.)

Il existe deux ensembles de méthodes pour manipuler des champs d’instance : un pour la lecture des champs d’instance et un pour l’écriture de champs d’instance. Tous les ensembles de méthodes requièrent un ID de champ pour lire ou écrire la valeur du champ.

### <a name="reading-instance-field-values"></a>Lecture des valeurs de champ d’instance

L’ensemble de méthodes permettant de lire les valeurs des champs d’instance suit le modèle d’affectation de noms :

```csharp
* JNIEnv.Get*Field(IntPtr instance, IntPtr fieldID);
```

où `*` est le type du champ :

- [JNIEnv. GetObjectField](xref:Android.Runtime.JNIEnv.GetObjectField*) &ndash; lit la valeur de n’importe quel champ d’instance qui n’est pas un type Builtin, tel que `java.lang.Object` , des tableaux et des types d’interfaces. La valeur retournée est une référence locale JNI.

- [JNIEnv. GetBooleanField](xref:Android.Runtime.JNIEnv.GetBooleanField*) &ndash; lit la valeur des `bool` champs d’instance.

- [JNIEnv. GetByteField](xref:Android.Runtime.JNIEnv.GetByteField*) &ndash; lit la valeur des `sbyte` champs d’instance.

- [JNIEnv. GetCharField](xref:Android.Runtime.JNIEnv.GetCharField*) &ndash; lit la valeur des `char` champs d’instance.

- [JNIEnv. GetShortField](xref:Android.Runtime.JNIEnv.GetShortField*) &ndash; lit la valeur des `short` champs d’instance.

- [JNIEnv. GetIntField](xref:Android.Runtime.JNIEnv.GetIntField*) &ndash; lit la valeur des `int` champs d’instance.

- [JNIEnv. GetLongField](xref:Android.Runtime.JNIEnv.GetLongField*) &ndash; lit la valeur des `long` champs d’instance.

- [JNIEnv. GetFloatField](xref:Android.Runtime.JNIEnv.GetFloatField*) &ndash; lit la valeur des `float` champs d’instance.

- [JNIEnv. GetDoubleField](xref:Android.Runtime.JNIEnv.GetDoubleField*) &ndash; lit la valeur des `double` champs d’instance.

### <a name="writing-instance-field-values"></a>Écriture de valeurs de champ d’instance

L’ensemble de méthodes permettant d’écrire des valeurs de champ d’instance suit le modèle d’affectation de noms :

```csharp
JNIEnv.SetField(IntPtr instance, IntPtr fieldID, Type value);
```

où *type* est le type du champ :

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; écrivez la valeur d’un champ qui n’est pas un type Builtin, tel que `java.lang.Object` , des tableaux et des types d’interfaces. La `IntPtr` valeur peut être une référence locale JNI, une référence globale JNI, une référence globale JNI faible, ou `IntPtr.Zero` (pour `null` ).

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; écrire la valeur des `bool` champs d’instance.

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; écrire la valeur des `sbyte` champs d’instance.

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; écrire la valeur des `char` champs d’instance.

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; écrire la valeur des `short` champs d’instance.

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; écrire la valeur des `int` champs d’instance.

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; écrire la valeur des `long` champs d’instance.

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; écrire la valeur des `float` champs d’instance.

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)) &ndash; écrire la valeur des `double` champs d’instance.

<a name="_Static_Fields"></a>

## <a name="static-fields"></a>Champs statiques

Les champs statiques sont manipulés à l’aide d' *ID de champ*. Les ID de champ sont obtenus via [JNIEnv. GetStaticFieldID](xref:Android.Runtime.JNIEnv.GetStaticFieldID*), qui requiert la classe dans laquelle le champ est défini, le nom du champ et la [signature de type JNI](#JNI_Type_Signatures) du champ.

Les ID de champ n’ont pas besoin d’être libérés et sont valides tant que le type Java correspondant est chargé. (Android ne prend pas en charge actuellement le déchargement de classe.)

Il existe deux ensembles de méthodes pour manipuler des champs statiques : un pour la lecture des champs d’instance et un pour l’écriture de champs d’instance. Tous les ensembles de méthodes requièrent un ID de champ pour lire ou écrire la valeur du champ.

### <a name="reading-static-field-values"></a>Lecture des valeurs de champs statiques

L’ensemble de méthodes permettant de lire les valeurs des champs statiques suit le modèle d’affectation de noms :

```csharp
* JNIEnv.GetStatic*Field(IntPtr class, IntPtr fieldID);
```

où `*` est le type du champ :

- [JNIEnv. GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*) &ndash; lit la valeur d’un champ statique qui n’est pas un type Builtin, tel que `java.lang.Object` , des tableaux et des types d’interfaces. La valeur retournée est une référence locale JNI.

- [JNIEnv. GetStaticBooleanField](xref:Android.Runtime.JNIEnv.GetStaticBooleanField*) &ndash; lit la valeur des `bool` champs statiques.

- [JNIEnv. GetStaticByteField](xref:Android.Runtime.JNIEnv.GetStaticByteField*) &ndash; lit la valeur des `sbyte` champs statiques.

- [JNIEnv. GetStaticCharField](xref:Android.Runtime.JNIEnv.GetStaticCharField*) &ndash; lit la valeur des `char` champs statiques.

- [JNIEnv. GetStaticShortField](xref:Android.Runtime.JNIEnv.GetStaticShortField*) &ndash; lit la valeur des `short` champs statiques.

- [JNIEnv. GetStaticLongField](xref:Android.Runtime.JNIEnv.GetStaticLongField*) &ndash; lit la valeur des `long` champs statiques.

- [JNIEnv. GetStaticFloatField](xref:Android.Runtime.JNIEnv.GetStaticFloatField*) &ndash; lit la valeur des `float` champs statiques.

- [JNIEnv. GetStaticDoubleField](xref:Android.Runtime.JNIEnv.GetStaticDoubleField*) &ndash; lit la valeur des `double` champs statiques.

### <a name="writing-static-field-values"></a>Écriture de valeurs de champ statiques

L’ensemble de méthodes permettant d’écrire des valeurs de champs statiques suit le modèle d’affectation de noms :

```csharp
JNIEnv.SetStaticField(IntPtr class, IntPtr fieldID, Type value);
```

où *type* est le type du champ :

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; écrire la valeur d’un champ statique qui n’est pas un type Builtin, tel que `java.lang.Object` , des tableaux et des types d’interfaces. La `IntPtr` valeur peut être une référence locale JNI, une référence globale JNI, une référence globale JNI faible, ou `IntPtr.Zero` (pour `null` ).

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; écrit la valeur des `bool` champs statiques.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; écrit la valeur des `sbyte` champs statiques.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; écrit la valeur des `char` champs statiques.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; écrit la valeur des `short` champs statiques.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; écrit la valeur des `int` champs statiques.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; écrit la valeur des `long` champs statiques.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; écrit la valeur des `float` champs statiques.

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)) &ndash; écrit la valeur des `double` champs statiques.

<a name="_Instance_Methods"></a>

## <a name="instance-methods"></a>Méthodes d’instance

Les méthodes d’instance sont appelées par le biais d' *ID de méthode*. Les ID de méthode sont obtenus via [JNIEnv. GetMethodID](xref:Android.Runtime.JNIEnv.GetMethodID*), qui requiert le type dans lequel la méthode est définie, le nom de la méthode et la [signature de type JNI](#JNI_Type_Signatures) de la méthode.

Les ID de méthode n’ont pas besoin d’être libérés et sont valides tant que le type Java correspondant est chargé. (Android ne prend pas en charge actuellement le déchargement de classe.)

Il existe deux ensembles de méthodes pour appeler des méthodes : une pour appeler des méthodes virtuellement et une pour appeler des méthodes non virtuellement. Les deux ensembles de méthodes requièrent un ID de méthode pour appeler la méthode, et l’appel non virtuel requiert également que vous spécifiiez l’implémentation de classe qui doit être appelée.

Les méthodes d’interface peuvent uniquement être recherchées dans le type déclarant ; les méthodes provenant d’interfaces étendues/héritées ne peuvent pas être recherchées. Pour plus d’informations, consultez la section interfaces de liaison/implémentation de l’appelant ultérieures.

Toute méthode déclarée dans la classe ou une classe de base ou une interface implémentée peut être recherchée.

### <a name="virtual-method-invocation"></a>Appel de méthode virtuelle

L’ensemble de méthodes pour l’appel de méthodes est virtuellement conforme au modèle d’affectation de noms :

```csharp
* JNIEnv.Call*Method( IntPtr instance, IntPtr methodID, params JValue[] args );
```

où `*` est le type de retour de la méthode.

- [JNIEnv. CallObjectMethod](xref:Android.Runtime.JNIEnv.CallObjectMethod*) &ndash; appelle une méthode qui retourne un type non Builtin, tel que `java.lang.Object` , des tableaux et des interfaces. La valeur retournée est une référence locale JNI.

- [JNIEnv. CallBooleanMethod](xref:Android.Runtime.JNIEnv.CallBooleanMethod*) &ndash; appelle une méthode qui retourne une `bool` valeur.

- [JNIEnv. CallByteMethod](xref:Android.Runtime.JNIEnv.CallByteMethod*) &ndash; appelle une méthode qui retourne une `sbyte` valeur.

- [JNIEnv. CallCharMethod](xref:Android.Runtime.JNIEnv.CallCharMethod*) &ndash; appelle une méthode qui retourne une `char` valeur.

- [JNIEnv. CallShortMethod](xref:Android.Runtime.JNIEnv.CallShortMethod*) &ndash; appelle une méthode qui retourne une `short` valeur.

- [JNIEnv. CallLongMethod](xref:Android.Runtime.JNIEnv.CallLongMethod*) &ndash; appelle une méthode qui retourne une `long` valeur.

- [JNIEnv. CallFloatMethod](xref:Android.Runtime.JNIEnv.CallFloatMethod*) &ndash; appelle une méthode qui retourne une `float` valeur.

- [JNIEnv. CallDoubleMethod](xref:Android.Runtime.JNIEnv.CallDoubleMethod*) &ndash; appelle une méthode qui retourne une `double` valeur.

### <a name="non-virtual-method-invocation"></a>Appel de méthode non virtuelle

L’ensemble de méthodes pour appeler des méthodes non virtuellement suit le modèle d’affectation de noms :

```csharp
* JNIEnv.CallNonvirtual*Method( IntPtr instance, IntPtr class, IntPtr methodID, params JValue[] args );
```

où `*` est le type de retour de la méthode. L’appel de méthode non virtuelle est généralement utilisé pour appeler la méthode de base d’une méthode virtuelle.

- [JNIEnv. CallNonvirtualObjectMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualObjectMethod*) &ndash; n’appelle pas virtuellement une méthode qui retourne un type non Builtin, tel que `java.lang.Object` , des tableaux et des interfaces. La valeur retournée est une référence locale JNI.

- [JNIEnv. CallNonvirtualBooleanMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualBooleanMethod*) &ndash; n’appelle pas virtuellement une méthode qui retourne une `bool` valeur.

- [JNIEnv. CallNonvirtualByteMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualByteMethod*) &ndash; n’appelle pas virtuellement une méthode qui retourne une `sbyte` valeur.

- [JNIEnv. CallNonvirtualCharMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualCharMethod*) &ndash; n’appelle pas virtuellement une méthode qui retourne une `char` valeur.

- [JNIEnv. CallNonvirtualShortMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualShortMethod*) &ndash; n’appelle pas virtuellement une méthode qui retourne une `short` valeur.

- [JNIEnv. CallNonvirtualLongMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualLongMethod*) &ndash; n’appelle pas virtuellement une méthode qui retourne une `long` valeur.

- [JNIEnv. CallNonvirtualFloatMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualFloatMethod*) &ndash; n’appelle pas virtuellement une méthode qui retourne une `float` valeur.

- [JNIEnv. CallNonvirtualDoubleMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualDoubleMethod*) &ndash; n’appelle pas virtuellement une méthode qui retourne une `double` valeur.

<a name="_Static_Methods"></a>

## <a name="static-methods"></a>Méthodes statiques

Les méthodes statiques sont appelées par le biais d' *ID de méthode*. Les ID de méthode sont obtenus via [JNIEnv. GetStaticMethodID](xref:Android.Runtime.JNIEnv.GetStaticMethodID*), qui requiert le type dans lequel la méthode est définie, le nom de la méthode et la [signature de type JNI](#JNI_Type_Signatures) de la méthode.

Les ID de méthode n’ont pas besoin d’être libérés et sont valides tant que le type Java correspondant est chargé. (Android ne prend pas en charge actuellement le déchargement de classe.)

### <a name="static-method-invocation"></a>Appel de méthode statique

L’ensemble de méthodes pour l’appel de méthodes est virtuellement conforme au modèle d’affectation de noms :

```csharp
* JNIEnv.CallStatic*Method( IntPtr class, IntPtr methodID, params JValue[] args );
```

où `*` est le type de retour de la méthode.

- [JNIEnv. CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) &ndash; appelle une méthode statique qui retourne un type non Builtin, tel que `java.lang.Object` , des tableaux et des interfaces. La valeur retournée est une référence locale JNI.

- [JNIEnv. CallStaticBooleanMethod](xref:Android.Runtime.JNIEnv.CallStaticBooleanMethod*) &ndash; appelle une méthode statique qui retourne une `bool` valeur.

- [JNIEnv. CallStaticByteMethod](xref:Android.Runtime.JNIEnv.CallStaticByteMethod*) &ndash; appelle une méthode statique qui retourne une `sbyte` valeur.

- [JNIEnv. CallStaticCharMethod](xref:Android.Runtime.JNIEnv.CallStaticCharMethod*) &ndash; appelle une méthode statique qui retourne une `char` valeur.

- [JNIEnv. CallStaticShortMethod](xref:Android.Runtime.JNIEnv.CallStaticShortMethod*) &ndash; appelle une méthode statique qui retourne une `short` valeur.

- [JNIEnv. CallStaticLongMethod](xref:Android.Runtime.JNIEnv.CallLongMethod*) &ndash; appelle une méthode statique qui retourne une `long` valeur.

- [JNIEnv. CallStaticFloatMethod](xref:Android.Runtime.JNIEnv.CallStaticFloatMethod*) &ndash; appelle une méthode statique qui retourne une `float` valeur.

- [JNIEnv. CallStaticDoubleMethod](xref:Android.Runtime.JNIEnv.CallStaticDoubleMethod*) &ndash; appelle une méthode statique qui retourne une `double` valeur.

<a name="JNI_Type_Signatures"></a>

## <a name="jni-type-signatures"></a>Signatures de type JNI

Les [signatures de type JNI](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/types.html#wp16432) sont des [références de type JNI](#_JNI_Type_References) (bien que des références de type non simplifiées), à l’exception des méthodes. Avec les méthodes, la signature de type JNI est une parenthèse ouvrante `'('` , suivie des références de type pour tous les types de paramètres concaténés ensemble (sans virgules de séparation ou autres éléments), suivis d’une parenthèse fermante `')'` , suivi de la référence de type JNI du type de retour de la méthode.

Par exemple, en fonction de la méthode Java :

```java
long f(int n, String s, int[] array);
```

La signature de type JNI serait la suivante :

```csharp
(ILjava/lang/String;[I)J
```

En général, il est *fortement* recommandé d’utiliser la `javap` commande pour déterminer les signatures JNI. Par exemple, la signature de type JNI de la méthode [java. lang. Thread. State. valueOf (String)](https://developer.android.com/reference/java/lang/Thread.State.html#valueOf(java.lang.String)) est « (Ljava/lang/String ;) Ljava/lang/thread $ State ; », alors que la signature de type JNI de la méthode [java. lang. Thread. State. Values](https://developer.android.com/reference/java/lang/Thread.State.html#values) est « () [Ljava/lang/thread $ State ; ». Regardez les points-virgules de fin ; celles- *ci font* partie de la signature de type JNI.

<a name="_JNI_Type_References"></a>

## <a name="jni-type-references"></a>Références de type JNI

Les références de type JNI diffèrent des références de type Java. Vous ne pouvez pas utiliser de noms de type Java complets `java.lang.String` , comme avec JNI, vous devez plutôt utiliser les variantes JNI `"java/lang/String"` ou `"Ljava/lang/String;"` , selon le contexte. pour plus d’informations, consultez la section ci-dessous.
Il existe quatre types de références de type JNI :

- **intégré**
- **faciliter**
- **type**
- **array**

### <a name="built-in-type-references"></a>Références de type intégrées

Les références de type intégrées sont un caractère unique, utilisé pour référencer des types valeur intégrés. Le mappage est le suivant :

- `"B"`pour `sbyte` .
- `"S"`pour `short` .
- `"I"`pour `int` .
- `"J"`pour `long` .
- `"F"`pour `float` .
- `"D"`pour `double` .
- `"C"`pour `char` .
- `"Z"`pour `bool` .
- `"V"`pour les `void` types de retour de méthode.

<a name="_Simplified_Type_References_1"></a>

### <a name="simplified-type-references"></a>Références de type simplifiées

Les références de type simplifiées ne peuvent être utilisées que dans [JNIEnv. FindClass (String)](xref:Android.Runtime.JNIEnv.FindClass*)).
Il existe deux façons de dériver une référence de type simplifiée :

1. À partir d’un nom Java complet, remplacez chaque `'.'` dans le nom du package et avant le nom du type par `'/'` , et chaque `'.'` dans un nom de type avec `'$'` .

1. Lire la sortie de `'unzip -l android.jar | grep JavaName'` .

L’une ou l’autre des deux entraînera le mappage du type Java [java. lang. Thread. State](https://developer.android.com/reference/java/lang/Thread.State.html) à la référence de type simplifiée `java/lang/Thread$State` .

### <a name="type-references"></a>Références de type

Une référence de type est une référence de type intégré ou une référence de type simplifiée avec un `'L'` préfixe et un `';'` suffixe. Pour le type Java Java [. lang. String](https://developer.android.com/reference/java/lang/String.html), la référence de type simplifiée est `"java/lang/String"` , tandis que la référence de type est `"Ljava/lang/String;"` .

Les références de type sont utilisées avec les références de type tableau et les signatures JNI.

Pour obtenir une référence de type, une autre méthode consiste à lire la sortie de `'javap -s -classpath android.jar fully.qualified.Java.Name'` .
Selon le type impliqué, vous pouvez utiliser une déclaration de constructeur ou un type de retour de méthode pour déterminer le nom JNI. Par exemple :

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

`Thread.State`est un type d’énumération Java. nous pouvons donc utiliser la signature de la `valueOf` méthode pour déterminer que la référence de type est Ljava/lang/thread $ State ;.

### <a name="array-type-references"></a>Références de type tableau

Les références de type tableau sont `'['` précédées d’une référence de type JNI.
Impossible d’utiliser des références de type simplifiées lors de la spécification de tableaux.

Par exemple, est, `int[]` `"[I"` `int[][]` est `"[[I"` , et `java.lang.Object[]` est `"[Ljava/lang/Object;"` .

## <a name="java-generics-and-type-erasure"></a>Génériques Java et effacement de type

La *plupart* du temps, comme vu par JNI, les génériques Java *n’existent pas*.
Il y a quelques « rides », mais ces rides sont dans la manière dont Java interagit avec les génériques, et non avec la manière dont JNI recherche et appelle les membres génériques.

Il n’existe aucune différence entre un type ou un membre générique et un type ou un membre non générique lors de l’interaction via JNI. Par exemple, le type générique [java. lang. Class &lt; T &gt; ](https://developer.android.com/reference/java/lang/Class.html) est également le type générique « RAW » `java.lang.Class` , tous deux ayant la même référence de type simplifiée, `"java/lang/Class"` .

## <a name="java-native-interface-support"></a>Prise en charge de l’interface native Java

[Android. Runtime. JNIEnv](xref:Android.Runtime.JNIEnv) est un wrapper managé pour l’interface native JAVE (JNI). Les fonctions JNI sont déclarées dans la spécification de l' [interface native Java](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html), bien que les méthodes aient été modifiées pour supprimer le `JNIEnv*` paramètre Explicit et `IntPtr` sont utilisées à la place de `jobject` , `jclass` , `jmethodID` , etc. Par exemple, considérez la [fonction JNI NewObject](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp4517):

```csharp
jobject NewObjectA(JNIEnv *env, jclass clazz, jmethodID methodID, jvalue *args);
```

Cela est exposé en tant que méthode [JNIEnv. NewObject](xref:Android.Runtime.JNIEnv.NewObject*) :

```csharp
public static IntPtr NewObject(IntPtr clazz, IntPtr jmethod, params JValue[] parms);
```

La conversion entre les deux appels est relativement simple. Dans C, vous auriez :

```c
jobject CreateMapActivity(JNIEnv *env)
{
    jclass    Map_Class   = (*env)->FindClass(env, "mono/samples/googlemaps/MyMapActivity");
    jmethodID Map_defCtor = (*env)->GetMethodID (env, Map_Class, "<init>", "()V");
    jobject   instance    = (*env)->NewObject (env, Map_Class, Map_defCtor);

    return instance;
}
```

L’équivalent C# est le suivant :

```csharp
IntPtr CreateMapActivity()
{
    IntPtr Map_Class   = JNIEnv.FindClass ("mono/samples/googlemaps/MyMapActivity");
    IntPtr Map_defCtor = JNIEnv.GetMethodID (Map_Class, "<init>", "()V");
    IntPtr instance    = JNIEnv.NewObject (Map_Class, Map_defCtor);

    return instance;
}
```

Une fois que vous avez une instance d’objet Java conservée dans un IntPtr, vous souhaiterez probablement effectuer une opération avec celle-ci. Vous pouvez utiliser des méthodes JNIEnv telles que [JNIEnv. CallVoidMethod ()](xref:Android.Runtime.JNIEnv.CallVoidMethod*) , mais s’il existe déjà un wrapper C# analogique, vous souhaiterez créer un wrapper sur la référence JNI. Pour ce faire, vous pouvez utiliser la méthode d’extension [Extensions. JavaCast \<T> ](xref:Android.Runtime.Extensions.JavaCast*) :

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = new Java.Lang.Object(lrefActivity, JniHandleOwnership.TransferLocalRef)
    .JavaCast<Activity>();
```

Vous pouvez également utiliser la méthode [java. lang. Object. \<T> GetObject](xref:Java.Lang.Object.GetObject*) :

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = Java.Lang.Object.GetObject<Activity>(lrefActivity, JniHandleOwnership.TransferLocalRef);
```

En outre, toutes les fonctions JNI ont été modifiées en supprimant le `JNIEnv*` paramètre présent dans chaque fonction JNI.

## <a name="summary"></a>Résumé

La gestion directe d’JNI est une expérience terrible qui devrait être évitée à tous les coûts. Malheureusement, il n’est pas toujours inévitable. Nous espérons que ce guide vous fournira de l’aide lorsque vous rencontrerez des cas en liaison mono avec mono pour Android.

## <a name="related-links"></a>Liens connexes

- [Spécification de l’interface native Java](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/jniTOC.html)
- [Fonctions d’interface native Java](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)
