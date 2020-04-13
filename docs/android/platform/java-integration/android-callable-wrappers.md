---
title: Emballages Callable Android pour Xamarin.Android
ms.prod: xamarin
ms.assetid: C33E15FA-1E2B-819A-C656-CA588D611492
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: 7278fd624bb3147c2e1a1a1a79adde68813a9888
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020155"
---
# <a name="android-callable-wrappers-for-xamarinandroid"></a>Emballages Callable Android pour Xamarin.Android

Android Callable Wrappers (ACWs) sont nécessaires chaque fois que le temps d’exécution Android invoque le code géré. Ces emballages sont nécessaires parce qu’il n’y a aucun moyen d’enregistrer des classes avec ART (l’heure d’exécution Android) à l’heure de l’exécution. (Spécifiquement, la [fonction JNI DefineClass()](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp15986) n’est pas prise en charge par l’exécution Android. Android Callable Wrappers compenser ainsi l’absence de support d’enregistrement de type runtime. 

*A chaque fois* Le code Android `virtual` doit exécuter une `overridden` méthode ou une interface qui est ou implémentée dans le code géré, Xamarin.Android doit fournir un proxy Java de sorte que cette méthode est expédiée au type géré approprié. Ces types de proxy Java sont le code Java qui a la "même" classe de base et la liste d’interface Java comme le type géré, la mise en œuvre des mêmes constructeurs et de déclarer toute classe de base et les méthodes d’interface. 

Les emballages callables Android sont générés par le programme **monodroid.exe** pendant le [processus de construction](~/android/deploy-test/building-apps/build-process.md): ils sont générés pour tous les types qui (directement ou indirectement) héritent de [Java.Lang.Object](xref:Java.Lang.Object). 

## <a name="android-callable-wrapper-naming"></a>Nom de l’emballage d’appel Android

Les noms de paquets pour Les emballages Callable Android sont basés sur le MD5SUM du nom qualifié d’assemblage du type exporté. Cette technique de nommage permet de mettre à disposition le même nom de type entièrement qualifié par différents assemblages sans introduire d’erreur d’emballage. 

En raison de ce système de nommage MD5SUM, vous ne pouvez pas accéder directement à vos types par leur nom. Par exemple, `adb` la commande suivante ne `my.ActivityType` fonctionnera pas parce que le nom de type n’est pas généré par défaut : 

```shell
adb shell am start -n My.Package.Name/my.ActivityType
```

En outre, vous pouvez voir des erreurs comme ce qui suit si vous essayez de référencer un type par nom:

```shell
java.lang.ClassNotFoundException: Didn't find class "com.company.app.MainActivity"
on path: DexPathList[[zip file "/data/app/com.company.App-1.apk"] ...
```

Si vous avez besoin *d’avoir* accès à des types par nom, vous pouvez déclarer un nom pour ce type dans une déclaration d’attribut. Par exemple, voici le code qui déclare une `My.ActivityType`activité avec le nom entièrement qualifié :

```csharp
namespace My {
    [Activity]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

La `ActivityAttribute.Name` propriété peut être définie pour déclarer explicitement le nom de cette activité : 

```csharp
namespace My {
    [Activity(Name="my.ActivityType")]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

Une fois que ce `my.ActivityType` paramètre de propriété est ajouté, peut être consulté par nom à partir de code externe et à partir de `adb` scripts. L’attribut `Name` peut être défini `Activity`pour `Application` `Service`de `BroadcastReceiver`nombreux `ContentProvider`types différents, y compris , , , , et: 

- [ActivityAttribute.Name](xref:Android.App.ActivityAttribute.Name)
- [ApplicationAttribute.Name](xref:Android.App.ApplicationAttribute.Name)
- [ServiceAttribute.Name](xref:Android.App.ServiceAttribute.Name)
- [BroadcastReceiverAttribute.Name](xref:Android.Content.BroadcastReceiverAttribute.Name)
- [ContentProviderAttribute.Name](xref:Android.Content.ContentProviderAttribute.Name)

Md5SUM-basé ACW nommage a été introduit dans Xamarin.Android 5.0. Pour plus d’informations sur la dénomination d’attribut, voir [RegisterAttribute](xref:Android.Runtime.RegisterAttribute). 

## <a name="implementing-interfaces"></a>Implémentation des interfaces

Il ya des moments où vous pouvez avoir besoin d’implémenter une interface Android, tels que [Android.Content.IComponentCallbacks](xref:Android.Content.IComponentCallbacks). Puisque toutes les classes et interfaces Android prolongent l’interface [Android.Runtime.IJavaObject,](xref:Android.Runtime.IJavaObject) la question se pose : comment implémentons-nous `IJavaObject`? 

La question a été répondu ci-dessus: `IJavaObject` la raison pour laquelle tous les types Android doivent implémenter est de sorte que Xamarin.Android a un emballage callable Android à fournir à Android, c’est-à-dire un proxy Java pour le type donné. Puisque **monodroid.exe** ne `Java.Lang.Object` cherche que `Java.Lang.Object` des `IJavaObject,` sous-classes, et `Java.Lang.Object`met en œuvre la réponse est évidente: sous-classe: 

```csharp
class MyComponentCallbacks : Java.Lang.Object, Android.Content.IComponentCallbacks {

    public void OnConfigurationChanged (Android.Content.Res.Configuration newConfig)
    {
        // implementation goes here...
    } 

    public void OnLowMemory ()
    {
        // implementation goes here...
    }
}
```

## <a name="implementation-details"></a>Informations d’implémentation

*Le reste de cette page fournit des détails de mise en œuvre sous réserve de changement sans préavis* (et est présenté ici seulement parce que les développeurs seront curieux de savoir ce qui se passe). 

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

public class HelloAndroid
    extends android.app.Activity
{
    static final String __md_methods;
    static {
        __md_methods = "n_onCreate:(Landroid/os/Bundle;)V:GetOnCreate_Landroid_os_Bundle_Handler\n" + "";
        mono.android.Runtime.register (
            "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, 
            Culture=neutral, PublicKeyToken=null", HelloAndroid.class, __md_methods);
    }

    public HelloAndroid ()
    {
        super ();
        if (getClass () == HelloAndroid.class)
            mono.android.TypeManager.Activate (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, 
                Culture=neutral, PublicKeyToken=null", "", this, new java.lang.Object[] {  });
    }

    @Override
    public void onCreate (android.os.Bundle p0)
    {
        n_onCreate (p0);
    }

    private native void n_onCreate (android.os.Bundle p0);
}
```

Notez que la classe de `native` base est préservée, et des déclarations de méthode sont fournies pour chaque méthode qui est remplacée dans le code géré. 
