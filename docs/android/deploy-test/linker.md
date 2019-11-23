---
title: Liaison sur Android
ms.prod: xamarin
ms.assetid: 3528E195-AA74-90AF-B5F3-3B65FB4F0BB8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/30/2018
ms.openlocfilehash: 729dede97a9b153738ca72af499d4d1e9d77fae0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021443"
---
# <a name="linking-on-android"></a>Liaison sur Android

Les applications Xamarin.Android utilisent un *éditeur de liens* pour réduire la taille de l’application. L’éditeur de liens effectue une analyse statique de votre application pour déterminer les assemblys réellement utilisés, les types réellement utilisés et les membres réellement utilisés. L’éditeur de liens se comporte ensuite comme un *récupérateur de mémoire*, qui recherche en permanence des assemblys, des types des membres qui sont référencés jusqu'à la fermeture complète des assemblys, types et membres référencés. Tous les autres éléments sont *ignorés*.

Par exemple, [Hello, Android](https://docs.microsoft.com/samples/xamarin/monodroid-samples/hellom4a) :

|Configuration|1.2.0 Size|4.0.1 Size|
|---|---|---|
|Version sans liaison :|14,0 Mo|16,0 Mo|
|Version avec liaison :|4,2 Mo|2,9 Mo|

La liaison produit un package qui est de 30 % de la taille du package d’origine (non lié) dans 1.2.0 et de 18 % du package non lié dans 4.0.1.

## <a name="control"></a>Contrôle

La liaison est basée sur *l’analyse statique*. Par conséquent, tout ce qui dépend de l’environnement d’exécution ne sera pas détecté :

```csharp
// To play along at home, Example must be in a different assembly from MyActivity.
public class Example {
    // Compiler provides default constructor...
}

[Activity (Label="Linker Example", MainLauncher=true)]
public class MyActivity {
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Will this work?
        var o = Activator.CreateInstance (typeof (ExampleLibrary.Example));
    }
}
```

### <a name="linker-behavior"></a>Comportement de l'éditeur de liens

Le principal mécanisme de contrôle de l’éditeur de liens est la liste déroulante **Comportement de l’éditeur de liens** (*Liaison* dans Visual Studio) dans la boîte de dialogue **Options du projet**. Trois options sont disponibles :

1. **Ne pas lier** (*Aucune* dans Visual Studio)
1. **Lier les assemblys du SDK** (*Uniquement les assemblys du SDK*)
1. **Lier tous les assemblys** (*Assemblys utilisateur et du SDK*)

L’option **Ne pas lier** désactive l’éditeur de liens ; l’exemple de taille des applications « Version sans liaison » ci-dessus utilisait ce comportement. Cela est utile pour la résolution des échecs d’exécution, pour déterminer si l’éditeur de liens est responsable. Ce paramètre n’est généralement pas recommandé pour les versions de production.

L’option **Lier les assemblys du SDK** lie uniquement les [assemblys qui sont fournis avec Xamarin.Android](~/cross-platform/internals/available-assemblies.md).
Tous les autres assemblys (comme votre code) ne sont pas liés.

L’option **Lier tous les assemblys** lie tous les assemblys, ce qui signifie que votre code peut également être supprimé s’il n’existe aucune référence statique.

L’exemple ci-dessus fonctionne avec les options *Ne pas lier* et *Lier les assemblys du SDK* et échoue avec le comportement *Lier tous les assemblys*, en générant l’erreur suivante :

```shell
E/mono    (17755): [0xafd4d440:] EXCEPTION handling: System.MissingMethodException: Default constructor not found for type ExampleLibrary.Example.
I/MonoDroid(17755): UNHANDLED EXCEPTION: System.MissingMethodException: Default constructor not found for type ExampleLibrary.Example.
I/MonoDroid(17755): at System.Activator.CreateInstance (System.Type,bool) <0x00180>
I/MonoDroid(17755): at System.Activator.CreateInstance (System.Type) <0x00017>
I/MonoDroid(17755): at LinkerScratch2.Activity1.OnCreate (Android.OS.Bundle) <0x00027>
I/MonoDroid(17755): at Android.App.Activity.n_OnCreate_Landroid_os_Bundle_ (intptr,intptr,intptr) <0x00057>
I/MonoDroid(17755): at (wrapper dynamic-method) object.95bb4fbe-bef8-4e5b-8e99-ca83a5d7a124 (intptr,intptr,intptr) <0x00033>
E/mono    (17755): [0xafd4d440:] EXCEPTION handling: System.MissingMethodException: Default constructor not found for type ExampleLibrary.Example.
E/mono    (17755):
E/mono    (17755): Unhandled Exception: System.MissingMethodException: Default constructor not found for type ExampleLibrary.Example.
E/mono    (17755):   at System.Activator.CreateInstance (System.Type type, Boolean nonPublic) [0x00000] in <filename unknown>:0
E/mono    (17755):   at System.Activator.CreateInstance (System.Type type) [0x00000] in <filename unknown>:0
E/mono    (17755):   at LinkerScratch2.Activity1.OnCreate (Android.OS.Bundle bundle) [0x00000] in <filename unknown>:0
E/mono    (17755):   at Android.App.Activity.n_OnCreate_Landroid_os_Bundle_ (IntPtr jnienv, IntPtr native__this, IntPtr native_savedInstanceState) [0x00000] in <filename unknown>:0
E/mono    (17755):   at (wrapper dynamic-method) object:95bb4fbe-bef8-4e5b-8e99-ca83a5d7a124 (intptr,intptr,intptr)
```

### <a name="preserving-code"></a>Conservation du code

L’éditeur de liens supprimera parfois du code que vous souhaitez conserver. Exemple :

- Vous pouvez obtenir le code que vous appelez dynamiquement via `System.Reflection.MemberInfo.Invoke`.

- Si vous instanciez dynamiquement des types, vous souhaiterez éventuellement conserver le constructeur par défaut de vos types.

- Si vous utilisez la sérialisation XML, vous souhaiterez éventuellement conserver les propriétés de vos types.

Dans ce cas, vous pouvez utiliser l’attribut [Android.Runtime.Preserve](xref:Android.Runtime.PreserveAttribute). Chaque membre qui n’est pas lié de manière statique par l’application est sujet à suppression. Ainsi, cet attribut peut être utilisé pour marquer les membres qui ne sont pas statiquement référencés, mais sont toujours requis par votre application. Vous pouvez appliquer cet attribut à chaque membre d’un type, ou au type lui-même.

Dans l’exemple suivant, cet attribut est utilisé pour conserver le constructeur de la classe `Example` :

```csharp
public class Example
{
    [Android.Runtime.Preserve]
    public Example ()
    {
    }
}
```

Si vous souhaitez conserver le type entier, vous pouvez utiliser la syntaxe d’attribut suivante :

```csharp
[Android.Runtime.Preserve (AllMembers = true)]
```

Par exemple, dans le fragment de code suivant, la classe `Example` est conservée pour la sérialisation XML :

```csharp
[Android.Runtime.Preserve (AllMembers = true)]
class Example
{
    // Compiler provides default constructor...
}
```

Parfois, vous souhaitez conserver certains membres, mais uniquement si le type conteneur a été conservé. Dans ce cas, utilisez la syntaxe d’attribut suivante :

```csharp
[Android.Runtime.Preserve (Conditional = true)]
```

Si vous ne souhaitez pas prendre une dépendance sur les bibliothèques Xamarin &ndash;, par exemple, vous créez une bibliothèque de classes portable (PCL) inter-plateformes &ndash; et pouvez toujours utiliser l’attribut `Android.Runtime.Preserve`. Pour ce faire, déclarez une classe `PreserveAttribute` au sein de l’espace de noms `Android.Runtime` comme suit :

```csharp
namespace Android.Runtime
{
    public sealed class PreserveAttribute : System.Attribute
    {
        public bool AllMembers;
        public bool Conditional;
    }
}
```

Dans les exemples ci-dessus, l’attribut `Preserve` est déclaré dans l’espace de noms `Android.Runtime`. Toutefois, vous pouvez utiliser l’attribut `Preserve` dans n’importe quel espace de noms, car l’éditeur de liens le recherche par nom de type.

### <a name="falseflag"></a>falseflag

Si l’attribut [Preserve] ne peut pas être utilisé, il est souvent utile de fournir un bloc de code afin que l’éditeur de liens pense que le type est utilisé, tout en empêchant le bloc de code d’être exécuté lors de l’exécution. Pour utiliser cette technique, nous pouvons procéder ainsi :

```csharp
[Activity (Label="Linker Example", MainLauncher=true)]
class MyActivity {

#pragma warning disable 0219, 0649
    static bool falseflag = false;
    static MyActivity ()
    {
        if (falseflag) {
            var ignore = new Example ();
        }
    }
#pragma warning restore 0219, 0649

    // ...
}
```

### <a name="linkskip"></a>linkskip

Il est possible de spécifier qu’un jeu d’assemblys fourni par l’utilisateur ne doit pas être lié du tout, tout en permettant aux autres assemblys utilisateur d’être ignorés en cliquant sur le comportement *Lier les assemblys du SDK* à l’aide de la [propriété MSBuild AndroidLinkSkip](~/android/deploy-test/building-apps/build-process.md) :

```xml
<PropertyGroup>
    <AndroidLinkSkip>Assembly1;Assembly2</AndroidLinkSkip>
</PropertyGroup>
```

### <a name="linkdescription"></a>LinkDescription

[`@(LinkDescription)`](~/android/deploy-test/building-apps/build-process.md)
**L’action de génération de build** peut être utilisée sur les fichiers qui peuvent contenir un [Fichier de configuration d’éditeur de liens personnalisé](~/cross-platform/deploy-test/linker.md).
fichier. Des fichiers de configuration d’éditeur de liens personnalisés peuvent être nécessaires pour conserver les membres `internal` ou `private` qui doivent l’être.

### <a name="custom-attributes"></a>Attributs personnalisés

Lorsqu’un assembly est lié, les types d’attributs personnalisés suivants sont supprimés de tous les membres :

- System.ObsoleteAttribute
- System.MonoDocumentationNoteAttribute
- System.MonoExtensionAttribute
- System.MonoInternalNoteAttribute
- System.MonoLimitationAttribute
- System.MonoNotSupportedAttribute
- System.MonoTODOAttribute
- System.Xml.MonoFIXAttribute

Lorsqu’un assembly est lié, les types d’attributs personnalisés suivants sont supprimés de tous les membres dans les builds de production :

- System.Diagnostics.DebuggableAttribute
- System.Diagnostics.DebuggerBrowsableAttribute
- System.Diagnostics.DebuggerDisplayAttribute
- System.Diagnostics.DebuggerHiddenAttribute
- System.Diagnostics.DebuggerNonUserCodeAttribute
- System.Diagnostics.DebuggerStepperBoundaryAttribute
- System.Diagnostics.DebuggerStepThroughAttribute
- System.Diagnostics.DebuggerTypeProxyAttribute
- System.Diagnostics.DebuggerVisualizerAttribute

## <a name="related-links"></a>Liens connexes

- [Configuration de l’éditeur de liens personnalisé](~/cross-platform/deploy-test/linker.md)
- [Liaison sur iOS](~/ios/deploy-test/linker.md)
