---
title: Travailler avec JNI et Xamarin.Android
description: Xamarin.Android permet d’écrire des applications Android avec C au lieu de Java. Plusieurs assemblages sont fournis avec Xamarin.Android qui fournissent des liaisons pour les bibliothèques java, y compris Mono.Android.dll et Mono.Android.GoogleMaps.dll. Cependant, les fixations ne sont pas fournies pour toutes les bibliothèques Java possibles, et les fixations qui sont fournies peuvent ne pas lier tous les types de Java et membre. Pour utiliser des types et des membres Java non liés, l’interface native java (JNI) peut être utilisée. Cet article illustre comment utiliser JNI pour interagir avec les types Java et les membres des applications Xamarin.Android.
ms.prod: xamarin
ms.assetid: A417DEE9-7B7B-4E35-A79C-284739E3838E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 0fa717a775ff2f1ace9e248a8afde8d373e8a1f8
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292722"
---
# <a name="working-with-jni-and-xamarinandroid"></a>Travailler avec JNI et Xamarin.Android

_Xamarin.Android permet d’écrire des applications Android avec C au lieu de Java. Plusieurs assemblages sont fournis avec Xamarin.Android qui fournissent des liaisons pour les bibliothèques java, y compris Mono.Android.dll et Mono.Android.GoogleMaps.dll. Cependant, les fixations ne sont pas fournies pour toutes les bibliothèques Java possibles, et les fixations qui sont fournies peuvent ne pas lier tous les types de Java et membre. Pour utiliser des types et des membres Java non liés, l’interface native java (JNI) peut être utilisée. Cet article illustre comment utiliser JNI pour interagir avec les types Java et les membres des applications Xamarin.Android._

## <a name="overview"></a>Vue d’ensemble

Il n’est pas toujours nécessaire ou possible de créer un emballage callable géré (MCW) pour invoquer le code Java. Dans de nombreux cas, "inline" JNI est parfaitement acceptable et utile pour l’utilisation ponctuelle des membres Java non liés. Il est souvent plus simple d’utiliser JNI pour invoquer une méthode unique sur une classe Java que de générer une liaison .jar entière.

Xamarin.Android fournit `Mono.Android.dll` l’assemblage, qui fournit `android.jar` une reliure pour la bibliothèque d’Android. Les types et `Mono.Android.dll` les membres qui `android.jar` ne sont pas présents à l’intérieur et les types qui ne sont pas présents à l’intérieur peuvent être utilisés en les liant manuellement. Pour lier les types et les membres Java, vous utilisez **l’interface indigène Java** (**JNI**) pour rechercher des types, lire et écrire des champs, et invoquer des méthodes.

L’API JNI dans Xamarin.Android est `System.Reflection` conceptuellement très similaire à l’API en .NET: il vous permet de rechercher des types et des membres par nom, lire et écrire des valeurs de champ, invoquer des méthodes, et plus encore. Vous pouvez utiliser JNI et l’attribut `Android.Runtime.RegisterAttribute` personnalisé pour déclarer les méthodes virtuelles qui peuvent être liées à la prise en charge de la prépondérer. Vous pouvez lier les interfaces afin qu’elles puissent être implémentées en C.

Ce document explique :

- Comment JNI se réfère aux types.
- Comment rechercher, lire et écrire des champs.
- Comment rechercher et invoquer des méthodes.
- Comment exposer les méthodes virtuelles pour permettre de passer au code géré.
- Comment exposer les interfaces.

## <a name="requirements"></a>Spécifications

JNI, tel qu’exposé via [l’android.Runtime.JNIEnv namespace](xref:Android.Runtime.JNIEnv), est disponible dans toutes les versions de Xamarin.Android.
Pour lier les types et les interfaces Java, vous devez utiliser Xamarin.Android 4.0 ou plus tard.

## <a name="managed-callable-wrappers"></a>Emballages Callable gérés

Un **wrapper Callable géré** (**MCW**) est une *liaison* pour une classe ou une interface Java qui enveloppe toutes les machines JNI afin que le code client C N’a pas besoin de s’inquiéter de la complexité sous-jacente de JNI. La `Mono.Android.dll` plupart se compose d’emballages callables gérés.

Les emballages callables gérés servent deux buts :

1. Encapsulation JNI utiliser de sorte que le code client n’a pas besoin de connaître la complexité sous-jacente.
1. Permettre de sous-classer les types Java et d’implémenter des interfaces Java.

Le premier but est purement pour la commodité et l’encapsulation de la complexité de sorte que les consommateurs ont un ensemble simple et géré de classes à utiliser. Cela nécessite l’utilisation des différents membres [JNIEnv](xref:Android.Runtime.JNIEnv) comme décrit plus tard dans cet article. Gardez à l’esprit que les emballages &ndash; callables gérés ne sont pas strictement nécessaires "en ligne" L’utilisation JNI est parfaitement acceptable et est utile pour l’utilisation ponctuelle des membres Java non liés. La sous-classe et l’implémentation de l’interface nécessitent l’utilisation d’emballages callables gérés.

## <a name="android-callable-wrappers"></a>Wrappers pouvant être appelés par Android

Les emballages appelants Android (ACW) sont nécessaires chaque fois que l’exécution Android (ART) doit invoquer le code géré; ces emballages sont nécessaires parce qu’il n’y a aucun moyen d’enregistrer des cours avec ART à l’heure de course.
(Spécifiquement, la fonction [DéfiniClass](https://docs.oracle.com/javase/6/docs/technotes/guides/jni/spec/functions.html#wp15986) JNI n’est pas prise en charge par le temps d’exécution Android. Les emballages callables Android compenserent ainsi l’absence de support d’enregistrement de type runtime.)

Chaque fois que le code Android a besoin d’exécuter une méthode virtuelle ou d’interface qui est annulée ou implémentée dans le code géré, Xamarin.Android doit fournir un proxy Java afin que cette méthode soit expédiée au type géré approprié. Ces types de proxy Java sont le code Java qui ont la "même" classe de base et la liste d’interface Java comme le type géré, la mise en œuvre des mêmes constructeurs et de déclarer toute classe de base et les méthodes d’interface.

Les emballages callables Android sont générés par le programme **monodroid.exe** pendant le [processus de construction,](~/android/deploy-test/building-apps/build-process.md)et sont générés pour tous les types qui (directement ou indirectement) héritent [java.Lang.Object](xref:Java.Lang.Object).

### <a name="implementing-interfaces"></a>Implémentation des interfaces

Il ya des moments où vous pouvez avoir besoin d’implémenter une interface Android, (comme [Android.Content.IComponentCallbacks](xref:Android.Content.IComponentCallbacks)).

Toutes les classes et interfaces Android prolongent l’interface [Android.Runtime.IJavaObject;](xref:Android.Runtime.IJavaObject) par conséquent, tous `IJavaObject`les types Android doivent implémenter .
Xamarin.Android profite de &ndash; ce `IJavaObject` fait qu’il utilise pour fournir Android avec un proxy Java (un emballage callable Android) pour le type géré donné. Parce que **monodroid.exe** ne cherche que des `Java.Lang.Object` sous-classes (qui doivent être mises en œuvre), `IJavaObject`la sous-classe `Java.Lang.Object` nous fournit un moyen de mettre en œuvre des interfaces dans le code géré. Par exemple :

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

*Le reste de cet article fournit des détails de mise en œuvre sous réserve de changement sans préavis* (et est présenté ici seulement parce que les développeurs peuvent être curieux de savoir ce qui se passe sous le capot).

Par exemple, compte tenu de la source suivante :

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

Le programme **mandroid.exe** générera l’Emballage Android Callable suivant:

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

Notez que la classe de base est préservée, et des déclarations de méthode indigène sont fournies pour chaque méthode qui est remplacée dans le code géré.

### <a name="exportattribute-and-exportfieldattribute"></a>ExportAttribute et ExportFieldAttribute

Typiquement, Xamarin.Android génère automatiquement le code Java qui comprend l’ACW; cette génération est basée sur les noms de classe et de méthode lorsqu’une classe dérive d’une classe Java et remplace les méthodes Java existantes. Cependant, dans certains scénarios, la génération de code n’est pas adéquate, comme indiqué ci-dessous:

- Android prend en charge les noms d’action dans la mise en page des attributs XML, par exemple l’attribut [Android:onClick](xref:Android.Views.View.IOnClickListener.OnClick*) XML. Lorsqu’il est spécifié, l’instance Vue gonflée tente de rechercher la méthode Java.

- [L’interface java.io.Serializable](https://developer.android.com/reference/java/io/Serializable.html) nécessite et `readObject` `writeObject` des méthodes. Puisqu’ils ne sont pas membres de cette interface, notre implémentation gérée correspondante n’expose pas ces méthodes au code Java.

- L’interface [android.os.Parcelable](xref:Android.OS.Parcelable) s’attend à ce `CREATOR` qu’une classe d’implémentation doit avoir un champ statique de type `Parcelable.Creator`. Le code Java généré nécessite un champ explicite. Avec notre scénario standard, il n’y a aucun moyen de sortie de champ dans le code Java à partir de code géré.

Parce que la génération de code ne fournit pas une solution pour générer des méthodes java arbitraires avec des noms arbitraires, à commencer par Xamarin.Android 4.2, [l’ExportAttribute](xref:Java.Interop.ExportAttribute) et [ExportFieldAttribute](xref:Java.Interop.ExportFieldAttribute) ont été introduits pour offrir une solution aux scénarios ci-dessus. Les deux attributs `Java.Interop` résident dans l’espace nom:

- `ExportAttribute`&ndash; spécifie un nom de méthode et ses types d’exception attendus (pour donner explicite "jets" à Java). Lorsqu’elle est utilisée sur une méthode, la méthode « exportera » une méthode Java qui génère un code d’expédition à l’invocation JNI correspondante à la méthode gérée. Cela peut être `android:onClick` `java.io.Serializable`utilisé avec et .

- `ExportFieldAttribute`&ndash; spécifie un nom de terrain. Il réside sur une méthode qui fonctionne comme un initialisateur de champ. Cela peut être `android.os.Parcelable`utilisé avec .

#### <a name="troubleshooting-exportattribute-and-exportfieldattribute"></a>Dépannage ExportAttribute et ExportFieldAttribute

- Emballage échoue en raison de manquer **Mono.Android.Export.dll** &ndash; si vous avez utilisé `ExportAttribute` ou `ExportFieldAttribute` sur certaines méthodes dans votre code ou bibliothèques dépendantes, vous devez ajouter **Mono.Android.Export.dll**. Cet assemblage est isolé pour prendre en charge le code de rappel de Java. Il est séparé de **Mono.Android.dll** car il ajoute une taille supplémentaire à l’application.

- Dans Release `MissingMethodException` build, se &ndash; produit pour `MissingMethodException` les méthodes d’exportation In Release construire, se produit pour les méthodes d’exportation. (Ce problème est fixé dans la dernière version de Xamarin.Android.)

### <a name="exportparameterattribute"></a>ExportParameterAttribute

`ExportAttribute`et `ExportFieldAttribute` fournir des fonctionnalités que le code Java run-time peut utiliser. Ce code en temps d’exécution accède au code géré grâce aux méthodes JNI générées par ces attributs. En conséquence, il n’existe aucune méthode Java existante que la méthode gérée lie; par conséquent, la méthode Java est générée à partir d’une signature de méthode gérée.

Toutefois, cette affaire n’est pas tout à fait déterminante. Plus particulièrement, cela est vrai dans certaines cartographies avancées entre les types gérés et les types Java tels que:

- InputStream
- OutputStream (en)
- XmlPullParser XmlPullParser
- XmlResourceParser XmlResourceParser

Lorsque des types comme ceux-ci `ExportParameterAttribute` sont nécessaires pour les méthodes exportées, il doit être utilisé pour donner explicitement le paramètre correspondant ou la valeur de retour un type.

### <a name="annotation-attribute"></a>Attribut d’annotation

Dans Xamarin.Android 4.2, `IAnnotation` nous avons converti les types de implémentation en attributs (System.Attribute), et ajouté un support pour la génération d’annotation dans les emballages Java.

Cela signifie les changements directionnels suivants :

- Le générateur de `Java.Lang.DeprecatedAttribute` `java.Lang.Deprecated` liaison génère à `[Obsolete]` partir (alors qu’il doit être dans le code géré).

- Cela ne signifie `Java.Lang.Deprecated` pas que la classe existante disparaîtra. Ces objets basés à Java pourraient encore être utilisés comme des objets Java habituels (si une telle utilisation existe). Il y `Deprecated` `DeprecatedAttribute` aura et des cours.

- La `Java.Lang.DeprecatedAttribute` classe est `[Annotation]` marquée comme . Lorsqu’il y a un attribut `[Annotation]` personnalisé hérité de cet attribut, la tâche de msbuild générera une annotation Java pour cet attribut personnalisé (@Deprecated) dans l’emballage Callable Android (ACW).

- Des annotations peuvent être générées sur les classes, les méthodes et les champs exportés (qui est une méthode dans le code géré).

Si la classe contenante (la classe annotée elle-même, ou la classe qui contient les membres annotés) n’est pas enregistrée, toute la source de la classe Java n’est pas générée du tout, y compris les annotations. Pour les méthodes, `ExportAttribute` vous pouvez spécifier la méthode explicitement générée et annotée. En outre, il n’est pas une fonctionnalité pour "générer" une définition de classe d’annotation Java. En d’autres termes, si vous définissez un attribut géré personnalisé pour une certaine annotation, vous devrez ajouter une autre bibliothèque .jar qui contient la classe d’annotation Java correspondante. L’ajout d’un fichier source Java qui définit le type d’annotation n’est pas suffisant. Le compilateur Java ne fonctionne pas de la même manière que **le sens**.

En outre, les limitations suivantes s’appliquent :

- Ce processus de `@Target` conversion ne tient pas compte de l’annotation sur le type d’annotation jusqu’à présent.

- Les attributs sur une propriété ne fonctionnent pas. Utilisez des attributs pour getter de propriété ou setter à la place.

## <a name="class-binding"></a>Liaison de classe

Lier une classe signifie écrire un emballage callable géré pour simplifier l’invocation du type Java sous-jacent.

Lier les méthodes virtuelles et abstraites pour permettre le remplacement de C exige Xamarin.Android 4.0. Cependant, toute version de Xamarin.Android peut lier les méthodes non virtuelles, les méthodes statiques ou les méthodes virtuelles sans remplacer.

Une liaison contient généralement les éléments suivants :

- Une [poignée JNI au type Java étant lié](#_Looking_up_Java_Types).

- [JNI field IDs et propriétés pour chaque champ lié](#_Instance_Fields).

- [JNI méthode IDs et méthodes pour chaque méthode liée](#_Instance_Methods).

- Si une sous-classe est nécessaire, le type doit avoir un attribut personnalisé [RegisterAttribute](xref:Android.Runtime.RegisterAttribute) sur la déclaration `true`de type avec [RegisterAttribute.DoNotGenerateAcw](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw) réglé à .

### <a name="declaring-type-handle"></a>Déclarer la poignée de type

Les méthodes de recherche sur le terrain et la méthode exigent une référence d’objet se référant à leur type de déclaration. Par convention, cela se `class_ref` tient dans un domaine :

```csharp
static IntPtr class_ref = JNIEnv.FindClass(CLASS);
```

Consultez la section Références de type `CLASS` [JNI](#_JNI_Type_References) pour plus de détails sur le jeton.

### <a name="binding-fields"></a>Champs de liaison

Les champs de Java sont exposés comme propriétés C, par exemple le champ Java [java.lang.System.in](https://developer.android.com/reference/java/lang/System.html#in) est lié que la propriété C [Java.Lang.JavaSystem.In](xref:Java.Lang.JavaSystem.In).
En outre, puisque JNI fait la distinction entre les champs statiques et les champs d’instance, différentes méthodes sont utilisées lors de la mise en œuvre des propriétés.

La liaison de champ comporte trois ensembles de méthodes :

1. La méthode *d’identification de champ.* La méthode *d’identification sur le terrain* est responsable du retour d’une poignée de champ que la *valeur du champ et* les méthodes de valeur de *champ définies* utiliseront. L’obtention de l’id de champ nécessite de connaître le type de déclaration, le nom du champ et la signature de [type JNI](#JNI_Type_Signatures) du champ.

1. Les méthodes *de valeur de champ d’obtenir.* Ces méthodes nécessitent la poignée de champ et sont responsables de la lecture de la valeur du champ de Java.
    La méthode d’utilisation dépend du type de champ.

1. Les méthodes *de valeur de champ défini.* Ces méthodes nécessitent la poignée de champ et sont responsables de l’écriture de la valeur du champ au sein de Java. La méthode d’utilisation dépend du type de champ.

[Les champs statiques](#_Static_Fields) utilisent les méthodes [JNIEnv.GetStaticFieldID](xref:Android.Runtime.JNIEnv.GetStaticMethodID*), `JNIEnv.GetStatic*Field`et [JNIEnv.SetStaticField.](xref:Android.Runtime.JNIEnv.SetStaticField*)

 [Les champs d’instance](#_Instance_Fields) utilisent les méthodes `JNIEnv.Get*Field` [JNIEnv.GetFieldID](xref:Android.Runtime.JNIEnv.GetFieldID*), et [JNIEnv.SetField.](xref:Android.Runtime.JNIEnv.SetField*)

Par exemple, la `JavaSystem.In` propriété statique peut être mise en œuvre sous la forme de :

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

Remarque : Nous utilisons [InputStreamInvoker.FromJniHandle](xref:Android.Runtime.InputStreamInvoker.FromJniHandle*) pour convertir la `System.IO.Stream` référence JNI en `JniHandleOwnership.TransferLocalRef` une instance, et nous utilisons parce que [JNIEnv.GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*) renvoie une référence locale.

Beaucoup de types [Android.Runtime](xref:Android.Runtime) ont des `FromJniHandle` méthodes qui convertiront une référence JNI dans le type désiré.

### <a name="method-binding"></a>Méthode de liaison

Les méthodes Java sont exposées comme des méthodes de C et comme propriétés C. Par exemple, la méthode Java [java.lang.Runtime.runFinalizersOnExit](https://developer.android.com/reference/java/lang/Runtime.html#runFinalizersOnExit(boolean)) méthode est liée comme la méthode [Java.Lang.Runtime.RunFinalizersOnExit,](xref:Java.Lang.Runtime.RunFinalizersOnExit*) et la méthode [java.lang.Object.getClass](https://developer.android.com/reference/java/lang/Object.html#getClass) est liée comme la propriété [Java.Lang.Object.Class.](xref:Java.Lang.Object.Class)

L’invocation de la méthode est un processus en deux étapes :

1. La *méthode d’identification pour* la méthode à invoquer. La méthode *d’identification de méthode d’identification* est responsable du retour d’une poignée de méthode que les méthodes d’invocation de méthode utiliseront. L’obtention de la méthode id nécessite de connaître le type de déclaration, le nom de la méthode, et la signature de [type JNI](#JNI_Type_Signatures) de la méthode.

1. Appelez la méthode.

Tout comme avec les champs, les méthodes à utiliser pour obtenir la méthode id et invoquer la méthode diffèrent entre les méthodes statiques et les méthodes d’instance.

[Les méthodes statiques](#_Static_Methods_1) utilisent [JNIEnv.GetStaticMethodID()](xref:Android.Runtime.JNIEnv.GetStaticMethodID*) pour rechercher `JNIEnv.CallStatic*Method` la méthode id, et utiliser la famille des méthodes pour l’invocation.

[Les méthodes d’instance](#_Instance_Methods) utilisent [JNIEnv.GetMethodID](xref:Android.Runtime.JNIEnv.GetMethodID*) pour `JNIEnv.Call*Method` rechercher `JNIEnv.CallNonvirtual*Method` la méthode id, et utiliser le et les familles de méthodes pour l’invocation.

La liaison de méthode est potentiellement plus qu’une simple invocation de méthode. La liaison de méthode inclut également permettre de laisser une méthode être remplacée (pour des méthodes abstraites et non finales) ou implémentée (pour les méthodes d’interface). La section [Héritage support, Interfaces](#_Supporting_Inheritance,_Interfaces_1) couvre les complexités du soutien des méthodes virtuelles et des méthodes d’interface.

<a name="_Static_Methods_1" />

#### <a name="static-methods"></a>Méthodes statiques

La liaison d’une méthode statique consiste à `JNIEnv.GetStaticMethodID` `JNIEnv.CallStatic*Method` utiliser pour obtenir une poignée de méthode, puis en utilisant la méthode appropriée, selon le type de retour de la méthode. Ce qui suit est un exemple d’une liaison pour la méthode [Runtime.getRuntime](https://developer.android.com/reference/java/lang/Runtime.html#getRuntime()) :

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

Notez que nous stockons la `id_getRuntime`poignée de méthode dans un champ statique, . Il s’agit d’une optimisation des performances, de sorte que la poignée de la méthode n’a pas besoin d’être recherché sur chaque invocation. Il n’est pas nécessaire de mettre en cache la poignée de la méthode de cette façon. Une fois la poignée de méthode obtenue, [JNIEnv.CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) est utilisé pour invoquer la méthode. `JNIEnv.CallStaticObjectMethod`retourne `IntPtr` un qui contient la poignée de l’instance Java retournée.
[Java.Lang.Object.GetObject&lt;&gt;T (IntPtr, JniHandleOwnership)](xref:Java.Lang.Object.GetObject*) est utilisé pour convertir la poignée Java en une instance d’objet fortement tapée.

#### <a name="non-virtual-instance-method-binding"></a>Méthode d’instance non virtuelle Contraignante

Lier `final` une méthode d’instance, ou une méthode d’instance qui ne nécessite pas de dépassement, implique d’utiliser `JNIEnv.GetMethodID` pour obtenir une poignée de méthode, puis d’utiliser la méthode appropriée, `JNIEnv.Call*Method` selon le type de retour de la méthode. Voici un exemple de liaison `Object.Class` pour le bien :

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

Notez que nous stockons la `id_getClass`poignée de méthode dans un champ statique, .
Il s’agit d’une optimisation des performances, de sorte que la poignée de la méthode n’a pas besoin d’être recherché sur chaque invocation. Il n’est pas nécessaire de mettre en cache la poignée de la méthode de cette façon. Une fois la poignée de méthode obtenue, [JNIEnv.CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) est utilisé pour invoquer la méthode. `JNIEnv.CallStaticObjectMethod`retourne `IntPtr` un qui contient la poignée de l’instance Java retournée.
[Java.Lang.Object.GetObject&lt;&gt;T (IntPtr, JniHandleOwnership)](xref:Java.Lang.Object.GetObject*) est utilisé pour convertir la poignée Java en une instance d’objet fortement tapée.

### <a name="binding-constructors"></a>Constructeurs contraignants

Les constructeurs sont des `"<init>"`méthodes Java avec le nom . Tout comme avec les `JNIEnv.GetMethodID` méthodes d’instance Java, est utilisé pour rechercher la poignée constructeur. Contrairement aux méthodes Java, les méthodes [JNIEnv.NewObject](xref:Android.Runtime.JNIEnv.NewObject*) sont utilisées pour invoquer la poignée de méthode constructeur. La valeur `JNIEnv.NewObject` de retour est une référence locale JNI:

```csharp
int value = 42;
IntPtr class_ref    = JNIEnv.FindClass ("java/lang/Integer");
IntPtr id_ctor_I    = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
IntPtr lrefInstance = JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value));
// Dispose of lrefInstance, class_ref…
```

Normalement, une reliure de classe sous-classe [Java.Lang.Object](xref:Java.Lang.Object).
Lors de `Java.Lang.Object`la sous-classification , une sémantique supplémentaire entre en jeu: une `Java.Lang.Object` instance maintient une référence globale à une instance Java à travers la `Java.Lang.Object.Handle` propriété.

1. Le `Java.Lang.Object` constructeur par défaut attribuera une instance Java.

1. Si le type `RegisterAttribute` a `RegisterAttribute.DoNotGenerateAcw` `true` un , et `RegisterAttribute.Name` est , puis une instance du type est créé par son constructeur par défaut.

1. Dans le cas contraire, l’Android [Callable Wrapper](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) correspondant à `this.GetType` est instantanée par son constructeur par défaut. Android Callable Wrappers sont générés `Java.Lang.Object` lors de `RegisterAttribute.DoNotGenerateAcw` la création `true`de paquets pour chaque sous-classe pour laquelle n’est pas réglé à .

Pour les types qui ne sont pas des fixations de `Mono.Samples.HelloWorld.HelloAndroid` classe, c’est `mono.samples.helloworld.HelloAndroid` la sémantique attendue: l’instantané d’une instance C devrait construire une instance Java qui est un généré Android Callable Wrapper.

Pour les fixations de classe, cela peut être le comportement correct si le type Java contient un constructeur par défaut et / ou aucun autre constructeur doit être invoqué. Dans le cas contraire, un constructeur doit être fourni qui effectue les actions suivantes :

1. Invoquant le [Java.Lang.Object (IntPtr, JniHandleOwnership)](xref:Java.Lang.Object#ctor*) `Java.Lang.Object` au lieu du constructeur par défaut. Ceci est nécessaire pour éviter de créer une nouvelle instance Java.

1. Vérifiez la valeur de [Java.Lang.Object.Handle](xref:Java.Lang.Object.Handle) avant de créer des instances Java. La `Object.Handle` propriété aura une `IntPtr.Zero` valeur autre que si un Emballage Android Callable a été construit dans le code Java, et la liaison de classe est en cours de construction pour contenir l’instance créée Android Callable Wrapper. Par exemple, lorsque `mono.samples.helloworld.HelloAndroid` Android crée une instance, l’Emballage Android Callable sera créé en premier, et le constructeur Java `HelloAndroid` va créer une instance du type correspondant, `Mono.Samples.HelloWorld.HelloAndroid` avec la `Object.Handle` propriété étant réglée à l’instance Java avant l’exécution du constructeur.

1. Si le type actuel de temps d’exécution n’est pas le même que le type de déclaration, alors une instance de l’emballage Android Callable correspondant doit être créé, et utiliser [Object.SetHandle](xref:Java.Lang.Object.SetHandle*) pour stocker la poignée retournée par [JNIEnv.CreateInstance](xref:Android.Runtime.JNIEnv.CreateInstance*).

1. Si le type actuel de temps d’exécution est le même que le type de déclaration, puis `JNIEnv.NewInstance` invoquer le constructeur Java et utiliser [Object.SetHandle](xref:Java.Lang.Object.SetHandle*) pour stocker la poignée retournée par .

Par exemple, considérez le [constructeur java.lang.Integer (int).](https://developer.android.com/reference/java/lang/Integer.html#Integer(int)) Ceci est lié comme:

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

Les méthodes [JNIEnv.CreateInstance](xref:Android.Runtime.JNIEnv.CreateInstance*) sont des `JNIEnv.FindClass` `JNIEnv.GetMethodID`aides `JNIEnv.NewObject`pour `JNIEnv.DeleteGlobalReference` effectuer un `JNIEnv.FindClass`, , , et sur la valeur retournée de . Pour plus d'informations, consultez la section suivante.

<a name="_Supporting_Inheritance,_Interfaces_1" />

### <a name="supporting-inheritance-interfaces"></a>Soutenir l’héritage, interfaces

La sous-classification d’un type Java ou la mise en œuvre d’une interface `Java.Lang.Object` Java nécessite la génération d’emballages [Callables Android](~/android/platform/java-integration/android-callable-wrappers.md) (ACW) qui sont générés pour chaque sous-classe pendant le processus d’emballage. La génération ACW est contrôlée par l’attribut personnalisé [Android.Runtime.RegisterAttribute.](xref:Android.Runtime.RegisterAttribute)

Pour les types `[Register]` C, le constructeur d’attributs personnalisé nécessite un argument : la [référence de type simplifié JNI](#_Simplified_Type_References_1) pour le type Java correspondant. Cela permet de fournir des noms différents entre Java et C.

Avant Xamarin.Android 4.0, `[Register]` l’attribut personnalisé n’était pas disponible pour "alias" types Java existants. C’est parce que le processus de génération `Java.Lang.Object` ACW générerait des ACF pour chaque sous-classe rencontrée.

Xamarin.Android 4.0 a présenté la propriété [RegisterAttribute.DoNotGenerateAcw.](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw) Cette propriété demande au processus de génération ACW de *sauter* le type annoté, permettant la déclaration de nouveaux emballages callables gérés qui n’entraîneront pas la production d’ACW au moment de la création de l’emballage. Cela permet de lier les types Java existants. Par exemple, considérez la `Adder`classe Java simple `add`suivante, , qui contient une méthode, , qui ajoute auxintégrés et retourne le résultat:

```java
package mono.android.test;
public class Adder {
    public int add (int a, int b) {
        return a + b;
    }
}
```

Le `Adder` type pourrait être lié comme:

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

Ici, `Adder` le type C `Adder` alias *le* type Java. L’attribut `[Register]` est utilisé pour spécifier le nom JNI du `mono.android.test.Adder` type Java, et la propriété est utilisée pour inhiber la `DoNotGenerateAcw` génération ACW. Cela se traduira par la génération `ManagedAdder` d’un ACW pour `mono.android.test.Adder` le type, qui sous-classe correctement le type. Si `RegisterAttribute.DoNotGenerateAcw` la propriété n’avait pas été utilisée, alors le processus `mono.android.test.Adder` de construction Xamarin.Android aurait généré un nouveau type Java. Cela entraînerait des erreurs `mono.android.test.Adder` de compilation, car le type serait présent deux fois, dans deux fichiers distincts.

### <a name="binding-virtual-methods"></a>Méthodes virtuelles contraignantes

`ManagedAdder`sous-classe le `Adder` type Java, mais il n’est `Adder` pas particulièrement intéressant: le `ManagedAdder` type C ne définit pas les méthodes virtuelles, donc ne peut pas remplacer quoi que ce soit.

Les `virtual` méthodes contraignantes pour permettre la suppression par des sous-classes exigent plusieurs choses qui doivent être faites qui entrent dans les deux catégories suivantes :

1. **Méthode de liaison**

1. **Enregistrement de la méthode**

#### <a name="method-binding"></a>Méthode de liaison

Une méthode contraignante nécessite l’ajout de `Adder` deux `ThresholdType`membres `ThresholdClass`de soutien à la définition de CMD : , et .

##### <a name="thresholdtype"></a>ThresholdType

La `ThresholdType` propriété retourne le type actuel de lier :

```csharp
partial class Adder {
    protected override System.Type ThresholdType {
        get {
            return typeof (Adder);
        }
    }
}
```

`ThresholdType`est utilisé dans la méthode de liaison pour déterminer quand il doit effectuer l’expédition virtuelle vs méthode non virtuelle. Il doit toujours `System.Type` renvoyer une instance qui correspond au type déclarant C.

##### <a name="thresholdclass"></a>SeuilClass

La `ThresholdClass` propriété renvoie la référence de classe JNI pour le type lié :

```csharp
partial class Adder {
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```

`ThresholdClass`est utilisé dans la méthode de liaison lors de l’invocation de méthodes non virtuelles.

#### <a name="binding-implementation"></a>Mise en œuvre contraignante

La méthode de mise en œuvre contraignante est responsable de l’invocation en temps d’exécution de la méthode Java. Il contient `[Register]` également une déclaration d’attribut personnalisé qui fait partie de l’enregistrement de la méthode, et sera discuté dans la section d’enregistrement de la méthode :

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

Le `id_add` champ contient la méthode ID pour la méthode Java à invoquer. La `id_add` valeur est `JNIEnv.GetMethodID`obtenue à partir de`class_ref`, qui nécessite`"add"`la classe déclarante ( ),`"(II)I"`le nom de méthode Java ( ), et la signature JNI de la méthode ( ).

Une fois l’ID `GetType` de méthode `ThresholdType` obtenu, est comparé pour déterminer si la répartition virtuelle ou non virtuelle est nécessaire. L’expédition virtuelle `GetType` `ThresholdType`est `Handle` nécessaire lorsque les correspondances, comme peut se référer à une sous-classe java-alloué qui l’emporte sur la méthode.

Quand `GetType` ne correspond `ThresholdType` `Adder` pas, a été sous-classé `ManagedAdder`(par `Adder.Add` exemple par), et la mise en `base.Add`œuvre ne sera invoquée que si la sous-classe invoquée . Il s’agit de l’affaire `ThresholdClass` de répartition non virtuelle, qui est là où vient. `ThresholdClass`précise quelle classe Java fournira la mise en œuvre de la méthode à invoquer.

#### <a name="method-registration"></a>Enregistrement de la méthode

Supposons que `ManagedAdder` nous avons une `Adder.Add` définition mise à jour qui l’emporte sur la méthode:

```csharp
partial class ManagedAdder : Adder {
    public override int Add (int a, int b) {
        return (a*2) + (b*2);
    }
}
```

Rappel `Adder.Add` qui `[Register]` avait un attribut personnalisé:

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
```

Le `[Register]` constructeur d’attributs personnalisé accepte trois valeurs :

1. Le nom de la `"add"` méthode Java, dans ce cas.

1. La signature de type JNI de la méthode, `"(II)I"` dans ce cas.

1. La méthode de `GetAddHandler` *connecteur* , dans ce cas.
    Les méthodes de connecteur seront discutées plus tard.

Les deux premiers paramètres permettent au processus de génération ACW de générer une déclaration de méthode pour passer outre à la méthode. L’ACW qui en résulterait contiendrait une partie du code suivant :

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

Notez `@Override` qu’une méthode est déclarée, qui délègue une `n_`méthode préfixée du même nom. Cela garantit que lorsque `ManagedAdder.add`le `ManagedAdder.n_add` code Java invoque, sera invoqué, `ManagedAdder.Add` ce qui permettra l’exécution de la méthode C.

Ainsi, la question la `ManagedAdder.n_add` plus importante `ManagedAdder.Add`: comment est-il branché ?

Les `native` méthodes Java sont enregistrées avec le temps d’exécution Java (l’Android) à travers la [fonction JNI RegisterNatives](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp17734).
`RegisterNatives`prend un tableau de structures contenant le nom de la méthode Java, la signature de type JNI, et un pointeur de fonction à invoquer qui suit [convention d’appel JNI](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/design.html#wp715).
Le pointeur de fonction doit être une fonction qui prend deux arguments de pointeur suivis par les paramètres de la méthode. La `ManagedAdder.n_add` méthode Java doit être implémentée par le biais d’une fonction qui a le prototype C suivant:

```csharp
int FunctionName(JNIEnv *env, jobject this, int a, int b)
```

Xamarin.Android n’expose `RegisterNatives` pas une méthode. Au lieu de cela, l’ACW et `RegisterNatives`le MCW fournissent ensemble les informations nécessaires pour invoquer : l’ACW contient le nom de la méthode et la signature de type JNI, la seule chose qui manque est un pointeur de fonction à brancher.

C’est là que la *méthode du connecteur* entre en vigueur. Le `[Register]` troisième paramètre d’attribut personnalisé est le nom d’une méthode définie dans le type `System.Delegate`enregistré ou une classe de base du type enregistré qui n’accepte aucun paramètre et renvoie un . Le `System.Delegate` retour se réfère à une méthode qui a la signature de fonction JNI correcte. Enfin, le délégué que la méthode de connexion retourne *doit* être enracinée de sorte que le GC ne le recueille pas, comme le délégué est fourni à Java.

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

La `GetAddHandler` méthode `Func<IntPtr, IntPtr, int, int,
int>` crée un délégué `n_Add` qui se réfère à la méthode, puis invoque [JNINativeWrapper.CreateDelegate](xref:Android.Runtime.JNINativeWrapper.CreateDelegate*).
`JNINativeWrapper.CreateDelegate`enveloppe la méthode fournie dans un bloc d’essai/capture, de sorte que toutes les exceptions non gérées sont manipulées et se traduira par l’augmentation de [l’événement AndroidEvent.UnhandledExceptionRaiser.](xref:Android.Runtime.AndroidEnvironment.UnhandledExceptionRaiser) Le délégué qui en `cb_add` résulte est stocké dans la variable statique de sorte que le GC ne libérera pas le délégué.

Enfin, `n_Add` la méthode est responsable de l’enchôtage des paramètres de l’INI aux types gérés correspondants, puis de déléguer l’appel de méthode.

Remarque : `JniHandleOwnership.DoNotTransfer` Utilisez toujours lors de l’obtention d’un MCW sur une instance Java. Les traiter comme une référence `JNIEnv.DeleteLocalRef`locale (et&gt; donc&gt; appeler ) brisera géré - Java - transitions de pile gérée.

### <a name="complete-adder-binding"></a>Liaison Adder complète

La liaison gérée `mono.android.tests.Adder` complète pour le type est :

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

Lors de l’écriture d’un type qui correspond aux critères suivants :

1. Sous-classes`Java.Lang.Object`

1. A `[Register]` un attribut personnalisé

1. `RegisterAttribute.DoNotGenerateAcw` est `true`

Ensuite, pour l’interaction GC le type ne `Java.Lang.Object` `Java.Lang.Object` *doit pas* avoir de champs qui peuvent se référer à une ou sous-classe au moment de l’exécution. Par exemple, les `System.Object` champs de type et tout type d’interface ne sont pas autorisés. Les types qui `Java.Lang.Object` ne peuvent pas `System.String` se `List<int>`référer à des cas sont autorisés, tels que et . Cette restriction vise à empêcher la collecte prématurée d’objets par le GC.

Si le type doit contenir un champ `Java.Lang.Object` d’instance qui peut `System.WeakReference` `GCHandle`se référer à une instance, alors le type de champ doit être ou .

## <a name="binding-abstract-methods"></a>Méthodes abstraites contraignantes

Les `abstract` méthodes de liaison sont en grande partie identiques aux méthodes virtuelles contraignantes. Il n’y a que deux différences :

1. La méthode abstraite est abstraite. Il conserve toujours `[Register]` l’attribut et l’enregistrement de méthode `Invoker` associé, la liaison de méthode est juste déplacé au type.

1. Un non-type `abstract` `Invoker` est créé qui sous-classe le type abstrait. Le `Invoker` type doit l’emporter sur toutes les méthodes abstraites déclarées dans la classe de base, et la mise en œuvre annulée est la mise en œuvre de liaison de méthode, bien que le cas de répartition non virtuel puisse être ignoré.

Par exemple, supposons `mono.android.test.Adder.add` que `abstract`la méthode ci-dessus étaient . La liaison Cmd changerait de sorte `Adder.Add` `AdderInvoker` qu’elle soit `Adder.Add`abstraite, et un nouveau type serait défini qui implémentait :

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

Le `Invoker` type n’est nécessaire que lors de l’obtention de références JNI à des instances créées par Java.

## <a name="binding-interfaces"></a>Interfaces contraignantes

Les interfaces de liaison sont conceptuellement similaires aux classes de liaison contenant des méthodes virtuelles, mais beaucoup de détails diffèrent de manière subtile (et pas si subtile). Considérez la [déclaration d’interface Java](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/Adder.java#L14)suivante :

```csharp
public interface Progress {
    void onAdd(int[] values, int currentIndex, int currentSum);
}
```

Les liaisons d’interface ont deux parties : la définition de l’interface C et une définition Invoker pour l’interface.

### <a name="interface-definition"></a>Définition d'interface

La définition de l’interface CMD doit répondre aux exigences suivantes :

- La définition de `[Register]` l’interface doit avoir un attribut personnalisé.

- La définition de `IJavaObject interface`l’interface doit étendre le .
    Si vous ne le faites pas, les ACW n’hériteront pas de l’interface Java.

- Chaque méthode d’interface doit contenir un `[Register]` attribut spécifiant le nom correspondant de la méthode Java, la signature JNI et la méthode du connecteur.

- La méthode du connecteur doit également spécifier le type sur lequel la méthode du connecteur peut être localisée.

Lorsque `abstract` la `virtual` liaison et les méthodes, la méthode de connecteur serait recherchée dans la hiérarchie de l’héritage du type enregistré. Les interfaces ne peuvent pas avoir de méthodes contenant des corps, de sorte que cela ne fonctionne pas, donc l’exigence qu’un type soit spécifié indiquant où la méthode de connecteur est situé. Le type est spécifié dans la chaîne `':'`de méthode de connecteur, après un côlon, et doit être le nom de type qualifié d’assemblage du type contenant l’invocateur.

Les déclarations de méthode d’interface sont une traduction de la méthode Java correspondante en utilisant des types *compatibles.* Pour les types de Java builtin, les types compatibles `int` sont les `int`types C correspondants, par exemple Java est C . Pour les types de référence, le type compatible est un type qui peut fournir une poignée JNI du type Java approprié.

Les membres de l’interface ne &ndash; seront pas directement invoqués par &ndash; l’invocation Java sera négocié par le type Invoker afin une certaine quantité de flexibilité est autorisée.

L’interface Java Progress peut être [déclarée dans C ' comme](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L83):

```csharp
[Register ("mono/android/test/Adder$Progress", DoNotGenerateAcw=true)]
public interface IAdderProgress : IJavaObject {
    [Register ("onAdd", "([III)V",
            "GetOnAddHandler:Mono.Samples.SanityTests.IAdderProgressInvoker, SanityTests, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null")]
    void OnAdd (JavaArray<int> values, int currentIndex, int currentSum);
}
```

Remarquez dans ce qui `int[]` précède que nous cartographions le paramètre Java à une [auberge&lt;&gt;JavaArray](xref:Android.Runtime.JavaArray`1).
Ce n’est pas nécessaire: nous aurions `int[]`pu le `IList<int>`lier à un C , ou un , ou quelque chose d’autre entièrement. Quel que soit `Invoker` le type choisi, les besoins `int[]` doivent être en mesure de le traduire en un type Java pour l’invocation.

### <a name="invoker-definition"></a>Définition de l’invocateur

La `Invoker` définition de `Java.Lang.Object`type doit hériter, implémenter l’interface appropriée et fournir toutes les méthodes de connexion référencées dans la définition de l’interface. Il y a une autre suggestion qui `class_ref` diffère d’une obligation de classe : les DIU sur le terrain et la méthode devraient être des membres d’instance, et non des membres statiques.

La raison de préférer les membres `JNIEnv.GetMethodID` d’instance a à voir avec le comportement dans le runtime Android. (Cela peut être le comportement Java ainsi; il n’a pas été testé.) `JNIEnv.GetMethodID` retourne nul lorsque vous cherchez une méthode qui provient d’une interface implémentée et non de l’interface déclarée. Considérez l’interface [java.util.SortedMap&lt;K, V&gt; ](https://developer.android.com/reference/java/util/SortedMap.html) Java, qui implémente [l’interface java.util.Map,&lt;V.&gt; ](https://developer.android.com/reference/java/util/Map.html) La carte fournit une méthode [claire,](https://developer.android.com/reference/java/util/Map.html#clear()) donc une définition apparemment raisonnable `Invoker` pour TriedMap serait:

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

Ce qui précède `JNIEnv.GetMethodID` échouera parce qu’il reviendra `null` en regardant la `Map.clear` méthode à travers l’instance de `SortedMap` classe.

Il existe deux solutions à cela: suivre quelle interface `class_ref` chaque méthode vient, et ont un pour chaque interface, ou tout garder en tant que membres d’instance et effectuer le lookup méthode sur le type de classe le plus dérivé, pas le type d’interface. Ce dernier est fait en **Mono.Android.dll**.

La définition Invoker comporte six sections `Dispose` : le `ThresholdType` `ThresholdClass` constructeur, `GetObject` la méthode, la méthode et les membres, la méthode, la mise en œuvre de la méthode d’interface et la mise en œuvre de la méthode de connecteur.

#### <a name="constructor"></a>Constructeur

Le constructeur doit rechercher la classe de temps d’exécution de l’instance `class_ref` en cours d’invoquage et stocker la classe de temps d’exécution dans le domaine de l’instance:

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

Remarque : `Handle` La propriété doit être utilisée dans `handle` le corps du constructeur, et `handle` non dans le paramètre, comme sur Android v4.0 le paramètre peut être invalide après que le constructeur de base a terminé l’exécution.

#### <a name="dispose-method"></a>Dispose, méthode

La `Dispose` méthode doit libérer la référence globale allouée dans le constructeur :

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

Les `ThresholdType` `ThresholdClass` membres et les membres sont identiques à ce que l’on trouve dans une classe de liaison :

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

Une `GetObject` méthode statique est nécessaire pour prendre en charge [Extensions.JavaCast&lt;&gt;T ()](xref:Android.Runtime.Extensions.JavaCast*):

```csharp
partial class IAdderProgressInvoker {
    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }
}
```

#### <a name="interface-methods"></a>Méthodes d'interface

Chaque méthode de l’interface doit avoir une implémentation, qui invoque la méthode Java correspondante par le biais de JNI:

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

Les méthodes de connecteur et l’infrastructure de soutien sont responsables de l’enchrage des paramètres de l’INM aux types Cmd appropriés. Le `int[]` paramètre Java sera adopté `jintArray`en tant `IntPtr` que JNI , qui est un dans C . Le `IntPtr` doit être mis `JavaArray<int>` en œillé à un afin de soutenir l’invocation de l’interface C:

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

Si `int[]` on `JavaList<int>`préfère, alors [JNIEnv.GetArray()](xref:Android.Runtime.JNIEnv.GetArray*) pourrait être utilisé à la place:

```csharp
int[] _values = (int[]) JNIEnv.GetArray(values, JniHandleOwnership.DoNotTransfer, typeof (int));
```

Notez, cependant, que `JNIEnv.GetArray` les copies de l’ensemble du tableau entre les VM, donc pour les grands tableaux cela pourrait entraîner beaucoup de pression GC ajouté.

### <a name="complete-invoker-definition"></a>Définition complète de l’invocateur

La [définition complète de IAdderProgressInvoker](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L88):

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

Beaucoup de méthodes JNIEnv retourner *références*objet `GCHandle` *JNI* , qui sont similaires à s. JNI fournit trois types différents de références d’objets : les références locales, les références globales et les références mondiales faibles. Tous les trois `System.IntPtr`sont représentés comme , *mais* (selon `IntPtr`la section `JNIEnv` JNI Function Types) tous les revenus des méthodes ne sont pas des références. Par exemple, [JNIEnv.GetMethodID](xref:Android.Runtime.JNIEnv.GetMethodID*) retourne un `IntPtr`, mais il ne retourne `jmethodID`pas une référence d’objet, il retourne un . Consultez la [documentation de la fonction JNI](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html) pour plus de détails.

Les références locales sont créées par *la plupart des* méthodes de création de références.
Android ne permet qu’à un nombre limité de références locales d’exister à un moment donné, généralement 512. Les références locales peuvent être supprimées via [JNIEnv.DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*).
Contrairement à JNI, toutes les méthodes de référence JNIEnv qui renvoient les références d’objet renvoient des références locales ; [JNIEnv.FindClass](xref:Android.Runtime.JNIEnv.FindClass*) renvoie une référence *mondiale.* Il est fortement recommandé de supprimer les références locales aussi rapidement que vous le pouvez, `JniHandleOwnership.TransferLocalRef` éventuellement en construisant un [Java.Lang.Object](xref:Java.Lang.Object) autour de l’objet et en spécifiant à la [Java.Lang.Object (poignée IntPtr, transfert JniHandleOwnership)](xref:Java.Lang.Object#ctor*) constructeur.

Des références globales sont créées par [JNIEnv.NewGlobalRef](xref:Android.Runtime.JNIEnv.NewGlobalRef*) et [JNIEnv.FindClass](xref:Android.Runtime.JNIEnv.FindClass*).
Ils peuvent être détruits avec [JNIEnv.DeleteGlobalRef](xref:Android.Runtime.JNIEnv.DeleteGlobalRef*).
Les émulateurs ont une limite de 2 000 références mondiales exceptionnelles, tandis que les appareils matériels ont une limite d’environ 52 000 références mondiales.

Les références mondiales faibles ne sont disponibles que sur Android v2.2 (Froyo) et plus tard. Les références mondiales faibles peuvent être supprimées avec [JNIEnv.DeleteWeakGlobalRef](xref:Android.Runtime.JNIEnv.DeleteWeakGlobalRef*).

### <a name="dealing-with-jni-local-references"></a>Traiter avec les références locales de la JNI

Le [JNIEnv.GetObjectField](xref:Android.Runtime.JNIEnv.GetObjectField*), [JNIEnv.GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*), [JNIEnv.CallObjectMethod](xref:Android.Runtime.JNIEnv.CallObjectMethod*), [JNIEnv.CallNonvirtualObjectMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualObjectMethod*) et [JNIEnv.CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) méthodes retourner un `IntPtr` `IntPtr.Zero` qui contient `null`une référence locale JNI à un objet Java, ou si Java retourné . En raison du nombre limité de références locales qui peuvent être en suspens à la fois (512 entrées), il est souhaitable de s’assurer que les références sont supprimées en temps opportun. Il y a trois façons de traiter les références locales `Java.Lang.Object` : les supprimer `Java.Lang.Object.GetObject<T>()` explicitement, créer une instance pour les tenir et utiliser pour créer un emballage callable géré autour d’eux.

### <a name="explicitly-deleting-local-references"></a>Suppression explicite des références locales

[JNIEnv.DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*) est utilisé pour supprimer les références locales. Une fois que la référence locale a été supprimée, elle ne `JNIEnv.DeleteLocalRef` peut plus être utilisée, il faut donc veiller à ce que ce soit la dernière chose faite avec la référence locale.

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
try {
    // Do something with `lref`
}
finally {
    JNIEnv.DeleteLocalRef (lref);
}
```

### <a name="wrapping-with-javalangobject"></a>Emballage avec Java.Lang.Object

`Java.Lang.Object`fournit un [constructeur Java.Lang.Object (IntPtr handle, JniHandleOwnership transfer)](xref:Java.Lang.Object#ctor*) qui peut être utilisé pour envelopper une référence JNI sortante. Le paramètre [JniHandleOwnership](xref:Android.Runtime.JniHandleOwnership) `IntPtr` détermine comment le paramètre doit être traité :

- [JniHandleOwnership.DoNotTransfer](xref:Android.Runtime.JniHandleOwnership.DoNotTransfer) &ndash; L’instance créée `Java.Lang.Object` créera une `handle` nouvelle `handle` référence globale à partir du paramètre, et est inchangée.
    L’appelant est responsable `handle` de la libération, si nécessaire.

- [JniHandleOwnership.TransferLocalRef](xref:Android.Runtime.JniHandleOwnership.TransferLocalRef) &ndash; `Java.Lang.Object` L’instance créée créera une `handle` nouvelle `handle` référence globale à partir du paramètre, et est supprimée avec [JNIEnv.DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*) . L’appelant ne `handle` doit pas libérer, et ne doit pas utiliser `handle` après que le constructeur termine l’exécution.

- [JniHandleOwnership.TransferGlobalRef](xref:Android.Runtime.JniHandleOwnership.TransferLocalRef) &ndash; L’instance créée `Java.Lang.Object` prendra `handle` en charge la propriété du paramètre. L’appelant ne `handle` doit pas être libre.

Puisque les méthodes d’invocation de `JniHandleOwnership.TransferLocalRef` méthode JNI renvoient les arbitres locaux, seraient normalement utilisés :

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef);
```

La référence globale créée ne `Java.Lang.Object` sera pas libérée tant que l’instance n’aura pas été recueillie. Si vous le pouvez, l’élimination de l’instance libérera la référence globale, accélérant les collectes d’ordures :

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
using (var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef)) {
    // use value ...
}
```

### <a name="using-javalangobjectgetobjectlttgt"></a>Utilisation de Java.Lang.Object.GetObject&lt;T&gt;()

`Java.Lang.Object`fournit une méthode [Java.Lang.Object.GetObject&lt;T&gt;(poignée IntPtr, transfert de JniHandleOwnership)](xref:Java.Lang.Object.GetObject*) qui peut être utilisée pour créer un emballage callable géré du type spécifié.

Le `T` type doit répondre aux exigences suivantes :

1. `T`doit être un type de référence.

1. `T` doit implémenter l'interface `IJavaObject`.

1. S’il `T` ne s’agit `T` pas d’une classe ou `(IntPtr,
    JniHandleOwnership)` d’une interface abstraite, il doit alors fournir un constructeur avec les types de paramètres .

1. `T` S’il s’agit d’une classe abstraite `T` ou d’une interface, il *doit* y avoir un *invocateur* disponible pour . Un invocateur est un type `T` non abstrait `T` qui hérite ou `T` met en œuvre, et a le même nom qu’avec un suffixe Invoker. Par exemple, si T `Java.Lang.IRunnable` est l’interface, alors le type `Java.Lang.IRunnableInvoker` doit exister et doit contenir le constructeur requis. `(IntPtr,
    JniHandleOwnership)`

Puisque les méthodes d’invocation de `JniHandleOwnership.TransferLocalRef` méthode JNI renvoient les arbitres locaux, seraient normalement utilisés :

```csharp
IntPtr lrefString = JNIEnv.CallObjectMethod(instance, methodID);
Java.Lang.String value = Java.Lang.Object.GetObject<Java.Lang.String>( lrefString, JniHandleOwnership.TransferLocalRef);
```

<a name="_Looking_up_Java_Types" />

## <a name="looking-up-java-types"></a>Rechercher des types java

Pour rechercher un champ ou une méthode dans JNI, le type de déclaration pour le champ ou la méthode doit être consulté en premier. La méthode [Android.Runtime.JNIEnv.FindClass(string)](xref:Android.Runtime.JNIEnv.FindClass*)) est utilisée pour rechercher les types Java. Le paramètre de chaîne est la *référence de type simplifiée* ou la *référence de type complet* pour le type Java. Consultez la [section Références de type JNI](#_JNI_Type_References) pour plus de détails sur les références simplifiées et complètes.

Remarque : Contrairement `JNIEnv` à toutes les `FindClass` autres méthodes qui renvoient les instances d’objets, renvoie une référence globale, et non une référence locale.

<a name="_Instance_Fields" />

## <a name="instance-fields"></a>Champs d’instance

Les champs sont manipulés par *des pièces d’Œd de champ*. Les cartes d’ID de champ sont obtenues via [JNIEnv.GetFieldID](xref:Android.Runtime.JNIEnv.GetFieldID*), qui exige la classe dans laquelle le champ est défini, le nom du champ et la signature de [type JNI](#JNI_Type_Signatures) du champ.

Les Œd de champ n’ont pas besoin d’être libérées et sont valides tant que le type Java correspondant est chargé. (Android ne prend actuellement pas en charge le déchargement de classe.)

Il existe deux ensembles de méthodes pour manipuler les champs d’instance : l’un pour la lecture des champs d’instance et l’autre pour l’écriture des champs d’instance. Tous les ensembles de méthodes nécessitent une pièce d’identité sur le terrain pour lire ou écrire la valeur du champ.

### <a name="reading-instance-field-values"></a>Lecture des valeurs de terrain d’instance

L’ensemble de méthodes de lecture des valeurs de champ d’instance suit le modèle de nommage :

```csharp
* JNIEnv.Get*Field(IntPtr instance, IntPtr fieldID);
```

où `*` est le type de champ:

- [JNIEnv.GetObjectField](xref:Android.Runtime.JNIEnv.GetObjectField*) &ndash; Lisez la valeur de n’importe quel champ d’instance qui n’est pas un type builtin, tels que `java.lang.Object` , les tableaux, et les types d’interface. La valeur retournée est une référence locale JNI.

- [JNIEnv.GetBooleanField](xref:Android.Runtime.JNIEnv.GetBooleanField*) &ndash; Lisez la `bool` valeur des champs d’instance.

- [JNIEnv.GetByteField](xref:Android.Runtime.JNIEnv.GetByteField*) &ndash; Lisez la `sbyte` valeur des champs d’instance.

- [JNIEnv.GetCharField](xref:Android.Runtime.JNIEnv.GetCharField*) &ndash; Lisez la `char` valeur des champs d’instance.

- [JNIEnv.GetShortField](xref:Android.Runtime.JNIEnv.GetShortField*) &ndash; Lisez la `short` valeur des champs d’instance.

- [JNIEnv.GetIntField](xref:Android.Runtime.JNIEnv.GetIntField*) &ndash; Lisez la `int` valeur des champs d’instance.

- [JNIEnv.GetLongField](xref:Android.Runtime.JNIEnv.GetLongField*) &ndash; Lisez la `long` valeur des champs d’instance.

- [JNIEnv.GetFloatField](xref:Android.Runtime.JNIEnv.GetFloatField*) &ndash; Lisez la `float` valeur des champs d’instance.

- [JNIEnv.GetDoubleField](xref:Android.Runtime.JNIEnv.GetDoubleField*) &ndash; Lisez la `double` valeur des champs d’instance.

### <a name="writing-instance-field-values"></a>Rédaction des valeurs de terrain d’instance

L’ensemble de méthodes d’écriture des valeurs de champ d’instance suit le modèle de nommage :

```csharp
JNIEnv.SetField(IntPtr instance, IntPtr fieldID, Type value);
```

*où le type* est le type de champ:

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Écrivez la valeur de n’importe quel `java.lang.Object` champ qui n’est pas un type builtin, tel que , les tableaux, et les types d’interface. La `IntPtr` valeur peut être une référence locale JNI, référence mondiale `IntPtr.Zero` JNI, JNI faible référence mondiale, ou (pour `null` ).

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Écrivez `bool` la valeur des champs d’instance.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Écrivez `sbyte` la valeur des champs d’instance.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Écrivez `char` la valeur des champs d’instance.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Écrivez `short` la valeur des champs d’instance.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Écrivez `int` la valeur des champs d’instance.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Écrivez `long` la valeur des champs d’instance.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Écrivez `float` la valeur des champs d’instance.

- [JNIEnv.SetField](xref:Android.Runtime.JNIEnv.SetField*) &ndash; ) Écrivez `double` la valeur des champs d’instance.

<a name="_Static_Fields" />

## <a name="static-fields"></a>Champs statiques

Les champs statiques sont manipulés par *des pièces d’IED de champ.* Les cartes d’ID de champ sont obtenues via [JNIEnv.GetStaticFieldID](xref:Android.Runtime.JNIEnv.GetStaticFieldID*), qui exige la classe dans laquelle le champ est défini, le nom du champ et la signature de [type JNI](#JNI_Type_Signatures) du champ.

Les Œd de champ n’ont pas besoin d’être libérées et sont valides tant que le type Java correspondant est chargé. (Android ne prend actuellement pas en charge le déchargement de classe.)

Il existe deux ensembles de méthodes pour manipuler les champs statiques : l’un pour la lecture des champs d’instance et l’autre pour l’écriture des champs d’instance. Tous les ensembles de méthodes nécessitent une pièce d’identité sur le terrain pour lire ou écrire la valeur du champ.

### <a name="reading-static-field-values"></a>Lecture des valeurs statiques de champ

L’ensemble de méthodes de lecture des valeurs statiques de champ suit le modèle de nommage :

```csharp
* JNIEnv.GetStatic*Field(IntPtr class, IntPtr fieldID);
```

où `*` est le type de champ:

- [JNIEnv.GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*) &ndash; Lisez la valeur de tout champ statique qui n’est pas un type builtin, tels que `java.lang.Object` , les tableaux, et les types d’interface. La valeur retournée est une référence locale JNI.

- [JNIEnv.GetStaticBooleanField](xref:Android.Runtime.JNIEnv.GetStaticBooleanField*) &ndash; Lisez la `bool` valeur des champs statiques.

- [JNIEnv.GetStaticByteField](xref:Android.Runtime.JNIEnv.GetStaticByteField*) &ndash; Lisez la `sbyte` valeur des champs statiques.

- [JNIEnv.GetStaticCharField](xref:Android.Runtime.JNIEnv.GetStaticCharField*) &ndash; Lisez la `char` valeur des champs statiques.

- [JNIEnv.GetStaticShortField](xref:Android.Runtime.JNIEnv.GetStaticShortField*) &ndash; Lisez la `short` valeur des champs statiques.

- [JNIEnv.GetStaticLongField](xref:Android.Runtime.JNIEnv.GetStaticLongField*) &ndash; Lisez la `long` valeur des champs statiques.

- [JNIEnv.GetStaticFloatField](xref:Android.Runtime.JNIEnv.GetStaticFloatField*) &ndash; Lisez la `float` valeur des champs statiques.

- [JNIEnv.GetStaticDoubleField](xref:Android.Runtime.JNIEnv.GetStaticDoubleField*) &ndash; Lisez la `double` valeur des champs statiques.

### <a name="writing-static-field-values"></a>Rédaction de valeurs de terrain statiques

L’ensemble de méthodes d’écriture des valeurs statiques de champ suit le modèle de nommage :

```csharp
JNIEnv.SetStaticField(IntPtr class, IntPtr fieldID, Type value);
```

*où le type* est le type de champ:

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Écrivez la valeur de tout champ statique qui `java.lang.Object` n’est pas un type builtin, tels que , les tableaux, et les types d’interface. La `IntPtr` valeur peut être une référence locale JNI, référence mondiale `IntPtr.Zero` JNI, JNI faible référence mondiale, ou (pour `null` ).

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Écrivez `bool` la valeur des champs statiques.

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Écrivez `sbyte` la valeur des champs statiques.

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Écrivez `char` la valeur des champs statiques.

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Écrivez `short` la valeur des champs statiques.

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Écrivez `int` la valeur des champs statiques.

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Écrivez `long` la valeur des champs statiques.

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Écrivez `float` la valeur des champs statiques.

- [JNIEnv.SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*) &ndash; ) Écrivez `double` la valeur des champs statiques.

<a name="_Instance_Methods" />

## <a name="instance-methods"></a>Méthodes d’instance

Les méthodes d’instance sont invoquées par *des Œd de méthode*. Les cartes d’ID de méthode sont obtenues via [JNIEnv.GetMethodID](xref:Android.Runtime.JNIEnv.GetMethodID*), qui exige le type dans lequel la méthode est définie, le nom de la méthode, et la signature de [type JNI](#JNI_Type_Signatures) de la méthode.

Les adresses d’ID de méthode n’ont pas besoin d’être libérées et sont valides tant que le type Java correspondant est chargé. (Android ne prend actuellement pas en charge le déchargement de classe.)

Il existe deux ensembles de méthodes pour invoquer des méthodes : l’une pour invoquer virtuellement les méthodes, et l’autre pour invoquer des méthodes non virtuelles. Les deux ensembles de méthodes nécessitent une pièce d’identité de méthode pour invoquer la méthode, et l’invocation non virtuelle exige également que vous spécifiiez quelle implémentation de classe doit être invoquée.

Les méthodes d’interface ne peuvent être consultées que dans le type déclarant; méthodes qui proviennent d’interfaces étendues/héritées ne peuvent pas être recherchées. Voir la section Interfaces contraignantes / Invocateur ultérieure pour plus de détails.

Toute méthode déclarée dans la classe ou n’importe quelle classe de base ou interface mise en œuvre peut être recherchée.

### <a name="virtual-method-invocation"></a>Invocation de méthode virtuelle

L’ensemble de méthodes d’invocation des méthodes suit pratiquement le modèle de nommage :

```csharp
* JNIEnv.Call*Method( IntPtr instance, IntPtr methodID, params JValue[] args );
```

où `*` est le type de retour de la méthode.

- [JNIEnv.CallObjectMethod](xref:Android.Runtime.JNIEnv.CallObjectMethod*) &ndash; Invoquez une méthode qui renvoie `java.lang.Object` un type non-builtin, tel que , les tableaux et les interfaces. La valeur retournée est une référence locale JNI.

- [JNIEnv.CallBooleanMethod](xref:Android.Runtime.JNIEnv.CallBooleanMethod*) &ndash; Invoquez `bool` une méthode qui retourne une valeur.

- [JNIEnv.CallByteMethod](xref:Android.Runtime.JNIEnv.CallByteMethod*) &ndash; Invoquez `sbyte` une méthode qui retourne une valeur.

- [JNIEnv.CallCharMethod](xref:Android.Runtime.JNIEnv.CallCharMethod*) &ndash; Invoquez `char` une méthode qui retourne une valeur.

- [JNIEnv.CallShortMethod](xref:Android.Runtime.JNIEnv.CallShortMethod*) &ndash; Invoquez `short` une méthode qui retourne une valeur.

- [JNIEnv.CallLongMethod](xref:Android.Runtime.JNIEnv.CallLongMethod*) &ndash; Invoquez `long` une méthode qui retourne une valeur.

- [JNIEnv.CallFloatMethod](xref:Android.Runtime.JNIEnv.CallFloatMethod*) &ndash; Invoquez `float` une méthode qui retourne une valeur.

- [JNIEnv.CallDoubleMethod](xref:Android.Runtime.JNIEnv.CallDoubleMethod*) &ndash; Invoquez `double` une méthode qui retourne une valeur.

### <a name="non-virtual-method-invocation"></a>Invocation de méthode non virtuelle

L’ensemble de méthodes d’invocation des méthodes suit non virtuellement le modèle de nommage :

```csharp
* JNIEnv.CallNonvirtual*Method( IntPtr instance, IntPtr class, IntPtr methodID, params JValue[] args );
```

où `*` est le type de retour de la méthode. L’invocation de méthode non virtuelle est habituellement utilisée pour invoquer la méthode de base d’une méthode virtuelle.

- [JNIEnv.CallNonvirtualObjectMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualObjectMethod*) &ndash; Non-virtuellement invoquer une méthode qui renvoie un type non-builtin, tels que `java.lang.Object` , les tableaux, et les interfaces. La valeur retournée est une référence locale JNI.

- [JNIEnv.CallNonvirtualBooleanMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualBooleanMethod*) &ndash; Non-virtually invoque `bool` une méthode qui retourne une valeur.

- [JNIEnv.CallNonvirtualByteMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualByteMethod*) &ndash; Non-virtuellement invoquer `sbyte` une méthode qui retourne une valeur.

- [JNIEnv.CallNonvirtualCharMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualCharMethod*) &ndash; Non-virtuellement invoquer `char` une méthode qui retourne une valeur.

- [JNIEnv.CallNonvirtualShortMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualShortMethod*) &ndash; Non-virtually invoque `short` une méthode qui retourne une valeur.

- [JNIEnv.CallNonvirtualLongMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualLongMethod*) &ndash; Non-virtuellement invoquer `long` une méthode qui retourne une valeur.

- [JNIEnv.CallNonvirtualFloatMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualFloatMethod*) &ndash; Non-virtuellement invoquer `float` une méthode qui retourne une valeur.

- [JNIEnv.CallNonvirtualDoubleMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualDoubleMethod*) &ndash; Non-virtuellement invoquer `double` une méthode qui retourne une valeur.

<a name="_Static_Methods" />

## <a name="static-methods"></a>Méthodes statiques

Les méthodes statiques sont invoquées par *des Œd de méthode*. Les cartes d’ID de méthode sont obtenues via [JNIEnv.GetStaticMethodID](xref:Android.Runtime.JNIEnv.GetStaticMethodID*), qui exige le type dans lequel la méthode est définie, le nom de la méthode, et la signature de [type JNI](#JNI_Type_Signatures) de la méthode.

Les adresses d’ID de méthode n’ont pas besoin d’être libérées et sont valides tant que le type Java correspondant est chargé. (Android ne prend actuellement pas en charge le déchargement de classe.)

### <a name="static-method-invocation"></a>Invocation statique de méthode

L’ensemble de méthodes d’invocation des méthodes suit pratiquement le modèle de nommage :

```csharp
* JNIEnv.CallStatic*Method( IntPtr class, IntPtr methodID, params JValue[] args );
```

où `*` est le type de retour de la méthode.

- [JNIEnv.CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) &ndash; Invoquez une méthode statique qui renvoie un type non-builtin, tel que `java.lang.Object` , les tableaux et les interfaces. La valeur retournée est une référence locale JNI.

- [JNIEnv.CallStaticBooleanMethod](xref:Android.Runtime.JNIEnv.CallStaticBooleanMethod*) &ndash; Invoquez une `bool` méthode statique qui retourne une valeur.

- [JNIEnv.CallStaticByteMethod](xref:Android.Runtime.JNIEnv.CallStaticByteMethod*) &ndash; Invoquez une `sbyte` méthode statique qui retourne une valeur.

- [JNIEnv.CallStaticCharMethod](xref:Android.Runtime.JNIEnv.CallStaticCharMethod*) &ndash; Invoquez une `char` méthode statique qui retourne une valeur.

- [JNIEnv.CallStaticShortMethod](xref:Android.Runtime.JNIEnv.CallStaticShortMethod*) &ndash; Invoquez une `short` méthode statique qui retourne une valeur.

- [JNIEnv.CallStaticLongMethod](xref:Android.Runtime.JNIEnv.CallLongMethod*) &ndash; Invoquez une `long` méthode statique qui retourne une valeur.

- [JNIEnv.CallStaticFloatMethod](xref:Android.Runtime.JNIEnv.CallStaticFloatMethod*) &ndash; Invoquez une `float` méthode statique qui retourne une valeur.

- [JNIEnv.CallStaticDoubleMethod](xref:Android.Runtime.JNIEnv.CallStaticDoubleMethod*) &ndash; Invoquez une `double` méthode statique qui retourne une valeur.

<a name="JNI_Type_Signatures" />

## <a name="jni-type-signatures"></a>JNI Type Signatures

[Les signatures de type JNI](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/types.html#wp16432) sont [des références de type JNI](#_JNI_Type_References) (mais pas des références de type simplifiées), à l’exception des méthodes. Avec des méthodes, la signature de type `'('`JNI est une parenthèse ouverte , suivie par les références de type pour tous les types `')'`de paramètres concatenated ensemble (sans virgule de séparation ou quoi que ce soit d’autre), suivie d’une parenthèse de clôture , suivie par la référence de type JNI du type de retour de méthode.

Par exemple, compte tenu de la méthode Java :

```java
long f(int n, String s, int[] array);
```

La signature de type JNI serait :

```csharp
(ILjava/lang/String;[I)J
```

En général, *strongly* il est fortement `javap` recommandé d’utiliser la commande pour déterminer les signatures de JNI. Par exemple, la signature de type JNI de la méthode [java.lang.Thread.State.valueOf(String)](https://developer.android.com/reference/java/lang/Thread.State.html#valueOf(java.lang.String)) est " (Ljava/lang/String;)Ljava/lang/Thread$State;", tandis que la signature de type JNI de la méthode [java.lang.Thread.State.values](https://developer.android.com/reference/java/lang/Thread.State.html#values) est "()[Ljava/lang/Thread$State;". Méfiez-vous des semi-colons de fuite; *ceux-ci font* partie de la signature de type JNI.

<a name="_JNI_Type_References" />

## <a name="jni-type-references"></a>Références de type JNI

Les références de type JNI sont différentes des références de type Java. Vous ne pouvez pas utiliser `java.lang.String` des noms de type Java entièrement qualifiés `"java/lang/String"` `"Ljava/lang/String;"`comme avec JNI, vous devez plutôt utiliser les variations JNI ou, selon le contexte; voir ci-dessous pour plus de détails.
Il existe quatre types de références de type JNI :

- **intégré**
- **Simplifié**
- **type**
- **tableau**

### <a name="built-in-type-references"></a>Références de type intégrées

Les références de type intégré sont un caractère unique, utilisé pour référencer les types de valeur intégrés. La cartographie est la suivante:

- `"B"`pour `sbyte` .
- `"S"`pour `short` .
- `"I"`pour `int` .
- `"J"`pour `long` .
- `"F"`pour `float` .
- `"D"`pour `double` .
- `"C"`pour `char` .
- `"Z"`pour `bool` .
- `"V"`pour `void` les types de retour de méthode.

<a name="_Simplified_Type_References_1" />

### <a name="simplified-type-references"></a>Références de type simplifiées

Les références de type simplifié ne peuvent être utilisées que dans [JNIEnv.FindClass(string)](xref:Android.Runtime.JNIEnv.FindClass*)).
Il y a deux façons d’obtenir une référence simplifiée :

1. D’un nom Java entièrement `'.'` qualifié, remplacer chaque dans le `'/'` nom `'.'` du paquet et `'$'` avant le nom de type avec , et chaque dans un nom de type avec .

1. Lire la `'unzip -l android.jar | grep JavaName'` sortie de .

L’un ou l’autre des deux se traduira par le type Java `java/lang/Thread$State` [java.lang.Thread.State](https://developer.android.com/reference/java/lang/Thread.State.html) étant cartographié à la référence de type simplifié .

### <a name="type-references"></a>Références de type

Une référence type est une référence de type intégré `'L'` ou une `';'` référence de type simplifiée avec un préfixe et un suffixe. Pour le type Java [java.lang.String](https://developer.android.com/reference/java/lang/String.html), `"java/lang/String"`la référence de `"Ljava/lang/String;"`type simplifié est, tandis que la référence de type est .

Les références de type sont utilisées avec des références de type Array et avec des signatures JNI.

Une autre façon d’obtenir une référence `'javap -s -classpath android.jar fully.qualified.Java.Name'`type est en lisant la sortie de .
Selon le type en cause, vous pouvez utiliser une déclaration de constructeur ou un type de retour de méthode pour déterminer le nom de LNI. Par exemple :

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

`Thread.State`est un type Java enum, de sorte `valueOf` que nous pouvons utiliser la signature de la méthode pour déterminer que la référence de type est Ljava /lang/Thread$State;.

### <a name="array-type-references"></a>Références de type Array

Les références `'['` de type array sont préfixées à une référence de type JNI.
Les références de type simplifié ne peuvent pas être utilisées lors de la spécifier des tableaux.

`int[]` Par exemple, `"[I"` `int[][]` est `"[[I"`, `java.lang.Object[]` `"[Ljava/lang/Object;"`est , et est .

## <a name="java-generics-and-type-erasure"></a>Java Génériques et Effacement de type

*La plupart* du temps, comme on le voit à travers JNI, les génériques Java *n’existent pas*.
Il ya quelques «rides», mais ces rides sont dans la façon dont Java interagit avec les génériques, pas avec la façon dont JNI lève les yeux et invoque les membres génériques.

Il n’y a aucune différence entre un type ou un membre générique et un type ou un membre non générique lors de l’interaction par l’intermédiaire de l’INM. Par exemple, le type générique [&lt;java.lang.Class T&gt; ](https://developer.android.com/reference/java/lang/Class.html) est également le type `java.lang.Class`générique "brut", qui ont tous deux la même référence de type simplifié, `"java/lang/Class"`.

## <a name="java-native-interface-support"></a>Support d’interface indigène java

[Android.Runtime.JNIEnv](xref:Android.Runtime.JNIEnv) est géré wrapper pour l’interface native Jave (JNI). Fonctions JNI sont déclarés dans la [spécification d’interface indigène](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)Java `JNIEnv*` , `IntPtr` bien que les méthodes ont été modifiées pour supprimer le paramètre explicite et est utilisé au lieu de `jobject`, , `jclass`, `jmethodID`etc. Par exemple, considérez la [fonction JNI NewObject](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp4517):

```csharp
jobject NewObjectA(JNIEnv *env, jclass clazz, jmethodID methodID, jvalue *args);
```

Ceci est exposé comme la méthode [JNIEnv.NewObject:](xref:Android.Runtime.JNIEnv.NewObject*)

```csharp
public static IntPtr NewObject(IntPtr clazz, IntPtr jmethod, params JValue[] parms);
```

La traduction entre les deux appels est assez simple. En C, vous auriez :

```c
jobject CreateMapActivity(JNIEnv *env)
{
    jclass    Map_Class   = (*env)->FindClass(env, "mono/samples/googlemaps/MyMapActivity");
    jmethodID Map_defCtor = (*env)->GetMethodID (env, Map_Class, "<init>", "()V");
    jobject   instance    = (*env)->NewObject (env, Map_Class, Map_defCtor);

    return instance;
}
```

L’équivalent C serait :

```csharp
IntPtr CreateMapActivity()
{
    IntPtr Map_Class   = JNIEnv.FindClass ("mono/samples/googlemaps/MyMapActivity");
    IntPtr Map_defCtor = JNIEnv.GetMethodID (Map_Class, "<init>", "()V");
    IntPtr instance    = JNIEnv.NewObject (Map_Class, Map_defCtor);

    return instance;
}
```

Une fois que vous avez une instance Java Object tenue dans un IntPtr, vous aurez probablement envie de faire quelque chose avec elle. Vous pouvez utiliser des méthodes JNIEnv telles que [JNIEnv.CallVoidMethod()](xref:Android.Runtime.JNIEnv.CallVoidMethod*) pour le faire, mais s’il y a déjà un emballage analogique C’alors vous voudrez construire un emballage sur la référence JNI. Vous pouvez le faire par le biais de la méthode [d’extension\<Extensions.JavaCast T>:](xref:Android.Runtime.Extensions.JavaCast*)

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = new Java.Lang.Object(lrefActivity, JniHandleOwnership.TransferLocalRef)
    .JavaCast<Activity>();
```

Vous pouvez également utiliser la méthode [Java.Lang.Object.GetObject\<T>](xref:Java.Lang.Object.GetObject*) :

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = Java.Lang.Object.GetObject<Activity>(lrefActivity, JniHandleOwnership.TransferLocalRef);
```

En outre, toutes les fonctions JNI ont `JNIEnv*` été modifiées en supprimant le paramètre présent dans chaque fonction JNI.

## <a name="summary"></a>Récapitulatif

Traiter directement avec JNI est une expérience terrible qui doit être évitée à tout prix. Malheureusement, ce n’est pas toujours évitable; espérons que ce guide fournira une certaine assistance lorsque vous frappez les cas de Java non liés avec Mono pour Android.

## <a name="related-links"></a>Liens connexes

- [Spécifications d’interface native de Java](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/jniTOC.html)
- [Fonctions d’interface native de Java](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)
