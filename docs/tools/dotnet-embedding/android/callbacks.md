---
title: Rappels sur Android
ms.prod: xamarin
ms.assetid: F3A7A4E6-41FE-4F12-949C-96090815C5D6
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 982eecea12a4e967bc0c05480ae9099cea10b4cb
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120043"
---
# <a name="callbacks-on-android"></a>Rappels sur Android

L’appel à Java C# à partir de est une *activité risquée*. Autrement dit, il existe un *modèle* pour les rappels de C# à Java; Toutefois, il est plus compliqué que ce que nous aimerions.

Nous allons aborder les trois options permettant d’effectuer les rappels qui sont les plus logiques pour Java:

- Classes abstraites
- Interfaces
- Méthodes virtuelles

## <a name="abstract-classes"></a>Classes abstraites

Il s’agit de l’itinéraire le plus simple pour les rappels. `abstract` je vous conseille donc d’utiliser si vous essayez simplement d’obtenir un rappel fonctionnant sous la forme la plus simple.

Commençons par une C# classe que nous aimerions que Java implémente:

```csharp
[Register("mono.embeddinator.android.AbstractClass")]
public abstract class AbstractClass : Java.Lang.Object
{
    public AbstractClass() { }

    public AbstractClass(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer) { }

    [Export("getText")]
    public abstract string GetText();
}
```

Voici les détails à prendre en charge:

- `[Register]`génère un nom de package attrayant dans Java: vous obtiendrez un nom de package généré automatiquement sans celui-ci.
- Sous-classement `Java.Lang.Object` des signaux à l’incorporation .net pour exécuter la classe par le biais du générateur Java Xamarin. Android.
- Constructeur vide: est ce que vous souhaitez utiliser à partir du code Java.
- `(IntPtr, JniHandleOwnership)`constructeur: est ce que Xamarin. Android utilisera pour créer C#l’équivalent des objets Java.
- `[Export]`signale à Xamarin. Android d’exposer la méthode à Java. Nous pouvons également modifier le nom de la méthode, car le monde Java aime utiliser des méthodes en minuscules.

Nous allons ensuite créer une C# méthode pour tester le scénario:

```csharp
[Register("mono.embeddinator.android.JavaCallbacks")]
public class JavaCallbacks : Java.Lang.Object
{
    [Export("abstractCallback")]
    public static string AbstractCallback(AbstractClass callback)
    {
        return callback.GetText();
    }
}
```

`JavaCallbacks`peut être n’importe quelle classe pour tester cela, à condition qu’il `Java.Lang.Object`s’agit d’un.

À présent, exécutez l’incorporation .NET sur votre assembly .NET pour générer un AAR. Pour plus d’informations, consultez le [Guide de prise en main](~/tools/dotnet-embedding/get-started/java/android.md) .

Après avoir importé le fichier AAR dans Android Studio, nous allons écrire un test unitaire:

```java
@Test
public void abstractCallback() throws Throwable {
    AbstractClass callback = new AbstractClass() {
        @Override
        public String getText() {
            return "Java";
        }
    };

    assertEquals("Java", callback.getText());
    assertEquals("Java", JavaCallbacks.abstractCallback(callback));
}
```

Nous avons donc:

- `AbstractClass` Implémenté dans Java avec un type anonyme
- Nous avons fait en sorte `"Java"` que notre instance retourne à partir de Java
- Nous avons fait en sorte `"Java"` que notre instance retourne deC#
- Ajouté `throws Throwable`, car C# les constructeurs sont actuellement marqués avec`throws`

Si nous avons exécuté ce test unitaire tel quel, l’opération échoue avec une erreur telle que:

```csharp
System.NotSupportedException: Unable to find Invoker for type 'Android.AbstractClass'. Was it linked away?
```

Ce qui manque ici est un `Invoker` type. Il s’agit d’une sous `AbstractClass` -classe de C# qui transfère les appels à Java. Si un objet Java entre dans C# le monde et que C# le type équivalent est abstract, Xamarin. Android recherche automatiquement un C# type avec le suffixe `Invoker` à utiliser dans C# le code.

Xamarin. Android utilise ce `Invoker` modèle pour les projets de liaison Java, entre autres choses.

Voici notre implémentation de `AbstractClassInvoker`:

```csharp
class AbstractClassInvoker : AbstractClass
{
    IntPtr class_ref, id_gettext;

    public AbstractClassInvoker(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass(Handle);
        class_ref = JNIEnv.NewGlobalRef(lref);
        JNIEnv.DeleteLocalRef(lref);
    }

    protected override Type ThresholdType
    {
        get { return typeof(AbstractClassInvoker); }
    }

    protected override IntPtr ThresholdClass
    {
        get { return class_ref; }
    }

    public override string GetText()
    {
        if (id_gettext == IntPtr.Zero)
            id_gettext = JNIEnv.GetMethodID(class_ref, "getText", "()Ljava/lang/String;");
        IntPtr lref = JNIEnv.CallObjectMethod(Handle, id_gettext);
        return GetObject<Java.Lang.String>(lref, JniHandleOwnership.TransferLocalRef)?.ToString();
    }

    protected override void Dispose(bool disposing)
    {
        if (class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef(class_ref);
        class_ref = IntPtr.Zero;

        base.Dispose(disposing);
    }
}
```

Il y a un peu de choses ici, nous:

- Ajout d’une classe avec le `Invoker` suffixe sous-classes`AbstractClass`
- Ajout `class_ref` de pour contenir la référence JNI à la classe Java qui sous-classe C# notre classe
- Ajout `id_gettext` de pour contenir la référence JNI à la `getText` méthode Java
- Un constructeur `(IntPtr, JniHandleOwnership)` a été inclus
- Implémenté `ThresholdType` et`ThresholdClass` comme exigence pour Xamarin. Android afin de connaître les détails de la`Invoker`
- `GetText`nécessaire pour rechercher la méthode `getText` Java avec la signature JNI appropriée et l’appeler
- `Dispose`est simplement nécessaire pour effacer la référence à`class_ref`

Après l’ajout de cette classe et la génération d’un nouveau AAR, notre test unitaire réussit. Comme vous pouvez le voir, ce modèle pour les rappels n’est pas *idéal*, mais réalisable.

Pour plus d’informations sur l’interopérabilité Java, consultez la [documentation incroyable Xamarin. Android](~/android/platform/java-integration/working-with-jni.md) sur ce sujet.

## <a name="interfaces"></a>Interfaces

Les interfaces sont très identiques aux classes abstraites, à l’exception d’un détail: Xamarin. Android ne génère pas de Java pour eux. Cela est dû au fait que, avant l’incorporation .NET, il n’y a pas beaucoup C# de scénarios dans lesquels Java implémenterait une interface.

Supposons que nous ayons l’interface C# suivante:

```csharp
[Register("mono.embeddinator.android.IJavaCallback")]
public interface IJavaCallback : IJavaObject
{
    [Export("send")]
    void Send(string text);
}
```

`IJavaObject`signale à l’incorporation .net qu’il s’agit d’une interface Xamarin. Android, mais elle est exactement la même `abstract` que celle d’une classe.

Étant donné que Xamarin. Android ne génère pas actuellement le code Java pour cette interface, ajoutez le code Java C# suivant à votre projet:

```java
package mono.embeddinator.android;

public interface IJavaCallback {
    void send(String text);
}
```

Vous pouvez placer le fichier n’importe où, mais veillez à définir son action `AndroidJavaSource`de génération sur. L’incorporation .NET est alors signalée pour être copiée dans le répertoire approprié pour être compilé dans votre fichier AAR.

Ensuite, l' `Invoker` implémentation sera assez similaire:

```csharp
class IJavaCallbackInvoker : Java.Lang.Object, IJavaCallback
{
    IntPtr class_ref, id_send;

    public IJavaCallbackInvoker(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass(Handle);
        class_ref = JNIEnv.NewGlobalRef(lref);
        JNIEnv.DeleteLocalRef(lref);
    }

    protected override Type ThresholdType
    {
        get { return typeof(IJavaCallbackInvoker); }
    }

    protected override IntPtr ThresholdClass
    {
        get { return class_ref; }
    }

    public void Send(string text)
    {
        if (id_send == IntPtr.Zero)
            id_send = JNIEnv.GetMethodID(class_ref, "send", "(Ljava/lang/String;)V");
        JNIEnv.CallVoidMethod(Handle, id_send, new JValue(new Java.Lang.String(text)));
    }

    protected override void Dispose(bool disposing)
    {
        if (class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef(class_ref);
        class_ref = IntPtr.Zero;

        base.Dispose(disposing);
    }
}
```

Après la génération d’un fichier AAR, dans Android Studio nous pourrions écrire le test unitaire de passage suivant:

```java
class ConcreteCallback implements IJavaCallback {
    public String text;
    @Override
    public void send(String text) {
        this.text = text;
    }
}

@Test
public void interfaceCallback() {
    ConcreteCallback callback = new ConcreteCallback();
    JavaCallbacks.interfaceCallback(callback, "Java");
    assertEquals("Java", callback.text);
}
```

## <a name="virtual-methods"></a>Méthodes virtuelles

Le remplacement d' `virtual` un dans Java est possible, mais n’est pas une excellente expérience.

Supposons que vous disposez de la C# classe suivante:

```csharp
[Register("mono.embeddinator.android.VirtualClass")]
public class VirtualClass : Java.Lang.Object
{
    public VirtualClass() { }

    public VirtualClass(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer) { }

    [Export("getText")]
    public virtual string GetText() { return "C#"; }
}
```

Si vous avez suivi `abstract` l’exemple de classe ci-dessus, cela fonctionne, à l’exception d’un détail: _Xamarin. Android ne recherche pas `Invoker`le_ .

Pour résoudre ce problème, modifiez C# la classe pour `abstract`qu’elle soit:

```csharp
public abstract class VirtualClass : Java.Lang.Object
```

Cela n’est pas idéal, mais ce scénario fonctionne. Xamarin. Android récupère `VirtualClassInvoker` et Java peut utiliser `@Override` la méthode.

## <a name="callbacks-in-the-future"></a>Rappels à l’avenir

Il existe deux façons d’améliorer ces scénarios:

1. `throws Throwable`sur C# ce [PR](https://github.com/xamarin/java.interop/pull/170), les constructeurs sont corrigés.
1. Créez le générateur Java dans les interfaces de prise en charge Xamarin. Android.
    - Cela supprime la nécessité d’ajouter un fichier source Java avec une action de `AndroidJavaSource`génération.
1. Créez un moyen pour Xamarin. Android de charger un `Invoker` pour les classes virtuelles.
    - Cela évite d’avoir à marquer la classe dans notre `virtual` exemple `abstract`.
1. Générer `Invoker` automatiquement des classes pour l’incorporation .net
    - Cela va être compliqué, mais réalisable. Xamarin. Android est déjà en cours d’opération pour les projets de liaison Java.

Il y a beaucoup de travail à effectuer ici, mais ces améliorations apportées à l’incorporation .NET sont possibles.

## <a name="further-reading"></a>informations supplémentaires

- [Prise en main sur Android](~/tools/dotnet-embedding/get-started/java/android.md)
- [Recherche Android préliminaire](~/tools/dotnet-embedding/android/index.md)
- [Limitations de l’incorporation .NET](~/tools/dotnet-embedding/limitations.md)
- [Contribution au projet open source](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
- [Codes d’erreur et descriptions](~/tools/dotnet-embedding/errors.md)
