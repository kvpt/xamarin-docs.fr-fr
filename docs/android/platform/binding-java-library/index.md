---
title: Liaison d’une bibliothèque Java
description: La communauté Android a de nombreuses bibliothèques Java que vous voudrez peut-être utiliser dans votre application; ce guide explique comment intégrer les bibliothèques Java dans votre application Xamarin.Android en créant une bibliothèque de liaisons.
ms.prod: xamarin
ms.assetid: B39FF1D5-69C3-8A76-D268-C227A23C9485
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/01/2017
ms.openlocfilehash: d40a23076ec8f405e57ec40de47ec9ad2261d85d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027602"
---
# <a name="binding-a-java-library"></a>Liaison d’une bibliothèque Java

_La communauté Android a de nombreuses bibliothèques Java que vous voudrez peut-être utiliser dans votre application; ce guide explique comment intégrer les bibliothèques Java dans votre application Xamarin.Android en créant une bibliothèque de liaisons._

## <a name="overview"></a>Vue d’ensemble

L’écosystème des bibliothèques tierces pour Android est énorme. Pour cette raison, il est souvent logique d’utiliser une bibliothèque Android existante que de créer une nouvelle. Xamarin.Android offre deux façons d’utiliser ces bibliothèques :

- Créez une *bibliothèque de liaisons* qui enveloppe automatiquement la bibliothèque avec des emballages Cmd afin que vous puissiez invoquer le code Java via les appels C.

- Utilisez *l’interface native java* (*JNI*) pour invoquer directement les appels dans le code de la bibliothèque Java. JNI est un cadre de programmation qui permet au code Java d’appeler et d’être appelé par des applications ou des bibliothèques autochtones.

Ce guide explique la première option : comment créer une *bibliothèque de liaisons* qui enveloppe une ou plusieurs bibliothèques Java existantes dans un assemblage que vous pouvez lier à votre application. Pour plus d’informations sur l’utilisation de JNI, voir [Travailler avec JNI](~/android/platform/java-integration/working-with-jni.md).

Xamarin.Android implémente des fixations en utilisant *Managed Callable Wrappers* (*MCW*). MCW est un pont JNI qui est utilisé lorsque le code géré doit invoquer le code Java. Les emballages callables gérés fournissent également un support pour la sous-classification des types de Java et pour les méthodes virtuelles dominantes sur les types de Java. De même, chaque fois que le code Android runtime (ART) souhaite invoquer le code géré, il le fait via un autre pont JNI connu sous le nom Android Callable Wrappers (ACW). Cette [architecture](~/android/internals/architecture.md) est illustrée dans le diagramme suivant :

[![Architecture de pont Android JNI](images/architecture.png)](images/architecture.png#lightbox)

Une bibliothèque de liaisons est un assemblage contenant des emballages callables gérés pour les types Java. Par exemple, voici un `MyClass`type Java, , que nous voulons envelopper dans une bibliothèque de liaisons:

```java
package com.xamarin.mycode;

public class MyClass
{
    public String myMethod (int i) { ... }
}
```

Après avoir généré une bibliothèque de liaisons pour le **.jar** qui contient `MyClass`, nous pouvons l’instantané et appeler des méthodes sur elle à partir de C':

```csharp
var instance = new MyClass ();

string result = instance.MyMethod (42);
```

Pour créer cette bibliothèque de liaisons, vous utilisez le modèle Xamarin.Android *Java Bindings Library.* Le projet de liaison qui en résulte crée un assemblage .NET avec les classes MCW, **.jar** fichier(s), et des ressources pour les projets Android Library intégrés en elle. Vous pouvez également créer des bibliothèques de liaisons pour Android Archive (. AAR) fichiers et Eclipse Android Library projets. En faisant référence à l’assemblage DLL de bindings Library qui en résulte, vous pouvez réutiliser une bibliothèque Java existante dans votre projet Xamarin.Android.

Lorsque vous faites référence à des types dans votre bibliothèque de liaison, vous devez utiliser l’espace nom de votre bibliothèque de liaison. En règle générale, vous ajoutez une `using` directive en haut de vos fichiers source C', c’est-à-dire la version .NET namespace du nom du paquet Java. Par exemple, si le nom du paquet Java pour votre **.jar** lié est le suivant:

```csharp
com.company.package
```

Ensuite, vous mettez `using` la déclaration suivante en haut de vos fichiers source C pour accéder aux types dans le fichier **.jar** lié:

```csharp
using Com.Company.Package;
```

Lorsque vous reliez une bibliothèque Android existante, il est nécessaire de garder à l’esprit les points suivants :

- **Y a-t-il des dépendances externes pour la bibliothèque?** &ndash;Toute dépendance Java requise par la bibliothèque Android doit être incluse dans le projet Xamarin.Android en tant que **ReferenceJar** ou en tant que **EmbeddedReferenceJar**. Tous les assemblages autochtones doivent être ajoutés au projet contraignant en tant que **Conseiller EmbeddedNative**.  

- **Quelle version de l’API Android ne la bibliothèque Android cible?** &ndash;Il n’est pas possible de « dégrader » le niveau d’API Android ; s’assurer que le projet de liaison Xamarin.Android vise le même niveau D’API (ou supérieur) que la bibliothèque Android.

- **Quelle version du JDK a été utilisée pour compiler la bibliothèque?** &ndash;Des erreurs de liaison peuvent se produire si la bibliothèque Android a été construite avec une version différente de JDK que dans l’utilisation par Xamarin.Android. Si possible, recompliquez la bibliothèque Android en utilisant la même version du JDK qui est utilisé par votre installation de Xamarin.Android.

## <a name="build-actions"></a>Actions de génération

Lorsque vous créez une bibliothèque de liaisons, vous définissez *des actions de construction* sur le **.jar** ou . Les fichiers AAR que vous incorporez dans votre projet &ndash; de bibliothèque de liaisons chaque action de construction détermine comment le **.jar** ou . Le fichier AAR sera intégré (ou référencé par) votre bibliothèque de liaisons. La liste suivante résume ces actions de construction :

- `EmbeddedJar`&ndash; Encorps le **.jar** dans la bibliothèque de liaisons résultantE DLL comme une ressource intégrée. Il s’agit de l’action de construction la plus simple et la plus couramment utilisée. Utilisez cette option lorsque vous voulez que le **.jar** soit automatiquement compilé dans le code byte et emballé dans la bibliothèque de liaisons.

- `InputJar`&ndash; N’intègre pas le **.jar** dans la bibliothèque de liaisons résultante . DLL. Votre bibliothèque de liaisons . DLL aura une dépendance sur ce **.jar** à l’heure de course. Utilisez cette option lorsque vous ne voulez pas inclure le **.jar** dans votre bibliothèque de fixations (par exemple, pour des raisons de licence). Si vous utilisez cette option, vous devez vous assurer que l’entrée **.jar** est disponible sur l’appareil qui exécute votre application.

- `LibraryProjectZip`&ndash; Embeds un . Fichier AAR dans la Bibliothèque des liaisons résultante . DLL. Ceci est similaire à EmbeddedJar, sauf que vous pouvez accéder à des ressources (ainsi que du code) dans la limite . Fichier AAR. Utilisez cette option lorsque vous voulez intégrer un . AAR dans votre bibliothèque de liaisons.

- `ReferenceJar`&ndash; Specifie une référence **.jar**: une référence **.jar** est un **.jar** que l’un de vos **limites .jar** ou . Les fichiers AAR dépendent. Cette référence **.jar** est utilisé uniquement pour satisfaire les dépendances compilile-temps. Lorsque vous utilisez cette action de construction, les fixations C n’est pas créée pour la référence **.jar** et elle n’est pas intégrée dans la bibliothèque de liaisons qui en résulte. DLL. Utilisez cette option lorsque vous allez faire une bibliothèque de liaisons pour la référence **.jar,** mais ne l’ont pas encore fait. Cette action de construction est utile pour l’emballage multiple **.jar**s (et/ ou . AARs) en plusieurs bibliothèques de liaisons interdépendantes.

- `EmbeddedReferenceJar`&ndash; Intègre une référence **.jar** dans la bibliothèque de liaisons résultante . DLL. Utilisez cette action de construction lorsque vous voulez créer des liaisons C POUR l’entrée **.jar** (ou . AAR) et toute sa référence **.jar**(s) dans votre bibliothèque de liaisons.

- `EmbeddedNativeLibrary`&ndash; Embeds un natif **.so** dans la liaison. Cette action de construction est utilisée pour **.so** fichiers qui sont requis par le fichier **.jar** étant lié. Il peut être nécessaire de charger manuellement la bibliothèque **.so** avant d’exécuter le code de la bibliothèque Java. Ceci est décrit ci-dessous.

Ces actions de construction sont expliquées plus en détail dans les guides suivants.

En outre, les actions de construction suivantes sont utilisées pour aider à importer la documentation Java API et les convertir en documentation C XML:

- `JavaDocJar`est utilisé pour pointer vers l’archive Javadoc Jar pour une bibliothèque `FOOBAR-javadoc**.jar**`Java qui se conforme à un style de paquet Maven (généralement ).
- `JavaDocIndex`est utilisé pour `index.html` pointer vers le fichier dans la documentation de référence API HTML.
- `JavaSourceJar`est utilisé `JavaDocJar`pour compléter , d’abord pour générer `JavaDocIndex`JavaDoc à partir de sources, puis traiter `FOOBAR-sources**.jar**`les résultats comme , pour une bibliothèque Java qui se conforme à un style de paquet Maven (généralement ).

La documentation API doit être le doclet par défaut de Java8, Java7 ou Java6 SDK (ils sont tous différents format), ou le style DroidDoc.

## <a name="including-a-native-library-in-a-binding"></a>Inclure une bibliothèque autochtone dans une liaison

Il peut être nécessaire d’inclure une bibliothèque **.so** dans un projet de liaison Xamarin.Android dans le cadre de la liaison d’une bibliothèque Java. Lorsque le code Java enveloppé s’exécute, Xamarin.Android ne parvient pas à faire l’appel JNI et le message d’erreur _java.lang.UnsatisfiedLinkError: Méthode indigène non trouvé:_ apparaîtra dans le logcat pour l’application.

Le correctif pour cela est de charger manuellement `Java.Lang.JavaSystem.LoadLibrary`la bibliothèque **.so** avec un appel à . Par exemple en supposant qu’un projet Xamarin.Android a partagé la bibliothèque **libpocketsphinx_jni.so** inclus dans le projet contraignant avec une action de construction de **EmbeddedNativeLibrary**, l’extrait suivant (exécuté avant d’utiliser la bibliothèque partagée) chargera la bibliothèque **.so:**

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="adapting-java-apis-to-ceparsl"></a>Adapter les API Java à C&eparsl;

Le générateur de liaison Xamarin.Android va changer certains idiomes Java et les modèles pour correspondre à des modèles .NET. La liste suivante décrit comment Java est cartographié à C/.NET:

- _Setter / Méthodes Getter_ à Java sont _des propriétés_ en .NET.

- _Champs_ à Java sont _des propriétés_ en .NET.

- _Les auditeurs/interfaces d’écoute_ à Java sont _des événements_ en .NET. Les paramètres des méthodes dans les interfaces de `EventArgs` rappel seront représentés par une sous-classe.

- Une _classe statique nichée_ à Java est une _classe nichée_ en .NET.

- Une _classe intérieure_ à Java est une classe _nichée_ avec un constructeur d’instance en C.

## <a name="binding-scenarios"></a>Scénarios contraignants

Les guides de scénarios contraignants suivants peuvent vous aider à lier une bibliothèque Java (ou des bibliothèques) pour l’incorporation dans votre application :

- [Liaison a . JAR](~/android/platform/binding-java-library/binding-a-jar.md) est une procédure pas à pas pour la création de bibliothèques de liaisons pour les fichiers **.jar.**

- [Lier un . AAR](~/android/platform/binding-java-library/binding-an-aar.md) est une procédure pas à pas pour la création de bibliothèques de liaisons pour . Fichiers AAR. Lisez cette procédure pas à pas pour apprendre à lier les bibliothèques Android Studio.

- [Lier un projet de bibliothèque Eclipse](~/android/platform/binding-java-library/binding-a-library-project.md) est une procédure pas à pas pour créer des bibliothèques contraignantes à partir de projets de bibliothèque Android. Lisez cette procédure pas à pas pour apprendre à lier Eclipse Android Library Projects.

- [La personnalisation des liaisons](~/android/platform/binding-java-library/customizing-bindings/index.md) explique comment apporter des modifications manuelles à la liaison pour résoudre les erreurs de construction et façonner l’API qui en résulte afin qu’elle soit plus « C-like ».

- [Les liaisons de dépannage](~/android/platform/binding-java-library/troubleshooting-bindings.md) répertorient les scénarios d’erreur contraignantes courants, expliquent les causes possibles et offrent des suggestions pour résoudre ces erreurs.

## <a name="related-links"></a>Liens connexes

- [Travailler avec JNI](~/android/platform/java-integration/working-with-jni.md)
- [Métadonnées GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
- [Utilisation de bibliothèques natives](~/android/platform/native-libraries.md)
