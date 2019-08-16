---
title: Wrappers Android pouvant être appelés pour Xamarin. Android
ms.prod: xamarin
ms.assetid: C33E15FA-1E2B-819A-C656-CA588D611492
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: fb00bf4a817ad8188d5a127b23b4a910dd4f23a7
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524137"
---
# <a name="android-callable-wrappers-for-xamarinandroid"></a>Wrappers Android pouvant être appelés pour Xamarin. Android

Les wrappers à appel Android (ACWs) sont requis chaque fois que le runtime Android appelle du code managé. Ces wrappers sont requis, car il n’existe aucun moyen d’inscrire des classes avec l’ART (Runtime Android) au moment de l’exécution. (En particulier, la [fonction JNI defineClass ()](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp15986) n’est pas prise en charge par le runtime Android.} Les wrappers pouvant être appelés par Android comportent l’absence de prise en charge de l’inscription du type d’exécution. 

À *chaque fois* Le code Android doit exécuter une `virtual` méthode d’interface ou qui `overridden` est ou qui est implémentée en code managé, Xamarin. Android doit fournir un proxy Java afin que cette méthode soit distribuée au type managé approprié. Ces types de proxy Java sont du code Java qui a la «même» classe de base et la même liste d’interfaces Java que le type managé, implémentant les mêmes constructeurs et déclarant toutes les classes de base et les méthodes d’interface substituées. 

Les wrappers pouvant être appelés par Android sont générés par le programme **monodroid. exe** pendant le [processus de génération](~/android/deploy-test/building-apps/build-process.md): ils sont générés pour tous les types qui héritent de [java. lang. Object](xref:Java.Lang.Object)(directement ou indirectement). 



## <a name="android-callable-wrapper-naming"></a>Nom du wrapper Android pouvant être appelé

Les noms de packages pour les wrappers pouvant être appelés Android sont basés sur le MD5SUM du nom qualifié d’assembly du type en cours d’exportation. Grâce à cette technique de nommage, le même nom de type qualifié complet peut être mis à disposition par différents assemblys sans introduire d’erreur de Packaging. 

En raison de ce schéma de nommage MD5SUM, vous ne pouvez pas accéder directement à vos types par nom. Par exemple, la commande `adb` suivante ne fonctionne pas, car le nom `my.ActivityType` du type n’est pas généré par défaut: 

```shell
adb shell am start -n My.Package.Name/my.ActivityType
```

En outre, vous pouvez voir des erreurs semblables à ce qui suit si vous tentez de référencer un type par nom:

```shell
java.lang.ClassNotFoundException: Didn't find class "com.company.app.MainActivity"
on path: DexPathList[[zip file "/data/app/com.company.App-1.apk"] ...
```

Si vous avez besoin d’accéder aux types par nom, vous pouvez déclarer un nom pour ce type dans une déclaration d’attribut. Par exemple, voici le code qui déclare une activité avec le nom `My.ActivityType`complet:

```csharp
namespace My {
    [Activity]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

La `ActivityAttribute.Name` propriété peut être définie pour déclarer explicitement le nom de cette activité: 

```csharp
namespace My {
    [Activity(Name="my.ActivityType")]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

Une fois ce paramètre de propriété ajouté `my.ActivityType` , il est possible d’y accéder par nom à `adb` partir de code externe et de scripts. L' `Name` attribut peut être défini pour de nombreux types différents `Activity`, `Application`notamment `Service`, `BroadcastReceiver`,, `ContentProvider`et: 

- [ActivityAttribute.Name](xref:Android.App.ActivityAttribute.Name)
- [ApplicationAttribute.Name](xref:Android.App.ApplicationAttribute.Name)
- [ServiceAttribute.Name](xref:Android.App.ServiceAttribute.Name)
- [BroadcastReceiverAttribute.Name](xref:Android.Content.BroadcastReceiverAttribute.Name)
- [ContentProviderAttribute.Name](xref:Android.Content.ContentProviderAttribute.Name)

L’attribution d’un nom ACW basé sur MD5SUM a été introduite dans Xamarin. Android 5,0. Pour plus d’informations sur l’affectation des noms d’attribut, consultez [RegisterAttribute](xref:Android.Runtime.RegisterAttribute). 



## <a name="implementing-interfaces"></a>Implémentation des interfaces

Il peut arriver que vous deviez implémenter une interface Android, par exemple [Android. Content. IComponentCallbacks](xref:Android.Content.IComponentCallbacks). Dans la mesure où toutes les classes et interfaces Android étendent l’interface [Android. Runtime. IJavaObject](xref:Android.Runtime.IJavaObject) , la question se `IJavaObject`pose: comment implémenter? 

Nous avons répondu à la question ci-dessus: la raison pour laquelle `IJavaObject` tous les types Android doivent être implémentés est de sorte que Xamarin. Android dispose d’un wrapper Android pouvant être appelé pour Android, c.-à-d. un proxy Java pour le type donné. Étant donné que **monodroid. exe** recherche `Java.Lang.Object` uniquement `IJavaObject,` les sous-classes `Java.Lang.Object` et que l’implémentation de la réponse est évidente `Java.Lang.Object`: Subclass: 

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


## <a name="implementation-details"></a>Détails de l’implémentation

*Le reste de cette page fournit des détails d’implémentation susceptibles de changer sans préavis* (et est présenté ici uniquement parce que les développeurs seront curieux de savoir ce qui se passe). 

Par exemple, à partir de C# la source suivante:

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

Le programme **Android. exe** génère le wrapper Android pouvant être appelé suivant: 

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

Notez que la classe de base est conservée `native` , et les déclarations de méthode sont fournies pour chaque méthode substituée dans le code managé. 
