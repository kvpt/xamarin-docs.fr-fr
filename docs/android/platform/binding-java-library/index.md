---
title: Liaison d’une bibliothèque Java
description: La communauté Android possède de nombreuses bibliothèques Java que vous pouvez utiliser dans votre application. ce guide explique comment incorporer des bibliothèques Java dans votre application Xamarin. Android en créant une bibliothèque de liaisons.
ms.prod: xamarin
ms.assetid: B39FF1D5-69C3-8A76-D268-C227A23C9485
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/01/2017
ms.openlocfilehash: d40a23076ec8f405e57ec40de47ec9ad2261d85d
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027602"
---
# <a name="binding-a-java-library"></a>Liaison d’une bibliothèque Java

_La communauté Android possède de nombreuses bibliothèques Java que vous pouvez utiliser dans votre application. ce guide explique comment incorporer des bibliothèques Java dans votre application Xamarin. Android en créant une bibliothèque de liaisons._

## <a name="overview"></a>Overview

L’écosystème de bibliothèque tiers pour Android est énorme. Pour cette raison, il est souvent judicieux d’utiliser une bibliothèque Android existante plutôt que d’en créer une nouvelle. Xamarin. Android offre deux façons d’utiliser ces bibliothèques :

- Créer une *bibliothèque de liaisons* qui encapsule automatiquement la bibliothèque avec C# des wrappers afin que vous puissiez appeler du C# code Java via des appels.

- Utilisez*JNI*( *Java Native Interface* ) pour appeler directement des appels dans le code de la bibliothèque Java. JNI est une infrastructure de programmation qui permet au code Java d’appeler et d’être appelé par des applications ou des bibliothèques natives.

Ce guide explique la première option : comment créer une *bibliothèque de liaisons* qui encapsule une ou plusieurs bibliothèques Java existantes dans un assembly que vous pouvez lier à votre application. Pour plus d’informations sur l’utilisation de JNI, consultez [utilisation de JNI](~/android/platform/java-integration/working-with-jni.md).

Xamarin. Android implémente des liaisons à l’aide de *wrappers pouvant être appelés managés* (*MCW*). MCW est un pont JNI utilisé lorsque du code managé doit appeler du code Java. Les wrappers pouvant être appelés sont également pris en charge pour le sous-classement des types Java et pour la substitution des méthodes virtuelles sur les types Java. De même, chaque fois que le code Android Runtime (ART) souhaite appeler du code managé, il le fait via un autre pont JNI connu sous le nom de wrappers pouvant être appelés Android (ACW). Cette [architecture](~/android/internals/architecture.md) est illustrée dans le diagramme suivant :

[architecture des ponts JNI ![Android](images/architecture.png)](images/architecture.png#lightbox)

Une bibliothèque de liaisons est un assembly contenant des wrappers pouvant être appelés managés pour les types Java. Par exemple, voici un type Java, `MyClass`, que nous voulons encapsuler dans une bibliothèque de liaisons :

```java
package com.xamarin.mycode;

public class MyClass
{
    public String myMethod (int i) { ... }
}
```

Une fois que nous avons généré une bibliothèque de liaisons pour le fichier **. jar** qui contient `MyClass`, nous pouvons l’instancier et C#appeler des méthodes sur celle-ci à partir de :

```csharp
var instance = new MyClass ();

string result = instance.MyMethod (42);
```

Pour créer cette bibliothèque de liaisons, vous utilisez le modèle de *bibliothèque de liaisons Java* Xamarin. Android. Le projet de liaison qui en résulte crée un assembly .NET avec les classes MCW, le ou les fichiers **. jar** et les ressources pour les projets de bibliothèque Android incorporés dans celui-ci. Vous pouvez également créer des bibliothèques de liaisons pour l’archive Android (. AAR) et les projets de bibliothèque Android Eclipse. En référençant l’assembly de la DLL de la bibliothèque de liaisons résultante, vous pouvez réutiliser une bibliothèque Java existante dans votre projet Xamarin. Android.

Quand vous référencez des types dans votre bibliothèque de liaisons, vous devez utiliser l’espace de noms de votre bibliothèque de liaisons. En règle générale, vous ajoutez une directive `using` en haut de C# vos fichiers sources qui est la version de l’espace de noms .net du nom du package java. Par exemple, si le nom du package java pour votre fichier **. jar** lié est le suivant :

```csharp
com.company.package
```

Vous placeriez ensuite l’instruction `using` suivante en haut de vos C# fichiers sources pour accéder aux types dans le fichier **. jar** lié :

```csharp
using Com.Company.Package;
```

Lors de la liaison d’une bibliothèque Android existante, il est nécessaire de garder les points suivants à l’esprit :

- **Existe-t-il des dépendances externes pour la bibliothèque ?** &ndash; les dépendances Java requises par la bibliothèque Android doivent être incluses dans le projet Xamarin. Android en tant que **ReferenceJar** ou **EmbeddedReferenceJar**. Tous les assemblys natifs doivent être ajoutés au projet de liaison en tant que **EmbeddedNativeLibrary**.  

- **Quelle version de l’API Android la cible de la bibliothèque Android ?** &ndash; il n’est pas possible de « rétrograder » le niveau de l’API Android. Assurez-vous que le projet de liaison Xamarin. Android cible le même niveau d’API (ou une version ultérieure) que la bibliothèque Android.

- **Quelle version du JDK a été utilisée pour compiler la bibliothèque ?** &ndash; erreurs de liaison peuvent se produire si la bibliothèque Android a été créée avec une version différente de JDK et qu’elle est utilisée par Xamarin. Android. Si possible, recompilez la bibliothèque Android en utilisant la même version du JDK que celle utilisée par votre installation de Xamarin. Android.

## <a name="build-actions"></a>Actions de génération

Lorsque vous créez une bibliothèque de liaisons, vous définissez des *actions de génération* sur le fichier **. jar** ou. Les fichiers AAR que vous incorporez dans votre projet de bibliothèque de liaisons &ndash; chaque action de génération détermine la manière dont le fichier **. jar** ou. Le fichier AAR sera incorporé dans (ou référencé par) votre bibliothèque de liaisons. La liste suivante résume ces actions de génération :

- `EmbeddedJar` &ndash; incorpore le fichier **. jar** dans la dll de la bibliothèque de liaisons résultante en tant que ressource incorporée. Il s’agit de l’action de génération la plus simple et la plus couramment utilisée. Utilisez cette option lorsque vous souhaitez que le fichier **. jar** soit automatiquement compilé en code d’octet et empaqueté dans la bibliothèque de liaisons.

- `InputJar` &ndash; n’incorpore pas le fichier **. jar** dans la bibliothèque de liaisons résultante. DLL. Votre bibliothèque de liaisons. La DLL aura une dépendance sur ce fichier **. jar** au moment de l’exécution. Utilisez cette option lorsque vous ne souhaitez pas inclure le fichier **. jar** dans votre bibliothèque de liaisons (par exemple, pour des raisons de licence). Si vous utilisez cette option, vous devez vous assurer que le fichier Input **. jar** est disponible sur l’appareil qui exécute votre application.

- `LibraryProjectZip` &ndash; incorpore un. AAR fichier dans la bibliothèque de liaisons résultante. DLL. Cela est similaire à EmbeddedJar, à ceci près que vous pouvez accéder aux ressources (ainsi qu’au code) dans le lié. Fichier AAR. Utilisez cette option lorsque vous souhaitez incorporer un. AAR dans votre bibliothèque de liaisons.

- `ReferenceJar` &ndash; spécifie un fichier Reference **. jar**: une référence **. jar** est un fichier **.** jar dont l’un des **. jar** ou. Les fichiers AAR dépendent de. Ce fichier Reference **. jar** est utilisé uniquement pour répondre aux dépendances au moment de la compilation. Lorsque vous utilisez cette action de génération C# , les liaisons ne sont pas créées pour Reference **. jar** et ne sont pas incorporées dans la bibliothèque de liaisons résultante. DLL. Utilisez cette option lorsque vous créez une bibliothèque de liaisons pour la référence **. jar** , mais que vous ne l’avez pas encore fait. Cette action de génération est utile pour l’empaquetage de plusieurs **. jar**s (et/ou. AARs) dans plusieurs bibliothèques de liaisons interdépendantes.

- `EmbeddedReferenceJar` &ndash; incorpore un fichier Reference **. jar** dans la bibliothèque de liaisons résultante. DLL. Utilisez cette action de génération lorsque vous souhaitez créer C# des liaisons pour le fichier d’entrée **. jar** (ou. AAR) et tous ses fichier Reference **. jar**dans votre bibliothèque de liaisons.

- `EmbeddedNativeLibrary` &ndash; incorpore un Native **. so** dans la liaison. Cette action de génération est utilisée pour les fichiers **. so** requis par le fichier **. jar** qui est lié. Il peut être nécessaire de charger manuellement la bibliothèque **. so** avant d’exécuter le code à partir de la bibliothèque Java. Cela est décrit ci-dessous.

Ces actions de génération sont expliquées plus en détail dans les guides suivants.

En outre, les actions de génération suivantes permettent d’importer la documentation de l’API Java et de C# les convertir en documentation XML :

- `JavaDocJar` est utilisé pour pointer vers l’archive Javadoc jar pour une bibliothèque Java qui est conforme à un style de package Maven (généralement `FOOBAR-javadoc**.jar**`).
- `JavaDocIndex` est utilisé pour pointer vers `index.html` fichier dans la documentation de référence de l’API HTML.
- `JavaSourceJar` est utilisé pour compléter `JavaDocJar`, pour commencer par générer des valeurs JavaDoc à partir de sources, puis pour traiter les résultats comme des `JavaDocIndex`, pour une bibliothèque Java qui est conforme à un style de package Maven (généralement `FOOBAR-sources**.jar**`).

La documentation de l’API doit être la Doclet par défaut de Java8, Java7 ou java6 SDK (ils ont un format différent) ou le style DroidDoc.

## <a name="including-a-native-library-in-a-binding"></a>Inclusion d’une bibliothèque native dans une liaison

Il peut être nécessaire d’inclure une bibliothèque **. so** dans un projet de liaison Xamarin. Android dans le cadre de la liaison d’une bibliothèque Java. Lors de l’exécution du code Java encapsulé, Xamarin. Android ne parvient pas à effectuer l’appel JNI et le message d’erreur _java. lang. UnsatisfiedLinkError : méthode Native introuvable :_ s’affiche dans le logcat pour l’application.

Pour résoudre ce problème, vous devez charger manuellement la bibliothèque **. so** avec un appel à `Java.Lang.JavaSystem.LoadLibrary`. Par exemple, en supposant qu’un projet Xamarin. Android a une bibliothèque partagée **libpocketsphinx_jni. donc** inclus dans le projet de liaison avec une action de génération **EmbeddedNativeLibrary**, l’extrait de code suivant (exécuté avant d’utiliser la bibliothèque partagée) chargera la bibliothèque **. so** :

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="adapting-java-apis-to-ceparsl"></a>Adaptation des API Java à C&eparsl;

Le générateur de liaisons Xamarin. Android modifie certains idiomes et modèles Java pour qu’ils correspondent aux modèles .NET. La liste suivante décrit comment Java est mappé à C#/.net :

- Les _méthodes setter/Getter_ dans Java sont des _Propriétés_ dans .net.

- Les _champs_ dans Java sont des _Propriétés_ dans .net.

- Les _interfaces d’écouteur/écouteur_ en Java sont des _événements_ dans .net. Les paramètres des méthodes dans les interfaces de rappel sont représentés par une sous-classe `EventArgs`.

- Une _classe imbriquée statique_ dans Java est une _classe imbriquée_ dans .net.

- Une _classe interne_ dans Java est une _classe imbriquée_ avec un constructeur d’instance C#dans.

## <a name="binding-scenarios"></a>Scénarios de liaison

Les guides de scénario de liaison suivants peuvent vous aider à lier une bibliothèque Java (ou des bibliothèques) pour l’incorporation dans votre application :

- [Liaison d’un. JAR](~/android/platform/binding-java-library/binding-a-jar.md) est une procédure pas à pas pour la création de bibliothèques de liaisons pour les fichiers **. jar** .

- [Liaison d’un. AAR](~/android/platform/binding-java-library/binding-an-aar.md) est une procédure pas à pas pour la création de bibliothèques de liaisons pour. Fichiers AAR. Lisez cette procédure pas à pas pour savoir comment lier des bibliothèques de Android Studio.

- La [liaison d’un projet de bibliothèque Eclipse](~/android/platform/binding-java-library/binding-a-library-project.md) est une procédure pas à pas pour la création de bibliothèques de liaison à partir de projets de bibliothèque Android. Lisez cette procédure pas à pas pour savoir comment lier des projets de bibliothèque Android Eclipse.

- [Personnalisation des liaisons](~/android/platform/binding-java-library/customizing-bindings/index.md) explique comment apporter des modifications manuelles à la liaison pour résoudre les erreurs de build et mettre en forme l’API résultante afinC#qu’elle soit plus « -like ».

- [Dépannage des liaisons](~/android/platform/binding-java-library/troubleshooting-bindings.md) répertorie les scénarios d’erreur de liaison courants, explique les causes possibles et propose des suggestions pour la résolution de ces erreurs.

## <a name="related-links"></a>Liens connexes

- [Utilisation de JNI](~/android/platform/java-integration/working-with-jni.md)
- [Métadonnées GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
- [Utilisation de bibliothèques natives](~/android/platform/native-libraries.md)
