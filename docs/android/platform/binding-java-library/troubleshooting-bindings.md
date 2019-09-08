---
title: Résolution des problèmes de liaisons
description: Cet article résume les erreurs de serveur courantes qui peuvent se produire lors de la génération de liaisons, ainsi que les causes possibles et les méthodes suggérées pour les résoudre.
ms.prod: xamarin
ms.assetid: BB81FCCF-F7BF-4C78-884E-F02C49AA819A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: dfbcb1a6f502d6d7a5b03dc03278fc21e57806bf
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756616"
---
# <a name="troubleshooting-bindings"></a>Résolution des problèmes de liaisons

_Cet article résume les erreurs de serveur courantes qui peuvent se produire lors de la génération de liaisons, ainsi que les causes possibles et les méthodes suggérées pour les résoudre._

## <a name="overview"></a>Présentation

La liaison d’une bibliothèque Android (un fichier **. AAR** ou **. jar**) est rarement une affaire simple. elle nécessite généralement des efforts supplémentaires pour atténuer les problèmes qui résultent des différences entre Java et .NET.
Ces problèmes empêchent Xamarin. Android de lier la bibliothèque Android et de se présenter comme des messages d’erreur dans le journal de génération. Ce guide propose des conseils pour résoudre les problèmes, répertorie certains des problèmes/scénarios les plus courants et fournit des solutions possibles pour réussir à lier la bibliothèque Android.

Lors de la liaison d’une bibliothèque Android existante, il est nécessaire de garder à l’esprit les points suivants :

- **Dépendances externes pour la bibliothèque** Toutes les dépendances Java requises par la bibliothèque Android doivent être incluses dans le projet Xamarin. Android en tant que **ReferenceJar** ou **EmbeddedReferenceJar.** &ndash;

- **Niveau d’API Android ciblé par la bibliothèque Android** &ndash; Il n’est pas possible de « rétrograder » le niveau d’API Android. Assurez-vous que le projet de liaison Xamarin. Android cible le même niveau d’API (ou une version ultérieure) que la bibliothèque Android.

- **Version du JDK Android qui a été utilisé pour empaqueter la bibliothèque Android** &ndash; Des erreurs de liaison peuvent se produire si la bibliothèque Android a été créée avec une version différente de JDK que celle utilisée par Xamarin. Android. Si possible, recompilez la bibliothèque Android en utilisant la même version du JDK que celle utilisée par votre installation de Xamarin. Android.

La première étape pour résoudre les problèmes liés à la liaison d’une bibliothèque Xamarin. Android consiste à activer la [sortie de diagnostic MSBuild](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).
Après avoir activé la sortie de diagnostic, régénérez le projet de liaison Xamarin. Android et examinez le journal de génération pour trouver des indices sur la cause du problème.

Il peut également s’avérer utile de décompiler la bibliothèque Android et d’examiner les types et les méthodes que Xamarin. Android tente de lier. Ce sujet est abordé plus en détail plus loin dans ce guide.

## <a name="decompiling-an-android-library"></a>Décompilation d’une bibliothèque Android

L’inspection des classes et des méthodes des classes Java peut fournir des informations précieuses qui vous aideront à lier une bibliothèque.
[JD-GUI](http://jd.benow.ca/) est un utilitaire graphique qui peut afficher le code source Java à partir des fichiers de **classe** contenus dans un fichier jar. Il peut être exécuté en tant qu’application autonome ou en tant que plug-in pour IntelliJ ou Eclipse.

Pour décompiler une bibliothèque Android, ouvrez le **. Fichier JAR** avec Java décompilateur. Si la bibliothèque est un **. Fichier AAR** , il est nécessaire d’extraire le fichier **classes. jar** du fichier d’archive. Voici un exemple de capture d’écran de l’utilisation de JD-GUI pour analyser le fichier jar [Picasso](http://square.github.io/picasso/) :

![Utilisation de Java décompilateur pour analyser Picasso-2.5.2. jar](troubleshooting-bindings-images/troubleshoot-bindings-01.png)

Une fois que vous avez décompilé la bibliothèque Android, examinez le code source. En général, recherchez :

- **Classes qui ont des caractéristiques d’obscurcissement** &ndash; Les caractéristiques des classes obscurcies sont les suivantes :

  - Le nom de la classe **$** comprend un, c.-à-d. **une classe $.**
  - Le nom de la classe est entièrement compromis en minuscules, c.-à-d. **une classe.**      

- les instructions &ndash; **pour les bibliothèques non référencées identifient la bibliothèque non référencée et ajoutent ces dépendances au projet de liaison Xamarin. Android avec une action de génération ReferenceJar ou `import`**   **EmbedddedReferenceJar**.

> [!NOTE]
> La décompilation d’une bibliothèque Java peut être interdite ou soumise à des restrictions légales en fonction des lois locales ou de la licence dans laquelle la bibliothèque Java a été publiée. Si nécessaire, inscrivez les services d’un professionnel légal avant de tenter de décompiler une bibliothèque Java et d’inspecter le code source.

## <a name="inspect-apixml"></a>Inspectez l’API. LANGAGE

Dans le cadre de la création d’un projet de liaison, Xamarin. Android génère un nom de fichier XML **obj/Debug/API. xml**:

![API. xml générée sous obj/Debug](troubleshooting-bindings-images/troubleshoot-bindings-02.png)

Ce fichier fournit la liste de toutes les API Java que Xamarin. Android tente de lier. Le contenu de ce fichier peut aider à identifier les types ou les méthodes manquants, la liaison dupliquée. Bien que l’inspection de ce fichier soit longue et fastidieuse, il peut fournir des indices sur ce qui peut causer des problèmes de liaison. Par exemple, l' **API. xml** peut révéler qu’une propriété retourne un type inapproprié ou qu’il existe deux types qui partagent le même nom géré.

## <a name="known-issues"></a>Problèmes connus

Cette section répertorie certains des messages d’erreur courants ou des symptômes qui se produisent lors de la tentative de liaison d’une bibliothèque Android.

### <a name="problem-java-version-mismatch"></a>Problème : Incompatibilité de version Java

Parfois, les types ne sont pas générés ou des blocages inattendus peuvent se produire, car vous utilisez une version plus récente ou une version antérieure de Java par rapport à ce à quoi la bibliothèque a été compilée. Recompilez la bibliothèque Android avec la même version du JDK que celle utilisée par votre projet Xamarin. Android.

### <a name="problem-at-least-one-java-library-is-required"></a>Problème : Au moins une bibliothèque Java est requise

Vous recevez l’erreur « au moins une bibliothèque Java est requise », même si un. JAR a été ajouté.

#### <a name="possible-causes"></a>Causes possibles :

Vérifiez que l’action de `EmbeddedJar`génération a la valeur. Puisqu’il existe plusieurs actions de génération pour. Les fichiers jar (tels `InputJar`que `EmbeddedJar` `ReferenceJar` , et `EmbeddedReferenceJar`), le générateur de liaisons ne peut pas deviner automatiquement celui à utiliser par défaut. Pour plus d’informations sur les actions de génération, consultez [actions de génération](~/android/platform/binding-java-library/index.md).

### <a name="problem-binding-tools-cannot-load-the-jar-library"></a>Problème : Les outils de liaison ne peuvent pas charger le. Bibliothèque JAR

Le générateur de bibliothèques de liaisons ne parvient pas à charger. Bibliothèque JAR.

#### <a name="possible-causes"></a>Causes possibles

Certains. Les bibliothèques JAR qui utilisent l’obscurcissement de code (par le biais d’outils tels que ProGuard) ne peuvent pas être chargées par les outils Java. Étant donné que notre outil utilise la réflexion Java et la bibliothèque d’ingénierie de code en octets ASM, ces outils dépendants peuvent rejeter les bibliothèques masquées alors que les outils d’exécution Android peuvent réussir. La solution de contournement consiste à lier manuellement ces bibliothèques au lieu d’utiliser le générateur de liaisons.

### <a name="problem-missing-c-types-in-generated-output"></a>Problème : Types C# manquants dans la sortie générée.

Le **fichier Binding. dll** est généré mais n’a pas de type Java C# , ou la source générée n’est pas générée en raison d’une erreur indiquant qu’il manque des types.

#### <a name="possible-causes"></a>Causes possibles :

Cette erreur peut se produire pour plusieurs raisons, comme indiqué ci-dessous :

- La bibliothèque en cours de liaison peut faire référence à une deuxième bibliothèque Java. Si l’API publique pour la bibliothèque liée utilise des types de la deuxième bibliothèque, vous devez également référencer une liaison managée pour la deuxième bibliothèque.

- Il est possible qu’une bibliothèque ait été injectée en raison d’une réflexion Java, similaire à la raison de l’erreur de chargement de la bibliothèque ci-dessus, provoquant le chargement inattendu de métadonnées. Les outils de Xamarin. Android ne peuvent pas actuellement résoudre cette situation. Dans ce cas, la bibliothèque doit être liée manuellement.

- Il y avait un bogue dans le Runtime .NET 4,0 qui n’a pas pu charger les assemblys lorsqu’il le devrait. Ce problème a été résolu dans le Runtime .NET 4,5.

- Java autorise la dérivation d’une classe publique à partir d’une classe non publique, mais cela n’est pas pris en charge dans .NET. Étant donné que le générateur de liaisons ne génère pas de liaisons pour les classes non publiques, les classes dérivées telles que celles-ci ne peuvent pas être générées correctement. Pour résoudre ce problème, supprimez l’entrée de métadonnées pour ces classes dérivées à l’aide de Remove-node dans **Metadata. xml**, ou corrigez les métadonnées qui rend la classe non publique publique. Bien que la dernière solution crée la liaison afin que la C# source soit générée, la classe non publique ne doit pas être utilisée.

  Par exemple :

  ```xml
  <attr path="/api/package[@name='com.some.package']/class[@name='SomeClass']"
      name="visibility">public</attr>
  ```

- Les outils qui brouillent les bibliothèques Java peuvent interférer avec le générateur de liaisons Xamarin. Android et C# sa capacité à générer des classes wrapper. L’extrait de code suivant montre comment mettre à jour le **fichier Metadata. xml** pour débrouiller un nom de classe :

  ```xml
  <attr path="/api/package[@name='{package_name}']/class[@name='{name}']"
      name="obfuscated">false</attr>
  ```

### <a name="problem-generated-c-source-does-not-build-due-to-parameter-type-mismatch"></a>Problème : La C# source générée n’est pas générée en raison d’une incompatibilité de type de paramètre

La source C# générée n’est pas générée. Les types de paramètres de la méthode substituée ne correspondent pas.

#### <a name="possible-causes"></a>Causes possibles :

Xamarin. Android comprend un large éventail de champs Java qui sont mappés aux enums dans C# les liaisons. Celles-ci peuvent entraîner des incompatibilités de type dans les liaisons générées. Pour résoudre ce besoin, les signatures de méthode créées à partir du générateur de liaisons doivent être modifiées pour utiliser les énumérations. Pour plus d’informations, consultez [Correction des énumérations](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md).

### <a name="problem-noclassdeffounderror-in-packaging"></a>Problème : NoClassDefFoundError dans l’empaquetage

`java.lang.NoClassDefFoundError`est levée dans l’étape d’empaquetage.

#### <a name="possible-causes"></a>Causes possibles :

La raison la plus probable de cette erreur est qu’une bibliothèque Java obligatoire doit être ajoutée au projet d’application ( **. csproj**). . Les fichiers JAR ne sont pas résolus automatiquement. Une liaison de bibliothèque Java n’est pas toujours générée par rapport à un assembly utilisateur qui n’existe pas dans l’émulateur ou l’appareil cible (par exemple, Google Maps **Maps. jar**). Ce n’est pas le cas pour la prise en charge des projets de bibliothèque Android, en tant que bibliothèque. JAR est incorporé dans la dll de bibliothèque. Par exemple :  [Bogue 4288](https://bugzilla.xamarin.com/show_bug.cgi?id=4288)

### <a name="problem-duplicate-custom-eventargs-types"></a>Problème : Types EventArgs personnalisés dupliqués

La génération échoue en raison de types EventArgs personnalisés dupliqués. Une erreur de ce type se produit :

```shell
error CS0102: The type `Com.Google.Ads.Mediation.DismissScreenEventArgs' already contains a definition for `p0'
```

#### <a name="possible-causes"></a>Causes possibles :

Cela est dû au fait qu’il existe un conflit entre les types d’événements provenant de plusieurs types d’écouteur d’interface qui partagent des méthodes ayant des noms identiques. Par exemple, s’il existe deux interfaces Java comme illustré dans l’exemple ci-dessous, le `DismissScreenEventArgs` générateur crée `MediationBannerListener` à `MediationInterstitialListener`la fois pour et, ce qui provoque l’erreur.

```java
// Java:
public interface MediationBannerListener {
    void onDismissScreen(MediationBannerAdapter p0);
}
public interface MediationInterstitialListener {
    void onDismissScreen(MediationInterstitialAdapter p0);
}
```

Cela est dû à la conception, afin que les longs noms sur les types d’arguments d’événement soient évités. Pour éviter ces conflits, certaines transformations de métadonnées sont requises. Modifiez [**Transforms\Metadata.xml**](https://github.com/xamarin/monodroid-samples/blob/master/AdMob/AdMob/Transforms/Metadata.xml) et ajoutez un `argsType` attribut sur l’une ou l’autre des interfaces (ou sur la méthode d’interface) :

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

### <a name="problem-class-does-not-implement-interface-method"></a>Problème : La classe n’implémente pas la méthode d’interface

Un message d’erreur est généré, indiquant qu’une classe générée n’implémente pas une méthode qui est requise pour une interface que la classe générée implémente. Toutefois, en examinant le code généré, vous pouvez voir que la méthode est implémentée.

Voici un exemple de l’erreur :

```shell
obj\Debug\generated\src\Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.cs(8,23):
error CS0738: 'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter' does not
implement interface member 'Oauth.Signpost.Http.IHttpRequest.Unwrap()'.
'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.Unwrap()' cannot implement
'Oauth.Signpost.Http.IHttpRequest.Unwrap()' because it does not have the matching
return type of 'Java.Lang.Object'
```

#### <a name="possible-causes"></a>Causes possibles :

Il s’agit d’un problème qui se produit lors de la liaison de méthodes Java avec des types de retour covariants. Dans cet exemple, la méthode `Oauth.Signpost.Http.IHttpRequest.UnWrap()` doit retourner. `Java.Lang.Object` Toutefois, la méthode `Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.UnWrap()` a un type de `HttpURLConnection`retour. Il existe deux façons de résoudre ce problème :

- Ajoutez une déclaration de classe partielle `HttpURLConnectionRequestAdapter` pour et implémentez `IHttpRequest.Unwrap()`explicitement :

  ```csharp
  namespace Oauth.Signpost.Basic {
      partial class HttpURLConnectionRequestAdapter {
          Java.Lang.Object OauthSignpost.Http.IHttpRequest.Unwrap() {
              return Unwrap();
          }
      }
  }
  ```

- Supprimez la covariance du code C# généré. Cela implique l’ajout de la transformation suivante à **Transforms\Metadata.xml** , ce qui C# entraîne le code généré à avoir un `Java.Lang.Object`type de retour :

  ```xml
  <attr
      path="/api/package[@name='oauth.signpost.basic']/class[@name='HttpURLConnectionRequestAdapter']/method[@name='unwrap']"
      name="managedReturn">Java.Lang.Object
  </attr>
  ```

### <a name="problem-name-collisions-on-inner-classes--properties"></a>Problème : Collisions de noms sur les classes/propriétés internes

Visibilité en conflit sur les objets hérités.

En Java, il n’est pas nécessaire qu’une classe dérivée ait la même visibilité que son parent. Java ne le corrigera que pour vous. Dans C#, qui doit être explicite, vous devez vous assurer que toutes les classes de la hiérarchie disposent de la visibilité appropriée. L’exemple suivant montre comment modifier un nom de package Java de `com.evernote.android.job` en `Evernote.AndroidJob`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

### <a name="problem-a-so-library-required-by-the-binding-is-not-loading"></a>Problème : R **.** la bibliothèque requise par la liaison n’est donc pas chargée

Certains projets de liaison peuvent également dépendre de la fonctionnalité dans une bibliothèque **. so** . Il est possible que Xamarin. Android ne charge pas automatiquement la bibliothèque **. so** . Lors de l’exécution du code Java encapsulé, Xamarin. Android ne parviendra pas à effectuer l’appel JNI et _le message d’erreur Java. lang. UnsatisfiedLinkError : Méthode Native introuvable_ : s’affichera dans le logcat pour l’application.

Pour résoudre ce problème, vous devez charger manuellement la bibliothèque **. so** avec un appel `Java.Lang.JavaSystem.LoadLibrary`à. Par exemple, en supposant qu’un projet Xamarin. Android contient une bibliothèque partagée **libpocketsphinx_jni. donc** inclus dans le projet de liaison avec une action de génération **EmbeddedNativeLibrary**, l’extrait de code suivant (exécuté avant l’utilisation de la bibliothèque partagée) chargera la bibliothèque **. so** :

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="summary"></a>Récapitulatif

Dans cet article, nous avons décrit les problèmes de dépannage courants associés aux liaisons Java et expliqué comment les résoudre.

## <a name="related-links"></a>Liens associés

- [Projets de bibliothèque](https://developer.android.com/tools/projects/index.html#LibraryProjects)
- [Utilisation de JNI](~/android/platform/java-integration/working-with-jni.md)
- [Activer la sortie de diagnostic](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)
- [Xamarin pour les développeurs Android](~/android/get-started/java-developers.md)
- [JD-GUI](http://jd.benow.ca/)
