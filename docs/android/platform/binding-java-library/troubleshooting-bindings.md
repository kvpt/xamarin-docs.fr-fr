---
title: Résolution des problèmes de liaisons
description: Cet article résume les erreurs communes de serveur qui peuvent se produire lors de la génération de liaisons, ainsi que des causes possibles et des moyens suggérés pour les résoudre.
ms.prod: xamarin
ms.assetid: BB81FCCF-F7BF-4C78-884E-F02C49AA819A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 0c273797d7512f062260e49e0f71fdd1132f037b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291973"
---
# <a name="troubleshooting-bindings"></a>Résolution des problèmes de liaisons

_Cet article résume les erreurs communes de serveur qui peuvent se produire lors de la génération de liaisons, ainsi que des causes possibles et des moyens suggérés pour les résoudre._

## <a name="overview"></a>Vue d’ensemble

Lier une bibliothèque Android (un fichier **.aar** ou **un .jar)** est rarement une affaire simple; il nécessite généralement des efforts supplémentaires pour atténuer les problèmes qui résultent des différences entre Java et .NET.
Ces problèmes empêcheront Xamarin.Android de lier la bibliothèque Android et se présentent comme des messages d’erreur dans le journal de construction. Ce guide fournira quelques conseils pour dépanner les problèmes, énumérer certains des problèmes/scénarios les plus courants, et fournira des solutions possibles pour réussir à lier la bibliothèque Android.

Lorsque vous reliez une bibliothèque Android existante, il est nécessaire de garder à l’esprit les points suivants :

- **Les dépendances externes pour la bibliothèque** &ndash; Toute dépendance Java requis par la bibliothèque Android doit être inclus dans le projet Xamarin.Android comme **un ReferenceJar** ou comme un **EmbeddedReferenceJar**.

- **Le niveau d’API Android que la bibliothèque Android cible** &ndash; Il n’est pas possible de "dégrader" le niveau d’API Android; s’assurer que le projet de liaison Xamarin.Android vise le même niveau D’API (ou supérieur) que la bibliothèque Android.

- **La version de l’Android JDK qui a été utilisé pour emballer les** &ndash; erreurs de liaison bibliothèque Android peut se produire si la bibliothèque Android a été construit avec une version différente de JDK que celle utilisée par Xamarin.Android. Si possible, recompliquez la bibliothèque Android en utilisant la même version du JDK qui est utilisé par votre installation de Xamarin.Android.

La première étape pour dépanner les problèmes avec la liaison d’une bibliothèque Xamarin.Android est de permettre [la sortie diagnostique MSBuild](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).
Après avoir permis la sortie de diagnostic, reconstruire le projet de liaison Xamarin.Android et examiner le journal de construction pour localiser des indices sur ce que la cause du problème est.

Il peut également s’avérer utile pour décomposer la bibliothèque Android et d’examiner les types et les méthodes que Xamarin.Android essaie de lier. Ceci est couvert plus en détail plus tard dans ce guide.

## <a name="decompiling-an-android-library"></a>Décomposition d’une bibliothèque Android

L’inspection des classes et des méthodes des classes Java peut fournir des informations précieuses qui aideront à lier une bibliothèque.
[JD-GUI](http://jd.benow.ca/) est un utilitaire graphique qui peut afficher le code source Java à partir des fichiers **CLASS** contenus dans un JAR. Il peut être exécuté comme une application autonome ou comme un plug-in pour IntelliJ ou Eclipse.

Pour compiler une bibliothèque Android ouvrir le **. Fichier JAR** avec le décomposeur Java. Si la bibliothèque est un **. Fichier AAR,** il est nécessaire d’extraire les classes de **fichiers.jar** du fichier d’archives. Ce qui suit est une capture d’écran d’échantillon d’utiliser JD-GUI pour analyser le [JAR Picasso:](https://square.github.io/picasso/)

![Utilisation du Java Decompiler pour analyser picasso-2.5.2.jar](troubleshooting-bindings-images/troubleshoot-bindings-01.png)

Une fois que vous avez décomposé la bibliothèque Android, examinez le code source. D’une manière générale, recherchez :

- **Les classes qui ont des caractéristiques d’obscurcissement** &ndash; Caractéristiques des classes obfusquées comprennent :

  - Le nom de **$** la classe comprend un , c’est-à-dire **une classe$.**
  - Le nom de la classe est entièrement compromis des caractères minuscules, c’est-à-dire **a.class**      

- &ndash; **Build Action** ** `import` déclarations pour les bibliothèques non réfrécrées** Identifier la bibliothèque non référée et ajouter ces dépendances au projet de liaison Xamarin.Android avec une action build de **ReferenceJar** ou **EmbedddedReferenceJar**.

> [!NOTE]
> La décomposition d’une bibliothèque Java peut être interdite ou soumise à des restrictions légales basées sur les lois locales ou la licence en vertu de laquelle la bibliothèque Java a été publiée. Si nécessaire, enrôlez les services d’un professionnel du droit avant de tenter de décompater une bibliothèque Java et d’inspecter le code source.

## <a name="inspect-apixml"></a>Inspecter l’API. Xml

Dans le cadre de la construction d’un projet contraignant, Xamarin.Android va générer un nom de fichier XML **obj/Debug/api.xml**:

![Généré api.xml sous obj/Debug](troubleshooting-bindings-images/troubleshoot-bindings-02.png)

Ce fichier fournit une liste de toutes les API Java que Xamarin.Android essaie de lier. Le contenu de ce fichier peut aider à identifier les types ou méthodes manquants, la liaison en double. Bien que l’inspection de ce fichier soit fastidieuse et longue, elle peut fournir des indices sur ce qui pourrait causer des problèmes contraignants. Par exemple, **api.xml** peut révéler qu’une propriété renvoie un type inapproprié, ou qu’il existe deux types qui partagent le même nom géré.

## <a name="known-issues"></a>Problèmes connus

Cette section énumérera certains des messages d’erreur ou des symptômes courants que mon occurr lorsque j’essaie de lier une bibliothèque Android.

### <a name="problem-java-version-mismatch"></a>Problème: Java Version Mismatch

Parfois, les types ne seront pas générés ou des plantages inattendus peuvent se produire parce que vous utilisez soit une version plus récente ou plus ancienne de Java par rapport à ce que la bibliothèque a été compilée avec. Recompliquez la bibliothèque Android avec la même version du JDK que votre projet Xamarin.Android utilise.

### <a name="problem-at-least-one-java-library-is-required"></a>Problème: Au moins une bibliothèque Java est nécessaire

Vous recevez l’erreur "au moins une bibliothèque Java est nécessaire," même si un . JAR a été ajouté.

#### <a name="possible-causes"></a>Causes possibles :

Assurez-vous que l’action de construction est définie à `EmbeddedJar`. Depuis il ya plusieurs actions de construction pour . Fichiers JAR (tels `EmbeddedJar` `ReferenceJar` que `EmbeddedReferenceJar` `InputJar`, , et ), le générateur de liaison ne peut pas automatiquement deviner lequel utiliser par défaut. Pour plus d’informations sur les actions de construction, voir [Build Actions](~/android/platform/binding-java-library/index.md).

### <a name="problem-binding-tools-cannot-load-the-jar-library"></a>Problème: outils de liaison ne peut pas charger le . Bibliothèque JAR

Le générateur de bibliothèque de liaison ne parvient pas à charger le . Bibliothèque JAR.

#### <a name="possible-causes"></a>Causes possibles

Certains. Les bibliothèques JAR qui utilisent l’obscurcissement du code (via des outils tels que Proguard) ne peuvent pas être chargées par les outils Java. Puisque notre outil utilise la réflexion Java et la bibliothèque d’ingénierie de code d’asM, ces outils dépendants peuvent rejeter les bibliothèques obscurcies tandis que les outils d’exécution d’Android peuvent passer. La solution de contournement pour cela est de reliser à la main ces bibliothèques au lieu d’utiliser le générateur de liaison.

### <a name="problem-missing-c-types-in-generated-output"></a>Problème : Types de C manquants dans la production.

La liaison **.dll** construit, mais manque certains types de Java, ou la source C générée ne s’accumule pas en raison d’une erreur indiquant qu’il ya des types manquants.

#### <a name="possible-causes"></a>Causes possibles :

Cette erreur peut se produire en raison de plusieurs raisons énumérées ci-dessous :

- La bibliothèque étant liée peut faire référence à une deuxième bibliothèque Java. Si l’API publique pour la bibliothèque reliée utilise des types de la deuxième bibliothèque, vous devez également faire référence à une liaison gérée pour la deuxième bibliothèque.

- Il est possible qu’une bibliothèque ait été injectée en raison de la réflexion Java, semblable à la raison de l’erreur de charge de bibliothèque ci-dessus, provoquant le chargement inattendu des métadonnées. L’outillage de Xamarin.Android ne peut pas résoudre cette situation actuellement. Dans un tel cas, la bibliothèque doit être liée manuellement.

- Il y avait un bug dans .NET 4.0 runtime qui n’a pas réussi à charger des assemblages quand il aurait dû. Ce problème a été corrigé dans le temps d’exécution .NET 4.5.

- Java permet de déduire une classe publique de la classe non publique, mais ce n’est pas pris en charge en .NET. Étant donné que le générateur de liaison ne génère pas de liaisons pour les classes non publiques, les classes dérivées comme celles-ci ne peuvent pas être générées correctement. Pour résoudre ce problème, soit supprimer l’entrée des métadonnées pour les classes dérivées en utilisant le nœud de suppression dans **Metadata.xml**, ou fixer les métadonnées qui rend la classe non publique publique publique publique. Bien que cette dernière solution créera la liaison de sorte que la source Cmd se construit, la classe non publique ne doit pas être utilisée.

  Par exemple :

  ```xml
  <attr path="/api/package[@name='com.some.package']/class[@name='SomeClass']"
      name="visibility">public</attr>
  ```

- Les outils qui obscurcient les bibliothèques Java peuvent interférer avec le générateur de liaison Xamarin.Android et sa capacité à générer des classes d’emballage C. L’extrait suivant montre comment mettre à jour **Metadata.xml** pour désobfuser un nom de classe:

  ```xml
  <attr path="/api/package[@name='{package_name}']/class[@name='{name}']"
      name="obfuscated">false</attr>
  ```

### <a name="problem-generated-c-source-does-not-build-due-to-parameter-type-mismatch"></a>Problème : La source Cmd générée ne s’accumule pas en raison de l’inadéquation du type de paramètre

La source C générée ne construit pas. Les types de paramètres de la méthode dépassée ne correspondent pas.

#### <a name="possible-causes"></a>Causes possibles :

Xamarin.Android comprend une variété de champs Java qui sont cartographiés pour enums dans les fixations C. Ceux-ci peuvent causer des incompatibilités de type dans les fixations générées. Pour résoudre ce problème, les signatures de méthode créées à partir du générateur de liaison doivent être modifiées pour utiliser les enums. Pour plus d’informations, s’il vous plaît voir [Corriger Enums](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md).

### <a name="problem-noclassdeffounderror-in-packaging"></a>Problème: NoClassDefFoundError dans l’emballage

`java.lang.NoClassDefFoundError`est jeté dans l’étape de l’emballage.

#### <a name="possible-causes"></a>Causes possibles :

La raison la plus probable de cette erreur est qu’une bibliothèque Java obligatoire doit être ajoutée au projet d’application (**.csproj**). . Les fichiers JAR ne sont pas automatiquement résolus. Une liaison de bibliothèque Java n’est pas toujours générée contre un assemblage d’utilisateurs qui n’existe pas dans l’appareil cible ou émulateur (comme Google Maps **maps.jar**). Ce n’est pas le cas pour le soutien du projet Android Library, comme la bibliothèque . JAR est intégré dans la bibliothèque dll. Par exemple : [Bug 4288](https://bugzilla.xamarin.com/show_bug.cgi?id=4288)

### <a name="problem-duplicate-custom-eventargs-types"></a>Problème: Duplicate personnalisé EventArgs types

Build échoue en raison de duplicates types EventArgs personnalisés. Une erreur comme celle-ci se produit:

```shell
error CS0102: The type `Com.Google.Ads.Mediation.DismissScreenEventArgs' already contains a definition for `p0'
```

#### <a name="possible-causes"></a>Causes possibles :

C’est parce qu’il ya un certain conflit entre les types d’événements qui proviennent de plus d’une interface "auditeur" type qui partage des méthodes ayant des noms identiques. Par exemple, s’il existe deux interfaces Java comme `DismissScreenEventArgs` on `MediationBannerListener` le `MediationInterstitialListener`voit dans l’exemple ci-dessous, le générateur crée pour les deux et, résultant en l’erreur.

```java
// Java:
public interface MediationBannerListener {
    void onDismissScreen(MediationBannerAdapter p0);
}
public interface MediationInterstitialListener {
    void onDismissScreen(MediationInterstitialAdapter p0);
}
```

C’est par conception que les noms longs sur les types d’argument d’événement sont évités. Pour éviter ces conflits, une certaine transformation des métadonnées est nécessaire. Modifier **Transforms-Metadata.xml** et `argsType` ajouter un attribut sur l’une ou l’autre des interfaces (ou sur la méthode de l’interface):

```xml
<attr path="/api/package[@name='com.google.ads.mediation']/
        interface[@name='MediationBannerListener']/method[@name='onDismissScreen']"
        name="argsType">BannerDismissScreenEventArgs</attr>

<attr path="/api/package[@name='com.google.ads.mediation']/
        interface[@name='MediationInterstitialListener']/method[@name='onDismissScreen']"
        name="argsType">IntersitionalDismissScreenEventArgs</attr>

<attr path="/api/package[@name='android.content']/
        interface[@name='DialogInterface.OnClickListener']"
        name="argsType">DialogClickEventArgs</attr>
```

### <a name="problem-class-does-not-implement-interface-method"></a>Problème : La classe ne met pas en œuvre la méthode de l’interface

Un message d’erreur est produit indiquant qu’une classe générée ne met pas en œuvre une méthode requise pour une interface que la classe générée implémente. Cependant, en regardant le code généré, vous pouvez voir que la méthode est implémentée.

Voici un exemple de l’erreur :

```shell
obj\Debug\generated\src\Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.cs(8,23):
error CS0738: 'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter' does not
implement interface member 'Oauth.Signpost.Http.IHttpRequest.Unwrap()'.
'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.Unwrap()' cannot implement
'Oauth.Signpost.Http.IHttpRequest.Unwrap()' because it does not have the matching
return type of 'Java.Lang.Object'
```

#### <a name="possible-causes"></a>Causes possibles :

Il s’agit d’un problème qui se produit avec des méthodes Java liaison avec les types de retour covariant. Dans cet exemple, `Oauth.Signpost.Http.IHttpRequest.UnWrap()` la `Java.Lang.Object`méthode doit revenir . Cependant, la `Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.UnWrap()` méthode a `HttpURLConnection`un type de retour de . Il y a deux façons de résoudre ce problème :

- Ajouter une déclaration `HttpURLConnectionRequestAdapter` de classe `IHttpRequest.Unwrap()`partielle pour et mettre explicitement en œuvre :

  ```csharp
  namespace Oauth.Signpost.Basic {
      partial class HttpURLConnectionRequestAdapter {
          Java.Lang.Object OauthSignpost.Http.IHttpRequest.Unwrap() {
              return Unwrap();
          }
      }
  }
  ```

- Retirez la covariance du code Cmd généré. Il s’agit d’ajouter la transformation suivante à **Transforms-Metadata.xml** qui `Java.Lang.Object`fera en sorte que le code C généré d’avoir un type de retour de :

  ```xml
  <attr
      path="/api/package[@name='oauth.signpost.basic']/class[@name='HttpURLConnectionRequestAdapter']/method[@name='unwrap']"
      name="managedReturn">Java.Lang.Object
  </attr>
  ```

### <a name="problem-name-collisions-on-inner-classes--properties"></a>Problème: Injures nom sur les classes intérieures / Propriétés

Visibilité contradictoire sur les objets hérités.

À Java, il n’est pas nécessaire qu’une classe dérivée ait la même visibilité que son parent. Java va juste réparer cela pour vous. Dans C, cela doit être explicite, de sorte que vous devez vous assurer que toutes les classes de la hiérarchie ont la visibilité appropriée. L’exemple suivant montre comment changer `com.evernote.android.job` un `Evernote.AndroidJob`nom de paquet Java de :

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

### <a name="problem-a-so-library-required-by-the-binding-is-not-loading"></a>Problème: A **.so** Bibliothèque requise par la liaison n’est pas le chargement

Certains projets contraignants peuvent également dépendre de fonctionnalités dans une bibliothèque **.so.** Il est possible que Xamarin.Android ne sera pas automatiquement charger la bibliothèque **.so.** Lorsque le code Java enveloppé s’exécute, Xamarin.Android ne parvient pas à faire l’appel JNI et le message d’erreur _java.lang.UnsatisfiedLinkError: Méthode indigène non trouvé:_ apparaîtra dans le logcat pour l’application.

Le correctif pour cela est de charger manuellement `Java.Lang.JavaSystem.LoadLibrary`la bibliothèque **.so** avec un appel à . Par exemple en supposant qu’un projet Xamarin.Android a partagé la bibliothèque **libpocketsphinx_jni.so** inclus dans le projet contraignant avec une action de construction de **EmbeddedNativeLibrary**, l’extrait suivant (exécuté avant d’utiliser la bibliothèque partagée) chargera la bibliothèque **.so:**

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="summary"></a>Récapitulatif

Dans cet article, nous avons énuméré les problèmes de dépannage courants associés aux liaisons Java et expliqué comment les résoudre.

## <a name="related-links"></a>Liens connexes

- [Projets de bibliothèque](https://developer.android.com/tools/projects/index.html#LibraryProjects)
- [Travailler avec JNI](~/android/platform/java-integration/working-with-jni.md)
- [Activer la sortie diagnostique](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)
- [Xamarin pour les développeurs Android](~/android/get-started/java-developers.md)
- [JD-GUI](http://jd.benow.ca/)
