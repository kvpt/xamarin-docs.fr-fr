---
title: Xamarin. Android et Desktop-différences dans le runtime mono
ms.prod: xamarin
ms.assetid: F953F9B4-3596-8B3A-A8E4-8219B5B9F7CA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 57d9d6a91f88d117f0889a8dba9e6198ec6b7f62
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524770"
---
# <a name="limitations"></a>Limites

Étant donné que les applications sur Android requièrent la génération de types de proxy Java pendant le processus de génération, il n’est pas possible de générer tout le code au moment de l’exécution.

Il s’agit des limitations de Xamarin. Android par rapport à l’ordinateur de bureau mono:

## <a name="limited-dynamic-language-support"></a>Prise en charge du langage dynamique limité

 Les [wrappers pouvant être appelés Android](~/android/platform/java-integration/android-callable-wrappers.md) sont nécessaires chaque fois que le runtime Android doit appeler du code managé. Les wrappers Android pouvant être appelés sont générés au moment de la compilation, en fonction de l’analyse statique de l’IL. Résultat net: vous *ne pouvez pas* utiliser les langages dynamiques (IronPython, IronRuby, etc.) dans tout scénario où la sous-classe des types Java est requise (y compris le sous-Class indirect), car il n’existe aucun moyen d’extraire ces types dynamiques au moment de la compilation vers Générez les wrappers Android pouvant être appelés.

## <a name="limited-java-generation-support"></a>Prise en charge limitée de la génération Java

Les [wrappers pouvant être appelés Android](~/android/platform/java-integration/android-callable-wrappers.md) doivent être générés pour que le code Java puisse appeler du code managé. *Par défaut*, les wrappers pouvant être appelés par Android ne contiennent que des constructeurs (certains) déclarés et des méthodes qui remplacent une méthode Java virtuelle [`RegisterAttribute`](xref:Android.Runtime.RegisterAttribute)(c’est-à-dire qu’elle a) ou implémentent une méthode `Attribute`d’interface Java (l’interface a également).
  
Avant la version 4,1, aucune autre méthode n’a pu être déclarée. Avec la version 4,1, [les `Export` attributs `ExportField` personnalisés et peuvent être utilisés pour déclarer des méthodes et des champs Java dans le wrapper Android pouvant être appelé](~/android/platform/java-integration/working-with-jni.md).

### <a name="missing-constructors"></a>Constructeurs manquants

Les constructeurs restent délicats, [`ExportAttribute`](xref:Java.Interop.ExportAttribute) sauf si est utilisé. L’algorithme pour générer des constructeurs de wrapper Android pouvant être appelés est qu’un constructeur Java sera émis dans les cas suivants:

1. Il existe un mappage Java pour tous les types de paramètres
2. La classe de base déclare le même constructeur &ndash; , ce qui est nécessaire, car le wrapper Android Callable *doit* appeler le constructeur de classe de base correspondant; aucun argument par défaut ne peut être utilisé (car il n’existe aucun moyen simple de déterminer les valeurs doit être utilisé dans Java).

Par exemple, considérons la classe suivante :

```csharp
[Service]
class MyIntentService : IntentService {
    public MyIntentService (): base ("value")
    {
    }
}
```

Bien que cela semble parfaitement logique, le wrapper Android pouvant être appelé *dans les versions release* ne contient pas de constructeur par défaut. Par conséquent, si vous tentez de démarrer ce service ( [`Context.StartService`](xref:Android.Content.Context.StartService*)par exemple, l’opération échouera:

```shell
E/AndroidRuntime(31766): FATAL EXCEPTION: main
E/AndroidRuntime(31766): java.lang.RuntimeException: Unable to instantiate service example.MyIntentService: java.lang.InstantiationException: can't instantiate class example.MyIntentService; no empty constructor
E/AndroidRuntime(31766):        at android.app.ActivityThread.handleCreateService(ActivityThread.java:2347)
E/AndroidRuntime(31766):        at android.app.ActivityThread.access$1600(ActivityThread.java:130)
E/AndroidRuntime(31766):        at android.app.ActivityThread$H.handleMessage(ActivityThread.java:1277)
E/AndroidRuntime(31766):        at android.os.Handler.dispatchMessage(Handler.java:99)
E/AndroidRuntime(31766):        at android.os.Looper.loop(Looper.java:137)
E/AndroidRuntime(31766):        at android.app.ActivityThread.main(ActivityThread.java:4745)
E/AndroidRuntime(31766):        at java.lang.reflect.Method.invokeNative(Native Method)
E/AndroidRuntime(31766):        at java.lang.reflect.Method.invoke(Method.java:511)
E/AndroidRuntime(31766):        at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:786)
E/AndroidRuntime(31766):        at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:553)
E/AndroidRuntime(31766):        at dalvik.system.NativeStart.main(Native Method)
E/AndroidRuntime(31766): Caused by: java.lang.InstantiationException: can't instantiate class example.MyIntentService; no empty constructor
E/AndroidRuntime(31766):        at java.lang.Class.newInstanceImpl(Native Method)
E/AndroidRuntime(31766):        at java.lang.Class.newInstance(Class.java:1319)
E/AndroidRuntime(31766):        at android.app.ActivityThread.handleCreateService(ActivityThread.java:2344)
E/AndroidRuntime(31766):        ... 10 more
```

La solution de contournement consiste à déclarer un constructeur par défaut, à `ExportAttribute`l’orner avec [`ExportAttribute.SuperStringArgument`](xref:Java.Interop.ExportAttribute.SuperArgumentsString)le et à définir le: 

```csharp
[Service]
class MyIntentService : IntentService {
    [Export (SuperArgumentsString = "\"value\"")]
    public MyIntentService (): base("value")
    {
    }

    // ...
}
```


### <a name="generic-c-classes"></a>Classes C# génériques

Les C# classes génériques ne sont que partiellement prises en charge. Les limitations suivantes existent:


- Les types génériques ne peuvent `[Export]` pas `[ExportField`utiliser ou]. Si vous tentez de le faire `XA4207` , une erreur est générée.

    ```csharp
    public abstract class Parcelable<T> : Java.Lang.Object, IParcelable
    {
        // Invalid; generates XA4207
        [ExportField ("CREATOR")]
        public static IParcelableCreator CreateCreator ()
        {
            ...
    }
    ```

- Les méthodes génériques ne peuvent `[Export]` pas `[ExportField]`utiliser ou:

    ```csharp
    public class Example : Java.Lang.Object
    {
        
        // Invalid; generates XA4207
        [Export]
        public static void Method<T>(T value)
        {
            ...
        }
    }
    ```

- `[ExportField]`ne peut pas être utilisé sur les méthodes `void`qui retournent:

    ```csharp
    public class Example : Java.Lang.Object
    {
        // Invalid; generates XA4208
        [ExportField ("CREATOR")]
        public static void CreateSomething ()
        {
        }
    }
    ```

- Les instances de types génériques _ne doivent pas_ être créées à partir du code Java.
    Ils peuvent uniquement être créés en toute sécurité à partir du code managé:

    ```csharp
    [Activity (Label="Die!", MainLauncher=true)]
    public class BadGenericActivity<T> : Activity
    {
        protected override void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);
        }
    }
    ```

## <a name="partial-java-generics-support"></a>Prise en charge des génériques Java partiels

La prise en charge de la liaison des génériques Java est limitée. En particulier, les membres d’une classe d’instance générique dérivée d’une autre classe générique (non instanciée) sont exposés en tant que Java. lang. Object. Par exemple, la méthode [Android. Content. intentions. GetParcelableExtra](xref:Android.Content.Intent.GetParcelableExtra*) retourne Java. lang. Object. Cela est dû à l’effacement des génériques Java.
Nous avons certaines classes qui n’appliquent pas cette limitation, mais elles sont ajustées manuellement.

## <a name="related-links"></a>Liens associés

- [Wrappers pouvant être appelés par Android](~/android/platform/java-integration/android-callable-wrappers.md)
- [Utilisation de JNI](~/android/platform/java-integration/working-with-jni.md)
- [ExportAttribute](xref:Java.Interop.ExportAttribute)
- [SuperString](xref:Java.Interop.ExportAttribute.SuperArgumentsString)
- [RegisterAttribute](xref:Android.Runtime.RegisterAttribute)
